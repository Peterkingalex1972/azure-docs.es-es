---
title: Notas de la versión de Azure Media Services v3 | Microsoft Docs
description: Para mantenerse al día con los últimos desarrollos, en este artículo se proporcionan las actualizaciones más reciente en Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 06/07/2019
ms.author: juliako
ms.openlocfilehash: f4a859f1e63866a50167031569dca05de3e9af27
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856308"
---
# <a name="azure-media-services-v3-release-notes"></a>Notas de la versión de Azure Media Services v3

Para mantenerse al día con los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

* Versiones más recientes
* Problemas conocidos
* Corrección de errores
* Funciones obsoletas

## <a name="known-issues"></a>Problemas conocidos

> [!NOTE]
> Actualmente, no puede usar Azure Portal para administrar recursos de v3. Use la [API de REST](https://aka.ms/ams-v3-rest-sdk), la CLI o uno de los SDK admitidos.

Para más información, consulte [Guía de migración para mover de Media Services v2 a v3](migrate-from-v2-to-v3.md#known-issues).

## <a name="august-2019"></a>Agosto de 2019

### <a name="south-africa-regional-pair-is-open-for-media-services"></a>El par regional de Sudáfrica está abierto para Media Services 

Media Services ya está disponible en las regiones Norte de Sudáfrica y Oeste de Sudáfrica.

Si desea obtener más información, vea [Nubes y regiones donde existe Azure Media Services v3](azure-clouds-regions.md).

## <a name="july-2019"></a>Julio de 2019

### <a name="content-protection"></a>Protección de contenido

Cuando el contenido de streaming está protegido con restricción de token, los usuarios finales deben obtener un token que se envía como parte de la solicitud de entrega de claves. La característica de *prevención de reproducción de tokens* permite a los clientes de Media Services establecer un límite en el número de veces que se puede usar el mismo token para solicitar una clave o una licencia. Para obtener más información, consulte [Prevención de reproducción de tokens](content-protection-overview.md#token-replay-prevention).

Esta característica está actualmente disponible en el centro de EE. UU. y centro-oeste de EE. UU.

## <a name="june-2019"></a>Junio de 2019

### <a name="video-subclipping"></a>Creación de subclips de vídeo

Ahora puede recortar un vídeo, o crear un subclip de vídeo al codificarlo mediante la opción [Trabajo](https://docs.microsoft.com/rest/api/media/jobs). 

Esta funcionalidad se puede usar con cualquier elemento [Transformación](https://docs.microsoft.com/rest/api/media/transforms) compilado mediante los valores preestablecidos [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) o [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). 

Ver ejemplos:

* [Creación de un subclip de vídeo con .NET](subclip-video-dotnet-howto.md)
* [Creación de un subclip de vídeo con REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Mayo de 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Soporte técnico de Azure Monitor para las métricas y los registros de diagnóstico de Media Services

Ahora puede usar a Azure Monitor para ver los datos de telemetría por emitidos por Media Services.

* Use los registros de diagnóstico de Azure Monitor para supervisar las solicitudes enviadas por el punto de conexión de la entrega de claves de Media Services. 
* Supervise las métricas emitidas por los [punto de conexión de streaming](streaming-endpoint-concept.md) de Media Services.   

Para obtener más información, consulte [Supervisión de los registros de diagnóstico y las métricas de Media Services](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Compatibilidad con varias pistas de audio en el empaquetado dinámico 

Al realizar el streaming de recursos que tienen varias pistas de audio con varios códecs y lenguajes, el [empaquetado dinámico](dynamic-packaging-overview.md) ahora admite varias pistas de audio para la salida HLS (versión 4 o superior).

### <a name="korea-regional-pair-is-open-for-media-services"></a>El par regional de Corea está abierto para Media Services 

Media Services ya está disponible en las regiones Centro de Corea del Sur y Sur de Corea del Sur. 

Si desea obtener más información, vea [Nubes y regiones donde existe Azure Media Services v3](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Mejoras en el rendimiento

Se han agregado actualizaciones que incluyen mejoras de rendimiento de Media Services.

* Se actualizó el tamaño de archivo máximo admitido para el procesamiento. Consulte [Cuotas y limitaciones](limits-quotas-constraints.md).
* [Mejoras de velocidades de codificación](media-reserved-units-cli-how-to.md#choosing-between-different-reserved-unit-types).

## <a name="april-2019"></a>Abril de 2019

### <a name="new-presets"></a>Nuevos valores preestablecidos

* [FaceDetectorPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#facedetectorpreset) se agregó a los valores preestablecidos del analizador integrado.
* [ContentAwareEncodingExperimental](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#encodernamedpreset) se agregó a los valores preestablecidos del codificador integrado. Para más información, consulte [Codificación que tiene en cuenta el contenido](cae-experimental.md). 

## <a name="march-2019"></a>Marzo de 2019

Ahora el empaquetado dinámico admite Dolby Atmos. Para más información, consulte [Códecs de audio compatibles con el empaquetado dinámico](dynamic-packaging-overview.md#audio-codecs).

Ahora puede especificar una lista de filtros de recursos o cuentas, que se aplicarían a su localizador de streaming. Para más información, consulte el tema sobre la [asociación de filtros al localizador de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Febrero de 2019

Media Services v3 ya se admite en las nubes nacionales de Azure. Aún no todas las características están disponibles en todas las nubes. Para obtener más detalles, consulte [Nubes y regiones donde existe Azure Media Services v3](azure-clouds-regions.md).

El evento [Microsoft.Media.JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) se ha agregado a los esquemas de Azure Event Grid para Media Services.

## <a name="january-2019"></a>Enero de 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard y archivos MPI 

Al codificar con Media Encoder Standard para generar archivos MP4, se genera un archivo .mpi nuevo y se agrega a la salida de activos. Este archivo MPI está diseñado para mejorar el rendimiento de escenarios de streaming y [empaquetado dinámico](dynamic-packaging-overview.md).

No debe modificar ni quitar el archivo MPI, así como tampoco tener ninguna dependencia en el servicio en la existencia (o no) de este tipo de archivo.

## <a name="december-2018"></a>Diciembre de 2018

Las actualizaciones de la versión de disponibilidad general de la API de V3 incluyen:
       
* Las propiedades de **PresentationTimeRange** ya no son necesarias para los **filtros de recursos** ni para los **filtros de cuenta**. 
* Las opciones de la consultas $top y $skip para **Jobs** y **Transforms** se han quitado y se ha agregado $orderby. Como parte de agregar la nueva funcionalidad de ordenación, se ha detectado que las opciones $top y $skip se expusieron previamente de forma accidental aunque no se habían implementado.
* La extensibilidad de enumeración se había activado de nuevo. Esta característica estaba habilitada en las versiones preliminares del SDK y se deshabilitó accidentalmente en la versión de disponibilidad general.
* Se ha cambiado el nombre de dos directivas de streaming predefinidas. **SecureStreaming** es ahora **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** es ahora **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>Noviembre de 2018

El módulo de la CLI 2.0 está ahora disponible para [Azure Media Services v3 con disponibilidad general](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest) – v 2.0.50.

### <a name="new-commands"></a>Nuevos comandos

- [az ams account](https://docs.microsoft.com/cli/azure/ams/account?view=azure-cli-latest)
- [az ams account-filter](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest)
- [az ams asset](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest)
- [az ams asset-filter](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)
- [az ams content-key-policy](https://docs.microsoft.com/cli/azure/ams/content-key-policy?view=azure-cli-latest)
- [az ams job](https://docs.microsoft.com/cli/azure/ams/job?view=azure-cli-latest)
- [az ams live-event](https://docs.microsoft.com/cli/azure/ams/live-event?view=azure-cli-latest)
- [az ams live-output](https://docs.microsoft.com/cli/azure/ams/live-output?view=azure-cli-latest)
- [az ams streaming-endpoint](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest)
- [az ams streaming-locator](https://docs.microsoft.com/cli/azure/ams/streaming-locator?view=azure-cli-latest)
- [az ams account mru](https://docs.microsoft.com/cli/azure/ams/account/mru?view=azure-cli-latest): permite administrar unidades reservadas de multimedia. Para más información, consulte [Escalado de unidades reservadas de multimedia](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nuevas características y cambios importantes

#### <a name="asset-commands"></a>Comandos de recursos

- Se han agregado los argumentos ```--storage-account``` y ```--container```.
- Se han agregado valores predeterminados para la hora de expiración (ahora +23 h) y los permisos (lectura) al comando ```az ams asset get-sas-url```.

#### <a name="job-commands"></a>Comandos de trabajos

- Se han agregado los argumentos ```--correlation-data``` y ```--label```.
- ```--output-asset-names``` cambia su nombre a ```--output-assets```. Ahora acepta una lista separada por espacios de recursos en formato "assetName=label". Se puede enviar un recurso sin etiqueta del siguiente modo: "assetName=".

#### <a name="streaming-locator-commands"></a>Comandos de localizador de streaming

- Se ha reemplazado el comando base ```az ams streaming locator``` por ```az ams streaming-locator```.
- Se han agregado los argumentos ```--streaming-locator-id``` y ```--alternative-media-id support```.
- Se ha actualizado el argumento ```--content-keys argument```.
- ```--content-policy-name``` cambia su nombre a ```--content-key-policy-name```.

#### <a name="streaming-policy-commands"></a>Comandos de directiva de streaming

- Se ha reemplazado el comando base ```az ams streaming policy``` por ```az ams streaming-policy```.
- Se ha agregado compatibilidad con los parámetros de cifrado en ```az ams streaming-policy create```.

#### <a name="transform-commands"></a>Comandos de transformación

- Se ha reemplazado el argumento ```--preset-names``` por ```--preset```. Ahora solo puede establecer una salida o valor preestablecido cada vez (para agregar más tendrá que ejecutar ```az ams transform output add```). Ademas, puede pasar la ruta de acceso al código JSON personalizado para establecer un StandardEncoderPreset personalizado.
- ```az ams transform output remove``` se puede realizar pasando el índice de salida a eliminar.
- Se han agregado argumentos ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` en los comandos ```az ams transform create``` y ```az ams transform output add```.

## <a name="october-2018---ga"></a>Octubre de 2018: disponibilidad general

En esta sección se describen las actualizaciones de octubre de Azure Media Services (AMS).

### <a name="rest-v3-ga-release"></a>Versión de disponibilidad general de REST v3

La [versión de disponibilidad general de REST v3](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) incluye otras API de Live, filtros de manifiesto de nivel de cuenta y recurso y compatibilidad con DRM.

#### <a name="azure-resource-management"></a>Administración de recursos de Azure 

La compatibilidad con la administración de recursos de Azure habilita la API de operaciones y administración unificada (ahora todo en un solo lugar).

A partir de esta versión, puede usar plantillas de Resource Manager para crear eventos en vivo.

#### <a name="improvement-of-asset-operations"></a>Mejora de las operaciones de recurso 

Se han introducido las siguientes mejoras:

- Ingesta de direcciones URL de HTTP o direcciones URL de SAS de Azure Blob Storage.
- Especifique sus propios nombres de contenedor para los recursos. 
- Compatibilidad con la salida más sencilla para crear flujos de trabajo personalizados con Azure Functions.

#### <a name="new-transform-object"></a>Nuevo objeto Transform

El nuevo objeto **Transform** simplifica el modelo de codificación. El nuevo objeto facilita la creación y el uso compartido de la codificación de plantillas y valores preestablecidos de Resource Manager. 

#### <a name="azure-active-directory-authentication-and-rbac"></a>Autenticación y RBAC de Azure Active Directory

El control de acceso basado en rol (RBAC) y la autenticación de Azure AD habilitan transformaciones seguras, objetos LiveEvent, directivas de clave de contenido o recursos por rol o usuarios en Azure AD.

#### <a name="client-sdks"></a>SDK de cliente  

Idiomas admitidos en Media Services v3: .NET Core, Java, Node.js, Ruby, Typescript, Python y Go.

#### <a name="live-encoding-updates"></a>Actualizaciones de Live Encoding

Se presentan las siguientes actualizaciones de Live Encoding:

- Nuevo modo de latencia baja de Live (10 segundos de principio a fin).
- Compatibilidad mejorada con RTMP (mayor estabilidad y mejor compatibilidad con codificadores de origen).
- Ingesta segura de RTMPS.

    Cuando se crea un evento en directo, ahora obtiene 4 direcciones URL de ingesta. Las cuatro direcciones URL de ingesta son casi idénticas, tienen el mismo token de streaming (AppId) y solo se diferencian en componente de número de puerto. Dos de las direcciones URL son principales y de copia de seguridad para RTMPS. 
- Soporte técnico de transcodificación las 24 horas. 
- Soporte técnico de señalización de anuncios mejorado en RTMP a través de SCTE35.

#### <a name="improved-event-grid-support"></a>Soporte técnico mejorado de Event Grid

Puede ver las siguientes mejoras de soporte técnico de Event Grid:

- Integración de Azure Event Grid para facilitar el desarrollo con Logic Apps y Azure Functions. 
- Suscríbase a eventos sobre codificación, canales en vivo y mucho más.

### <a name="cmaf-support"></a>Compatibilidad con CMAF

Compatibilidad con el cifrado de CMAF y "cbcs" para reproductores Apple HLS (iOS 11+) y MPEG-DASH que admiten CMAF.

### <a name="video-indexer"></a>Video Indexer

La versión de disponibilidad general Video Indexer se anunció en agosto. Para información reciente sobre las características admitidas actualmente, vea [Novedades de Video Indexer](../../cognitive-services/video-indexer/video-indexer-overview.md?toc=/azure/media-services/video-indexer/toc.json&bc=/azure/media-services/video-indexer/breadcrumb/toc.json). 

### <a name="plans-for-changes"></a>Planes de cambios

#### <a name="azure-cli-20"></a>CLI de Azure 2.0
 
Próximamente estará disponible el módulo de la CLI de Azure 2.0 que incluye operaciones en todas las características (como Live, directivas de clave de contenido, filtros de cuenta/recurso, directivas de streaming). 

### <a name="known-issues"></a>Problemas conocidos

Solo los clientes que usan la API de versión preliminar con filtros de cuenta o recurso se ven afectados por el problema siguiente.

Si creó filtros de cuenta o recurso entre el 28/09 y el 12/10 con las API o la CLI de Media Services v3, debe quitar todos los filtros de cuenta y recurso y volver a crearlos debido a un conflicto de versiones. 

## <a name="may-2018---preview"></a>Mayo de 2018: versión preliminar

### <a name="net-sdk"></a>.NET SDK

Las características siguientes están disponibles en el SDK de .NET:

* **Transformaciones** y **trabajos** para codificar o analizar el contenido multimedia. Para ver ejemplos, consulte los artículos sobre [streaming de archivos](stream-files-tutorial-with-api.md) y [análisis](analyze-videos-tutorial-with-api.md).
* **Localizadores de streaming** para publicar y transmitir contenido a los dispositivos de usuarios finales.
* **Directivas de streaming** y **directivas de claves de contenido** para configurar la entrega de claves y la protección de contenido (DRM) al entregar el contenido.
* **Eventos en directo** y **salidas de eventos** para configurar la ingesta y el archivo de contenido de streaming en vivo.
* **Recursos** para almacenar y publicado contenido multimedia en Azure Storage. 
* **Puntos de conexión de streaming** para configurar y escalar el empaquetado dinámico, cifrado y streaming tanto de contenido multimedia en vivo como a petición.

### <a name="known-issues"></a>Problemas conocidos

* Al enviar un trabajo, puede especificar que se ingiera el vídeo de origen mediante direcciones URL HTTPS, URL SAS o rutas de acceso a archivos ubicados en Azure Blob Storage. Actualmente, AMS v3 no admite la codificación de transferencia fragmentada a través de direcciones URL HTTPS.

## <a name="ask-questions-give-feedback-get-updates"></a>Formule preguntas, realice comentarios y obtenga actualizaciones

Consulte el artículo [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="next-steps"></a>Pasos siguientes

[Información general](media-services-overview.md)
