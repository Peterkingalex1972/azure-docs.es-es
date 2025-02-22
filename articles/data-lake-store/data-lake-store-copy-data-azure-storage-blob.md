---
title: Copia de datos desde Azure Storage Blob a Azure Data Lake Storage Gen1 | Microsoft Docs
description: Use la herramienta AdlCopy para copiar datos desde Azure Storage Blob a Azure Data Lake Storage Gen1.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: dc273ef8-96ef-47a6-b831-98e8a777a5c1
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: be66fd51b37c0e62b2b757a88ee1db9319b2093a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60878841"
---
# <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-storage-gen1"></a>Copia de datos de Azure Storage Blobs a Azure Data Lake Storage Gen1
> [!div class="op_single_selector"]
> * [Uso de DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Uso de AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Azure Data Lake Storage Gen1 proporciona una herramienta de línea de comandos, [AdlCopy](https://aka.ms/downloadadlcopy), para copiar datos desde los siguientes orígenes:

* De Azure Storage Blobs a Data Lake Storage Gen1. No puede utilizar AdlCopy para copiar datos de Data Lake Storage Gen1 a Azure Storage Blob.
* Entre dos cuentas de Azure Data Lake Storage Gen1.

Además, puede usar la herramienta AdlCopy en dos modos diferentes:

* **Independiente**, donde la herramienta usa recursos de Data Lake Storage Gen1 para realizar la tarea.
* **Con una cuenta de Análisis de Data Lake**, donde las unidades asignadas a la cuenta de Análisis de Data Lake se usan para realizar la operación de copia. Puede que desee usar esta opción cuando quiera realizar las tareas de copia de forma predecible.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener lo siguiente:

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **blobs de Azure Storage** con algunos datos.
* **Una cuenta de Azure Data Lake Storage Gen1**. Para instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Cuenta de Azure Data Lake Analytics (opcional)** . Consulte el artículo de [introducción a Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obtener instrucciones sobre cómo crear una cuenta de Data Lake Analytics.
* **Herramienta AdlCopy**. Instale la herramienta de AdlCopy de [https://aka.ms/downloadadlcopy](https://aka.ms/downloadadlcopy).

## <a name="syntax-of-the-adlcopy-tool"></a>Sintaxis de la herramienta AdlCopy
Use la siguiente sintaxis para trabajar con la herramienta AdlCopy.

    AdlCopy /Source <Blob or Data Lake Storage Gen1 source> /Dest <Data Lake Storage Gen1 destination> /SourceKey <Key for Blob account> /Account <Data Lake Analytics account> /Units <Number of Analytics units> /Pattern

A continuación, se describen los parámetros de la sintaxis:

| Opción | DESCRIPCIÓN |
| --- | --- |
| Origen |Especifica la ubicación de los datos de origen en el blob de almacenamiento de Azure. El origen puede ser un contenedor de blobs, un blob u otra cuenta de Data Lake Storage Gen1. |
| Dest |Especifica el destino de Azure Data Lake Storage Gen1 al que copiar. |
| SourceKey |Especifica la clave de acceso de almacenamiento para el origen de blobs de almacenamiento de Azure. Esto solo es necesario si el origen es un contenedor de blobs o un blob. |
| Cuenta |**Opcional**. Utilícelo si desea usar una cuenta de Análisis de Azure Data Lake para ejecutar el trabajo de copia. Si usa la opción /Account en la sintaxis, pero no especifica una cuenta de Análisis de Data Lake, AdlCopy usa una cuenta predeterminada para ejecutar el trabajo. Además, si usa esta opción, debe agregar el origen (Azure Storage Blob) y el destino (Azure Data Lake Storage Gen1) como orígenes de datos para la cuenta de Data Lake Analytics. |
| Unidades |Especifica la cantidad de unidades de Análisis de Data Lake que se usará para el trabajo de copia. Esta opción es obligatoria si usa la opción **/Account** para especificar la cuenta de Análisis de Data Lake. |
| Patrón |Especifica un patrón Regex que indica qué blobs o archivos se van a copiar. AdlCopy usa una coincidencia que distingue mayúsculas de minúsculas. El patrón predeterminado que se usa cuando no se especifica ninguno es copiar todos los elementos. No se admite la especificación de varios patrones de archivos. |

## <a name="use-adlcopy-as-standalone-to-copy-data-from-an-azure-storage-blob"></a>Uso de AdlCopy (en modo independiente) para copiar datos desde un blob de Azure Storage
1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Ejecute el siguiente comando para copiar un blob específico desde el contenedor de origen a una carpeta de Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Por ejemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

    >[!NOTE] 
    >La sintaxis anterior especifica el archivo que se va a copiar en una carpeta de la cuenta de Data Lake Storage Gen1. La herramienta AdlCopy crea una carpeta si el nombre de carpeta especificado no existe.

    Se le pedirá que escriba las credenciales de la suscripción a Azure en la que tiene la cuenta de Data Lake Storage Gen1. Verá un resultado similar al siguiente:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.

1. También puede copiar todos los blobs desde un contenedor a la cuenta de Data Lake Storage Gen1 con el siguiente comando:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/ /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>        

    Por ejemplo:

        AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

### <a name="performance-considerations"></a>Consideraciones sobre rendimiento

Si va a copiar de una cuenta de Azure Blob Storage, puede tener alguna limitación durante la copia en Blob Storage. Como consecuencia, disminuirá el rendimiento de su trabajo de copia. Para aprender sobre los límites de Azure Blob Storage, consulte la información al respecto en [Límites de suscripciones y servicios de Azure](../azure-subscription-service-limits.md).

## <a name="use-adlcopy-as-standalone-to-copy-data-from-another-data-lake-storage-gen1-account"></a>Uso de AdlCopy (en modo independiente) para copiar datos desde otra cuenta de Data Lake Storage Gen1
También puede usar AdlCopy para copiar datos entre dos cuentas de Data Lake Storage Gen1.

1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Ejecute el siguiente comando para copiar un archivo específico de una cuenta de Data Lake Storage Gen1 a otra.

        AdlCopy /Source adl://<source_adlsg1_account>.azuredatalakestore.net/<path_to_file> /dest adl://<dest_adlsg1_account>.azuredatalakestore.net/<path>/

    Por ejemplo:

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/909f2b.log /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

   > [!NOTE]
   > La sintaxis anterior especifica el archivo que se va a copiar en una carpeta de la cuenta de Data Lake Storage Gen1 de destino. La herramienta AdlCopy crea una carpeta si el nombre de carpeta especificado no existe.
   >
   >

    Se le pedirá que escriba las credenciales de la suscripción a Azure en la que tiene la cuenta de Data Lake Storage Gen1. Verá un resultado similar al siguiente:

        Initializing Copy.
        Copy Started.|
        100% data copied.
        Finishing Copy.
        Copy Completed. 1 file copied.
3. El siguiente comando copia todos los archivos de una carpeta específica de la cuenta de Data Lake Storage Gen1 de origen en una carpeta de la cuenta de Data Lake Storage Gen1 de destino.

        AdlCopy /Source adl://mydatastorage.azuredatalakestore.net/mynewfolder/ /dest adl://mynewdatalakestorage.azuredatalakestore.net/mynewfolder/

### <a name="performance-considerations"></a>Consideraciones sobre rendimiento

Cuando se usa AdlCopy como una herramienta independiente, la copia se ejecuta en recursos administrados de Azure compartidos. El rendimiento que obtenga en este entorno depende de la carga del sistema y de los recursos disponibles. Este modo se usa preferiblemente en el caso de pequeñas transferencias de manera ad hoc. Cuando se usa AdlCopy como herramienta independiente, no es necesario ajustar ningún parámetro.

## <a name="use-adlcopy-with-data-lake-analytics-account-to-copy-data"></a>Uso de AdlCopy (con una cuenta de Data Lake Analytics) para copiar datos
También puede usar la cuenta de Data Lake Analytics para ejecutar el trabajo AdlCopy para copiar datos desde los blobs de almacenamiento de Azure a Data Lake Storage Gen1. Con frecuencia, usaría esta opción cuando los datos que se van a migrar se encuentran en el rango de gigabytes y terabytes y desea obtener un mejor rendimiento predecible.

Para usar la cuenta de Data Lake Analytics con AdlCopy para copiar desde un blob de Azure Storage, se debe agregar el origen (blob de Azure Storage) como origen de datos para la cuenta de Data Lake Analytics. Para obtener instrucciones sobre cómo agregar orígenes de datos adicionales a la cuenta de Data Lake Analytics, vea [Administración de orígenes de datos de la cuenta de Data Lake Analytics](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#manage-data-sources).

> [!NOTE]
> Si va a copiar desde una cuenta de Azure Data Lake Storage Gen1 como origen mediante una cuenta de Data Lake Analytics, no es necesario asociar la cuenta de Data Lake Storage Gen1 con la cuenta de Data Lake Analytics. El requisito para asociar el almacén de origen a la cuenta de Data Lake Analytics solo se aplica cuando el origen es una cuenta de Azure Storage.
>
>

Ejecute el siguiente comando para copiar de un blob de Azure Storage a una cuenta de Data Lake Storage Gen1 mediante la cuenta de Data Lake Analytics:

    AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Account <data_lake_analytics_account> /Units <number_of_data_lake_analytics_units_to_be_used>

Por ejemplo:

    AdlCopy /Source https://mystorage.blob.core.windows.net/mycluster/example/data/gutenberg/ /dest swebhdfs://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Account mydatalakeanalyticaccount /Units 2

De forma similar, ejecute el siguiente comando para copiar todos los archivos de una carpeta específica de la cuenta de Data Lake Storage Gen1 de origen en una carpeta de la cuenta de Data Lake Storage Gen1 de destino con la utilización de la cuenta de Data Lake Analytics:

    AdlCopy /Source adl://mysourcedatalakestorage.azuredatalakestore.net/mynewfolder/ /dest adl://mydestdatastorage.azuredatalakestore.net/mynewfolder/ /Account mydatalakeanalyticaccount /Units 2

### <a name="performance-considerations"></a>Consideraciones sobre rendimiento

Cuando se copian datos en el intervalo de terabytes, el uso de AdlCopy con su propia cuenta de Azure Data Lake Analytics proporciona un rendimiento mejor y más predecible. El parámetro que debe ajustarse es el número de unidades de Azure Data Lake Analytics que se usarán con el trabajo de copia. Al aumentar el número de unidades, aumentará el rendimiento de su trabajo de copia. Cada archivo que se va a copiar puede usar como máximo una unidad. Si se especifican más unidades que el número de archivos que se copian, no aumentará el rendimiento.

## <a name="use-adlcopy-to-copy-data-using-pattern-matching"></a>Uso de AdlCopy para copiar datos mediante la coincidencia de patrones
En esta sección, aprenderá a usar AdlCopy para copiar datos de un origen (en el siguiente ejemplo, se usa Azure Storage Blob) a una cuenta de Data Lake Storage Gen1 de destino mediante la coincidencia de patrones. Por ejemplo, puede usar los pasos siguientes para copiar todos los archivos con la extensión .csv del blob de origen al de destino.

1. Abra un símbolo del sistema y vaya al directorio donde está instalada la herramienta AdlCopy, normalmente `%HOMEPATH%\Documents\adlcopy`.
2. Ejecute el siguiente comando para copiar todos los archivos con la extensión *.csv de un blob específico del contenedor de origen a una cuenta de Data Lake Storage Gen1:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adlsg1_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container> /Pattern *.csv

    Por ejemplo:

        AdlCopy /source https://mystorage.blob.core.windows.net/mycluster/HdiSamples/HdiSamples/FoodInspectionData/ /dest adl://mydatalakestorage.azuredatalakestore.net/mynewfolder/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ== /Pattern *.csv

## <a name="billing"></a>Facturación
* Si usa la herramienta AdlCopy de forma independiente, se le facturarán los costos de salida por migrar datos, si la cuenta de Azure Storage de origen no está en la misma región que Data Lake Storage Gen1.
* Si usa la herramienta AdlCopy con la cuenta de Análisis de Data Lake, se aplicarán las [tarifas de facturación de Análisis de Data Lake](https://azure.microsoft.com/pricing/details/data-lake-analytics/) estándar.

## <a name="considerations-for-using-adlcopy"></a>Consideraciones para usar AdlCopy
* AdlCopy (para la versión 1.0.5) admite la copia de datos desde orígenes que en conjunto tienen más de miles de archivos y carpetas. Sin embargo, si tiene problemas al copiar un conjunto de datos grande, puede distribuir los archivos o carpetas en subcarpetas diferentes y usar la ruta de acceso a esas subcarpetas como origen en su lugar.

## <a name="performance-considerations-for-using-adlcopy"></a>Consideraciones de rendimiento sobre el uso de AdlCopy

AdlCopy admite la copia de datos que contienen miles de archivos y carpetas. Sin embargo, si encuentra problemas al copiar un conjunto de datos grande, puede distribuir las archivos o carpetas en subcarpetas más pequeñas. AdlCopy se creó para copias ad hoc. Si va a copiar datos de forma repetida, debe considerar el uso de [Azure Data Factory](../data-factory/connector-azure-data-lake-store.md), que proporciona administración completa en torno a las operaciones de copia.

## <a name="release-notes"></a>Notas de la versión
* 1.0.13: si va a copiar datos en la misma cuenta de Azure Data Lake Storage Gen1 entre varios comandos adlcopy, ya no es necesario volver a escribir las credenciales en cada ejecución. Ahora, Adlcopy almacena en caché esa información entre varias ejecuciones.

## <a name="next-steps"></a>Pasos siguientes
* [Protección de datos en Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Use Azure Data Lake Analytics with Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md) (Uso de Azure Data Lake Analytics con Data Lake Storage Gen1)
* [Uso de Azure HDInsight con Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
