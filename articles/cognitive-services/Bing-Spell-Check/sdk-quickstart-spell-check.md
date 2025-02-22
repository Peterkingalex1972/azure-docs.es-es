---
title: 'Inicio rápido: Revisión ortográfica con el SDK de Bing Spell Check para C#'
titleSuffix: Azure Cognitive Services
description: Introducción al uso de la API REST de Bing Spell Check para la revisión ortográfica y gramatical.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: d98d00275cbd89702e4bae0c93aa262805617e59
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500779"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-sdk-for-c"></a>Inicio rápido: Revisión ortográfica con el SDK de Bing Spell Check para C#

Use este inicio rápido para empezar la revisión ortográfica con el SDK de Bing Spell Check para C#. Aunque Bing Spell Check tiene una API REST compatible con la mayoría de los lenguajes de programación, el SDK proporciona una forma sencilla de integrar el servicio en sus aplicaciones. El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/samples/SpellCheck).

## <a name="application-dependencies"></a>Dependencias de aplicaciones

* Cualquier edición de [Visual Studio 2017 o posterior](https://visualstudio.microsoft.com/downloads/).
* El [paquete NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.SpellCheck) de Bing Spell Check

Para agregar el SDK de Bing Spell Check, seleccione **Administración de paquetes de NuGet** en el **Explorador de soluciones** en Visual Studio. Agregue el paquete `Microsoft.Azure.CognitiveServices.Language.SpellCheck`. El paquete también instala las siguientes dependencias:

* Microsoft.Rest.ClientRuntime
* Microsoft.Rest.ClientRuntime.Azure
* Newtonsoft.Json

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Creación e inicialización de la aplicación

1. Cree una solución de consola de C# en Visual Studio. A continuación, agregue la siguiente instrucción `using`.
    
    ```csharp
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck;
    using Microsoft.Azure.CognitiveServices.Language.SpellCheck.Models;
    ```

2. Cree una clase. A continuación, cree una función asincrónica llamada `SpellCheckCorrection()` que toma una clave de suscripción y envía la solicitud de revisión ortográfica.

3. Cree una instancia del cliente mediante la creación de un objeto `ApiKeyServiceClientCredentials`. 

    ```csharp
    public static class SpellCheckSample{
        public static async Task SpellCheckCorrection(string key){
            var client = new SpellCheckClient(new ApiKeyServiceClientCredentials(key));
        }
        //...
    }
    ```

## <a name="send-the-request-and-read-the-response"></a>Envío de la solicitud y lectura de la respuesta

1. En la función creada anteriormente, realice los pasos siguientes. Envíe la solicitud de revisión ortográfica al cliente. Agregue el texto que se va a revisar al parámetro `text` y establezca el modo en `proof`.  
    
    ```csharp
    var result = await client.SpellCheckerWithHttpMessagesAsync(text: "Bill Gatas", mode: "proof");
    ```

2. Obtenga el primer resultado de la revisión ortográfica, si lo hay. Imprima la primera palabra mal escrita (token) devuelta, el tipo de token y el número de sugerencias.

    ```csharp
    if (firstspellCheckResult != null){
        var firstspellCheckResult = result.Body.FlaggedTokens.FirstOrDefault();
    
        Console.WriteLine("SpellCheck Results#{0}", result.Body.FlaggedTokens.Count);
        Console.WriteLine("First SpellCheck Result token: {0} ", firstspellCheckResult.Token);
        Console.WriteLine("First SpellCheck Result Type: {0} ", firstspellCheckResult.Type);
        Console.WriteLine("First SpellCheck Result Suggestion Count: {0} ", firstspellCheckResult.Suggestions.Count);
    }
    ```

3. Obtenga la primera corrección sugerida, si la hay. Imprima la puntuación de la sugerencia y la palabra sugerida. 

    ```csharp
            var suggestions = firstspellCheckResult.Suggestions;

            if (suggestions?.Count > 0)
            {
                var firstSuggestion = suggestions.FirstOrDefault();
                Console.WriteLine("First SpellCheck Suggestion Score: {0} ", firstSuggestion.Score);
                Console.WriteLine("First SpellCheck Suggestion : {0} ", firstSuggestion.Suggestion);
            }
   }

## Next steps

> [!div class="nextstepaction"]
> [Create a single page web-app](tutorials/spellcheck.md)

- [What is the Bing Spell Check API?](overview.md)
- [Bing Spell Check C# SDK reference guide](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/bingspellcheck?view=azure-dotnet)