---
title: 'Metadatos con GenerateAnswer API: QnA Maker'
titleSuffix: Azure Cognitive Services
description: QnA Maker permite agregar metadatos, en forma de pares de clave-valor, a los conjuntos de preguntas y respuestas. Puede filtrar los resultados a las consultas de usuario y almacenar la información adicional que se puede usar en las conversaciones de seguimiento.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: diberry
ms.openlocfilehash: 18b901b429ee675726ef0e36535f1f97f4cdd076
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543000"
---
# <a name="get-an-answer-with-the-generateanswer-api-and-metadata"></a>Obtención de una respuesta con GenerateAnswer API y metadatos

Para obtener la respuesta prevista a la pregunta de un usuario, use GenerateAnswer API. Al publicar una base de conocimiento, puede consultar información sobre cómo usar esta API en la página **Publicar**. También puede configurar la API para filtrar las respuestas según las etiquetas de metadatos y probar la base de conocimiento desde el punto de conexión con el parámetro de cadena de consulta de prueba.

QnA Maker permite agregar metadatos, en forma de pares clave-valor, a los conjuntos de preguntas y respuestas. Luego puede usar esta información para filtrar los resultados de las consultas de usuario y para almacenar información adicional que se puede usar en las conversaciones de seguimiento. Para más información, consulte la [base de conocimiento](../Concepts/knowledge-base.md).

<a name="qna-entity"></a>

## <a name="store-questions-and-answers-with-a-qna-entity"></a>Almacenamiento de preguntas y respuestas con una entidad QnA

Resulta importante entender cómo QnA Maker almacena los datos de preguntas y respuestas. En la siguiente ilustración se muestra una entidad QnA:

