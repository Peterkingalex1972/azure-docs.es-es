---
title: Preguntas frecuentes sobre Azure Firewall
description: Preguntas frecuentes sobre Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 6/21/2019
ms.author: victorh
ms.openlocfilehash: 933b4167f25db5a01cf1160f5e781a1fe31afc6b
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304593"
---
# <a name="azure-firewall-faq"></a>Preguntas frecuentes sobre Azure Firewall

## <a name="what-is-azure-firewall"></a>¿Qué es Azure Firewall?

Azure Firewall es un servicio de seguridad de red administrado y basado en la nube que protege los recursos de Azure Virtual Network. Se trata de un firewall como servicio con estado completo que incorpora alta disponibilidad y escalabilidad a la nube sin restricciones. Puede crear, aplicar y registrar directivas de aplicaciones y de conectividad de red a nivel central en suscripciones y redes virtuales.

## <a name="what-capabilities-are-supported-in-azure-firewall"></a>¿Qué funcionalidades son compatibles con Azure Firewall?

* Firewall con estado como servicio
* Alta disponibilidad integrada con escalabilidad en la nube sin restricciones
* Filtrado de FQDN
* Etiquetas FQDN
* Reglas de filtrado de tráfico de red
* Compatibilidad con SNAT saliente
* Compatibilidad con DNAT entrante
* Creación, aplicación y registro de directivas de aplicación y de conectividad de red en las suscripciones y redes virtuales de Azure
* Totalmente integrado con Azure Monitor para registros y análisis

## <a name="what-is-the-typical-deployment-model-for-azure-firewall"></a>¿Cuál es el modelo de implementación típico para Azure Firewall?

Puede implementar Azure Firewall en cualquier red virtual, pero los clientes habitualmente lo implementan en una red virtual central y lo emparejan con otras redes virtuales en un modelo de concentrador y radio. A continuación, puede establecer la ruta predeterminada desde las redes virtuales emparejadas para que apunte a esta red virtual de firewall central. Se admite el emparejamiento de VNet global, pero no se recomienda debido a posibles problemas de rendimiento y latencia entre regiones. Para obtener el mejor rendimiento, implemente un firewall por región.

La ventaja de este modelo es la posibilidad de ejercer control centralizado sobre varias redes virtuales de radio en distintas suscripciones. También hay un ahorro en los costos, ya que no es necesario implementar un firewall en cada red virtual por separado. Este ahorro se debe medir frente al costo de emparejamiento asociado basado en los patrones de tráfico del cliente.

## <a name="how-can-i-install-the-azure-firewall"></a>¿Cómo puedo instalar Azure Firewall?

Puede configurar Azure Firewall mediante Azure Portal, PowerShell, API REST o a través del uso de plantillas. Vea [Tutorial: Implementación y configuración de Azure Firewall mediante Azure Portal](tutorial-firewall-deploy-portal.md) para obtener instrucciones detalladas.

## <a name="what-are-some-azure-firewall-concepts"></a>¿Cuáles son algunos de los conceptos de Azure Firewall?

Azure Firewall es compatible con las reglas y las colecciones de reglas. Una colección de reglas es una lista de reglas que comparten el mismo orden y prioridad. Las colecciones de reglas se ejecutan en su orden de prioridad. Las colecciones de reglas de red tienen mayor prioridad que las colecciones de reglas de aplicación y se finalizan todas las reglas.

Hay tres tipos de colecciones de reglas:

* *Reglas de aplicación*: configurar los nombres de dominio completo (FQDN) a los que se puede acceder desde una subred.
* *Reglas de red*: configurar reglas que contienen direcciones de origen, protocolos, puertos de destino y direcciones de destino.
* *Reglas NAT*: configurar reglas DNAT para permitir conexiones entrantes.

## <a name="does-azure-firewall-support-inbound-traffic-filtering"></a>¿Admite Azure Firewall el filtrado del tráfico de entrada?

Azure Firewall es compatible con el filtrado entrante y saliente. La protección entrante es para los protocolos que no son HTTP/S. Por ejemplo, los protocolos RDP, SSH y FTP.

## <a name="which-logging-and-analytics-services-are-supported-by-the-azure-firewall"></a>¿Qué servicios de registro y análisis son compatibles con Azure Firewall?

Azure Firewall se integra con Azure Monitor para ver y analizar los registros de firewall. Los registros se pueden enviar a Log Analytics, Azure Storage o Event Hubs. Puede analizarse en Log Analytics o usando distintas herramientas, como Excel y Power BI. Para obtener más información, vea [Tutorial: Supervisión de los registros de Azure Firewall](tutorial-diagnostics.md).

