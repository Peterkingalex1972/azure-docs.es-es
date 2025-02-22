---
title: Registro de diagnósticos
titleSuffix: Azure Cognitive Services
description: Esta guía proporciona instrucciones paso a paso para habilitar el registro de diagnóstico para un servicio de Azure Cognitive Services. Estos registros proporcionan datos exhaustivos y frecuentes sobre el funcionamiento de un recurso que se utilizan para la identificación y depuración del problema.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 06/14/2019
ms.author: erhopf
ms.openlocfilehash: cd380b4e2a7c05f0beedc2ab102b268aa4068f66
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516369"
---
# <a name="enable-diagnostic-logging-for-azure-cognitive-services"></a>Habilitación del registro de diagnóstico para Azure Cognitive Services

Esta guía proporciona instrucciones paso a paso para habilitar el registro de diagnóstico para un servicio de Azure Cognitive Services. Estos registros proporcionan datos exhaustivos y frecuentes sobre el funcionamiento de un recurso que se utilizan para la identificación y depuración del problema. Antes de continuar, debe tener una cuenta de Azure con una suscripción a al menos un servicio de Cognitive Services, como [Bing Web Search](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/overview), [Servicios de voz](https://docs.microsoft.com/azure/cognitive-services/speech-service/overview) o [LUIS](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis).

## <a name="prerequisites"></a>Requisitos previos

Para habilitar el registro de diagnóstico, necesitará un lugar donde almacenar los datos de registro. En este tutorial se utiliza Azure Storage y Log Analytics.

* [Azure Storage](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-archive-diagnostic-logs): conserva los registros de diagnóstico para auditorías de directivas, análisis estáticos o copias de seguridad. La cuenta de almacenamiento no tiene que estar en la misma suscripción que la del recurso que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.
* [Log Analytics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-stream-diagnostic-logs-log-analytics): una herramienta flexible de búsqueda y de análisis de registros que permite el análisis de los registros sin procesar que genera un recurso de Azure.

> [!NOTE]
> Hay más opciones de configuración disponibles. Para más información, consulte [Recopilación y consumo de los datos de registro provenientes de los recursos de Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).

## <a name="enable-diagnostic-log-collection"></a>Habilitación de la colección de registros de diagnóstico  

Comencemos por habilitar el registro de diagnóstico mediante Azure Portal.

> [!NOTE]
> Para habilitar este rol mediante PowerShell o la CLI de Azure, use las instrucciones proporcionadas en [Recopilación y consumo de datos de registro de los recursos de Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview#diagnostic-settings).

1. Acceda a Azure Portal. Busque y seleccione un recurso de Cognitive Services. Por ejemplo, la suscripción a Bing Web Search.   
2. A continuación, en el menú de navegación izquierdo, busque **Monitoring** (Supervisión) y seleccione **Diagnostic settings** (Configuración de diagnóstico). Esta pantalla contiene todas las configuraciones de diagnóstico creadas anteriormente para este recurso.
3. Si hay un recurso creado previamente que le gustaría utilizar, puede seleccionarlo. En caso contrario, seleccione **+ Add diagnostic setting** (+ Agregar configuración de diagnóstico).
4. Escriba un nombre para la configuración. Seleccione **Archive to a storage account** (Archivar en una cuenta de almacenamiento) y luego seleccione **Send to log Analytics** (Enviar a Log Analytics).
5. Cuando se le solicite configurarla, seleccione la cuenta de almacenamiento y el área de trabajo de OMS que le gustaría usar para almacenar los registros de diagnóstico. **Nota**: Si no tiene una cuenta de almacenamiento o un área de trabajo de OMS, siga las indicaciones para crear una.
6. Seleccione **Audit**, **RequestResponse** y **AllMetrics**. Después, establezca el período de retención para los datos de registro de diagnóstico. Si se establece en cero una directiva de retención, los eventos para esa categoría de registro se almacenan indefinidamente.
7. Haga clic en **Save**(Guardar).

Pueden pasar hasta dos horas antes de que los datos de registro estén disponibles para su consulta y análisis. Así que no se preocupe si no ve nada de inmediato.

## <a name="view-and-export-diagnostic-data-from-azure-storage"></a>Visualización y exportación de datos de diagnóstico de Azure Storage

Azure Storage es una sólida solución de almacenamiento de objetos que está optimizada para almacenar grandes cantidades de datos no estructurados. En esta sección, aprenderá a consultar su cuenta de almacenamiento para conocer el total de transacciones en un período de 30 días y exportar los datos a Excel.

1. Desde Azure Portal, busque el recurso de Azure Storage que creó en la última sección.
2. En el menú de navegación izquierdo, busque **Monitoring** (Supervisión) y, a continuación, seleccione **Metrics** (Métricas).
3. Use las listas desplegables disponibles para configurar la consulta. En este ejemplo, vamos a establecer el intervalo de tiempo en **Last 30 days** (Últimos 30 días) y la métrica en **Transaction** (Transacción).
4. Cuando la consulta esté completa, verá una visualización de la transacción en los últimos 30 días. Para exportar estos datos, utilice el botón **Exportar a Excel** situado en la parte superior de la página.

Más información sobre lo que puede hacer con los datos de diagnóstico en [Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).

## <a name="view-logs-in-log-analytics"></a>Visualización de los registros de Log Analytics

Siga estas instrucciones para explorar los datos de análisis de registros para el recurso.

1. En Azure Portal, localice y seleccione **Log Analytics** en el menú de navegación izquierdo.
2. Busque y seleccione el recurso que ha creado cuando se habilita el diagnóstico.
3. En **General**, busque y seleccione **Logs** (Registros). Desde esta página, puede realizar las consultas en los registros.

### <a name="sample-queries"></a>Consultas de ejemplo

Estas son algunas consultas básicas de Kusto que puede utilizar para explorar los datos de registro.

Ejecute esta consulta para todos los registros de diagnóstico de Azure Cognitive Services durante el período de tiempo especificado:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
```

Ejecute esta consulta para ver los 10 registros más recientes:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| take 10
```

Ejecute esta consulta para agrupar las operaciones por **recurso**:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES" |
summarize count() by Resource
```
Ejecute esta consulta para buscar el promedio de tiempo que se tarda en realizar una operación:

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize avg(DurationMs)
by OperationName
```

Ejecute esta consulta para ver el volumen de operaciones a lo largo del tiempo dividido por el nombre de la operación con recuentos agrupados por decenas.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.COGNITIVESERVICES"
| summarize count()
by bin(TimeGenerated, 10s), OperationName
| render areachart kind=unstacked
```

## <a name="next-steps"></a>Pasos siguientes

* Para comprender cómo habilitar el registro y también las categorías de métricas y registro que admiten los distintos servicios de Azure, lea los artículos [Información general sobre las métricas en Microsoft Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics) e [Información general sobre los registros de diagnóstico de Azure](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-logs-overview).
* Lea estos artículos para obtener información sobre Event Hubs:
  * [¿Qué es Azure Event Hubs?](https://docs.microsoft.com/azure/event-hubs/event-hubs-what-is-event-hubs)
  * [Introducción a Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-csharp-ephcs-getstarted)
* Consulte [Descarga de métricas y registros de diagnóstico de Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#download-blobs).
* Consulte [Descripción de las búsquedas de registros en los registros de Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-log-search-new).
