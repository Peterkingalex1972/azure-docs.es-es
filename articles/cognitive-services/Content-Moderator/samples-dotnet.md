---
title: 'Ejemplos de código: Content Moderator, .NET'
titleSuffix: Azure Cognitive Services
description: Uso de Content Moderator en sus aplicaciones de .NET mediante el SDK.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0c7db3c48f1ff7a141fda84caaad84ac8ff7f85d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564423"
---
# <a name="content-moderator-net-sdk-samples"></a>Ejemplos del SDK de .NET de Content Moderator

En la lista siguiente se incluyen vínculos a ejemplos de código compilados mediante el SDK de Azure Content Moderator para .NET.

## <a name="moderation"></a>Moderación

- **Moderación de imágenes**: [evalúe una imagen en busca de contenido explícito y para adultos, texto y caras](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Consulte el [inicio rápido](image-moderation-quickstart-dotnet.md).
- **Imágenes personalizadas**: [modere con listas de imágenes personalizadas](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Consulte el [inicio rápido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Hay un límite máximo de **5 listas de imágenes** y cada una de ellas **no debe superar las 10 000 imágenes**.
>

- **Moderación de texto**: [Pantalla de texto para blasfemias y datos personales](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Consulte el [inicio rápido](text-moderation-quickstart-dotnet.md).
- **Términos personalizados**: [modere con listas de términos personalizados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Consulte el [inicio rápido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Hay un límite máximo de **5 listas de términos** y cada lista **no debe superar los 10 000 términos**.
>

- **Moderación de vídeo**: [examine un vídeo para buscar contenido explícito y para adultos y obtener resultados](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Consulte el [inicio rápido](video-moderation-api.md).

## <a name="review"></a>Revisión

- **Trabajos con imágenes**: [inicie un trabajo de moderación que examine y cree revisiones](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Consulte el [inicio rápido](moderation-jobs-quickstart-dotnet.md).
- **Revisiones de imágenes**: [cree revisiones con intervención humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Consulte el [inicio rápido](moderation-reviews-quickstart-dotnet.md).
- **Revisiones de vídeos**: [cree revisiones de vídeo con intervención humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Consulte el [inicio rápido](video-reviews-quickstart-dotnet.md).
- **Revisiones de transcripciones de vídeos**: [cree revisiones de transcripciones de vídeos con intervención humana](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Consulte el [inicio rápido](video-reviews-quickstart-dotnet.md).

Consulte todos los ejemplos de .NET en los [ejemplos de .NET para Content Moderator en GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
