---
title: Administración de alertas de seguridad en Azure Security Center | Microsoft Docs
description: Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar y responder a las alertas de seguridad.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2018
ms.author: v-mohabe
ms.openlocfilehash: 39849514d772f128434daad590de22f941245af7
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516095"
---
# <a name="managing-and-responding-to-security-alerts-in-azure-security-center"></a>Administración y respuesta a las alertas de seguridad en el Centro de seguridad de Azure
Este documento le ayuda a usar Azure Security Center para administrar las alertas de seguridad y responder a ellas.

> [!NOTE]
> Para habilitar las detecciones avanzadas, actualice a la versión estándar de Azure Security Center. Hay una evaluación gratuita disponible. Para realizar la actualización, seleccione el plan de tarifa en la [directiva de seguridad](tutorial-security-policy.md). Consulte [Precios de Azure Security Center](security-center-pricing.md) para más información.
>
>

## <a name="what-are-security-alerts"></a>¿Qué son las alertas de seguridad?
Security Center recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente y recomendaciones para corregir un ataque.


> [!NOTE]
> Para más información acerca de cómo actúan las funcionalidades de detección de Security Center, consulte [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md).
>
>

## <a name="managing-security-alerts"></a>Administración de alertas de seguridad
Puede revisar las alertas actuales en el icono **Alertas de seguridad** . Siga estos pasos para ver más detalles sobre cada alerta:

1. En el panel Security Center, verá el icono **Alertas de seguridad**.

    ![Icono Alertas de seguridad en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig1-ga.png)

2. Haga clic en el icono para abrir **Alertas de seguridad** y ver más detalles sobre las alertas.

   ![Las alertas de seguridad en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig2-ga.png)

En la parte inferior de esta página aparecen los detalles de cada alerta. Para ordenar, haga clic en la columna que desea ordenar. A continuación se muestra la definición de cada columna:

* **Descripción**: una breve explicación de la alerta.
* **Recuento**: una lista de todas las alertas de este tipo específico que se han detectado en un día concreto.
* **Detectada por**: el servicio responsable de desencadenar la alerta.
* **Fecha**: la fecha en que se ha producido el evento.
* **Estado**: el estado actual de esa alerta. Existen dos tipos de servicios:
  * **Activa**: se ha detectado la alerta de seguridad.
  * **Descartada**: el usuario ha descartado la alerta de seguridad. Este estado suele utilizarse para alertas que se investigaron pero que se mitigaron o se observó que no planteaban un ataque real.
* **Gravedad**: el nivel de gravedad, que puede ser alto, medio o bajo.

