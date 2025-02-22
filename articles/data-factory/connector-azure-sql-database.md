---
title: Copia de datos con una base de datos de Azure SQL como origen o destino mediante Azure Data Factory | Microsoft Docs
description: Aprenda a copiar datos desde cualquier almacén de datos de origen compatible a Azure SQL Database, o bien desde SQL Database a cualquier almacén de datos receptor compatible mediante Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/12/2019
ms.author: jingwang
ms.openlocfilehash: 2b4d636737dbd75829c9555e340f79c3c867910d
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967559"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copia de datos hacia y desde Azure SQL Database mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión de Azure Data Factory que está usando:"]
> * [Versión 1](v1/data-factory-azure-sql-connector.md)
> * [Versión actual](connector-azure-sql-database.md)

En este artículo se explica cómo copiar datos en y desde Azure SQL Database. Para información sobre Azure Data Factory, lea el [artículo de introducción](introduction.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Azure SQL Database es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con tabla de [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Asignación de Data Flow](concepts-data-flow-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)

En concreto, este conector de Azure SQL Database admite estas funciones:

- Copia de datos mediante la autenticación con SQL y la autenticación de tokens de aplicaciones de Azure Active Directory (Azure AD) con una entidad de servicio o identidades administradas para los recursos de Azure.
- Como origen, la recuperación de datos mediante una consulta SQL o un procedimiento almacenado.
- Como receptor, la anexión de datos a una tabla de destino o la invocación de un procedimiento almacenado con lógica personalizada durante la copia.

>[!NOTE]
>Actualmente, este conector no admite [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) de Azure SQL Database. Como solución alternativa, puede usar un [conector ODBC genérico](connector-odbc.md) y un controlador ODBC de SQL Server mediante un entorno de ejecución de integración autohospedado. Siga [esta guía](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) con configuraciones de cadena de conexión y descarga del controlador ODBC.

> [!IMPORTANT]
> Si copia los datos mediante el entorno de ejecución de integración de Azure Data Factory, configure un [firewall de Azure SQL Server ](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) para que los servicios de Azure puedan obtener acceso al servidor.
> Si copia los datos mediante un entorno de ejecución de integración autohospedado, configure el firewall de Azure SQL Server para permitir el intervalo IP apropiado. Dicho intervalo incluye la dirección IP de la máquina que se utiliza para conectarse a Azure SQL Database.

## <a name="get-started"></a>Primeros pasos

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporciona información acerca de las propiedades que se usan para definir las entidades de Data Factory específicas de un conector de Azure SQL Database.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Estas propiedades son compatibles con un servicio vinculado de Azure SQL Database:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** debe establecerse en **AzureSqlDatabase**. | Sí |
| connectionString | Especifique la información necesaria para conectarse a la base de datos de Azure SQL para la propiedad **connectionString**. <br/>Marque este campo como **SecureString** para almacenarlo de forma segura en Azure Data Factory. También puede poner una contraseña o una clave de entidad de servicio Azure Key Vault. Si se trata de la autenticación de SQL, extraiga la configuración `password` de la cadena de conexión. Para obtener más información, vea el ejemplo de JSON debajo de la tabla y consulte el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| servicePrincipalId | Especifique el id. de cliente de la aplicación. | Sí, al utilizar la autenticación de Azure AD con una entidad de servicio |
| servicePrincipalKey | Especifique la clave de la aplicación. Marque este campo como [SecureString](store-credentials-in-key-vault.md) para almacenarlo de forma segura en Azure Data Factory, o bien **para hacer referencia a un secreto almacenado en Azure Key Vault**. | Sí, al utilizar la autenticación de Azure AD con una entidad de servicio |
| tenant | Especifique la información del inquilino, como el nombre de dominio o identificador de inquilino, en el que reside la aplicación. Para recuperarlo, mantenga el puntero del mouse en la esquina superior derecha de Azure Portal. | Sí, al utilizar la autenticación de Azure AD con una entidad de servicio |
| connectVia | Este [entorno de ejecución de integración](concepts-integration-runtime.md) se usa para conectarse al almacén de datos. Se puede usar Azure Integration Runtime o un entorno de ejecución de integración autohospedado, si el almacén de datos se encuentra en una red privada. Si no se especifica, se usa el valor predeterminado de Azure Integration Runtime. | Sin |

Para ver los distintos tipos de autenticación, consulte las secciones siguientes acerca de requisitos previos y ejemplos de JSON, respectivamente:

- [Autenticación de SQL](#sql-authentication)
- [Autenticación de token de la aplicación de Azure AD: entidad de servicio](#service-principal-authentication)
- [Autenticación de token de la aplicación de Azure AD: identidades administradas de recursos de Azure](#managed-identity)

>[!TIP]
>Si recibió un error con código de error como "UserErrorFailedToConnectToSqlServer" y un mensaje como "The session limit for the database is XXX and has been reached" (El límite de sesión de la base de datos es XXX y ya se ha alcanzado), agregue `Pooling=false` a la cadena de conexión e inténtelo de nuevo.

### <a name="sql-authentication"></a>Autenticación de SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Ejemplo de servicio vinculado que usa la autenticación de SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Contraseña en Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticación de entidad de servicio

Para usar la autenticación de token de aplicación de Azure AD basada en la entidad de servicio, realice estos pasos:

1. [Cree una aplicación de Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) desde Azure Portal. Anote el nombre de la aplicación y los siguientes valores, que definen el servicio vinculado:

    - Identificador de aplicación
    - Clave de la aplicación
    - Id. de inquilino

2. [Aprovisione un administrador de Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para Azure SQL Server desde Azure Portal, en caso de que aún no lo haya hecho. El administrador de Azure AD debe ser un usuario de Azure AD o un grupo de Azure AD, pero no puede ser una entidad de servicio. Este paso se realiza con el fin de que, en el siguiente paso, pueda usar una identidad de Azure AD para crear un usuario de base de datos independiente para la entidad de servicio.

3. [Cree usuarios de bases de datos independientes](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para la entidad de servicio. Conéctese a la base de datos de la que desea copiar datos (o a la que desea copiarlos) mediante alguna herramienta como SQL Server Management Studio, con una identidad de Azure AD que tenga al menos permiso para MODIFICAR CUALQUIER USUARIO. Ejecute el siguiente T-SQL: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Conceda a la entidad de servicio los permisos necesarios, tal como lo haría normalmente para los usuarios de SQL, u otros usuarios. Ejecute el código siguiente: Para más opciones, consulte [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. En Azure Data Factory, configure un servicio vinculado de Azure SQL Database.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Ejemplo de servicio vinculado que usa la autenticación de entidad de servicio

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Identidades administradas para la autenticación de recursos de Azure

Una factoría de datos se puede asociar con una [identidad administrada para recursos de Azure](data-factory-service-identity.md) que representa esa factoría de datos concreta. Esta identidad administrada se puede usar para la autenticación de Azure SQL Database. Con esta identidad la fábrica designada y puede acceder y copiar datos de su base de datos o en ella.

Para usar la autenticación de identidad administrada, siga estos pasos.

1. [Aprovisione un administrador de Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) para Azure SQL Server desde Azure Portal, en caso de que aún no lo haya hecho. El administrador de Azure AD puede ser un usuario de Azure AD o un grupo de Azure AD. Si concede al grupo con identidad administrada un rol de administrador, omita los pasos 3 y 4. El administrador tiene acceso total a la base de datos.

2. [Cree usuarios de bases de datos independientes](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) para la identidad administrada de Azure Data Factory. Conéctese a la base de datos de la que desea copiar datos (o a la que desea copiarlos) mediante alguna herramienta como SQL Server Management Studio, con una identidad de Azure AD que tenga al menos permiso para MODIFICAR CUALQUIER USUARIO. Ejecute el siguiente T-SQL: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Conceda a la identidad administrada de Data Factory los permisos necesarios, tal como lo haría normalmente para los usuarios de SQL y otros usuarios. Ejecute el código siguiente: Para más opciones, consulte [este documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. En Azure Data Factory, configure un servicio vinculado de Azure SQL Database.

**Ejemplo**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea obtener una lista completa de secciones y propiedades disponibles para definir los conjuntos de datos, consulte [Conjuntos de datos](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Azure SQL Database.

Las siguientes propiedades son compatibles para copiar datos dese y a Azure SQL Database:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del conjunto de datos debe establecerse en **AzureSqlTable**. | Sí |
| tableName | Nombre de la tabla o vista de la base de datos de Azure SQL a la que hace referencia el servicio vinculado. | No para el origen, sí para el receptor |

#### <a name="dataset-properties-example"></a>Ejemplo de propiedades de un conjunto de datos

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte [Canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que el origen y el receptor de Azure SQL Database admiten.

### <a name="azure-sql-database-as-the-source"></a>Azure SQL Database como origen

Para copiar datos desde Azure SQL Database, se admiten las siguientes propiedades en la sección de **origen** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad **type** del origen de la actividad de copia debe establecerse en **AzureSqlSource**. Todavía se admite el tipo "SqlSource" para la compatibilidad con versiones anteriores. | Sí |
| sqlReaderQuery | Esta propiedad usa la consulta SQL personalizada para leer los datos. Un ejemplo es `select * from MyTable`. | Sin |
| sqlReaderStoredProcedureName | Nombre del procedimiento almacenado que lee datos de la tabla de origen. La última instrucción SQL debe ser una instrucción SELECT del procedimiento almacenado. | Sin |
| storedProcedureParameters | Parámetros del procedimiento almacenado.<br/>Los valores permitidos son pares de nombre o valor. Los nombres y las mayúsculas y minúsculas de los parámetros tienen que coincidir con las mismas características de los parámetros de procedimiento almacenado. | Sin |

**Puntos a tener en cuenta:**

- Si se especifica **sqlReaderQuery** para **AzureSqlSource**, la actividad de copia ejecuta la consulta en el origen de Azure SQL Database para obtener los datos. También puede indicar un procedimiento almacenado mediante la definición de **sqlReaderStoredProcedureName** y **storedProcedureParameters** si el procedimiento almacenado adopta parámetros.
- Si no se especifican **sqlReaderQuery** ni **sqlReaderStoredProcedureName**, se usan las columnas que se definen en la sección "structure" del JSON del conjunto de datos para construir una consulta. La consulta `select column1, column2 from mytable` se ejecuta en Azure SQL Database. Si la definición del conjunto de datos no tiene la sección "structure", se seleccionan todas las columnas de la tabla.

#### <a name="sql-query-example"></a>Ejemplo de consulta SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Ejemplo de procedimiento almacenado

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definición del procedimiento almacenado

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Azure SQL Database como receptor

> [!TIP]
> Más información sobre los comportamientos de escritura, las configuraciones y los procedimientos recomendados que se admiten en [Procedimiento recomendado para cargar datos en Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Para copiar datos en Azure SQL Database, se admiten las siguientes propiedades en la sección de **receptor** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad **type** del receptor de la actividad de copia debe establecerse en **AzureSqlSink**. Todavía se admite el tipo "SqlSink" para la compatibilidad con versiones anteriores. | Sí |
| writeBatchSize | Número de filas que se va a insertar en la tabla SQL *por lote*.<br/> El valor que se permite es un **entero** (número de filas). De forma predeterminada, Azure Data Factory determina dinámicamente el tamaño adecuado del lote en función del tamaño de fila. | Sin |
| writeBatchTimeout | Tiempo que se concede a la operación de inserción por lotes para que finalice antes de que se agote el tiempo de espera.<br/> El valor permitido es **intervalo de tiempo**. Un ejemplo es "00:30:00" (30 minutos). | Sin |
| preCopyScript | Especifique una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en Azure SQL Database. Solo se invoca una vez por cada copia que se ejecuta. Esta propiedad se usa para limpiar los datos cargados previamente. | Sin |
| sqlWriterStoredProcedureName | El nombre del procedimiento almacenado que define cómo se aplican los datos de origen en una tabla de destino. <br/>Este procedimiento almacenado se *invoca por lote*. Para las operaciones que solo se ejecuta una vez y que no tiene nada que ver con los datos de origen, como por ejemplo, eliminar o truncar, use la propiedad `preCopyScript`. | Sin |
| storedProcedureTableTypeParameterName |Nombre del parámetro del tipo de tabla especificado en el procedimiento almacenado.  |Sin |
| sqlWriterTableType |Nombre del tipo de tabla que se usará en el procedimiento almacenado. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |Sin |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son pares de nombre y valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. | Sin |

**Ejemplo 1: Anexión de datos**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Ejemplo 2: Invocación de un procedimiento almacenado durante la copia**

Para más información, vea [Invocación del procedimiento almacenado desde el receptor de SQL ](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Procedimiento recomendado para cargar datos en Azure SQL Database

Al copiar datos en Azure SQL Database, puede requerir un comportamiento de escritura diferente:

- [Anexar](#append-data): mis datos de origen solo tienen registros nuevos.
- [Actualización e inserción](#upsert-data): mis datos de origen tienen inserciones y actualizaciones.
- [Sobrescribir](#overwrite-the-entire-table): quiero recargar una tabla de dimensiones completa cada vez.
- [Escribir con lógica personalizada](#write-data-with-custom-logic): necesito un procesamiento adicional antes de la inserción final en la tabla de destino.

Consulte las secciones correspondientes sobre cómo configurar en Azure Data Factory y los procedimientos recomendados.

### <a name="append-data"></a>Anexión de datos

La anexión de datos es el comportamiento predeterminado de este conector de receptor de Azure SQL Database. Azure Data Factory realiza una inserción masiva para escribir en la tabla de forma eficaz. Puede configurar el origen y el receptor según corresponda en la actividad de copia.

### <a name="upsert-data"></a>Actualización e inserción de datos

**Opción 1:** Cuando tenga una gran cantidad de datos que copiar, use el siguiente enfoque para realizar una operación upsert: 

- En primer lugar, use una [tabla temporal con ámbito de base de datos](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) para cargar de forma masiva todos los registros mediante la actividad de copia. Dado que las operaciones en tablas temporales con ámbito de base de datos no se registran, puede cargar millones de registros en segundos.
- Ejecute una actividad de procedimiento almacenado en Azure Data Factory para aplicar una instrucción [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o INSERT/UPDATE. Use la tabla temporal como el origen para realizar todas las actualizaciones o inserciones como una sola transacción. De este modo, se reduce el número de recorridos de ida y vuelta y de operaciones de registro. Al final de la actividad de procedimiento almacenado, se puede truncar la tabla temporal para que esté lista para el siguiente ciclo de operaciones upsert.

Por ejemplo, en Azure Data Factory, puede crear una canalización con una **actividad de copia** encadenada con una **actividad de procedimiento almacenado**. La primera copia datos desde el almacén de origen en una tabla temporal de Azure SQL Database, por ejemplo, **##UpsertTempTable**, como el nombre de la tabla del conjunto de datos. Después, la segunda invoca un procedimiento almacenado para combinar los datos de origen de la tabla temporal en la tabla de destino y limpiar la tabla temporal.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

En la base de datos, defina un procedimiento almacenado con la lógica MERGE, como en el ejemplo siguiente, al que se señala desde la actividad de procedimiento almacenado anterior. Suponga que el destino es la tabla **Marketing** con tres columnas: **ProfileID**, **State** y **Category**. Realice la operación upsert de acuerdo con la columna **ProfileID**.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opción 2:** También puede optar por [invocar un procedimiento almacenado dentro de una actividad de copia](#invoke-a-stored-procedure-from-a-sql-sink). En este enfoque se ejecuta cada fila en la tabla de origen en lugar de usar la inserción masiva como el método predeterminado de la actividad de copia, que no es adecuado para las operaciones upsert a gran escala.

### <a name="overwrite-the-entire-table"></a>Sobrescritura de toda la tabla

Puede configurar la propiedad **preCopyScript** en un receptor de la actividad de copia. En este caso, para cada actividad de copia que ejecute, Azure Data Factory ejecuta primero el script. Después, ejecuta la copia para insertar los datos. Por ejemplo, para sobrescribir toda la tabla con los datos más recientes, especifique un script para eliminar primero todos los registros antes de realizar la carga masiva de los nuevos datos desde el origen.

### <a name="write-data-with-custom-logic"></a>Escritura de datos con lógica personalizada

Los pasos necesarios para escribir datos con lógica personalizada son similares a los que se describen en la sección [Actualización e inserción de datos](#upsert-data). Si necesita aplicar un procesamiento adicional antes de la inserción final de los datos de origen en la tabla de destino, a gran escala, puede llevar a cabo una de estas dos acciones:

- Cargar los datos en una tabla temporal con ámbito de base de dato y luego invocar un procedimiento almacenado. 
- Invocar un procedimiento almacenado durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocación del procedimiento almacenado desde el receptor de SQL

Al copiar datos en Azure SQL Database, también se puede configurar e invocar un procedimiento almacenado especificado por el usuario con parámetros adicionales. La característica de procedimiento almacenado aprovecha los [parámetros con valores de tabla](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Al invocar un procedimiento almacenado, se procesan los datos fila por fila en lugar de mediante una operación masiva, que no se recomienda para la copia a gran escala. Más información en [Procedimiento recomendado para cargar datos en Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Cuando los mecanismos de copia integrados no prestan el servicio, se puede usar un procedimiento almacenado. Por ejemplo, si quiere aplicar un procesamiento adicional antes de la inserción final de los datos de origen en la tabla de destino. Otros ejemplos de procesamiento adicional son cuando quiere combinar columnas, buscar valores adicionales e insertar datos en más de una tabla.

En el ejemplo siguiente se muestra cómo usar un procedimiento almacenado para realizar una operación upsert en una tabla de Azure SQL Database. Supongamos que los datos de entrada y la tabla **Marketing** del receptor tienen tres columnas: **ProfileID**, **State** y **Category**. Realice una operación UPSERT en función de la columna **ProfileID** y aplíquela solo a una categoría específica llamada "ProductA".

1. En la base de datos, defina el tipo de tabla con el mismo nombre que **sqlWriterTableType**. El esquema del tipo de tabla es el mismo que el que devuelven los datos de entrada.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL，
        [Category] [varchar](256) NOT NULL
    )
    ```

2. En la base de datos, defina el procedimiento almacenado con el mismo nombre que **SqlWriterStoredProcedureName**. Dicho procedimiento administra los datos de entrada del origen especificado y los combina en la tabla de salida. El nombre del parámetro del tipo de tabla del procedimiento almacenado es el mismo que el de **tableName** que se ha definido en el conjunto de datos.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. En Azure Data Factory, defina la sección del **receptor SQL** en la actividad de copia como se indica a continuación:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "SqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "SqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Propiedades de Asignación de instancias de Data Flow

Obtenga información detallada de la [transformación de origen](data-flow-source.md) y la [transformación de receptor](data-flow-sink.md) en la asignación de flujo de datos.

## <a name="data-type-mapping-for-azure-sql-database"></a>Asignación de tipos de Azure SQL Database

Al copiar datos desde o a Azure SQL Database, se utilizan las siguientes asignaciones de tipos de datos de Azure SQL Database en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para más información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo de datos de Azure SQL Database | Tipo de datos provisionales de Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Para los tipos de datos que se asignan al tipo decimal provisional, Azure Data Factory actualmente admite una precisión de hasta 28. Si tiene datos con una precisión mayor de 28, considere la posibilidad de convertirlos en una cadena de consulta SQL.

## <a name="next-steps"></a>Pasos siguientes
Para ver una lista de los almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores, consulte los [formatos y almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats).
