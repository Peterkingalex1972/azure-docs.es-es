---
title: Desarrollo con API v3 - Azure | Microsoft Docs
description: En este artículo se analizan las reglas que se aplican a las entidades y las API cuando se desarrolla con Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/05/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 26fea4322df625b2e38028a3b7121fb41f2acf81
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68311857"
---
# <a name="developing-with-media-services-v3-apis"></a>Desarrollo con API de Media Services v3

Como desarrollador, puede usar la [API de REST](https://aka.ms/ams-v3-rest-ref) de Media Services o bibliotecas de cliente que le permitan interactuar con la API de REST para crear, administrar y mantener fácilmente flujos de trabajo multimedia personalizados. La API de [Media Services v3](https://aka.ms/ams-v3-rest-sdk) se basa en la especificación OpenAPI (anteriormente conocida como Swagger).

En este artículo se analizan las reglas que se aplican a las entidades y las API cuando se desarrolla con Media Services v3.

## <a name="accessing-the-azure-media-services-api"></a>Acceso a la API de Azure Media Services

Para ser autorizado a acceder a recursos de Media Services y a Media Services API, se debe autenticar primero. Media Services admite la[ autenticación basada en Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md). Dos opciones comunes de autenticación son:
 
* La **Autenticación de entidad de servicio**: se usa para autenticar un servicio (por ejemplo: aplicaciones web, aplicaciones de funciones, aplicaciones lógicas, API y microservicios). Las aplicaciones que normalmente utilizan este método de autenticación son las que ejecutan servicios de demonio, servicios de nivel intermedio o trabajos programados. Por ejemplo, para las aplicaciones web, siempre debe haber un nivel intermedio que se conecte a Media Services con una entidad de servicio.
* **Autenticación de usuario**: se usa para autenticar a una persona que está usando la aplicación para interactuar con los recursos de Azure Media Services. La aplicación interactiva, en primer lugar, debe solicitar al usuario las credenciales. Un ejemplo es una aplicación de consola de administración que usan los usuarios autorizados para supervisar trabajos de codificación o streaming en vivo.

La API de Media Services requiere que el usuario o la aplicación que realiza las solicitudes de la API de REST tenga acceso al recurso de la cuenta de Media Services y use una función de **Colaborador** o **Propietario**. Se puede acceder a la API con la función **Lector** pero solo estarán disponibles las operaciones **Obtener** o **Enumerar** . Para obtener más información, consulte [Role-based access control for Media Services accounts](rbac-overview.md) (Control de acceso basado en roles para las cuentas de Media Services).

En lugar de crear una entidad de servicio, use las identidades administradas de los recursos de Azure para acceder a la API de Media Services a través de Azure Resource Manager. Para obtener más información sobre las identidades administradas para los recursos de Azure, consulte [¿Qué es Managed Identities for Azure Resources?](../../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-ad-service-principal"></a>Entidad de servicio de Azure AD 

Si está creando una aplicación de Azure AD y una entidad de servicio, la aplicación debe estar en su propio inquilino. Después de crear la aplicación, asigne al rol **Colaborador** o **Propietario** de la aplicación acceso a la cuenta de Media Services. 

Si no está seguro de tener permisos para crear una aplicación de Azure AD, consulte [Permisos necesarios](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

En la ilustración siguiente, los números representan el flujo de las solicitudes en orden cronológico:

![Aplicaciones de nivel intermedio](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. Una aplicación de nivel medio solicita un token de acceso de Azure AD que tiene los siguientes parámetros:  

   * Punto de conexión de inquilino de Azure AD.
   * URI del recurso de Media Services.
   * URI del recurso de Media Services de REST.
   * Valores de aplicación de Azure AD: el Id. de cliente y el secreto de cliente.
   
   Para obtener todos los valores necesarios, consulte [Access Azure Media Services API with the Azure CLI](access-api-cli-how-to.md) (Acceso a la API de Azure Media Services con la CLI de Azure).

2. El token de acceso de Azure AD se envía al nivel intermedio.
4. El nivel intermedio envía una solicitud a la API de REST de Azure Media Services con el token de Azure AD.
5. El nivel intermedio recibe los datos de Media Services.

### <a name="samples"></a>Ejemplos

Consulte los siguientes ejemplos que muestran cómo conectarse con la entidad de servicio de Azure AD:

* [Conexión con REST](media-rest-apis-with-postman.md)  
* [Conexión con Java](configure-connect-java-howto.md)
* [Conexión con .NET](configure-connect-dotnet-howto.md)
* [Conexión con Node.js](configure-connect-nodejs-howto.md)
* [Conexión con Python](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Convenciones de nomenclatura

Los nombres de recursos de Azure Media Services v3 (por ejemplo, recursos, trabajos y transformaciones) están sujetos a las restricciones de nomenclatura de Azure Resource Manager. De acuerdo con Azure Resource Manager, los nombres de los recursos siempre son únicos. Por lo tanto, puede usar cualquier cadena de identificador único (por ejemplo, GUID) para los nombres de recursos. 

Los nombres de recursos de Media Services no pueden incluir: '<', '>', '%', '&', ':', '&#92;', '?', '/', '*', '+', '.', el carácter de comilla simple o cualquier carácter de control. Todos los demás caracteres se permiten. La longitud máxima de un nombre de recurso es de 260 caracteres. 

Para más información sobre la nomenclatura de Azure Resource Manager, consulte: [Requisitos de nomenclatura](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) y [Convenciones de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions).

## <a name="long-running-operations"></a>Operaciones de larga duración

Las operaciones marcadas con `x-ms-long-running-operation` en los [archivos de Swagger](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) de Azure Media Services son operaciones de larga duración. 

Para obtener detalles sobre cómo realizar un seguimiento de las operaciones asincrónicas de Azure, consulte [Operaciones asincrónicas](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation).

Media Services tiene las siguientes operaciones de larga duración:

* [Crear LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Actualizar LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [Eliminar LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Iniciar LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [Detener LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  Use el parámetro `removeOutputsOnStop` para eliminar todos los objetos LiveOutput asociados al detener el evento.  
* [Restablecer LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [Crear LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [Eliminar LiveOutput](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [Crear StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [Actualizar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [Eliminar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [Iniciar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [Detener StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [Escalar StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

Si se envía correctamente una operación larga, recibirá un mensaje "202 Aceptado" y deberá sondear la finalización de la operación con el identificador de operación devuelto.

Solo se admite una operación de larga duración para un LiveEvent determinado o para cualquiera de sus LiveOutput asociados. Una vez iniciada, la operación de larga duración se debe completar antes de iniciar una operación de larga duración posterior en el mismo LiveEvent o en cualquier LiveOutput asociado. En el caso de los LiveEvent con varios LiveOutput, debe esperar a que se complete una operación de larga duración en un LiveOutput antes de desencadenar una operación de larga duración en otro LiveOutput. 

## <a name="sdks"></a>SDK

> [!NOTE]
> No se garantiza que los SDK de Azure Media Services v3 sean seguros para subprocesos. Al desarrollar una aplicación multiproceso, debe agregar su propia lógica de sincronización de subprocesos para proteger el cliente o usar un nuevo objeto AzureMediaServicesClient por subproceso. También debe tener cuidado con los problemas de subprocesos múltiples introducidos por objetos opcionales que proporciona el código al cliente (por ejemplo, una instancia de HttpClient en .NET).

|SDK|Referencia|
|---|---|
|[SDK de .NET](https://aka.ms/ams-v3-dotnet-sdk)|[Referencia de .NET](https://aka.ms/ams-v3-dotnet-ref)|
|[SDK de Java](https://aka.ms/ams-v3-java-sdk)|[Referencia de Java](https://aka.ms/ams-v3-java-ref)|
|[SDK de Python](https://aka.ms/ams-v3-python-sdk)|[Referencia de Python](https://aka.ms/ams-v3-python-ref)|
|[SDK de Node.js](https://aka.ms/ams-v3-nodejs-sdk) |[Referencia de Node.js](https://aka.ms/ams-v3-nodejs-ref)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Referencia de Go](https://aka.ms/ams-v3-go-ref)|
|[SDK de Ruby](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Otras referencias

- [EventGrid .NET SDK that includes Media Service events](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/) (SDK de .NET en EventGrid que incluye eventos de Media Services)
- [Definitions of Media Services events](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json) (Definición de eventos de Media Services)

## <a name="azure-media-services-explorer"></a>Explorador de Azure Media Services

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) es una herramienta disponible para los clientes de Windows que desean obtener información acerca de Media Services. AMSE es una aplicación de Winforms o C# que permite cargar, descargar, codificar, transmitir vídeo bajo demanda y contenido en directo con Media Services. Esta herramienta es para aquellos clientes que deseen probar Media Services sin escribir ningún código. El código AMSE se proporciona como un recurso para los clientes que desean desarrollar con Media Services.

AMSE es un proyecto de código abierto en el que el soporte técnico lo facilita la comunidad (se pueden notificar los problemas a https://github.com/Azure/Azure-Media-Services-Explorer/issues) ). Este proyecto ha adoptado el [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) (Código de conducta de código abierto de Microsoft). Para más información, consulte las [preguntas más frecuentes del código de conducta](https://opensource.microsoft.com/codeofconduct/faq/) o póngase en contacto con opencode@microsoft.com si tiene cualquier otra pregunta o comentario.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Filtrado, ordenación y paginación de entidades de Media Services

Consulte [Filtrado, ordenación y paginación de entidades de Azure Media Services](entities-overview.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Formule preguntas, realice comentarios y obtenga actualizaciones

Consulte el artículo [Comunidad de Azure Media Services](media-services-community.md) para ver diferentes formas de formular preguntas, enviar comentarios y obtener actualizaciones de Media Services.

## <a name="see-also"></a>Otras referencias

[CLI de Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Pasos siguientes

* [Conexión a Media Services con Java](configure-connect-java-howto.md)
* [Conexión a Media Services con .NET](configure-connect-dotnet-howto.md)
* [Conexión a Media Services con Node.js](configure-connect-nodejs-howto.md)
* [Conexión a Media Services con Python](configure-connect-python-howto.md)
