---
title: 'Inicio rápido: Publicación de la base de conocimiento en QnA Maker con REST y Python'
titleSuffix: Azure Cognitive Services
description: En este inicio rápido basado en REST de Python se explica el proceso de publicación de la base de conocimiento que le permite insertar la versión más reciente probada en un índice de Azure Search dedicado que representa la base de conocimiento publicada. También se crea un punto de conexión al que puede llamar en su aplicación o bot de chat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: a2db22334bace43cd29584c5931a5a7d08afaf78
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559751"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-python"></a>Inicio rápido: Publicación de una base de conocimiento en QnA Maker con Python

En esta guía de inicio rápido basada en REST se describe la publicación mediante programación de una base de conocimiento (KB). La publicación inserta la versión más reciente de la base de conocimiento en un índice de Azure Search dedicado y crea un punto de conexión que se puede llamar en su aplicación o bot de chat.

En esta guía de inicio rápido se llama a las siguientes API de QnA Maker:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): esta API no requiere ninguna información en el cuerpo de la solicitud.

## <a name="prerequisites"></a>Requisitos previos

* [Python 3.7](https://www.python.org/downloads/)
* Debe tener un servicio QnA Maker. Para recuperar la clave, seleccione Claves en Administración de recursos en el panel.
* Identificador de la base de conocimiento (KB) de QnA Maker encontrado en la dirección URL en el parámetro de cadena de consulta de KBID, como se muestra a continuación.

    ![Identificador de base de conocimiento de QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Si aún no tiene una base de conocimiento, puede crear una de ejemplo para usar en este inicio rápido: [Creación de una base de conocimiento](create-new-kb-nodejs.md)

> [!NOTE] 
> Los archivos de la solución completa están disponibles en el repositorio [**Azure-Samples/cognitive-services-qnamaker-python** de GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-python/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-knowledge-base-python-file"></a>Creación de un archivo de Python de base de conocimiento

Cree un archivo llamado `publish-kb-3x.py`.

## <a name="add-the-required-dependencies"></a>Incorporación de las dependencias necesarias

En la parte superior de `publish-kb-3x.py`, agregue las líneas siguientes para agregar las dependencias necesarias al proyecto:

[!code-python[Add the required dependencies](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=1-1 "Add the required dependencies")]

## <a name="add-required-constants"></a>Incorporación de las constantes necesarias

Después de las dependencias necesarias anteriores, agregue las constantes necesarias para acceder a QnA Maker. Reemplace los valores por los suyos.

[!code-python[Add the required constants](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=5-15 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Incorporación de una solicitud POST para publicar la base de conocimiento

Tras las constantes requeridas, agregue el siguiente código, que realiza una solicitud HTTPS a QnA Maker API para publicar una base de conocimiento y recibe la respuesta:

[!code-python[Add a POST request to publish knowledge base](~/samples-qnamaker-python/documentation-samples/quickstarts/publish-knowledge-base/publish-kb-3x.py?range=17-26 "Add a POST request to publish knowledge base")]

La llamada API devuelve un estado 204 en caso de publicación correcta sin ningún contenido en el cuerpo de la respuesta. El código agrega el contenido para las respuestas 204.

Para cualquier otra respuesta, esa respuesta se devuelve sin modificar.

## <a name="build-and-run-the-program"></a>Compilar y ejecutar el programa

Escriba el siguiente comando en una línea de comandos para ejecutar el programa. Este enviará la solicitud a QnA Maker API para publicar la base de conocimiento y, después, imprimirá 204 en el caso de aciertos o errores.

```bash
python publish-kb-3x.py
```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Pasos siguientes

Después de publicar la base de conocimiento, es preciso que la dirección URL del punto de conexión [ genere una respuesta](../Tutorials/create-publish-answer.md#generating-an-answer). 

> [!div class="nextstepaction"]
> [Referencia de QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)

[Introducción de QnA Maker](../Overview/overview.md)
