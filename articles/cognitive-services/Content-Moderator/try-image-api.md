---
title: 'Moderación de imágenes con la consola de API: Content Moderator'
titleSuffix: Azure Cognitive Services
description: Use Image Moderation API en Azure Content Moderator para iniciar flujos de trabajo de moderación de examen y revisión de contenido de imágenes.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 20353d488cba255925d21017b77d1def2d06b30b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68561258"
---
# <a name="moderate-images-from-the-api-console"></a>Moderación de imágenes desde la consola de API

Use [Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c) de Azure Content Moderator para iniciar flujos de trabajo de moderación de examen y revisión de contenido de imágenes. El trabajo de moderación examina el contenido en busca de palabras soeces y también lo compara con listas negras compartidas y personalizadas.

## <a name="use-the-api-console"></a>Uso de la consola de API
Antes de probar la API en la consola en línea, necesita la clave de suscripción. Se encuentra en la pestaña **Configuración**, en el cuadro **Ocp-Apim-Subscription-Key**. Para más información, consulte [Información general](overview.md).

1. Vaya a la [referencia de Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c).

   Se abre la página de moderación de imágenes **Image - Evaluate** (Imagen: evaluar).

2. En **Open API testing console** (Abrir consola de pruebas de API), seleccione la región que mejor describa su ubicación. 

   ![Selección de la región en la página Try Image - Evaluate (Probar imagen: evaluar)](images/test-drive-region.png)
  
   Se abre la consola de API **Image - Evaluate** (Imagen: evaluar).

3. En el cuadro **Ocp-Apim-Subscription-Key**, especifique la clave de suscripción.

   ![Clave de suscripción de la consola Try Image - Evaluate (Probar imagen: evaluar)](images/try-image-api-1.PNG)

4. En el cuadro **Request body** (Cuerpo de la solicitud), use la imagen de ejemplo predeterminada o especifique una imagen para examinar. Puede enviar la imagen como datos de bits binarios o especificar para ella una dirección URL de acceso público. 

   En este ejemplo, use la ruta de acceso proporcionada en el cuadro **Request body** (Cuerpo de la solicitud) y, a continuación, seleccione **Send** (Enviar). 

   ![Cuerpo de solicitud de la consola Try Image - Evaluate (Probar imagen: evaluar)](images/try-image-api-2.PNG)

   Esta es la imagen en esa dirección URL:

   ![Imagen de ejemplo de la consola Try Image - Evaluate (Probar imagen: evaluar)](images/sample-image.jpg) 

5. Seleccione **Enviar**.

6. La API devuelve una puntuación de probabilidad para cada clasificación. También devuelve una determinación de si la imagen cumple las condiciones (**true** o **false**). 

   ![Puntuación de probabilidad y determinación de condición de la consola Try Image - Evaluate (Probar imagen: evaluar)](images/try-image-api-3.PNG)

## <a name="face-detection"></a>Detección de caras

Se puede usar Image Moderation API para buscar caras en una imagen. Esta opción puede ser útil cuando le preocupa la privacidad y quiere impedir que una determinada cara se publique en su plataforma. 

1. En la [referencia de Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), en el menú izquierdo, en **Image** (Imagen), seleccione **Find Faces** (Buscar caras). 

   Se abre la página **Image - Find Faces** (Imagen: buscar caras).

2. En **Open API testing console** (Abrir consola de pruebas de API), seleccione la región que mejor describa su ubicación. 

   ![Selección de la región en la página Try Image - Find Faces (Probar imagen: buscar caras)](images/test-drive-region.png)

   Se abre la consola de API **Image - Find Faces** (Imagen: buscar caras).

3. Especifique una imagen para examinar. Puede enviar la imagen como datos de bits binarios o especificar para ella una dirección URL de acceso público. En este ejemplo se crea un vínculo a una imagen que se usa en una historia de la CNN.

   ![Imagen de ejemplo de Try Image - Find Faces (Probar imagen: buscar caras)](images/try-image-api-face-image.jpg)

   ![Solicitud de ejemplo de Try Image - Find Faces (Probar imagen: buscar caras)](images/try-image-api-face-request.png)

4. Seleccione **Enviar**. En este ejemplo, la API busca dos caras y devuelve sus coordenadas en la imagen.

   ![Cuadro de contenido de respuesta de ejemplo de Try Image - Find Faces (Probar imagen: buscar caras)](images/try-image-api-face-response.png)

## <a name="text-detection-via-ocr-capability"></a>Detección de texto mediante la funcionalidad OCR

Puede usar la funcionalidad OCR de Content Moderator para detectar el texto de las imágenes.

1. En la [referencia de Image Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c), en el menú izquierdo, en **Image** (Imagen), seleccione **OCR**. 

   Se abre la página **Image - OCR** (Imagen: OCR).

2. En **Open API testing console** (Abrir consola de pruebas de API), seleccione la región que mejor describa su ubicación. 

   ![Selección de la región de la página Image - OCR (Imagen: OCR)](images/test-drive-region.png)

   Se abre la consola de API **Image - OCR** (Imagen: OCR).

3. En el cuadro **Ocp-Apim-Subscription-Key**, especifique la clave de suscripción.

4. En el cuadro **Request body** (Cuerpo de la solicitud), use la imagen de ejemplo predeterminada. Esta es la misma imagen que se usa en la sección anterior.

5. Seleccione **Enviar**. El texto extraído se muestra en JSON:

   ![Cuadro de contenido de respuesta de ejemplo de Image - OCR (Imagen: OCR)](images/try-image-api-ocr.PNG)

## <a name="next-steps"></a>Pasos siguientes

Use la API REST en el código o empiece con el [inicio rápido de .NET para la moderación de imágenes](image-moderation-quickstart-dotnet.md) para integrar la aplicación.
