---
title: 'La interacción humana y los tiempos de espera en Durable Functions: Azure'
description: Aprenda a controlar la interacción humana y los tiempos de espera en la extensión Durable Functions para Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 3918c37d985c6766fe6ad4601b70ddbd4597b0ba
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087146"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Las interacciones humanas en Durable Functions: comprobación telefónica de ejemplo

Este ejemplo muestra cómo compilar una orquestación de [Durable Functions](durable-functions-overview.md) con interacción humana. Cada vez que una persona real participa en un proceso automatizado,este debe ser capaz de enviar notificaciones a la persona y de recibir respuestas de forma asincrónica. También debe permitir la posibilidad de que la persona no esté disponible. (Esta última parte es donde los tiempos de espera son relevantes).

En este ejemplo se implementa un sistema de comprobación telefónica por SMS. A menudo se usan estos tipos de flujos al comprobar el número de teléfono de un cliente o para la autenticación multifactor (MFA). Este es un ejemplo eficaz, porque toda la implementación se realiza con un par de pequeñas funciones. No se necesita almacén de datos externo, como una base de datos.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Información general de escenario

La comprobación telefónica sirve para verificar la identidad de los usuarios finales de la aplicación y que no son responsables de correo basura. La autenticación multifactor es un caso de uso común para proteger las cuentas de usuario de los piratas informáticos. La dificultad de implementar su propia comprobación telefónica consiste en que requiere una **interacción con estado** con una persona. Normalmente, al usuario final se le proporciona código (por ejemplo, un número de 4 dígitos) y debe responder **en un intervalo de tiempo razonable**.

Las instancias normales de Azure Functions no tienen estado (igual que muchos otros puntos de conexión en la nube de otras plataformas), por lo que estos tipos de interacciones implican explícitamente la administración externa del estado en una base de datos u otro almacén persistente. Además, la interacción debe dividirse en varias funciones que se puedan coordinar entre sí. Por ejemplo, necesita al menos una función para determinar un código, almacenarlo en alguna parte y enviarlo al teléfono del usuario. Además, necesita al menos un otra función para recibir una respuesta del usuario y asignarla de algún modo a la llamada de función original para la validación del código. El tiempo de espera también es un aspecto importante para garantizar la seguridad. Este factor puede complicarse bon bastante rapidez.

Con Durable Functions se reduce enormemente la complejidad de este escenario. Como verá en este ejemplo, una función de orquestador puede administrar la interacción con estado muy fácilmente y sin que intervengan almacenes de datos externos. Dado que las funciones de orquestador son *durables*, estos flujos interactivos también son muy confiables.

## <a name="configuring-twilio-integration"></a>Configuración de la integración de Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funciones

En este artículo se explican las funciones siguientes en la aplicación de ejemplo:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

En las siguientes secciones se explican la configuración y el código que se utilizan para el scripting C# y para JavaScript. Al final del artículo se muestra el código para el desarrollo de Visual Studio.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>La orquestación de verificación por SMS (código de ejemplo de Azure Portal y Visual Studio Code)

La función **E4_SmsPhoneVerification** utiliza la norma *function.json* para las funciones de orquestador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Este es el código que implementa la función:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Una vez iniciada, la función de orquestador hace lo siguiente:

1. Obtiene un número de teléfono al que *enviar* la notificación mediante SMS.
2. Llama a **E4_SendSmsChallenge** para enviar un mensaje SMS al usuario y devuelve el código de desafío de 4 dígitos esperado.
3. Crea un temporizador durable que se desencadena 90 segundos a partir de la hora actual.
4. Además del temporizador, espera un evento **SmsChallengeResponse** del usuario.

El usuario recibe un mensaje SMS con un código de cuatro dígitos. Tiene 90 segundos para devolver ese mismo código de 4 dígitos a la instancia de la función de orquestador para completar el proceso de comprobación. Si envía un código incorrecto, tiene otros tres intentos para enviar el correcto (dentro de esos mismos 90 segundos).

> [!NOTE]
> Puede no resultar obvio al principio, pero esta función de orquestador es totalmente determinista. Esto se debe a que las propiedades `CurrentUtcDateTime` (.NET) y `currentUtcDateTime` (JavaScript) se utilizan para calcular la fecha de expiración del temporizador y devuelven el mismo valor en todas las reproducciones en ese momento en el código del orquestador. Esto es importante para garantizar que se produce el mismo `winner` en todas las llamadas repetidas a `Task.WhenAny` (.NET) o `context.df.Task.any` (JavaScript).

> [!WARNING]
> Es importante [cancelar los temporizadores](durable-functions-timers.md) si ya no se necesita que expiren, como en el ejemplo anterior, cuando se acepta una respuesta de desafío.

## <a name="send-the-sms-message"></a>Envío del mensaje SMS

La función **E4_SendSmsChallenge** usa el enlace de Twilio para enviar el mensaje SMS con el código de 4 dígitos al usuario final. *function.json* se define como sigue:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

Y este es el código que genera el código de desafío de 4 dígitos y envía el mensaje SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (solo Functions 2.x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

A esta función **E4_SendSmsChallenge** solo se la llama una vez, aunque el proceso se bloquee o se reproduzca. Esto es práctico, porque no conviene que el usuario final obtenga varios mensajes SMS. El valor devuelto de `challengeCode` se almacena automáticamente, por lo que la función de orquestador siempre sabe cuál es el código correcto.

## <a name="run-the-sample"></a>Ejecución del ejemplo

Con las funciones desencadenadas mediante HTTP del ejemplo, puede iniciar la orquestación con el envío de la siguiente solicitud HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

La función de orquestador recibe el número de teléfono proporcionado e inmediatamente le envía un mensaje SMS con un código de verificación de 4 dígitos generado aleatoriamente, &mdash;por ejemplo, *2168*. A continuación, la función espera respuesta durante 90 segundos.

Para responder con el código, puede usar [`RaiseEventAsync` (.NET) o `raiseEvent` (JavaScript)](durable-functions-instance-management.md) dentro de otra función o invocar el webhook HTTP POST **sendEventUrl** al que se hace referencia en la respuesta 202 anterior y sustituir `{eventName}` por el nombre del evento, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Si se envía antes de que expire el temporizador, se completa la orquestación y el campo `output` se establece en `true`, lo cual indica que la comprobación es correcta.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Si permite que el temporizador expire o si escribe el código incorrecto cuatro veces, puede consultar el estado y verá una salida de función de orquestación `false`, que indica un error en la comprobación telefónica.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Código de ejemplo de Visual Studio

Esta es la orquestación como archivo único de C# en un proyecto de Visual Studio:

> [!NOTE]
> Deberá instalar el paquete Nuget `Microsoft.Azure.WebJobs.Extensions.Twilio` para ejecutar el siguiente código de ejemplo.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Pasos siguientes

En este ejemplo, se han demostrado algunas de las funcionalidades avanzadas de Durable Functions, en particular `WaitForExternalEvent` y `CreateTimer`. Hemos visto cómo estas se combinan con `Task.WaitAny` para implementar un sistema confiable de tiempo de espera, que a menudo resulta útil para interactuar con personas reales. Puede obtener más información acerca de cómo utilizar Durable Functions leyendo una serie de artículos que ofrecen información detallada acerca de temas específicos.

> [!div class="nextstepaction"]
> [Ir al primer artículo de la serie](durable-functions-bindings.md)
