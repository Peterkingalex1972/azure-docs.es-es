---
title: 'Métricas, alertas y registros de diagnóstico: Azure Batch | Microsoft Docs'
description: Registre y analice los eventos de registro de diagnóstico de los recursos de la cuenta de Azure Batch como tareas y grupos.
services: batch
documentationcenter: ''
author: laurenhughes
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 63d0196609e432b081e91a49b5b1410431223632
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68323624"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Métricas, alertas y registros de Batch para evaluación de diagnóstico y supervisión

 
En este artículo se explica cómo supervisar una cuenta de Batch con las características de [Azure Monitor](../azure-monitor/overview.md). Azure Monitor recopila [métricas](../azure-monitor/platform/data-platform-metrics.md) y [registros de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md) de recursos en la cuenta de Batch. Recopile y consuma estos datos de diversas maneras para supervisar la cuenta de Batch y diagnosticar problemas. También puede configurar [alertas de métricas](../azure-monitor/platform/alerts-overview.md) para recibir notificaciones cuando una métrica alcance un valor especificado. 

## <a name="batch-metrics"></a>Métricas de Batch

Las métricas son datos de telemetría de Azure (también denominados contadores de rendimiento) que emiten los recursos de Azure y que consume el servicio Azure Monitor. Las métricas de ejemplo en una cuenta de Batch incluyen: Eventos de creación de grupo, Recuento de nodos de baja prioridad y Eventos de tarea completada. 

