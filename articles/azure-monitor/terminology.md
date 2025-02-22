---
title: Actualizaciones de terminología de Azure Monitor | Microsoft Docs
description: Se describen los cambios recientes de terminología realizados en los servicios de supervisión de Azure.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: f8a86c0b71a5248164ea522472f9f2ecb2ce36bc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827358"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Cambios de nomenclatura y terminología de Azure Monitor
Se han realizado cambios significativos en Azure Monitor recientemente, con diferentes servicios que se han consolidado para simplificar la supervisión para los clientes de Azure. En este artículo se describen cambios recientes de nombres y de terminología en la documentación de Azure Monitor.

## <a name="february-2019---log-analytics-terminology"></a>Febrero de 2019: terminología de Log Analytics
Después de la consolidación de diferentes servicios en Azure Monitor, el siguiente paso consiste en modificar la terminología de la documentación para describir mejor el servicio Azure Monitor y sus distintos componentes. 

### <a name="log-analytics"></a>Log Analytics
Los datos de registro se siguen almacenando en un área de trabajo de Log Analytics y aún se siguen recopilando y analizando con el mismo servicio de Log Analytics, pero estamos cambiando el término _Log Analytics_ en muchos lugares por el de _registros de Azure Monitor_. Este término refleja mejor su rol en Azure Monitor y proporciona mayor coherencia con las [métricas de Azure Monitor](platform/data-platform-metrics.md).

El término _Log Analytics_ ahora se aplica principalmente a la página de Azure Portal que se usa para escribir y ejecutar consultas, y analizar datos de registros. Es el equivalente funcional del [explorador de métricas](platform/metrics-charts.md), que está en la página de Azure Portal que se usa para analizar los datos de las métricas.

### <a name="log-analytics-workspaces"></a>Áreas de trabajo de Log Analytics
A las [áreas de trabajo](platform/manage-access.md) que contienen datos de registro de Azure Monitor todavía se les hace referencia como áreas de trabajo de Log Analytics. Se ha cambiado el nombre del menú **Log Analytics** de Azure Portal por el de **Áreas de trabajo de Log Analytics** y aquí es dónde [podrá crear nuevas áreas de trabajo](learn/quick-create-workspace.md) y configurar orígenes de datos. Analice los registros y otros datos de supervisión en **Azure Monitor** y configure el área de trabajo en **Áreas de trabajo de Log Analytics**.

### <a name="management-solutions"></a>Soluciones de administración
Se ha cambiado el nombre de [soluciones de administración](insights/solutions.md) por el de _soluciones de supervisión_, que describe mejor la funcionalidad.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>Agosto de 2018: consolidación de servicios de supervisión en Azure Monitor
Log Analytics y Application Insights se han consolidado en Azure Monitor para proporcionar una única experiencia integrada para la supervisión de los entornos híbridos y recursos de Azure. No se ha quitado ninguna funcionalidad de estos servicios y los usuarios pueden actuar en los mismos escenarios de siempre sin que ninguna característica pueda perderse ni corra peligro.

La documentación de todos estos servicios se ha consolidado en un único conjunto de contenido en Azure Monitor. Esto ayudará al lector a buscar todo el contenido de un escenario de supervisión concreto en una sola ubicación, en lugar de tener que hacer referencia a varios conjuntos de contenido. A medida que el servicio consolidado evolucione, se integrará más el contenido.

Otras características que formaban parte de Log Analytics, como agentes y vistas, han pasado también a ser características de Azure Monitor. Su funcionalidad no ha cambiado, aparte de las mejoras potenciales en su experiencia en Azure Portal.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>Abril de 2018: retirada de la marca Operations Management Suite
Operations Management Suite (OMS) era una unión de los siguientes servicios de administración de Azure para facilitar su licencia:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Se ha introducido los nuevos precios de estos servicios](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) y la unión OMS ya no está disponible para los nuevos clientes. Ninguno de los servicios que formaban parte de OMS han cambiado, salvo la consolidación de Azure Monitor que se ha descrito anteriormente. 




## <a name="next-steps"></a>Pasos siguientes

- Leer una [introducción a Azure Monitor](overview.md) que describe sus características y componentes diferentes.
- Obtenga información acerca de la [transición del portal de OMS](../log-analytics/log-analytics-oms-portal-transition.md).
