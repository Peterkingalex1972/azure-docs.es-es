---
title: Uso de la Plataforma de identidad de Microsoft para el inicio de sesión de usuarios en dispositivos sin explorador | Azure
description: Cree flujos de autenticación sin explorador e insertados mediante el uso de la concesión de código de dispositivo.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 274c4e89ff3f996cc71cdacdfb7b5b72e813ae4b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297654"
---
# <a name="microsoft-identity-platform-and-the-oauth-20-device-code-flow"></a>Flujo de código de dispositivo de OAuth 2.0 y la Plataforma de identidad de Microsoft

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

La Plataforma de identidad de Microsoft admite la [concesión de código de dispositivo](https://tools.ietf.org/html/draft-ietf-oauth-device-flow-12), lo que permite que los usuarios inicien sesión en dispositivos con limitaciones de entrada, como un televisor inteligente, un dispositivo IoT o una impresora.  Para habilitar este flujo, el dispositivo pide que el usuario visite una página web en su explorador en otro dispositivo para iniciar sesión.  Una vez que el usuario inicia sesión, el dispositivo es capaz de obtener tokens de acceso y tokens de actualización según sea necesario.  

> [!IMPORTANT]
> En este momento, el punto de conexión de la Plataforma de identidad de Microsoft solo es compatible con el flujo de dispositivo para los inquilinos de Azure AD, pero no para las cuentas personales.  Esto significa que debe usar un punto de conexión configurado como inquilino, o bien el punto de conexión `organizations`.  Esta compatibilidad se habilitará pronto. 
>
> Las cuentas personales invitadas a un inquilino de Azure AD podrán usar la concesión de flujo de dispositivo, pero solo en el contexto del inquilino.
>
> Como nota adicional, el campo de respuesta `verification_uri_complete` no se incluye ni se admite en este momento.  

> [!NOTE]
> No todas las características y escenarios de Azure Active Directory son compatibles con el punto de conexión de la Plataforma de identidad de Microsoft. Para determinar si debe usar el punto de conexión de la Plataforma de identidad de Microsoft, conozca las [limitaciones de esta plataforma](active-directory-v2-limitations.md).

## <a name="protocol-diagram"></a>Diagrama de protocolo

El flujo de código de dispositivo completo tiene un aspecto similar al diagrama siguiente. Más adelante en este artículo se describe cada uno de los pasos.

![Flujo de código de dispositivo](./media/v2-oauth2-device-code/v2-oauth-device-flow.svg)

## <a name="device-authorization-request"></a>Solicitud de autorización de dispositivo

El cliente debe primero realizar una comprobación con el servidor de autenticación para obtener un código de usuario y dispositivo que se usa para iniciar la autenticación. El cliente recopila esta solicitud desde el punto de conexión `/devicecode`. En esta solicitud, el cliente también debe incluir los permisos que necesita obtener por parte del usuario. Desde el momento en que se envía esta solicitud, el usuario tiene solo 15 minutos para iniciar sesión (el valor habitual de `expires_in`), por lo que solo se debe realizar esta solicitud cuando el usuario ha indicado que está listo para iniciar sesión.

> [!TIP]
> Pruebe a ejecutar esta solicitud en Postman
> [![Pruebe a ejecutar esta solicitud en Postman](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)

```
// Line breaks are for legibility only.

POST https://login.microsoftonline.com/{tenant}/oauth2/v2.0/devicecode
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
scope=user.read%20openid%20profile

```

| Parámetro | Condición | DESCRIPCIÓN |
| --- | --- | --- |
| `tenant` | Obligatorio |El inquilino de directorio al que quiere solicitar permiso. Puede estar en formato de nombre descriptivo o GUID.  |
| `client_id` | Obligatorio | El **identificador de aplicación (cliente)** que la experiencia [Azure Portal: Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) asignó a la aplicación. |
| `scope` | Recomendado | Una lista separada por espacios de [ámbitos](v2-permissions-and-consent.md) que desea que el usuario consienta.  |

### <a name="device-authorization-response"></a>Respuesta de autorización de dispositivo

Una respuesta correcta será un objeto JSON que contiene la información necesaria para permitir que el usuario inicie sesión.  

| Parámetro | Formato | DESCRIPCIÓN |
| ---              | --- | --- |
|`device_code`     | Cadena | Cadena larga que se usa para comprobar la sesión entre el cliente y el servidor de autorización. El cliente usa este parámetro para solicitar el token de acceso al servidor de autorización. |
|`user_code`       | Cadena | Cadena corta que se muestra al usuario y se usa para identificar la sesión en un dispositivo secundario.|
|`verification_uri`| URI | Identificador URI al que debe ir el usuario con el `user_code` para iniciar sesión. |
|`expires_in`      | int | Número de segundos antes de que `device_code` y `user_code` expiren. |
|`interval`        | int | Número de segundos que el cliente debe esperar entre solicitudes de sondeo. |
| `message`        | Cadena | Cadena legible con instrucciones para el usuario. Esto se puede traducir mediante la inclusión de un **parámetro de consulta** en la solicitud del formulario `?mkt=xx-XX`, con el código de referencia cultural del idioma correspondiente. |

## <a name="authenticating-the-user"></a>Autenticación del usuario

Después de recibir `user_code` y `verification_uri`, el cliente los muestra al usuario y le pide que inicie sesión con su teléfono móvil o explorador de PC.  Además, el cliente puede usar un código QR u otro mecanismo similar para mostrar el valor de `verfication_uri_complete`, que le lleva al paso de introducir el valor de `user_code` del usuario.

Mientras el usuario se autentica en `verification_uri`, el cliente debe sondear el punto de conexión `/token` para obtener el token solicitado mediante el uso de `device_code`.

``` 
POST https://login.microsoftonline.com/tenant/oauth2/v2.0/token
Content-Type: application/x-www-form-urlencoded

grant_type: urn:ietf:params:oauth:grant-type:device_code
client_id: 6731de76-14a6-49ae-97bc-6eba6914391e
device_code: GMMhmHCXhWEzkobqIHGG_EnNYYsAkukHspeYUk9E8
```

| Parámetro | Obligatorio | DESCRIPCIÓN|
| -------- | -------- | ---------- |
| `grant_type` | Obligatorio | Debe ser `urn:ietf:params:oauth:grant-type:device_code`|
| `client_id`  | Obligatorio | Debe coincidir con el valor de `client_id` utilizado en la solicitud inicial. |
| `device_code`| Obligatorio | Valor de `device_code` devuelto en la solicitud de autorización de dispositivo.  |

### <a name="expected-errors"></a>Errores esperados

El flujo de código de dispositivo es un protocolo de sondeo, por lo que el cliente debe esperar recibir errores antes de que el usuario haya terminado la autenticación.  

| Error | DESCRIPCIÓN | Acción del cliente |
| ------ | ----------- | -------------|
| `authorization_pending` | El usuario no ha finalizado la autenticación, pero canceló el flujo. | Repetir la solicitud después de, por lo menos, los segundos especificados en `interval`. |
| `authorization_declined` | El usuario final ha denegado la solicitud de autorización.| Detener el sondeo y revertir a un estado de no autenticado.  |
| `bad_verification_code`| No se reconoció el valor de `device_code` que se envió al punto de conexión `/token`. | Comprobar que el cliente está enviando el valor correcto de `device_code` en la solicitud. |
| `expired_token` | Han transcurrido al menos `expires_in` segundos y la autenticación ya no es posible con este `device_code`. | Detener el sondeo y revertir a un estado de no autenticado. |

### <a name="successful-authentication-response"></a>Respuesta de autenticación correcta

Una respuesta de token correcta tendrá un aspecto similar al siguiente:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Parámetro | Formato | DESCRIPCIÓN |
| --------- | ------ | ----------- |
| `token_type` | Cadena| Siempre "Bearer". |
| `scope` | Cadenas separadas por espacios | Si se devolvió un token de acceso, esto muestra los ámbitos para los que es válido el token de acceso. |
| `expires_in`| int | Número de segundos antes de los que el token de acceso incluido es válido. |
| `access_token`| Cadena opaca | Se emite para los [ámbitos](v2-permissions-and-consent.md) solicitados.  |
| `id_token`   | JWT | Se emite si el parámetro `scope` original incluye el ámbito `openid`.  |
| `refresh_token` | Cadena opaca | Se emite si el parámetro `scope` original incluye `offline_access`.  |

Puede usar el token de actualización para adquirir nuevos tokens de acceso y tokens de actualización con el mismo flujo que se indica en la [documentación del flujo de código de OAuth](v2-oauth2-auth-code-flow.md#refresh-the-access-token).  