![Ilustración de una entidad QnA](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

Cada entidad QnA tiene un identificador único y persistente. Puede usar el identificador para realizar actualizaciones en una entidad QnA determinada.

<a name="generateanswer-api"></a>

## <a name="get-answer-predictions-with-the-generateanswer-api"></a>Obtención de predicciones de respuestas con GenerateAnswer API

[GenerateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer) se usa en un bot o aplicación para consultar la base de conocimiento con una pregunta de usuario y obtener la mejor coincidencia de los conjuntos de preguntas y respuestas.

<a name="generateanswer-endpoint"></a>

## <a name="publish-to-get-generateanswer-endpoint"></a>Publicación para obtener el punto de conexión de GenerateAnswer 

Después de publicar la base de conocimiento, ya sea desde el [portal de QnA Maker](https://www.qnamaker.ai) o mediante la [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/publish), puede obtener los detalles de su punto de conexión de GenerateAnswer.

Para obtener los detalles del punto de conexión:
1. Inicie sesión en [https://www.qnamaker.ai](https://www.qnamaker.ai).
1. En **My knowledge bases** (Mis bases de conocimiento), seleccione **View Code** (Ver código) correspondiente a su base de conocimiento.
    ![Captura de pantalla de My knowledge bases (Mis bases de conocimiento)](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
1. Obtenga los detalles del punto de conexión de GenerateAnswer.

    ![Captura de los detalles del punto de conexión](../media/qnamaker-how-to-metadata-usage/view-code.png)

También puede obtener los detalles del punto de conexión en la pestaña **Settings** (Configuración) de la base de conocimiento.

<a name="generateanswer-request"></a>

## <a name="generateanswer-request-configuration"></a>Configuración de la solicitud GenerateAnswer

Para llamar a GenerateAnswer, se utiliza una solicitud HTTP POST. Para ver código de ejemplo que muestra cómo llamar a GenerateAnswer, consulte las [guías de inicio rápido](../quickstarts/csharp.md). 

La solicitud POST usa:

* Los [parámetros de URI](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#uri-parameters) obligatorios.
* La [propiedad del encabezado](https://docs.microsoft.com/azure/cognitive-services/qnamaker/quickstarts/get-answer-from-knowledge-base-nodejs#add-a-post-request-to-send-question-and-get-an-answer) obligatoria, `Authorization`, por seguridad.
* Las [propiedades del cuerpo](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train#feedbackrecorddto) obligatorias. 

La dirección URL de GenerateAnswer tiene el formato siguiente: 

```
https://{QnA-Maker-endpoint}/knowledgebases/{knowledge-base-ID}/generateAnswer
```

Recuerde establecer la propiedad del encabezado HTTP de `Authorization` con un valor de la cadena `EndpointKey` con un espacio final y, luego, la clave del punto de conexión que se encontró en la página **Configuración**.

Un cuerpo JSON de ejemplo tiene el siguiente aspecto:

```json
{
    "question": "qna maker and luis",
    "top": 6,
    "isTest": true,
    "scoreThreshold": 20,
    "strictFilters": [
    {
        "name": "category",
        "value": "api"
    }],
    "userId": "sd53lsY="
}
```

<a name="generateanswer-response"></a>

## <a name="generateanswer-response-properties"></a>Propiedades de la respuesta de GenerateAnswer

La [respuesta](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#successful-query) es un objeto JSON que incluye toda la información necesaria para mostrar la respuesta y el siguiente turno en la conversación, si está disponible.

```json
{
    "answers": [
        {
            "score": 28.54820341616869,
            "Id": 20,
            "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
            "source": "Custom Editorial",
            "questions": [
                "How can I integrate LUIS with QnA Maker?"
            ],
            "metadata": [
                {
                    "name": "category",
                    "value": "api"
                }
            ]
        }
    ]
}
```

## <a name="use-qna-maker-with-a-bot-in-c"></a>Uso de QnA Maker con un bot en C#

Bot Framework brinda acceso a las propiedades de QnA Maker:

```csharp
using Microsoft.Bot.Builder.AI.QnA;
var metadata = new Microsoft.Bot.Builder.AI.QnA.Metadata();
var qnaOptions = new QnAMakerOptions();

qnaOptions.Top = Constants.DefaultTop;
qnaOptions.ScoreThreshold = 0.3F;
var response = await _services.QnAServices[QnAMakerKey].GetAnswersAsync(turnContext, qnaOptions);
```

El bot de soporte técnico tiene [un ejemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-support/csharp_dotnetcore/Service/SupportBotService.cs#L418) con este código.

## <a name="use-qna-maker-with-a-bot-in-nodejs"></a>Uso de QnA Maker con un bot en Node.js

Bot Framework brinda acceso a las propiedades de QnA Maker:

```javascript
const { QnAMaker } = require('botbuilder-ai');
this.qnaMaker = new QnAMaker(endpoint);

// Default QnAMakerOptions
var qnaMakerOptions = {
    ScoreThreshold: 0.03,
    Top: 3
};
var qnaResults = await this.qnaMaker.getAnswers(stepContext.context, qnaMakerOptions);
```

El bot de soporte técnico tiene [un ejemplo](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/qnamaker-activelearning/javascript_nodejs/Helpers/dialogHelper.js#L36) con este código.

<a name="metadata-example"></a>

## <a name="use-metadata-to-filter-answers-by-custom-metadata-tags"></a>Uso de metadatos para filtrar las respuestas por etiquetas de metadatos personalizadas

Agregar los metadatos le permite filtrar las respuestas según estas etiquetas de metadatos. Agregue la columna de metadatos desde el menú **View Options** (Ver opciones). Para agregar los metadatos a la base de conocimiento, seleccione el icono **+** de los metadatos para agregar un par de metadatos. Este par consta de una clave y un valor.

![Captura de pantalla de incorporación de metadatos](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

<a name="filter-results-with-strictfilters-for-metadata-tags"></a>

## <a name="filter-results-with-strictfilters-for-metadata-tags"></a>Filtrar los resultados con strictFilters para las etiquetas de metadatos

Considere la pregunta del usuario "¿Cuándo cierra este hotel?", donde la intención está implícita para el restaurante "Paradise".

Puesto que los resultados solo son necesarios para el restaurante "Paradise", puede establecer un filtro en la llamada GenerateAnswer para los metadatos "Nombre del restaurante". Esto se muestra en el ejemplo siguiente:

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

<a name="keep-context"></a>

## <a name="use-question-and-answer-results-to-keep-conversation-context"></a>Uso de los resultados de las preguntas y respuestas para mantener el contexto de la conversación

La respuesta a GenerateAnswer contiene la información de los metadatos correspondientes al conjunto de preguntas y respuestas que coinciden. Puede usar esta información en la aplicación cliente para almacenar el contexto de la conversación anterior para su uso en conversiones posteriores. 

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

## <a name="match-questions-only-by-text"></a>Coincidencia solo de preguntas, por texto

De manera predeterminada, QnA Maker busca a través de preguntas y respuestas. Si quiere buscar solo en las preguntas, para generar una respuesta, utilice el objeto `RankerType=QuestionOnly` del cuerpo de POST de la solicitud GenerateAnswer.

Puede buscar en la base de conocimiento publicada, con `isTest=false`, o bien en la base de conocimiento de prueba, con `isTest=true`.

```json
{
  "question": "Hi",
  "top": 30,
  "isTest": true,
  "RankerType":"QuestionOnly"
}
```

## <a name="common-http-errors"></a>Errores HTTP comunes

|Código|Explicación|
|:--|--|
|2xx|Correcto|
|400|Los parámetros de la solicitud no son correctos, lo que significa que faltan parámetros necesarios, o bien que tienen un formato incorrecto o son demasiado grandes|
|400|El cuerpo de la solicitud es incorrecto, lo que significa que falta el código JSON, o bien que tiene un formato incorrecto o es demasiado grande|
|401|Clave no válida|
|403|Prohibido: no tiene los permisos correctos|
|404|KB no existe|
|410|Esta API está en desuso y ya no está disponible|

## <a name="next-steps"></a>Pasos siguientes

La página **Publicar** también proporciona información para generar una respuesta con [Postman](../Quickstarts/get-answer-from-kb-using-postman.md) y [cURL](../Quickstarts/get-answer-from-kb-using-curl.md). 

> [!div class="nextstepaction"]
> [Creación de una base de conocimientos](./create-knowledge-base.md)
