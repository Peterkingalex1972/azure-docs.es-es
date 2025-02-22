---
title: Varias direcciones VIP para un servicio en la nube
titlesuffix: Azure Load Balancer
description: Información general sobre multiVIP y cómo establecer varias direcciones VIP en un servicio en la nube
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: allensu
ms.openlocfilehash: 3e97bea85d4d97b159168b21b4a6e932e655ccfb
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274707"
---
# <a name="configure-multiple-vips-for-a-cloud-service"></a>Configuración de varias direcciones VIP para un servicio en la nube

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Puede tener acceso a los servicios en la nube de Azure a través de la Internet pública mediante una dirección IP proporcionada por Azure. Esta dirección IP pública se conoce como una dirección VIP (IP virtual) dado que está vinculada a Azure Load Balancer y no realmente a las instancias de máquina virtual dentro del servicio en la nube. Con una sola dirección VIP puede tener acceso a cualquier instancia de máquina virtual dentro de un servicio en la nube.

Sin embargo, hay escenarios en los que puede que necesite más de una dirección VIP como punto de entrada al mismo servicio en la nube. Por ejemplo, el servicio en la nube podría hospedar varios sitios web que requieren conectividad SSL usando el puerto predeterminado de 443, y cada sitio hospedarse para un cliente o inquilino diferente. En este escenario, deberá tener una dirección IP accesible de forma pública diferente para cada sitio web. El siguiente diagrama muestra un hospedaje web multiempresa típico en el que se necesitan varios certificados SSL en el mismo puerto público.

![Escenario SSL de varias direcciones VIP](./media/load-balancer-multivip/Figure1.png)

En el escenario anterior, todas las direcciones VIP usan el mismo puerto público (443) y el tráfico se redirige a una o varias máquinas virtuales con equilibrio de carga en un único puerto privado, según la dirección IP interna del servicio en la nube que hospeda todos los sitios web.

> [!NOTE]
> Otro escenario de uso de varias direcciones VIP es el hospedaje de varios agentes de escucha de grupo de disponibilidad SQL AlwaysOn en el mismo conjunto de máquinas virtuales.

Las direcciones VIP son dinámicas de forma predeterminada, lo que significa que la dirección IP real asignada al servicio en la nube puede cambiar con el tiempo. Para evitar que esto suceda, puede reservar una dirección VIP para su servicio. Para obtener más información sobre las direcciones VIP reservadas, consulte [IP pública reservada](../virtual-network/virtual-networks-reserved-public-ip.md).

> [!NOTE]
> Consulte [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses/) para obtener información sobre los precios de las direcciones VIP y las direcciones IP reservadas.

Puede usar PowerShell para comprobar las direcciones VIP usadas por los servicios en la nube, y también para agregar y quitar direcciones VIP, asociar una dirección VIP a un extremo y configurar el equilibrio de carga para una dirección VIP específica.

## <a name="limitations"></a>Limitaciones

En este momento, la funcionalidad de VIP múltiples se limita a los siguientes escenarios:

* **Solo IaaS**. Solo puede habilitar VIP múltiples en servicios en la nube que contengan máquinas virtuales. No puede usar VIP múltiples en escenarios de PaaS, con instancias de rol.
* **Solo PowerShell**. Los VIP múltiples solo se pueden administrar mediante PowerShell.

Estas limitaciones son temporales y pueden cambiar en cualquier momento. Asegúrese de volver a visitar esta página para comprobar los cambios futuros.

## <a name="how-to-add-a-vip-to-a-cloud-service"></a>Agregar una dirección VIP a un servicio en la nube
Para agregar una dirección VIP a su servicio, ejecute el siguiente comando de PowerShell:

```powershell
Add-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

Este comando muestra un resultado similar al siguiente ejemplo:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Add-AzureVirtualIP   4bd7b638-d2e7-216f-ba38-5221233d70ce Succeeded

## <a name="how-to-remove-a-vip-from-a-cloud-service"></a>Eliminación de una dirección VIP de un servicio en la nube
Para quitar la dirección VIP agregada al servicio en el  ejemplo anterior, ejecute el siguiente comando de PowerShell:

```powershell
Remove-AzureVirtualIP -VirtualIPName Vip3 -ServiceName myService
```

> [!IMPORTANT]
> Solo puede quitar una dirección VIP si no tiene ningún extremo asociado a ella.


## <a name="how-to-retrieve-vip-information-from-a-cloud-service"></a>Recuperación de la información de dirección VIP de un servicio en la nube
Para recuperar las direcciones VIP asociadas a un servicio en la nube, ejecute el siguiente script de PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

El script muestra un resultado similar al siguiente ejemplo:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

En este ejemplo, el servicio en la nube tiene 3 direcciones VIP:

* **Vip1** es la dirección VIP predeterminada. Se sabe porque el valor de IsDnsProgrammedName está establecido en true.
* **Vip2** y **Vip3** no se usan, ya que no tienen ninguna dirección IP. Solo se usarán si asocia un extremo a la dirección VIP.

> [!NOTE]
> Solo se cobrarán las direcciones VIP adicionales de su suscripción una vez que estén asociadas a un punto de conexión. Para obtener más información sobre los precios, consulte [Precios de las direcciones IP](https://azure.microsoft.com/pricing/details/ip-addresses/).

## <a name="how-to-associate-a-vip-to-an-endpoint"></a>Asociación de una dirección VIP a un extremo

Para asociar una dirección VIP de un servicio en la nube a un extremo, ejecute el siguiente comando de PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -Protocol tcp -LocalPort 8080 -PublicPort 80 -VirtualIPName Vip2 |
    Update-AzureVM
```

El comando anterior crea un punto de conexión vinculado a la dirección VIP denominada *Vip2* en el puerto *80*, y lo vincula a la máquina virtual denominada *myVM1* en un servicio en la nube denominado *myService* con *TCP* en el puerto *8080*.

Para comprobar la configuración, ejecute el siguiente comando de PowerShell:

```powershell
$deployment = Get-AzureDeployment -ServiceName myService
$deployment.VirtualIPs
```

La salida es similar a la siguiente:

    Address         : 191.238.74.148
    IsDnsProgrammed : True
    Name            : Vip1
    ReservedIPName  :
    ExtensionData   :

    Address         : 191.238.74.13
    IsDnsProgrammed :
    Name            : Vip2
    ReservedIPName  :
    ExtensionData   :

    Address         :
    IsDnsProgrammed :
    Name            : Vip3
    ReservedIPName  :
    ExtensionData   :

## <a name="how-to-enable-load-balancing-on-a-specific-vip"></a>Habilitación del equilibrio de carga en una dirección VIP específica

Puede asociar una sola dirección VIP a varias máquinas virtuales con el fin de equilibrar la carga. Por ejemplo, suponga que tiene un servicio en la nube denominado *myService* y dos máquinas virtuales denominadas *myVM1* y *myVM2*. Y el servicio en la nube tiene varias direcciones VIP, una de ellas denominada *Vip2*. Si desea asegurarse de que todo el tráfico al puerto *81* en *Vip2* se equilibre entre *myVM1* y *myVM2* en el puerto *8181*, ejecute el siguiente script de PowerShell:

```powershell
Get-AzureVM -ServiceName myService -Name myVM1 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2 -DefaultProbe |
    Update-AzureVM

Get-AzureVM -ServiceName myService -Name myVM2 |
    Add-AzureEndpoint -Name myEndpoint -LoadBalancedEndpointSetName myLBSet -Protocol tcp -LocalPort 8181 -PublicPort 81 -VirtualIPName Vip2  -DefaultProbe |
    Update-AzureVM
```

También puede actualizar el equilibrador de carga para que use una dirección VIP diferente. Por ejemplo, si ejecuta el siguiente comando de PowerShell, cambiará el conjunto de equilibrio de carga para que use una dirección VIP denominada Vip1:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName myService -LBSetName myLBSet -VirtualIPName Vip1
```

## <a name="next-steps"></a>Pasos siguientes

[Registros de Azure Monitor para Azure Load Balancer](load-balancer-monitor-log.md)

[Información general sobre el equilibrador de carga accesible desde Internet](load-balancer-internet-overview.md)

[Introducción al equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)

[Información general sobre redes virtuales](../virtual-network/virtual-networks-overview.md)

[API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx)
