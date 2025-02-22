---
title: Automatización del aprovisionamiento de aplicaciones con SCIM en Azure Active Directory | Microsoft Docs
description: Azure Active Directory puede aprovisionar automáticamente los usuarios y grupos a cualquier aplicación o almacén de identidades proporcionado por un servicio web con la interfaz definida en la especificación del protocolo SCIM
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: mimart
ms.reviewer: arvinh
ms.custom: aaddev;it-pro;seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48eb36151948dc6e39edd4ae0fd863c63e83a52b
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68741364"
---
# <a name="using-system-for-cross-domain-identity-management-scim-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Uso de System for Cross-Domain Identity Management (SCIM) para aprovisionar automáticamente a los usuarios y grupos de Azure Active Directory para aplicaciones

SCIM es un protocolo y esquema estandarizado que tiene como objetivo conseguir una mayor coherencia en la forma en la que se administran las identidades entre los sistemas. Cuando una aplicación es compatible con un punto de conexión SCIM para la administración de usuarios, el servicio de aprovisionamiento de usuario de Azure AD puede enviar solicitudes para crear, modificar o eliminar usuarios y grupos asignados a este punto de conexión.

Muchas de las aplicaciones para las que Azure AD admite [aprovisionamiento automático de usuarios preintegrado](../saas-apps/tutorial-list.md), implementan SCIM como la forma de recibir notificaciones de cambio del usuario.  Además, los clientes pueden conectar las aplicaciones que admiten un perfil específico de la [especificación del protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644) con la opción de integración genérica "fuera de la galería" en Azure Portal.

El enfoque principal de este artículo se encuentra en el perfil de SCIM 2.0 que Azure AD implementa como parte de su conector genérico de SCIM para las aplicaciones fuera de la galería. De todas formas, la correcta realización de pruebas de una aplicación que admite SCIM con el conector genérico de Azure AD, es un paso para que una aplicación se muestre en la galería de Azure AD como que admite el aprovisionamiento de usuarios. Para más información sobre cómo hacer que la aplicación aparezca en la galería de aplicaciones de Azure AD, consulte [Procedimientos: Registro de una aplicación en la galería de aplicaciones de Azure AD](../develop/howto-app-gallery-listing.md).

> [!IMPORTANT]
> El comportamiento de la implementación de SCIM de Azure AD se actualizó por última vez el 18 de diciembre de 2018. Para obtener información sobre los cambios que se han producido, consulte [SCIM 2.0 protocol compliance of the Azure AD User Provisioning service](application-provisioning-config-problem-scim-compatibility.md) (Cumplimiento del protocolo SCIM 2.0 del servicio de aprovisionamiento de usuarios de Azure AD).

![Muestra el aprovisionamiento desde Azure AD a una aplicación o un almacén de identidades][0]<br/>
*Figura 1: Aprovisionamiento desde Azure Active Directory a un aplicación o almacén de identidades que implementa SCIM*

Este artículo se divide en cuatro secciones:

* **[Aprovisionamiento de usuarios y grupos para aplicaciones de terceros que admiten SCIM 2.0](#provisioning-users-and-groups-to-applications-that-support-scim)** : si su organización usa una aplicación de terceros que implementa el perfil de SCIM 2.0 que Azure AD admite, puede empezar a automatizar tanto el aprovisionamiento como el desaprovisionamiento de usuarios y grupos de forma inmediata.
* **[Información sobre la implementación de SCIM de Azure AD](#understanding-the-azure-ad-scim-implementation)** : si está creando una aplicación que admite una API de administración de usuario de SCIM 2.0, esta sección describe en detalle cómo se implementa el cliente de SCIM de Azure AD, y cómo debe modelar el control y las respuestas de la solicitud de protocolo SCIM.
* **[Creación de punto de conexión SCIM con las bibliotecas CLI de Microsoft](#building-a-scim-endpoint-using-microsoft-cli-libraries)** : las bibliotecas de Common Language Infrastructure (CLI) junto con ejemplos de código, muestran cómo desarrollar un punto de conexión SCIM y traducir los mensajes SCIM.  
* **[Referencia de esquema de grupo y usuario](#user-and-group-schema-reference)** : describe el esquema de grupo y usuario compatibles con la implementación de SCIM de Azure AD para las aplicaciones fuera de la galería.

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamiento de usuarios y grupos para aplicaciones que admiten SCIM

Azure AD se puede configurar para que aprovisione automáticamente usuarios y grupos asignados a aplicaciones que implementan un perfil específico del [protocolo SCIM 2.0](https://tools.ietf.org/html/rfc7644). Los detalles del perfil están documentados en [Información sobre la implementación de SCIM de Azure AD](#understanding-the-azure-ad-scim-implementation).

Consulte a su proveedor de la aplicación o la documentación que se incluye con la aplicación para ver si existen declaraciones de compatibilidad con estos requisitos.

> [!IMPORTANT]
> La implementación de SCIM de Azure AD se basa en el servicio de aprovisionamiento de usuarios de Azure AD, que está diseñado para mantener a los usuarios constantemente sincronizados entre Azure AD y la aplicación de destino, y que implementa un conjunto muy específico de operaciones estándar. Es importante comprender estos comportamientos para entender el comportamiento del cliente de SCIM de Azure AD. Para más información, consulte [¿Qué ocurre durante el aprovisionamiento de usuarios?](user-provisioning.md#what-happens-during-provisioning)

### <a name="getting-started"></a>Introducción

Las aplicaciones que admiten el perfil SCIM descrito en este artículo se pueden conectar a Azure Active Directory mediante la característica "de aplicación situada fuera de la galería" de la galería de aplicaciones de Azure AD. Una vez conectadas, Azure AD ejecuta un proceso de sincronización cada 40 minutos en el que consulta el punto de conexión SCIM de la aplicación para ver los usuarios y grupos asignados, y los crea o modifica según los detalles de asignación.

**Para conectar una aplicación que admite SCIM:**

1. Inicie sesión en el [portal de Azure Active Directory](https://aad.portal.azure.com). 
1. En el panel izquierdo, seleccione **Aplicaciones empresariales**. Se muestra una lista de las aplicaciones configuradas, incluidas aquellas que se han agregado desde la galería.
1. Seleccione **+ Nueva aplicación** > **Todas** > **Aplicación situada fuera de la galería**.
1. Escriba un nombre para la aplicación y seleccione **Agregar** para crear un objeto de aplicación. La nueva aplicación se agrega a la lista de aplicaciones empresariales y se abre en su pantalla de administración de la aplicación.

   ![Captura de pantalla que muestra la galería de aplicaciones de Azure AD][1]<br/>
   *Figura 2: Galería de aplicaciones de Azure AD*

1. En la pantalla de administración de la aplicación, seleccione **Aprovisionamiento** en el panel izquierdo.
1. En el menú **Modo de aprovisionamiento**, seleccione **Automático**.

   ![Ejemplo: Página Aprovisionamiento de una aplicación en Azure Portal][2]<br/>
   *Figura 3: Configuración del aprovisionamiento en Azure Portal*

1. En el campo **Dirección URL del inquilino**, escriba la dirección URL del punto de conexión SCIM de la aplicación. Ejemplo: https://api.contoso.com/scim/
1. Si el punto de conexión SCIM requiere un token de portador OAuth de un emisor que no sea Azure AD, copie el token de portador OAuth necesario en el campo **Token secreto**. Si este campo se deja en blanco, Azure AD incluye un token de portador OAuth emitido desde Azure AD con cada solicitud. Las aplicaciones que usan Azure AD como un proveedor de identidades pueden validar este token que emitió Azure AD.
1. Seleccione **Probar conexión** para que Azure Active Directory intente conectarse al punto de conexión SCIM. Si se produce un error en el intento, se muestra la información de error.  

    > [!NOTE]
    > La **prueba de conexión** consulta el punto de conexión SCIM de un usuario que no existe mediante un GUID aleatorio, como la propiedad de coincidencia seleccionada en la configuración de Azure AD. La respuesta correcta esperada es HTTP 200 OK con un mensaje ListResponse de SCIM vacío.

1. Si la conexión se realiza con éxito, a continuación, seleccione **Guardar** para guardar las credenciales de administrador.
1. En la sección **Asignaciones**, hay dos conjuntos seleccionables de asignaciones de atributos: uno para los objetos de usuario y otro para los objetos de grupo. Seleccione cada uno de ellos para revisar los atributos que se sincronizan desde Azure Active Directory a la aplicación. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los usuarios y grupos de la aplicación con el objetivo de realizar operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.

    > [!NOTE]
    > Opcionalmente, puede deshabilitar la sincronización de objetos de grupo deshabilitando la asignación de "grupos".

1. En **Settings** (Configuración), el campo **Scope** (Ámbito) define qué usuarios y grupos se sincronizan. Seleccione **Sincronizar solo los usuarios y grupos asignados** (recomendado) para que se sincronicen solamente los usuarios y los grupos asignados en la pestaña **Usuarios y grupos**.
1. Una vez completada la configuración, cambie el **Estado de aprovisionamiento** a **Activado**.
1. Seleccione **Guardar** para iniciar el servicio de aprovisionamiento de Azure AD.
1. Al sincronizar solo los usuarios y grupos asignados (recomendado), no olvide seleccionar la pestaña **Usuarios y grupos** y asignar los usuarios o grupos que quiere sincronizar.

Una vez que haya iniciado la sincronización inicial, puede seleccionar **Registros de auditoría** en el panel izquierdo para supervisar el progreso, con esto se muestran todas las acciones realizadas por el servicio de aprovisionamiento en la aplicación. Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](check-status-user-account-provisioning.md).

> [!NOTE]
> La sincronización inicial tarda más tiempo en realizarse que las posteriores, que se producen aproximadamente cada 40 minutos si se está ejecutando el servicio.

## <a name="understanding-the-azure-ad-scim-implementation"></a>Información sobre la implementación de SCIM de Azure AD

Si está creando una aplicación que admite una API de administración de usuario de SCIM 2.0, esta sección describe en detalle cómo se implementa el cliente de SCIM de Azure AD, y cómo debe modelar el control y las respuestas de la solicitud de protocolo SCIM. Una vez que haya implementado el punto de conexión SCIM, puede probarlo con el procedimiento descrito en la sección anterior.

Dentro de la [especificación del protocolo SCIM 2.0](http://www.simplecloud.info/#Specification), la aplicación tiene que cumplir estos requisitos:

* Admitir la creación de usuarios y de forma opcional también de grupos, según la sección [3.3 del protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.3).  
* Admitir la modificación de usuarios o grupos con solicitudes PATCH, según la [sección 3.5.2 del protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.5.2).  
* Admitir la recuperación de un recurso conocido, para un usuario o un grupo creado anteriormente, según la [sección 3.4.1 del protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.1).  
* Admitir la consulta de usuarios o grupos, según la sección [3.4.2 del protocolo SCIM](https://tools.ietf.org/html/rfc7644#section-3.4.2).  De forma predeterminada, los usuarios se recuperan por sus `id` y se consultan por sus `username` y `externalid`, y los grupos por su `displayName`.  
* Admitir la consulta de usuarios por identificador y por administrador, según la sección 3.4.2 del protocolo SCIM.  
* Admitir la consulta de grupos por Id. y miembro, según la sección 3.4.2 del protocolo SCIM.  
* Acepta un token de portador único para la autenticación y autorización de Azure AD para la aplicación.

Siga estas directrices generales al implementar un punto de conexión SCIM para garantizar la compatibilidad con Azure AD:

* `id` es una propiedad obligatoria para todos los recursos. Todas las respuestas que devuelve un recurso deben asegurarse de que cada recurso tiene esta propiedad, excepto `ListResponse` con cero miembros.
* La respuesta a una solicitud de consulta o filtrado siempre debe ser una `ListResponse`.
* Los grupos son opcionales, pero solo se admiten si la implementación de SCIM admite las solicitudes PATCH.
* No es necesario incluir todo el recurso completo en la respuesta PATCH.
* Microsoft Azure AD solo usa los siguientes operadores:  
     - `eq`
     - `and`
* No solicite una coincidencia entre mayúsculas y minúsculas en los elementos estructurales de SCIM, en particular en los valores de operación PATCH `op`, como se define en https://tools.ietf.org/html/rfc7644#section-3.5.2. Azure AD emite los valores de "op" como `Add`, `Replace`, y `Remove`.
* Microsoft Azure AD realiza solicitudes para recuperar un usuario y un grupo aleatorio para asegurarse de que el punto de conexión y las credenciales son válidas. También se realiza como parte del flujo de la **Prueba de conexión** en [Azure Portal](https://portal.azure.com). 
* El atributo sobre el que se pueden consultar los recursos debe establecerse como un atributo coincidente en la aplicación en [Azure Portal](https://portal.azure.com). Para más información, consulte [Personalización de asignaciones de atributos de aprovisionamiento de usuarios](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

### <a name="user-provisioning-and-de-provisioning"></a>Aprovisionamiento y cancelación del aprovisionamiento de usuarios

La siguiente ilustración muestra los mensajes que Azure Active Directory envía a un servicio SCIM para administrar el ciclo de vida de un usuario en su almacén de identidades.  

![Muestra la secuencia de aprovisionamiento y desaprovisionamiento de usuarios][4]<br/>
*Ilustración 4: Secuencia de aprovisionamiento y cancelación de aprovisionamiento de usuarios*

### <a name="group-provisioning-and-de-provisioning"></a>Aprovisionamiento y cancelación del aprovisionamiento de grupos

El aprovisionamiento y desaprovisionamiento de grupos es opcional. Cuando se implementa y habilita, la siguiente ilustración muestra los mensajes que Azure AD envía a un servicio SCIM para administrar el ciclo de vida de un usuario en el almacén de identidades de la aplicación.  Esos mensajes se diferencian de los mensajes sobre los usuarios de dos maneras:

* Las solicitudes para recuperar grupos especifican que el atributo members se excluya de cualquier recurso proporcionado en respuesta a la solicitud.  
* Las solicitudes para determinar si un atributo de referencia tiene un valor determinado son solicitudes sobre el atributo members.  

![Muestra la secuencia de aprovisionamiento y desaprovisionamiento de grupos][5]<br/>
*Ilustración 5: Secuencia de aprovisionamiento y cancelación del aprovisionamiento de grupos*

### <a name="scim-protocol-requests-and-responses"></a>Modificación de solicitudes y respuestas de protocolo SCIM
Esta sección proporciona solicitudes SCIM de ejemplo emitidas por el cliente de SCIM de Azure AD y el ejemplo respuestas esperadas. Para obtener mejores resultados, debe codificar la aplicación para controlar estas solicitudes en este formato y emitir las respuestas esperadas.

> [!IMPORTANT]
> Para comprender cómo y cuándo el servicio de aprovisionamiento de usuario de Azure AD emite las operaciones que se describen a continuación, consulte [¿Qué ocurre durante el aprovisionamiento de usuarios?](user-provisioning.md#what-happens-during-provisioning).

- [Operaciones de usuario](#user-operations)
  - [Crear usuario](#create-user)
    - [Solicitud](#request)
    - [Respuesta](#response)
  - [Obtener usuario](#get-user)
    - [Solicitud](#request-1)
    - [Respuesta](#response-1)
  - [Obtener usuario por consulta](#get-user-by-query)
    - [Solicitud](#request-2)
    - [Respuesta](#response-2)
  - [Obtener usuario por consulta: cero resultados](#get-user-by-query---zero-results)
    - [Solicitud](#request-3)
    - [Respuesta](#response-3)
  - [Actualizar usuario [propiedades con varios valores]](#update-user-multi-valued-properties)
    - [Solicitud](#request-4)
    - [Respuesta](#response-4)
  - [Actualizar usuario [propiedades de un solo valor]](#update-user-single-valued-properties)
    - [Solicitud](#request-5)
    - [Respuesta](#response-5)
  - [Eliminar usuario](#delete-user)
    - [Solicitud](#request-6)
    - [Respuesta](#response-6)
- [Operaciones de grupo](#group-operations)
  - [Crear grupo](#create-group)
    - [Solicitud](#request-7)
    - [Respuesta](#response-7)
  - [Obtener grupo](#get-group)
    - [Solicitud](#request-8)
    - [Respuesta](#response-8)
  - [Obtener grupo por displayName](#get-group-by-displayname)
    - [Solicitud](#request-9)
    - [Respuesta](#response-9)
  - [Actualizar grupo [atributos no de miembro]](#update-group-non-member-attributes)
    - [Solicitud](#request-10)
    - [Respuesta](#response-10)
  - [Actualizar grupo [Agregar miembros]](#update-group-add-members)
    - [Solicitud](#request-11)
    - [Respuesta](#response-11)
  - [Actualizar grupo [Eliminar miembros]](#update-group-remove-members)
    - [Solicitud](#request-12)
    - [Respuesta](#response-12)
  - [Eliminar grupo](#delete-group)
    - [Solicitud](#request-13)
    - [Respuesta](#response-13)

### <a name="user-operations"></a>Operaciones de usuario

* Los usuarios pueden ser consultados por atributos `userName` o `email[type eq "work"]`.  

#### <a name="create-user"></a>Crear usuario

###### <a name="request"></a>Solicitud

*POST /Users*
```json
{
    "schemas": [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"],
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "active": true,
    "emails": [{
        "primary": true,
        "type": "work",
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com"
    }],
    "meta": {
        "resourceType": "User"
    },
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName"
    },
    "roles": []
}
```

##### <a name="response"></a>Response

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "48af03ac28ad4fb88478",
    "externalId": "0a21f0f2-8d2a-4f8e-bf98-7363c4aed4ef",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_ab6490ee-1e48-479e-a20b-2d77186b5dd1",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_fd0ea19b-0777-472c-9f96-4f70d2226f2e@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user"></a>Obtener usuario

###### <a name="request-1"></a>Solicitud
*GET /Users/5d48a0a8e9f04aa38008* 

###### <a name="response-1"></a>Respuesta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5d48a0a8e9f04aa38008",
    "externalId": "58342554-38d6-4ec8-948c-50044d0a33fd",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_feed3ace-693c-4e5a-82e2-694be1b39934",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_22370c1a-9012-42b2-bf64-86099c2a1c22@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="get-user-by-query"></a>Obtener usuario por consulta

##### <a name="request-2"></a>Solicitud

*GET /Users?filter=userName eq "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081"*

##### <a name="response-2"></a>Respuesta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
        "id": "2441309d85324e7793ae",
        "externalId": "7fce0092-d52e-4f76-b727-3955bd72c939",
        "meta": {
            "resourceType": "User",
            "created": "2018-03-27T19:59:26.000Z",
            "lastModified": "2018-03-27T19:59:26.000Z"
            
        },
        "userName": "Test_User_dfeef4c5-5681-4387-b016-bdf221e82081",
        "name": {
            "familyName": "familyName",
            "givenName": "givenName"
        },
        "active": true,
        "emails": [{
            "value": "Test_User_91b67701-697b-46de-b864-bd0bbe4f99c1@testuser.com",
            "type": "work",
            "primary": true
        }]
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="get-user-by-query---zero-results"></a>Obtener usuario por consulta: cero resultados

##### <a name="request-3"></a>Solicitud

*GET /Users?filter=userName eq "non-existent user"*

##### <a name="response-3"></a>Respuesta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 0,
    "Resources": [],
    "startIndex": 1,
    "itemsPerPage": 20
}

```

#### <a name="update-user-multi-valued-properties"></a>Actualizar usuario [propiedades con varios valores]

##### <a name="request-4"></a>Solicitud

*PATCH /Users/6764549bef60420686bc HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [
            {
            "op": "Replace",
            "path": "emails[type eq \"work\"].value",
            "value": "updatedEmail@microsoft.com"
            },
            {
            "op": "Replace",
            "path": "name.familyName",
            "value": "updatedFamilyName"
            }
    ]
}
```

##### <a name="response-4"></a>Respuesta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "6764549bef60420686bc",
    "externalId": "6c75de36-30fa-4d2d-a196-6bdcdb6b6539",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "userName": "Test_User_fbb9dda4-fcde-4f98-a68b-6c5599e17c27",
    "name": {
        "formatted": "givenName updatedFamilyName",
        "familyName": "updatedFamilyName",
        "givenName": "givenName"
    },
    "active": true,
    "emails": [{
        "value": "updatedEmail@microsoft.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="update-user-single-valued-properties"></a>Actualizar usuario [propiedades de un solo valor]

##### <a name="request-5"></a>Solicitud

*PATCH /Users/5171a35d82074e068ce2 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "userName",
        "value": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com"
    }]
}
```

##### <a name="response-5"></a>Respuesta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:User"],
    "id": "5171a35d82074e068ce2",
    "externalId": "aa1eca08-7179-4eeb-a0be-a519f7e5cd1a",
    "meta": {
        "resourceType": "User",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "userName": "5b50642d-79fc-4410-9e90-4c077cdd1a59@testuser.com",
    "name": {
        "formatted": "givenName familyName",
        "familyName": "familyName",
        "givenName": "givenName",
    },
    "active": true,
    "emails": [{
        "value": "Test_User_49dc1090-aada-4657-8434-4995c25a00f7@testuser.com",
        "type": "work",
        "primary": true
    }]
}
```

#### <a name="delete-user"></a>Eliminar usuario

##### <a name="request-6"></a>Solicitud

*DELETE /Users/5171a35d82074e068ce2 HTTP/1.1*

##### <a name="response-6"></a>Respuesta

*HTTP/1.1 204 No Content*

### <a name="group-operations"></a>Operaciones de grupo

* Los grupos deben crearse siempre con una lista de miembros vacía.
* Los grupos pueden ser consultados por el atributo `displayName`.
* La actualización de la solicitud de PATCH del grupo debe producir un *HTTP 204 No Content* en la respuesta. La devolución de un cuerpo con una lista de todos los miembros no es aconsejable.
* No es necesario admitir la devolución de todos los miembros del grupo.

#### <a name="create-group"></a>Crear grupo

##### <a name="request-7"></a>Solicitud

*POST /Groups HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group", "http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/2.0/Group"],
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "displayName": "displayName",
    "members": [],
    "meta": {
        "resourceType": "Group"
    }
}
```

##### <a name="response-7"></a>Respuesta

*HTTP/1.1 201 Created*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "927fa2c08dcb4a7fae9e",
    "externalId": "8aa1a0c0-c4c3-4bc0-b4a5-2ef676900159",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
        
    },
    "displayName": "displayName",
    "members": []
}
```

#### <a name="get-group"></a>Obtener grupo

##### <a name="request-8"></a>Solicitud

*GET /Groups/40734ae655284ad3abcc?excludedAttributes=members HTTP/1.1*

##### <a name="response-8"></a>Respuesta
*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
    "id": "40734ae655284ad3abcc",
    "externalId": "60f1bb27-2e1e-402d-bcc4-ec999564a194",
    "meta": {
        "resourceType": "Group",
        "created": "2018-03-27T19:59:26.000Z",
        "lastModified": "2018-03-27T19:59:26.000Z"
    },
    "displayName": "displayName",
}
```

#### <a name="get-group-by-displayname"></a>Obtener grupo por displayName

##### <a name="request-9"></a>Solicitud
*GET /Groups?excludedAttributes=members&filter=displayName eq "displayName" HTTP/1.1*

##### <a name="response-9"></a>Respuesta

*HTTP/1.1 200 OK*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:ListResponse"],
    "totalResults": 1,
    "Resources": [{
        "schemas": ["urn:ietf:params:scim:schemas:core:2.0:Group"],
        "id": "8c601452cc934a9ebef9",
        "externalId": "0db508eb-91e2-46e4-809c-30dcbda0c685",
        "meta": {
            "resourceType": "Group",
            "created": "2018-03-27T22:02:32.000Z",
            "lastModified": "2018-03-27T22:02:32.000Z",
            
        },
        "displayName": "displayName",
    }],
    "startIndex": 1,
    "itemsPerPage": 20
}
```

#### <a name="update-group-non-member-attributes"></a>Actualizar grupo [atributos no de miembro]

##### <a name="request-10"></a>Solicitud

*PATCH /Groups/fa2ce26709934589afc5 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Replace",
        "path": "displayName",
        "value": "1879db59-3bdf-4490-ad68-ab880a269474updatedDisplayName"
    }]
}
```

##### <a name="response-10"></a>Respuesta

*HTTP/1.1 204 No Content*

### <a name="update-group-add-members"></a>Actualizar grupo [Agregar miembros]

##### <a name="request-11"></a>Solicitud

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Add",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-11"></a>Respuesta

*HTTP/1.1 204 No Content*

#### <a name="update-group-remove-members"></a>Actualizar grupo [Eliminar miembros]

##### <a name="request-12"></a>Solicitud

*PATCH /Groups/a99962b9f99d4c4fac67 HTTP/1.1*
```json
{
    "schemas": ["urn:ietf:params:scim:api:messages:2.0:PatchOp"],
    "Operations": [{
        "op": "Remove",
        "path": "members",
        "value": [{
            "$ref": null,
            "value": "f648f8d5ea4e4cd38e9c"
        }]
    }]
}
```

##### <a name="response-12"></a>Respuesta

*HTTP/1.1 204 No Content*

#### <a name="delete-group"></a>Eliminar grupo

##### <a name="request-13"></a>Solicitud

*DELETE /Groups/cdb1ce18f65944079d37 HTTP/1.1*

##### <a name="response-13"></a>Respuesta

*HTTP/1.1 204 No Content*

## <a name="building-a-scim-endpoint-using-microsoft-cli-libraries"></a>Creación de punto de conexión SCIM con las bibliotecas CLI de Microsoft

Al crear un servicio web SCIM que interactúa con Azure Active Directory, puede habilitar el aprovisionamiento automático de usuarios para prácticamente cualquier aplicación o almacén de identidades.

Aquí le mostramos cómo funciona:

1. Azure AD proporciona una biblioteca de infraestructura de lenguaje común (CLI) llamada Microsoft.SystemForCrossDomainIdentityManagement, incluida con los ejemplos de código descritos a continuación. Los desarrolladores y los integradores de sistemas pueden usar esta biblioteca para crear e implementar un punto de conexión de servicio web basado en SCIM que pueda conectar Azure AD a cualquier almacén de identidades de aplicación.
2. Las asignaciones se implementan en el servicio web para asignar el esquema de usuario estándar al esquema de usuario y el protocolo requerido por la aplicación. 
3. La dirección URL del punto de conexión está registrada en Azure AD como parte de una aplicación personalizada en la galería de aplicaciones.
4. Se asignan usuarios y grupos a esta aplicación en Azure AD. Tras la asignación, se colocan en una cola para sincronizarse con la aplicación de destino. El proceso de sincronización que controla la cola se ejecuta cada 40 minutos.

### <a name="code-samples"></a>Ejemplos de código

Para facilitar este proceso, se proporcionan [ejemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) que crean un punto de conexión de servicio web SCIM y demuestran el aprovisionamiento automático. El ejemplo es de un proveedor que mantiene un archivo con filas de valores separados por comas que representan a usuarios y grupos.

**Requisitos previos**

* Visual Studio 2013 o posterior.
* [SDK de Azure para .NET](https://azure.microsoft.com/downloads/)
* Equipo de Windows que admite el ASP.NET framework 4.5 que se usará como punto de conexión SCIM. Esta máquina tiene que ser accesible desde la nube.
* [Una suscripción de Azure con una versión de prueba o con licencia de Azure AD Premium](https://azure.microsoft.com/services/active-directory/)

### <a name="getting-started"></a>Introducción

Es la manera más fácil de implementar un punto de conexión SCIM que puede aceptar las solicitudes de aprovisionamiento de Azure AD para compilar e implementar el ejemplo de código que genera los usuarios aprovisionados en un archivo de valores separados por comas (CSV).

#### <a name="to-create-a-sample-scim-endpoint"></a>Para crear un punto de conexión SCIM de ejemplo

1. Descargue el paquete de ejemplo de código en [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
1. Descomprima el paquete y colóquelo en un equipo con Windows, en una ubicación como C:\AzureAD-BYOA-Provisioning-Samples\.
1. En esta carpeta, inicie el proyecto FileProvisioning\Host\FileProvisioningService.csproj en Visual Studio.
1. Seleccione **Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes** y ejecute los siguientes comandos para que el proyecto FileProvisioningService resuelva las referencias de la solución:

   ```powershell
    Update-Package -Reinstall
   ```

1. Cree el proyecto FileProvisioningService.
1. Inicie la aplicación Símbolo del sistema de Windows (como administrador) y use el comando **cd** para cambiar el directorio a la carpeta **\AzureAD-BYOA-Provisioning-Samples\FileProvisioning\Host\bin\Debug**.
1. Ejecute el comando siguiente y reemplace `<ip-address>` por la dirección IP o el nombre de dominio de la máquina Windows:

   ```
    FileSvc.exe http://<ip-address>:9000 TargetFile.csv
   ```

1. En Windows, en **Configuración de Windows** > **Configuración de Internet y red**, seleccione **Firewall de Windows** >  **Configuración avanzada** y cree una **regla de entrada** que permita el acceso de entrada al puerto 9000.
1. Si la máquina Windows está detrás de un enrutador, este tiene que configurarse para ejecutar la traducción de direcciones de red entre su puerto 9000, que está expuesto a Internet, y el puerto 9000 de la máquina Windows. Esta configuración es necesaria para que Azure AD tenga acceso a este punto de conexión en la nube.

#### <a name="to-register-the-sample-scim-endpoint-in-azure-ad"></a>Para registrar el punto de conexión SCIM de ejemplo en Azure AD

1. Inicie sesión en el [portal de Azure Active Directory](https://aad.portal.azure.com). 
1. En el panel izquierdo, seleccione **Aplicaciones empresariales**. Se muestra una lista de las aplicaciones configuradas, incluidas aquellas que se han agregado desde la galería.
1. Seleccione **+ Nueva aplicación** > **Todas** > **Aplicación situada fuera de la galería**.
1. Escriba un nombre para la aplicación y seleccione **Agregar** para crear un objeto de aplicación. El objeto de la aplicación creado está diseñado para representar la aplicación de destino a la que va a aprovisionar y para la que va a implementar el inicio de sesión único, no solo el punto de conexión SCIM.
1. En la pantalla de administración de la aplicación, seleccione **Aprovisionamiento** en el panel izquierdo.
1. En el menú **Modo de aprovisionamiento**, seleccione **Automático**.    
1. En el campo **Dirección URL del inquilino**, escriba la dirección URL del punto de conexión SCIM de la aplicación. Ejemplo: https://api.contoso.com/scim/

1. Si el punto de conexión SCIM requiere un token de portador OAuth de un emisor que no sea Azure AD, copie el token de portador OAuth necesario en el campo **Token secreto**. Si este campo se deja en blanco, Azure AD incluye un token de portador OAuth emitido desde Azure AD con cada solicitud. Las aplicaciones que usan Azure AD como un proveedor de identidades pueden validar este token que emitió Azure AD.
1. Seleccione **Probar conexión** para que Azure Active Directory intente conectarse al punto de conexión SCIM. Si se produce un error en el intento, se muestra la información de error.  

    > [!NOTE]
    > La **prueba de conexión** consulta el punto de conexión SCIM de un usuario que no existe mediante un GUID aleatorio, como la propiedad de coincidencia seleccionada en la configuración de Azure AD. La respuesta correcta esperada es HTTP 200 OK con un mensaje ListResponse de SCIM vacío.

1. Si la conexión se realiza con éxito, a continuación, seleccione **Guardar** para guardar las credenciales de administrador.
1. En la sección **Asignaciones**, hay dos conjuntos seleccionables de asignaciones de atributos: uno para los objetos de usuario y otro para los objetos de grupo. Seleccione cada uno de ellos para revisar los atributos que se sincronizan desde Azure Active Directory a la aplicación. Los atributos seleccionados como propiedades de **Coincidencia** se usan para buscar coincidencias con los usuarios y grupos de la aplicación con el objetivo de realizar operaciones de actualización. Para confirmar los cambios, seleccione **Guardar**.
1. En **Configuración**, el campo **Ámbito** define qué usuarios y grupos se sincronizan. Seleccione **Sincronizar solo los usuarios y grupos asignados** (recomendado) para que se sincronicen solamente los usuarios y los grupos asignados en la pestaña **Usuarios y grupos**.
1. Una vez completada la configuración, cambie el **Estado de aprovisionamiento** a **Activado**.
1. Seleccione **Guardar** para iniciar el servicio de aprovisionamiento de Azure AD.
1. Al sincronizar solo los usuarios y grupos asignados (recomendado), no olvide seleccionar la pestaña **Usuarios y grupos** y asignar los usuarios o grupos que quiere sincronizar.

Una vez que haya iniciado la sincronización inicial, puede seleccionar **Registros de auditoría** en el panel izquierdo para supervisar el progreso, con esto se muestran todas las acciones realizadas por el servicio de aprovisionamiento en la aplicación. Para más información sobre cómo leer los registros de aprovisionamiento de Azure AD, consulte el tutorial de [Creación de informes sobre el aprovisionamiento automático de cuentas de usuario](check-status-user-account-provisioning.md).

El último paso en la comprobación del ejemplo es abrir el archivo TargetFile.csv en la carpeta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug en el equipo Windows. Una vez que se ejecuta el proceso de aprovisionamiento, este archivo muestra los detalles de todos los usuarios y grupos asignados y aprovisionados.

### <a name="development-libraries"></a>Bibliotecas de desarrollo

Para desarrollar su propio servicio web que cumpla la especificación SCIM, familiarícese primero con las siguientes bibliotecas proporcionadas por Microsoft para ayudar a acelerar el proceso de desarrollo:

* Se ofrecen bibliotecas de Common Language Infrastructure (CLI) que se pueden usar con lenguajes basados en dicha infraestructura, como C#. Una de esas bibliotecas, Microsoft.SystemForCrossDomainIdentityManagement.Service, declara una interfaz Microsoft.SystemForCrossDomainIdentityManagement.IProvider, que se muestra en la ilustración a continuación. Un desarrollador que usa las bibliotecas debería implementar esa interfaz con una clase a la que se puede hacer referencia, de forma genérica, como un proveedor. Las bibliotecas permiten al desarrollador implementar un servicio web que se ajusta a la especificación SCIM. El servicio web se puede hospedar en Internet Information Services o en cualquier ensamblado ejecutable de Common Language Infrastructure. La solicitud se traduce en llamadas a los métodos del proveedor, que pueden ser programadas por el desarrollador para operar en un almacén de identidades.
  
   ![Desglose: Una solicitud traducida en llamadas a los métodos del proveedor][3]
  
* Los [controladores de ruta Express](https://expressjs.com/guide/routing.html) están disponibles para analizar los objetos de solicitud de node.js que representan llamadas (tal y como se define en la especificación SCIM), realizadas a un servicio web de node.js.

### <a name="building-a-custom-scim-endpoint"></a>Creación de un punto de conexión SCIM personalizado

Los desarrolladores que usan las bibliotecas CLI pueden hospedar sus servicios en cualquier ensamblado ejecutable de Common Language Infrastructure o en Internet Information Services. Aquí se incluye un ejemplo de código para hospedar un servicio dentro de un ensamblado ejecutable en la dirección http://localhost:9000: 

   ```csharp
    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }
   ```

Este servicio debe tener una dirección HTTP y un certificado de autenticación de servidor para el que la entidad de certificación raíz tenga uno de los siguientes nombres: 

* CNNIC
* Comodo
* CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Un certificado de autenticación de servidor puede enlazarse a un puerto en un host de Windows mediante la utilidad de shell de red siguiente: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

En este caso, el valor proporcionado para el argumento certhash es la huella digital del certificado, mientras que el valor proporcionado para el argumento appid es un identificador único global arbitrario.  

Para hospedar el servicio en Internet Information Services, un desarrollador debería crear un ensamblado de biblioteca de código CLI con una clase denominada Startup en el espacio de nombres predeterminado del ensamblado.  Este es un ejemplo de este tipo de clase: 

   ```csharp
    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }
   ```

### <a name="handling-endpoint-authentication"></a>Control de la autenticación de puntos de conexión

Las solicitudes de Azure Active Directory incluyen un token de portador de OAuth 2.0.   Cualquier servicio que reciba la solicitud debe autenticar al emisor como Azure Active Directory para el inquilino Azure Active Directory esperado, para el acceso al servicio web Graph de Azure Active Directory.  En el token, el emisor se identifica mediante una notificación de iss; por ejemplo, "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  En este ejemplo, la dirección base del valor de notificación, https://sts.windows.net, identifica a Azure Active Directory como el emisor, mientras que el segmento de la dirección relativa, cbb1a5ac f33b 45fa 9bf5 f37db0fed422, es un identificador único del inquilino de Azure Active Directory para el que se emitió el token. La audiencia del token será el identificador de la plantilla de aplicación de la aplicación en la galería. El identificador de la plantilla de aplicación para todas las aplicaciones personalizadas es 8adf8e6e-67b2-4cf2-a259-e3dc5476c621. El identificador de la plantilla de aplicación de cada aplicación de la galería varía. Póngase en contacto con ProvisioningFeedback@microsoft.com para cuestiones relativas al identificador de la plantilla de aplicación de una aplicación de la galería. Cada una de las aplicaciones registradas en un solo inquilino puede recibir la misma notificación `iss` con las solicitudes SCIM.

Los desarrolladores que usan las bibliotecas de CLI proporcionadas por Microsoft para crear un servicio SCIM pueden autenticar las solicitudes de Azure Active Directory mediante el paquete Microsoft.Owin.Security.ActiveDirectory siguiendo estos pasos: 

1. En un proveedor, implemente la propiedad Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior; para ello, haga que devuelva un método al que llamar cuando se inicia el servicio: 

   ```csharp
     public override Action<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration> StartupBehavior
     {
       get
       {
         return this.OnServiceStartup;
       }
     }

     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
       System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
     {
     }
   ```

1. Agregue el siguiente código a dicho método para que cualquier solicitud a cualquiera de los puntos de conexión del servicio se autentique como si tuviera un token emitido por Azure Active Directory para un inquilino especificado, para acceder al servicio web Graph de Azure Active Directory: 

   ```csharp
     private void OnServiceStartup(
       Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
       System.Web.Http.HttpConfiguration HttpConfiguration configuration)
     {
       // IFilter is defined in System.Web.Http.dll.  
       System.Web.Http.Filters.IFilter authorizationFilter = 
         new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

       // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
       // System.IdentityModel.Token.Jwt.dll.
       SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
         new TokenValidationParameters()
         {
           ValidAudience = "8adf8e6e-67b2-4cf2-a259-e3dc5476c621"
         };

       // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
       // Microsoft.Owin.Security.ActiveDirectory.dll
       Microsoft.Owin.Security.ActiveDirectory.
       WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
         new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
         TokenValidationParameters = tokenValidationParameters,
         Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                       // identifier for this one.  
       };

       applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
     }
   ```

### <a name="handling-provisioning-and-deprovisioning-of-users"></a>Control del aprovisionamiento y desaprovisionamiento de usuarios

1. Azure Active Directory consulta el servicio para un usuario con un valor de atributo externalId que coincida con el valor de atributo mailNickname de un usuario de Azure AD. La consulta se expresa como una solicitud de Protocolo de transferencia de hipertexto (HTTP) como la de este ejemplo, donde jyoung es un ejemplo de un mailNickname de un usuario en Azure Active Directory.

    >[!NOTE]
    > Esto es solo un ejemplo. No todos los usuarios tendrán un atributo mailNickname, y el valor que tiene un usuario no puede ser único en el directorio. Además, el atributo utilizado para la coincidencia (que en este caso es externalId) es configurable en las [asignaciones de atributos de Azure AD](customize-application-attributes.md).

   ```
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
   ```
   Si el servicio se creó mediante las bibliotecas de CLI proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Query del proveedor del servicio.  Esta es la firma de ese método: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
   ```
   Esta es la definición de la interfaz de Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 
   ```csharp
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }
   ```

   ```
     GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
     Authorization: Bearer ...
   ```

   Si el servicio se creó mediante las bibliotecas de Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Query del proveedor del servicio.  Esta es la firma de ese método: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
     // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  
 
     System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]>  Query(
       Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
       string correlationIdentifier);
   ```

   Esta es la definición de la interfaz de Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters: 

   ```csharp
     public interface IQueryParameters: 
       Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
         System.Collections.Generic.IReadOnlyCollection  <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
         { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
     {
       system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
       { get; }
       System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
       { get; }
       string SchemaIdentifier 
       { get; }
     }

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
     {
         Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
           { get; set; }
         string AttributePath 
           { get; } 
         Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
           { get; }
         string ComparisonValue 
           { get; }
     }

     public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
     {
         Equals
     }
   ```

   En el ejemplo anterior de una consulta para un usuario con un valor especificado para el atributo externalId, los valores de los argumentos pasados al método Query serán los siguientes: 
   * parameters.AlternateFilters.Count: 1
   * parameters.AlternateFilters.ElementAt(0).AttributePath: "externalId"
   * parameters.AlternateFilters.ElementAt(0).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(0).ComparisonValue: "jyoung"
   * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin.RequestId"] 

1. Si la respuesta a una consulta al servicio web relativa a un usuario con un valor de atributo externalId que coincide con el valor de atributo mailNickname de un usuario, no devuelve ningún usuario, Azure Active Directory solicita al servicio que aprovisione un usuario correspondiente al de Azure Active Directory.  Este es un ejemplo de dicha solicitud: 

   ```
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
   ```
   Las bibliotecas de CLI proporcionadas por Microsoft para implementar servicios SCIM traducirían esa solicitud en una llamada al método Create del proveedor del servicio.  El método Create tiene esta firma: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
   ```
   En el caso de una solicitud de aprovisionamiento de un usuario, el valor del argumento del recurso es una instancia de Microsoft.SystemForCrossDomainIdentityManagement. Clase Core2EnterpriseUser, definida en la biblioteca Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Si la solicitud para aprovisionar el usuario se realiza correctamente, la implementación del método debería devolver una instancia de Microsoft.SystemForCrossDomainIdentityManagement. Clase Core2EnterpriseUser, con el valor de la propiedad Identifier establecido en el identificador único del usuario recién aprovisionado.  

1. Para actualizar un usuario que se sabe que existe en un almacén de identidades dirigido por un SCIM, Azure Active Directory solicita el estado actual de dicho usuario desde el servicio con una solicitud como esta: 
   ```
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ```
   En un servicio creado mediante las bibliotecas de CLI proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Retrieve del proveedor del servicio.  Esta es la firma del método Retrieve: 
   ```csharp
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
   ```
   En el ejemplo anterior de una solicitud para recuperar el estado actual de un usuario, los valores de las propiedades del objeto proporcionados como el valor del argumento parameters son los siguientes: 
  
   * Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

1. Si se va a actualizar un atributo de referencia, Azure Active Directory consulta el servicio para determinar si el valor actual del atributo de referencia en el almacén de identidades dirigido por el servicio, ya coincide con el valor de dicho atributo en Azure Active Directory. Para los usuarios, el único atributo del que se va a consultar el valor actual de esta manera es el atributo manager. Este es un ejemplo de una solicitud para determinar si el atributo de administrador de un determinado objeto de usuario tiene actualmente un determinado valor: 

   Si el servicio se creó mediante las bibliotecas de CLI proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Query del proveedor del servicio. El valor de las propiedades del objeto proporcionado como el valor del argumento parameters es el siguiente: 
  
   * parameters.AlternateFilters.Count: 2
   * parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
   * parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(x).ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
   * parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
   * parameters.AlternateFilter.ElementAt(y).ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
   * parameters.RequestedAttributePaths.ElementAt(0): "ID"
   * parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

   En este caso, el valor del índice x puede ser 0 y el valor del índice y puede ser 1, o bien el valor de x puede ser 1 y el valor de y puede ser 0, en función del orden de las expresiones del parámetro de consulta filter.   

1. A continuación se proporciona un ejemplo de una solicitud de Azure Active Directory a un servicio SCIM para actualizar un usuario: 
   ```
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/scim+json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```
   Las bibliotecas de CLI para implementar servicios SCIM traducirían la solicitud en una llamada al método Update del proveedor del servicio. Esta es la signatura del método Update: 
   ```csharp
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}
   ```

   Si el servicio se creó mediante las bibliotecas de Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Query del proveedor del servicio. El valor de las propiedades del objeto proporcionado como el valor del argumento parameters es el siguiente: 
  
* parameters.AlternateFilters.Count: 2
* parameters.AlternateFilters.ElementAt(x).AttributePath: "ID"
* parameters.AlternateFilters.ElementAt(x).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(x).ComparisonValue:  "54D382A4-2050-4C03-94D1-E769F1D15682"
* parameters.AlternateFilters.ElementAt(y).AttributePath: "manager"
* parameters.AlternateFilters.ElementAt(y).ComparisonOperator: ComparisonOperator.Equals
* parameters.AlternateFilter.ElementAt(y).ComparisonValue:  "2819c223-7f76-453a-919d-413861904646"
* parameters.RequestedAttributePaths.ElementAt(0): "ID"
* parameters.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

  En este caso, el valor del índice x puede ser 0 y el valor del índice y puede ser 1, o bien el valor de x puede ser 1 y el valor de y puede ser 0, en función del orden de las expresiones del parámetro de consulta filter.   

1. A continuación se proporciona un ejemplo de una solicitud de Azure Active Directory a un servicio SCIM para actualizar un usuario: 

   ```
     PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
     Content-type: application/scim+json
     {
       "schemas": 
       [
         "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
       "Operations":
       [
         {
           "op":"Add",
           "path":"manager",
           "value":
             [
               {
                 "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                 "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
   ```

   Las bibliotecas de Microsoft Common Language Infrastructure para implementar servicios SCIM traducirían la solicitud de una llamada al método Update del proveedor del servicio. Esta es la signatura del método Update: 

   ```csharp
     // System.Threading.Tasks.Tasks and 
     // System.Collections.Generic.IReadOnlyCollection<T>
     // are defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
     // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
     // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
     // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

     System.Threading.Tasks.Task Update(
       Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
       string correlationIdentifier);

     public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
     {
     Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
       PatchRequest 
         { get; set; }
     Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
       ResourceIdentifier 
         { get; set; }        
     }

     public class PatchRequest2: 
       Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
     {
     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
         Operations
         { get;}

     public void AddOperation(
       Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
     }

     public class PatchOperation
     {
     public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
       Name
       { get; set; }

     public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
       Path
       { get; set; }

     public System.Collections.Generic.IReadOnlyCollection
       <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
       { get; }

     public void AddValue(
       Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
     }

     public enum OperationName
     {
       Add,
       Remove,
       Replace
     }

     public interface IPath
     {
       string AttributePath { get; }
       System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
       Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
     }

     public class OperationValue
     {
       public string Reference
       { get; set; }

       public string Value
       { get; set; }
     }
   ```

    En el ejemplo de una solicitud para actualizar un usuario, el objeto proporcionado como el valor del argumento patch tiene estos valores de propiedad: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier:  "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"
   * (PatchRequest as PatchRequest2).Operations.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).OperationName: OperationName.Add
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Path.AttributePath: "manager"
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.Count: 1
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Reference: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
   * (PatchRequest as PatchRequest2).Operations.ElementAt(0).Value.ElementAt(0).Value: 2819c223-7f76-453a-919d-413861904646

1. Para cancelar el aprovisionamiento de un usuario de un almacén de identidades dirigido por un servicio SCIM, Azure AD envía una solicitud como esta: 

   ```
     DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
     Authorization: Bearer ...
   ```

   Si el servicio se creó mediante las bibliotecas Common Language Infrastructure proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce a una llamada al método Delete del proveedor del servicio.   Este método tiene esta firma: 

   ```csharp
     // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
     // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
     // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
     System.Threading.Tasks.Task Delete(
       Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
         resourceIdentifier, 
       string correlationIdentifier);
   ```

   El objeto proporcionado como el valor del argumento resourceIdentifier tiene tendrá estos valores de propiedad en el ejemplo anterior de una solicitud de cancelación del aprovisionamiento de un usuario: 

1. Para cancelar el aprovisionamiento de un usuario de un almacén de identidades dirigido por un servicio SCIM, Azure AD envía una solicitud como esta: 
   ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
   ````
   Si el servicio se creó mediante las bibliotecas de CLI proporcionadas por Microsoft para implementar servicios SCIM, la solicitud se traduce en una llamada al método Delete del proveedor del servicio.   Este método tiene esta firma: 
   ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
   ````
   El objeto proporcionado como el valor del argumento resourceIdentifier tiene tendrá estos valores de propiedad en el ejemplo anterior de una solicitud de cancelación del aprovisionamiento de un usuario: 
  
   * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
   * ResourceIdentifier.SchemaIdentifier: "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="user-and-group-schema-reference"></a>Referencia de esquema de grupo y usuario

Azure Active Directory puede aprovisionar dos tipos de recursos a los servicios web SCIM.  Esos tipos de recursos son los usuarios y grupos.  

Los recursos de usuario se identifican mediante el identificador de esquema `urn:ietf:params:scim:schemas:extension:enterprise:2.0:User`, que se incluye en esta especificación del protocolo: https://tools.ietf.org/html/rfc7643.  La asignación predeterminada entre los atributos de los grupos de Azure Active Directory y los atributos de los recursos se proporciona en la Tabla 1.  

Los recursos del grupo se identifican mediante el identificado de esquema `urn:ietf:params:scim:schemas:core:2.0:Group`. En la Tabla 2, se muestra la asignación predeterminada entre los atributos de los grupos de Azure Active Directory y los atributos de los recursos.  

### <a name="table-1-default-user-attribute-mapping"></a>Tabla 1: Asignación de atributos de usuario predeterminada

| Usuario de Azure Active Directory | "urn:ietf:params:scim:schemas:extension:enterprise:2.0:User" |
| --- | --- |
| IsSoftDeleted |active |
| DisplayName |DisplayName |
| Facsimile-TelephoneNumber |phoneNumbers[type eq "fax"].value |
| givenName |name.givenName |
| jobTitle |título |
| mail |emails[type eq "work"].value |
| mailNickname |externalId |
| manager |manager |
| mobile |phoneNumbers[type eq "mobile"].value |
| objectId |id |
| postalCode |addresses[type eq "work"].postalCode |
| proxy-Addresses |emails[type eq "other"].Value |
| physical-Delivery-OfficeName |addresses[type eq "other"].Formatted |
| streetAddress |addresses[type eq "work"].streetAddress |
| surname |name.familyName |
| telephone-Number |phoneNumbers[type eq "work"].value |
| user-PrincipalName |userName |

### <a name="table-2-default-group-attribute-mapping"></a>Tabla 2: Asignación de atributos de grupo predeterminada

| Grupo de Azure Active Directory | urn:ietf:params:scim:schemas:core:2.0:Group |
| --- | --- |
| DisplayName |externalId |
| mail |emails[type eq "work"].value |
| mailNickname |DisplayName |
| members |members |
| objectId |id |
| proxyAddresses |emails[type eq "other"].Value |

## <a name="allow-ip-addresses-used-by-the-azure-ad-provisioning-service-to-make-scim-requests"></a>Permiso para que las direcciones IP usadas por el servicio de aprovisionamiento de Azure AD realicen solicitudes SCIM

Determinadas aplicaciones permiten el tráfico de entrada a su aplicación. Para que el servicio de aprovisionamiento de Azure AD funcione según lo previsto, se tienen que permitir las direcciones IP usadas. Para obtener una lista de direcciones IP para cada etiqueta o región de servicio, consulte el archivo JSON [Rangos de direcciones IP y etiquetas de servicio de Azure: nube pública](https://www.microsoft.com/download/details.aspx?id=56519). Puede descargar y programa estas direcciones IP en el firewall según sea necesario. Los intervalos IP reservados para el aprovisionamiento de Azure AD se pueden encontrar en "AzureActiveDirectoryDomainServices".

## <a name="related-articles"></a>Artículos relacionados

* [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](user-provisioning.md)
* [Personalización de asignaciones de atributos para el aprovisionamiento de usuarios](customize-application-attributes.md)
* [Escritura de expresiones para asignaciones de atributos](functions-for-customizing-application-data.md)
* [Filtros de ámbito para el aprovisionamiento de usuario](define-conditional-rules-for-provisioning-user-accounts.md)
* [Notificaciones de aprovisionamiento de cuentas](user-provisioning.md)
* [Lista de tutoriales sobre cómo integrar aplicaciones SaaS](../saas-apps/tutorial-list.md)

<!--Image references-->
[0]: ./media/use-scim-to-provision-users-and-groups/scim-figure-1.png
[1]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2a.png
[2]: ./media/use-scim-to-provision-users-and-groups/scim-figure-2b.png
[3]: ./media/use-scim-to-provision-users-and-groups/scim-figure-3.png
[4]: ./media/use-scim-to-provision-users-and-groups/scim-figure-4.png
[5]: ./media/use-scim-to-provision-users-and-groups/scim-figure-5.png
