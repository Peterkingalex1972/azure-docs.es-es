---
title: 'Conexión de puertas de enlace Azure VPN Gateway a varios dispositivos VPN locales basados en directivas: Azure Resource Manager: PowerShell | Microsoft Docs'
description: Configure una puerta de enlace VPN basada en rutas de Azure para varios dispositivos VPN basados en directivas con Azure Resource Manager y PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: yushwang
ms.openlocfilehash: 9085d5ee21b1e955b7d9416a379ee730ba26ad3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66150088"
---
# <a name="connect-azure-vpn-gateways-to-multiple-on-premises-policy-based-vpn-devices-using-powershell"></a>Conexión de puertas de enlace Azure VPN Gateway a varios dispositivos VPN locales basados en directivas con PowerShell

Este artículo le ayuda a configurar una puerta de enlace de VPN basada en rutas de Azure para conectarse a varios dispositivos VPN locales basados en directivas al aprovechar las directivas IPsec/IKE personalizadas en las conexiones VPN de sitio a sitio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about"></a>Acerca de las puertas de enlace de VPN basadas en directivas y en rutas

Los dispositivos VPN basados en directivas *frente* a los basados en rutas se diferencian en la forma en la que se establecen los selectores de tráfico IPsec en una conexión:

* Los dispositivos VPN **basados en directivas** usan las combinaciones de prefijos de ambas redes para definir la forma en la que se cifra/descifra el tráfico a través de túneles IPsec. Normalmente se basa en dispositivos de firewall que realizan el filtrado de paquetes. El cifrado y descifrado de túneles IPsec se agregan al motor de procesamiento y al filtrado de paquetes.
* Los dispositivos VPN **basados en rutas** usan selectores de tráfico universales (caracteres comodín) y permiten a las tablas de reenvío/enrutamiento dirigir el tráfico a distintos túneles IPsec. Normalmente se basa en plataformas de enrutador donde cada túnel IPsec se modela como una interfaz de red o VTI (interfaz de túnel virtual).

Los diagramas siguientes resaltan los dos modelos:

### <a name="policy-based-vpn-example"></a>Ejemplo de VPN basada en directivas
![basada en directivas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedmultisite.png)

### <a name="route-based-vpn-example"></a>Ejemplo de VPN basada en rutas
![basada en rutas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/routebasedmultisite.png)

### <a name="azure-support-for-policy-based-vpn"></a>Compatibilidad de Azure con VPN basada en directivas
Actualmente, Azure admite los dos modos de puertas de enlace de VPN: puertas de enlace de VPN basadas en directivas y puertas de enlace de VPN basadas en rutas. Se crean en distintas plataformas internas, lo que da lugar a diferentes especificaciones:

|                          | **VPN Gateway PolicyBased** | **VPN Gateway RouteBased**               |
| ---                      | ---                         | ---                                      |
| **SKU de puerta de enlace de Azure**    | Básica                       | Básica, Estándar, HighPerformance, VpnGw1, VpnGw2, VpnGw3 |
| **Versión de IKE**          | IKEv1                       | IKEv2                                    |
| **Máx. de conexiones de sitio a sitio** | **1**                       | Básica o Estándar: 10<br> HighPerformance: 30 |
|                          |                             |                                          |

Con la directiva IPsec/IKE personalizada, ahora puede configurar puertas de enlace Azure VPN Gateway basadas en rutas para usar selectores de tráfico basados en prefijo con la opción "**PolicyBasedTrafficSelectors**" para conectarse a dispositivos VPN locales basados en directivas. Esta capacidad le permite conectarse desde una red virtual de Azure y una puerta de enlace Azure VPN Gateway a varios dispositivos VPN/firewall locales basados en directivas y quitar el límite de conexión único de las actuales puertas de enlace Azure VPN Gateway basadas en directivas.

> [!IMPORTANT]
> 1. Para habilitar esta conectividad, los dispositivos VPN locales basados en directivas deben admitir **IKEv2** para conectarse a las puertas de enlace Azure VPN Gateway basadas en rutas. Compruebe las especificaciones del dispositivo VPN.
> 2. Las redes locales que se conectan a través de dispositivos VPN basados en directivas con este mecanismo solo pueden conectarse a la red virtual de Azure; **no se permite el tránsito a otras redes locales o redes virtuales a través de la misma puerta de enlace de VPN de Azure**.
> 3. La opción de configuración forma parte de la directiva de conexión IPsec/IKE personalizada. Si habilita la opción de selector de tráfico basado en directivas, debe especificar la directiva completa (vigencias de SA, puntos clave, algoritmos de integridad y cifrado IPsec/IKE).

El diagrama siguiente muestra por qué el enrutamiento del tránsito a través de la puerta de enlace de VPN de Azure no funciona con la opción basada en directivas:

![tránsito basado en directivas](./media/vpn-gateway-connect-multiple-policybased-rm-ps/policybasedtransit.png)

Como se muestra en el diagrama, la puerta de enlace de VPN de Azure tiene selectores de tráfico desde la red virtual a cada prefijo de red local, pero no a los prefijos de conexión cruzada. Por ejemplo, los sitios 2, 3 y 4 locales pueden comunicarse con VNet1, pero no se pueden conectar entre sí a través de la puerta de enlace de VPN de Azure. El diagrama muestra los selectores de tráfico de conexión cruzada que no están disponibles en la puerta de enlace Azure VPN Gateway en esta configuración.

## <a name="configurepolicybased"></a>Configuración de los selectores de tráfico basados en directivas en una conexión

Las instrucciones de este artículo siguen el mismo ejemplo de [Configurar una directiva de IPsec o IKE para conexiones VPN de sitio a sitio o de red virtual a red virtual](vpn-gateway-ipsecikepolicy-rm-powershell.md) para establecer una conexión VPN de sitio a sitio. Se muestra en el diagrama siguiente:

![s2s-policy](./media/vpn-gateway-connect-multiple-policybased-rm-ps/s2spolicypb.png)

El flujo de trabajo para habilitar esta conectividad:
1. Cree la red virtual, la puerta de enlace VPN y la puerta de enlace de red local para la conexión entre locales.
2. Cree una directiva IPsec/IKE.
3. Aplique la directiva cuando se cree una conexión de sitio a sitio o de red virtual a red virtual, y **habilite los selectores de tráfico basados en directivas** en la conexión.
4. Si ya se ha creado la conexión, puede aplicar la directiva a una conexión existente o actualizarla.

## <a name="before-you-begin"></a>Antes de empezar

