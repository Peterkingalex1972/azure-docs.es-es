---
title: Definición de un perfil técnico de OAuth1 en una directiva personalizada en Azure Active Directory B2C | Microsoft Docs
description: Defina un perfil técnico de OAuth1 en una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 993fc8b2e318b59775f61de391ac75fa765485f0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66513113"
---
# <a name="define-an-oauth1-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico de OAuth1 en una directiva personalizada en Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C proporciona compatibilidad con el proveedor de identidades del protocolo [OAuth 1.0](https://tools.ietf.org/html/rfc5849). En este artículo se describen los detalles para que un perfil técnico interactúe con un proveedor de notificaciones que admita este protocolo estandarizado. Con un perfil técnico OAuth1, puede realizar la federación con un proveedor de identidades basado en OAuth1, como Twitter. Esto permite a los usuarios iniciar sesión con sus identidades de redes sociales o de empresa existentes.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `OAuth1`. Por ejemplo, el protocolo para el perfil técnico **Twitter-OAUTH1** es `OAuth1`.

```XML
<TechnicalProfile Id="Twitter-OAUTH1">
  <DisplayName>Twitter</DisplayName>
  <Protocol Name="OAuth1" />
  ...    
```

## <a name="input-claims"></a>Notificaciones de entrada

Los elementos **InputClaims** y **InputClaimsTransformations** están vacíos o faltan.

## <a name="output-claims"></a>Notificaciones de salida

El elemento **OutputClaims** contiene una lista de notificaciones devuelta por el proveedor de identidadades de OAuth1. Es posible que tenga que asignar el nombre de la notificación definida en la directiva al nombre definido en el proveedor de identidades. También puede incluir notificaciones no especificadas por el proveedor de identidades, siempre que establezca el atributo **DefaultValue**.

El elemento **OutputClaimsTransformations** puede contener una colección de elementos **OutputClaimsTransformation** que se usan para modificar las notificaciones de salida o generar otras nuevas.

El ejemplo siguiente muestra las notificaciones devueltas por el proveedor de identidades de Twitter:

- La notificación **user_id** también se asigna a la notificación **issuerUserId**.
- La notificación **screen_name** que se asigna a la notificación **displayName**.
- La notificación **email** sin asignación de nombre.

El perfil técnico también devuelve notificaciones, que no son devueltas por el proveedor de identidades: 

- La notificación **identityProvider** que contiene el nombre del proveedor de identidades.
- La notificación **authenticationSource** con un valor predeterminado de `socialIdpAuthentication`.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="user_id" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="screen_name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="twitter.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| client_id | Sí | El identificador de la aplicación del proveedor de identidades. |
| ProviderName | Sin | Nombre del proveedor de identidades. |
| request_token_endpoint | Sí | La dirección URL del punto de conexión del token de solicitud de acuerdo con RFC 5849. |
| authorization_endpoint | Sí | La dirección URL del punto de conexión de autorización de acuerdo con RFC 5849. |
| access_token_endpoint | Sí | La dirección URL del punto de conexión del token de acuerdo con RFC 5849. |
| ClaimsEndpoint | Sin | La dirección URL del punto de conexión de la información de usuario. | 
| ClaimsResponseFormat | Sin | El formato de respuesta de las notificaciones.|

## <a name="cryptographic-keys"></a>Claves de cifrado

El elemento **CryptographicKeys** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| client_secret | Sí | Secreto de cliente de la aplicación del proveedor de identidades.   | 

## <a name="redirect-uri"></a>URI de redireccionamiento

Cuando configure la dirección URL de redireccionamiento de su proveedor de identidades, escriba `https://login.microsoftonline.com/te/tenant/policyId/oauth1/authresp`. Asegúrese de reemplazar **tenant** por el nombre de su inquilino (por ejemplo, contosob2c.onmicrosoft.com) y **policyId** por el identificador de la directiva (por ejemplo, b2c_1_policy). El URI de redireccionamiento necesita estar escrito todo en minúsculas. Agregue un URI de redirección para todas las directivas que usan el inicio de sesión del proveedor de identidades. 

Si usa el dominio **b2clogin.com** en lugar de **login.microsoftonline.com**, asegúrese de usar b2clogin.com en lugar de login.microsoftonline.com.

Ejemplos:

- [Adición de Twitter como un proveedor de identidades de OAuth1 mediante directivas personalizadas](active-directory-b2c-custom-setup-twitter-idp.md)













