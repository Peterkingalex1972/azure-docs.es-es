---
title: Localizadores de streaming en Azure Media Services | Microsoft Docs
description: En este artículo se explica qué son los localizadores de streaming y cómo los usa Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/26/2019
ms.author: juliako
ms.openlocfilehash: 5897b7df2460257784c40eb974c473573ec4003d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66299169"
---
# <a name="streaming-locators"></a>Localizadores de streaming

Para que los vídeos en el recurso de salida estén disponibles para los clientes para reproducción, tiene que crear un objeto [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) y, luego, generar direcciones URL de streaming. Para crear una dirección URL, debe concatenar el nombre de host del punto de conexión de streaming y la ruta de acceso del objeto StreamingLocator. Para un ejemplo de.NET, consulte [Obtención de un objeto StreamingLocator](stream-files-tutorial-with-api.md#get-a-streaming-locator).

El proceso de creación de un objeto **StreamingLocator** se denomina publicación. De forma predeterminada, el objeto **StreamingLocator** es válido inmediatamente después de realizar las llamadas a la API y dura hasta que se elimina, salvo que configure las horas de inicio y de finalización opcionales. 

Al crear un objeto **StreamingLocator**, debe especificar el nombre del **Recurso** y el nombre de la **Directiva de streaming**. Para obtener más información, consulte los temas siguientes:

* [Recursos](assets-concept.md)
* [Directivas de streaming](streaming-policy-concept.md)
* [Directivas de claves de contenido](content-key-policy-concept.md)

También puede especificar la fecha inicial y final en el localizador de streaming, que solo permitirá al usuario reproducir el contenido entre estos períodos (por ejemplo, entre el 1/5/2019 hasta el 5/5/2019).  

## <a name="considerations"></a>Consideraciones

* Los **localizadores de streaming** no son actualizables. 
* Las propiedades de objetos **StreamingLocator** que son del tipo Datetime siempre están en formato UTC.
* Debería diseñar un conjunto limitado de directivas para su cuenta de Media Service y reutilizarlas con los objetos StreamingLocator siempre que se necesiten las mismas opciones. Para más información, consulte [Cuotas y limitaciones](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Creación de localizadores de streaming  

### <a name="not-encrypted"></a>No cifrado

Si quiere transmitir el archivo sin cifrar, establezca la directiva de streaming sin cifrar predefinida en "Predefined_ClearStreamingOnly" (en. NET, puede usar la enumeración PredefinedStreamingPolicy.ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Cifrados 

Si necesita cifrar el contenido con cifrado CENC, establezca la directiva en "Predefined_MultiDrmCencStreaming". El cifrado Widevine se aplicará a una transmisión de DASH y PlayReady a Smooth. La clave se entregará a un cliente de reproducción en función de las licencias DRM configuradas.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Si también quiere cifrar su transmisión de HLS con CBCS (FairPlay), use "Predefined_MultiDrmStreaming".

## <a name="associate-filters-with-streaming-locators"></a>Asociación de filtros con localizadores de streaming

Consulte [Asociación de filtros con localizadores de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrado, solicitud y paginación de entidades de localizador de streaming

Consulte [Filtrado, ordenación y paginación de entidades de Media Services](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Enumeración de los localizadores de streaming por nombre de recurso

Para obtener los localizadores de streaming según el nombre de recurso asociado, use las siguientes operaciones:

|Idioma|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams asset list-streaming-locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/java/api/com.microsoft.azure.management.mediaservices.v2018_07_01.assetstreaminglocator?view=azure-java-stable)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/assets?view=azure-node-latest#liststreaminglocators-string--string--string--object-)|

## <a name="also-see"></a>Consulte también:

* [Recursos](assets-concept.md)
* [Directivas de streaming](streaming-policy-concept.md)
* [Directivas de claves de contenido](content-key-policy-concept.md)

## <a name="next-steps"></a>Pasos siguientes

[Tutorial: Carga, codificación y transmisión en secuencias de videos mediante .NET](stream-files-tutorial-with-api.md)