Compruebe que tiene una suscripción a Azure. Si todavía no la tiene, puede activar sus [ventajas como suscriptor de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="enablepolicybased"></a>Habilitación de los selectores de tráfico basados en directivas en una conexión

Asegúrese de haber completado la [parte 3 del artículo Configurar una directiva de IPsec o IKE](vpn-gateway-ipsecikepolicy-rm-powershell.md) para esta sección. El ejemplo siguiente usa los mismos parámetros y pasos:

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>Paso 1: crear la red virtual, la puerta de enlace de VPN y la puerta de enlace de red local

#### <a name="1-connect-to-your-subscription-and-declare-your-variables"></a>1. Conexión a la suscripción y declaración de variables

[!INCLUDE [sign in](../../includes/vpn-gateway-cloud-shell-ps-login.md)]

Declare las variables. Para este ejercicio, usamos las siguientes variables:

```azurepowershell-interactive
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>2. Creación de la red virtual, la puerta de enlace de VPN y la puerta de enlace de red local

Cree un grupo de recursos.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

Use el ejemplo siguiente para crear la red virtual TestVNet1, con tres subredes y la puerta de enlace de VPN. Si desea reemplazar los valores, es importante que siempre asigne el nombre GatewaySubnet a la subred de la puerta de enlace. Si usa otro, se produce un error al crear la puerta de enlace.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a>Paso 2: Creación de una conexión VPN de sitio a sitio con una directiva IPsec/IKE

#### <a name="1-create-an-ipsecike-policy"></a>1. Cree una directiva IPsec/IKE.

> [!IMPORTANT]
> Debe crear una directiva IPsec/IKE para habilitar la opción "UsePolicyBasedTrafficSelectors" en la conexión.

El ejemplo siguiente crea una directiva IPsec/IKE con estos algoritmos y parámetros:
* IKEv2: AES256, SHA384 y DHGroup24
* IPsec: AES256, SHA256, sin PFS, 14 400 segundos de vigencia de SA y 102 400 000 KB

```azurepowershell-interactive
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-the-s2s-vpn-connection-with-policy-based-traffic-selectors-and-ipsecike-policy"></a>2. Creación de una conexión VPN de sitio a sitio con selectores de tráfico basados en directivas y directiva IPsec/IKE
Cree una conexión VPN de sitio a sitio y aplique la directiva IPsec/IKE creada en el paso anterior. Tenga en cuenta el parámetro adicional "-UsePolicyBasedTrafficSelectors $True", que habilita los selectores de tráfico basados en directivas en la conexión.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -UsePolicyBasedTrafficSelectors $True -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

Después de completar los pasos, la conexión VPN de sitio a sitio usará la directiva IPsec/IKE definida y habilitará los selectores de tráfico basados en directivas en la conexión. Puede repetir los mismos pasos para agregar más conexiones a los dispositivos VPN locales adicionales basados en directivas desde la misma puerta de enlace Azure VPN Gateway.

## <a name="update-policy-based-traffic-selectors-for-a-connection"></a>Actualización de los selectores de tráfico basados en directivas para una conexión
La última sección muestra cómo actualizar la opción de selectores de tráfico basados en directivas para una conexión VPN de sitio a sitio existente.

### <a name="1-get-the-connection"></a>1. Obtención de la conexión
Obtenga el recurso de conexión.

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
```

### <a name="2-check-the-policy-based-traffic-selectors-option"></a>2. Comprobación de la opción de selectores de tráfico basados en directivas
La siguiente línea muestra si se usan los selectores de tráfico basados en directivas para la conexión:

```azurepowershell-interactive
$connection6.UsePolicyBasedTrafficSelectors
```

Si la línea devuelve "**True**", los selectores de tráfico basados en directivas están configurados en la conexión; en caso contrario, devuelve "**False**".

### <a name="3-enabledisable-the-policy-based-traffic-selectors-on-a-connection"></a>3. Habilitar/Deshabilitar los selectores de tráfico basados en directivas en una conexión
Una vez que obtenga el recurso de conexión, puede habilitar o deshabilitar la opción.

#### <a name="to-enable-usepolicybasedtrafficselectors"></a>Para habilitar UsePolicyBasedTrafficSelectors
En el ejemplo siguiente se habilita la opción de selectores de tráfico basados en directivas y se deja sin modificar la directiva IPsec/IKE:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $True
```

#### <a name="to-disable-usepolicybasedtrafficselectors"></a>Para deshabilitar UsePolicyBasedTrafficSelectors
En el ejemplo siguiente se deshabilita la opción de selectores de tráfico basados en directivas, pero se deja sin modificar la directiva IPsec/IKE:

```azurepowershell-interactive
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -UsePolicyBasedTrafficSelectors $False
```

## <a name="next-steps"></a>Pasos siguientes
Una vez completada la conexión, puede agregar máquinas virtuales a las redes virtuales. Consulte [Creación de una máquina virtual que ejecuta Windows en el Portal de Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ver los pasos.

Consulte también [Configure IPsec/IKE policy for S2S VPN or VNet-to-VNet connections](vpn-gateway-ipsecikepolicy-rm-powershell.md) (Configuración de la directiva IPsec/IKE para conexiones VPN de sitio a sitio o de red virtual a red virtual) para obtener más información sobre las directivas IPsec/IKE personalizadas.
