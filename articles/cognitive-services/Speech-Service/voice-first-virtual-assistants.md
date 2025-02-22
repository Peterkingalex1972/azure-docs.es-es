---
title: 'Asistentes virtuales por voz personalizados (versión preliminar): Speech Service'
titleSuffix: Azure Cognitive Services
description: Información general de las características, funcionalidades y restricciones de los asistentes virtuales por voz personalizados mediante el canal de Voz Direct Line en Bot Framework y el kit de desarrollo de software (SDK) de Voz de Cognitive Services.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: travisw
ms.openlocfilehash: f77a95af1edcd37043ea1e067560577140a81a04
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967602"
---
# <a name="about-custom-voice-first-virtual-assistants-preview"></a>Sobre los asistentes virtuales por voz personalizados (versión preliminar)

Los asistentes virtuales personalizadas que utilizan los servicios de voz de Azure permiten a los desarrolladores crear interfaces de conversación naturales, similares a la humana, para sus aplicaciones y experiencias. El canal de voz Direct Line de Bot Framework mejora estas funcionalidades porque proporciona un punto de entrada coordinado y organizado a un bot compatible que permite la interacción de entrada y salida de voz con baja latencia y alta confiabilidad. Estos bots pueden usar Language Understanding (LUIS) de Microsoft para la interacción con el lenguaje natural. Los dispositivos acceden a Direct Line Speech mediante el kit de desarrollo de software (SDK) de voz.

   ![Diagrama conceptual del flujo de servicio de orquestación de Direct Line Speech](media/voice-first-virtual-assistants/overview.png "Flujo de canal de voz")


Direct Line Speech y su funcionalidad asociada para los asistentes virtuales por voz personalizados constituyen un complemento perfecto para la [solución Virtual Assistant y la plantilla empresarial](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Aunque Direct Line Speech puede funcionar con cualquier bot compatible, estos recursos ofrecen una línea de base reutilizable para experiencias conversacionales de alta calidad, así como habilidades y modelos complementarios comunes para comenzar rápidamente.


## <a name="core-features"></a>Características principales

| Categoría | Características |
|----------|----------|
|[Palabra de reactivación personalizada](speech-devices-sdk-create-kws.md) | Puede permitir que los usuarios inicien conversaciones con bots con una palabra clave personalizada, por ejemplo, "Hola Contoso". Esta tarea se realiza con un motor de palabras de reactivación personalizadas en Speech SDK, que puede configurarse con una palabra de reactivación personalizada [que puede generar aquí](speech-devices-sdk-create-kws.md). El canal de Direct Line Speech incluye la comprobación de palabras de reactivación del servicio que mejora la precisión de la activación de palabras de reactivación en comparación con el dispositivo por sí solo.
|[Speech to Text](speech-to-text.md) | El canal Direct Line Speech incluye la transcripción en tiempo real de audio en texto reconocido mediante [Conversión de texto a voz](speech-to-text.md) de los servicios de Voz de Azure. Este texto está disponible para el bot y la aplicación cliente a medida que se transcribe.
|[Texto a voz](text-to-speech.md) | Las respuestas textuales desde el bot se sintetizan mediante [Texto a voz](text-to-speech.md) de los servicios de Voz de Azure. Esta síntesis se pone luego a disposición de la aplicación cliente como una secuencia de audio. Microsoft ofrece la posibilidad de crear su propia voz TTS neuronal personalizada de alta calidad que le pone voz a su marca. Para más información, [póngase en contacto con nosotros](mailto:mstts@microsoft.com).
|[Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech) | En cuanto canal dentro de Bot Framework, Direct Line Speech permite una conexión completa y fluida entre la aplicación cliente, un bot compatible y las funcionalidades de los servicios de Voz de Azure. Para más información sobre cómo configurar el bot para usar el canal Direct Line Speech, consulte [su página en la documentación de Bot Framework](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="get-started-with-virtual-assistants"></a>Introducción a los asistentes virtuales

Le ofrecemos inicios rápidos diseñados para que ejecute el código en menos de 10 minutos. Esta tabla incluye una lista de inicios rápidos de asistente virtual por voz ordenados por idioma.

| Guía de inicio rápido | Plataforma | Referencia de API |
|------------|----------|---------------|
| C#, UWP | Windows | [Browse](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Browse](https://aka.ms/csspeech/javaref) |
| Java | Android | [Browse](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de ejemplo

El código de ejemplo para crear un asistente virtual por voz está disponible en GitHub. Estos ejemplos abarcan la aplicación cliente para conectarse al bot en varios lenguajes de programación conocidos.

* [Ejemplos del asistente virtual por voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: Habilitación del bot con voz mediante el SDK de voz, C#](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Tutorial
Tutorial sobre cómo [habilitar el bot con voz mediante el SDK de voz y el canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalización

Los asistentes virtuales por voz creados mediante los servicios de Voz de Azure pueden usar la amplia variedad de opciones de personalización disponibles para [conversión de voz a texto](speech-to-text.md), [conversión de texto a voz](text-to-speech.md) y [selección de palabra clave personalizada](speech-devices-sdk-create-kws.md).

> [!NOTE]
> Las opciones de personalización varían según el idioma o la configuración regional (consulte los [idiomas admitidos](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de referencia

* [Speech SDK](speech-sdk-reference.md)
* [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Pasos siguientes

* [Get a Speech Services subscription key for free](get-started.md) (Consiga una clave de suscripción a los servicios de voz gratis)
* [Obtención de Speech SDK](speech-sdk.md)
* [Creación e implementación de un bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obtención de la solución Virtual Assistant y la plantilla empresarial](https://github.com/Microsoft/AI)
