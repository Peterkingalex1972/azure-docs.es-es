---
title: Cadenas de conexión de Azure SQL Data Warehouse | Microsoft Docs
description: Cadenas de conexión de SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: afc8dbfa0bc1b1ad37e5f1a0f290c42998ed1e4b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68479702"
---
# <a name="connection-strings-for-azure-sql-data-warehouse"></a>Cadenas de conexión de Azure SQL Data Warehouse
Puede conectarse a SQL Data Warehouse con varios protocolos de aplicación diferentes como [ADO.NET][ADO.NET], [ODBC][ODBC], [PHP][PHP] y [JDBC][JDBC]. A continuación se muestran algunos ejemplos de cadenas de conexión para cada protocolo.  También puede usar el Portal de Azure para crear la cadena de conexión.  Para generar la cadena de conexión mediante Azure Portal, vaya a la hoja de la base de datos en *Aspectos básicos* y haga clic en *Mostrar cadenas de conexión de base de datos*.

## <a name="sample-adonet-connection-string"></a>Cadena de conexión ADO.NET de ejemplo
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## <a name="sample-odbc-connection-string"></a>Cadena de conexión ODBC de ejemplo
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## <a name="sample-php-connection-string"></a>Cadena de conexión PHP de ejemplo
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## <a name="sample-jdbc-connection-string"></a>Cadena de conexión JDBC de ejemplo
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Considere la posibilidad de establecer el tiempo de espera de conexión en 300 segundos para permitir que la conexión sobreviva durante breves períodos de falta de disponibilidad.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para empezar a realizar consultas en el almacenamiento de datos con Visual Studio y otras aplicaciones, consulte [Query with Visual Studio][Query with Visual Studio](Realización de consultas con Visual Studio).

<!--Image references-->

<!--Azure.com references-->
[Query with Visual Studio]: ./sql-data-warehouse-query-visual-studio.md

<!--MSDN references-->
[ADO.NET]: https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx
[ODBC]: https://msdn.microsoft.com/library/jj730314.aspx
[PHP]: https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396
[JDBC]: https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx

<!--Other references-->