## <a name="how-does-azure-firewall-work-differently-from-existing-services-such-as-nvas-in-the-marketplace"></a>¿Cómo Azure Firewall funciona distinto de servicios existentes como NVA que ya están en el mercado?

Azure Firewall es un servicio de firewall básica que puede abordar determinados escenarios de cliente. Se espera que tenga una combinación de NVA de terceros y Azure Firewall. Trabajar mejor juntos es una prioridad fundamental.

## <a name="what-is-the-difference-between-application-gateway-waf-and-azure-firewall"></a>¿Cuál es la diferencia entre WAF de Application Gateway y Azure Firewall?

Firewall de aplicaciones web (WAF) es una característica de Application Gateway que proporciona a las aplicaciones una protección de entrada centralizada contra ataques y puntos vulnerables comunes. Azure Firewall proporciona protección de entrada para protocolos no HTTP/S (como RDP, SSH y FTP), protección de salida a nivel de red para todos los puertos y protocolos, y protección a nivel de aplicación para las salidas HTTP/S.

## <a name="what-is-the-difference-between-network-security-groups-nsgs-and-azure-firewall"></a>¿Cuál es la diferencia entre los grupos de seguridad de red (NSG) y Azure Firewall?

El servicio Azure Firewall complementa la funcionalidad de grupo de seguridad de red. Juntos proporcionan una mejor seguridad de red de "defensa en profundidad". Los grupos de seguridad de red proporcionan filtrado del trafico de capas de red distribuida para limitar el tráfico a los recursos dentro de las redes virtuales en cada suscripción. Azure Firewall es un firewall de red centralizada como servicio con estado completo que proporciona protección a nivel de red y de aplicación en todas las distintas suscripciones y redes virtuales.

## <a name="are-network-security-groups-nsgs-supported-on-the-azure-firewall-subnet"></a>¿Se admiten grupos de seguridad de red (NSG) en la subred de Azure Firewall?

Azure Firewall es un servicio administrado con varias capas de protección, como la protección de la plataforma con NSG de nivel de NIC (no visible).  Los NSG no son obligatorios en la subred de Azure Firewall y están deshabilitados para garantizar que no se produzcan interrupciones en el servicio.

## <a name="how-do-i-set-up-azure-firewall-with-my-service-endpoints"></a>¿Cómo puedo configurar Azure Firewall con mis puntos de conexión de servicio?

Para un acceso seguro a los servicios de PaaS, se recomiendan los puntos de conexión de servicio. Puede elegir habilitar puntos de conexión de servicio en la subred de Azure Firewall y deshabilitarlos en las redes virtuales de radios conectadas. De este modo se beneficia de ambas características: la seguridad de los puntos de conexión y el registro central de todo el tráfico.

## <a name="what-is-the-pricing-for-azure-firewall"></a>¿Cuál es el precio de Azure Firewall?

