---
title: Definición de un perfil técnico de OAuth2 en una directiva personalizada en Azure Active Directory B2C | Microsoft Docs
description: Defina un perfil técnico de OAuth2 en una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 80b196b34e8eee99ed77c3c8a914f89fa68d87b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66512947"
---
# <a name="define-an-oauth2-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico de OAuth2 en una directiva personalizada en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C proporciona compatibilidad con el proveedor de identidades de protocolo OAuth2. OAuth2 es el protocolo principal para la autorización y la autenticación delegada. Para obtener más información, consulte el artículo [RFC 6749 The OAuth 2.0 Authorization Framework](https://tools.ietf.org/html/rfc6749) (RFC 6749 Plataforma de autorización de OAuth 2.0). Con un perfil técnico de OAuth2, puede realizar la federación con un proveedor de identidades basado en OAuth2, como Facebook. Esta federación permite a los usuarios iniciar sesión con sus identidades de redes sociales o de empresa existentes.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `OAuth2`. Por ejemplo, el protocolo para el perfil técnico **Facebook-OAUTH** es `OAuth2`:

```XML
<TechnicalProfile Id="Facebook-OAUTH">
  <DisplayName>Facebook</DisplayName>
  <Protocol Name="OAuth2" />
  ...    
```

## <a name="input-claims"></a>Notificaciones de entrada

Los elementos **InputClaims** y **InputClaimsTransformations** no son necesarios. Pero puede que quiera enviar otros parámetros al proveedor de identidades. En el ejemplo siguiente se agrega el parámetro de cadena de consulta **domain_hint** con el valor de `contoso.com` a la solicitud de autorización.

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="domain_hint" DefaultValue="contoso.com" />
</InputClaims>
```

## <a name="output-claims"></a>Notificaciones de salida

El elemento **OutputClaims** contiene una lista de notificaciones devueltas por el proveedor de identidades de OAuth2. Puede que tenga que asignar el nombre de la notificación definida en la directiva al nombre definido en el proveedor de identidades. También puede incluir notificaciones no especificadas por el proveedor de identidades, siempre que establezca el atributo `DefaultValue`.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o para generar nuevas.

El ejemplo siguiente muestra las notificaciones devueltas por el proveedor de identidades de Facebook:

- La notificación **first_name** se asigna a la notificación **givenName**.
- La notificación **last_name** se asigna a la notificación **surname**.
- La notificación **displayName** sin asignación de nombre.
- La notificación **email** sin asignación de nombre.

El perfil técnico también devuelve notificaciones, que no son devueltas por el proveedor de identidades: 

- La notificación **identityProvider** que contiene el nombre del proveedor de identidades.
- La notificación **authenticationSource** con un valor predeterminado de **socialIdpAuthentication**.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="id" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="facebook.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| client_id | Sí | El identificador de la aplicación del proveedor de identidades. |
| IdTokenAudience | Sin | El público de id_token. Si se especifica, Azure AD B2C comprueba si el token está en una notificación devuelta por el proveedor de identidades y es igual al especificado. |
| authorization_endpoint | Sí | La dirección URL del punto de conexión de autorización de acuerdo con RFC 6749. |
| AccessTokenEndpoint | Sí | La dirección URL del punto de conexión del token de acuerdo con RFC 6749. |  
| ClaimsEndpoint | Sí | La dirección URL del punto de conexión de la información de usuario de acuerdo con RFC 6749. | 
| AccessTokenResponseFormat | Sin | El formato de la llamada al punto de conexión del token de acceso. Por ejemplo, Facebook requiere un método HTTP GET, pero la respuesta del token de acceso está en formato JSON. |
| AdditionalRequestQueryParameters | Sin | Parámetros de consulta de solicitud adicionales. Por ejemplo, es posible que desee enviar parámetros adicionales a su proveedor de identidades. Puede incluir varios parámetros usando la coma como delimitador. | 
| ClaimsEndpointAccessTokenName | Sin | El nombre del parámetro de la cadena de consulta del token de acceso. Los puntos de conexión de notificaciones de algunos proveedores de identidades admiten la solicitud GET HTTP. En este caso, se envía el token de portador mediante un parámetro de cadena de consulta en lugar del encabezado de autorización. |
| ClaimsEndpointFormatName | Sin | El nombre del parámetro de cadena de consulta de formato. Por ejemplo, puede establecer el nombre como `format` en este punto de conexión de notificaciones de LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ClaimsEndpointFormat | Sin | El valor del parámetro de cadena de consulta de formato. Por ejemplo, puede establecer el valor como `json` en este punto de conexión de notificaciones de LinkedIn `https://api.linkedin.com/v1/people/~?format=json`. | 
| ProviderName | Sin | Nombre del proveedor de identidades. |
| response_mode | Sin | Método que usará el proveedor de identidades para enviar de vuelta el resultado Azure AD B2C. Valores posibles: `query`, `form_post` (predeterminado) o `fragment`. |
| scope | Sin | El ámbito de la solicitud que se define según la especificación del proveedor de identidades de OAuth2. Por ejemplo, `openid`, `profile` y `email`. |
| HttpBinding | Sin | Enlace HTTP esperado al token de acceso y los puntos de conexión del token de notificaciones. Valores posibles: `GET` o `POST`.  |
| ResponseErrorCodeParamName | Sin | El nombre del parámetro que contiene el mensaje de error devuelto a través de HTTP 200 (OK). |
| ExtraParamsInAccessTokenEndpointResponse | Sin | Contiene los parámetros adicionales que pueden devolverse en la respuesta de **AccessTokenEndpoint** por algunos proveedores de identidades. Por ejemplo, la respuesta de **AccessTokenEndpoint** contiene un parámetro adicional, como `openid`, que es un parámetro obligatorio además del elemento access_token en una cadena de consulta de solicitud **ClaimsEndpoint**. Varios nombres de parámetro deben convertirse en caracteres de escape y estar separados por el delimitador de coma ",". |
| ExtraParamsInClaimsEndpointRequest | Sin | Contiene los parámetros adicionales que pueden devolverse en la solicitud **ClaimsEndpoint** por algunos proveedores de identidades. Varios nombres de parámetro deben convertirse en caracteres de escape y estar separados por el delimitador de coma ",". |

## <a name="cryptographic-keys"></a>Claves de cifrado

El elemento **CryptographicKeys** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| client_secret | Sí | Secreto de cliente de la aplicación del proveedor de identidades. La clave de cifrado es necesaria solo si los metadatos de **response_type** se establecen en `code`. En este caso, Azure AD B2C realiza otra llamada para cambiar el código de autorización por un token de acceso. Si los metadatos se establecen en `id_token`, puede omitir la clave criptográfica. |  

## <a name="redirect-uri"></a>URI de redireccionamiento

Cuando configure la dirección URL de redireccionamiento de su proveedor de identidades, escriba `https://login.microsoftonline.com/te/tenant/policyId/oauth2/authresp`. Asegúrese de reemplazar **tenant** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com) y **policyId** por el identificador de la directiva (por ejemplo, b2c_1_policy). El URI de redireccionamiento debe estar en minúsculas.

Si usa el dominio **b2clogin.com** en lugar de **login.microsoftonline.com**, asegúrese de usar b2clogin.com en lugar de login.microsoftonline.com.

Ejemplos:

- [Adición de Google + como un proveedor de identidades de OAuth2 mediante directivas personalizadas](active-directory-b2c-custom-setup-goog-idp.md)













