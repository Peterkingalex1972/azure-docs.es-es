---
title: Métricas y registros de diagnóstico de Azure SQL Database | Microsoft Docs
description: Aprenda a habilitar diagnósticos en Azure SQL Database para almacenar información sobre el uso de recursos y las estadísticas de ejecución de consultas.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: seoapril2019
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 05/21/2019
ms.openlocfilehash: a1475188d2e1ab0db3dfd9775fc37d3fc0a17158
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567263"
---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas y registros de diagnóstico de Azure SQL Database

En este tema, obtendrá información sobre cómo configurar el registro de telemetría de diagnósticos para Azure SQL Database mediante Azure Portal, PowerShell, CLI de Azure, API REST de Azure Monitor y la plantilla de Azure Resource Manager. Estos diagnósticos se pueden usar para medir el uso de recursos y las estadísticas de ejecución de consultas.

Las bases de datos únicas, bases de datos agrupadas en grupos elásticos y bases de datos de instancia en una instancia administrada pueden transmitir métricas y registros de diagnóstico para facilitar la supervisión del rendimiento. Puede configurar una base de datos para optimizar el uso de recursos, los trabajos y sesiones, y la conectividad en uno de los siguientes recursos de Azure:

- **Azure SQL Analytics**: para obtener supervisión inteligente de sus bases de datos de Azure SQL que incluya informes de rendimiento, alertas y recomendaciones de mitigación.
- **Azure Event Hubs**: para integrar la telemetría de SQL Database con una solución de supervisión personalizada o canalizaciones activas.
- **Azure Storage**: para archivar grandes cantidades de telemetría por una fracción del precio.

    ![Arquitectura](./media/sql-database-metrics-diag-logging/architecture.png)

Para más información sobre las métricas y las categorías de registro admitidas por los diversos servicios de Azure, consulte:

