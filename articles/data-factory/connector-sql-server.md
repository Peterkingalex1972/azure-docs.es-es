---
title: Copia de datos con SQL Server como origen o destino mediante Azure Data Factory | Microsoft Docs
description: Aprenda a trasladar los datos hacia y desde una base de datos SQL Server en un entorno local o en una máquina virtual de Azure mediante Azure Data Factory.
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
ms.openlocfilehash: f5ddd9928194c477d8f8b6f4c9569a8fe58f39d3
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967380"
---
# <a name="copy-data-to-and-from-sql-server-by-using-azure-data-factory"></a>Copia de datos con SQL Server como origen o destino mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión de Azure Data Factory que está usando:"]
> * [Versión 1](v1/data-factory-sqlserver-connector.md)
> * [Versión actual](connector-sql-server.md)

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos con una base de datos SQL Server como origen o destino. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que incluye información general de esta actividad.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos desde una base de datos de SQL Server en cualquier almacén de datos receptor compatible. O bien puede copiar datos desde cualquier almacén de datos de origen compatible a una base de datos de SQL Server. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector SQL Server admite las siguientes funcionalidades:

- SQL Server versión 2016, 2014, 2012, 2008 R2, 2008 y 2005.
- La copia de datos con autenticación de SQL o Windows.
- Como origen, la recuperación de datos mediante una consulta SQL o un procedimiento almacenado.
- Como receptor, anexar datos a una tabla de destino o invocar un procedimiento almacenado con lógica personalizada durante la copia.

