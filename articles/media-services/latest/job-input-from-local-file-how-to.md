---
title: Creación de una entrada de trabajo de Azure Media Services desde un archivo local | Microsoft Docs
description: En este tema se muestra cómo crear una entrada de trabajo a partir de un archivo local.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: 399f6724b8948c8e507bc50622a4fb65b2262491
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653956"
---
# <a name="create-a-job-input-from-a-local-file"></a>Creación de una entrada de trabajo a partir de un archivo local

En Media Services v3, cuando se envían trabajos para procesar los vídeos, es necesario indicar a Media Services dónde encontrar el vídeo de entrada. El vídeo de entrada se puede almacenar como un recurso de Media Services, en cuyo caso se crea un recurso de entrada basado en un archivo (almacenado localmente o en Azure Blob Storage). En este tema se muestra cómo crear una entrada de trabajo a partir de un archivo local. Para un ejemplo completo, consulte este [ejemplo de GitHub](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ejemplo de .NET

En el código siguiente se muestra cómo crear un recurso de entrada y usarlo como la entrada del trabajo. La función CreateInputAsset realiza las acciones siguientes:

* Crea el recurso.
* Obtiene una [dirección URL de SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) que se puede escribir en el [contenedor de almacenamiento](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) del recurso.
* Carga el archivo en el contenedor de almacenamiento mediante la dirección URL de SAS

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Códigos de error de trabajo

Consulte [Códigos de error](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Pasos siguientes

[Creación de una entrada de trabajo a partir de una dirección URL de HTTP(s)](job-input-from-http-how-to.md).
