---
title: Servicios vinculados en Azure Data Factory | Microsoft Docs
description: Obtenga información sobre los servicios vinculados en Data Factory. Los servicios vinculados vinculan almacenes de datos o servicios de proceso a una factoría de datos.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: shlo
ms.openlocfilehash: ba2041495e1e3c63ee322a0b748753ad6cb68914
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64870138"
---
# <a name="linked-services-in-azure-data-factory"></a>Servicios vinculados en Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-create-datasets.md)
> * [Versión actual](concepts-datasets-linked-services.md)

En este artículo se describe qué son los servicios vinculados, cómo se definen en formato JSON y cómo se usan en canalizaciones de Azure Data Factory.

Si no está familiarizado con Data Factory, consulte [Introducción a Azure Data Factory](introduction.md) para obtener información general.

## <a name="overview"></a>Información general
Una factoría de datos puede tener una o más canalizaciones. Una **canalización** es una agrupación lógica de **actividades** que realizan una tarea. Las actividades de una canalización definen las acciones que se van a realizar en los datos. Por ejemplo, puede usar una actividad de copia para copiar datos de un servidor SQL Server local en una instancia de Azure Blob Storage. Después, podría usar una actividad de Hive que ejecute un script de Hive en un clúster de Azure HDInsight para procesar datos de Blob Storage con el fin de generar datos de salida. Finalmente, podría usar segunda actividad de copia para copiar los datos de salida en una instancia de Azure SQL Data Warehouse en función de qué soluciones de generación de informes de inteligencia empresarial (BI) estén integradas. Para más información sobre canalizaciones y actividades, consulte el artículo [Pipelines and activities](concepts-pipelines-activities.md) (Canalizaciones y actividades) en Azure Data Factory.

Ahora, un **conjunto de datos** es una vista con nombre de los datos que simplemente apunta o hace referencia a los datos que desea usar en sus **actividades** como entradas y salidas.

Antes de crear un conjunto de datos, debe crear un **servicio vinculado** para vincular su almacén de datos a la factoría de datos. Los servicios vinculados son muy similares a las cadenas de conexión que definen la información de conexión necesaria para que Data Factory se conecte a recursos externos. Considérelos de esta forma: el conjunto de datos representa la estructura de los datos dentro de los almacenes de datos vinculados y el servicio vinculado define la conexión al origen de datos. Por ejemplo, un servicio vinculado Azure Storage vincula una cuenta de almacenamiento a la factoría de datos. Un conjunto de datos de blobs de Azure representa el contenedor de blobs y la carpeta dentro de esa cuenta de Azure Storage que contiene los blobs de entrada que se van a procesar.

Este es un escenario de ejemplo. Para copiar datos de Blob Storage a una base de datos SQL, creará dos servicios vinculados: Microsoft Azure Storage y Azure SQL Database. Después, creará dos conjuntos de datos: el conjunto de datos de un blob de Azure (que hace referencia al servicio vinculado Azure Storage) y el conjunto de datos de Azure SQL Table (que hace referencia al servicio vinculado Azure SQL Database). Los servicios vinculados de Azure Storage y Azure SQL Database contienen cadenas de conexión que Data Factory usa en tiempo de ejecución para conectarse a las instancias de Azure Storage y Azure SQL Database, respectivamente. El conjunto de datos Azure Blob especifica el contenedor de blobs y la carpeta de blobs que contiene los blobs de entrada de Blob Storage. El conjunto de datos Azure SQL Table especifica la tabla de SQL de la base de datos SQL en la que se van a copiar los datos.

En el siguiente diagrama se muestra la relación entre la canalización, la actividad, el conjunto de datos y el servicio vinculado en Data Factory:

![Relación entre la canalización, la actividad, el conjunto de datos y los servicios vinculados](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>Servicio vinculado JSON
Un servicio vinculado de Data Factory se define con formato JSON de la manera siguiente:

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

La tabla siguiente describe las propiedades del JSON anterior:

Propiedad | DESCRIPCIÓN | Obligatorio |
-------- | ----------- | -------- |
Nombre | Nombre del servicio vinculado. Consulte [Azure Data Factory - Naming rules](naming-rules.md) (Azure Data Factory: reglas de nomenclatura). |  Sí |
Tipo | Tipo de servicio vinculado. Por ejemplo:  AzureStorage (almacén de datos) o AzureBatch (proceso). Vea la descripción de typeProperties. | Sí |
typeProperties | Las propiedades de tipo son diferentes para cada almacén de datos o proceso. <br/><br/> Para los tipos de almacenes de datos compatibles y sus propiedades de tipo, consulte la tabla [Tipo de conjunto de datos](concepts-datasets-linked-services.md#dataset-type) en este artículo. Vaya al artículo del conector del almacén de datos para obtener información acerca de las propiedades de tipo específicas de un almacén de datos. <br/><br/> Para los tipos de procesos compatibles y sus propiedades de tipo, vea [Servicios de proceso vinculados](compute-linked-services.md). | Sí |
connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Puede usar los entornos Integration Runtime (autohospedado) (si el almacén de datos se encuentra en una red privada) o Azure Integration Runtime. Si no se especifica, se usará Azure Integration Runtime. | Sin

## <a name="linked-service-example"></a>Ejemplo de servicio vinculado
El siguiente servicio vinculado no es un servicio vinculado de Azure Storage. Tenga en cuenta que el tipo está establecido en AzureStorage. Las propiedades de tipo del servicio vinculado de Azure Storage incluyen una cadena de conexión. El servicio Data Factory utiliza esta cadena de conexión para conectarse al almacén de datos en tiempo de ejecución.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="create-linked-services"></a>Crear servicios vinculados
Puede crear servicios vinculados mediante una de estas herramientas o SDK: [API de .NET](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [API REST](quickstart-create-data-factory-rest-api.md), plantilla de Azure Resource Manager y Azure Portal.

## <a name="data-store-linked-services"></a>Servicios vinculados con almacenes de datos
Puede encontrar información sobre la conexión con almacenes de datos en nuestra lista de [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats). Consulte la lista para conocer propiedades de conexión específicas necesarias para los diferentes almacenes.

## <a name="compute-linked-services"></a>Servicios vinculados de proceso
Consulte [Entornos de proceso compatibles](compute-linked-services.md) para obtener detalles sobre los diferentes entornos de proceso a los que puede conectarse desde la factoría de datos, así como las diferentes configuraciones.

## <a name="next-steps"></a>Pasos siguientes
Consulte el siguiente tutorial para obtener instrucciones paso a paso sobre cómo crear canalizaciones y conjuntos de datos con una de estas herramientas o SDK.

- [Inicio rápido: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Crear una factoría de datos mediante .NET)
- [Inicio rápido: create a data factory using PowerShell](quickstart-create-data-factory-powershell.md) (Crear una factoría de datos mediante PowerShell)
- [Inicio rápido: create a data factory using REST API](quickstart-create-data-factory-rest-api.md) (Crear una factoría de datos mediante la API de REST)
- [Inicio rápido: creación de una factoría de datos mediante Azure Portal](quickstart-create-data-factory-portal.md)
