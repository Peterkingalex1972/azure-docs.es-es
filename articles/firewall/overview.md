---
title: ¿Qué es Azure Firewall?
description: Conozca las características de Azure Firewall.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: overview
ms.custom: mvc
ms.date: 08/23/2019
ms.author: victorh
Customer intent: As an administrator, I want to evaluate Azure Firewall so I can determine if I want to use it.
ms.openlocfilehash: f65b1d62a9c0e6835421c2ae796f9ea390407c9a
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971593"
---
# <a name="what-is-azure-firewall"></a>¿Qué es Azure Firewall?

Azure Firewall es un servicio de seguridad de red administrado y basado en la nube que protege los recursos de Azure Virtual Network. Se trata de un firewall como servicio con estado completo que incorpora alta disponibilidad y escalabilidad a la nube sin restricciones.

![Información general de firewalls](media/overview/firewall-threat.png)

Puede crear, aplicar y registrar directivas de aplicaciones y de conectividad de red a nivel central en suscripciones y redes virtuales. Azure Firewall usa una dirección IP pública estática para los recursos de red virtual, que permite que los firewall externos identifiquen el tráfico procedente de la red virtual.  El servicio está totalmente integrado con Azure Monitor para los registros y análisis.

Azure Firewall ofrece las siguientes características:

## <a name="built-in-high-availability"></a>Alta disponibilidad integrada

Gracias a la alta disponibilidad integrada, no se necesita ningún equilibrador de carga adicional y no es necesario configurar nada.

## <a name="availability-zones"></a>Zonas de disponibilidad

