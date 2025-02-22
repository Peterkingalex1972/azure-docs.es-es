---
title: Información de rendimiento de consultas en Azure Database for MariaDB
description: En este artículo se describe la característica Información de rendimiento de consultas en Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 709428d420c7d5f725d1b7df85e4860aec765b48
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950661"
---
# <a name="query-performance-insight-in-azure-database-for-mariadb"></a>Información de rendimiento de consultas en Azure Database for MariaDB

**Se aplica a:** Azure Database for MariaDB 10.2

> [!IMPORTANT]
> Información de rendimiento de consultas está en versión preliminar.

Query Performance Insight le ayuda a identificar rápidamente cuáles son las consultas que más tardan en ejecutarse, cómo cambian con el tiempo y qué esperas están afectándoles.

## <a name="common-scenarios"></a>Escenarios comunes

### <a name="long-running-queries"></a>Consultas de larga duración

- Identificar las consultas de ejecución más largas en las últimas X horas
- Identificar las N principales consultas que esperan recursos
 
### <a name="wait-statistics"></a>Estadísticas de espera

- Descripción de la naturaleza de la espera de una consulta
- Descripción de las tendencias de las esperas de recursos y dónde existe la contención de recursos

## <a name="permissions"></a>Permisos

Se necesitan los permisos **Propietario** o **Colaborador** para ver el texto de las consultas en Query Performance Insight. **Lector** pueden ver las tablas y los gráficos, pero no el texto de consulta.

## <a name="prerequisites"></a>Requisitos previos

Para que Query Performance Insight funcione, deben existir datos en el [Almacén de consultas](concepts-query-store.md).

## <a name="viewing-performance-insights"></a>Ver información de rendimiento

La vista [Query Performance Insight](concepts-query-performance-insight.md) en Azure Portal detectará visualizaciones en la información de clave del Almacén de consultas.

En la página del portal de su servidor de Azure Database for MariaDB, seleccione **Información de rendimiento de consultas** en la sección **Rendimiento inteligente** de la barra de menús.

### <a name="long-running-queries"></a>Consultas de larga duración

La pestaña **Consultas de larga duración** muestra las cinco principales consultas por promedio de duración por ejecución, que se agregan en intervalos de 15 minutos. Para ver más consultas, selecciónelas en la lista desplegable **Número de consultas**. Al hacerlo, los colores del gráfico pueden cambiar a un identificador de consulta específico.

Puede hacer clic y arrastrar en el gráfico para restringir a un período de tiempo específico. Como alternativa, puede usar los iconos de acercar y alejar para ver un período de tiempo mayor o menor, respectivamente.

![Consultas de larga ejecución de Query Performance Insight](./media/concepts-query-performance-insight/query-performance-insight-landing-page.png)

### <a name="wait-statistics"></a>Estadísticas de espera 

> [!NOTE]
> Las estadísticas de espera están diseñadas para solucionar problemas con el rendimiento de una consulta. Se recomienda activarlas solo para solucionar problemas.

Las estadísticas de espera brindan una vista de los eventos de espera que se producen durante la ejecución de una consulta específica. Puede encontrar más información sobre los tipos de eventos de espera en la [documentación del motor de MySQL](https://go.microsoft.com/fwlink/?linkid=2098206).

Seleccione la pestaña **Estadísticas de espera** para ver las visualizaciones correspondientes a esperas en el servidor.

Las consultas que se muestran en la vista de estadísticas de espera se agrupan según las consultas que presentan las mayores esperas durante el intervalo de tiempo especificado.

![Estadísticas de espera de Información de rendimiento de consultas](./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [supervisión y el ajuste](concepts-monitoring.md) en Azure Database for MariaDB.