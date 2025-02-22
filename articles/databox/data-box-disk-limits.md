---
title: Límites de Azure Data Box Disk | Microsoft Docs
description: Se describen los límites del sistema y los tamaños recomendados de Microsoft Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 04/01/2019
ms.author: alkohli
ms.openlocfilehash: 32445e3f6859a6161eb2fae20233c598234f18a0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60400633"
---
# <a name="azure-data-box-disk-limits"></a>Límites de Azure Data Box Disk


Tenga en cuenta estos límites cuando implemente y use su solución de Microsoft Azure Data Box.

## <a name="data-box-service-limits"></a>Límites de servicio de Data Box

 - El servicio Data Box está disponible en las regiones de Azure incluidas en [Disponibilidad en regiones](data-box-disk-overview.md#region-availability).
 - Solo se admite una cuenta de almacenamiento con Data Box Disk.

## <a name="data-box-disk-performance"></a>Rendimiento de Data Box Disk

Cuando se prueba con discos conectados mediante USB 3.0, el rendimiento del disco era de hasta 430 MB/s. Los números reales varían según el tamaño de archivo usado. Con archivos más pequeños, es posible que observe un rendimiento menor.

## <a name="azure-storage-limits"></a>Límites de almacenamiento de Azure

En esta sección se describen los límites del servicio Azure Storage y las convenciones de nomenclatura necesarias para Azure Files, blobs en bloques de Azure y blobs en páginas de Azure, según corresponda para el servicio Data Box. Revise cuidadosamente los límites de almacenamiento y siga todas las recomendaciones.

Para conocer la información más reciente sobre los límites del servicio de almacenamiento de Azure y los procedimientos recomendados para asignar nombres a recursos compartidos, contenedores y archivos, vaya a:

- [Nomenclatura y referencia de contenedores](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)
- [Nomenclatura y referencia de recursos compartidos](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata)
- [Convenciones de blobs en bloques y blobs en páginas](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)

> [!IMPORTANT]
> Si hay archivos o directorios que superan los límites del servicio Azure Storage, o no cumplen las convenciones de nomenclatura de archivos y blobs de Azure, estos archivos o directorios no se ingieren en Azure Storage mediante el servicio Data Box.

## <a name="data-upload-caveats"></a>Advertencias al cargar los datos

- No copie los datos directamente en los discos. Copie los datos en las carpetas *BlockBlob*, *PageBlob* y *AzureFile* creadas previamente.
- Una carpeta en las carpetas *BlockBlob* y *PageBlob* es un contenedor. Por ejemplo, los contenedores se crean como *BlockBlob/contenedor* y *PageBlob/contenedor*.
- Si tiene un objeto existente de Azure (por ejemplo, un blob) en la nube con el mismo nombre que el objeto que se está copiando, Data Box Disk cambiará el nombre del archivo a archivo(1) en la nube.
- Todos los archivos escritos en los recursos compartidos *BlockBlob* y *PageBlob* se cargan como blob en bloques y blob en páginas, respectivamente.
- Todas las jerarquías de directorios vacías (sin archivos) que creó en las carpetas *BlockBlob* y *PageBlob* no se cargan.
- Si se han producido errores al cargar datos en Azure, se crea un registro de errores en la cuenta de almacenamiento de destino. La ruta de acceso a este registro de errores está disponible en el portal cuando se completa la carga. Puede revisar el registro para realizar acciones correctivas. No elimine los datos del origen sin comprobar los datos cargados.
- Si especificó en orden los discos administrados, revise las siguientes consideraciones adicionales:

    - Solo puede tener un disco administrado con un nombre determinado en un grupo de recursos en las carpetas que se crearon previamente y en Data Box Disk. Esto implica que los VHD cargados en las carpetas creadas previamente deben tener nombres únicos. Asegúrese de que el nombre indicado no coincida con un disco administrado ya existente en un grupo de recursos. Si los discos duros virtuales tienen los mismos nombres, solo uno se convierte en un disco administrado con ese nombre. Los otros discos duros virtuales se cargan como blobs en páginas en la cuenta de almacenamiento provisional.
    - Copie siempre los VHD en una de las carpetas creadas previamente. Si copia los VHD fuera de estas carpetas o en una carpeta que usted mismo haya creado, se cargarán en la cuenta de Azure Storage como blobs en páginas y no como discos administrados.
    - Solo se pueden cargar VHD fijos para crear discos administrados. No se admiten los discos duros virtuales dinámicos, los de diferenciación ni los archivos VHDX.

## <a name="azure-storage-account-size-limits"></a>Límites de tamaño de cuenta de almacenamiento de Azure

Estos son los límites del tamaño de los datos que se copian en la cuenta de almacenamiento. Asegúrese de que los datos que carga se ajustan a estos límites. Para tener la información más actualizada sobre estos límites, vaya a [Objetivos de escalabilidad de Azure Blob Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-blob-storage-scale-targets) y [Objetivos de escalabilidad de Azure Files](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets#azure-files-scale-targets).

| Tamaño de los datos que se copian en la cuenta de almacenamiento de Azure                      | Límite predeterminado          |
|---------------------------------------------------------------------|------------------------|
| Blob en bloques y blobs en páginas                                            | 500 TB por cuenta de almacenamiento. <br> Esto incluye datos de todos los orígenes, incluido Data Box Disk.|


## <a name="azure-object-size-limits"></a>Límites de tamaño de objeto de Azure

Estos son los tamaños de los objetos de Azure que se pueden escribir. Asegúrese de que todos los archivos que se cargan se ajustan a estos límites.

| Tipo de objeto de Azure | Límite predeterminado                                             |
|-------------------|-----------------------------------------------------------|
| Blob en bloques        | ~4,75 TiB                                                 |
| Blob en páginas         | 8 TiB <br> Todos los archivos cargados en formato de blob en páginas deben tener 512 bytes alineados; de lo contrario, se produce un error en la carga. <br> VHD y VHDX tienen 512 bytes alineados. |
|Archivos de Azure        | 1 TiB <br> Máx. tamaño del recurso compartido es de 5 TiB     |
| Discos administrados     |4 TiB <br> Para más información sobre el tamaño y los límites, consulte: <li>[Objetivos de escalabilidad para discos administrados](../virtual-machines/windows/disk-scalability-targets.md#managed-virtual-machine-disks)</li>|


## <a name="azure-block-blob-page-blob-and-file-naming-conventions"></a>Convenciones de nomenclatura de archivos, blobs en páginas y blobs en bloques de Azure

| Entidad                                       | Convenciones                                                                                                                                                                                                                                                                                                               |
|----------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nombres de contenedor de blob en bloques y blob en páginas <br> Nombres de recursos compartidos de archivos de Azure | Debe ser un nombre DNS válido que tenga entre 3 y 63 caracteres. <br>  Debe empezar por una letra o un número. <br> Solo puede contener letras minúsculas, números y el guion (-). <br> Los caracteres de guión (-) deben estar inmediatamente precedidos y seguidos por una letra o un número. <br> No se permiten guiones consecutivos en nombres. |
| Nombres de archivos y directorios para archivos de Azure     |<li> No distingue mayúsculas y minúsculas, mantiene las mayúsculas y minúsculas, y no debe superar los 255 caracteres. </li><li> No puede terminar en una barra diagonal (/). </li><li>Si se proporciona, se quitará automáticamente. </li><li> No se permiten los caracteres siguientes: <code>" \\ / : \| < > * ?</code></li><li> Los caracteres de URL reservadas deben convertirse correspondientemente. </li><li> No se permiten caracteres no válidos en la ruta de acceso de la dirección URL. Los puntos de código como \\uE000 no son caracteres Unicode válidos. Tampoco se permiten algunos caracteres ASCII o Unicode, como los caracteres de control (0x00 a 0x1F, \\u0081, etc.). Para conocer las reglas que rigen las cadenas Unicode en HTTP/1.1, consulte RFC 2616, sección 2.2: Basic Rules y RFC 3987. </li><li> No se permiten los siguientes nombres de archivo: LPT1, LPT2, LPT3, LPT4, LPT5, LPT6, LPT7, LPT8, LPT9, COM1, COM2, COM3, COM4, COM5, COM6, COM7, COM8, COM9, PRN, AUX, NUL, CON, CLOCK$, carácter de punto (.) y caracteres de dos puntos (..).</li>|
| Nombres de blob para blob en bloques y blob en páginas      | Los nombres de blob distinguen mayúsculas de minúsculas y pueden contener cualquier combinación de caracteres. <br> Un nombre de blob debe tener entre 1 y 1024 caracteres. <br> Los caracteres de URL reservadas deben convertirse correspondientemente. <br>El número de segmentos de ruta de acceso que componen el nombre del blob no puede superar los 254. Un segmento de ruta de acceso es la cadena entre caracteres delimitadores consecutivos (por ejemplo, la barra diagonal "/") que se corresponden con el nombre de un directorio virtual. |

## <a name="managed-disk-naming-conventions"></a>Convenciones de nomenclatura de discos administrados

| Entidad | Convenciones                                             |
|-------------------|-----------------------------------------------------------|
| Nombres de discos administrados       | <li> El nombre debe tener una longitud de entre 1 y 80 caracteres. </li><li> El nombre debe comenzar con una letra o un número y terminar con una letra, un número o un carácter de subrayado. </li><li> El nombre solo puede contener letras, números, caracteres de subrayado, puntos o guiones. </li><li>   El nombre no debe tener espacios ni `/`.                                              |

## <a name="next-steps"></a>Pasos siguientes

- Revise los [requisitos del sistema de Azure Data Box Disk](data-box-disk-system-requirements.md).
