---
title: Tutorial para comprobar la carga de datos de Azure Data Box Disk a la cuenta de almacenamiento | Microsoft Docs
description: Use este tutorial para obtener información sobre cómo comprobar los datos cargados desde Azure Data Box Disk a la cuenta de Azure Storage.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.localizationpriority: high
ms.date: 08/22/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: e36f009422307b3b70091775d2288ee710839172
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "70014000"
---
::: zone target="docs"

# <a name="tutorial-verify-data-upload-from-azure-data-box-disk"></a>Tutorial: Comprobación de la carga de datos desde Azure Data Box Disk

Este es el último tutorial de la serie: Implementación de Azure Data Box Disk. En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * Comprobación de la carga de datos en Azure
> * Eliminación de los datos de Data Box Disk

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que ha completado [Tutorial: Devolución de Azure Data Box Disk](data-box-disk-deploy-picked-up.md).


## <a name="verify-data-upload-to-azure"></a>Comprobación de la carga de datos en Azure

Una vez que los discos son recogidos por el transportista, el estado del pedido en el portal se actualiza a **Picked up** (Recogido). También se muestra un identificador de seguimiento.

![Discos recogidos](media/data-box-disk-deploy-picked-up/data-box-portal-pickedup.png)

Cuando Microsoft recibe y examina el disco, el estado del trabajo se actualiza a **Received** (Recibido). 

![Discos recibidos](media/data-box-disk-deploy-picked-up/data-box-portal-received.png)

Los datos se copian automáticamente una vez que los discos se conectan a un servidor en el centro de datos de Azure. Dependiendo del tamaño de los datos, la operación de copia puede tardar horas o días en completarse. Puede supervisar el progreso del trabajo de copia en el portal.

Una vez finalizada la copia, el estado del pedido se actualiza a **Completed** (Completado).

![Copia de datos finalizada](media/data-box-disk-deploy-picked-up/data-box-portal-completed.png)

Si la copia se completa con errores, vea [cómo solucionar errores de carga](data-box-disk-troubleshoot-upload.md).

Compruebe que los datos estén en las cuentas de almacenamiento antes de eliminarlos del origen. Los datos pueden estar en:

- Sus cuentas de Azure Storage. Al copiar los datos en Data Box, dependiendo del tipo, estos se cargan en una de las siguientes rutas de acceso de la cuenta de Azure Storage.

  - Para los blobs en bloques y los blobs en páginas: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Para Azure Files: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Como alternativa, puede ir a su cuenta de almacenamiento de Azure en Azure Portal e ir desde allí.

- Sus grupos de recursos de disco administrados. Al crear discos administrados, los discos duros virtuales se cargan como blobs en páginas y se convierten en discos administrados. Los discos administrados se conectan a los grupos de recursos especificados en el momento de creación del pedido.

  - Si la copia en los discos administrados de Azure se realizó correctamente, puede ir a **Detalles del pedido** en Azure Portal y tomar nota de los grupos de recursos especificados para los discos administrados.

      ![Visualización de los detalles de pedido](media/data-box-disk-deploy-picked-up/order-details-resource-group.png)

    Vaya al grupo de recursos anotado y busque los discos administrados.

      ![Grupo de recursos para discos administrados](media/data-box-disk-deploy-picked-up/resource-group-attached-managed-disk.png)

  - Si copió un VHDX o un disco duro virtual dinámico o de diferenciación, el VHD o VHDX se carga en la cuenta de almacenamiento provisional como si fuera un blob en bloques. Vaya a su almacenamiento provisional **Cuenta de almacenamiento > Blobs** y seleccione el contenedor adecuado (SSD estándar, HDD estándar o SSD prémium). Los VHD/VHDX deberían aparecer como blobs en bloques en su cuenta de almacenamiento provisional.

Para comprobar que los datos se han cargado en Azure, realice los pasos siguientes:

1. Vaya a la cuenta de almacenamiento asociada al pedido de disco.
2. Vaya a **Blob service > Examinar blobs**. Se presenta la lista de contenedores. En la subcarpeta que creó bajo las carpetas *BlockBlob* y *PageBlob*, se crean contenedores con el mismo nombre en la cuenta de almacenamiento.
    Si los nombres de carpeta no se ajustan a las convenciones de nomenclatura de Azure, se producirá un error en la carga de datos en Azure.

4. Para comprobar que ha cargado todo el conjunto de datos, use el Explorador de Microsoft Azure Storage. Asocie la cuenta de almacenamiento correspondiente al pedido de alquiler de disco y, a continuación, examine la lista de contenedores de blobs. Seleccione un contenedor, haga clic en **... Más** y, a continuación, haga clic en **Folder statistics** (Estadísticas de carpeta). En el panel **Actividades**, se muestran las estadísticas de esa carpeta incluido el número de blobs y el tamaño total de los blobs. El tamaño total de los blobs en bytes debe coincidir con el tamaño del conjunto de datos.

    ![Estadísticas de carpeta en el Explorador de Storage](media/data-box-disk-deploy-picked-up/folder-statistics-storage-explorer.png)

## <a name="erasure-of-data-from-data-box-disk"></a>Eliminación de los datos de Data Box Disk

Una vez que se ha completado la copia y ha comprobado que los datos están en la cuenta de almacenamiento de Azure, los discos se borran de forma segura según el estándar NIST.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido sobre temas relacionados con Azure Data Box Disk; por ejemplo:

> [!div class="checklist"]
> * Comprobación de la carga de datos en Azure
> * Eliminación de los datos de Data Box Disk


Avance hasta el siguiente procedimiento para obtener información sobre cómo administrar Data Box Disk mediante Azure Portal.

> [!div class="nextstepaction"]
> [Uso de Azure Portal para administrar Azure Data Box Disk](./data-box-portal-ui-admin.md)

::: zone-end

::: zone target="chromeless"

# <a name="verify-data-upload-to-azure"></a>Comprobación de la carga de datos en Azure

Tras cargar los datos en Azure, compruebe que los datos están en las cuentas de almacenamiento antes de eliminarlos del origen. Los datos pueden estar en:

- Sus cuentas de Azure Storage. Al copiar los datos en Data Box, dependiendo del tipo, estos se cargan en una de las siguientes rutas de acceso de la cuenta de Azure Storage.

    - **Para blobs en bloques y blobs en páginas**: https://<nombre_de_la_cuenta_de_almacenamiento>.blob.core.windows.net/<containername>/files/a.txt

    - **Para Azure Files**: https://<nombre_de_la_cuenta_de_almacenamiento>.file.core.windows.net/<sharename>/files/a.txt

    Como alternativa, puede ir a su cuenta de almacenamiento de Azure en Azure Portal e ir desde allí.

- Sus grupos de recursos de disco administrados. Al crear discos administrados, los discos duros virtuales se cargan como blobs en páginas y se convierten en discos administrados. Los discos administrados se conectan a los grupos de recursos especificados en el momento de creación del pedido.

::: zone-end


