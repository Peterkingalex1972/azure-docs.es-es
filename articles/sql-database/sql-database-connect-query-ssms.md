---
title: 'SSMS: Conexión y consulta de datos en una base de datos de Azure SQL | Microsoft Docs'
description: Aprenda a conectarse a SQL Database en Azure con SQL Server Management Studio (SSMS). A continuación, ejecute instrucciones de Transact-SQL (T-SQL) para consultar y editar los datos.
keywords: conexión a base de datos sql,sql server management studio
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/25/2019
ms.openlocfilehash: 0a08ee9d38065b0dff13f68a7b5473aa93787cf5
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569178"
---
# <a name="quickstart-use-sql-server-management-studio-to-connect-and-query-an-azure-sql-database"></a>Guía de inicio rápido: uso de SQL Server Management Studio para conectarse a una base de datos de Azure SQL Database y realizar consultas en ella

En este inicio rápido usará [SQL Server Management Studio][ssms-install-latest-84g] (SSMS) para conectarse a una base de datos de Azure SQL. Después, usará instrucciones Transact-SQL para consultar, insertar, actualizar y eliminar datos. Puede usar SSMS para administrar cualquier infraestructura de SQL, desde SQL Server a SQL Database para Microsoft Windows.  

## <a name="prerequisites"></a>Requisitos previos

Una base de datos de Azure SQL. Puede utilizar uno de estos inicios rápidos para crear y configurar una base de datos en Azure SQL Database:

  || Base de datos única | Instancia administrada |
  |:--- |:--- |:---|
  | Crear| [Portal](sql-database-single-database-get-started.md) | [Portal](sql-database-managed-instance-get-started.md) |
  || [CLI](scripts/sql-database-create-and-configure-database-cli.md) | [CLI](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44) |
  || [PowerShell](scripts/sql-database-create-and-configure-database-powershell.md) | [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md) |
  | Configuración | [Regla de firewall de IP en el nivel de servidor](sql-database-server-level-firewall-rule.md)| [Conectividad desde una máquina virtual](sql-database-managed-instance-configure-vm.md)|
  |||[Conectividad desde el sitio](sql-database-managed-instance-configure-p2s.md)
  |Carga de datos|Adventure Works cargado por inicio rápido|[Restauración de World Wide Importers](sql-database-managed-instance-get-started-restore.md)
  |||Restauración o importación de Adventure Works a partir del archivo [BACPAC](sql-database-import.md) desde [GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/adventure-works)|

  > [!IMPORTANT]
  > Los scripts de este artículo se escriben para utilizar la base de datos Adventure Works. Con una instancia administrada, debe importar la base de datos Adventure Works en una base de datos de instancia o modificar los scripts de este artículo para utilizar la base de datos Wide World Importers.

## <a name="install-the-latest-ssms"></a>Instalación del SSMS más reciente

Antes de empezar, asegúrese de tener instalada la versión más reciente de [SSMS][ssms-install-latest-84g].

## <a name="get-sql-server-connection-information"></a>Obtención de información de conexión de SQL Server

Obtención de la información de conexión necesaria para conectarse a Azure SQL Database. En los procedimientos siguientes, necesitará el nombre completo del servidor o nombre de host, el nombre de la base de datos y la información de inicio de sesión.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

2. Vaya a las páginas **SQL Database** o **Instancias administradas de SQL**.

3. En la página **Información general**, revise el nombre completo del servidor junto a **Nombre del servidor** para una única base de datos o el nombre completo del servidor junto a **Host** para una instancia administrada. Para copiar el nombre del servidor o nombre de host, mantenga el cursor sobre él y seleccione el icono **Copiar**.

## <a name="connect-to-your-database"></a>Conectarse a la base de datos

En SMSS, conéctese al servidor de Azure SQL Database.

> [!IMPORTANT]
> Un servidor de Azure SQL Database escucha en el puerto 1433. Para conectarse a un servidor de SQL Database desde detrás de un firewall corporativo, el firewall debe tener abierto este puerto.
>

1. Abra SSMS. Aparecerá el cuadro de diálogo **Conectar con el servidor** .

2. Escriba la siguiente información:

   | Configuración      | Valor sugerido    | DESCRIPCIÓN |
   | ------------ | ------------------ | ----------- |
   | **Tipo de servidor** | Motor de base de datos | Valor requerido. |
   | **Nombre del servidor** | Nombre completo del servidor | Algo similar a: **mynewserver20170313.database.windows.net**. |
   | **Autenticación** | Autenticación de SQL Server | Este tutorial usa Autenticación de SQL. |
   | **Inicio de sesión** | Identificador de usuario de la cuenta de administrador del servidor | El identificador de usuario de la cuenta de administrador del servidor que se usó para crear el servidor. |
   | **Contraseña** | Contraseña segura de la cuenta de administrador | La contraseña de la cuenta de administrador del servidor que se usó para crear el servidor. |
   ||||

   ![conectar con el servidor](./media/sql-database-connect-query-ssms/connect.png)  

