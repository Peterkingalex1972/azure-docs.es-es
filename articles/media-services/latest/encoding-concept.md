---
title: 'Codificación en la nube con Media Services: Azure | Microsoft Docs'
description: Este tema describe el proceso de codificación con Azure Media Services
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 06/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: b0a71e8b3ffff822521a23aafd6764bcce9bd4d4
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67303928"
---
# <a name="encoding-with-media-services"></a>Codificación con Media Services

El término codificación en Media Services se aplica al proceso de convertir archivos que contienen vídeo o audio digital de un formato estándar a otro, con el objetivo de (a) reducir el tamaño de los archivos o (b) producir un formato que sea compatible con un amplia gama de dispositivos y aplicaciones. Este proceso también se conoce como compresión de vídeo o transcodificación. Consulte [Compresión de datos](https://en.wikipedia.org/wiki/Data_compression) y [What Is Encoding and Transcoding?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx) (¿Qué es la codificación y transcodificación?) para más información sobre los conceptos.

Normalmente, los vídeos se entregan a los dispositivos y aplicaciones a través de una [descarga progresiva](https://en.wikipedia.org/wiki/Progressive_download) o el [streaming con velocidad de bits adaptable](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming). 

* Para la entrega a través de una descarga progresiva, puede usar Azure Media Services para convertir un archivo multimedia digital (intermedio) en un archivo [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) que contiene el vídeo que se ha codificado con el códec [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) y audio que se ha codificado con el códec [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding). Este archivo MP4 se escribe en un recurso de la cuenta de almacenamiento. Puede usar los SDK o API de Azure Storage (por ejemplo, [API de REST de almacenamiento](../../storage/common/storage-rest-api-auth.md), [SDK de JAVA](../../storage/blobs/storage-quickstart-blobs-java-v10.md) o [SDK de .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para descargar el archivo directamente. Si creó el recurso de salida con un nombre de contenedor específico en el almacenamiento, use esa ubicación. De lo contrario, puede usar Media Services para [enumerar las direcciones URL del contenedor de recursos](https://docs.microsoft.com/rest/api/media/assets/listcontainersas). 
* Para preparar el contenido para la entrega a través del streaming con velocidad de bits adaptable, el archivo intermedio se debe codificar en varias velocidades de bits (de alta a baja). Para garantizar una transición correcta de la calidad, a medida que se reduzca la velocidad de bits, también se reduce la resolución del vídeo. Esto resulta en lo que se conoce como una escala de codificación: una tabla de resoluciones y velocidades de bits (consulte [Escala de velocidad de bits generada automáticamente](autogen-bitrate-ladder.md)). Puede usar Media Services para codificar los archivos intermedios en varias velocidades de bits. Al hacerlo, obtendrá un conjunto de archivos MP4 y archivos de configuración de streaming asociados, escritos en un recurso de la cuenta de almacenamiento. Luego puede usar la funcionalidad de [empaquetado dinámico](dynamic-packaging-overview.md) de Media Services para entregar el vídeo a través de protocolos de streaming, como [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) y [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming). Esto requiere crear un [localizador de streaming](streaming-locators-concept.md) y generar direcciones URL de streaming que correspondan a los protocolos admitidos, que luego se pueden entregar a los dispositivos o aplicaciones según sus funcionalidades.

En el siguiente diagrama se muestra el flujo de trabajo para la codificación a petición con empaquetado dinámico.

![Empaquetado dinámico](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

En este tema se proporcionan instrucciones sobre cómo codificar el contenido con Media Services v3.

## <a name="transforms-and-jobs"></a>Transformaciones y trabajos

Para codificar con Media Services v3, debe crear una [transformación](https://docs.microsoft.com/rest/api/media/transforms) y un [trabajo](https://docs.microsoft.com/rest/api/media/jobs). La transformación define la receta para la configuración de la codificación y las salidas, y el trabajo es una instancia de la receta. Para más información, consulte [Transformaciones y trabajos](transforms-jobs-concept.md)

Al codificar con Media Services, se usan valores preestablecidos para indicar al codificador cómo se deben procesar los archivos multimedia de entrada. Por ejemplo, puede especificar la resolución de vídeo o el número de canales de audio que desea en el contenido codificado. 

Puede empezar a trabajar rápidamente con alguno de los valores preestablecidos integrados recomendados basados en los procedimientos recomendados del sector o puede optar por crear un valor preestablecido destinado a su escenario específico o los requisitos de dispositivos. Para más información, vea [Procedimiento de codificación con una transformación personalizada](customize-encoder-presets-how-to.md). 

A partir de enero de 2019, al codificar con Media Encoder Standard para generar archivos MP4, se genera un archivo .mpi nuevo y se agrega a la salida de activos. Este archivo MPI está diseñado para mejorar el rendimiento de escenarios de streaming y [empaquetado dinámico](dynamic-packaging-overview.md).

> [!NOTE]
> No debe modificar ni quitar el archivo MPI, así como tampoco tener ninguna dependencia en el servicio en la existencia (o no) de este tipo de archivo.

### <a name="creating-job-input-from-an-https-url"></a>Creación de una entrada de trabajo a partir de una dirección URL de HTTPS

Cuando se envían trabajos para procesar los vídeos, es necesario indicar a Media Services dónde encontrar el vídeo de entrada. Una de las opciones es especificar una dirección URL de HTTPS como una entrada de trabajo. Actualmente, Media Services v3 no admite la codificación de transferencia fragmentada a través de direcciones URL HTTPS. 

#### <a name="examples"></a>Ejemplos

* [Codificación desde una dirección URL de HTTPS con .NET](stream-files-dotnet-quickstart.md)
* [Codificación desde una dirección URL de HTTPS con REST](stream-files-tutorial-with-rest.md)
* [Codificación desde una dirección URL de HTTPS con la CLI](stream-files-cli-quickstart.md)
* [Codificación desde una dirección URL de HTTPS con Node.js](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>Creación de una entrada de trabajo a partir de un archivo local

El vídeo de entrada se puede almacenar como un recurso de Media Services, en cuyo caso se crea un recurso de entrada basado en un archivo (almacenado localmente o en Azure Blob Storage). 

#### <a name="examples"></a>Ejemplos

[Codificación de un archivo local con valores preestablecidos integrados](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>Creación de la entrada del trabajo con creación de subclips

Al codificar un vídeo, puede especificar que también se recorte el archivo de origen y que se genere una salida que solo tenga una parte deseada de la entrada de vídeo. Esta funcionalidad se puede usar con cualquier elemento [Transformación](https://docs.microsoft.com/rest/api/media/transforms) que se compila mediante los valores preestablecidos [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). 

Puede especificar crear un [trabajo](https://docs.microsoft.com/rest/api/media/jobs/create) con un solo clip de un vídeo a petición o archivo en directo (un evento grabado). La entrada del trabajo podría ser un recurso o una dirección URL HTTPS.

> [!TIP]
> Si quiere hacer streaming de un sublip del vídeo sin volver a codificarlo, considere la posibilidad de usar [manifiestos de filtrado previo con el empaquetador dinámico](filters-dynamic-manifest-overview.md).

#### <a name="examples"></a>Ejemplos

Ver ejemplos:

* [Creación de un subclip de video con .NET](subclip-video-dotnet-howto.md)
* [Creación de un subclip de video con REST](subclip-video-rest-howto.md)

## <a name="built-in-presets"></a>Valores preestablecidos integrados

Media Services admite actualmente los siguientes valores preestablecidos de codificación integrados:  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) se usa para establecer un valor preestablecido integrado para codificar el vídeo de entrada con Standard Encoder. 

Actualmente se admiten los valores preestablecidos siguientes:

- **EncoderNamedPreset.AACGoodQualityAudio**: genera un solo archivo MP4 que contiene únicamente audio estéreo codificado a 192 kbps.
- **EncoderNamedPreset.AdaptiveStreaming** (recomendado). Para más información, consulte [Generación automática de una escala de velocidad de bits](autogen-bitrate-ladder.md).
- **EncoderNamedPreset.ContentAwareEncodingExperimental** expone un valor preestablecido experimental para la codificación según el contenido. Dado cualquier contenido de entrada, el servicio intenta determinar automáticamente el número óptimo de capas, la velocidad de bits adecuada y la configuración de resolución para la entrega a través del streaming adaptable. Los algoritmos subyacentes seguirán evolucionando con el tiempo. La salida contendrá archivos MP4 con el vídeo y audio intercalados. Para más información, consulte [Valor preestablecido experimental para la codificación según el contenido](cae-experimental.md).
- **EncoderNamedPreset.H264MultipleBitrate1080p** genera un conjunto de 8 archivos MP4 alineados con GOP, que abarcan desde 6000 kbps hasta 400 kbps y audio AAC estéreo. La resolución comienza en 1080p y baja hasta 360p.
- **EncoderNamedPreset.H264MultipleBitrate720p** genera un conjunto de 6 archivos MP4 alineados con GOP, que abarcan desde 3400 kbps hasta 400 kbps y audio AAC estéreo. La resolución comienza en 720p y baja hasta 360p.
- **EncoderNamedPreset.H264MultipleBitrateSD** genera un conjunto de 5 archivos MP4 alineados con GOP, que abarcan desde 1600 kbps hasta 400 kbps y audio AAC estéreo. La resolución comienza en 480p y baja hasta 360p.
- **EncoderNamedPreset.H264SingleBitrate1080p** genera un archivo MP4 en el que el vídeo se codifica con el códec H.264 a 6750 kbps y una altura de imagen de 1080 píxeles y el audio estéreo se codifica con el códec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrate720p** genera un archivo MP4 en el que el vídeo se codifica con el códec H.264 a 4500 kbps y una altura de imagen de 720 píxeles y el audio estéreo se codifica con el códec AAC-LC a 64 kbps.
- **EncoderNamedPreset.H264SingleBitrateSD** genera un archivo MP4 en el que el vídeo se codifica con el códec H.264 a 2200 kbps y una altura de imagen de 480 píxeles y el audio estéreo se codifica con el códec AAC-LC a 64 kbps.

Para ver la lista más actualizada de valores preestablecidos, consulte los [valores preestablecidos integrados que se usarán para codificar vídeos](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset).

Para ver cómo se usan los valores preestablecidos, consulte [Carga, codificación y transmisión en secuencias de videos](stream-files-tutorial-with-api.md).

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) describe la configuración que se utilizará al codificar el vídeo de entrada con Standard Encoder. Utilice este valor preestablecido al personalizar los valores preestablecidos de transformación. 

#### <a name="considerations"></a>Consideraciones

Al crear valores preestablecidos personalizados, se aplican las consideraciones siguientes:

- Todos los valores de alto y ancho del contenido de AVC deben ser un múltiplo de 4.
- En Azure Media Services v3, todas las velocidades de bits de codificación se expresan en bits por segundo. Esto difiere de los valores preestablecidos en las API v2, en donde se usaban kilobits por segundo como unidad. Por ejemplo, si la velocidad de bits de v2 se especificaba como 128 (kilobits/segundo), en v3 se establecería en 128 000 (bits/segundo).

### <a name="customizing-presets"></a>Personalización de los valores preestablecidos

Media Services es totalmente compatible con la personalización de todos los valores preestablecidos para que cubran sus necesidades y requisitos de codificación específicos. Para obtener ejemplos en los que se muestra cómo personalizar los valores preestablecidos del codificador, consulte:

#### <a name="examples"></a>Ejemplos

- [Personalización de valores preestablecidos con .NET](customize-encoder-presets-how-to.md)
- [Personalización de valores preestablecidos con la CLI](custom-preset-cli-howto.md)
- [Personalización de valores preestablecidos con REST](custom-preset-rest-howto.md)

## <a name="preset-schema"></a>Esquema de valores preestablecidos

En Media Services v3, los valores preestablecidos son entidades fuertemente tipadas en la propia API. Encontrará la definición de "esquema" de estos objetos en la [especificación de Open API (o Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). También puede ver las definiciones preestablecidas (como **StandardEncoderPreset**) en la [API de REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) y el [SDK de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (o en otra documentación de referencia del SDK de Media Services v3).

## <a name="scaling-encoding-in-v3"></a>Escalado de la codificación en v3

Para escalar el procesamiento multimedia, consulte [Escalado con CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Formule preguntas, realice comentarios y obtenga actualizaciones

Consulte el artículo [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="next-steps"></a>Pasos siguientes

* [Carga, codificación y transmisión con Media Services](stream-files-tutorial-with-api.md)
* [Codificación desde una dirección URL de HTTPS con valores preestablecidos integrados](job-input-from-http-how-to.md)
* [Codificación de un archivo local con valores preestablecidos integrados](job-input-from-local-file-how-to.md)
* [Compilación de un valor preestablecido personalizado para sus requisitos específicos de escenario o dispositivo](customize-encoder-presets-how-to.md)