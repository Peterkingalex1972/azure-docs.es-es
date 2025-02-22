---
title: 'Inicio rápido: Publicación de la base de conocimiento en QnA Maker con REST y Java'
titleSuffix: Azure Cognitive Services
description: En este inicio rápido basado en REST de Java se explica el proceso de publicación de la base de conocimiento que le permite insertar la versión más reciente probada en un índice de Azure Search dedicado que representa la base de conocimiento publicada. También se crea un punto de conexión al que puede llamar en su aplicación o bot de chat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 4252ce3e0dfdaf9033221d86b0aacc728783b75b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559765"
---
# <a name="quickstart-publish-a-knowledge-base-in-qna-maker-using-java"></a>Inicio rápido: Publicación de una base de conocimiento en QnA Maker con Java

En esta guía de inicio rápido basada en REST se describe la publicación mediante programación de una base de conocimiento (KB). La publicación inserta la versión más reciente de la base de conocimiento en un índice de Azure Search dedicado y crea un punto de conexión que se puede llamar en su aplicación o bot de chat.

En esta guía de inicio rápido se llama a las siguientes API de QnA Maker:
* [Publicar](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish): esta API no requiere ninguna información en el cuerpo de la solicitud.

## <a name="prerequisites"></a>Requisitos previos

* [JDK SE](https://aka.ms/azure-jdks) (Kit de desarrollo de Java, Standard Edition)
* Este ejemplo utiliza el [cliente HTTP](https://hc.apache.org/httpcomponents-client-ga/) Apache desde HTTP Components. Es preciso agregar las siguientes bibliotecas de cliente HTTP Apache al proyecto: 
    * httpclient-4.5.3.jar
    * httpcore-4.4.6.jar
    * commons-logging-1.2.jar
* [Visual Studio Code](https://code.visualstudio.com/)
* Debe tener un [servicio QnA Maker](../How-To/set-up-qnamaker-service-azure.md). Para recuperar la clave, seleccione **Claves** en **Administración de recursos** en el panel de Azure del recurso de QnA Maker. . 
* Identificador de la base de conocimiento (KB) de QnA Maker encontrado en la dirección URL en el parámetro de cadena de consulta de KBID, como se muestra a continuación.

    ![Identificador de base de conocimiento de QnA Maker](../media/qnamaker-quickstart-kb/qna-maker-id.png)

    Si aún no tiene una base de conocimiento, puede crear una de ejemplo para usar en este inicio rápido: [Creación de una base de conocimiento](create-new-kb-csharp.md)

> [!NOTE] 
> Los archivos de la solución completa están disponibles en el repositorio [**Azure-Samples/cognitive-services-qnamaker-java** de GitHub](https://github.com/Azure-Samples/cognitive-services-qnamaker-java/tree/master/documentation-samples/quickstarts/publish-knowledge-base).

## <a name="create-a-java-file"></a>Creación de un archivo Java

Abra VSCode y cree un archivo llamado `PublishKB.java`.

## <a name="add-the-required-dependencies"></a>Incorporación de las dependencias necesarias

En la parte superior de `PublishKB.java`, encima de la clase, agregue las líneas siguientes para agregar las dependencias necesarias al proyecto:

[!code-java[Add the required dependencies](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=1-13 "Add the required dependencies")]

## <a name="create-publishkb-class-with-main-method"></a>Creación de la clase PublishKB con el método main

Después de las dependencias agregue la siguiente clase:

```Go
public class PublishKB {

    public static void main(String[] args) 
    {
    }
}
```

## <a name="add-required-constants"></a>Incorporación de las constantes necesarias

En el método **main**, agregue las constantes necesarias para acceder a QnA Maker. Reemplace los valores por los suyos.

[!code-java[Add the required constants](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=27-30 "Add the required constants")]

## <a name="add-post-request-to-publish-knowledge-base"></a>Incorporación de una solicitud POST para publicar la base de conocimiento

Tras las constantes requeridas, agregue el siguiente código, que realiza una solicitud HTTPS a QnA Maker API para publicar una base de conocimiento y recibe la respuesta:

[!code-java[Add a POST request to publish knowledge base](~/samples-qnamaker-java/documentation-samples/quickstarts/publish-knowledge-base/PublishKB.java?range=32-44 "Add a POST request to publish knowledge base")]

La llamada API devuelve un estado 204 en caso de publicación correcta sin ningún contenido en el cuerpo de la respuesta. El código agrega el contenido para las respuestas 204.

Para cualquier otra respuesta, esa respuesta se devuelve sin modificar.

## <a name="build-and-run-the-program"></a>Compilar y ejecutar el programa

Compile y ejecute el programa desde la línea de comandos. Este enviará automáticamente la solicitud a QnA Maker API y luego la imprimirá en la ventana de la consola.

1. Compile el archivo:

    ```bash
    javac -cp "lib/*" PublishKB.java
    ```

1. Ejecute el archivo:

    ```bash
    java -cp ".;lib/*" PublishKB
    ```

[!INCLUDE [Clean up files and knowledge base](../../../../includes/cognitive-services-qnamaker-quickstart-cleanup-resources.md)] 

## <a name="next-steps"></a>Pasos siguientes

Después de publicar la base de conocimiento, es preciso que la dirección URL del punto de conexión [ genere una respuesta](../Tutorials/create-publish-answer.md#generating-an-answer).  

> [!div class="nextstepaction"]
> [Referencia de QnA Maker (V4) REST API](https://go.microsoft.com/fwlink/?linkid=2092179)
