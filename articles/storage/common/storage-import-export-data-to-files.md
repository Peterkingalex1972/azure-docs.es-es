---
title: Uso de Azure Import/Export para transferir datos a Azure Files | Microsoft Docs
description: Aprenda a crear trabajos de importación en Azure Portal para transferir datos a Azure Files.
author: alkohli
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/08/2019
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 28026a429643c62434ddfd7591126169857a7371
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479069"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Uso del servicio Azure Import/Export para importar datos a Azure Files

Este artículo proporciona instrucciones paso a paso sobre cómo usar el servicio Azure Import/Export para importar de forma segura grandes cantidades de datos en Azure Files. Para importar datos, el servicio necesita que envíe las unidades de disco admitidas que contiene los datos a un centro de datos de Azure.  

El servicio Import/Export admite solo la importación de Azure Files en Azure Storage. No se admite la exportación de archivos desde Azure Files.

## <a name="prerequisites"></a>Requisitos previos

Antes de crear un trabajo de importación para transferir datos en Azure Files, revise con cuidado y complete la siguiente lista de requisitos previos. Debe:

- Tener una suscripción activa de Azure para usar con el servicio Import/Export.
- Tener al menos una cuenta de Azure Storage. Consulte la lista de [las cuenta de almacenamiento y los tipos de almacenamiento admitidos para el servicio Import/Export](storage-import-export-requirements.md). Para obtener información acerca de la creación de una nueva cuenta de almacenamiento, consulte [Creación de una cuenta de almacenamiento](storage-quickstart-create-account.md).
- Tener un número suficiente de discos de los [tipos admitidos](storage-import-export-requirements.md#supported-disks). 
- Tener un sistema de Windows que ejecute una [versión admitida del sistema operativo](storage-import-export-requirements.md#supported-operating-systems).
- [Descargar la versión 2 de WAImportExport](https://aka.ms/waiev2) en el sistema de Windows. Descomprima en la carpeta predeterminada `waimportexport`. Por ejemplo, `C:\WaImportExport`.
- Tener una cuenta de FedEx o DHL. Si quiere usar alguna empresa de mensajería que no sea FedEx o DHL, póngase en contacto con el equipo de operaciones de Azure Data Box en `adbops@microsoft.com`.  
    - La cuenta debe ser válida, debe tener saldo positivo y debe tener capacidades de devolución de envíos.
    - Generar un número de seguimiento del trabajo de exportación.
    - Cada trabajo debe tener un número de seguimiento independiente. No se admiten varios trabajos con el mismo número de seguimiento.
    - Si no tiene una cuenta de transportista, vaya a:
        - [Crear una cuenta de FedEX](https://www.fedex.com/en-us/create-account.html), o 
        - [Crear una cuenta de DHL](http://www.dhl-usa.com/en/express/shipping/open_account.html).
 


## <a name="step-1-prepare-the-drives"></a>Paso 1: Preparación de las unidades de disco

Este paso genera un archivo de diario. El archivo de diario almacena información básica como el número de serie de unidad, la clave de cifrado y los detalles de la cuenta de almacenamiento.

Realice los pasos siguientes para preparar las unidades de disco.

1. Conecte las unidades de disco al sistema de Windows a través de los conectores SATA.
2. Cree un volumen NTFS único en cada unidad. Asigne una letra de unidad al volumen. No utilice puntos de montaje.
3. Modifique el archivo *dataset.csv* en el directorio raíz en el que reside la herramienta. Dependiendo de si desea importar un archivo o carpeta o ambos, agregue las entradas en el archivo *dataset.csv* de forma similar a los siguientes ejemplos.  

   - **Para importar un archivo**: en el ejemplo siguiente, los datos que se van a copiar residen en la unidad C: El archivo *MyFile1.txt* se copia en la raíz de *MyAzureFileshare1*. Si *MyAzureFileshare1* no existe, se crea en la cuenta de Azure Storage. Se mantiene la estructura de carpetas.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
    
       ```
   - **Para importar una carpeta**: todos los archivos y carpetas bajo *MyFolder2* se copiarán en el recurso compartido de archivos de forma recursiva. Se mantiene la estructura de carpetas.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
            
       ```
     Se pueden realizar varias entradas en el mismo archivo correspondiente a las carpetas o archivos que se importan. 

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None 
                        
       ```
     Obtenga más información sobre cómo [preparar el archivo CSV de conjunto de datos](storage-import-export-tool-preparing-hard-drives-import.md#prepare-the-dataset-csv-file).
    

4. Modifique el archivo *driveset.csv* en el directorio raíz en el que reside la herramienta. Agregar entradas en el archivo *driveset.csv* similares a las de los siguientes ejemplos. El archivo driveset tiene la lista de discos y las letras de unidad de disco correspondiente para que la herramienta pueda elegir correctamente la lista de discos que se van a preparar.

    En este ejemplo se da por supuesto que están conectados dos discos y que se crean volúmenes NTFS básicos G:\ y H:\. H:\no está cifrado mientras G: ya está cifrado. La herramienta da formato y cifra el disco que hospeda solo H:\ (y no G:\).

   - **Para un disco que no está cifrado**: especificar *Encrypt* para habilitar el cifrado de BitLocker en el disco.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```
    
   - **Para un disco que ya está cifrado**: especificar *AlreadyEncrypted* y proporcionar la clave de BitLocker.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     Se pueden realizar varias entradas en el mismo archivo que correspondan a varias unidades de disco. Obtenga más información sobre cómo [preparar el archivo CSV de conjunto de unidades](storage-import-export-tool-preparing-hard-drives-import.md#prepare-initialdriveset-or-additionaldriveset-csv-file). 

5. Use la opción `PrepImport` para copiar y preparar los datos para la unidad de disco. Para la primera sesión de copia para copiar directorios o archivos con una nueva sesión de copia, ejecute el siguiente comando:

       ```
       .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
       ```

   A continuación se muestra un ejemplo de importación.
  
       ```
       .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
       ```
 
6. Se crea un archivo de diario con el nombre proporcionado con el parámetro `/j:` para cada ejecución de la línea de comandos. Cada unidad que prepare tiene un archivo de diario que se tiene que cargar cuando se crea el trabajo de importación. Las unidades sin archivos de diario no se procesarán.

    > [!IMPORTANT]
    > - No modifique los datos de las unidades de disco o el archivo de diario después de completar la preparación del disco.

Para ejemplos adicionales, vaya a [Ejemplos para archivos de diario](#samples-for-journal-files).

## <a name="step-2-create-an-import-job"></a>Paso 2: Crear un trabajo de importación 

Siga estos pasos para crear un trabajo de importación en Azure Portal.
1. Inicie sesión en https://portal.azure.com/.
2. Vaya a **Todos los servicios > Almacenamiento > Trabajos de importación o exportación**. 

    ![Ir a importar o exportar trabajos](./media/storage-import-export-data-to-blobs/import-to-blob1.png)

3. Haga clic en **Crear el trabajo de importación o exportación**.

    ![Hacer clic en trabajo de importación o exportación](./media/storage-import-export-data-to-blobs/import-to-blob2.png)

4. En **Aspectos básicos**:

    - Seleccione **Import into Azure** (Importar en Azure).
    - Escriba un nombre descriptivo para el trabajo de importación. Use este nombre para realizar un seguimiento de sus trabajos mientras estén en curso y una vez que se hayan completado.
        -  Este nombre puede contener solo letras minúsculas, números, guiones y caracteres de subrayado.
        -  El nombre tiene que empezar por una letra y no puede contener espacios. 
    - Seleccione una suscripción.
    - Seleccione un grupo de recursos. 

        ![Creación del trabajo de importación: Paso 1](./media/storage-import-export-data-to-blobs/import-to-blob3.png)

3. En **Detalles del trabajo**:
    
    - Cargar los archivos de diario que creó con anterioridad en [Paso 1: Preparación de las unidades de disco](#step-1-prepare-the-drives). 
    - Seleccione la cuenta de almacenamiento en la que se importarán los datos. 
    - La ubicación de la entrega se rellena automáticamente según la región de la cuenta de almacenamiento seleccionada.
   
       ![Creación del trabajo de importación: Paso 2](./media/storage-import-export-data-to-blobs/import-to-blob4.png)

4. En **Información de envío de devolución**:

    - Seleccione el transportista en la lista desplegable. Si desea usar una empresa de mensajería que no sea FedEx o DHL, elija una de las opciones de la lista desplegable. Póngase en contacto con el equipo de operaciones de Azure Data Box en `adbops@microsoft.com` con la información relacionada con el transportista que quiere usar.
    - Escriba un número válido de cuenta de transportista que haya creado con ese transportista. Microsoft usa esta cuenta para devolverle las unidades una vez que haya finalizado el trabajo de importación. 
    - Proporcione información completa y válida del contacto: nombre, teléfono, correo electrónico, dirección postal, ciudad, código postal, estado o provincia y país o región.

        > [!TIP] 
        > En lugar de especificar una dirección de correo electrónico para un solo usuario, proporcione un correo electrónico de grupo. Esto garantiza que recibirá notificaciones incluso si sale un administrador.

       ![Creación del trabajo de importación - Paso 3](./media/storage-import-export-data-to-blobs/import-to-blob5.png)

   
5. En el **Resumen**:

    - Proporcione la dirección de envío del centro de datos de Azure para el envío de discos a Azure. Asegúrese de que en la etiqueta de envío se mencione el nombre del trabajo y la dirección completa.
    - Haga clic en **Aceptar** para completar la creación del trabajo de importación.

        ![Creación del trabajo de importación: Paso 4](./media/storage-import-export-data-to-blobs/import-to-blob6.png)

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>Paso 3: Envío de las unidades de disco al centro de datos de Azure 

[!INCLUDE [storage-import-export-ship-drives](../../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>Paso 4: Actualización del trabajo con información de seguimiento

[!INCLUDE [storage-import-export-update-job-tracking](../../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>Paso 5: Comprobación de la carga de datos en Azure

Siga el trabajo hasta su finalización. Una vez que se haya finalizado el trabajo, compruebe que los datos se han cargado en Azure. Elimine los datos de forma local después de comprobar que la carga fue correcta.

## <a name="samples-for-journal-files"></a>Ejemplos para archivos de diario

Para **agregar más unidades**, cree un nuevo archivo de conjunto de unidades y ejecute el comando como se indica a continuación. 

Para sesiones de copia posteriores en unidades de disco distintas a las especificadas en el archivo *InitialDriveset.csv*, especifique un nuevo archivo *.csv* de conjunto de unidades y proporciónelo como valor para el parámetro `AdditionalDriveSet`. Use el **mismo nombre de archivo de diario** y proporcione un **nuevo identificador de sesión**. El formato del archivo CSV AdditionalDriveset es el mismo que el formato de InitialDriveSet.

    ```
    WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
    ```

A continuación se muestra un ejemplo de importación.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
    ```


Para agregar datos adicionales al mismo conjunto de unidades, use el comando PrepImport para sesiones de copia posteriores para copiar archivos o directorios adicionales.

En las sucesivas sesiones de copia en las mismas unidades de disco duro especificadas en el archivo *InitialDriveset.csv*, especifique el mismo nombre **de archivo de diario** e indique un **nuevo identificador de sesión**. No hace falta proporcionar la clave de la cuenta de almacenamiento.

    ```
    WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
    ```

A continuación se muestra un ejemplo de importación.

    ```
    WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Visualización del estado del trabajo y de la unidad de disco](storage-import-export-view-drive-status.md)
* [Revisión de los requisitos de Import/Export](storage-import-export-requirements.md)


