---
title: 'Encadenamiento de funciones en Durable Functions: Azure'
description: Obtenga información acerca de cómo ejecutar un ejemplo de Durable Functions que ejecute una secuencia de funciones.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 1168963c0698c6bdafe20babe2e5143585bf90a8
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087119"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Encadenamiento de funciones en Durable Functions: Hello Sequence de ejemplo

El encadenamiento de funciones hace referencia al patrón de ejecución de una secuencia de funciones en un orden concreto. A menudo la salida de una función tiene que aplicarse a la entrada de otra función. Este artículo describe la secuencia de encadenamiento que se crea al completar el inicio rápido de Durable Functions ([ C# ](durable-functions-create-first-csharp.md) o [JavaScript](quickstart-js-vscode.md)). Para más información sobre Durable Functions, consulte [Durable Functions patterns and technical concepts](durable-functions-concepts.md) (Patrones y conceptos técnicos de Durable Functions).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funciones

En este artículo se explican las funciones siguientes en la aplicación de ejemplo:

* `E1_HelloSequence`: una función de orquestador que llama a `E1_SayHello` varias veces en una secuencia. Almacena las salidas de las llamadas de `E1_SayHello` y registra los resultados.
* `E1_SayHello`: una función de actividad que antepone una cadena con "Hello".

En las siguientes secciones se explican la configuración y el código que se utilizan para el scripting C# y para JavaScript. Al final del artículo se muestra el código para el desarrollo de Visual Studio.

> [!NOTE]
> Durable Functions de JavaScript están disponibles solo para el entorno de ejecución de Functions 2.x.

## <a name="e1_hellosequence"></a>E1_HelloSequence

### <a name="functionjson-file"></a>Archivo function.json

Si utiliza Visual Studio Code o Azure Portal para el desarrollo, aquí tiene el contenido del archivo *function.json* para la función de orquestador. La mayoría de los archivos *function.json* de orquestador presentan un aspecto prácticamente idéntico a este.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/function.json)]

Lo importante es el tipo de enlace `orchestrationTrigger`. Todas las funciones de orquestador deben usar este tipo de desencadenador.

> [!WARNING]
> Para cumplir la regla de "ninguna E/S" de las funciones de orquestador, no use ningún enlace de entrada o salida al utilizar el enlace de desencadenador `orchestrationTrigger`.  Si se necesitan otros enlaces de entrada o salida, deberían utilizarse en el contexto de funciones `activityTrigger` en su lugar, a las que llama el orquestador.

### <a name="c-script-visual-studio-code-and-azure-portal-sample-code"></a>Script de C# (código de ejemplo de Azure Portal y Visual Studio Code)

El siguiente es el código fuente:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_HelloSequence/run.csx)]

Todas las funciones de orquestación de C# deben tener un parámetro de tipo `DurableOrchestrationContext`, que existe en el ensamblado `Microsoft.Azure.WebJobs.Extensions.DurableTask`. Si utiliza un script de C#, se puede hacer referencia al ensamblado mediante la notación `#r`. Este objeto de contexto le permite llamar a otras funciones de *actividad* y pasar parámetros de entrada mediante su método [CallActivityAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

El código llama a `E1_SayHello` tres veces en secuencia con distintos valores de parámetro. El valor devuelto de cada llamada se agrega a la lista `outputs`, que se devuelve al final de la función.

### <a name="javascript"></a>Javascript

