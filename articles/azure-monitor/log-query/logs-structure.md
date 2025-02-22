---
title: Estructura de registros de Azure Monitor | Microsoft Docs
description: Requiere una consulta de registros para recuperar datos de registro desde Azure Monitor.  Este artículo describe cómo se usan las nuevas consultas de registros en Azure Monitor y proporciona los conceptos que debe comprender antes de crear una.
services: log-analytics
author: bwren
ms.service: log-analytics
ms.topic: conceptual
ms.date: 06/16/2019
ms.author: bwren
ms.openlocfilehash: 6f5ae426018c9e7fa2ac586a2886c8e5e609069b
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68813853"
---
# <a name="structure-of-azure-monitor-logs"></a>Estructura de registros de Azure Monitor
La capacidad de obtener rápidamente información sobre sus datos mediante una [consulta de registro](log-query-overview.md) es una versátil característica de Azure Monitor. Para crear consultas eficaces y útiles, debe comprender algunos conceptos básicos, como dónde se encuentran los datos que desea y cómo se estructuran. En este artículo se proporcionan los conceptos básicos que necesita para empezar a trabajar.

## <a name="overview"></a>Información general
Los datos de los registros de Azure Monitor se almacenan en un área de trabajo de Log Analytics o en una aplicación de Application Insights. Ambos usan [Azure Data Explorer](/azure/data-explorer/), lo que significa que aprovechan su lenguaje de consulta y motor de datos eficaces.

Los datos de las áreas de trabajo y las aplicaciones se organizan en tablas, cada una de las cuales almacena diferentes tipos de datos y tiene su propio conjunto único de propiedades. La mayoría de los [orígenes de datos](../platform/data-sources.md) escribirán en sus propias tablas en un área de trabajo de Log Analytics, mientras que Application Insights escribirá en un conjunto predefinido de tablas en una aplicación de Application Insights. Las consultas son muy flexibles, lo que le permite combinar fácilmente datos de varias tablas e incluso usar una consulta entre recursos para combinar datos de tablas en varias áreas de trabajo o para escribir las consultas que combinan datos de áreas de trabajo y aplicaciones.

En la siguiente imagen se muestran ejemplos de orígenes de datos que escriben en tablas diferentes que se usan en consultas de ejemplo.

![Tablas](media/logs-structure/queries-tables.png)

## <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics
Todos los datos que los registros de Azure Monitor recopilan, excepto Application Insights, se almacenan en un [área de trabajo de Log Analytics](../platform/manage-access.md). Puede crear una o varias áreas de trabajo según sus requisitos particulares. Los [orígenes de datos](../platform/data-sources.md), como los registros de actividad y los registros de diagnóstico de recursos de Azure, los agentes de máquinas virtuales y los datos de información y soluciones de supervisión escribirán datos en una o varias áreas de trabajo que se configuran como parte de su incorporación. Otros servicios, como [Azure Security Center](/azure/security-center/) y [Azure Sentinel](/azure/sentinel/), también usan un área de trabajo de Log Analytics para almacenar sus datos, por lo que se pueden analizar mediante consultas de registro junto con datos de supervisión de otros orígenes.

Los distintos tipos de datos se almacenan en tablas diferentes en el área de trabajo y cada tabla tiene un único conjunto de propiedades. Un conjunto estándar de tablas se agrega a un área de trabajo cuando se crea y se agregan nuevas tablas para diferentes orígenes de datos, soluciones y servicios a medida que se incorporan. También puede crear tablas personalizadas mediante [Data Collector API](../platform/data-collector-api.md).

Puede examinar las tablas de un área de trabajo y su esquema en la pestaña **Esquema** en Log Analytics para el área de trabajo.

![Esquema del área de trabajo](media/scope/workspace-schema.png)

Utilice la consulta siguiente para enumerar las tablas del área de trabajo y el número de registros que se recopilaron en cada una de ellas a lo largo del día anterior. 

```Kusto
union withsource = table * 
| where TimeGenerated > ago(1d)
| summarize count() by table
| sort by table asc
```
Consulte la documentación de cada origen de datos para conocer los detalles de las tablas que crean. Algunos ejemplos son artículos para los [orígenes de datos del agente](../platform/agent-data-sources.md), [los registros de diagnóstico](../platform/diagnostic-logs-schema.md) y las [soluciones de supervisión](../insights/solutions-inventory.md).

### <a name="workspace-permissions"></a>Permisos de área de trabajo
Consulte [Ámbito y permisos del área de trabajo](../platform/manage-access.md#manage-accounts-and-users) para detalles sobre cómo proporcionar acceso a los datos en un área de trabajo. Además de conceder acceso a la propia área de trabajo, puede limitar el acceso a tablas individuales mediante [RBAC de nivel de tabla](../platform/manage-access.md#table-level-rbac).

## <a name="application-insights-application"></a>Aplicación de Application Insights
Al crear una aplicación en Application Insights, se crea automáticamente una aplicación correspondiente en los registros de Azure Monitor. No se necesita ninguna configuración para recopilar datos y la aplicación escribirá automáticamente datos tales como las vistas de páginas, solicitudes y excepciones de supervisión.

A diferencia de un área de trabajo de Log Analytics, una aplicación de Application Insights tiene un conjunto fijo de tablas. No puede configurar otros orígenes de datos para escribir en la aplicación, por lo que no se puede crear ninguna tabla adicional. 

| Tabla | DESCRIPCIÓN | 
|:---|:---|
| availabilityResults | Datos de resumen de las pruebas de disponibilidad. |
| browserTimings      | Datos sobre el rendimiento del cliente, como el tiempo que se tardan en procesar los datos entrantes. |
| customEvents        | Eventos personalizados creados por la aplicación. |
| customMetrics       | Métricas personalizadas creadas por la aplicación. |
| dependencies        | Llamadas desde la aplicación a componentes externos. |
| exceptions          | Excepciones generadas por el entorno de ejecución de la aplicación. |
| pageViews           | Datos sobre cada vista del sitio web con información del explorador. |
| performanceCounters | Medidas de rendimiento de los recursos de proceso que respaldan la aplicación. |
| Solicitudes            | Detalles de cada solicitud de aplicación.  |
| traces              | Resultados del seguimiento distribuido. |

Puede ver el esquema para cada tabla de la pestaña **Esquema** en Log Analytics para la aplicación.

![Esquema de la aplicación](media/scope/application-schema.png)

## <a name="standard-properties"></a>Propiedades estándar
Aunque cada tabla de los registros de Azure Monitor tiene su propio esquema, hay propiedades estándar compartidas por todas las tablas. Consulte [Propiedades estándar de los registros de Azure Monitor](../platform/log-standard-properties.md) para detalles.

| Área de trabajo de Log Analytics | Aplicación de Application Insights | DESCRIPCIÓN |
|:---|:---|:---|
| TimeGenerated | timestamp  | Fecha y hora en que se creó el registro. |
| type          | itemType   | Nombre de la tabla de la que se recuperó el registro. |
| _ResourceId   |            | Identificador único para el recurso al que está asociado el registro. |
| _IsBillable   |            | Especifica si los datos ingeridos son facturables. |
| _BilledSize   |            | Especifica el tamaño en bytes de los datos que se facturarán. |

## <a name="next-steps"></a>Pasos siguientes
- Aprenda a usar [Log Analytics para crear y editar búsquedas de registros](../log-query/portals.md).
- Consulte un [tutorial sobre cómo escribir consultas](../log-query/get-started-queries.md) mediante el nuevo lenguaje de consulta.
