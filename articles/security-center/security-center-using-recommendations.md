---
title: Uso de las recomendaciones de Azure Security Center para mejorar la seguridad | Microsoft Docs
description: " Obtenga información sobre cómo usar las recomendaciones y directivas de seguridad en Azure Security Center para ayudar a mitigar un ataque de seguridad. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: v-mohabe
ms.openlocfilehash: 79a5f9a1269bdfc63d9d0b6fffd8458d011b777b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640872"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Uso de las recomendaciones de Azure Security Center para mejorar la seguridad
Puede reducir las posibilidades de que se produzca un incidente de seguridad importante configurando una directiva de seguridad y, luego, implementando las recomendaciones de Azure Security Center. En este artículo se muestra cómo usar las recomendaciones y directivas de seguridad de Security Center para ayudar a mitigar un ataque de seguridad. 

Security Center ejecuta automáticamente un análisis continuo para analizar el estado de seguridad de los recursos de Azure. Cuando Security Center identifica posibles vulnerabilidades de seguridad, crea recomendaciones que lo guiarán por el proceso de configuración de los controles de seguridad necesarios. Security Center actualiza sus recomendaciones en el plazo de 24 horas, con las siguientes excepciones:

- Las recomendaciones de la configuración de seguridad del sistema operativo se actualizan en un plazo de 48 horas.
- Las recomendaciones de los problemas de Endpoint Protection se actualizan en un plazo de 8 horas.

## <a name="scenario"></a>Escenario
Este escenario muestra cómo usar Security Center para ayudar a reducir las posibilidades de que se produzca un incidente de seguridad al supervisar las recomendaciones de Security Center y tomar medidas. El escenario utiliza la compañía ficticia Contoso y los roles de la [Guía de planeamiento y operaciones](security-center-planning-and-operations-guide.md#security-roles-and-access-controls) de Security Center. En este escenario, nos centraremos en los roles siguientes:

![Roles de escenario](./media/security-center-using-recommendations/scenario-roles.png)

Contoso había migrado recientemente algunos de sus recursos locales a Azure. Contoso quiere proteger sus recursos y reducir la vulnerabilidad de los recursos en la nube.

## <a name="use-azure-security-center"></a>Uso de Azure Security Center
David, de seguridad de TI de Contoso, ya ha elegido incorporar Security Center en las suscripciones de Contoso con Azure Security Center para evitar y detectar las vulnerabilidades de seguridad. 

Security Center analiza el estado de seguridad de los recursos de Azure de Contoso y aplica las directivas de seguridad predeterminadas automáticamente. Cuando Security Center identifica vulnerabilidades de seguridad potenciales, crea **recomendaciones** basadas en el conjunto de controles de la política de seguridad. 

David ejecuta el nivel estándar de Azure Security, en todas sus suscripciones para obtener el conjunto completo de recomendaciones y características de seguridad disponibles. Jeff también incorpora todos sus servidores locales existentes que aún no se han migrado a la nube para poder aprovechar las ventajas de la compatibilidad híbrida de Security Center en servidores [Windows](quick-onboard-windows-computer.md) y [Linux](quick-onboard-linux-computer.md).

Jeff es propietario de la carga de trabajo de la nube. Jeff es responsable de aplicar controles de seguridad de acuerdo con las directivas de seguridad de Contoso. 

Jeff realiza las siguientes tareas:

- Supervisar las recomendaciones de seguridad de Security Center
- Evaluar las recomendaciones de seguridad y decidir si se deben aplicar o descartar.
- Aplicar las recomendaciones de seguridad

### <a name="remediate-threats-using-recommendations"></a>Corregir las amenazas según las recomendaciones
Como parte de sus actividades de supervisión diarias, Jeff inicia sesión en Azure y abre Security Center. 

1. Jeff selecciona las suscripciones de la carga de trabajo.

2. Jeff comprueba la **puntuación segura** para obtener una imagen general de cuán seguras son las suscripciones y ve que la puntuación es de 548.

3. Jeff tiene que decidir de qué recomendaciones ocuparse primero. Por lo que Jeff hace clic en Puntuación segura y empieza a encargarse de las recomendaciones en función de cuánto mejoran su [Impacto de la puntuación segura](security-center-secure-score.md).

4. Dado que Jeff tiene una gran cantidad de servidores y VM conectados, decide centrarse en **Procesos y aplicaciones**.

5. Cuando hace clic en **Procesos y aplicaciones**, ve una lista de recomendaciones y se encarga de ellas según el impacto de la puntuación segura.

6. Jeff tiene varias máquinas virtuales que apuntan a Internet y, dado que sus puertos están expuestos, le preocupa que un atacante pueda obtener el control de los servidores. Por lo que Jeff decide usar (**acceso a VM Just-In-Time**) [security-center-just-in-time.md].

Jeff continúa explorando las recomendaciones de prioridad alta y media, y toma decisiones de implementación. Para cada recomendación, Jeff examina la información detallada proporcionada por Security Center para comprender qué recursos se ven afectados, cuál es el impacto de la puntuación segura, qué significa cada recomendación y los pasos de corrección para saber cómo mitigar cada problema.

## <a name="conclusion"></a>Conclusión
Supervisar las recomendaciones de Security Center le ayudar a eliminar vulnerabilidades de seguridad antes de que se produzca un ataque. Cuando aborda las recomendaciones, mejoran la puntuación segura y la postura de seguridad de las cargas de trabajo. Security Center detecta automáticamente los nuevos recursos para implementar, los evalúa con respecto a su directiva de seguridad y proporciona nuevas recomendaciones para protegerlos.


## <a name="next-steps"></a>Pasos siguientes
Asegúrese de haber puesto en práctica un proceso de supervisión, en el que periódicamente compruebe las recomendaciones de Security Center para poder asegurarse de mantener protegidos los recursos a lo largo del tiempo.

En este escenario se muestra cómo usar las recomendaciones y directivas de seguridad de Security Center para ayudar a mitigar un ataque de seguridad. Consulte el [escenario de respuesta a incidentes](security-center-incident-response.md) para saber cómo tener implantado un plan de respuesta a incidentes antes de que tenga lugar un ataque.

Obtenga información sobre cómo responder a amenazas con la [respuesta a incidentes](security-center-incident-response.md).