> [!NOTE]
> Las alertas de seguridad que genera Security Center también aparecerá en el registro de actividad de Azure. Para más información acerca de cómo acceder al registros de actividad en Azure, consulte [Visualización de registros de actividad para auditar las acciones sobre los recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
>


### <a name="alert-severity"></a>Gravedad de las alertas

-   **Alta**: hay una probabilidad elevada de que el recurso esté en peligro. Debe investigarse de inmediato. El grado de certeza de Security Center sobre la mala intención de la acción y los hallazgos utilizados para emitir la alerta es elevado. Una alerta de este tipo sería podría detectar la ejecución de una herramienta malintencionada conocida; por ejemplo, Mimikatz, una herramienta que se usa habitualmente para robar credenciales. 
-   **Media**: es probable que sea actividad sospechosa que puede indicar que un recurso está en peligro.
El grado de certeza de Security Center sobre el análisis o los hallazgos es medio, mientras que el grado de certeza sobre la mala intención es medio o alto. Suelen tratarse de detecciones basadas en anomalías o aprendizaje automático. Por ejemplo, un intento de inicio de sesión desde una ubicación anómala.
-   **Baja**: podría tratarse de un hallazgo benigno o de un ataque bloqueado. 
    - Security Center no tiene la certeza suficiente de que la intención fuera mala y la actividad podría ser inofensiva. Por ejemplo, borrar un registro es una acción que podría producirse si un atacante intenta ocultar sus huellas, pero en muchos casos es una operación rutinaria que realizan los administradores.
    - Por lo general, Security Center no avisa cuando se bloquean ataques a menos que se considere un caso interesante que convenga examinar. 
-   **Informativas**: solo verá las alertas informativas cuando explore en profundidad un incidente de seguridad, o si usa la API REST con un determinado identificador de alerta. Normalmente, las incidencias se componen de varias alertas, algunas de las cuales pueden parecer meramente informativas, aunque a tenor de otras alertas puede ser conveniente investigarlas.  

> [!NOTE]
> Si usa la versión de API **2015-06-01-preview**, hay diferencias en los tipos de gravedad de alarma que se aplican a cada escenario, con respecto a lo que se ha indicado antes.  

### <a name="filtering-alerts"></a>Filtrado de alertas
Puede filtrar alertas en función de la fecha, el estado y la gravedad. Puede resultar útil filtrar las alertas en aquellos escenarios en que necesite restringir el ámbito de las alertas de seguridad que se muestran. Por ejemplo, podría comprobar las alertas de seguridad que se produjeron en las 24 horas anteriores, ya que se está investigando una posible brecha en el sistema.

1. Haga clic en **Filtro** en **Alertas de seguridad**. Se abre la hoja **Filtro**, donde podrá seleccionar los valores de fecha, estado y gravedad que desee ver.

    ![Filtrado de alertas en Security Center](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig3-2017.png)

### <a name="respond-to-security-alerts"></a>Responder a alertas de seguridad
Seleccione una alerta de seguridad para ver más información sobre el evento o los eventos que la desencadenaron y, si existen, los pasos que debe seguir para corregir un ataque. Las alertas de seguridad se agrupan según el tipo y la fecha. Al hacer clic en una alerta de seguridad se abre una página que contiene una lista de las alertas agrupadas.

![Responder a alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig5-ga.png)

En este caso, las alertas desencadenadas hacen referencia a las actividades sospechosas del Protocolo de escritorio remoto (RDP). En la primera columna se muestran los recursos atacados, en la segunda, las veces que el recurso fue atacado, en la tercera, la hora en la que se produjo el ataque, en la cuarta, el estado de la alerta y, en la quinta, la gravedad del ataque. Después de revisar esta información, haga clic en el recurso atacado.

![Sugerencias sobre qué hacer con las alertas de seguridad en el Centro de seguridad de Azure](./media/security-center-managing-and-responding-alerts/security-center-managing-and-responding-alerts-fig6-ga.png)

En el campo **Descripción**, encontrará más detalles sobre este evento. Estos detalles adicionales ofrecen información detallada sobre lo que activó la alerta de seguridad, el recurso de destino, la dirección IP de origen si corresponde, y recomendaciones sobre cómo corregirla.  En algunos casos, la dirección IP de origen está vacía (no disponible) porque no todos los registros de eventos de seguridad de Windows incluyen la dirección IP.

La corrección sugerida por Security Center varía según la alerta de seguridad. En algunos casos, tendrá que utilizar otras capacidades de Azure para implementar la corrección recomendada. Por ejemplo, la solución para este ataque consiste en no permitir la dirección IP que lo está generando mediante una [ACL de red](../virtual-network/virtual-networks-acl.md) o una regla de [grupo de seguridad de red](../virtual-network/security-overview.md#security-rules). Para más información sobre los distintos tipos de alertas, consulte [Tipos de alertas de seguridad](security-center-alerts-overview.md#security-alert-types).

> [!NOTE]
> Security Center ha lanzado un nuevo conjunto de detecciones en versión preliminar limitada que aprovecha los registros de auditoría, un marco de auditoría común, para detectar comportamientos malintencionados en máquinas Linux. Envíe un correo electrónico con los identificadores de suscripción a [nuestro equipo](mailto:ASC_linuxdetections@microsoft.com) para unirse a la versión preliminar.


## <a name="see-also"></a>Otras referencias
En este documento ha aprendido a configurar directivas de seguridad en el Centro de seguridad. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Control de incidentes de seguridad en Azure Security Center](security-center-incident.md)
* [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md)
* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md)
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](https://blogs.msdn.com/b/azuresecurity/) : encuentre entradas de blog sobre el cumplimiento y la seguridad de Azure.
