---
title: 'Firewall de aplicaciones web de Azure: preguntas más frecuentes'
description: Esta página proporciona respuestas a las preguntas más frecuentes acerca de Azure Front Door Service.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/10/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: c993e465bc439ff52cba3241dbff64b7655d1f12
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849169"
---
# <a name="frequently-asked-questions-for-azure-web-application-firewall"></a>Preguntas más frecuentes sobre el firewall de aplicaciones web de Azure

En este artículo se responden preguntas comunes sobre la funcionalidad y las características del firewall de aplicaciones web (WAF) de Azure. 

## <a name="what-is-azure-waf"></a>¿Qué es WAF de Azure?

WAF de Azure es un firewall de aplicaciones web que ayuda a proteger sus aplicaciones web frente a amenazas comunes como inyección de código SQL, el scripting entre sitios y otras vulnerabilidades de seguridad de la web. Puede definir una directiva de WAF, que consta de una combinación de reglas personalizadas y administradas para controlar el acceso a sus aplicaciones web.

Para las aplicaciones web hospedadas en el servicio Application Gateway o Azure Front Door Service, se puede aplicar una directiva de WAF de Azure.

## <a name="what-is-waf-for-azure-front-door-service"></a>¿Qué es WAF para Azure Front Door Service? 

Azure Front Door es una red de entrega de contenido y de aplicaciones altamente escalable y distribuida globalmente. Cuando WAF de Azure se integra con Front Door, detiene los ataques por denegación de servicio y de aplicaciones dirigidas en el borde de la red de Azure. De este modo, cierra los orígenes de los ataques antes de que entren en la red virtual y ofrece protección sin sacrificar el rendimiento.

## <a name="does-azure-waf-support-https"></a>¿WAF de Azure admite HTTPS?

Front Door Service ofrece la descarga de SSL. WAF se integra de forma nativa con Front Door y puede inspeccionar una solicitud una vez se descifra.

## <a name="does-azure-waf-support-ipv6"></a>¿WAF de Azure admite IPv6?

Sí. Puede configurar restricciones de IP para IPv4 e IPv6.

## <a name="how-up-to-date-are-the-managed-rule-sets"></a>¿En qué medida están actualizados los conjuntos de reglas administrados?

Hacemos todo lo posible para mantenernos al día con el panorama de amenazas que cambia constantemente. Una vez que se actualiza una regla nueva, se agrega al conjunto de reglas predeterminado con un nuevo número de versión.

## <a name="what-is-the-propagation-time-if-i-make-a-change-to-my-waf-policy"></a>¿Cuál es el tiempo de propagación si realizo un cambio en mi directiva de WAF?

El proceso de implementación global de una directiva de WAF suele tardar aproximadamente 5 minutos y a menudo se completa antes.

## <a name="can-waf-policies-be-different-for-different-regions"></a>¿Las directivas de WAF pueden ser diferentes según las distintas regiones?

Cuando se integra con Front Door Service, WAF es un recurso global. Se aplica la misma configuración en todas las ubicaciones de Front Door.
 
## <a name="how-do-i-limit-access-to-my-back-end-to-be-from-front-door-only"></a>¿Cómo puedo limitar el acceso a mi back-end desde Front Door únicamente?

Puede configurar la lista de control de acceso de IP en el back-end para permitir solo los intervalos de direcciones IP salientes de Front Door y denegar cualquier acceso directo desde Internet. Se admite el uso de etiquetas de servicio en la red virtual. Además, puede verificar que el campo de encabezado HTTP X-Forwarded-Host es válido para su aplicación web.




## <a name="which-azure-waf-options-should-i-choose"></a>¿Qué opciones de WAF de Azure debería elegir?

Hay dos opciones al aplicar las directivas de WAF en Azure. WAF con Azure Front Door es una solución de seguridad de borde distribuida globalmente. WAF con Application Gateway es una solución regional y dedicada. Le recomendamos que elija una solución basada en los requisitos de seguridad y rendimiento generales. Para obtener más información, consulte [Equilibrio de carga con el conjunto de entrega de aplicaciones de Azure](https://docs.microsoft.com/azure/frontdoor/front-door-lb-with-azure-app-delivery-suite).


## <a name="do-you-support-same-waf-features-in-all-integrated-platforms"></a>¿Se admiten las mismas características de WAF en todas las plataformas integradas?

Actualmente, las reglas ModSec CRS 2.2.9 y CRS 3.0 solo se admiten con WAF en Application Gateway. Las reglas de limitación de volumen, de filtrado geográfico y de conjuntos de reglas predeterminadas administradas por Azure solo se admiten con WAF en Azure Front Door.

## <a name="is-ddos-protection-integrated-with-front-door"></a>¿La protección contra DDoS está integrada con Door Front? 

Gracias a que Azure Front Door está distribuido globalmente en los bordes de la red de Azure, puede absorber y aislar geográficamente los ataques de gran volumen. Puede crear directivas personalizadas de WAF para bloquear automáticamente los ataques HTTP(S) que tienen firmas conocidas, así como para limitar el volumen de estos. Además, puede habilitar el estándar DDoS Protection en la red virtual donde se implementan sus back-ends. Los clientes del estándar Azure DDoS Protection reciben ventajas adicionales, como protección de los costos, garantía del SLA y acceso a expertos del equipo de respuesta rápida de DDoS para obtener ayuda inmediata durante un ataque. 

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre el [firewall de aplicaciones web de Azure](waf-overview.md).
- Obtenga más información acerca de [Azure Front Door](front-door-overview.md).
