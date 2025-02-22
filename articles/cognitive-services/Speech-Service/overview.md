---
title: ¿Qué son los servicios Voz?
titleSuffix: Azure Cognitive Services
description: Los servicios de voz son la unificación de los servicios de voz a texto, texto a voz y traducción de voz en una única suscripción de Azure. Es fácil agregar voz a sus aplicaciones, herramientas y dispositivos con el SDK de voz, el SDK de dispositivos de voz o las API de REST. Agregue la funcionalidad de voz a un bot de chat existente, convierta texto a voz en una aplicación de traducción o transcriba grandes volúmenes de datos del centro de llamadas.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: overview
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 0aa4286d8cb630f221613bebd13f7ea722224ac6
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70068845"
---
# <a name="what-are-the-speech-services"></a>¿Qué son los servicios Voz?

Los servicios de voz son la unificación de los servicios de voz a texto, texto a voz y traducción de voz en una única suscripción de Azure. Es fácil habilitar voz en sus aplicaciones, herramientas y dispositivos con el [SDK de voz](speech-sdk-reference.md), el [SDK de dispositivos de voz](https://aka.ms/sdsdk-quickstart) o las [API de REST](rest-apis.md).

> [!IMPORTANT]
> Los servicios de voz han reemplazado Bing Speech API, Translator Speech y Custom Speech. Consulte *Guías de procedimientos > Migración* para obtener instrucciones de migración.

Estas características conforman los servicios de voz de Azure. Use los vínculos en esta tabla para obtener más información sobre los casos de uso comunes para cada característica o examinar la referencia de API.

| Servicio | Característica | DESCRIPCIÓN | SDK | REST |
|---------|---------|-------------|-----|------|
| [Voz a texto](speech-to-text.md) | Voz a texto | Voz a texto transcribe secuencias de audio a texto en tiempo real que sus aplicaciones, herramientas o dispositivos pueden usar o mostrar. Use voz a texto con [Language Understanding (LUIS)](https://docs.microsoft.com/azure/cognitive-services/luis/) para derivar las intenciones del usuario a partir de voz transcrita y actuar en los comandos de voz. | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Batch Transcription](batch-transcription.md) (Transcripción de Azure Batch) | La transcripción de Azure Batch permite la transcripción de voz a texto asincrónica de grandes volúmenes de datos. Este es un servicio basado en REST, que usa el mismo punto de conexión que la personalización y la administración de modelos. | Sin | [Sí](https://westus.cris.ai/swagger/ui/index) |
| | [Transcripción de conversaciones](conversation-transcription-service.md) | Permite el reconocimiento de voz en tiempo real, la identificación del hablante y la diarización. Es perfecto para transcribir reuniones en persona con la capacidad de distinguir a los oradores. | Sí | Sin |
| | [Creación de modelos de voz personalizados](#customize-your-speech-experience) | Si usa voz a texto para el reconocimiento y la transcripción en un entorno único, puede crear y entrenar modelos acústicos, de lenguaje y pronunciación personalizados para dirigir el sonido ambiental o vocabulario específico del sector. | Sin | [Sí](https://westus.cris.ai/swagger/ui/index) |
| [Texto a voz](text-to-speech.md) | Texto a voz | Texto a voz convierte el texto de entrada en voz sintetizada similar a la humana mediante el [Lenguaje de marcado de síntesis de voz (SSML)](text-to-speech.md#speech-synthesis-markup-language-ssml). Elija entre voces estándar y voces neuronales (consulte [Compatibilidad de idioma](language-support.md)). | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| | [Creación de voces personalizadas](#customize-your-speech-experience) | Cree fuentes de voz personalizadas únicas para su marca o producto. | Sin | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) |
| [Traducción de voz](speech-translation.md) | Traducción de voz | La traducción de voz habilita la traducción de voz en varios idiomas en tiempo real en sus aplicaciones, herramientas y dispositivos. Use este servicio para la traducción de voz a voz y voz a texto. | [Sí](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk-reference) | Sin |
| [Asistentes virtuales por voz](voice-first-virtual-assistants.md) | Asistentes virtuales por voz | Los asistentes virtuales personalizadas que utilizan los servicios de voz de Azure permiten a los desarrolladores crear interfaces de conversación naturales, similares a la humana, para sus aplicaciones y experiencias. El canal de voz Direct Line de Bot Framework mejora estas funcionalidades porque proporciona un punto de entrada coordinado y organizado a un bot compatible que permite la interacción de entrada y salida de voz con baja latencia y alta confiabilidad. | [Sí](voice-first-virtual-assistants.md) | Sin |

## <a name="news-and-updates"></a>Noticias y actualizaciones

Obtenga información sobre las novedades con los servicios de voz de Azure.

* Agosto de 2019
  * **Nuevo tutorial**: [Habilitar el bot con Voz mediante Speech SDK, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
  * Se ha agregado un nuevo estilo de habla, [`chat`](speech-synthesis-markup.md#adjust-speaking-styles), para la voz `en-US-JessaNeural`. 
* Junio de 2019
  * El SDK de Voz versión 1.6.0 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).
* Mayo de 2019: ya hay documentación disponible para [Transcripción de conversaciones](conversation-transcription-service.md), [Transcripción de centros de llamadas](call-center-transcription.md) y [Asistentes virtuales por voz](voice-first-virtual-assistants.md).
* Mayo de 2019
  * Speech SDK versión 1.5.1 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).
  * Speech SDK versión 1.5.0 publicado. Para obtener una lista completa de actualizaciones, mejoras y problemas conocidos, consulte las [Notas de la versión](releasenotes.md).

## <a name="try-speech-services"></a>Prueba de los servicios de voz

Ofrecemos guías de inicio rápido en los lenguajes de programación más populares, cuyo diseño individual le permite ejecutar código en menos de 10 minutos. En esta tabla se incluyen las guías de inicio rápido más populares para cada característica. Use el menú de navegación izquierdo para explorar lenguajes y plataformas adicionales.

| Voz a texto (SDK) | Texto a voz (SDK) | Traducción (SDK) |
|----------------------|----------------------|-------------------|
| [C#, .NET Core (Windows)](quickstart-csharp-dotnet-windows.md) | [C#, .NET Framework (Windows)](quickstart-text-to-speech-dotnet-windows.md) | [Java (Windows, Linux)](quickstart-translate-speech-java-jre.md) |
| [JavaScript (Explorador)](quickstart-js-browser.md) | [C++ (Windows)](quickstart-text-to-speech-cpp-windows.md) | [C#, .NET Core (Windows)](quickstart-translate-speech-dotnetcore-windows.md) |
| [Python (Windows, Linux, macOS)](quickstart-python.md) | [C++ (Linux)](quickstart-text-to-speech-cpp-linux.md) | [C#, .NET Framework (Windows)](quickstart-translate-speech-dotnetframework-windows.md) |
| [Java (Windows, Linux)](quickstart-java-jre.md) | | [C++ (Windows)](quickstart-translate-speech-cpp-windows.md) |

> [!NOTE]
> Voz a texto y texto a voz también tienen asociados puntos de conexión REST e inicios rápidos.

Una vez que haya tenido la oportunidad de usar los servicios de voz, pruebe nuestro tutorial, que le enseña a reconocer intenciones a partir de contenido de voz mediante el SDK de voz y LUIS.

* [Tutorial: Reconocimiento de intenciones a partir de contenido de voz con el SDK de voz y LUIS, C#](how-to-recognize-intents-from-speech-csharp.md)
* [Tutorial: activación del bot con voz mediante el SDK de Speech, C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* [Tutorial: Compilación de una aplicación de Flask para traducir texto, analizar opiniones y sintetizar la voz de texto a voz, REST](https://docs.microsoft.com/azure/cognitive-services/translator/tutorial-build-flask-app-translation-synthesis?toc=%2fazure%2fcognitive-services%2fspeech-service%2ftoc.json&bc=%2fazure%2fcognitive-services%2fspeech-service%2fbreadcrumb%2ftoc.json&toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fspeech-service%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

## <a name="get-sample-code"></a>Obtención de código de ejemplo

El código de ejemplo está disponible en GitHub para cada uno de los servicios de voz de Azure. En estos ejemplos se tratan escenarios comunes como la lectura de audio de un archivo o flujo, el reconocimiento continuo y de una sola emisión, y el trabajo con modelos personalizados. Use estos vínculos para ver ejemplos de SDK y REST:

* [Ejemplos de conversión de voz a texto, texto a voz y traducción de voz (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Ejemplos de transcripción de Azure Batch (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
* [Ejemplos de texto a voz (REST)](https://github.com/Azure-Samples/Cognitive-Speech-TTS)
* [Ejemplos del asistente virtual por voz (SDK)](https://aka.ms/csspeech/samples)

## <a name="customize-your-speech-experience"></a>Personalización de su experiencia de voz

Los servicios de voz de Azure funcionan bien con los modelos integrados; sin embargo, es posible que desee personalizar y optimizar más la experiencia para su producto o entorno. Las opciones de personalización abarcan desde la optimización de modelos acústicos a fuentes de voz únicas para su marca. Una vez que haya creado un modelo personalizado, podrá usarlo con cualquiera de los servicios de voz de Azure.

| Speech Service | Plataforma | DESCRIPCIÓN |
|----------------|-------------|-------------|
| Voz a texto | [Custom Speech](https://aka.ms/customspeech) | El reconocimiento de voz personalizado se adapta a sus necesidades y datos disponibles. Elimine las barreras del reconocimiento de voz, como el estilo de habla, el vocabulario y el ruido de fondo. |
| Text-to-Speech | [Voz personalizada](https://aka.ms/customvoice) | Cree una voz reconocible única para las aplicaciones de texto a voz con los datos de habla disponibles. Puede optimizar aún más las salidas de voz ajustando un conjunto de parámetros de voz. |

## <a name="reference-docs"></a>Documentos de referencia

* [Speech SDK](speech-sdk-reference.md)
* [Speech Devices SDK](speech-devices-sdk.md)
* [API REST: Speech-to-text](rest-speech-to-text.md) (API de REST: Voz a texto)
* [API REST: Text-to-speech](rest-text-to-speech.md) (API de REST: Texto a voz)
* [API REST: Batch transcription and customization](https://westus.cris.ai/swagger/ui/index) (API de REST: Transcripción y personalización de Azure Batch)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Get a Speech Services subscription key for free](get-started.md) (Consiga una clave de suscripción a los servicios de voz gratis)
