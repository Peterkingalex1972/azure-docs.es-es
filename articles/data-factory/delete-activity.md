---
title: Actividad de eliminación en Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo eliminar archivos en varios almacenes de archivo con la actividad de eliminación en Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2019
ms.openlocfilehash: d9a1c76e8ac386b954c68f16e2189df4e6c0e1b7
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "69996319"
---
# <a name="delete-activity-in-azure-data-factory"></a>Actividad de eliminación en Azure Data Factory

Puede usar la actividad de eliminación en Azure Data Factory para eliminar archivos o carpetas de almacenes locales o almacenes en la nube. Utilice esta actividad para limpiar o archivar los archivos cuando ya no se necesiten.

> [!WARNING]
> No se pueden restaurar las carpetas ni los archivos eliminados. Tenga cuidado al usar la actividad de eliminación para eliminar archivos o carpetas.

## <a name="best-practices"></a>Procedimientos recomendados

Estas son algunas recomendaciones para usar la actividad de eliminación:

-   Realice una copia de seguridad de los archivos antes de eliminarlos con la actividad de eliminación por si tuviera que restaurarlos en el futuro.

-   Asegúrese de que Data Factory tiene permisos de escritura para eliminar archivos o carpetas desde el almacén de almacenamiento.

-   Asegúrese de que no está eliminando archivos que se escriben al mismo tiempo. 

-   Si quiere eliminar archivos o carpetas de un sistema local, asegúrese de que está utilizando un entorno de ejecución de integración autohospedado con una versión superior a 3.14.

## <a name="supported-data-stores"></a>Almacenes de datos compatibles

