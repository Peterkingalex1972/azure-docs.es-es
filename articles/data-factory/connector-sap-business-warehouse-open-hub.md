---
title: Copia de datos desde SAP Business Warehouse con Open Hub en Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo copiar datos desde SAP Business Warehouse (BW) con Open Hub a almacenes de datos receptores compatibles mediante una actividad de copia en una canalización de Azure Data Factory.
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
ms.openlocfilehash: e94c4f179174a3957aef8828687ebf1fbb299903
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967436"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Copia de datos desde SAP Business Warehouse con Open Hub en Azure Data Factory

En este artículo se explica el uso de la actividad de copia de Azure Data Factory para copiar datos desde una instancia de SAP Business Warehouse (BW) con Open Hub. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Puede copiar los datos desde SAP Business Warehouse con Open Hub en cualquier almacén de datos de receptores que se admita. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

Concretamente, este conector de Open Hub para SAP Business Warehouse admite:

- SAP Business Warehouse **versión 7.01 o posteriores (en una pila de paquetes de soporte técnico de SAP reciente lanzada después de 2015)** .
- Copiar datos con la tabla local del destino Open Hub que, de forma subyacente, puede ser DSO, InfoCube, MultiProvider, DataSource, etc.
- Copiar datos con la autenticación básica.
- Conectarse al servidor de aplicaciones.

## <a name="sap-bw-open-hub-integration"></a>Integración de Open Hub con SAP BW 

El [servicio de Open Hub para SAP BW](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) es una forma eficaz de extraer datos de SAP BW. En el siguiente diagrama se muestra uno de los flujos típicos que los clientes tienen en su sistema SAP, en cuyo caso, los datos fluyen desde SAP ECC -> PSA -> DSO -> Cube.

El destino Open Hub (OHD) para SAP BW define el destino al que se retransmitirán los datos de SAP. Todos los objetos compatibles con el proceso de transferencia de datos (DTP) de SAP pueden utilizarse como orígenes de datos de Open Hub; por ejemplo, DSO, InfoCube, DataSource, etc. Tipo de destino Open Hub: donde se almacenan los datos retransmitidos; puede tratarse de tablas de base de datos (locales o remotas) y de archivos planos. Este conector de Open Hube para SAP BW admite la copia de datos desde la tabla local de OHD a BW. Si usa otros tipos, puede conectarse directamente a la base de datos o al sistema de archivos con otros conectores.

