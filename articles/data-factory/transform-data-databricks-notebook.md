---
title: 'Transformación de datos con cuadernos de Databricks: Azure | Microsoft Docs'
description: Aprenda a procesar o transformar datos mediante la ejecución de blocs de notas de Databricks.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/15/2018
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 2bc8b84d4b98036acc93788dee88444786df139e
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335845"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformación de datos mediante la ejecución de blocs de notas de Databricks

La actividad Notebook de Azure Databricks en una [canalización de Data Factory](concepts-pipelines-activities.md) ejecuta un bloc de notas de Databricks en el área de trabajo de Azure Databricks. Este artículo se basa en el artículo sobre  [actividades de transformación de datos](transform-data.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas. Azure Databricks es una plataforma administrada para ejecutar Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definición de la actividad Notebook de Databricks

Esta es la definición de JSON de ejemplo de una actividad Notebook de Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propiedades de la actividad Notebook de Databricks

En la siguiente tabla se describen las propiedades JSON que se usan en la definición de JSON:

|Propiedad|DESCRIPCIÓN|Obligatorio|
|---|---|---|
|name|Nombre de la actividad en la canalización.|Sí|
|description|Texto que describe para qué se usa la actividad.|Sin|
|Tipo|Para la actividad Notebook de Databricks, el tipo de actividad es DatabricksNotebook.|Sí|
|linkedServiceName|Nombre del servicio vinculado de Databricks en el que se ejecuta el bloc de notas de Databricks. Para más información sobre este servicio vinculado, consulte el artículo  [Servicios vinculados de proceso](compute-linked-services.md) .|Sí|
|notebookPath|La ruta de acceso absoluta del cuaderno que se va a ejecutar en el área de trabajo de Databricks. Esta ruta de acceso debe comenzar con una barra diagonal.|Sí|
|baseParameters|Una matriz de pares de clave y valor. Se pueden utilizar parámetros base para cada ejecución de actividad. Si el cuaderno toma un parámetro que no se ha especificado, se usará el valor predeterminado del cuaderno. Encuentre más información sobre los parámetros en los [cuadernos de Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Sin|
|libraries|Lista de bibliotecas para instalar en el clúster que ejecutará el trabajo. Puede ser una matriz de \<cadena, objeto>.|Sin|


## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas compatibles con las actividades de Databricks

En la definición de la actividad de Databricks anterior, se especifican estos tiposd e biblioteca: *jar*, *egg*, *whl*, *maven*, *pypi*, *cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

Habitualmente, las bibliotecas de Jar se almacenan en dbfs:/FileStore/jars mientras se usa la interfaz de usuario. Puede enumerar todo a través de la CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copia de la biblioteca mediante la CLI de Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Ejemplo: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
