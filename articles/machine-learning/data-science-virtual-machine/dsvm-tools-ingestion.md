---
title: Herramientas de ingesta de datos de Data Science Virtual Machine (Azure) | Microsoft Docs
description: Obtenga información sobre las utilidades y herramientas de ingesta de datos instaladas previamente en Data Science Virtual Machine.
keywords: herramientas de ciencia de datos, máquina virtual de ciencia de datos, herramientas para la ciencia de datos, ciencia de datos de linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2017
ms.author: vijetaj
ms.openlocfilehash: ffc6071206236bc25d3c02576225c1de935f722a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68557714"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Herramientas de ingesta de datos de Data Science Virtual Machine

Uno de los primeros pasos técnicos en un proyecto de inteligencia artificial o ciencia de datos es identificar los conjuntos de datos que se van a usar y colocarlos en el entorno de análisis. Data Science Virtual Machine (DSVM) proporciona herramientas y bibliotecas para traer datos de distintos orígenes a un almacenamiento de datos de análisis local en DSVM o en una plataforma de datos local o en la nube. 

Estas son algunas herramientas de movimiento de datos que se pueden encontrar en DSVM. 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Una herramienta para copiar datos de blobs de Azure Storage en Azure Data Lake Store. También puede usar copiar datos entre dos cuentas de Azure Data Lake Store.      |
| Versiones de DSVM compatibles      | Windows      |
| Usos típicos      | Importación de varios blobs de Azure Storage en Azure Data Lake Store.      |
|  ¿Cómo se usa o ejecuta?    |   Abra un símbolo del sistema y escriba `adlcopy` para obtener ayuda.    |
| Vínculos a ejemplos      | [Uso de AdlCopy](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| Herramientas relacionadas en DSVM      | AzCopy, línea de comandos de Azure     |

## <a name="azure-command-line"></a>Línea de comandos de Azure

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Una herramienta de administración para Azure. También contiene verbos de comando para mover datos de plataformas de datos de Azure como blobs de almacenamiento de Azure o Azure Data Lake Storage     |
| Versiones de DSVM compatibles      | Windows, Linux     |
| Usos típicos      | Importación y exportación de datos a y desde Azure Storage o Azure Data Lake Store      |
|  ¿Cómo se usa o ejecuta?    |   Abra un símbolo del sistema y escriba `az` para obtener ayuda.    |
| Vínculos a ejemplos      | [Uso de la CLI de Azure](https://docs.microsoft.com/cli/azure)     |
| Herramientas relacionadas en DSVM      | AzCopy, AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Una herramienta para copiar datos a y desde archivos locales, blobs de almacenamiento de Azure, archivos y tablas.      |
| Versiones de DSVM compatibles      | Windows      |
| Usos típicos      | Copia de archivos a Blob Storage o copia de blobs entre cuentas.      |
|  ¿Cómo se usa o ejecuta?    |   Abra un símbolo del sistema y escriba `azcopy` para obtener ayuda.    |
| Vínculos a ejemplos      | [AzCopy en Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy)      |
| Herramientas relacionadas en DSVM      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Herramienta de migración de datos de Azure Cosmos DB

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Herramienta para importar datos desde diversos orígenes, incluidos archivos JSON, archivos CSV, SQL, MongoDB, Azure Table Storage, Amazon DynamoDB y colecciones de la API de SQL a Azure Cosmos DB.      |
| Versiones de DSVM compatibles      | Windows      |
| Usos típicos      | Importación de archivos de una máquina virtual a CosmosDB, importación de datos desde Azure Table Storage a CosmosDB o importación de datos desde una base de datos de SQL Server a CosmosDB.     |
|  ¿Cómo se usa o ejecuta?    |   Para usar la versión de línea de comandos, abra un símbolo del sistema y escriba `dt`. Para usar la versión de línea de comandos, abra un símbolo del sistema y escriba `dtui`.    |
| Vínculos a ejemplos      | [Importación de datos en Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/import-data)      |
| Herramientas relacionadas en DSVM      | AzCopy, AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Herramienta de SQL Server para copiar datos entre SQL Server y un archivo de datos.      |
| Versiones de DSVM compatibles      | Windows      |
| Usos típicos      | Importación de un archivo CSV a una tabla de SQL Server o exportación de una tabla de SQL Server a un archivo.      |
|  ¿Cómo se usa o ejecuta?    |   Abra un símbolo del sistema y escriba `bcp` para obtener ayuda.    |
| Vínculos a ejemplos      | [Utilidad de copia masiva](https://docs.microsoft.com/sql/tools/bcp-utility)      |
| Herramientas relacionadas en DSVM      | SQL Server y sqlcmd      |

## <a name="blobfuse"></a>blobfuse

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Una herramienta para montar un contenedor de blobs de Azure en el sistema de archivos de Linux.      |
| Versiones de DSVM compatibles      | Linux      |
| Usos típicos      | Leer y escribir en los blobs de un contenedor      |
|  ¿Cómo se usa o ejecuta?    |   Ejecute _blobfuse_ en un terminal.    |
| Vínculos a ejemplos      | [blobfuse en GitHub](https://github.com/Azure/azure-storage-fuse)      |
| Herramientas relacionadas en DSVM      | Línea de comandos de Azure      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| ¿Qué es?   | Una herramienta para conectar orígenes de datos locales a servicios en la nube para su consumo.      |
| Versiones de DSVM compatibles      | Windows      |
| Usos típicos      | Conexión de una máquina virtual a un origen de datos local.      |
|  ¿Cómo se usa o ejecuta?    |   Inicie "Microsoft Data Management Gateway" desde el menú Inicio.    |
| Vínculos a ejemplos      | [Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx)      |
| Herramientas relacionadas en DSVM      | AzCopy, AdlCopy y bcp    |
