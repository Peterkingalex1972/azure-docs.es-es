---
title: Transformaciones y trabajos en Azure Media Services | Microsoft Docs
description: Al usar Media Services, debe crear una transformación para describir las reglas o especificaciones para procesar los vídeos. En este artículo se proporciona información general sobre qué es un transformación y cómo se usa.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/19/2019
ms.author: juliako
ms.openlocfilehash: 466ab0737aa5af40bd1bc137b98ab57a48feafde
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69637351"
---
# <a name="transforms-and-jobs"></a>Transformaciones y trabajos

En este tema se proporcionan detalles sobre las [transformaciones](https://docs.microsoft.com/rest/api/media/transforms) y los [trabajos](https://docs.microsoft.com/rest/api/media/jobs) y se explica la relación entre estas entidades. 

## <a name="overview"></a>Información general 

### <a name="transformsjobs-workflow"></a>Flujo de trabajo de las transformaciones y los trabajos

En el siguiente diagrama se muestra el flujo de trabajo de transformaciones y trabajos.

![Transformaciones](./media/encoding/transforms-jobs.png)

#### <a name="typical-workflow"></a>Flujo de trabajo típico

1. Cree una transformación 
2. Enviar trabajos en esa transformación 
3. Enumerar las transformaciones 
4. Elimine una transformación, si no planea usarla en el futuro. 

#### <a name="example"></a>Ejemplo

Imagine que desea extraer el primer fotograma de todos los vídeos como una imagen en miniatura; los pasos que debería seguir son: 

1. Definir la receta o regla para el procesamiento de los vídeos: "usar el primer fotograma del vídeo como la miniatura". 
2. Para cada vídeo, debería indicar lo siguiente al servicio: 
    1. Dónde encontrar ese vídeo,  
    2. Dónde escribir la imagen en miniatura de salida. 

Una **transformación** ayuda a crear la receta una vez (paso 1), y enviar trabajos mediante esa receta (paso 2).

> [!NOTE]
> Las propiedades de **Transformación** y **Trabajo** que son del tipo Datetime siempre están en formato UTC.

## <a name="transforms"></a>Transformaciones

Use **transformaciones** para configurar tareas comunes para codificar o analizar vídeos. Cada **Transformación** describe una receta, o un flujo de trabajo simple de tareas para procesar los archivos de vídeo o audio. Una única transformación puede aplicar más de una regla. Por ejemplo, una transformación podría especificar que cada vídeo se codifique como un archivo MP4 con una velocidad de bits determinada, y que se genere una imagen en miniatura a partir del primer fotograma del vídeo. Tendría que agregar una entrada TransformOutput para cada regla que quiera incluir en la transformación. Para indicar a la transformación cómo se deben procesar los archivos multimedia de entrada, use los valores preestablecidos.

### <a name="viewing-schema"></a>Visualización del esquema

En Media Services v3, los valores preestablecidos son entidades fuertemente tipadas en la propia API. Encontrará la definición de "esquema" de estos objetos en la [especificación de Open API (o Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01). También puede ver las definiciones preestablecidas (como **StandardEncoderPreset**) en la [API de REST](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) y el [SDK de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset?view=azure-dotnet) (o en otra documentación de referencia del SDK de Media Services v3).

### <a name="creating-transforms"></a>Creación de transformaciones

Puede crear transformaciones mediante REST, la CLI o cualquiera de los SDK publicados. Azure Resource Manager controla la API v3 de Media Services, por lo que también se pueden usar plantillas de Resource Manager para crear e implementar las transformaciones en la cuenta de Media Services. El control de acceso basado en rol se puede usar para bloquear el acceso a las transformaciones.

### <a name="updating-transforms"></a>Actualización de transformaciones

Si necesita actualizar su [transformación](https://docs.microsoft.com/rest/api/media/transforms), use la operación de **actualización**. Está diseñada para realizar cambios en la descripción o las prioridades del elemento TransformOutputs subyacente. Se recomienda realizar tales actualizaciones cuando se hayan completado todos los trabajos en curso. Si piensa volver a escribir la receta, deberá crear una nueva transformación.

### <a name="transform-object-diagram"></a>Diagrama del objeto Transformación

En el diagrama siguiente se muestra el objeto **Transformación** y los objetos a los que hace referencia, incluidas las relaciones de derivación. Las flechas grises indican un tipo al que hace referencia el trabajo y las flechas verdes indican las relaciones de derivación de clase.<br/>Haga clic en la imagen para verla a tamaño completo.  

<a href="./media/api-diagrams/transform-large.png" target="_blank"><img src="./media/api-diagrams/transform-small.png"></a> 

## <a name="jobs"></a>Trabajos

Un **trabajo** es la solicitud real a Azure Media Services para aplicar la **transformación** a un contenido de vídeo o audio determinado. Una vez creada la transformación, puede enviar trabajos mediante las API de Media Services o cualquiera de los SDK publicados. El **trabajo** especifica información como la ubicación del vídeo de entrada y la ubicación de la salida. Puede especificar la ubicación del vídeo de entrada mediante: direcciones URL HTTPS, direcciones URL SAS o [recursos](https://docs.microsoft.com/rest/api/media/assets).  

### <a name="job-input-from-https"></a>Entrada de trabajo desde HTTPS

Use una [entrada de trabajo desde HTTPS](job-input-from-http-how-to.md) si el contenido ya es accesible a través de una dirección URL y no necesita almacenar el archivo de código fuente en Azure (por ejemplo, importación de S3). Este método también es adecuado si tiene el contenido en Azure Blob Storage, pero no necesita que el archivo se encuentre en un recurso. Actualmente, este método solo admite un único archivo de entrada.

### <a name="asset-as-job-input"></a>Recurso como entrada de trabajo

Use un [recurso como entrada de trabajo](job-input-from-local-file-how-to.md) si el contenido de entrada ya se encuentra en un recurso o si está almacenado en un archivo local. También es una buena opción si va a publicar el recurso de entrada para el streaming o la descarga (imagine que quiere publicar los mp4 para su descarga, pero también le interesa realizar conversión de voz en texto o detección de caras). Este método es compatible con varios recursos de archivos (por ejemplo, conjuntos de streaming de MBR codificados localmente).

### <a name="checking-job-progress"></a>Comprobación del progreso del trabajo

El progreso y estado de los trabajos se pueden obtener mediante la supervisión de eventos con Event Grid. Para obtener más información, vea [Supervisión de eventos mediante EventGrid](job-state-events-cli-how-to.md).

### <a name="updating-jobs"></a>Actualización de los trabajos

La operación de actualización en la entidad [Trabajo](https://docs.microsoft.com/rest/api/media/jobs) puede usarse para modificar las propiedades *description* y *priority* después de enviar el trabajo. Un cambio en la propiedad *priority* es eficaz solo si el trabajo todavía está en un estado en cola. Si el trabajo ha comenzado a procesarse o ha finalizado, cambiar la prioridad no tiene ningún efecto.

### <a name="job-object-diagram"></a>Diagrama del objeto Trabajo

En el diagrama siguiente se muestra el objeto **Trabajo** y los objetos a los que hace referencia, incluidas las relaciones de derivación.<br/>Haga clic en la imagen para verla a tamaño completo.  

<a href="./media/api-diagrams/job-large.png" target="_blank"><img src="./media/api-diagrams/job-small.png"></a> 

## <a name="configure-media-reserved-units"></a>Configuración de las unidades reservadas de multimedia

En trabajos de análisis de audio y vídeo desencadenados por Media Services v3 o Video Indexer, se recomienda encarecidamente aprovisionar la cuenta con 10 unidades reservadas de multimedia (MRU) S3. Si necesita más de 10 MRU S3, abra una incidencia de soporte técnico desde [Azure Portal](https://portal.azure.com/).

Para más información, consulte [Escalado de procesamiento de medios con la CLI](media-reserved-units-cli-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Formule preguntas, realice comentarios y obtenga actualizaciones

Consulte el artículo [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="see-also"></a>Otras referencias

* [Códigos de error](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode)
* [Filtrado, ordenación y paginación de entidades de Media Services](entities-overview.md)

## <a name="next-steps"></a>Pasos siguientes

- Antes de comenzar con el desarrollo, vea [Desarrollo con las API de Media Services v3](media-services-apis-overview.md) (incluye información sobre cómo acceder a las API, convenciones de nomenclatura, etc.).
- Consulte estos tutoriales:

    - [Tutorial: Codificación de un archivo remoto según una dirección URL y transmisión del vídeo: REST](stream-files-tutorial-with-rest.md)
    - [Tutorial: Carga, codificación y streaming de vídeos](stream-files-tutorial-with-api.md)
    - [Tutorial: Análisis de vídeos con Media Services v3](analyze-videos-tutorial-with-api.md)
