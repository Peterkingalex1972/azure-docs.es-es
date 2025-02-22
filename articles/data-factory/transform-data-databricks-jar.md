---
title: 'Transformación de datos con Jar en Databricks: Azure | Microsoft Docs'
description: Obtenga información sobre cómo procesar o transformar datos mediante la ejecución de una instancia de Jar en Databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: nabhishek
ms.author: abnarain
manager: craigg
ms.openlocfilehash: d299a785d50657ef40c0c49cb2dce33b8939fd02
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860996"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformación de datos mediante la ejecución de una actividad de Jar en Azure Databricks

La actividad de Jar en Azure Databricks en una [canalización de Data Factory](concepts-pipelines-activities.md) ejecuta un archivo Jar de Spark en el clúster de Azure Databricks. Este artículo se basa en el artículo sobre  [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas. Azure Databricks es una plataforma administrada para ejecutar Apache Spark.

Si desea una introducción y demostración de once minutos de esta característica, vea el siguiente vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definición de la actividad de Jar en Databricks

Esta es la definición de JSON de ejemplo de una actividad de Jar en Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propiedades de la actividad de Jar en Databricks

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

|Propiedad|DESCRIPCIÓN|Obligatorio|
|:--|---|:-:|
|Nombre|Nombre de la actividad en la canalización.|Sí|
|description|Texto que describe para qué se usa la actividad.|Sin|
|Tipo|En el caso de la actividad de Jar en Databricks, el tipo de actividad es DatabricksSparkJar.|Sí|
|linkedServiceName|Nombre del servicio vinculado de Databricks en el que se ejecuta la actividad de Jar. Para más información sobre este servicio vinculado, consulte el artículo  [Compute linked services](compute-linked-services.md)  (Servicios vinculados de procesos).|Sí|
|mainClassName|Nombre completo de la clase que incluye el método principal que se va a ejecutar. Esta clase debe estar contenida en un archivo JAR que se proporciona como una biblioteca.|Sí|
|parameters|Parámetros que se pasarán al método principal.  Se trata de una matriz de cadenas.|Sin|
|libraries|Lista de bibliotecas para instalar en el clúster que ejecutará el trabajo. Puede ser una cadena de <cadena, objeto>|Sí (al menos una con el método mainClassName)|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas compatibles con las actividades de Databricks

En la definición de la actividad de Databricks anterior, especifica estos tipos de biblioteca: *jar*, *egg*, *maven*, *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Para más detalles, consulte la [documentación de Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) sobre los tipos de bibliotecas.

## <a name="how-to-upload-a-library-in-databricks"></a>Carga de una biblioteca en Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Uso de la interfaz de usuario del área de trabajo de Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obtener la ruta de acceso de dbfs de la biblioteca que se agregó mediante la interfaz de usuario, puede usar la [CLI de Databricks (instalación)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Habitualmente, las bibliotecas de Jar se almacenan en dbfs:/FileStore/jars mientras se usa la interfaz de usuario. Puede enumerar todo mediante la CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copia de la biblioteca mediante la CLI de Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Use la CLI de Databricks [(pasos de instalación)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Ejemplo: copiar el archivo JAR en dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
