---
title: Introducción a Protección contra DDoS de Azure estándar| Microsoft Docs
description: Conozca el servicio Protección contra DDoS de Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 41e9d88df49d153089e6dc7a12c5873ccc167279
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65209453"
---
# <a name="azure-ddos-protection-standard-overview"></a>Introducción a Protección contra DDoS de Azure estándar

Los ataques por denegación de servicio distribuido (DDoS) son uno de los problemas de seguridad y disponibilidad más extendidos a los que se enfrentan los clientes que mueven sus aplicaciones a la nube. Un ataque DDoS intenta agotar los recursos de una aplicación haciendo que esta no esté disponible para los usuarios legítimos. Los ataques DDoS pueden ir dirigidos a cualquier punto de conexión que sea públicamente accesible a través de Internet.

Azure DDoS Protection, junto con los procedimientos recomendados de diseño de aplicaciones, constituyen una defensa frente a los ataques DDoS. La protección contra DDoS de Azure proporciona los siguientes niveles de servicio:

- **Básico**: habilitado de forma automática como parte de la plataforma de Azure. La supervisión continua del tráfico y la reducción en tiempo real de los ataques a nivel de red más comunes ofrecen la misma defensa que usan los servicios en línea de Microsoft. Puede usarse la escala completa de una red global de Azure para distribuir y reducir el tráfico de ataques en las distintas regiones. Se proporciona protección para [direcciones IP públicas](virtual-network-public-ip-address.md) de Azure IPv4 e IPv6.
- **Estándar**: ofrece funciones adicionales de reducción de ataques en comparación con el nivel de servicio básico, adaptadas específicamente a los recursos de Azure Virtual Network. El servicio Protección contra DDoS estándar es fácil de habilitar y no requiere ningún cambio en la aplicación. Las directivas de protección se ajustan a través de la supervisión del tráfico dedicado y los algoritmos de Machine Learning. Las directivas se aplican a direcciones IP públicas asociadas a recursos implementados en redes virtuales, como instancias de Azure Load Balancer, Azure Application Gateway y Azure Service Fabric, pero esta protección no se aplica a las instancias de App Service Environment. La telemetría en tiempo real está disponible a través de las vistas de Azure Monitor durante un ataque y para el historial. En la configuración de diagnóstico, hay disponibles análisis avanzados de mitigación de ataques. La protección del nivel de aplicación se puede agregar mediante el [firewall de aplicaciones web de Azure Application Gateway Web](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o instalando un firewall de terceros desde Azure Marketplace. Se proporciona protección para [direcciones IP públicas](virtual-network-public-ip-address.md) de Azure IPv4 e IPv6.

![Nivel básico de Azure DDoS Protection frente al nivel Estándar](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Tipos de ataques DDoS que mitiga Protección contra DDoS estándar

El servicio Protección contra DDoS estándar puede mitigar los tipos de ataque siguientes:

- **Ataques volumétricos**: el objetivo del ataque es desbordar la capa de la red con una gran cantidad de tráfico aparentemente legítimo. Esto incluye ataques de tipo "flood" de UDP, de amplificación y otros ataques de tipo "flood" de paquetes falsificados. El servicio Protección contra DDoS estándar mitiga estos posibles ataques de varios gigabytes, ya que los absorbe y los limpia automáticamente aprovechando la escala de red global de Azure.
- **Ataques a protocolos**: estos ataques representan un destino inaccesible al aprovechar una vulnerabilidad en la pila de protocolo en los niveles 3 y 4. Esto incluye ataques de tipo “flood” de SYN, ataques de reflejo y otros ataques de protocolo. El servicio Protección contra DDoS estándar mitiga estos ataques al diferenciar entre el tráfico malintencionado y el legítimo. Para ello, interactúa con el cliente y bloquea el tráfico malintencionado. 
- **Ataques de nivel de recurso (aplicación)** : estos ataques van dirigidos a paquetes de aplicaciones web y su objetivo es interrumpir la transmisión de datos entre hosts. Los ataques incluyen infracciones de protocolo HTTP, inyección de código SQL, scripts de sitios y otros ataques de nivel 7. Use el [firewall de aplicaciones web de Azure Application Gateway](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) con Protección contra DDoS estándar para defenderse de estos ataques. También existen ofertas de firewall de aplicaciones web de terceros disponibles en [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

Protección contra DDoS estándar protege los recursos de las redes virtuales, incluidas las direcciones IP públicas asociadas a máquinas virtuales, los equilibradores de carga y las puertas de enlace de aplicaciones. Cuando se combina con el firewall de aplicaciones web de Application Gateway, Protección contra DDoS estándar puede proporcionar funciones de total mitigación en niveles del 3 al 7.

## <a name="ddos-protection-standard-features"></a>Características de Protección contra DDoS estándar

![Funciones de DDoS](./media/ddos-protection-overview/ddosfeatures.png)

Entre las características de Protección contra DDoS estándar se incluyen:

- **Integración de plataforma nativa:** integrado de forma nativa en Azure. Incluye la configuración a través de Azure Portal. Protección contra DDoS estándar comprende sus recursos y la configuración de recursos.
- **Protección llave en mano:** la configuración simplificada protege de inmediato todos los recursos de una red virtual desde el momento en que se habilita DDoS Protection Estándar. No se requiere intervención ni definición del usuario. Protección contra DDoS estándar mitiga el ataque de forma instantánea y automática una vez detectado.
- **Supervisión continua del tráfico:** los patrones de tráfico de la aplicación se supervisan de forma ininterrumpida en busca de indicadores de ataques DDoS. La mitigación se realiza cuando se sobrepasan las directivas de protección.
- **Ajuste adaptable:** la generación de perfiles de tráfico inteligente va conociendo con el tiempo el tráfico de la aplicación y selecciona y actualiza el perfil que resulta más adecuado para el servicio. El perfil se ajusta a medida que el tráfico cambia con el tiempo.
- **Protección multicapa:** proporciona protección contra DDoS de pila completa cuando se usa con un firewall de aplicaciones web.
- **Escala de mitigación amplia:** se pueden mitigar más de 60 tipos de ataque diferentes con capacidad global para protegerse contra los ataques DDoS más conocidos.
- **Análisis de ataques:** ofrece informes detallados en incrementos de cinco minutos durante un ataque y un resumen completo después de que el ataque termine. Transmita los registros del flujo de mitigación a un sistema de administración de eventos e información de seguridad (SIEM) sin conexión para supervisar el sistema casi en tiempo real durante un ataque.
- **Métricas de ataques:** con Azure Monitor se puede acceder a un resumen de métricas de cada ataque.
- **Alertas de ataques:** las alertas se pueden configurar en el inicio y la detención de un ataque y a lo largo de la duración del ataque mediante métricas de ataque integradas. Las alertas se integran en el software operativo como registros de Microsoft Azure Monitor, Splunk, Azure Storage, correo electrónico y Azure Portal.
- **Garantía de costo:** créditos para servicio de escalado horizontal de aplicaciones y transferencia de datos para ataques de DDoS documentados.

## <a name="ddos-protection-standard-mitigation"></a>Mitigación mediante Protección contra DDoS estándar

El servicio Protección contra DDoS estándar supervisa el uso de tráfico real y lo compara constantemente con los umbrales definidos en la directiva de DDoS. Cuando se supera el umbral de tráfico, se inicia automáticamente la mitigación de DDoS. Cuando el tráfico vuelve a estar por debajo del umbral, se quita la mitigación.

![Mitigación](./media/ddos-protection-overview/mitigation.png)

Durante la mitigación, el servicio Protección contra DDoS redirige el tráfico enviado al recurso protegido y realiza varias comprobaciones, como las que se indican a continuación:

- Asegurarse de que los paquetes se ajustan a las especificaciones de Internet y no tienen un formato incorrecto.
- Interactúe con el cliente para determinar si el tráfico es un posible paquete falsificado (por ejemplo, Autorización de SYN o Cookie de SYN o colocando un paquete para que el origen lo retransmita).
- Limitar la velocidad de los paquetes si no se puede realizar ningún otro método de cumplimiento.

El servicio Protección contra DDoS bloquea el tráfico del ataque y reenvía el tráfico restante al destino previsto. En un intervalo de pocos minutos tras la detección del ataque, recibe una notificación mediante las métricas de Azure Monitor. Si configura el registro de datos de telemetría de Protección contra DDoS estándar, puede escribir los registros en opciones disponibles para su posterior análisis. Los datos métricos de Azure Monitor para Protección contra DDoS estándar se conservan actualmente durante 30 días.

Microsoft se ha asociado con [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) para crear una interfaz en la que pueda generar tráfico destinado a las direcciones IP públicas que tengan habilitado el servicio DDoS Protection con fines de simulación. La simulación de BreakPoint Cloud le permite:

- Comprobar en qué medida Microsoft Azure DDoS Protection estándar protege sus recursos de Azure frente a ataques de DDoS
- Optimizar el proceso de respuesta a incidentes durante el ataque de DDoS.
- Documentar el cumplimiento normativo de DDoS.
- Enseñar a los equipos de seguridad de red.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del servicio Protección contra DDoS estándar](manage-ddos-protection.md)