-   [Almacenamiento de blobs de Azure](connector-azure-blob-storage.md)
-   [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
-   [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
-   [Azure File Storage](connector-azure-file-storage.md)

### <a name="file-system-data-stores"></a>Almacenes de datos del sistema de archivos

-   [Sistema de archivos](connector-file-system.md)
-   [FTP](connector-ftp.md)
-   [SFTP](connector-sftp.md)
-   [Amazon S3](connector-amazon-simple-storage-service.md)
-   [Google Cloud Storage](connector-google-cloud-storage.md)

## <a name="syntax"></a>Sintaxis

```json
{
    "name": "DeleteActivity",
    "type": "Delete",
    "typeProperties": {
        "dataset": {
            "referenceName": "<dataset name>",
            "type": "DatasetReference"
        },
        "recursive": true/false,
        "maxConcurrentConnections": <number>,
        "enableLogging": true/false,
        "logStorageSettings": {
            "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference"
            },
            "path": "<path to save log file>"
        }
    }
}
```

## <a name="type-properties"></a>Propiedades de tipo

| Propiedad | DESCRIPCIÓN | Obligatorio |
| --- | --- | --- |
| dataset | Proporciona la referencia del conjunto de datos para determinar qué archivos o carpetas se van a eliminar. | Sí |
| recursive | Indica si los archivos se eliminan de forma recursiva de las subcarpetas o solo de la carpeta especificada.  | No. El valor predeterminado es `false`. |
| maxConcurrentConnections | El número de conexiones para conectarse al almacén de almacenamiento al mismo tiempo para eliminar archivos o carpetas.   |  No. El valor predeterminado es `1`. |
| enablelogging | Indica si es necesario registrar los nombres de carpeta o archivo que se han eliminado. Si es true, tiene que proporcionar una cuenta de almacenamiento para guardar el archivo de registro, por lo que puede realizar un seguimiento de los comportamientos de la actividad de eliminación leyendo el archivo de registro. | Sin |
| logStorageSettings | Solo se aplica cuando enablelogging = true.<br/><br/>Un grupo de propiedades de almacenamiento que se pueden especificar donde desea guardar el archivo de registro que contiene los nombres de carpeta o archivo que se han eliminado por la actividad de eliminación. | Sin |
| linkedServiceName | Solo se aplica cuando enablelogging = true.<br/><br/>El servicio vinculado de [Azure Storage](connector-azure-blob-storage.md#linked-service-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#linked-service-properties) o [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#linked-service-properties) para almacenar el archivo de registro que contiene los nombres de carpeta o archivo que se han eliminado con la actividad de eliminación. Tenga en cuenta que se debe configurar con el mismo tipo de Integration Runtime que el que la actividad de eliminación usó para eliminar archivos. | Sin |
| path | Solo se aplica cuando enablelogging = true.<br/><br/>La ruta de acceso para guardar el archivo de registro en la cuenta de almacenamiento. Si no se proporciona una ruta de acceso, el servicio creará un contenedor para usted. | Sin |

## <a name="monitoring"></a>Supervisión

Hay dos lugares donde puede ver y supervisar los resultados de la actividad de eliminación: 
-   En la salida de la actividad de eliminación.
-   En el archivo de registro.

### <a name="sample-output-of-the-delete-activity"></a>Salida de ejemplo de la actividad de eliminación

```json
{ 
  "datasetName": "AmazonS3",
  "type": "AmazonS3Object",
  "prefix": "test",
  "bucketName": "adf",
  "recursive": true,
  "isWildcardUsed": false,
  "maxConcurrentConnections": 2,  
  "filesDeleted": 4,
  "logPath": "https://sample.blob.core.windows.net/mycontainer/5c698705-a6e2-40bf-911e-e0a927de3f07",
  "effectiveIntegrationRuntime": "MyAzureIR (West Central US)",
  "executionDuration": 650
}
```

### <a name="sample-log-file-of-the-delete-activity"></a>Archivo de registro de ejemplo de la actividad de eliminación

| NOMBRE | Categoría | Status | Error |
|:--- |:--- |:--- |:--- |
| test1/yyy.json | Archivo | Deleted |  |
| test2/hello789.txt | Archivo | Deleted |  |
| test2/test3/hello000.txt | Archivo | Deleted |  |
| test2/test3/zzz.json | Archivo | Deleted |  |

## <a name="examples-of-using-the-delete-activity"></a>Ejemplos de uso de la actividad de eliminación

### <a name="delete-specific-folders-or-files"></a>Eliminación de carpetas o archivos específicos

El almacén tiene la estructura de carpetas siguiente:

Root/ (Raíz):<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt

Ahora está usando la actividad de eliminación para eliminar archivos o carpetas mediante la combinación de diferentes valores de propiedad del conjunto de datos y la actividad de eliminación:

| folderPath (del conjunto de datos) | fileName (del conjunto de datos) | recursive (de la actividad de eliminación) | Output |
|:--- |:--- |:--- |:--- |
| Root (Raíz)/ Folder_A_2 | NULL | False | Root/ (Raíz):<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root (Raíz)/ Folder_A_2 | NULL | True | Root/ (Raíz):<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_A_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>5.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_1/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>7.csv</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>Folder_B_2/</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |
| Root (Raíz)/ Folder_A_2 | *.txt | False | Root/ (Raíz):<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;6.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;8.txt |
| Root (Raíz)/ Folder_A_2 | *.txt | True | Root/ (Raíz):<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2.txt<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;Folder_A_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>4.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_1/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>6.txt</strike><br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;7.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Folder_B_2/<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<strike>8.txt</strike> |

### <a name="periodically-clean-up-the-time-partitioned-folder-or-files"></a>Limpieza periódica de la carpeta o los archivos de particiones por hora

Puede crear una canalización para limpiar periódicamente la carpeta o los archivos de particiones por hora.  Por ejemplo, la estructura de carpetas es similar a: `/mycontainer/2018/12/14/*.csv`.  Puede aprovechar la variable del sistema ADF del desencadenador de programación para identificar qué archivos o carpetas se deben eliminar en cada ejecución de canalización. 

#### <a name="sample-pipeline"></a>Canalización de ejemplo

```json
{
    "name": "cleanup_time_partitioned_folder",
    "properties": {
        "activities": [
            {
                "name": "DeleteOneFolder",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "PartitionedFolder",
                        "type": "DatasetReference",
                        "parameters": {
                            "TriggerTime": {
                                "value": "@formatDateTime(pipeline().parameters.TriggerTime, 'yyyy/MM/dd')",
                                "type": "Expression"
                            }
                        }
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ],
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/pipelines"
}
```

#### <a name="sample-dataset"></a>Conjunto de datos de ejemplo

```json
{
    "name": "PartitionedFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "TriggerTime": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": {
                "value": "@concat('mycontainer/',dataset().TriggerTime)",
                "type": "Expression"
            }
        }
    },
    "type": "Microsoft.DataFactory/factories/datasets"
}
```

#### <a name="sample-trigger"></a>Desencadenador de ejemplo

```json
{
    "name": "DailyTrigger",
    "properties": {
        "runtimeState": "Started",
        "pipelines": [
            {
                "pipelineReference": {
                    "referenceName": "cleanup_time_partitioned_folder",
                    "type": "PipelineReference"
                },
                "parameters": {
                    "TriggerTime": "@trigger().scheduledTime"
                }
            }
        ],
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Day",
                "interval": 1,
                "startTime": "2018-12-13T00:00:00.000Z",
                "timeZone": "UTC",
                "schedule": {
                    "minutes": [
                        59
                    ],
                    "hours": [
                        23
                    ]
                }
            }
        }
    }
}
```

### <a name="clean-up-the-expired-files-that-were-last-modified-before-201811"></a>Limpie los archivos expirados que se modificaron por última vez antes del 1 de enero de 2018.

Puede crear una canalización para limpiar los archivos antiguos o expirados aprovechando el filtro de atributo de archivos: "LastModified" en el conjunto de datos.  

#### <a name="sample-pipeline"></a>Canalización de ejemplo

```json
{
    "name": "CleanupExpiredFiles",
    "properties": {
        "activities": [
            {
                "name": "DeleteFilebyLastModified",
                "type": "Delete",
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "typeProperties": {
                    "dataset": {
                        "referenceName": "BlobFilesLastModifiedBefore201811",
                        "type": "DatasetReference"
                    },
                    "recursive": true,
                    "logStorageSettings": {
                        "linkedServiceName": {
                            "referenceName": "BloblinkedService",
                            "type": "LinkedServiceReference"
                        },
                        "path": "mycontainer/log"
                    },
                    "enableLogging": true
                }
            }
        ]
    }
}
```

#### <a name="sample-dataset"></a>Conjunto de datos de ejemplo

```json
{
    "name": "BlobFilesLastModifiedBefore201811",
    "properties": {
        "linkedServiceName": {
            "referenceName": "BloblinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "*",
            "folderPath": "mycontainer",
            "modifiedDatetimeEnd": "2018-01-01T00:00:00.000Z"
        }
    }
}
```

### <a name="move-files-by-chaining-the-copy-activity-and-the-delete-activity"></a>Movimiento de archivos encadenando la actividad de copia y la actividad de eliminación

Puede mover un archivo mediante una actividad de copia para copiar un archivo y, a continuación, una actividad de eliminación para eliminar un archivo en una canalización.  Si desea mover varios archivos, puede usar la actividad GetMetadata + actividad de filtro + actividad Foreach + actividad de copia + actividad de eliminación como en el ejemplo siguiente:

> [!NOTE]
> Si desea mover toda la carpeta definiendo un conjunto de datos que contiene solo una ruta de carpeta y, a continuación, usando una actividad de copia y una actividad de eliminación para hacer referencia al mismo conjunto de datos que representa una carpeta, deberá tener mucho cuidado. El motivo es que tiene que asegurarse de que NO lleguen nuevos archivos a la carpeta entre la operación de copia y la operación de eliminación.  Si llegan archivos nuevos a la carpeta en el momento en que la actividad de copia acaba de completar el trabajo de copia pero la actividad de eliminación aún no ha empezado, es posible que la actividad de eliminación elimine este nuevo archivo entrante que NO se ha copiado aún en el destino mediante la eliminación de toda la carpeta. 

#### <a name="sample-pipeline"></a>Canalización de ejemplo

```json
{
    "name": "MoveFiles",
    "properties": {
        "activities": [
            {
                "name": "GetFileList",
                "type": "GetMetadata",
                "typeProperties": {
                    "dataset": {
                        "referenceName": "OneSourceFolder",
                        "type": "DatasetReference"
                    },
                    "fieldList": [
                        "childItems"
                    ]
                }
            },
            {
                "name": "FilterFiles",
                "type": "Filter",
                "dependsOn": [
                    {
                        "activity": "GetFileList",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('GetFileList').output.childItems",
                        "type": "Expression"
                    },
                    "condition": {
                        "value": "@equals(item().type, 'File')",
                        "type": "Expression"
                    }
                }
            },
            {
                "name": "ForEachFile",
                "type": "ForEach",
                "dependsOn": [
                    {
                        "activity": "FilterFiles",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "typeProperties": {
                    "items": {
                        "value": "@activity('FilterFiles').output.value",
                        "type": "Expression"
                    },
                    "batchCount": 20,
                    "activities": [
                        {
                            "name": "CopyAFile",
                            "type": "Copy",
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": false
                                },
                                "sink": {
                                    "type": "BlobSink"
                                },
                                "enableStaging": false,
                                "dataIntegrationUnits": 0
                            },
                            "inputs": [
                                {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "OneDestinationFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "DestinationFileName": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                }
                            ]
                        },
                        {
                            "name": "DeleteAFile",
                            "type": "Delete",
                            "dependsOn": [
                                {
                                    "activity": "CopyAFile",
                                    "dependencyConditions": [
                                        "Succeeded"
                                    ]
                                }
                            ],
                            "policy": {
                                "timeout": "7.00:00:00",
                                "retry": 0,
                                "retryIntervalInSeconds": 30,
                                "secureOutput": false,
                                "secureInput": false
                            },
                            "typeProperties": {
                                "dataset": {
                                    "referenceName": "OneSourceFile",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "path": "myFolder",
                                        "filename": {
                                            "value": "@item().name",
                                            "type": "Expression"
                                        }
                                    }
                                },
                                "logStorageSettings": {
                                    "linkedServiceName": {
                                        "referenceName": "BloblinkedService",
                                        "type": "LinkedServiceReference"
                                    },
                                    "path": "Container/log"
                                },
                                "enableLogging": true
                            }
                        }
                    ]
                }
            }
        ]
    }
}
```

#### <a name="sample-datasets"></a>Conjuntos de datos de ejemplo

Conjunto de datos utilizado por la actividad GetMetadata para enumerar la lista de archivos.

```json
{
    "name": "OneSourceFolder",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": "",
            "folderPath": "myFolder"
        }
    }
}
```

Conjunto de datos para origen de datos utilizado por la actividad de copia y la actividad de eliminación.

```json
{
    "name": "OneSourceFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "path": {
                "type": "String"
            },
            "filename": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().filename",
                "type": "Expression"
            },
            "folderPath": {
                "value": "@{dataset().path}",
                "type": "Expression"
            }
        }
    }
}
```

Conjunto de datos de destino de datos utilizado por la actividad de copia.

```json
{
    "name": "OneDestinationFile",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "DestinationFileName": {
                "type": "String"
            }
        },
        "type": "AzureBlob",
        "typeProperties": {
            "fileName": {
                "value": "@dataset().DestinationFileName",
                "type": "Expression"
            },
            "folderPath": "mycontainer/dest"
        }
    }
}
```

También puede obtener la plantilla para mover archivos [aquí](solution-template-move-files.md).

## <a name="known-limitation"></a>Limitación conocida

-   La actividad de eliminación no admite la eliminación de la lista de carpetas descrita con el comodín.

-   Cuando use el filtro de atributo de archivo: modifiedDatetimeStart y modifiedDatetimeEnd para seleccionar los archivos que van a eliminar, asegúrese de establecer "fileName": "*" en el conjunto de datos.

## <a name="next-steps"></a>Pasos siguientes

Obtener más información acerca de cómo mover archivos en Azure Data Factory.

-   [Herramienta Copiar datos en Azure Data Factory](copy-data-tool.md)