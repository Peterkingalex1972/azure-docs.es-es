---
title: Definición de un perfil técnico para un emisor de tokens JWT en una directiva personalizada en Azure Active Directory B2C | Microsoft Docs
description: Defina un perfil técnico para un emisor de tokens JWT en una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 573463d91fc7a4119bd1bc30182588ff9dfdecb7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510701"
---
# <a name="define-a-technical-profile-for-a-jwt-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definición de un perfil técnico para un emisor de tokens JWT en una directiva personalizada de Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C emite varios tipos de tokens de seguridad a medida que procesa cada flujo de autenticación. Un perfil técnico de un emisor de tokens de JWT emite un token de JWT que se devuelve a la aplicación de usuario de confianza. Este perfil técnico suele ser el último paso de orquestación en el recorrido del usuario.

## <a name="protocol"></a>Protocolo

El atributo **Name** del elemento **Protocol** tiene que establecerse en `None`. Establezca el elemento **OutputTokenFormat** en `JWT`.

En el ejemplo siguiente se muestra un perfil técnico de `JwtIssuer`:

```XML
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  ...
</TechnicalProfile>
```
 
## <a name="input-output-and-persist-claims"></a>Notificaciones de entrada, de salida y persistentes

Los elementos **InputClaims**, **OutputClaims** y **PersistClaims** están vacíos o faltan. Los elementos **InutputClaimsTransformations** y **OutputClaimsTransformations** faltan también.

## <a name="metadata"></a>Metadatos

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| issuer_refresh_token_user_identity_claim_type | Sí | La notificación que se debe usar como identidad del usuario en los tokens de actualización y los códigos de autorización de OAuth2. De forma predeterminada, se debe establecer en `objectId`, a menos que especifique un tipo de notificación SubjectNamingInfo diferente. | 
| SendTokenResponseBodyWithJsonNumbers | Sin | Siempre se establece en `true`. En el caso de formatos heredados en que los valores numéricos se indiquen como cadenas en lugar de números JSON, establézcalo en `false`. Este atributo es necesario para los clientes que han tenido una dependencia de una implementación anterior que devolvía tales propiedades como cadenas. | 
| token_lifetime_secs | Sin | Vigencia del token de acceso. La duración del token de portador de OAuth 2.0 se ha usado para obtener acceso a un recurso protegido. El valor predeterminado es 3600 segundos (1 hora). El mínimo (incluido) es de 300 segundos (5 minutos). El máximo (incluido) es de 86 400 segundos (24 horas). | 
| id_token_lifetime_secs | Sin | Vigencia del token de identificador. El valor predeterminado es 3600 segundos (1 hora). El mínimo (incluido) es de 300 segundos (5 minutos). El máximo (incluido) es de 86 400 segundos (24 horas). | 
| refresh_token_lifetime_secs | Sin | Vigencia del token de actualización. El período máximo en que un token de actualización se puede utilizar para adquirir un nuevo token de acceso, si se hubiera concedido el ámbito offline_access a la aplicación. El valor predeterminado es 1 209 600 segundos (14 días). El mínimo (incluido) es de 86 400 segundos (24 horas). El máximo (incluido) es de 7 776 000 segundos (90 días). | 
| rolling_refresh_token_lifetime_secs | Sin | Vigencia de la ventana deslizante del token de actualización. Una vez que haya transcurrido este período de tiempo, el usuario está obligado a volver a autenticarse, independientemente del período de validez del token de actualización más reciente que haya adquirido la aplicación. Si no desea aplicar una duración de ventana deslizante, establezca el valor de allow_infinite_rolling_refresh_token en `true`. El valor predeterminado es 7 776 000 segundos (90 días). El mínimo (incluido) es de 86 400 segundos (24 horas). El máximo (incluido) es de 31 536 000 segundos (365 días). | 
| allow_infinite_rolling_refresh_token | Sin | Si se establece en `true`, la vigencia de la ventana deslizante del token de actualización es permanente. |
| IssuanceClaimPattern | Sí | Controla la notificación del emisor (iss). Uno de los valores:<ul><li>AuthorityAndTenantGuid: la notificación de emisor incluye el nombre de dominio, como `login.microsoftonline` o `tenant-name.b2clogin.com`, así como el identificador de inquilino https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/v2.0/.</li><li>AuthorityWithTfp: la notificación de emisor incluye el nombre de dominio, como `login.microsoftonline` o `tenant-name.b2clogin.com`, su identificador de inquilino y el nombre de directiva de usuario de confianza. https://login.microsoftonline.com/tfp/00000000-0000-0000-0000-000000000000/b2c_1a_tp_sign-up-or-sign-in/v2.0/</li></ul> | 
| AuthenticationContextReferenceClaimPattern | Sin | Controla el valor de notificación `acr`.<ul><li>None: Azure AD B2C no emite la notificación de acr</li><li>PolicyId: la notificación `acr` contiene el nombre de directiva</li></ul>Las opciones para configurar este valor son TFP (directiva de marco de confianza) y ACR (referencia de contexto de autenticación). Se recomienda establecer este valor en TFP, para establecer el valor, asegúrese de que existe `<Item>` con `Key="AuthenticationContextReferenceClaimPattern"` y el valor es `None`. En la directiva de usuario de confianza, agregue el elemento `<OutputClaims>` y `<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />`. También asegúrese de que la directiva contiene el tipo de notificación `<ClaimType Id="trustFrameworkPolicy">   <DisplayName>trustFrameworkPolicy</DisplayName>     <DataType>string</DataType> </ClaimType>`. | 

## <a name="cryptographic-keys"></a>Claves de cifrado

El elemento CryptographicKeys contiene los siguientes atributos:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| issuer_secret | Sí | El certificado X509 (conjunto de claves RSA) que se va a usar para firmar el token JWT. Esta es la clave `B2C_1A_TokenSigningKeyContainer` configurada en [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md). | 
| issuer_refresh_token_key | Sí | El certificado X509 (conjunto de claves RSA) que se va a usar para cifrar el token de actualización. Ha configurado la clave `B2C_1A_TokenEncryptionKeyContainer` en [Introducción a las directivas personalizadas](active-directory-b2c-get-started-custom.md). |