3. Seleccione **Opciones** en el cuadro de diálogo **Conectar con el servidor**. En el menú desplegable **Conectar con el servidor**, seleccione **mySampleDatabase**.

   ![conectar a base de datos en el servidor](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Seleccione **Conectar**. Se abre la ventana del Explorador de objetos.

5. Para ver los objetos de la base de datos, expanda **Bases de datos** y luego expanda **mySampleDatabase**.

   ![Objetos mySampleDatabase](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="query-data"></a>Datos de consulta

Ejecute el código Transact-SQL [SELECT](https://msdn.microsoft.com/library/ms189499.aspx) para consultar los 20 primeros productos por categoría.

1. En el Explorador de objetos, haga clic con el botón derecho en **mySampleDatabase** y seleccione **Nueva consulta**. Se abre una ventana de consulta conectada a la base de datos.

2. En la ventana de consulta, pegue esta consulta SQL.

   ```sql
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM [SalesLT].[ProductCategory] pc
   JOIN [SalesLT].[Product] p
   ON pc.productcategoryid = p.productcategoryid;
   ```

3. En la barra de herramienta, seleccione **Ejecutar** para recuperar los datos de las tablas `Product` y `ProductCategory`.

    ![consulta para recuperar datos desde las tabla Product y ProductCategory](./media/sql-database-connect-query-ssms/query2.png)

## <a name="insert-data"></a>Insertar datos

Ejecute el código Transact-SQL [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) para crear un nuevo producto en la tabla `SalesLT.Product`.

1. Reemplace la consulta anterior por esta otra.

   ```sql
   INSERT INTO [SalesLT].[Product]
           ( [Name]
           , [ProductNumber]
           , [Color]
           , [ProductCategoryID]
           , [StandardCost]
           , [ListPrice]
           , [SellStartDate] )
     VALUES
           ('myNewProduct'
           ,123456789
           ,'NewColor'
           ,1
           ,100
           ,100
           ,GETDATE() );
   ```

2. Seleccione **Ejecutar** para insertar una nueva fila en la tabla `Product`. El panel **Mensajes** muestra **(1 fila afectada)** .

## <a name="view-the-result"></a>Visualización del resultado

1. Reemplace la consulta anterior por esta otra.

   ```sql
   SELECT * FROM [SalesLT].[Product]
   WHERE Name='myNewProduct'
   ```

2. Seleccione **Execute**(Ejecutar). Aparecen los siguientes resultados.

   ![resultado de la consulta de la tabla Product](./media/sql-database-connect-query-ssms/result.png)

## <a name="update-data"></a>Actualización de datos

Ejecute el código Transact-SQL [UPDATE](https://msdn.microsoft.com/library/ms177523.aspx) para modificar el nuevo producto.

1. Reemplace la consulta anterior por esta otra.

   ```sql
   UPDATE [SalesLT].[Product]
   SET [ListPrice] = 125
   WHERE Name = 'myNewProduct';
   ```

2. Seleccione **Ejecutar** para actualizar la fila especificada en la tabla `Product`. El panel **Mensajes** muestra **(1 fila afectada)** .

## <a name="delete-data"></a>Eliminación de datos

Ejecute el código Transact-SQL [DELETE](https://msdn.microsoft.com/library/ms189835.aspx) para eliminar el nuevo producto.

1. Reemplace la consulta anterior por esta otra.

   ```sql
   DELETE FROM [SalesLT].[Product]
   WHERE Name = 'myNewProduct';
   ```

2. Seleccione **Ejecutar** para eliminar la fila especificada en la tabla `Product`. El panel **Mensajes** muestra **(1 fila afectada)** .

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre SSMS, consulte [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx).
- Para conectarse y consultar mediante Azure Portal, consulte [Conexión y consulta con el editor de consultas SQL de Azure Portal](sql-database-connect-query-portal.md).
- Para conectarse y consultar con Visual Studio, vea [Conexión y consultas con Visual Studio Code](sql-database-connect-query-vscode.md).
- Para conectarse y consultar con .NET, vea [Conexión y consultas con .NET](sql-database-connect-query-dotnet.md).
- Para conectarse y consultar con PHP, vea [Conexión y consultas con PHP](sql-database-connect-query-php.md).
- Para conectarse y consultar con Node.js, vea [Conexión y consultas con Node.js](sql-database-connect-query-nodejs.md).
- Para conectarse y consultar con Java, vea [Conexión y consultas con Java](sql-database-connect-query-java.md).
- Para conectarse y consultar con Python, vea [Conexión y consultas con Python](sql-database-connect-query-python.md).
- Para conectarse y consultar con Ruby, vea [Conexión y consultas con Ruby](sql-database-connect-query-ruby.md).

<!-- Article link references. -->

[ssms-install-latest-84g]: https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms
