---
title: 'Referencia de API: Content Moderator'
titleSuffix: Azure Cognitive Services
description: Aprenda sobre las distintas API de moderación y revisión de contenido de Content Moderator.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 05/29/2019
ms.author: sajagtap
ms.openlocfilehash: 3ad911a95dbe6209fcf55adcac3cf2937b06d1ff
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565616"
---
# <a name="content-moderator-api-reference"></a>Referencia de API de Content Moderator

Puede empezar a trabajar con las API de Azure Content Moderator de las siguientes maneras:

- En Azure Portal, [suscríbase a Content Moderator API](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator).
- Consulte [Prueba de Content Moderator en la web](quick-start.md) para registrarse en la [herramienta de revisión de Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

## <a name="moderation-apis"></a>API de moderación

Puede usar las siguientes API de Content Moderator para configurar los flujos de trabajo posteriores a la moderación.

| DESCRIPCIÓN | Referencia |
| -------------------- |-------------|
| **Image Moderation API**<br /><br />Examine imágenes y detecte posible contenido para adultos o explícito mediante etiquetas, puntuaciones de confianza y otra información extraída. <br /><br />Use esta información para publicar, rechazar o revisar el contenido del flujo de trabajo posterior a la moderación. <br /><br />| [Referencia de Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Image Moderation API reference")   |
| **Text Moderation API**<br /><br />Examine contenido de texto. Las blasfemias y los datos personales se devuelven. <br /><br />Use esta información para publicar, rechazar o revisar el contenido del flujo de trabajo posterior a la moderación.<br /><br /> | [Referencia de Text Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Text Moderation API reference")   |
| **Video Moderation API**<br /><br />Examine vídeos y detecte posible contenido para adultos y explícito. <br /><br />Use esta información para publicar, rechazar o revisar el contenido del flujo de trabajo posterior a la moderación.<br /><br /> | [Introducción a Video Moderation API](video-moderation-api.md "Video Moderation API overview")   |
| **List Management API**<br /><br />Cree y administre listas personalizadas de exclusión o inclusión de texto e imágenes. Si están habilitadas, las operaciones **Image - Match** (Imagen: coincidir) y **Text - Screen** (Texto: pantalla) buscan coincidencias parciales del contenido enviado en las listas personalizadas. <br /><br />Para mejorar la eficacia, puede omitir el paso de moderación basado en el aprendizaje automático.<br /><br /> | [Referencia de List Management API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "List Management API reference")   |

## <a name="review-apis"></a>Revisión de las API

Review APIs tienen los siguientes componentes:

| DESCRIPCIÓN | Referencia |
| -------------------- |-------------|
| **Trabajos**<br /><br /> Inicie flujos de trabajo de examen y revisión para el contenido de imagen y texto. Un trabajo de moderación examina el contenido mediante Image Moderation API y Text Moderation API. Los trabajos de moderación usan los flujos de trabajo definidos y predeterminados para generar revisiones. <br /><br />Después de que un moderador humano ha revisado las etiquetas asignadas automáticamente y los datos de predicción y de que ha enviado una decisión de moderación de contenido, Review API envía toda la información al punto de conexión de API.<br /><br /> | [Referencia de trabajo](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Job reference")   |
| **Revisiones**<br /><br />Use la herramienta de revisión para crear directamente revisiones de texto o imagen para moderadores humanos.<br /><br /> Después de que un moderador humano ha revisado las etiquetas asignadas automáticamente y los datos de predicción y de que ha enviado una decisión de moderación de contenido, Review API envía toda la información al punto de conexión de API.<br /><br /> | [Referencia de Review](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Review reference")   |
| **Flujos de trabajo**<br /><br />Cree, actualice y obtenga información detallada sobre los flujos de trabajo personalizados que crea el equipo. Los flujos de trabajo se definen mediante la herramienta de revisión. <br /> <br />Normalmente, los flujos de trabajo usan Content Moderator, pero también pueden usar algunas otras API que están disponibles como conectores en la herramienta de revisión.<br /><br /> | [Referencia de Workflow](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Workflow reference")   |