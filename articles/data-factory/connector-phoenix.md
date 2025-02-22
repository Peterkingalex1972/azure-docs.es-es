---
title: Copia de datos de Phoenix con Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo copiar datos de Phoenix en almacenes de datos receptores compatibles a través de una actividad de copia de una canalización de Azure Data Factory.
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
ms.openlocfilehash: 01f764d96eacdc94fd90b4f695c4b774a6ded5c5
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967448"
---
# <a name="copy-data-from-phoenix-using-azure-data-factory"></a>Copiar datos de Phoenix con Azure Data Factory 

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos de Phoenix. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar datos de Phoenix en cualquier almacén de datos de receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Azure Data Factory proporciona un controlador integrado para habilitar la conectividad. Por lo tanto, no es necesario instalar manualmente ningún controlador mediante este conector.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Phoenix.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Phoenix:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **Phoenix** | Sí |
| host | Dirección IP o nombre de host del servidor de Phoenix. (es, decir 192.168.222.160)  | Sí |
| port | Puerto TCP que el servidor de Phoenix utiliza para escuchar las conexiones del cliente. El valor predeterminado es 8765. Si se conecta a Azure HDInsights, especifique el puerto 443. | Sin |
| httpPath | Dirección URL parcial correspondiente al servidor de Phoenix. (es decir, /gateway/sandbox/phoenix/version). Especifique `/hbasephoenix0` si se usa el clúster de HDInsights.  | Sin |
| authenticationType | Mecanismo de autenticación utilizado para conectarse al servidor de Phoenix. <br/>Los valores permitidos son: **Anonymous**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sí |
| username | Nombre de usuario que se usa para conectarse al servidor de Phoenix.  | Sin |
| password | Contraseña que corresponde al nombre de usuario. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sin |
| enableSsl | Especifica si las conexiones al servidor se cifran mediante SSL. El valor predeterminado es false.  | Sin |
| trustedCertPath | Ruta de acceso completa del archivo .pem que contiene certificados de CA de confianza para comprobar el servidor al conectarse a través de SSL. Esta propiedad solo puede establecerse al utilizar SSL en IR autohospedados. El valor predeterminado es el archivo cacerts.pem instalado con el IR.  | Sin |
| useSystemTrustStore | Especifica si se utiliza un certificado de CA del almacén de confianza del sistema o de un archivo PEM especificado. El valor predeterminado es false.  | Sin |
| allowHostNameCNMismatch | Especifica si se requiere que el nombre del certificado SSL emitido por una CA coincida con el nombre de host del servidor al conectarse a través de SSL. El valor predeterminado es false.  | Sin |
| allowSelfSignedServerCert | Especifica si se permiten los certificados autofirmados del servidor. El valor predeterminado es false.  | Sin |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Obtenga más información en la sección [Requisitos previos](#prerequisites). Si no se especifica, se usará Azure Integration Runtime. |Sin |

>[!NOTE]
>Si el clúster no es compatible con las sesiones rápidas, como HDInsight, agregue explícitamente el índice del nodo al final de la configuración de la ruta de acceso http; por ejemplo, especifique `/hbasephoenix0` en lugar de `/hbasephoenix`.

**Ejemplo:**

```json
{
    "name": "PhoenixLinkedService",
    "properties": {
        "type": "Phoenix",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "443",
            "httpPath" : "/hbasephoenix0",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades compatibles con el conjunto de datos de Phoenix.

Para copiar datos de Phoenix, establezca la propiedad type del conjunto de datos en **PhoenixObject**. Se admiten las siguientes propiedades:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **PhoenixObject** | Sí |
| tableName | Nombre de la tabla. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "PhoenixDataset",
    "properties": {
        "type": "PhoenixObject",
        "linkedServiceName": {
            "referenceName": "<Phoenix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el origen de Phoenix.

### <a name="phoenix-as-source"></a>Phoenix como origen

Para copiar datos de Phoenix, establezca el tipo de origen de la actividad de copia en **PhoenixSource**. Se admiten las siguientes propiedades en la sección **source** de la actividad de copia:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en: **PhoenixSource** | Sí |
| query | Use la consulta SQL personalizada para leer los datos. Por ejemplo: `"SELECT * FROM MyTable"`. | No (si se especifica "tableName" en el conjunto de datos) |

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromPhoenix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Phoenix input dataset name>",
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
                "type": "PhoenixSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
