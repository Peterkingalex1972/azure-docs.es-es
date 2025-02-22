---
title: Supervisión de Azure SQL Data Sync con registros de Azure Monitor| Microsoft Docs
description: Aprenda a supervisar Azure SQL Data Sync con registros de Azure Monitor
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: allenwux
ms.author: xiwu
ms.reviewer: carlrab
ms.date: 12/20/2018
ms.openlocfilehash: d1461a1bb026d478d51a5f79cc02b34172524db6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68566410"
---
# <a name="monitor-sql-data-sync-with-azure-monitor-logs"></a>Supervisión de SQL Data Sync con registros de Azure Monitor 

Antes, para consultar el registro de actividad de SQL Data Sync y detectar errores y advertencias, había que hacerlo manualmente en el Azure Portal, o bien recurrir a PowerShell o a la API de REST. Siga los pasos descritos en este artículo para configurar una solución personalizada que mejora la experiencia de supervisión de Data Sync. Esta solución se puede personalizar para adaptarse a su escenario.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Para obtener información general acerca de SQL Data Sync, consulte [Sincronización de datos entre varias bases de datos locales y de la nube con Azure SQL Data Sync](sql-database-sync-data.md).

> [!IMPORTANT]
> Azure SQL Data Sync **no** admite en este momento Instancia administrada de Azure SQL Database.

## <a name="monitoring-dashboard-for-all-your-sync-groups"></a>Panel de supervisión de todos los grupos de sincronización 

Ya no es necesario buscar por todos los registros de cada grupo de sincronización individualmente para dar con algún problema. Ahora, todos los grupos de sincronización de cualquiera de sus suscripciones se pueden supervisar en un solo lugar, con una vista de Azure Monitor personalizada. Esta vista presenta la información que es importante para los clientes de SQL Data Sync.

![Panel de supervisión de Data Sync](media/sql-database-sync-monitor-oms/sync-monitoring-dashboard.png)

## <a name="automated-email-notifications"></a>Notificaciones de correo electrónico automatizadas

Ya no es necesario consultar el registro manualmente en Azure Portal o a través de PowerShell o la API de REST. Con los [registros de Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) puede crear alertas que se envían directamente a las direcciones de correo electrónico de los usuarios que necesitan ver esas alertas cuando se produce un error.

![Notificaciones de correo electrónico de Data Sync](media/sql-database-sync-monitor-oms/sync-email-notifications.png)

## <a name="how-do-you-set-up-these-monitoring-features"></a>¿Cómo configurar estas características de supervisión? 

Si hace lo siguiente, implementará una solución de supervisión de registros de Azure Monitor personalizada para SQL Data Sync en menos de una hora:

Debe configurar tres componentes:

-   Un runbook de PowerShell para introducir datos de registro de SQL Data Sync en registros de Azure Monitor.

-   Una alerta de Azure Monitor para las notificaciones de correo electrónico.

-   Una vista de Azure Monitor para la supervisión.

### <a name="samples-to-download"></a>Descarga de ejemplos

Descargue los dos ejemplos siguientes:

-   [DataSyncLogPowerShellRunbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vista de Azure Monitor de Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

### <a name="prerequisites"></a>Requisitos previos

Asegúrese de que tiene configurado lo siguiente:

-   Una cuenta de Azure Automation

-   Área de trabajo de Log Analytics

## <a name="powershell-runbook-to-get-sql-data-sync-log"></a>Runbook de PowerShell para obtener el registro de SQL Data Sync 

Use un runbook de PowerShell hospedado en Azure Automation para extraer los datos del registro de SQL Data Sync y enviarlos a los registros de Azure Monitor. Se incluye un script de ejemplo. Como requisito previo, debe tener una cuenta de Azure Automation. Luego, debe crear un runbook y programarlo para ejecutarlo. 

### <a name="create-a-runbook"></a>Crear un runbook

Para más información sobre cómo crear un runbook, vea [Mi primer runbook de PowerShell](https://docs.microsoft.com/azure/automation/automation-first-runbook-textual-powershell).

1.  En la cuenta de Azure Automation, seleccione la pestaña **Runbooks** en Automatización de procesos.

2.  Seleccione **Agregar un Runbook** en la esquina superior izquierda de la página Runbooks.

3.  Seleccione **Importar un Runbook existente**.

4.  En **Archivo de Runbook**, use el `DataSyncLogPowerShellRunbook` proporcionado. Establezca el **Tipo de Runbook** en `PowerShell`. Asigne un nombre al runbook.

5.  Seleccione **Crear**. Ya tenemos un runbook.

6.  En la cuenta de Azure Automation, seleccione la pestaña **Variables** en Recursos compartidos.

7.  Seleccione **Agregar una variable** en la página Variables. Cree una variable para almacenar la hora de la última ejecución del runbook. Si tiene varios runbooks, será necesaria una variable para cada uno de ellos.

8.  Especifique el nombre de variable `DataSyncLogLastUpdatedTime` y establezca el tipo en Fecha y hora.

9.  Seleccione el runbook y haga clic en el botón Editar en la parte superior de la página.

10. Realice los cambios pertinentes según su cuenta y la configuración de SQL Data Sync (Para obtener información más pormenorizada, consulte el script de ejemplo).

    1.  Información de Azure

    2.  Información del grupo de sincronización

    3.  Información de registros de Azure Monitor Encontrará esta información en Azure Portal | Configuración | Orígenes conectados. Para más información sobre cómo enviar datos a registros de Azure Monitor, consulte [Envío de datos de registro a Azure Monitor con HTTP Data Collector API (versión preliminar pública)](../azure-monitor/platform/data-collector-api.md).

11. Ejecute el runbook en el panel de prueba y confirme que se ha realizado correctamente.

    Si se producen errores, asegúrese de que tiene instalado el módulo de PowerShell más reciente. También puede instalar el módulo de PowerShell más reciente en la **Galería de módulos** de su cuenta de Automation.

12. Haga clic en **Publicar**.

### <a name="schedule-the-runbook"></a>Programar un runbook

Para programar un runbook:

1.  En el runbook, seleccione la pestaña **Programaciones** en Recursos.

2.  Seleccione **Agregar una programación** en la página Programaciones.

3.  Seleccione **Vincular una programación a su Runbook**.

4.  Seleccione **Crear una nueva programación**.

5.  Establezca la opción **Periodicidad** en Periódico y defina el intervalo que quiera. Use el mismo intervalo aquí, en el script y en los registros de Azure Monitor.

6.  Seleccione **Crear**.

### <a name="check-the-automation"></a>Comprobar la automatización

Para averiguar si la automatización funciona según lo previsto, en la sección **Información general** de su cuenta de Automation, busque la vista **Estadísticas del trabajo** dentro de **Supervisión**. Ancle esta vista en el panel para que sea más fácil verla. Las ejecuciones del runbook que se realizan correctamente se muestran como "Completado" y las que no, como "Error".

## <a name="create-an-azure-monitor-reader-alert-for-email-notifications"></a>Creación de una alerta de lector de Azure Monitor para notificaciones de correo electrónico

Haga lo siguiente para crear una alerta que usa registros de Azure Monitor. Como requisito previo, registros de Azure Monitor debe estar vinculado a un área de trabajo de Log Analytics.

1.  En Azure Portal, seleccione **Búsqueda de registros**.

2.  Crear una consulta para seleccionar los errores y las advertencias por grupo de sincronización dentro del intervalo seleccionado. Por ejemplo:

    `Type=DataSyncLog\_CL LogLevel\_s!=Success| measure count() by SyncGroupName\_s interval 60minute`

3.  Después de ejecutar la consulta, seleccione la campana que reza **Alerta**.

4.  En **Generar alerta según**, seleccione **Unidades métricas**.

    1.  Establezca Valor agregado en **Mayor que**.

    2.  Después de establecer **Mayor que**, especifique el umbral de tiempo que debe transcurrir para recibir notificaciones. En Data Sync, los errores transitorios son habituales. Para reducir el ruido, establezca el umbral en 5.

5.  En **Acciones**, establezca **Notificación por correo electrónico** en "Sí". Especifique los destinatarios de correo electrónico que quiera.

6.  Haga clic en **Save**(Guardar). Ahora, los destinatarios especificados recibirán notificaciones por correo electrónico cuando se produzcan errores.

## <a name="create-an-azure-monitor-view-for-monitoring"></a>Creación de una vista de Azure Monitor para la supervisión

Con este paso crearemos una vista de Azure Monitor que nos permita supervisar visualmente todos los grupos de sincronización especificados. La vista engloba varios componentes:

-   Un icono de información general que refleja el número de errores, operaciones correctas y advertencias que tienen todos los grupos de sincronización.

-   Un icono para todos los grupos de sincronización que muestra el número de errores y advertencias de cada grupo de sincronización. Los grupos sin problemas no aparecen en este icono.

-   Un icono para cada grupo de sincronización que muestra el número de errores, operaciones correctas y advertencias, además de los mensajes de error recientes.

Haga lo siguiente para configurar la vista de Azure Monitor:

1.  En la página principal del área de trabajo de Log Analytics, seleccione el signo más a la izquierda para abrir el **Diseñador de vistas**.

2.  Seleccione **Importar** en la barra superior del diseñador de vistas. Luego, seleccione el archivo de ejemplo "DataSyncLogOMSView".

3.  La vista de ejemplo sirve para administrar dos grupos de sincronización. Modifique esta vista para que se adapte a su escenario. Haga clic en **Editar** y realice los siguientes cambios:

    1.  Cree los objetos de anillo y de lista que sean necesarios en la galería.

    2.  En cada icono, actualice las consultas con su información.

        1.  En cada icono, cambie el intervalo de TimeStamp_t según sea necesario.

        2.  En los iconos de cada grupo de sincronización, actualice los nombres de grupo de sincronización.

    3.  En cada icono, actualice el título según sea necesario.

4.  Haga clic en **Guardar**. La vista está lista.

## <a name="cost-of-this-solution"></a>Coste de esta solución

En la mayoría de los casos, esta solución es gratuita.

**Azure Automation**: se puede incurrir en costos en función del uso que haga de la cuenta de Azure Automation. Los primeros 500 minutos de tiempo de ejecución del trabajo al mes son gratuitos. Esta solución casi nunca suele superar los 500 minutos al mes. Para no incurrir en gastos, programe el runbook para que se ejecute en un intervalo de dos horas o más. Para más información, vea [Precios de Automation](https://azure.microsoft.com/pricing/details/automation/).

**Registros de Azure Monitor:** se puede incurrir en un costo asociado a los registros de Azure Monitor en función del uso que haga. El nivel gratuito incluye 500 MB de datos introducidos por día. Esta solución casi nunca suele superar los 500 MB de datos introducidos al mes. Para reducir el uso, use el filtro de solo errores incluido en el runbook. Si usa más de 500 MB al día, actualice al nivel de pago para no arriesgarse a que Log Analytics se detenga cuando se alcance la limitación. Para más información, consulte [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/log-analytics/).

## <a name="code-samples"></a>Ejemplos de código

Descargue los códigos de ejemplo descritos en este artículo de las siguientes ubicaciones:

-   [DataSyncLogPowerShellRunbook](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogPowerShellRunbook.ps1)

-   [Vista de Azure Monitor de Data Sync](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/sql-data-sync/DataSyncLogOmsView.omsview)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre SQL Data Sync, consulte:

-   Introducción: [Sincronización de datos entre varias bases de datos locales y en la nube con Azure SQL Data Sync](sql-database-sync-data.md)
-   Configuración de Data Sync
    - En el portal, [Tutorial: Configuración de SQL Data Sync para sincronizar datos entre Azure SQL Database e instancias locales de SQL Server](sql-database-get-started-sql-data-sync.md)
    - Con PowerShell
        -  [Uso de PowerShell para sincronizar varias bases de datos de Azure SQL](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [Uso de PowerShell para realizar la sincronización entre una base de datos de Azure SQL y una base de datos de SQL Server local](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Agente de sincronización de datos: [Agente de sincronización de datos para Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Procedimientos recomendados: [Procedimientos recomendados para Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Solución de problemas: [Solución de problemas de Azure SQL Data Sync](sql-database-troubleshoot-data-sync.md)
-   Actualización del esquema de sincronización
    -   Con Transact-SQL: [Automatización de la replicación de los cambios de esquema en Azure SQL Data Sync](sql-database-update-sync-schema.md)
    -   Con PowerShell: [Usar PowerShell para actualizar el esquema de sincronización en un grupo de sincronización existente](scripts/sql-database-sync-update-schema.md)

Para más información sobre SQL Database, consulte:

-   [Información general de SQL Database](sql-database-technical-overview.md)
-   [Administración del ciclo de vida de las aplicaciones](https://msdn.microsoft.com/library/jj907294.aspx)