Consulte la [lista de métricas admitidas de Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Las métricas se caracterizan por:

* Se habilitan de forma predeterminada en cada cuenta de Batch sin ninguna configuración adicional.
* Se generan cada 1 minuto.
* No se guardan automáticamente, sino que tienen un historial acumulado de 30 días. Se pueden conservar las métricas de actividad como parte del registro de diagnóstico.

### <a name="view-metrics"></a>Visualización de métricas

Vea las métricas de la cuenta de Batch en Azure Portal. La página **Información general** de la cuenta muestra de forma predeterminada las métricas principales de nodos, núcleos y tareas. 

Para ver todas las métricas de la cuenta de Batch, siga estos pasos: 

1. En el portal, haga clic en **Todos los servicios** > **Cuentas de Batch** y, luego, haga clic en el nombre de la cuenta de Batch.
2. En **Supervisión**, haga clic en **Métricas**.
3. Seleccione una o varias de las métricas. Si lo desea, seleccione métricas de recursos adicionales mediante las listas desplegables **Suscripciones**, **Grupo de recursos**, **Tipo de recurso** y **Recurso**.

    ![Métricas de Batch](media/batch-diagnostics/metrics-portal.png)

Para recuperar las métricas mediante programación, use las API de Azure Monitor. Para ver ejemplos, consulte [Retrieve Azure Monitor metrics with .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/) (Recuperación de métricas de Azure Monitor con .NET).

## <a name="batch-metric-reliability"></a>Confiabilidad de métricas de Batch

Las métricas están diseñadas para usarse para el análisis de tendencias y datos. La entrega de la métrica no está garantizada y está sujeta a la entrega desordenada, la pérdida de datos y/o la duplicación. No se recomienda el uso de eventos únicos para funciones de alerta o de desencadenador. Vea la sección [Alertas de métricas de Batch](#batch-metric-alerts) para obtener más información sobre cómo establecer umbrales para las alertas.

Es posible que todavía se estén agregando las métricas emitidas en los últimos tres minutos. Durante este período de tiempo, es posible que los valores de la métrica se informen muy por debajo de los reales.

## <a name="batch-metric-alerts"></a>Alertas de métricas de Batch

Opcionalmente, configure *alertas de métricas* en tiempo real que se desencadenen cuando el valor de una métrica especificada supere un umbral asignado. La alerta genera una [notificación](../monitoring-and-diagnostics/insights-alerts-portal.md) que se elige cuando la alerta está "Activada" (si se sobrepasa el umbral y se cumple la condición de alerta), así como cuando queda en estado "Resuelto" (cuando se vuelve a sobrepasa el umbral y ya no se cumple la condición). No se recomienda generar alertas basadas en puntos de datos únicos, ya que las métricas están sujetas a la entrega desordenada, la pérdida de datos y/o la duplicación. Las alertas deben usar los umbrales para tener en cuenta estas incoherencias.

Por ejemplo, podría configurar una alerta de métrica cuando el recuento de núcleos de baja prioridad caiga hasta cierto punto, y así poder ajustar la composición de los grupos. Se recomienda establecer un período de 10 o más minutos en el que se activen alertas si el recuento promedio de núcleos de prioridad baja cae por debajo del valor de umbral para todo el período. No se recomienda generar alertas en un período de 1 a 5 minutos, ya que es posible que las métricas todavía se estén agregando.

Para configurar una alerta de métrica en el portal:

1. Haga clic en **Todos los servicios** > **Cuentas de Batch** y haga clic en el nombre de la cuenta de Batch.
2. En **Supervisión**, haga clic en **Reglas de alerta** > **Agregar alerta de métrica**.
3. Seleccione una métrica, una condición de alerta (por ejemplo, cuando una métrica supere un valor determinado durante un período) y una o más notificaciones.

También puede configurar una alerta casi en tiempo real mediante la [API REST](https://docs.microsoft.com/rest/api/monitor/). Para más información, consulte [Introducción a las alertas](../azure-monitor/platform/alerts-overview.md)

## <a name="batch-diagnostics"></a>Diagnóstico de Batch

Los registros de diagnóstico contienen información que emiten los recursos de Azure que describe el funcionamiento de cada recurso. En Batch, puede recopilar los registros siguientes:

* Eventos de **registros de servicio** que emite el servicio Azure Batch durante la vigencia de un recurso de Batch individual, como un grupo o una tarea. 

* Registros de **métricas** en el nivel de cuenta. 

La configuración para habilitar la recopilación de registros de diagnóstico no está habilitada de forma predeterminada. Habilite explícitamente la configuración de diagnóstico de cada cuenta de Batch que quiera supervisar.

### <a name="log-destinations"></a>Destinos de registro

Un escenario común consiste en seleccionar una cuenta de Azure Storage como destino del registro. Para almacenar los registros en Azure Storage, cree la cuenta antes de habilitar la recopilación de registros. Si asoció una cuenta de almacenamiento con su cuenta de Batch, puede elegir esa cuenta como destino de registro. 

Otros destinos opcionales de los registros de diagnóstico:

* Transmita los eventos de registro de diagnóstico de Batch a un [centro de eventos de Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs puede ingerir millones de eventos por segundo, que posteriormente se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real. 

* Envíe los registros de diagnóstico a los [registros de Azure Monitor](../log-analytics/log-analytics-overview.md), donde puede analizarlos o exportarlos para su análisis en Power BI o Excel.

> [!NOTE]
> Es posible que el almacenamiento o el procesamiento de datos de registro de diagnóstico con servicios de Azure conlleve costos adicionales. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Habilitación de la recopilación de registros de diagnóstico de Batch

1. En el portal, haga clic en **Todos los servicios** > **Cuentas de Batch** y, luego, haga clic en el nombre de la cuenta de Batch.
2. En **Supervisión**, haga clic en **Registros de diagnóstico** > **Turn on diagnostics** (Activar diagnósticos).
3. En **Configuración de diagnóstico**, escriba un nombre para la configuración y elija un destino de registro (cuenta de almacenamiento existente, centro de eventos o registros de Azure Monitor). Seleccione **ServiceLog** o **AllMetrics**, o ambos.

    Cuando seleccione una cuenta de almacenamiento, puede definir opcionalmente una directiva de retención. Si no especifica un número de días de retención, los datos se conservan durante la vigencia de la cuenta de almacenamiento.

4. Haga clic en **Save**(Guardar).

    ![Diagnóstico de Batch](media/batch-diagnostics/diagnostics-portal.png)

Otras opciones para habilitar la recopilación de registros son: usar Azure Monitor en el portal para configurar el diagnóstico, usar una [plantilla de Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md) o usar Azure PowerShell o la CLI de Azure. Consulte [Recopilación y uso de los datos de registro provenientes de los recursos de Azure](../azure-monitor/platform/diagnostic-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Acceso a los registros de diagnóstico en el almacenamiento

Si archiva registros de diagnóstico de Batch en una cuenta de almacenamiento, se crea un contenedor de almacenamiento en la cuenta de almacenamiento en cuanto se produce un evento relacionado. Se crean blobs según el siguiente patrón de nomenclatura:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Ejemplo:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Cada archivo de blob PT1H.json contiene eventos con formato JSON que se producen dentro de la hora especificada en la dirección URL del blob (por ejemplo, h=12). Durante la hora en cuestión, los eventos se anexan al archivo PT1H.json a medida que se producen. El valor de los minutos (m=00) siempre es 00, ya que los eventos de los registros de diagnóstico se dividen en blobs individuales por hora. (Todas las horas se muestran en UTC).


Para más información sobre el esquema de registros de diagnóstico en la cuenta de almacenamiento, consulte [Archivo de registros de diagnóstico de Azure](../azure-monitor/platform/archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Para acceder a los registros de la cuenta de almacenamiento mediante programación, use las API de Storage. 

### <a name="service-log-events"></a>Eventos del registro del servicio
Los registros del servicio Azure Batch, cuando se recopilan, contienen los eventos que emite el servicio Azure Batch durante la vigencia de un recurso de Batch individual, como un grupo o una tarea. Cada evento que emite Batch se registra en formato JSON. Por ejemplo, este es el cuerpo de un **evento de creación de grupos** de ejemplo:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Actualmente, el servicio Batch emite los siguientes eventos del registro del servicio. Es posible que la lista no sea exhaustiva, ya que se pueden haber agregado eventos adicionales desde la última vez que se actualizó este artículo.

| **Eventos del registro del servicio** |
| --- |
| [Creación del grupo](batch-pool-create-event.md) |
| [Inicio de eliminación del grupo](batch-pool-delete-start-event.md) |
| [Finalización de eliminación del grupo](batch-pool-delete-complete-event.md) |
| [Inicio de cambio de tamaño del grupo](batch-pool-resize-start-event.md) |
| [Finalización de cambio de tamaño del grupo](batch-pool-resize-complete-event.md) |
| [Inicio de tarea](batch-task-start-event.md) |
| [Finalización de tarea](batch-task-complete-event.md) |
| [Error en la tarea](batch-task-fail-event.md) |



## <a name="next-steps"></a>Pasos siguientes

* Obtenga información acerca de las [API y herramientas de Batch](batch-apis-tools.md) disponibles para la creación de soluciones de Batch.
* Más información sobre la [supervisión de soluciones de Batch](monitoring-overview.md).