Consulte [Precios de Azure Firewall](https://azure.microsoft.com/pricing/details/azure-firewall/).

## <a name="how-can-i-stop-and-start-azure-firewall"></a>¿Cómo puedo detener e iniciar Azure Firewall?

Puede usar los métodos *deallocate* y *allocate* de Azure PowerShell.

Por ejemplo:

```azurepowershell
# Stop an existing firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$azfw.Deallocate()
Set-AzFirewall -AzureFirewall $azfw
```

```azurepowershell
# Start a firewall

$azfw = Get-AzFirewall -Name "FW Name" -ResourceGroupName "RG Name"
$vnet = Get-AzVirtualNetwork -ResourceGroupName "RG Name" -Name "VNet Name"
$publicip = Get-AzPublicIpAddress -Name "Public IP Name" -ResourceGroupName " RG Name"
$azfw.Allocate($vnet,$publicip)
Set-AzFirewall -AzureFirewall $azfw
```

> [!NOTE]
> Debe reasignar un firewall y una dirección IP pública al grupo de recursos y la suscripción originales.

## <a name="what-are-the-known-service-limits"></a>¿Cuáles son los límites conocidos del servicio?

Para ver los límites del servicio Azure Firewall, consulte [Suscripción de Azure y límites de servicio, cuotas y restricciones](../azure-subscription-service-limits.md#azure-firewall-limits).

## <a name="can-azure-firewall-in-a-hub-virtual-network-forward-and-filter-network-traffic-between-two-spoke-virtual-networks"></a>¿Azure Firewall en la red virtual de un concentrador puede reenviar y filtrar el tráfico de red entre dos redes virtuales de radio?

Sí, puede usar Azure Firewall en la red virtual de un concentrador para enrutar y filtrar el tráfico de red entre dos redes virtuales de radio. Las subredes de cada una de las redes virtuales de radio deben tener una ruta definida por el usuario (UDR) que apunte a la instancia de Azure Firewall como puerta de enlace predeterminada para que este escenario funcione correctamente.

## <a name="can-azure-firewall-forward-and-filter-network-traffic-between-subnets-in-the-same-virtual-network-or-peered-virtual-networks"></a>¿Azure Firewall puede reenviar y filtrar el tráfico de red entre subredes de la misma red virtual o redes virtuales emparejadas?

Sí. Sin embargo, es necesario prestar una atención especial al configurar las UDR para redirigir el tráfico entre subredes de la misma red virtual. Aunque basta con usar el intervalo de direcciones de red virtual como prefijo de destino para la UDR, también se enruta todo el tráfico de una máquina a otra de la misma subred mediante la instancia de Azure Firewall. Para evitar este problema, incluya una ruta para la subred en la UDR con un tipo de próximo salto **VNET**. La administración de estas rutas podría ser problemática y estar sujeta a errores. El método recomendado para la segmentación de redes internas es usar grupos de seguridad de red, que no necesitan UDR.

## <a name="does-azure-firewall-outbound-snat-between-private-networks"></a>¿Azure Firewall aplica SNAT de salida entre redes privadas?

Azure Firewall no aplica SNAT cuando la dirección IP de destino es un intervalo de direcciones IP privadas por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Si su organización usa un intervalo de direcciones IP públicas para las redes privadas, Azure Firewall aplicará SNAT al tráfico para una de las direcciones IP privadas de firewall en AzureFirewallSubnet.

## <a name="is-forced-tunnelingchaining-to-a-network-virtual-appliance-supported"></a>¿Se admite la tunelización o encadenamiento forzados a una aplicación virtual de red?

La tunelización forzada no se admite de forma predeterminada, pero se puede habilitar con la ayuda del soporte técnico.

Azure Firewall debe tener conectividad directa a Internet. Si AzureFirewallSubnet aprende una ruta predeterminada a la red local mediante BGP, debe reemplazarla por una UDR 0.0.0.0/0 con el valor **NextHopType** establecido como **Internet** para mantener la conectividad directa a Internet. De forma predeterminada, Azure Firewall no admite la tunelización forzada a una red local.

Sin embargo, si la configuración requiere la tunelización forzada a una red local, Microsoft proporcionará soporte según el caso. Póngase en contacto con soporte técnico para que podamos revisar su caso. Si acepta, permitiremos su suscripción y nos aseguraremos de que se mantenga la conectividad del firewall a Internet requerida.

## <a name="are-there-any-firewall-resource-group-restrictions"></a>¿Existe alguna restricción de grupo de recursos de firewall?

Sí. El firewall, la subred, la red virtual y la dirección IP pública deben estar en el mismo grupo de recursos.

## <a name="when-configuring-dnat-for-inbound-network-traffic-do-i-also-need-to-configure-a-corresponding-network-rule-to-allow-that-traffic"></a>Al configurar DNAT para el tráfico de red entrante, ¿es necesario que también configure una regla de red correspondiente para permitir ese tráfico?

No. Las reglas NAT agregan de forma implícita una regla de red correspondiente implícita para permitir el tráfico traducido. Para invalidar este comportamiento, agregue explícitamente una colección de reglas de red con reglas de denegación que coinciden con el tráfico traducido. Para más información acerca de la lógica de procesamiento de reglas Azure Firewall, consulte [Lógica de procesamiento de reglas de Azure Firewall](rule-processing.md).

## <a name="how-do-wildcards-work-in-an-application-rule-target-fqdn"></a>¿Cómo funcionan los caracteres comodín en un FQDN de destino de la regla de aplicación?

Si configura * **.contoso.com**, permite *cualquiervalor*.contoso.com, pero no contoso.com (el vértice de dominio). Si desea permitir el vértice de dominio, debe configurarlo explícitamente como un FQDN de destino.

## <a name="what-does-provisioning-state-failed-mean"></a>¿Qué significa *Estado de aprovisionamiento: error*?

Cada vez que se aplique un cambio de configuración, Azure Firewall intenta actualizar todas sus instancias de back-end subyacentes. En raras ocasiones, una de estas instancias de back-end puede producir un error al actualizar con la nueva configuración y el proceso de actualización se detiene con un estado de aprovisionamiento con error. Azure Firewall todavía está operativo, pero es posible que la configuración aplicada se encuentre en un estado incoherente, donde algunas instancias tienen la configuración anterior y otras tienen el conjunto de reglas actualizado. Si esto sucede, intente actualizar la configuración una vez más, hasta que la operación se realice correctamente y el firewall se encuentre en un estado de aprovisionamiento *Correcto*.
