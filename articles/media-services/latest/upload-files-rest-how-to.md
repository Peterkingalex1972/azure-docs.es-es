---
title: Carga de archivos en una cuenta de Azure Media Services mediante REST | Microsoft Docs
description: Aprenda a obtener contenido multimedia en Media Services mediante la creación y carga de recursos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: a6f872880b61a5bd9510abda2f15e2edea16e940
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703876"
---
# <a name="upload-files-into-a-media-services-account-using-rest"></a>Carga de archivos en una cuenta de Media Services mediante API de REST

En Media Services, cargue los archivos digitales en un contenedor de blobs asociado con un recurso. La entidad [Recurso](https://docs.microsoft.com/rest/api/media/operations/asset) puede contener archivos de vídeo, audio, imágenes, colecciones de miniaturas, pistas de texto y subtítulos (y los metadatos sobre estos archivos). Una vez cargados los archivos en el contenedor del recurso, el contenido se almacena de forma segura en la nube para un posterior procesamiento y streaming.

En este artículo se muestra cómo cargar un archivo local con REST.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos descritos en este tema, ha de:

- Revisar el [concepto de recursos](assets-concept.md).
- [Configuración de Postman para llamadas API REST de Azure Media Services](media-rest-apis-with-postman.md).
    
    Asegúrese de seguir el último paso en el tema [Obtención del token de Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="create-an-asset"></a>Creación de un recurso

En esta sección se muestra cómo crear un nuevo recurso.

1. Seleccione **Recurso** -> **Create or update an Asset** (Crear o actualizar un recurso).
2. Presione **Enviar**.

    ![Creación de un recurso](./media/upload-files/postman-create-asset.png)

Verá la **respuesta** con la información sobre el recurso recién creado.

## <a name="get-a-sas-url-with-read-write-permissions"></a>Obtener una dirección URL de SAS con permisos de lectura y escritura 

En esta sección se muestra cómo obtener una dirección URL de SAS que se ha generado para el recurso creado. La dirección URL de SAS se ha creado con permisos de lectura y escritura, y se puede usar para cargar archivos digitales en el contenedor de recursos.

1. Seleccione **Recursos** -> **List the Asset URLs** (Mostrar las direcciones URL de recurso).
2. Presione **Enviar**.

    ![Cargar un archivo](./media/upload-files/postman-create-sas-locator.png)

Verá la **respuesta** con la información sobre las direcciones URL del recurso. Copie la primera dirección URL y úsela para cargar el archivo.

## <a name="upload-a-file-to-blob-storage-using-the-upload-url"></a>Carga de un archivo a Blob Storage mediante la dirección URL de carga

Use los SDK o API de Azure Storage (por ejemplo, la [API REST de Azure Storage](../../storage/common/storage-rest-api-auth.md), el [SDK para JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md) o el [SDK para .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Carga, codificación y transmisión de vídeos con REST](stream-files-tutorial-with-rest.md)