![Open Hub para SAP BW](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Flujo de extracción diferencial

El conector Open Hub para ADF SAP BW ofrece dos propiedades opcionales: `excludeLastRequest` y `baseRequestId`, que se pueden usar para administrar la carga diferencial de Open Hub. 

- **excludeLastRequestId**: Si desea excluir los registros de la última solicitud. El valor predeterminado es true. 
- **baseRequestId**: El identificador de la solicitud para la carga diferencial. Una vez establecido, solo se recuperarán los datos con el identificador de solicitud mayor que el valor de esta propiedad. 

En general, la extracción de InfoProviders de SAP a Azure Data Factory (ADF) consta de 2 pasos: 

1. **Proceso de transferencia de datos (DTP) de SAP BW**: en este paso se copian los datos desde InfoProvider para SAP BW a una tabla de Open Hub para SAP BW. 

1. **Copia de datos ADF**: en este paso, el conector ADF lee la tabla de Open Hub. 

![Flujo de extracción diferencial](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

En el primer paso, se ejecuta un DTP. Cada ejecución crea un nuevo identificador de solicitud SAP. El identificador de solicitud se almacena en la tabla de Open Hub y, a continuación, se usa el conector de ADF para identificar la diferencia. Los dos pasos se ejecutan de forma asincrónica: SAP desencadena el DTP y la copia de datos de ADF se desencadena a través de ADF. 

De forma predeterminada, ADF no lee los flujos diferenciales más recientes de la tabla de Open Hub (la opción de "exclusión de última solicitud" se establece en true). De esta manera, los datos en ADF no están 100 % actualizados con los datos en la tabla de Open Hub (falta la última diferencia). A cambio, este procedimiento garantiza que no se pierden filas debido a la extracción asíncrona. Funciona correctamente incluso cuando el ADF lee la tabla de Open Hub mientras el DTP sigue escribiendo en la misma tabla. 

Por lo general, el Identificador de solicitud copiado máximo se almacena en la última ejecución del ADF en un almacén de datos de prueba (como Azure Blob en el diagrama anterior). Por lo tanto, ADF no lee la misma solicitud por segunda vez en la ejecución posterior. Mientras tanto, tenga en cuenta que los datos no se eliminan automáticamente de la tabla de Open Hub.

Para una correcta administración de la diferencia, no está permitido disponer de identificadores de solicitud de diferentes DTP en la misma tabla de Open Hub. Por lo tanto, no debe crear más de un DTP para cada destino de Open Hub (OHD). Cuando necesite la extracción completa y diferencial del mismo InfoProvider, debe crear dos OHD para el mismo InfoProvider. 

## <a name="prerequisites"></a>Requisitos previos

Para usar este conector de Open Hub para SAP Business Warehouse, necesita:

- Configurar un entorno de ejecución de integración autohospedado con la versión 3.13 o posterior. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información.

- Descargar el **[conector de SAP para .NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) de 64 bits** del sitio web de SAP e instalarlo en la máquina del IR autohospedado. Al instalarlo, en la ventana de los pasos de configuración opcionales, asegúrese de seleccionar la opción **Install Assemblies to GAC** (Instalar ensamblados en GAC) como se muestra en la imagen siguiente. 

    ![Instalación del conector de SAP para .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- El usuario de SAP utilizado en el conector para BW en Data Factory necesita tener los siguientes permisos: 

    - Autorización para RFC y SAP BW. 
    - Permisos para la actividad “Ejecutar” del objeto de autorización “S_SDSAUTH”.

- Crear un tipo de destino Open Hub para SAP como **tabla de base de datos** con la opción "Technical Key" (Clave técnica) seleccionada.  También se recomienda dejar desmarcada la opción de eliminación de datos de la tabla, aunque no es necesario. Utilice DTP (con ejecución directa o integración en la cadena de procesos existente) para enviar los datos desde el objeto de origen (como un cubo) elegido hasta la tabla de destino Open Hub.

## <a name="getting-started"></a>Introducción

> [!TIP]
>
> Para ver un tutorial del uso del conector de Open Hub de SAP BW, consulte [Carga de datos desde SAP Business Warehouse (BW) mediante Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de Open Hub para SAP Business Warehouse.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Open Hub para SAP Business Warehouse:

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **SapOpenHub** | Sí |
| server | Nombre del servidor en el que reside la instancia de SAP BW. | Sí |
| systemNumber | Número del sistema de SAP BW.<br/>Valor permitido: número decimal de dos dígitos que se representa en forma de cadena. | Sí |
| clientId | Identificador del cliente en el sistema SAP W.<br/>Valor permitido: número decimal de tres dígitos que se representa en forma de cadena. | Sí |
| language | Idioma que utiliza el sistema SAP. | No (el valor predeterminado es **EN**)|
| userName | Nombre del usuario que tiene acceso al servidor SAP. | Sí |
| password | Contraseña del usuario. Marque este campo como SecureString para almacenarlo de forma segura en Data Factory o [para hacer referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Tal y como se mencionó en los [requisitos previos](#prerequisites), se requiere un entorno Integration Runtime autohospedado. |Sí |

**Ejemplo:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Open Hub para SAP BW.

Para copiar datos desde y hacia Open Hub para SAP BW, establezca la propiedad type del conjunto de datos en **SapOpenHubTable**. Se admiten las siguientes propiedades.

| Propiedad | DESCRIPCIÓN | Obligatorio |
|:--- |:--- |:--- |
| Tipo | La propiedad type debe establecerse en **SapOpenHubTable**.  | Sí |
| openHubDestinationName | Nombre del destino Open Hub desde el que copiar los datos. | Sí |
| excludeLastRequest | Si desea excluir los registros de la última solicitud. | No (el valor predeterminado es **true**) |
| baseRequestId | El identificador de la solicitud para la carga diferencial. Una vez establecido, solo se recuperarán los datos con el identificador de solicitud **mayor que** el valor de esta propiedad.  | Sin |

>[!TIP]
>Si la tabla de Open Hub solo contiene los datos generados por un único identificador de solicitud y, por ejemplo, siempre hace una carga completa y sobrescribe los datos existentes en la tabla o solo ejecuta el DTP una vez por cada prueba, recuerde desactivar la opción "excludeLastRequest" para poder copiar los datos.

**Ejemplo:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen Open Hub para SAP BW.

### <a name="sap-bw-open-hub-as-source"></a>Open Hub para SAP BW como origen

Para copiar datos desde Open Hub para SAP BW, establezca el tipo de origen de la actividad de copia en **SapOpenHubSource**. No se necesitan propiedades específicas para type adicionales en la sección de **origen** de la actividad de copia.

Para acelerar la carga de datos, puede establecer [`parallelCopies`](copy-activity-performance.md#parallel-copy) en la actividad de copia para cargar datos desde Open Hub para SAP BW en paralelo. Por ejemplo, si establece `parallelCopies` en cuatro, Data Factory ejecutará simultáneamente cuatro llamadas de RFC, y cada llamada de RFC recuperará una parte de los datos de la tabla de Open Hub para SAP BW, particionada por el identificador de solicitud de DTP y por el identificador de paquete. Esto se aplica cuando la suma de identificadores únicos de solicitud de DTP y de paquete es superior al valor de `parallelCopies`.

**Ejemplo:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Asignación de tipos de datos en Open Hub para SAP BW

Al copiar datos desde Open Hub para SAP BW, se usan las siguientes asignaciones de tipos de datos de SAP BW en los tipos de datos provisionales de Azure Data Factory. Consulte el artículo sobre [asignaciones de tipos de datos y esquema](copy-activity-schema-and-type-mapping.md) para información sobre cómo la actividad de copia asigna el tipo de datos y el esquema de origen al receptor.

| Tipo ABAP para SAP | Tipo de datos provisionales de Data Factory |
|:--- |:--- |
| C (cadena) | Cadena |
| I (entero) | Int32 |
| F (flotante) | Double |
| D (fecha) | Cadena |
| T (hora) | Cadena |
| P (BCD empaquetado, moneda, decimal, cantidad) | Decimal |
| N (número) | Cadena |
| X (binario y sin procesar) | Cadena |

## <a name="next-steps"></a>Pasos siguientes
Consulte los [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver la lista de almacenes de datos que la actividad de copia de Azure Data Factory admite como orígenes y receptores.