No se admite la base de datos local LocalDB de [SQL Server Express](https://docs.microsoft.com/sql/database-engine/configure-windows/sql-server-express-localdb?view=sql-server-2017).

>[!NOTE]
>Actualmente, este conector no admite [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=sql-server-2017) de SQL Server. Como solución alternativa, puede usar un [conector ODBC genérico](connector-odbc.md) y un controlador ODBC de SQL Server. Siga [esta guía](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=sql-server-2017) con configuraciones de cadena de conexión y descarga del controlador ODBC.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Primeros pasos

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de base de datos SQL Server.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las propiedades siguientes son compatibles con el servicio vinculado SQL Server:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type se debe establecer en: **SqlServer**. | Sí |
| connectionString |Especifique la información de **connectionString** necesaria para conectarse a la base de datos SQL Server mediante autenticación de SQL o autenticación de Windows. Consulte los ejemplos siguientes.<br/>Marque este campo como **SecureString** para almacenarlo de forma segura en Azure Data Factory. También puede asignar una contraseña en Azure Key Vault. Si se trata de la autenticación de SQL, extraiga la configuración `password` de la cadena de conexión. Para obtener más información, vea el ejemplo de JSON debajo de la tabla y consulte el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md). |Sí |
| userName |Especifique el nombre de usuario si usa la autenticación de Windows. Un ejemplo es **domainname\\username**. |Sin |
| password |Especifique la contraseña de la cuenta de usuario que se especificó para el nombre de usuario. Marque este campo como **SecureString** para almacenarlo de forma segura en Azure Data Factory. O bien puede [hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). |Sin |
| connectVia | Este [entorno de ejecución de integración](concepts-integration-runtime.md) se usa para conectarse al almacén de datos. Obtenga más información en la sección [Requisitos previos](#prerequisites). Si no se especifica, se usa el valor predeterminado de Azure Integration Runtime. |Sin |

>[!TIP]
>Si recibió un error con el código de error "UserErrorFailedToConnectToSqlServer" y un mensaje parecido a "The session limit for the database is XXX and has been reached" (El límite de sesión de la base de datos es XXX y ya se ha alcanzado), agregue `Pooling=false` a la cadena de conexión e inténtelo de nuevo.

**Ejemplo 1: Uso de la autenticación de SQL**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo 2: Uso de la autenticación de SQL con una contraseña en Azure Key Vault**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;"
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

**Ejemplo 3: Uso de la autenticación de Windows**

```json
{
    "name": "SqlServerLinkedService",
    "properties": {
        "type": "SqlServer",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Data Source=<servername>\\<instance name if using named instance>;Initial Catalog=<databasename>;Integrated Security=True;"
            },
            "userName": "<domain\\username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre conjuntos de datos. En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de SQL Server.

Las siguientes propiedades son compatibles para copiar datos con una base de datos SQL Server como origen y destino:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos se debe establecer en **SqlServerTable**. | Sí |
| tableName |Esta propiedad es el nombre de la tabla o vista de la instancia de base de datos SQL Server a la que hace referencia el servicio vinculado. | No para el origen, sí para el receptor |

**Ejemplo**

```json
{
    "name": "SQLServerDataset",
    "properties":
    {
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "<SQL Server linked service name>",
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, vea el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admiten el origen y receptor de SQL Server.

### <a name="sql-server-as-a-source"></a>SQL Server como origen

Si va a copiar datos desde SQL Server, establezca el tipo de origen de la actividad de copia en **SqlSource**. En la sección source de la actividad de copia se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en **SqlSource**. | Sí |
| sqlReaderQuery |Use la consulta SQL personalizada para leer los datos. Un ejemplo es `select * from MyTable`. |Sin |
| sqlReaderStoredProcedureName |Esta propiedad es el nombre del procedimiento almacenado que lee datos de la tabla de origen. La última instrucción SQL debe ser una instrucción SELECT del procedimiento almacenado. |Sin |
| storedProcedureParameters |Estos parámetros son para el procedimiento almacenado.<br/>Los valores permitidos son pares de nombre o valor. Los nombres y las mayúsculas y minúsculas de los parámetros tienen que coincidir con las mismas características de los parámetros de procedimiento almacenado. |Sin |

**Puntos a tener en cuenta:**

- Si se especifica **sqlReaderQuery** para **SqlSource**, la actividad de copia ejecuta la consulta en el origen de SQL Server para obtener los datos. También puede indicar un procedimiento almacenado mediante la definición de **sqlReaderStoredProcedureName** y **storedProcedureParameters** si el procedimiento almacenado adopta parámetros.
- Si no se especifican **sqlReaderQuery** ni **sqlReaderStoredProcedureName**, se usan las columnas que se definen en la sección "structure" del JSON del conjunto de datos para construir una consulta. La consulta `select column1, column2 from mytable` se ejecuta en la instancia de SQL Server. Si la definición del conjunto de datos no tiene la sección "structure", se seleccionan todas las columnas de la tabla.

**Ejemplo: Uso de la consulta SQL**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Ejemplo: Uso de un procedimiento almacenado**

```json
"activities":[
    {
        "name": "CopyFromSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Server input dataset name>",
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
                "type": "SqlSource",
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

**Definición del procedimiento almacenado**

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

### <a name="sql-server-as-a-sink"></a>SQL Server como receptor

> [!TIP]
> Más información sobre los comportamientos de escritura, las configuraciones y los procedimientos recomendados que se admiten en [Procedimiento recomendado para cargar datos en SQL Server](#best-practice-for-loading-data-into-sql-server).

Si va a copiar datos en SQL Server, establezca el tipo de receptor de la actividad de copia en **SqlSink**. La sección sink de la actividad de copia admite las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del receptor de la actividad de copia debe establecerse en **SqlSink**. | Sí |
| writeBatchSize |Número de filas que se va a insertar en la tabla SQL *por lote*.<br/>Los valores permitidos son enteros para el número de filas. De forma predeterminada, Azure Data Factory determina dinámicamente el tamaño adecuado del lote en función del tamaño de fila. |Sin |
| writeBatchTimeout |Esta propiedad especifica el tiempo de espera para que la operación de inserción por lotes se complete antes de que se agote el tiempo de espera.<br/>Los valores permitidos son para el intervalo de tiempo. Un ejemplo es "00:30:00" para 30 minutos. |Sin |
| preCopyScript |Esta propiedad especifica una consulta SQL para que la actividad de copia se ejecute antes de escribir datos en SQL Server. Solo se invoca una vez por cada copia que se ejecuta. Puede usar esta propiedad para limpiar los datos cargados previamente. |Sin |
| sqlWriterStoredProcedureName | El nombre del procedimiento almacenado que define cómo se aplican los datos de origen en una tabla de destino. <br/>Este procedimiento almacenado se *invoca por lote*. Para las operaciones que solo se ejecuta una vez y que no tiene nada que ver con los datos de origen, como por ejemplo, eliminar o truncar, use la propiedad `preCopyScript`. | Sin |
| storedProcedureTableTypeParameterName |Nombre del parámetro del tipo de tabla especificado en el procedimiento almacenado.  |Sin |
| sqlWriterTableType |Nombre del tipo de tabla que se usará en el procedimiento almacenado. La actividad de copia dispone que los datos que se mueven estén disponibles en una tabla temporal con este tipo de tabla. El código de procedimiento almacenado puede combinar los datos copiados con datos existentes. |Sin |
| storedProcedureParameters |Parámetros del procedimiento almacenado.<br/>Los valores permitidos son pares de nombre y valor. Los nombres y las mayúsculas y minúsculas de los parámetros deben coincidir con las mismas características de los parámetros de procedimiento almacenado. | Sin |

**Ejemplo 1: Anexión de datos**

```json
"activities":[
    {
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
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
        "name": "CopyToSQLServer",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<SQL Server output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
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

## <a name="best-practice-for-loading-data-into-sql-server"></a>Procedimiento recomendado para cargar datos en SQL Server

Al copiar datos en SQL Server, puede requerir un comportamiento de escritura diferente:

- [Anexión](#append-data): mis datos de origen solo tienen registros nuevos.
- [Actualización e inserción](#upsert-data): mis datos de origen tienen inserciones y actualizaciones.
- [Sobrescritura](#overwrite-the-entire-table): quiero volver a cargar toda la tabla de dimensiones cada vez.
- [Escritura con lógica personalizada](#write-data-with-custom-logic): necesito un procesamiento adicional antes de la inserción final en la tabla de destino.

Consulte las secciones correspondientes sobre cómo configurar estas operaciones en Azure Data Factory y los procedimientos recomendados.

### <a name="append-data"></a>Anexión de datos

La anexión de datos es el comportamiento predeterminado de este conector de receptor de SQL Server. Azure Data Factory realiza una inserción masiva para escribir en la tabla de forma eficaz. Puede configurar el origen y el receptor según corresponda en la actividad de copia.

### <a name="upsert-data"></a>Actualización e inserción de datos

**Opción 1:** Cuando tenga una gran cantidad de datos que copiar, use el siguiente procedimiento para realizar una operación upsert: 

- En primer lugar, use una [tabla temporal](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) para cargar de forma masiva todos los registros mediante la actividad de copia. Dado que las operaciones en tablas temporales no se registran, puede cargar millones de registros en segundos.
- Ejecute una actividad de procedimiento almacenado en Azure Data Factory para aplicar una instrucción [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o INSERT/UPDATE. Use la tabla temporal como origen para realizar todas las actualizaciones o inserciones como una sola transacción. De este modo, se reduce el número de recorridos de ida y vuelta y operaciones de registro. Al final de la actividad de procedimiento almacenado, se puede truncar la tabla temporal para que esté lista para el siguiente ciclo de operaciones upsert.

Por ejemplo, en Azure Data Factory, puede crear una canalización con una **actividad de copia** encadenada con una **actividad de procedimiento almacenado**. La primera copia datos desde el almacén de origen en una tabla temporal; por ejemplo, **##UpsertTempTable** como nombre de la tabla del conjunto de datos. Después, la segunda invoca un procedimiento almacenado para combinar los datos de origen de la tabla temporal en la tabla de destino y limpiar la tabla temporal.

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

Puede configurar la propiedad **preCopyScript** en un receptor de la actividad de copia. En este caso, Azure Data Factory ejecuta primero el script para cada actividad de copia que ejecute. Después, ejecuta la copia para insertar los datos. Por ejemplo, para sobrescribir toda la tabla con los datos más recientes, especifique un script para eliminar primero todos los registros antes de realizar la carga masiva de los nuevos datos desde el origen.

### <a name="write-data-with-custom-logic"></a>Escritura de datos con lógica personalizada

Los pasos necesarios para escribir datos con lógica personalizada son similares a los que se describen en la sección [Actualización e inserción de datos](#upsert-data). Si necesita aplicar un procesamiento adicional antes de la inserción final de los datos de origen en la tabla de destino, a gran escala, puede llevar a cabo una de estas dos acciones: 

- Cargue los datos en una tabla temporal y luego invoque un procedimiento almacenado. 
- Invoque un procedimiento almacenado durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Invocación del procedimiento almacenado desde el receptor de SQL

Al copiar datos en una base de datos SQL Server, también se puede configurar e invocar un procedimiento almacenado especificado por el usuario con parámetros adicionales. La característica de procedimiento almacenado aprovecha los [parámetros con valores de tabla](https://msdn.microsoft.com/library/bb675163.aspx).

> [!TIP]
> Al invocar un procedimiento almacenado, se procesan los datos fila por fila en lugar de mediante una operación masiva, que no se recomienda para la copia a gran escala. Obtenga más información en el [Procedimiento recomendado para cargar datos en SQL Server](#best-practice-for-loading-data-into-sql-server).

Cuando los mecanismos de copia integrados no prestan el servicio, se puede usar un procedimiento almacenado. Por ejemplo, si quiere aplicar un procesamiento adicional antes de la inserción final de los datos de origen en la tabla de destino. Otros ejemplos de procesamiento adicional son cuando quiere combinar columnas, buscar valores adicionales e insertar datos en más de una tabla.

En el ejemplo siguiente se muestra cómo usar un procedimiento almacenado para realizar una operación UPSERT en una tabla en la base de datos SQL Server. Supongamos que los datos de entrada y la tabla **Marketing** del receptor tienen tres columnas: **ProfileID**, **State** y **Category**. Realice una operación UPSERT en función de la columna **ProfileID** y aplíquela solo a una categoría específica llamada "ProductA".

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
        "type": "SqlSink",
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

## <a name="data-type-mapping-for-sql-server"></a>Asignación de tipos de datos para SQL Server

Cuando copia datos con SQL Server como origen y destino, se usan las siguientes asignaciones de tipos de datos de SQL Server para los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para más información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipos de datos de SQL Server | Tipo de datos provisionales de Azure Data Factory |
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
| tinyint |Int16 |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Para los tipos de datos que se asignan al tipo decimal provisional, Azure Data Factory actualmente admite una precisión de hasta 28. Si tiene datos que requieren una precisión mayor que 28, considere la posibilidad de convertir a una cadena en una consulta SQL.

## <a name="troubleshoot-connection-issues"></a>Solución de problemas de conexión

1. Configure su instancia de SQL Server para que acepte conexiones remotas. Inicie **SQL Server Management Studio**, haga clic con el botón derecho en **servidor** y seleccione **Propiedades**. Seleccione **Conexiones** en la lista y marque la casilla **Permitir conexiones remotas con este servidor**.

    ![Habilitar conexiones remotas](media/copy-data-to-from-sql-server/AllowRemoteConnections.png)

    Consulte los pasos detallados en el artículo [Configurar la opción de configuración del servidor Acceso remoto](https://msdn.microsoft.com/library/ms191464.aspx).

2. Inicie el **Administrador de configuración de SQL Server**. Expanda **Configuración de red de SQL Server** para la instancia que desee y seleccione **Protocols for MSSQLSERVER** (Protocolos para MSSQLSERVER). Los protocolos aparecen en el panel derecho. Para habilitar TCP/IP, haga clic con el botón derecho en **TCP/IP** y seleccione **Habilitar**.

    ![Habilitar TCP/IP](./media/copy-data-to-from-sql-server/EnableTCPProptocol.png)

    Para obtener más información y maneras alternativas de habilitar el protocolo TCP/IP, consulte [Habilitar o deshabilitar un protocolo de red de servidor](https://msdn.microsoft.com/library/ms191294.aspx).

3. En la misma ventana, haga doble clic en **TCP/IP** para abrir la ventana **TCP/IP Properties** (Propiedades de TCP/IP).
4. Cambie a la pestaña **Direcciones IP** . Desplácese hacia abajo hasta la sección **IPAll**. Escriba el **puerto TCP**. El valor predeterminado es **1433**.
5. Cree una **regla del Firewall de Windows** en la máquina para permitir el tráfico entrante a través de este puerto. 
6. **Verifique la conexión**: use SQL Server Management Studio en una máquina diferente para conectarse a SQL Server con un nombre completo. Un ejemplo es `"<machine>.<domain>.corp.<company>.com,1433"`.

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md##supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
