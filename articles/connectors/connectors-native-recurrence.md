---
title: 'Programación de tareas periódicas con desencadenador periodicidad: Azure Logic Apps'
description: Programación y ejecución de tareas y flujos de trabajo automatizados periódicos con el desencadenador de periodicidad en Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: f5fc778ee4d8f91232bc732cc276f642f748b29d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66297539"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-recurrence-trigger-in-azure-logic-apps"></a>Creación, programación y ejecución de tareas y flujos de trabajo periódicos con el desencadenador de periodicidad en Azure Logic Apps

Para ejecutar con regularidad las tareas, procesos o trabajos en una programación específica, puede iniciar el flujo de trabajo de la aplicación lógica con el desencadenador de **periodicidad-programación** integrado. Puede establecer una fecha y hora, así como una zona horaria para iniciar el flujo de trabajo y una periodicidad para la repetición de ese flujo de trabajo. Si se pierde la periodicidad por cualquier motivo, este desencadenador hace que continúe en el siguiente intervalo programado. Para obtener más información sobre los desencadenadores y las acciones de programación integradas, consulte [Programación y ejecución de tareas y flujos de trabajo automatizados periódicos con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

A continuación se muestran algunos patrones compatibles con este desencadenador junto con periodicidades más avanzadas y programaciones complejas:

* Ejecutar inmediatamente y repetir cada *n* segundos, minutos, horas, días, semanas o meses.

* Empezar a una fecha y hora específicas, y después ejecutar y repetir cada *n* segundos, minutos, horas, días, semanas o meses.

* Ejecutar y repetir una o varias veces al día, por ejemplo, a las 8:00 a. m. y a las 5:00 p. m.

* Ejecutar y repetir cada semana, pero solo días específicos, como el sábado y domingo.

* Ejecutar y repetir cada semana, pero solo días específicos a horas concretas, como de lunes a viernes, a las 8:00 a. m. y a las 5:00 p. m.

Para ver las diferencias entre este desencadenador y el desencadenador de ventana deslizante, o para obtener más información sobre la programación de flujos de trabajo periódicos, consulte [Programación y ejecución de tareas, procesos y flujos de trabajo automatizados periódicos con Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Si desea desencadenar la aplicación lógica y ejecutarla solo una vez en el futuro, consulte [Ejecución de trabajos una sola vez](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once).

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción, puede [registrarse para obtener una cuenta de Azure gratuita](https://azure.microsoft.com/free/).

* Conocimientos básicos sobre [aplicaciones lógicas](../logic-apps/logic-apps-overview.md). Si es la primera vez que interactúa con las aplicaciones lógicas, consulte el artículo sobre [cómo crear la primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="add-recurrence-trigger"></a>Adición de desencadenador "Periodicidad"

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Crear una aplicación lógica en blanco.

1. Cuando se abra el Diseñador de aplicaciones lógicas, en el cuadro de búsqueda, escriba el filtro "recurrence" (periodicidad). En la lista de desencadenadores, seleccione este desencadenador como primer paso en el flujo de trabajo de la aplicación lógica: **Periodicidad**

   ![Seleccionar desencadenador "Periodicidad"](./media/connectors-native-recurrence/add-recurrence-trigger.png)

1. Defina el intervalo y la frecuencia de periodicidad. En este ejemplo, defina estas propiedades para ejecutar el flujo de trabajo todas las semanas.

   ![Establecer el intervalo y la frecuencia](./media/connectors-native-recurrence/recurrence-trigger-details.png)

   | Propiedad | Obligatorio | Nombre JSON | Type | DESCRIPCIÓN |
   |----------|----------|-----------|------|-------------|
   | **Intervalo** | Sí | interval | Entero | Entero positivo que describe la frecuencia con la que se ejecuta el flujo de trabajo. Estos son los intervalos mínimo y máximo: <p>- Mes: 1-16 meses </br>- Día: 1-500 días </br>- Hora: 1-12 000 horas </br>- Minuto: 1-72 000 minutos </br>- Segundo: 1-9 999 999 segundos<p>Por ejemplo, si el intervalo es 6 y la frecuencia es "month", la periodicidad es cada 6 meses. |
   | **Frecuencia** | Sí | frequency | Cadena | Unidad de tiempo que se usa para la periodicidad: **Segundo**, **Minuto**, **Hora**, **Día**, **Semana** o **Mes** |
   ||||||

   Para ver más opciones de programación, abra la lista **Agregar nuevo parámetro**. 
   Las opciones que seleccione aparecerán en el desencadenador después de la selección.

   ![Opciones avanzadas de programación](./media/connectors-native-recurrence/recurrence-trigger-more-options-details.png)

   | Propiedad | Obligatorio | Nombre JSON | Type | DESCRIPCIÓN |
   |----------|----------|-----------|------|-------------|
   | **Zona horaria** | Sin | timeZone | Cadena | Solo se aplica cuando se especifica una hora de inicio porque este desencadenador no acepta [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Seleccione la zona horaria que desea aplicar. |
   | **Hora de inicio** | Sin | startTime | Cadena | Proporcione una fecha y hora de inicio en este formato: <p>AAAA-MM-DDThh:mm:ss si selecciona una zona horaria <p>O bien <p>AAAA-MM-DDThh:mm:ssZ si no selecciona una zona horaria <p>Por ejemplo, si desea la fecha del 18 de septiembre de 2017 a las 14:00, especifique entonces "2017-09-18T14:00:00" y especifique una zona horaria como "Hora estándar del Pacífico". O bien, especifique "2017-09-18T14:00:00Z" sin una zona horaria. <p>**Nota:** Esta hora de inicio debe seguir la [especificación de fecha y hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) en [formato de hora y fecha UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), pero sin una [diferencia horaria con UTC](https://en.wikipedia.org/wiki/UTC_offset). Si no se selecciona una zona horaria, debe agregar la letra "Z" al final sin espacios. Esta "Z" se refiere al equivalente de [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para las programaciones simples, la hora de inicio es la primera aparición, mientras que para programaciones complejas, el desencadenador no se activa antes de la hora de inicio. [ *¿De qué formas puedo usar la fecha y hora de inicio?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   | **En estos días** | Sin | weekDays | Cadena o matriz de cadenas | Si selecciona "Semana", puede seleccionar uno o más días que desee ejecutar el flujo de trabajo: **Lunes**, **Martes**, **Miércoles**, **Jueves**, **Viernes**, **Sábado** y **Domingo** |
   | **A estas horas** | Sin | hours | Entero o matriz de enteros | Si selecciona "Day" o "Week", puede especificar uno o varios enteros de 0 a 23 como las horas del día en las que desea ejecutar el flujo de trabajo. <p><p>Por ejemplo, si especifica "10", "12" y "14", obtendrá las 10:00, las 12:00 y las 14:00 para las horas del día, pero los minutos del día se calcularán en función del momento en el que se inicie la periodicidad. Para configurar los minutos del día, especifique el valor de la propiedad **En estos minutos**. |
   | **En estos minutos** | Sin | minutes | Entero o matriz de enteros | Si selecciona "Day" o "Week", puede seleccionar uno o varios enteros de 0 a 59 como los minutos de la hora en los que desea ejecutar el flujo de trabajo. <p>Por ejemplo, puede especificar "30" como la marca de minuto y, utilizando el ejemplo anterior para las horas del día, obtendrá 10:30 a. m., 12:30 p. m. y las 2:30 p. m. |
   |||||

   Por ejemplo, supongamos que hoy es lunes, 4 de septiembre de 2017. El siguiente desencadenador de periodicidad no se inicia *antes* de la fecha y hora de inicio, establecida el lunes, 18 de septiembre de 2017, a las 8:00 a. m. PST. Sin embargo, la programación de periodicidad está establecida para las 10:30 a. m., las 12:30 p. m. y las 2:30 p. m., solo los lunes. Por tanto, el desencadenador inicia y crea una instancia de flujo de trabajo de aplicación lógica por primera vez a las 10:30 a. m. Para más información sobre cómo funcionan las horas de inicio, vea estos [ejemplos de hora de inicio](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time).

   Las futuras ejecuciones se realizan a las 12:30 p. m. y a las 2:30 p. m. ese mismo día. Cada periodicidad crea su propia instancia de flujo de trabajo. Después de eso, toda la programación se repite el próximo lunes. [ *¿Qué otros ejemplos se hay?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#example-recurrences)

   ![Ejemplo de programación avanzada](./media/connectors-native-recurrence/recurrence-trigger-more-options-advanced-schedule.png)

   > [!NOTE]
   > El desencadenador muestra una vista previa de la periodicidad especificada solo si selecciona la frecuencia "Day" o "Week".

1. Ahora cree el flujo de trabajo restante con otras acciones. Para ver más acciones que se puedan agregar, consulte [Conectores para Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---recurrence"></a>Definición de flujo de trabajo: periodicidad

En la definición de flujo de trabajo subyacente de la aplicación lógica, que usa JSON, podrá ver la [definición del desencadenador de periodicidad](../logic-apps/logic-apps-workflow-actions-triggers.md#recurrence-trigger) con las opciones que eligió. Para ver esta definición, en la barra de herramientas del diseñador, elija **Vista Código**. Para volver al diseñador, elija la barra de herramientas del diseñador, **Diseñador**.

En este ejemplo se muestra el aspecto que podrá tener una definición de desencadenador de periodicidad en una definición de flujo de trabajo subyacente:

``` json
"triggers": {
   "Recurrence": {
      "type": "Recurrence",
      "recurrence": {
         "frequency": "Week",
         "interval": 1,
         "schedule": {
            "hours": [
               10,
               12,
               14
            ],
            "minutes": [
               30
            ],
            "weekDays": [
               "Monday"
            ]
         },
         "startTime": "2017-09-07T14:00:00Z",
         "timeZone": "Pacific Standard Time"
      }
   }
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Pausa de flujos de trabajo con acciones de retraso](../connectors/connectors-native-delay.md)
* [Conectores de Logic Apps](../connectors/apis-list.md)