- [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Información general de los registros de diagnóstico de Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

En este artículo se proporciona orientación para ayudarle a habilitar los datos de telemetría de diagnósticos para bases de datos, grupos elásticos e Instancias administradas de Azure SQL. También puede ayudarle a saber cómo configurar Azure SQL Analytics como una herramienta de supervisión que permita visualizar los datos de telemetría de diagnóstico de las bases de datos.

## <a name="enable-logging-of-diagnostics-telemetry"></a>Habilitación del registro de datos de telemetría de diagnóstico

Puede habilitar y administrar las métricas y los registros de datos de telemetría de diagnóstico utilizando uno de los métodos siguientes:

- Portal de Azure
- PowerShell
- Azure CLI
- API de REST de Azure Monitor
- Plantilla del Administrador de recursos de Azure

Al habilitar las métricas y los registros de diagnóstico, debe especificar el destino de recursos de Azure para recopilar los datos de telemetría de diagnóstico. Las opciones disponibles incluyen:

- Azure SQL Analytics
- Azure Event Hubs
- Azure Storage

Puede aprovisionar un nuevo recurso de Azure o seleccionar uno existente. Después de elegir un recurso, deberá especificar qué datos se deben recopilar mediante la opción de **configuración de diagnóstico**.

## <a name="supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases"></a>Registro de diagnóstico admitido para bases de datos de Azure SQL y bases de datos de instancia

Habilite las métricas y el registro de diagnóstico en bases de datos SQL; no están habilitadas de forma predeterminada.

Puede configurar bases de datos de Azure SQL y bases de datos de instancia para recopilar los siguientes datos de telemetría de diagnóstico:

| Telemetría de supervisión de bases de datos | Compatibilidad con bases de datos únicas y bases de datos agrupadas | Compatibilidad de base de datos de instancia |
| :------------------- | ----- | ----- |
| [Métricas básicas](#basic-metrics): contiene el porcentaje de DTU/CPU, el límite de DTU/CPU, el porcentaje de lectura de datos físicos, el porcentaje de escritura en registro, las conexiones correctas, erróneas o bloqueadas por el firewall, el porcentaje de sesiones, el porcentaje de trabajo, el almacenamiento, el porcentaje de almacenamiento y el porcentaje de almacenamiento de XTP. | Sí | Sin |
| [QueryStoreRuntimeStatistics](#query-store-runtime-statistics): contiene la información sobre las estadísticas de tiempo de ejecución de consulta, como el uso de CPU y la duración de la consulta. | Sí | Sí |
| [QueryStoreWaitStatistics](#query-store-wait-statistics): contiene la información sobre las estadísticas de espera de las consultas (el motivo de la espera de sus consultas), como CPU, LOG y LOCKING. | Sí | Sí |
| [Errores](#errors-dataset): contiene información sobre los errores de SQL en una base de datos. | Sí | Sí |
| [DatabaseWaitStatistics](#database-wait-statistics-dataset): contiene la información sobre cuánto tiempo ha dedicado una base de datos a esperar distintos tipos de espera. | Sí | Sin |
| [Tiempos de expiración](#time-outs-dataset): contiene información sobre los tiempos de expiración en una base de datos. | Sí | Sin |
| [Bloqueos](#blockings-dataset): contiene información sobre los eventos de bloqueo en una base de datos. | Sí | Sin |
| [Interbloqueos](#deadlocks-dataset): contiene información sobre los eventos de interbloqueo en una base de datos. | Sí | Sin |
| [AutomaticTuning](#automatic-tuning-dataset): contiene información sobre las recomendaciones de ajuste automático para una base de datos. | Sí | Sin |
| [SQLInsights](#intelligent-insights-dataset): contiene Intelligent Insights sobre el rendimiento de una base de datos. Para obtener más información, consulte [Intelligent Insights](sql-database-intelligent-insights.md). | Sí | Sí |

> [!IMPORTANT]
> Los grupos elásticos y las instancias administradas tienen sus propios datos de telemetría de las bases de datos que contienen. Es importante tener esto en cuenta, porque la telemetría de diagnóstico se configura por separado para cada uno de estos recursos, tal y como se describe más adelante.

> [!NOTE]
> No se pueden habilitar los registros de SQLSecurityAuditEvents y de auditoría de seguridad desde la configuración de diagnóstico de la base de datos (aunque se muestran en la pantalla). Para habilitar el streaming de registros de auditoría, vea [Configuración de la auditoría para su base de datos](sql-database-auditing.md#subheading-2) y [SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242) (Registros de auditoría de SQL en Azure Log Analytics y Azure Event Hubs).

## <a name="azure-portal"></a>Portal de Azure

Use el menú **Configuración de diagnóstico** para configurar el streaming de los datos de telemetría de diagnóstico para cada base de datos única, agrupada o de instancia en Azure Portal. Además, los datos de telemetría de diagnóstico también pueden configurarse por separado para los contenedores de base de datos: los grupos elásticos y las instancias administradas. Puede establecer los siguientes destinos para transmitir los datos de telemetría de diagnósticos: Azure Storage, Azure Event Hubs y registros de Azure Monitor.

### <a name="configure-streaming-of-diagnostics-telemetry-for-elastic-pools"></a>Configuración del streaming de telemetría de diagnóstico para grupos elásticos

   ![Icono de grupo elástico](./media/sql-database-metrics-diag-logging/icon-elastic-pool-text.png)

Puede configurar un recurso de grupos elásticos para recopilar los siguientes datos de telemetría de diagnóstico:

| Recurso | Telemetría de supervisión |
| :------------------- | ------------------- |
| **Grupo elástico** | [Métricas básicas](sql-database-metrics-diag-logging.md#basic-metrics) contiene el porcentaje de eDTU/CPU, el límite de eDTU/CPU, el porcentaje de lectura de datos físicos, el porcentaje de escritura en registro, el porcentaje de sesiones, el porcentaje de trabajos, el almacenamiento, el porcentaje de almacenamiento, el límite de almacenamiento y el porcentaje de almacenamiento de XTP. |

Para configurar el streaming de datos de telemetría de diagnóstico para grupos elásticos y bases de datos en grupos elásticos, deberá configurar por separado estas **dos** opciones:

- Habilitar el streaming de datos de telemetría de diagnóstico para un grupo elástico, **y**
- Habilitar el streaming de datos de telemetría de diagnóstico para cada base de datos en un grupo elástico.

El motivo es que el grupo elástico es un contenedor de base de datos en el que sus propios datos de telemetría están separados de los datos de telemetría de una base de datos individual.

Para habilitar el streaming de telemetría de diagnóstico del recurso de grupos elásticos, siga estos pasos:

1. Vaya al recurso del **grupo elástico** de Azure Portal.
1. Seleccione **Configuración de diagnóstico**.
1. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.

   ![Habilitación de diagnósticos en grupos elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-enable.png)

1. Escriba un nombre de configuración para su propia referencia.
1. Seleccione un recurso de destino para los datos de diagnóstico de streaming: **Archivar en una cuenta de almacenamiento**, **Transmitir en secuencias a un centro de eventos** o **Enviar a Log Analytics**.
1. En los análisis de registros, seleccione **Configurar** y cree una nueva área de trabajo en **+Crear nueva área de trabajo**, o bien seleccione un área de trabajo existente.
1. Active la casilla para la telemetría de diagnóstico de grupos elásticos: Métricas **básicas**.
   ![Configuración de diagnósticos en grupos elásticos](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-elasticpool-selection.png)
1. Seleccione **Guardar**.
1. Además, configure el streaming de datos de telemetría de diagnóstico para cada base de datos del grupo elástico que quiera supervisar siguiendo los pasos descritos en la sección siguiente.

> [!IMPORTANT]
> Además de configurar los datos de telemetría de diagnóstico para un grupo elástico, también deberá configurar los datos de telemetría de diagnóstico para cada base de datos de un grupo elástico, tal como se describe a continuación.

### <a name="configure-streaming-of-diagnostics-telemetry-for-single-database-or-database-in-elastic-pool"></a>Configuración del streaming de datos de telemetría de diagnóstico para una base de datos única o una base de datos en un grupo elástico

   ![Icono de SQL Database](./media/sql-database-metrics-diag-logging/icon-sql-database-text.png)

Para habilitar el streaming de datos de telemetría de diagnóstico de bases de datos únicas o agrupadas, siga estos pasos:

1. Vaya al recurso **Base de datos de Azure SQL**.
1. Seleccione **Configuración de diagnóstico**.
1. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.
   - Puede crear hasta tres conexiones paralelas para hacer streaming de la telemetría de diagnóstico.
   - Seleccione **+Agregar configuración de diagnóstico** para configurar el streaming en paralelo de diagnóstico a varios recursos.

   ![Habilitación de diagnóstico para bases de datos únicas, agrupadas y de instancia](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-enable.png)
1. Escriba un nombre de configuración para su propia referencia.
1. Seleccione un recurso de destino para los datos de diagnóstico de streaming: **Archivar en una cuenta de almacenamiento**, **Transmitir en secuencias a un centro de eventos** o **Enviar a Log Analytics**.
1. Para la experiencia de supervisión basada en eventos estándar, active las siguientes casillas para la telemetría de registro de diagnóstico de base de datos: **SQLInsights**, **AutomaticTuning**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics**, **Errores**, **DatabaseWaitStatistics**, **Tiempos de expiración**, **Bloqueos** e **Interbloqueos**.
1. Para una experiencia avanzada de supervisión basada en un minuto, active la casilla de métricas **Básicas**.
   ![Configuración de diagnóstico para bases de datos únicas, agrupadas o de instancia](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-sql-selection.png)
1. Seleccione **Guardar**.
1. Repita estos pasos para cada base de datos que quiera supervisar.

> [!NOTE]
> No se pueden habilitar los registros de SQLSecurityAuditEvents y de auditoría de seguridad desde la configuración de diagnóstico de la base de datos (aunque se muestran en la pantalla). Para habilitar el streaming de registros de auditoría, vea [Configuración de la auditoría para su base de datos](sql-database-auditing.md#subheading-2) y [SQL Audit logs in Azure Log Analytics and Azure Event Hubs](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/SQL-Audit-logs-in-Azure-Log-Analytics-and-Azure-Event-Hubs/ba-p/386242) (Registros de auditoría de SQL en Azure Log Analytics y Azure Event Hubs).
> [!TIP]
> Repita estos pasos para cada base de datos de Azure SQL que quiera supervisar.

### <a name="configure-streaming-of-diagnostics-telemetry-for-managed-instances"></a>Configuración del streaming de datos de telemetría de diagnóstico para instancias administradas

   ![Icono de Instancia administrada](./media/sql-database-metrics-diag-logging/icon-managed-instance-text.png)

Puede configurar un recurso de instancia administrada para recopilar los siguientes datos de telemetría de diagnóstico:

| Recurso | Telemetría de supervisión |
| :------------------- | ------------------- |
| **Instancia administrada** | [ResourceUsageStats](#resource-usage-stats-for-managed-instance): contiene el número de núcleos virtuales, el porcentaje medio de CPU, las solicitudes de E/S, los bytes leídos y escritos, el espacio de almacenamiento reservado y el espacio de almacenamiento utilizado. |

Para configurar el streaming de datos de telemetría de diagnóstico para bases de datos de instancia e instancia administrada, deberá configurar por separado estas **dos** opciones:

- Habilitar el streaming de datos de telemetría de diagnóstico para una instancia administrada, **y**
- Habilitar el streaming de datos de telemetría de diagnóstico para cada base de datos de instancia.

El motivo es que la instancia administrada es un contenedor de base de datos en el que sus propios datos de telemetría están separados de los datos de telemetría de una base de datos de instancia individual.

Para habilitar el streaming de datos de telemetría de diagnóstico de un recurso de instancia administrada, siga estos pasos:

1. Vaya al recurso de **instancia administrada** en Azure Portal.
1. Seleccione **Configuración de diagnóstico**.
1. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.

   ![Habilitación de diagnósticos para una instancia administrada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-enable.png)

1. Escriba un nombre de configuración para su propia referencia.
1. Seleccione un recurso de destino para los datos de diagnóstico de streaming: **Archivar en una cuenta de almacenamiento**, **Transmitir en secuencias a un centro de eventos** o **Enviar a Log Analytics**.
1. En los análisis de registros, seleccione **Configurar** y cree una nueva área de trabajo en **+Crear nueva área de trabajo**, o bien use un área de trabajo existente.
1. Active la casilla para la telemetría de diagnóstico de instancias: **ResourceUsageStats**.
   ![Configuración de diagnósticos para una instancia administrada](./media/sql-database-metrics-diag-logging/diagnostics-settings-container-mi-selection.png)
1. Seleccione **Guardar**.
1. Además, configure el streaming de datos de telemetría de diagnóstico para cada base de datos de instancia dentro de la instancia administrada que quiere supervisar siguiendo los pasos descritos en la sección siguiente.

> [!IMPORTANT]
> Además de configurar los datos de telemetría de diagnóstico para una instancia administrada, también deberá configurar los datos de telemetría de diagnóstico para cada base de datos de la instancia, tal como se describe a continuación.

### <a name="configure-streaming-of-diagnostics-telemetry-for-instance-databases"></a>Configuración del streaming de datos de telemetría de diagnóstico para bases de datos de instancia

   ![Icono de base de datos de instancia en Instancia administrada](./media/sql-database-metrics-diag-logging/icon-mi-database-text.png)

Para habilitar el streaming de datos de telemetría de diagnóstico para bases de datos de instancia, siga estos pasos:

1. Vaya al recurso de **base de datos de instancia** dentro de la instancia administrada.
1. Seleccione **Configuración de diagnóstico**.
1. Seleccione **Activar diagnóstico** si no existe ninguna configuración anterior o **Editar configuración** para editar una configuración anterior.
   - Se pueden crear hasta tres (3) conexiones paralelas para hacer streaming de la telemetría de diagnóstico.
   - Seleccione **+Agregar configuración de diagnóstico** para configurar el streaming en paralelo de diagnóstico a varios recursos.

   ![Habilitación de diagnóstico para bases de datos de instancia](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-enable.png)

1. Escriba un nombre de configuración para su propia referencia.
1. Seleccione un recurso de destino para los datos de diagnóstico de streaming: **Archivar en una cuenta de almacenamiento**, **Transmitir en secuencias a un centro de eventos** o **Enviar a Log Analytics**.
1. Active las casillas para la telemetría de diagnóstico de base de datos: **SQLInsights**, **QueryStoreRuntimeStatistics**, **QueryStoreWaitStatistics** y **Errores**.
   ![Configuración de diagnóstico para bases de datos de instancia](./media/sql-database-metrics-diag-logging/diagnostics-settings-database-mi-selection.png)
1. Seleccione **Guardar**.
1. Repita estos pasos para cada base de datos de instancia que quiera supervisar.

> [!TIP]
> Repita estos pasos para cada base de datos de instancia que quiera supervisar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager de PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos en el módulo Az y en los módulos AzureRm son esencialmente idénticos.

Puede habilitar las métricas y los registros de diagnóstico con PowerShell.

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento de destino.

- Para habilitar la transmisión de registros de diagnóstico a un centro de eventos, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   El identificador de regla de Azure Service Bus es una cadena con este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```powershell
   Set-AzDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Puede obtener el identificador de recurso de su área de trabajo de Log Analytics con el comando siguiente:

   ```powershell
   (Get-AzOperationalInsightsWorkspace).ResourceId
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="to-configure-multiple-azure-resources"></a>Para configurar varios recursos de Azure

Para admitir varias suscripciones, use el script de PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/20../../enable-azure-resource-metrics-logging-using-powershell/) (Habilitar registro de métricas de recursos de Azure mediante PowerShell).

Proporcione el identificador de recurso del área de trabajo \<$WSID\> como un parámetro al ejecutar el script `Enable-AzureRMDiagnostics.ps1` para enviar los datos de diagnóstico de varios recursos a una área de trabajo.

- Para obtener el identificador de área de trabajo \<$WSID\> del destino para sus datos de diagnóstico, use el siguiente script:

    ```powershell
    PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/<RG_NAME>/providers/microsoft.operationalinsights/workspaces/<WS_NAME>"
    PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
    ```

   Reemplace \<subID\> por el identificador de suscripción, \<RG_NAME\> por el nombre del grupo de recursos y \<WS_NAME\> por el nombre del área de trabajo.

### <a name="azure-cli"></a>CLI de Azure

Puede habilitar las métricas y los registros de diagnóstico con la CLI de Azure.

> [!NOTE]
> Los scripts para habilitar el registro de diagnóstico son compatibles con CLI de Azure v1.0. Tenga en cuenta que CLI versión 2.0 no es compatible en este momento.

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento de destino.

- Para habilitar la transmisión de registros de diagnóstico a un centro de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   El identificador de regla de Service Bus es una cadena con este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="rest-api"></a>API DE REST

Lea sobre cómo [cambiar la configuración de diagnóstico con la API de REST de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

### <a name="resource-manager-template"></a>Plantilla de Resource Manager

Lea sobre cómo [habilitar la configuración de diagnóstico al crear recursos con la plantilla de Resource Manager](../azure-monitor/platform/diagnostic-logs-stream-template.md).

## <a name="stream-into-azure-sql-analytics"></a>Transmisión en Azure SQL Analytics

Azure SQL Analytics es una solución en la nube que supervisa el rendimiento de bases de datos, grupos elásticos e instancias administradas de Azure SQL a escala entre varias suscripciones. Puede ayudarle a recopilar y visualizar métricas del rendimiento de Azure SQL Database y cuenta con inteligencia integrada para solucionar problemas de rendimiento.

![Información general de Azure SQL Analytics](../azure-monitor/insights/media/azure-sql/azure-sql-sol-overview.png)

Las métricas y los registros de diagnóstico de SQL Database se pueden transmitir a Azure SQL Analytics mediante la opción **Enviar a Log Analytics** integrada en la pestaña de configuración de diagnósticos del portal. También puede habilitar los análisis de registros mediante una configuración de diagnóstico a través de los cmdlets de PowerShell, la CLI de Azure o la API REST de Azure Monitor.

### <a name="installation-overview"></a>Introducción a la instalación

Puede supervisar una flota de SQL Database con Azure SQL Analytics. Lleve a cabo los siguiente pasos:

1. Cree una solución de Azure SQL Analytics en Azure Marketplace.
2. Cree un área de trabajo de supervisión en la solución.
3. Configure bases de datos para transmitir datos de telemetría de diagnóstico al área de trabajo.

Si usa grupos elásticos o instancias administradas, también debe configurar la transmisión en streaming de datos de telemetría de diagnóstico a partir de estos recursos.

### <a name="create-azure-sql-analytics-resource"></a>Creación de un recurso de Azure SQL Analytics

1. Busque Azure SQL Analytics en Azure Marketplace y selecciónelo.

   ![Busque Azure SQL Analytics en el portal.](./media/sql-database-metrics-diag-logging/sql-analytics-in-marketplace.png)

2. Seleccione **Crear** en la pantalla de información general de la solución.

3. Rellene el formulario de Azure SQL Analytics con la información adicional necesaria: nombre del área de trabajo, suscripción, grupo de recursos, ubicación y plan de tarifa.

   ![Configuración de Azure SQL Analytics en el portal](./media/sql-database-metrics-diag-logging/sql-analytics-configuration-blade.png)

4. Seleccione **Aceptar** para confirmar y después **Crear**.

### <a name="configure-databases-to-record-metrics-and-diagnostics-logs"></a>Configuración de bases de datos para registrar las métricas y los registros de diagnóstico

La manera más sencilla de configurar la ubicación en que las bases de datos registran los datos métricos es mediante Azure Portal. Tal como se ha descrito anteriormente, vaya a su recurso de SQL Database en Azure Portal y seleccione **Configuración de diagnóstico**.

Si utiliza grupos elásticos o instancias administradas, también tendrá que configurar los valores de diagnóstico de estos recursos para habilitar los datos de telemetría de diagnóstico que se transmitirán al área de trabajo.

### <a name="use-the-sql-analytics-solution"></a>Uso de la solución SQL Analytics

Puede usar SQL Analytics como panel jerárquico para ver sus recursos de SQL Database. Para obtener información sobre cómo utilizar la solución SQL Analytics, consulte [Supervisión de Azure SQL Database mediante Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="stream-into-event-hubs"></a>Transmisión a Event Hubs

Puede transmitir las métricas y los registros de diagnóstico de SQL Database a Event Hubs mediante el uso de la opción **Transmitir a un centro de eventos** integrada en Azure Portal. También puede habilitar el identificador de regla de Service Bus mediante una configuración de diagnóstico a través de los cmdlets de PowerShell, la CLI de Azure o la API de REST de Azure Monitor.

### <a name="what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs"></a>Utilidad de las métricas y los registros de diagnóstico de Event Hubs

Una vez que los datos seleccionados se transmiten a los Event Hubs, está un paso más cerca de habilitar escenarios de supervisión avanzados. Los centros de Event Hubs actúan como la puerta de entrada de una canalización de eventos. Una vez que los datos se recopilan en un centro de eventos, se pueden transformar y almacenar con un proveedor de análisis en tiempo real o un adaptador de almacenamiento. Los centros de Event Hubs desacoplan la producción de un flujo de eventos del consumo de esos eventos. De esta manera, los consumidores de eventos pueden tener acceso a los eventos en su propia programación. Para obtener más información sobre Event Hubs, consulte:

- [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

Puede usar las métricas transmitidas en Event Hubs para:

- **Visualización del estado del servicio mediante la transmisión de datos de ruta de acceso activa a Power BI**. Con Event Hubs, Stream Analytics y Power BI, puede transformar fácilmente las métricas y los datos de diagnóstico en información prácticamente en tiempo real sobre los servicios de Azure. Para obtener información general sobre cómo configurar un centro de Event Hubs, procesar datos con Stream Analytics y usar Power BI como salida, vea [Stream Analytics y Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).

- **Transmisión de registros a registros de terceros y flujos de telemetría**. Con la transmisión de Event Hubs puede enviar las métricas y los registros de diagnóstico a distintas soluciones de supervisión y análisis de registros de terceros.

- **Creación de una plataforma personalizada de registro y telemetría**. ¿Tiene ya una plataforma de telemetría personalizada o está pensando en crear una? La naturaleza de suscribir a publicación altamente escalable de Event Hubs le permite ingerir registros de diagnóstico de forma flexible. Vea la [guía de Dan Rosanova para usar Event Hubs en una plataforma de telemetría de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-storage"></a>Transmisión a Storage

Puede almacenar las métricas y los registros de diagnóstico de SQL Database mediante la opción **Archivar en una cuenta de almacenamiento** integrada en Azure Portal. También puede habilitar Storage mediante una configuración de diagnóstico a través de los cmdlets de PowerShell, la CLI de Azure o la API de REST de Azure Monitor.

### <a name="schema-of-metrics-and-diagnostics-logs-in-the-storage-account"></a>Esquema de métricas y registros de diagnóstico en la cuenta de almacenamiento

Una vez que ha configurado la recopilación de métricas y los registros de diagnóstico, se crea un contenedor de almacenamiento en la cuenta de almacenamiento seleccionada cuando las primeras filas de datos están disponibles. La estructura de los blobs es:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

O, sencillamente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por ejemplo, un nombre de blob para las métricas básicas podría ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Un nombre de blob para almacenar datos desde un grupo elástico tiene el aspecto siguiente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

## <a name="data-retention-policy-and-pricing"></a>Directiva de retención de datos y precios

Si selecciona Event Hubs o una cuenta de almacenamiento, puede especificar una directiva de retención. Esta directiva elimina los datos anteriores a un período de tiempo seleccionado. Si especifica Log Analytics, la directiva de retención depende del plan de tarifa seleccionado. En este caso, las unidades gratuitas de ingesta de datos especificadas pueden habilitar la supervisión gratuita de varias bases de datos al mes. Todo uso en exceso de la telemetría de diagnóstico de las unidades gratuitas puede suponer costos. Tenga en cuenta que las bases de datos activas con cargas de trabajo más pesadas ingieren más datos que las bases de datos inactivas. Para más información, vea [Precios de análisis de registros](https://azure.microsoft.com/pricing/details/monitor/).

Si usa Azure SQL Analytics, puede supervisar el consumo de ingesta de datos en la solución. Para ello, seleccione el **área de trabajo de OMS** en el menú de navegación de Azure SQL Analytics y luego elija **Uso** y **costos estimados**.

## <a name="metrics-and-logs-available"></a>Métricas y registros disponibles

La supervisión de telemetría disponible para Azure SQL Database, grupos elásticos y la instancia administrada se describe más abajo. Los datos de telemetría de supervisión recopilados en SQL Analytics pueden usarse para realizar su propio análisis personalizado y desarrollo de aplicaciones mediante el lenguaje de [consultas de registro de Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="basic-metrics"></a>Métricas básicas

Vea las siguientes tablas para más detalles sobre todas las métricas básicas por recurso.

> [!NOTE]
> La opción Métricas básicas se conocía anteriormente como Todas las métricas. El cambio se realizó solo en el nombre y no se produjo ningún cambio en las métricas supervisadas. Este cambio se inició para permitir la introducción de las categorías de métricas adicionales en el futuro.

### <a name="basic-metrics-for-elastic-pools"></a>Métricas básicas para grupos elásticos

|**Recurso**|**Métricas**|
|---|---|
|Grupo elástico|Porcentaje de eDTU; eDTU usada; límite de eDTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; porcentaje de sesiones; porcentaje de trabajos; almacenamiento; porcentaje de almacenamiento; límite de almacenamiento y porcentaje de almacenamiento de XTP |

### <a name="basic-metrics-for-azure-sql-databases"></a>Métricas básicas para instancias de Azure SQL Database

|**Recurso**|**Métricas**|
|---|---|
|Azure SQL Database|Porcentaje de DTU; DTU usada; límite de DTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; conexiones correctas, erróneas o bloqueadas por el firewall; porcentaje de sesiones; porcentaje de trabajos; almacenamiento; porcentaje de almacenamiento; porcentaje de almacenamiento de XTP e interbloqueos |

## <a name="basic-logs"></a>Registros básicos

Los detalles de datos de telemetría disponibles para todos los registros se documentan en las tablas siguientes. Vea el [registro de diagnóstico admitido](#supported-diagnostic-logging-for-azure-sql-databases-and-instance-databases) para comprender qué registros se admiten para un tipo de base de datos concreto: base de datos de Azure SQL única, agrupada o de instancia.

### <a name="resource-usage-stats-for-managed-instance"></a>Estadísticas de uso de recursos para una instancia administrada

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure|
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: ResourceUsageStats |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: MANAGEDINSTANCES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre de la instancia administrada |
|ResourceId|URI de recurso |
|SKU_s|SKU del producto de instancia administrada |
|virtual_core_count_s|Número de núcleos virtuales disponibles |
|avg_cpu_percent_s|Porcentaje de CPU medio |
|reserved_storage_mb_s|Capacidad de almacenamiento reservado en la instancia administrada |
|storage_space_used_mb_s|Almacenamiento usado en la instancia administrada |
|io_requests_s|Recuento de IOPS |
|io_bytes_read_s|Bytes de IOPS leídos |
|io_bytes_written_s|Bytes de IOPS escritos |

### <a name="query-store-runtime-statistics"></a>Estadísticas de tiempo de ejecución del Almacén de consultas

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: QueryStoreRuntimeStatistics |
|OperationName|Nombre de la operación. Siempre: QueryStoreRuntimeStatisticsEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|query_hash_s|Hash de consulta |
|query_plan_hash_s|Hash del plan de consulta |
|statement_sql_handle_s|Controlador de SQL de instrucción |
|interval_start_time_d|Inicio datetimeoffset del intervalo en número de tics de 1900-1-1 |
|interval_end_time_d|Fin datetimeoffset del intervalo en número de tics de 1900-1-1 |
|logical_io_writes_d|Número total de escrituras de E/S lógicas |
|max_logical_io_writes_d|Número máximo de escrituras de E/S lógicas por ejecución |
|physical_io_reads_d|Número total de lecturas de E/S físicas |
|max_physical_io_reads_d|Número máximo de lecturas de E/S lógicas por ejecución |
|logical_io_reads_d|Número total de lecturas de E/S lógicas |
|max_logical_io_reads_d|Número máximo de lecturas de E/S lógicas por ejecución |
|execution_type_d|Tipo de ejecución |
|count_executions_d|Número de ejecuciones de la consulta |
|cpu_time_d|Tiempo total de la CPU usado por la consulta en microsegundos |
|max_cpu_time_d|Consumidor de tiempo máximo de la CPU por una sola ejecución en microsegundos |
|dop_d|Suma de los grados de paralelismo |
|max_dop_d|Grado máximo de paralelismo que se usa para una sola ejecución |
|rowcount_d|Número total de filas devueltas |
|max_rowcount_d|Número máximo de filas devueltas en una sola ejecución |
|query_max_used_memory_d|Cantidad total de memoria usada en KB |
|max_query_max_used_memory_d|Cantidad máxima de memoria usada por una sola ejecución en KB |
|duration_d|Tiempo de ejecución total en milisegundos |
|max_duration_d|Tiempo de ejecución máximo de una sola ejecución |
|num_physical_io_reads_d|Número total de lecturas físicas |
|max_num_physical_io_reads_d|Número máximo de lecturas físicas por ejecución |
|log_bytes_used_d|Cantidad total de bytes de registro usados |
|max_log_bytes_used_d|Cantidad máxima de bytes de registro usados por ejecución |
|query_id_d|Identificador de la consulta en el Almacén de consultas |
|plan_id_d|Identificador del plan en el Almacén de consultas |

Obtenga más información sobre los [datos de estadísticas de tiempo de ejecución del Almacén de consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql).

### <a name="query-store-wait-statistics"></a>Estadísticas de espera del Almacén de consultas

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: QueryStoreWaitStatistics |
|OperationName|Nombre de la operación. Siempre: QueryStoreWaitStatisticsEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|wait_category_s|Categoría de la espera |
|is_parameterizable_s|La consulta se puede parametrizar |
|statement_type_s|Tipo de la instrucción |
|statement_key_hash_s|Hash de clave de instrucción |
|exec_type_d|Tipo de ejecución |
|total_query_wait_time_ms_d|Tiempo total de espera de la consulta en la categoría de espera específica |
|max_query_wait_time_ms_d|Tiempo de espera máximo de la consulta en ejecución individual en la categoría de espera específica |
|query_param_type_d|0 |
|query_hash_s|Hash de consulta del Almacén de consultas |
|query_plan_hash_s|Hash de plan de consulta del Almacén de consultas |
|statement_sql_handle_s|Controlador de instrucción del Almacén de consultas |
|interval_start_time_d|Inicio datetimeoffset del intervalo en número de tics de 1900-1-1 |
|interval_end_time_d|Fin datetimeoffset del intervalo en número de tics de 1900-1-1 |
|count_executions_d|Número de ejecuciones de la consulta. |
|query_id_d|Identificador de la consulta en el Almacén de consultas |
|plan_id_d|Identificador del plan en el Almacén de consultas |

Obtenga más información sobre los [datos de estadísticas de espera del Almacén de consultas](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql).

### <a name="errors-dataset"></a>Conjunto de datos de errores

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQ |
|Categoría|Nombre de la categoría Siempre: Errors |
|OperationName|Nombre de la operación. Siempre: ErrorEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|Message|Mensaje de error en texto sin formato. |
|user_defined_b|Es el bit de error definido por el usuario. |
|error_number_d|Código de error |
|severity|Gravedad del error |
|state_d|Estado del error |
|query_hash_s|Hash de consulta de la consulta errónea, si está disponible |
|query_plan_hash_s|Hash de plan de consulta de la consulta errónea, si está disponible |

Obtenga más información sobre [mensajes de error de SQL Server](https://msdn.microsoft.com/library/cc645603.aspx).

### <a name="database-wait-statistics-dataset"></a>Conjunto de datos de estadísticas de espera de base de datos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: DatabaseWaitStatistics |
|OperationName|Nombre de la operación. Siempre: DatabaseWaitStatisticsEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|wait_type_s|Nombre del tipo de espera |
|start_utc_date_t [UTC]|Hora de inicio del período medido |
|end_utc_date_t [UTC]|Hora de finalización del período medido |
|delta_max_wait_time_ms_d|Tiempo máximo esperado por ejecución |
|delta_signal_wait_time_ms_d|Tiempo de espera de señales total |
|delta_wait_time_ms_d|Tiempo total de espera en el período |
|delta_waiting_tasks_count_d|Número de tareas en espera |

Obtenga más información sobre las [estadísticas de espera de la base de datos](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql).

### <a name="time-outs-dataset"></a>Conjunto de datos de los tiempos de espera

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: Tiempos de expiración |
|OperationName|Nombre de la operación. Siempre: TimeoutEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|error_state_d|Código de estado de error |
|query_hash_s|Hash de consulta si está disponible |
|query_plan_hash_s|Hash de plan de consulta si está disponible |

### <a name="blockings-dataset"></a>Conjunto de datos de bloqueos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: Blocks |
|OperationName|Nombre de la operación. Siempre: BlockEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|lock_mode_s|Modo de bloqueo usado por la consulta |
|resource_owner_type_s|Propietario del bloqueo |
|blocked_process_filtered_s|Archivo XML de informe del proceso bloqueado |
|duration_d|Duración del bloqueo en microsegundos |

### <a name="deadlocks-dataset"></a>Conjunto de datos de interbloqueos

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC] |Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: Interbloqueos |
|OperationName|Nombre de la operación. Siempre: DeadlockEvent |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|deadlock_xml_s|Informe XML de interbloqueo |

### <a name="automatic-tuning-dataset"></a>Conjunto de datos de ajuste automático

|Propiedad|Descripción|
|---|---|
|TenantId|El identificador de inquilino |
|SourceSystem|Siempre: Azure |
|TimeGenerated [UTC]|Marca de tiempo de cuándo se registró el registro |
|type|Siempre: AzureDiagnostics |
|ResourceProvider|Nombre del proveedor de recursos Siempre: MICROSOFT.SQL |
|Categoría|Nombre de la categoría Siempre: AutomaticTuning |
|Recurso|Nombre del recurso |
|ResourceType|Nombre del tipo de recurso Siempre: SERVERS/DATABASES |
|SubscriptionId|GUID de la suscripción para la base de datos |
|ResourceGroup|Nombre del grupo de recursos de la base de datos |
|LogicalServerName_s|Nombre del servidor de la base de datos |
|LogicalDatabaseName_s|Nombre de la base de datos |
|ElasticPoolName_s|Nombre del grupo elástico de la base de datos, si lo hay |
|DatabaseName_s|Nombre de la base de datos |
|ResourceId|URI de recurso |
|RecommendationHash_s|Código hash único de la recomendación de ajuste automático |
|OptionName_s|Operación de ajuste automático |
|Schema_s|Esquema de la base de datos |
|Table_s|Tabla afectada |
|IndexName_s|Nombre del índice |
|IndexColumns_s|Nombre de la columna |
|IncludedColumns_s|Columnas incluidas |
|EstimatedImpact_s|Impacto estimado del formato JSON de la recomendación de ajuste automático |
|Event_s|Tipo de evento de ajuste automático |
|Timestamp_t|Última marca de tiempo actualizada |

### <a name="intelligent-insights-dataset"></a>Conjunto de datos de Intelligent Insights

Obtenga más información sobre el [formato de registro de Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).

## <a name="next-steps"></a>Pasos siguientes

Para aprender a habilitar el registro y comprender las métricas y las categorías de registro admitidas por los diferentes servicios de Azure, vea lo siguiente:

- [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)
- [Información general de los registros de diagnóstico de Azure](../azure-monitor/platform/diagnostic-logs-overview.md)

Para obtener información sobre Event Hubs, lea lo siguiente:

- [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
