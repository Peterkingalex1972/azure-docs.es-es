---
title: Recopilación de los parámetros obligatorios
services: cognitive-services
author: IEvangelist
manager: nitinme
description: Los parámetros de todos los contenedores de Cognitive Services
ms.service: cognitive-services
ms.topic: include
ms.date: 7/24/2019
ms.author: dapine
ms.openlocfilehash: 636a41fde345a08db1549e53626522962f9cf74f
ms.sourcegitcommit: bafb70af41ad1326adf3b7f8db50493e20a64926
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2019
ms.locfileid: "68488762"
---
## <a name="gathering-required-parameters"></a>Recopilación de los parámetros obligatorios

Hay tres parámetros principales para todos los contenedores de Cognitive Services que son necesarios. El contrato de licencia para el usuario final (**CLUF**) debe estar presente con un valor de `accept`. Además, se necesitan una dirección URL de punto de conexión y una clave de API.

### <a name="endpoint-uri-endpoint_uri"></a>URI de punto de conexión `{Endpoint_URI}`

El valor del URI del **punto de conexión** está disponible en la página *Información general* de Azure Portal del recurso de Cognitive Services correspondiente. Vaya a la página *Información general*, mantenga el cursor sobre el punto de conexión y aparecerá un icono `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span>. Cópielo y utilícelo cuando sea necesario.

![Recopilación el URI del punto de conexión para su uso posterior](../media/overview-endpoint-uri.png)

### <a name="keys-api_key"></a>Claves `{API_Key}`

Esta clave se usa para iniciar el contenedor y está disponible en la página de claves de Azure Portal del recurso de Cognitive Services correspondiente. Vaya a la página *Claves* y haga clic en el icono `Copy to clipboard` <span class="docon docon-edit-copy x-hidden-focus"></span>.

![Obtención de una de las dos claves para su uso posterior](../media/keys-copy-api-key.png)

> [!IMPORTANT]
> Estas claves de suscripción se usan para tener acceso a la API de Cognitive Services. No comparta las claves. Almacénelas de forma segura, por ejemplo, con Azure Key Vault. También se recomienda regenerar estas claves periódicamente. Solo se necesita una clave para realizar una llamada API. Al volver a generar la primera clave, puede usar la segunda clave para seguir teniendo acceso al servicio.