---
title: Transformación de datos mediante la actividad de Hive - Azure | Microsoft Docs
description: Aprenda cómo puede usar la actividad de Hive en Factoría de datos de Azure para ejecutar consultas de Hive en un clúster de HDInsight suyo propio o a petición.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: a63ef969f17fc48145174d99fec53e77b61885a4
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827970"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformación de datos mediante la actividad de Hive en Azure Data Factory 
> [!div class="op_single_selector" title1="Actividades de transformación"]
> * [Actividad de Hive](data-factory-hive-activity.md) 
> * [Actividad de Pig](data-factory-pig-activity.md)
> * [Actividad MapReduce](data-factory-map-reduce.md)
> * [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Actividad de Spark](data-factory-spark.md)
> * [Actividad de ejecución de Batch de Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Actividad Actualizar recurso de Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Actividad de procedimiento almacenado](data-factory-stored-proc-activity.md)
> * [Actividad U-SQL de Data Lake Analytics](data-factory-usql-activity.md)
> * [Actividad personalizada de .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, consulte el artículo acerca de la [transformación de datos mediante la actividad Hive en Data Factory](../transform-data-using-hadoop-hive.md).

La actividad de Hive de HDInsight en una [canalización](data-factory-create-pipelines.md) de Data Factory ejecuta consultas de Hive en [su propio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clúster de HDInsight o en uno [a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) basado en Windows/Linux. Este artículo se basa en el artículo sobre [actividades de transformación de datos](data-factory-data-transformation-activities.md) , que presenta información general de la transformación de datos y las actividades de transformación admitidas.

> [!NOTE] 
> Si no está familiarizado con Azure Data Factory, vea [Introducción a Azure Data Factory](data-factory-introduction.md) y siga el tutorial sobre la [compilación de la primera canalización de datos](data-factory-build-your-first-pipeline.md) antes de leer este artículo. 

## <a name="syntax"></a>Sintaxis

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Detalles de la sintaxis
| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| name |Nombre de la actividad |Sí |
| description |Texto que describe para qué se usa la actividad. |Sin |
| type |HDinsightHive |Sí |
| inputs |Entradas consumidas por la actividad de Hive |Sin |
| outputs |Salidas producidas por la actividad de Hive |Sí |
| linkedServiceName |Referencia al clúster de HDInsight registrado como un servicio vinculado en la factoría de datos |Sí |
| script |Especifica el script de Hive en línea |Sin |
| scriptPath |Almacena el script de Hive en un almacenamiento de blobs de Azure y proporciona la ruta de acceso al archivo. Use la propiedad 'script' o 'scriptPath'. No se pueden usar las dos juntas. El nombre del archivo distingue mayúsculas de minúsculas. |Sin |
| defines |Especifique parámetros como pares de clave y valor para referencia en el script de Hive con 'hiveconf' |Sin |

## <a name="example"></a>Ejemplo
Veamos un ejemplo de análisis de registros de juegos en el que desea identificar el tiempo dedicado por los usuarios a los juegos de su empresa. 

El siguiente registro muestra un registro de juego de ejemplo, que está separado por comas (`,`) y que contiene los siguientes campos: ProfileID, SessionStart, Duration, SrcIPAddress y GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

El **script de Hive** para procesar estos datos tiene el siguiente aspecto:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Para ejecutar este script de Hive en una canalización de Data Factory, necesita hacer lo siguiente

1. Crear un servicio vinculado para registrar [su propio clúster de proceso de HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) o configurar un [clúster de proceso de HDInsight a petición](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Llamaremos a este servicio vinculado "HDInsightLinkedService".
2. Crear un [servicio vinculado](data-factory-azure-blob-connector.md) para configurar la conexión al almacenamiento de blobs de Azure que hospeda los datos. Llamaremos a este servicio vinculado "StorageLinkedService"
3. Crear [conjuntos de datos](data-factory-create-datasets.md) que apuntan a los datos de entrada y salida. Llamaremos al conjunto de datos de entrada "HiveSampleIn" y al conjunto de datos de salida "HiveSampleOut"
4. Copiar la consulta de Hive como un archivo en Azure Blob Storage configurado en el paso 2. Si el almacenamiento para hospedar los datos es diferente al que hospeda este archivo de consulta, crear un servicio vinculado de Azure Storage independiente y hacer referencia a él en la actividad. Use **scriptPath** para especificar la ruta de acceso al archivo de consulta de Hive y **scriptLinkedService** para especificar el almacenamiento de Azure que contiene el archivo de script. 
   
   > [!NOTE]
   > También puede proporcionar el script de Hive en línea en la definición de actividad mediante la propiedad **script** . No se recomienda este enfoque si todos los caracteres especiales del script del documento JSON tienen que ser caracteres de escape y pueden provocar problemas de depuración. La práctica recomendada es seguir el paso 4.
   > 
   > 
5. Cree una canalización con la actividad HDInsightHive. La actividad de procesa y transforma los datos.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Implemente la canalización. Consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md) para obtener más información. 
7. Supervise la canalización mediante las vistas de supervisión y administración de Factoría de datos. Consulte el artículo [Supervisión y administración de las canalizaciones de Factoría de datos](data-factory-monitor-manage-pipelines.md) para obtener más información. 

## <a name="specifying-parameters-for-a-hive-script"></a>Especificación de parámetros para un script de Hive
En este ejemplo, los registros de juegos se introducen diariamente en Azure Blob Storage y se almacenan en una carpeta dividida con fecha y hora. Desea parametrizar el script de Hive y pasar la ubicación de la carpeta de entrada dinámicamente en tiempo de ejecución y también generar la salida dividida con fecha y hora.

Para usar scripts de Hive parametrizados, haga lo siguiente

* Defina los parámetros en **defines**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* En el Script de Hive, consulte el parámetro mediante **${hiveconf:parameterName}** . 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Otras referencias
* [Actividad de Pig](data-factory-pig-activity.md)
* [Actividad MapReduce](data-factory-map-reduce.md)
* [Actividad de streaming de Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar programas Spark](data-factory-spark.md)
* [Invocar scripts de R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

