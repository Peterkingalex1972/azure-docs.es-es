---
title: Publicación de una oferta | Azure Marketplace
description: API para publicar la oferta especificada.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: reference
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 117a4e5e238e754524ff813ce25ebc1105e2153c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64934978"
---
<a name="publish-an-offer"></a>Publicación de una oferta
================

Inicia el proceso de publicación de la oferta especificada. Esta llamada es una operación de larga duración.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

<a name="uri-parameters"></a>Parámetros del identificador URI
--------------

|  **Nombre**      |    **Descripción**                               |  **Tipo de datos** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador del publicador, por ejemplo `contoso`.      |   Cadena       |
|  offerId       | Identificador de la oferta.                                 |   Cadena       |
|  api-version   | La versión más reciente de la API                        |   Date         |
|  |  |


<a name="header"></a>Encabezado
------

|  **Nombre**        |    **Valor**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  Autorización   |  `Bearer YOUR_TOKEN`  |
|  |  |


<a name="body-example"></a>Ejemplo de cuerpo
------------

### <a name="request"></a>Solicitud

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Propiedades del cuerpo de la solicitud

|  **Nombre**               |   **Descripción**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | Lista de direcciones de correo electrónico separadas por comas para recibir una notificación del progreso de la operación de publicación. |
|  |  |


### <a name="response"></a>Response

   `Operation-Location: /api/operations/contoso$56615b67-2185-49fe-80d2-c4ddf77bb2e8$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>Encabezado de respuesta

|  **Nombre**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Operation-Location    | Dirección URL que se puede consultar para determinar el estado actual de la operación.    |
|  |  |


### <a name="response-status-codes"></a>Códigos de estado de respuesta

| **Código** |  **Descripción**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted`: La solicitud se aceptó correctamente. La respuesta contiene una ubicación que se puede usar para realizar un seguimiento de la operación que se inicia. |
| 400   | `Bad/Malformed request`: el cuerpo de la respuesta del error puede proporcionar más información.                                                               |
| 422   | `Un-processable entity`: indica que la entidad que se va a publicar no se pudo validar.                                                        |
| 404   | `Not found`: la entidad especificada por el cliente no existe.                                                                              |
|  |  |