Azure Firewall se puede configurar durante la implementación para abarcar varias zonas de disponibilidad y aumentar la disponibilidad. Con Availability Zones, la disponibilidad aumenta a un tiempo de actividad del 99,99 %. Para más información, consulte el [Acuerdo de Nivel de Servicio (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) de Azure Firewall. El SLA de tiempo de actividad del 99,99 % se ofrece cuando se seleccionan dos o más zonas de Availability Zones.

También puede asociar Azure Firewall a una zona específica solo por motivos de proximidad, mediante el SLA del 99,95 % estándar del servicio.

No hay ningún costo adicional por el firewall implementado en una zona de disponibilidad. Sin embargo, hay costos adicionales para las transferencias de datos entrantes y salientes asociadas con Availability Zones. Para más información, consulte [Detalles de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/).

Availability Zones de Azure Firewall está disponible en las regiones que lo admiten. Para más información, consulte [¿Qué son las zonas de disponibilidad en Azure?](../availability-zones/az-overview.md#services-support-by-region)

> [!NOTE]
> Availability Zones solo se puede configurar durante la implementación. No se puede configurar un firewall existente para incluirlo.

Para más información sobre Availability Zones, consulte [¿Qué son las zonas de disponibilidad en Azure?](../availability-zones/az-overview.md)

## <a name="unrestricted-cloud-scalability"></a>Escalabilidad a la nube sin restricciones

Azure Firewall puede escalarse verticalmente todo lo que sea necesario para acoger los flujos de tráfico de red cambiantes, por lo que no es necesario elaborar un presupuesto para el tráfico en su momento álgido.

## <a name="application-fqdn-filtering-rules"></a>Reglas de filtrado de FQDN de aplicación

Puede limitar el tráfico HTTP/S o el tráfico de Azure SQL (versión preliminar) saliente a una lista especificada de nombres de dominio completos (FQDN) que incluye caracteres comodín. Esta característica no requiere terminación de SSL.

## <a name="network-traffic-filtering-rules"></a>Reglas de filtrado de tráfico de red

Puede crear reglas de filtrado de red para *permitir* o *denegar* por dirección IP de origen y destino, puerto y protocolo. Azure Firewall tiene estado completo, de modo que puede distinguir los paquetes legítimos de diferentes tipos de conexiones. Las reglas se aplican y se registran en varias suscripciones y redes virtuales.

## <a name="fqdn-tags"></a>Etiquetas FQDN

Con las etiquetas FQDN, puede permitir fácilmente el conocido tráfico de red del servicio de Azure a través del firewall. Por ejemplo, supongamos que quiere permitir el tráfico de red de Windows Update a través del firewall. Puede crear una regla de aplicación e incluir la etiqueta de Windows Update. Ahora, el tráfico de red de Windows Update puede fluir a través del firewall.

## <a name="service-tags"></a>Etiquetas de servicio

Una etiqueta de servicio representa un grupo de prefijos de direcciones IP que ayudan a reducir la complejidad de la creación de reglas de seguridad. No puede crear su propia etiqueta de servicio ni especificar qué direcciones IP se incluyen dentro de una etiqueta. Microsoft administra los prefijos de direcciones que incluye la etiqueta de servicio y actualiza automáticamente esta a medida que las direcciones cambian.

## <a name="threat-intelligence"></a>Información sobre amenazas

El filtrado basado en inteligencia sobre amenazas puede habilitarse para que el firewall alerte y deniegue el tráfico desde y hacia los dominios y las direcciones IP malintencionados. La direcciones IP y los dominios proceden de la fuente Inteligencia sobre amenazas de Microsoft.

## <a name="outbound-snat-support"></a>Compatibilidad con SNAT saliente

Todas las direcciones IP de tráfico de red virtual salientes se convierten a direcciones IP públicas de Azure Firewall (traducción de direcciones de red de origen). Puede identificar y permitir el tráfico que se origina en la red virtual y se dirige a los destinos de Internet remotos. Azure Firewall no aplica SNAT cuando la dirección IP de destino es un intervalo de direcciones IP privadas por [IANA RFC 1918](https://tools.ietf.org/html/rfc1918). Si su organización usa un intervalo de direcciones IP públicas para las redes privadas, Azure Firewall aplicará SNAT al tráfico para una de las direcciones IP privadas de firewall en AzureFirewallSubnet.

## <a name="inbound-dnat-support"></a>Compatibilidad con DNAT entrante

El tráfico de red entrante a la dirección IP pública de firewall se traduce (traducción de direcciones de red de destino) y se filtra para las direcciones IP privadas en las redes virtuales.

## <a name="multiple-public-ip-addresses"></a>Varias direcciones IP públicas

> [!IMPORTANT]
> Azure Firewall con varias direcciones IP públicas está disponible mediante Azure Portal, Azure PowerShell, la CLI de Azure, REST y plantillas.


Puede asociar varias direcciones IP públicas (hasta 100) con el firewall.

Esto admite los siguientes escenarios:

- **DNAT**: puede traducir varias instancias de puerto estándar para los servidores back-end. Por ejemplo, si tiene dos direcciones IP públicas, puede traducir el puerto TCP 3389 (RDP) para ambas direcciones IP.
- **SNAT**: hay disponibles puertos adicionales para las conexiones SNAT salientes, lo que reduce la posibilidad de que se agoten los puertos SNAT. En este momento, Azure Firewall selecciona aleatoriamente la dirección IP pública de origen que se usará para una conexión. Si dispone de algún filtro de nivel inferior de la red, deberá permitir todas las direcciones IP públicas asociadas con el firewall.

## <a name="azure-monitor-logging"></a>Registro de Azure Monitor

Todos los eventos se integran en Azure Monitor, lo que permite archivar registros en una cuenta de almacenamiento, transmitir eventos al centro de eventos o enviarlos a los registros de Azure Monitor.

## <a name="known-issues"></a>Problemas conocidos

Azure Firewall presenta los siguientes problemas conocidos:

|Problema  |DESCRIPCIÓN  |Mitigación  |
|---------|---------|---------|
Las reglas de filtrado de red para protocolos que no son TCP/UDP (por ejemplo, ICMP) no funcionan con el tráfico enlazado a Internet|Las reglas de filtrado de red de protocolos que no son TCP/UDP no funcionan con la traducción SNAT a la dirección IP pública. Los protocolos que no son TCP/UDP no se admiten entre subredes de radio y redes virtuales.|Azure Firewall usa Standard Load Balancer, [que actualmente no admite SNAT para los protocolos IP](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#limitations). Se están examinando opciones para admitir este escenario en una versión futura.|
|Falta de compatibilidad entre PowerShell y CLI con ICMP|Azure PowerShell y CLI no admiten ICMP como protocolo válido en las reglas de red.|Aun así se puede usar ICMP como protocolo a través del portal y la API REST. Estamos trabajando para agregar pronto ICMP a PowerShell y la CLI.|
|Las etiquetas FQDN requieren que se establezca una combinación protocolo: puerto|Las reglas de aplicaciones con las etiquetas FQDN requieren la definición de puerto: protocolo.|Puede usar **https** como valor de puerto: protocolo. Estamos trabajando para que este campo sea opcional cuando se usen etiquetas FQDN.|
|No se admite la posibilidad de mover un firewall a otro grupo de recursos o suscripción.|No se admite la posibilidad de mover un firewall a otro grupo de recursos o suscripción.|La compatibilidad con esta funcionalidad está en nuestro mapa de ruta. Para mover un firewall a otro un grupo de recursos o suscripción, debe eliminar la instancia actual y volver a crearla en el nuevo grupo de recursos o suscripción.|
|Intervalo de puertos en reglas de red y aplicación|Los puertos están limitados a 64.000, ya que los puertos altos están reservados para la administración y los sondeos de estado. |Estamos trabajando para reducir esta limitación.|
|Las alertas de inteligencia sobre amenazas pueden estar enmascaradas|Reglas de red con destino 80/443 para las alertas de inteligencia sobre amenazas de las máscaras de filtrado de salida cuando la configuración está establecida en el modo de solo alerta.|Cree filtrado de salida para 80/443 mediante reglas de aplicaciones. También puede cambiar el modo de inteligencia sobre amenazas a **Alertar y denegar**.|
|Azure Firewall usa Azure DNS para la resolución de nombres|Azure Firewall resuelve los FQDN solo mediante Azure DNS. No se admite un servidor DNS personalizado. No hay ningún efecto sobre la resolución de DNS en otras subredes.|Estamos trabajando para reducir esta limitación.|
|SNAT/DNAT de Firewall de Azure no funciona para destinos de IP privadas|La compatibilidad con SNAT/DNAT de Azure Firewall se limita a la entrada/salida de Internet. SNAT/DNAT no funciona actualmente con destinos de IP privada. Por ejemplo, radio a radio.|Se trata de una limitación actual.|
|No se puede eliminar la primera configuración de IP pública|Cada IP pública de Azure Firewall se asigna a una *configuración de IP*.  La primera configuración de IP se asigna durante la implementación del firewall y, normalmente, también contiene una referencia a la subred del firewall (a menos que se configure explícitamente de forma diferente mediante una implementación de plantilla). No se puede eliminar esta configuración de IP porque podría anular la asignación del firewall. Aún podrá cambiar o eliminar la dirección IP pública asociada con esta configuración de IP si el firewall tiene al menos otra dirección IP pública disponible para su uso.|Esto es así por diseño.|
|Availability Zones solo se puede configurar durante la implementación.|Availability Zones solo se puede configurar durante la implementación. No se puede configurar una vez implementado un firewall.|Esto es así por diseño.|
|SNAT en conexiones entrantes|Además de DNAT, en las conexiones mediante la dirección IP pública del firewall (entrante) se aplica SNAT en una de las direcciones IP privadas del firewall. Este requisito hoy en día (también para aplicaciones virtuales de red activa/activa) es para garantizar el enrutamiento simétrico.|Para conservar el código fuente original para HTTP/S, considere el uso de encabezados [XFF](https://en.wikipedia.org/wiki/X-Forwarded-For). Por ejemplo, use un servicio como [Azure Portal](../frontdoor/front-door-http-headers-protocol.md#front-door-service-to-backend) delante del firewall. También puede agregar WAF como parte de Azure Front Door Service y la cadena al firewall.
|El filtrado por nombre de dominio completo de SQL se admite solo en modo de proxy (puerto 1433)|Para Azure SQL Database, Azure SQL Data Warehouse e Instancia administrada de SQL Database:<br><br>Durante la versión preliminar, el filtrado por nombre de dominio completo de SQL se admite solo en modo de proxy (puerto 1433).<br><br>Para IaaS de Azure SQL:<br><br>Si va a usar puertos que no son los estándar, puede especificar esos puertos en las reglas de la aplicación.|Para SQL en modo de redirección, que es el valor predeterminado si se conecta desde dentro de Azure, puede filtrar en su lugar el acceso mediante la etiqueta de servicio de SQL como parte de las reglas de red de Azure Firewall.
|No se permite el tráfico de salida en el puerto TCP 25.| Las conexiones SMTP salientes que usan el puerto TCP 25 se bloquearon. El puerto 25 se usa principalmente para la entrega de correo electrónico sin autenticación. Éste es el comportamiento predeterminado de la plataforma para las máquinas virtuales. Para más información, consulte [Solución de problemas de conectividad SMTP saliente en Azure](../virtual-network/troubleshoot-outbound-smtp-connectivity.md). Sin embargo, a diferencia de las máquinas virtuales, actualmente no es posible habilitar esta funcionalidad en Azure Firewall.|Siga el método recomendado para enviar correo electrónico como se documenta en el artículo de solución de problemas de SMTP. Como alternativa, excluya la máquina virtual que necesita acceso SMTP saliente desde la ruta predeterminada al firewall y, en su lugar, configure el acceso saliente directamente a Internet.

## <a name="next-steps"></a>Pasos siguientes

- [Tutorial: Implementación y configuración de Azure Firewall mediante Azure Portal](tutorial-firewall-deploy-portal.md)
- [Implementación de Azure Firewall mediante una plantilla](deploy-template.md)
- [Creación de un entorno de prueba de Azure Firewall](scripts/sample-create-firewall-test.md)
