---
title: Consulta de datos en Azure Monitor con Azure Data Explorer (versión preliminar)
description: En este tema, se van a consultar los datos en Azure Monitor mediante la creación de un proxy de Azure Data Explorer para consultas entre productos con Application Insights y Log Analytics.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: f363e59e6faa6b115eb40a2a5d35432f02299d52
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810035"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Consulta de datos en Azure Monitor con Azure Data Explorer (versión preliminar)

El clúster de proxy de Azure Data Explorer (ADX Proxy) es una entidad que le permite realizar consultas entre productos entre Azure Data Explorer, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview) y [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs) en el servicio [Azure Monitor](/azure/azure-monitor/). Puede asignar áreas de trabajo de Log Analytics o aplicaciones de Application Insights en Azure Monitor como un clúster de proxy. A continuación, puede consultar el clúster de proxy mediante las herramientas de Azure Data Explorer y referirse a él en una consulta entre clústeres. El artículo muestra cómo conectarse a un clúster de proxy, agregar un clúster de proxy a la interfaz de usuario web de Azure Data Explorer y ejecutar consultas en las aplicaciones de Application Insights o en áreas de trabajo de Log Analytics desde Azure Data Explorer.

El flujo del proxy de Azure Data Explorer: 

![Flujo de proxy de ADX](media/adx-proxy/adx-proxy-flow.png)

## <a name="prerequisites"></a>Requisitos previos

> [!NOTE]
> El proxy de ADX está en modo de versión preliminar. Para habilitar esta característica, póngase en contacto con el equipo de [ADXProxy](mailto:adxproxy@microsoft.com).

## <a name="connect-to-the-proxy"></a>Conexión con el proxy

1. Compruebe que el clúster nativo de Azure Data Explorer (como el clúster *help*) aparece en el menú izquierdo antes de conectarse al clúster de Log Analytics o de Application Insights.

    ![Clúster nativo de ADX](media/adx-proxy/web-ui-help-cluster.png)

1. En la interfaz de usuario de Azure Data Explorer (https://dataexplorer.azure.com/clusters), seleccione **Agregar clúster**.

1. En la ventana **Agregar clúster**:

    * Agregue la dirección URL para el clúster de Log Analytics o Application Insights. Por ejemplo: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`

    * Seleccione **Agregar**.

    ![Agregar clúster](media/adx-proxy/add-cluster.png)

    Si agrega una conexión a más de un clúster de proxy, dé a cada uno un nombre diferente. En caso contrario, todos tendrán el mismo nombre en el panel izquierdo.

1. Una vez establecida la conexión, el clúster de Log Analytics o Application Insights aparecerá en el panel izquierdo con el clúster de ADX nativo. 

    ![Clústeres de Log Analytics y de Azure Data Explorer](media/adx-proxy/la-adx-clusters.png)

## <a name="run-queries"></a>Ejecutar consultas

Puede usar Kusto Explorer, explorador web de ADX, Jupyter Kqlmagic o la API REST para consultar los clústeres de proxy. 

> [!TIP]
> * El nombre de la base de datos debe tener el mismo nombre que el recurso especificado en el clúster de proxy. Los nombres distinguen mayúsculas de minúsculas.
> * En las consultas entre clústeres, asegúrese de que la [nomenclatura de las aplicaciones y áreas de trabajo](#application-insights-app-and-log-analytics-workspace-names) sea correcta.

### <a name="query-against-the-native-azure-data-explorer-cluster"></a>Consultas en el clúster de Azure Data Explorer nativo 

Ejecute las consultas en el clúster de Azure Data Explorer (como la tabla *StormEvents* en el clúster *help*). Cuando ejecute la consulta, compruebe que el clúster nativo de Azure Data Explorer esté seleccionado en el panel izquierdo.

```kusto
StormEvents | take 10 // Demonstrate query through the native ADX cluster
```

![Consulta de la tabla StormEvents](media/adx-proxy/query-adx.png)

### <a name="query-against-your-la-or-ai-cluster"></a>Consultas en el clúster de Log Analytics o Application Insights

Al ejecutar consultas en el clúster de Log Analytics o Application Insights, compruebe que dicho clúster está seleccionado en el panel izquierdo. 

```kusto
Perf | take 10 // Demonstrate query through the proxy on the LA workspace
```

![Consulta del área de trabajo de Log Analytics](media/adx-proxy/query-la.png)

### <a name="query-your-la-or-ai-cluster-from-the-adx-proxy"></a>Consulta del clúster de desde el proxy de ADX  

Al ejecutar consultas en el clúster de Log Analytics o Application Insights desde el proxy, compruebe que el clúster nativo de ADX está seleccionado en el panel izquierdo. En el ejemplo siguiente se muestra una consulta del área de trabajo de Log Analytics que utiliza el clúster de ADX nativo

```kusto
cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name').Perf
| take 10 
```

![Consulta desde el proxy de Azure Data Explorer](media/adx-proxy/query-adx-proxy.png)

### <a name="cross-query-of-la-or-ai-cluster-and-the-adx-cluster-from-the-adx-proxy"></a>Consulta cruzada del clúster de Log Analytics o Application Insights desde el proxy de ADX 

Al ejecutar consultas entre clústeres desde el proxy, compruebe que el clúster nativo de ADX está seleccionado en el panel izquierdo. En los ejemplos siguientes se muestra la combinación de tablas de clúster de ADX (mediante `union`) con el área de trabajo de Log Analytics.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10 
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <ADX table>, cluster(CL1).database(<workspace-name>).<table name>
```

![Consulta cruzada desde el proxy de Azure Data Explorer](media/adx-proxy/cross-query-adx-proxy.png)

Usar el operador [`join`](/azure/kusto/query/joinoperator), en lugar de unión, puede requerir una sugerencia para ejecutarlo en un clúster nativo de Azure Data Explorer (y no en el proxy). 

## <a name="additional-syntax-examples"></a>Ejemplos de sintaxis adicionales

Las opciones de sintaxis siguientes están disponibles cuando se llama a los clústeres de Application Insights o Log Analytics:

|Descripción de la sintaxis  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| Base de datos dentro de un clúster que contiene solo el recurso definido en esta suscripción (**recomendado para las consultas entre clústeres**) |   clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>`) | clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>`)     |
| Clúster que contiene todas las aplicaciones o áreas de trabajo en esta suscripción    |     clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>`)    |    clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>`)     |
|Clúster que contiene todas las aplicaciones o áreas de trabajo de la suscripción y que son miembros de este grupo de recursos    |   clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |    clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>`)      |
|Clúster que contiene solo el recurso definido en esta suscripción      |    clúster (`https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`)    |  clúster (`https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`)     |

### <a name="application-insights-app-and-log-analytics-workspace-names"></a>Aplicación de Application Insights y área de trabajo de Log Analytics

* Si los nombres contienen caracteres especiales, se sustituyen por codificación URL en el nombre del clúster del proxy. 
* Si los nombres incluyen caracteres que no cumplen las [reglas del nombre del identificador KQL](/azure/kusto/query/schema-entities/entity-names), se sustituyen por el carácter de guión **-** .

## <a name="next-steps"></a>Pasos siguientes

[Escritura de consultas](write-queries.md)