El siguiente es el código fuente:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Todas las funciones de orquestación de JavaScript tienen que incluir el módulo [`durable-functions`](https://www.npmjs.com/package/durable-functions). Esta es una biblioteca que le permite escribir Durable Functions en JavaScript. Hay tres diferencias importantes entre una función de orquestación y otras funciones de JavaScript:

1. La función es una [función de generador.](https://docs.microsoft.com/scripting/javascript/advanced/iterators-and-generators-javascript)
2. La función se ajusta en una llamada al método `orchestrator` del módulo `durable-functions` (aquí `df`).
3. La función debe ser sincrónica. Dado que el método "orchestrator" se encarga de llamar a "context.done", la función debería simplemente devolver "return".

El objeto `context` contiene un objeto `df` que le permite llamar a otras funciones de *actividad* y pasar parámetros de entrada mediante su método `callActivity`. El código llama a `E1_SayHello` tres veces en secuencia con valores de parámetro diferentes, utilizando `yield` para indicar que la ejecución debe esperar en las llamadas de función de actividad asincrónica que se van a devolver. El valor devuelto de cada llamada se agrega a la lista `outputs`, que se devuelve al final de la función.

## <a name="e1_sayhello"></a>E1_SayHello

### <a name="functionjson-file"></a>Archivo function.json

El archivo *function.json* para la función de actividad `E1_SayHello` es similar al de `E1_HelloSequence` salvo en que usa un tipo de enlace `activityTrigger` en lugar de un tipo de enlace `orchestrationTrigger`.

[!code-json[Main](~/samples-durable-functions/samples/csx/E1_SayHello/function.json)]

> [!NOTE]
> Cualquier función llamada por una función de orquestación debe utilizar el enlace `activityTrigger`.

La implementación de `E1_SayHello` es una operación de formato de cadena relativamente sencilla.

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E1_SayHello/run.csx)]

Esta función tiene un parámetro de tipo [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html), que usa para obtener la entrada que se le pasó mediante la llamada de la función de orquestador a [`CallActivityAsync<T>`](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CallActivityAsync_).

### <a name="javascript"></a>JavaScript

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

A diferencia de una función de orquestación de JavaScript, una función de actividad no necesita una configuración especial. La entrada que le pasa la función del orquestador se encuentra en el objeto `context.bindings` bajo el nombre del enlace `activityTrigger`, en este caso `context.bindings.name`. El nombre de enlace puede establecerse como un parámetro de la función exportada y se puede acceder a él directamente, que es lo que hace el código de ejemplo.

## <a name="run-the-sample"></a>Ejecución del ejemplo

Para ejecutar la orquestación de `E1_HelloSequence`, envíe la siguiente solicitud POST HTTP.

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> El fragmento de código HTTP anterior da por supuesto que hay una entrada en el archivo `host.json` que elimina el prefijo `api/` predeterminado de todas las direcciones URL de las funciones de activación de HTTP. Puede encontrar el marcador para esta configuración en el archivo `host.json` en los ejemplos.

Por ejemplo, si ejecuta el ejemplo en una aplicación de función llamada "myfunctionapp", reemplace "{host}" por "myfunctionapp.azurewebsites.net".

El resultado es una respuesta HTTP 202, similar a la siguiente (acortada para simplificar):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

En este punto, la orquestación se pone en cola y comienza a ejecutarse inmediatamente. La dirección URL en el encabezado `Location` puede usarse para comprobar el estado de la ejecución.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

El resultado es el estado de la orquestación. Se ejecuta y se completa con rapidez, por lo que se muestra con el estado *Completed* (Completado) con una respuesta similar a la siguiente (acortada para simplificar):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Como puede ver, el valor de `runtimeStatus` de la instancia es *Completed* (Completado) y `output` contiene el resultado serializado con JSON de la ejecución de la función de orquestador.

> [!NOTE]
> El punto de conexión HTTP POST que inició la función de orquestador se implementa en la aplicación de ejemplo como una función de desencadenador HTTP denominada "HttpStart". Puede implementar una lógica de inicio similar para otros tipos de desencadenadores, como `queueTrigger`, `eventHubTrigger` o `timerTrigger`.

Observe los registros de ejecución de la función. La función `E1_HelloSequence` se ha iniciado y completado varias veces debido al comportamiento de reproducción descrito en la [introducción](durable-functions-concepts.md). Por otro lado, solo ha habido tres de ejecuciones de `E1_SayHello`, puesto que esas ejecuciones de función no se reproducen.

## <a name="visual-studio-sample-code"></a>Código de ejemplo de Visual Studio

Esta es la orquestación como archivo único de C# en un proyecto de Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs)]

## <a name="next-steps"></a>Pasos siguientes

En este ejemplo se ha demostrado una orquestación de encadenamiento de función simple. En el ejemplo siguiente se muestra cómo implementar el de distribución ramificada de salida y entrada.

> [!div class="nextstepaction"]
> [Ejecución del ejemplo de distribución ramificada de salida y de entrada](durable-functions-cloud-backup.md)
