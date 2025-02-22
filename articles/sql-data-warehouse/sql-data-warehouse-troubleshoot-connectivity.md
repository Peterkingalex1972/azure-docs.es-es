---
title: Solución de problemas de Azure SQL Data Warehouse | Microsoft Docs
description: Cómo solucionar los problemas de Azure SQL Data Warehouse.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: supportability
ms.date: 03/27/2019
ms.author: anjangsh
ms.reviewer: igorstan
ms.openlocfilehash: 290753b866f15e09a52572fdd7a43a60fc2812d6
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575545"
---
# <a name="troubleshooting-connectivity-issues"></a>Solución de problemas de conectividad

En este artículo se enumeran las técnicas de solución de problemas comunes en torno a la conexión a SQL Data Warehouse.
- [Comprobación de la disponibilidad del servicio](./sql-data-warehouse-troubleshoot-connectivity.md#check-service-availability)
- [Comprobación de la operación de pausa o escalado](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-paused-or-scaling-operation)
- [Comprobación de la configuración de firewall](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-firewall-settings)
- [Comprobación de la configuración del punto de conexión de servicio o red virtual](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-vnetservice-endpoint-settings)
- [Comprobación de los controladores más recientes](./sql-data-warehouse-troubleshoot-connectivity.md#check-for-the-latest-drivers)
- [Comprobación de la cadena de conexión](./sql-data-warehouse-troubleshoot-connectivity.md#check-your-connection-string)
- [Problemas de conexión intermitente](./sql-data-warehouse-troubleshoot-connectivity.md#intermittent-connection-issues)
- [Mensajes comunes de error](./sql-data-warehouse-troubleshoot-connectivity.md#common-error-messages)

## <a name="check-service-availability"></a>Comprobación de la disponibilidad del servicio

Compruebe si el servicio está disponible. En Azure Portal, vaya a la instancia de SQL Data Warehouse a la que está intentando conectarse. En el panel de índice izquierdo, haga clic en **Diagnosticar y solucionar problemas**.

![Selección de Resource Health](./media/sql-data-warehouse-troubleshoot-connectivity/diagnostics-link.png)

Aquí se mostrará el estado del almacenamiento SQL Data Warehouse. Si el servicio no se muestra como **disponible**, consulte más pasos.

![El servicio está disponible](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health.png)

Si Resource Health muestra que el almacenamiento de datos está en pausa o escalado, siga las instrucciones para reanudar el almacenamiento de datos.

![Servicio pausado](./media/sql-data-warehouse-troubleshoot-connectivity/resource-health-pausing.png) Aquí puede encontrar información adicional sobre Resource Health.

## <a name="check-for-paused-or-scaling-operation"></a>Comprobación de la operación de pausa o escalado

Compruebe en el portal si su almacenamiento SQL Data Warehouse está en pausa o se está escalando.

![Servicio en pausa](./media/sql-data-warehouse-troubleshoot-connectivity/overview-paused.png)

Si ve el servicio está en pausa o escalado, compruebe que no es durante la programación de mantenimiento. En el portal de *información general* del almacenamiento SQL Data Warehouse, verá la programación de mantenimiento seleccionada.

![Información general de la programación de mantenimiento](./media/sql-data-warehouse-troubleshoot-connectivity/overview-maintance-schedule.png)

En caso contrario, consulte al Administrador de TI para comprobar que este mantenimiento no es un evento programado. Para reanudar el almacenamiento SQL Data Warehouse, siga los pasos descritos [aquí](https://docs.microsoft.com/azure/sql-data-warehouse/pause-and-resume-compute-portal#resume-compute).

## <a name="check-your-firewall-settings"></a>Comprobación de la configuración de firewall

SQL Data Warehouse se comunica a través del puerto 1433.   Si intenta conectarse desde una red corporativa, es posible que el firewall de la red no permita el tráfico saliente a través del puerto 1433. En ese caso, no puede conectarse al servidor de Azure SQL Database, salvo que el departamento de TI abra el puerto 1433. Puede encontrar información adicional sobre las configuraciones de firewall [aquí](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure#manage-server-level-ip-firewall-rules-using-the-azure-portal).

## <a name="check-your-vnetservice-endpoint-settings"></a>Comprobación de la configuración del punto de conexión de servicio o red virtual

Si recibe los errores 40914 y 40615, consulte la [descripción del error y la resolución aquí](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="check-for-the-latest-drivers"></a>Comprobación de los controladores más recientes

### <a name="software"></a>Software

Asegúrese de usar las herramientas más recientes para conectarse al almacenamiento SQL Data Warehouse:

* SSMS
* Azure Data Studio
* SQL Server Data Tools (Visual Studio)

### <a name="drivers"></a>Controladores

Asegúrese de usar las versiones más recientes del controlador.  El uso de una versión anterior de los controladores podría provocar comportamientos inesperados como que los controladores más antiguos no admitiesen las nuevas características.

* [ODBC](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server)
* [JDBC](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)
* [OLE DB](https://docs.microsoft.com/sql/connect/oledb/download-oledb-driver-for-sql-server)
* [PHP](https://docs.microsoft.com/sql/connect/php/download-drivers-php-sql-server)

## <a name="check-your-connection-string"></a>Comprobación de la cadena de conexión

Asegúrese de que las cadenas de conexión se han establecido correctamente.  A continuación se muestran algunos ejemplos.  También puede encontrar información adicional sobre [las cadenas de conexión aquí](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connection-strings).

Cadena de conexión ADO.NET

```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

Cadena de conexión ODBC

```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

Cadena de conexión PHP

```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

Cadena de conexión JDBC

```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

## <a name="intermittent-connection-issues"></a>Problemas de conexión intermitente

Compruebe si experimenta mucha carga en el servidor con un gran número de solicitudes en cola. Es posible que deba escalar verticalmente el almacenamiento de datos para obtener recursos adicionales.

## <a name="common-error-messages"></a>Mensajes comunes de error

Con los errores 40914 y 40615, consulte la [descripción del error y la resolución aquí](https://docs.microsoft.com/azure/sql-database/sql-database-vnet-service-endpoint-rule-overview?toc=/azure/sql-data-warehouse/toc.json#errors-40914-and-40615).

## <a name="still-having-connectivity-issues"></a>¿Sigue teniendo problemas de conectividad?
Cree una [incidencia de soporte técnico](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) para que el equipo de ingeniería pueda ayudarle.
