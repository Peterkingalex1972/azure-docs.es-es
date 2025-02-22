---
title: RelyingParty - Azure Active Directory B2C | Microsoft Docs
description: Especifique el elemento RelyingParty de una directiva personalizada en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bf8d4889c277d59d0c42894281a89345fbf90a84
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68716688"
---
# <a name="relyingparty"></a>RelyingParty

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

El elemento **RelyingParty** especifica el recorrido del usuario para ejecutar la solicitud actual en Azure Active Directory (Azure AD) B2C. También se especifica la lista de notificaciones que la aplicación del usuario de confianza necesita como parte del token emitido. Una aplicación del usuario de confianza, como una aplicación web, de escritorio o para dispositivos móviles, llama al archivo de la directiva del usuario de confianza. El archivo de la directiva del usuario de confianza ejecuta una tarea específica, como iniciar sesión, restablecer una contraseña o editar un perfil. Hay varias aplicaciones que pueden usar la misma directiva del usuario de confianza y una sola aplicación puede usar varias directivas. Todas las aplicaciones de usuario de confianza reciben el mismo token con notificaciones y el usuario sigue el mismo recorrido del usuario.

En el ejemplo siguiente se muestra un elemento **RelyingParty** en el archivo de directiva *B2C_1A_signup_signin*:

```XML
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="your-tenant.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>your-tenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>

  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
      <SingleSignOn Scope="TrustFramework" KeepAliveInDays="7"/>
      <SessionExpiryType>Rolling</SessionExpiryType>
      <SessionExpiryInSeconds>300</SessionExpiryInSeconds>
      <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="your-application-insights-key" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
      </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Description>The policy profile</Description>
      <Protocol Name="OpenIdConnect" />
      <Metadata>collection of key/value pairs of data</Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
  ...
```

El elemento **RelyingParty** opcional contiene los siguientes elementos:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| DefaultUserJourney | 1:1 | El recorrido del usuario predeterminado para la aplicación de usuario de confianza. |
| UserJourneyBehaviors | 0:1 | El ámbito de los comportamientos del recorrido del usuario. |
| TechnicalProfile | 1:1 | Un perfil técnico que es compatible con la aplicación del usuario de confianza. El perfil técnico proporciona un contrato para que la aplicación del usuario de confianza contacte con Azure AD B2C. |

## <a name="defaultuserjourney"></a>DefaultUserJourney

El elemento `DefaultUserJourney` especifica una referencia al identificador del recorrido del usuario que normalmente se define en la directiva Base o Extensions. En los ejemplos siguientes se muestra el recorrido del usuario para registrarse o iniciar sesión que se especifica en el elemento **RelyingParty**:

Directiva de *B2C_1A_signup_signin*:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

*B2C_1A_TrustFrameWorkBase* o *B2C_1A_TrustFrameworkExtensionPolicy*:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

El elemento **DefaultUserJourney** contiene el siguiente atributo:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| ReferenceId | Sí | Identificador del recorrido del usuario en la directiva. Para más información, consulte los [recorridos del usuario](userjourneys.md). |

## <a name="userjourneybehaviors"></a>UserJourneyBehaviors

El elemento **UserJourneyBehaviors** contiene los siguientes elementos:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| SingleSignOn | 0:1 | Ámbito del comportamiento de sesión de inicio de sesión único (SSO) de un recorrido del usuario. |
| SessionExpiryType |0:1 | El comportamiento de autenticación de la sesión. Valores posibles: `Rolling` o `Absolute`. El valor (predeterminado) `Rolling` indica que el usuario permanece conectado siempre y cuando el usuario esté activo en la aplicación. El valor `Absolute` indica que el usuario está obligado a autenticarse de nuevo tras el período de tiempo especificado por la duración de sesión de la aplicación. |
| SessionExpiryInSeconds | 0:1 | La duración de la cookie de la sesión de Azure AD B2C especificada como un entero se ha almacenado en el explorador del usuario tras la autenticación correcta. |
| JourneyInsights | 0:1 | La clave de instrumentación de Application Insights de Azure que se va a usar. |
| ContentDefinitionParameters | 0:1 | La lista de pares clave-valor que se anexará a la URI de carga de la definición de contenido. |

### <a name="singlesignon"></a>SingleSignOn

El elemento **SingleSignOn** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Ámbito | Sí | El ámbito del comportamiento de inicio de sesión único. Valores posibles: `Suppressed`, `Tenant`, `Application` o `Policy`. El valor `Suppressed` indica que se suprime el comportamiento. Por ejemplo, en el caso de una sesión de inicio de sesión único, no se mantiene ninguna sesión para el usuario y siempre se solicita al usuario una selección del proveedor de identidades. El valor `TrustFramework` indica que el comportamiento se aplica a todas las directivas del marco de confianza. Por ejemplo, un usuario que navega por dos recorridos de directiva para un marco de confianza no se solicita para una selección del proveedor de identidades. El valor `Tenant` indica que el comportamiento se aplica a todas las directivas del inquilino. Por ejemplo, a un usuario que sigue dos recorridos de directiva para un inquilino no se le solicita que seleccione el proveedor de identidades. El valor `Application` indica que el comportamiento se aplica a todas las directivas de la aplicación que hace la solicitud. Por ejemplo, a un usuario que sigue dos recorridos de directiva para una aplicación no se le solicita que seleccione el proveedor de identidades. El valor `Policy` indica que el comportamiento solo se aplica a una directiva. Por ejemplo, a un usuario que sigue dos recorridos de directiva para un marco de confianza se le solicita que seleccione el proveedor de identidades al cambiar de una directiva a otra. |
| KeepAliveInDays | Sí | Controla cuánto tiempo permanece el usuario con la sesión iniciada. Si se establece el valor en 0, se desactiva la funcionalidad KMSI. Para más información, consulte [Mantener la sesión iniciada](active-directory-b2c-reference-kmsi-custom.md). |

## <a name="journeyinsights"></a>JourneyInsights

El elemento **JourneyInsights** contiene los siguientes atributos:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| TelemetryEngine | Sí | El valor tiene que ser `ApplicationInsights`. |
| InstrumentationKey | Sí | Cadena que contiene la clave de instrumentación para el elemento de Application Insights. |
| DeveloperMode | Sí | Valores posibles: `true` o `false`. Si es `true`, Application Insights acelera la telemetría a través de la canalización de procesamiento. Esta configuración es adecuada para el desarrollo, pero está limitada a grandes volúmenes. Los registros de actividad detallados solo están diseñados para contribuir en el desarrollo de directivas personalizadas. No use el modo de desarrollo en producción. Los registros recopilan todas las notificaciones que se envían y se reciben de los proveedores de identidad durante el desarrollo. Si se utilizan en producción, el programador asume la responsabilidad sobre la PII (información personal de identificación) recopilada en el registro de información de la instancia de App Insights que le pertenece. Estos registros detallados solo se recopilan cuando este valor se establece en `true`.|
| ClientEnabled | Sí | Valores posibles: `true` o `false`. Si es `true`, se envía el script del lado cliente ApplicationInsights para realizar un seguimiento de la vista de página y de los errores del lado cliente. |
| ServerEnabled | Sí | Valores posibles: `true` o `false`. Si es `true`, se envía el JSON UserJourneyRecorder existente como evento personalizado a Application Insights. |
| TelemetryVersion | Sí | El valor tiene que ser `1.0.0`. |

Para obtener más información, vea [Recopilación de registros](active-directory-b2c-troubleshoot-custom.md).

## <a name="contentdefinitionparameters"></a>ContentDefinitionParameters

Mediante el uso de las directivas de Azure AD B2C, puede enviar un parámetro en una cadena de consulta. Al pasar dicho parámetro al punto de conexión HTML, puede cambiar de forma dinámica el contenido de la página. Por ejemplo, puede cambiar la imagen de fondo en la página de inicio de sesión o de registro de Azure AD B2C en función de un parámetro que se pasa desde la aplicación web o dispositivo móvil. Azure AD B2C pasa los parámetros de la cadena de consulta al archivo HTML dinámico, como un archivo .aspx.

En el ejemplo siguiente se pasa un parámetro denominado `campaignId` con un valor de `hawaii` en la cadena de consulta:

`https://login.microsoft.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?pB2C_1A_signup_signin&client_id=a415078a-0402-4ce3-a9c6-ec1947fcfb3f&nonce=defaultNonce&redirect_uri=http%3A%2F%2Fjwt.io%2F&scope=openid&response_type=id_token&prompt=login&campaignId=hawaii`

El elemento **ContentDefinitionParameters** contiene el elemento siguiente:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| ContentDefinitionParameter | 0:n | Una cadena que contiene el par clave-valor que se anexa a la cadena de consulta de un URI de carga de definición de contenido. |

El elemento **ContentDefinitionParameter** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Name | Sí | El nombre del par clave-valor. |

Para obtener más información, vea [Configuración de la interfaz de usuario con contenido dinámico usando directivas personalizadas](active-directory-b2c-ui-customization-custom-dynamic.md).

## <a name="technicalprofile"></a>TechnicalProfile

El elemento **TechnicalProfile** contiene el atributo siguiente:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Id | Sí | El valor tiene que ser `PolicyProfile`. |

El elemento **TechnicalProfile** contiene los elementos siguientes:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| DisplayName | 0:1 | Cadena que contiene el nombre del perfil técnico que se muestra a los usuarios. |
| DESCRIPCIÓN | 0:1 | Cadena que contiene la descripción del perfil técnico que se muestra a los usuarios. |
| Protocolo | 1:1 | Protocolo usado para la federación. |
| Metadatos | 0:1 | La colección de *Item* de los pares clave-valor usados por el protocolo para comunicarse con el punto de conexión en el transcurso de una transacción para configurar la interacción entre el usuario de confianza y otros participantes de la comunidad. |
| OutputClaims | 0:1 | Una lista de tipos de notificación que se consideran el resultado del perfil técnico. Cada uno de estos elementos contiene la referencia a un **ClaimType** ya definido en la sección **ClaimsSchema** o en una directiva de la que este archivo de directiva es heredero. |
| SubjectNamingInfo | 0:1 | El nombre del sujeto usado en los tokens. |

El elemento **Protocol** contiene el siguiente atributo:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| Name | Sí | El nombre de un protocolo válido admitido por Azure AD B2C que se usará como parte del perfil técnico. Valores posibles: `OpenIdConnect` o `SAML2`. El valor `OpenIdConnect` representa el estándar del protocolo OpenID Connect 1.0 según la especificación de la fundación de OpenID. `SAML2` representa el estándar del protocolo SAML 2.0 según la especificación de OASIS. No use un token de SAML en producción. |

## <a name="outputclaims"></a>OutputClaims

El elemento **OutputClaims** contiene el elemento siguiente:

| Elemento | Repeticiones | DESCRIPCIÓN |
| ------- | ----------- | ----------- |
| OutputClaim | 0:n | El nombre de un tipo de notificación esperado en la lista admitida para la directiva en la que se suscribe el usuario de confianza. Esta notificación actúa como un resultado del perfil técnico. |

El elemento **OutputClaim** contiene los atributos siguientes:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| ClaimTypeReferenceId | Sí | Una referencia a un **ClaimType** ya definido en la sección **ClaimsSchema** del archivo de directiva. |
| DefaultValue | Sin | Un valor predeterminado que se puede usar si el valor de notificación está vacío. |
| PartnerClaimType | Sin | Envía la notificación con un nombre distinto al configurado en la definición de ClaimType. |

### <a name="subjectnaminginfo"></a>SubjectNamingInfo

Con el elemento **SubjectNameingInfo**, controla el valor del asunto del token:
- **Token JTW**: la notificación `sub`. Esta es la entidad de seguridad sobre la que el token declara información como, por ejemplo, el usuario de una aplicación. Este valor es inmutable y no se puede reasignar ni volver a usar. Se puede usar para realizar comprobaciones de autorización de forma segura, por ejemplo, cuando el token se usa para acceder a un recurso. De manera predeterminada, la notificación del asunto se rellena con el identificador de objeto del usuario del directorio. Para obtener más información, vea [Configuración de token, sesión e inicio de sesión único](active-directory-b2c-token-session-sso.md).
- **Token SAML**: el elemento `<Subject><NameID>` que identifica el elemento del asunto.

El elemento **SubjectNamingInfo** contiene el siguiente atributo:

| Atributo | Obligatorio | DESCRIPCIÓN |
| --------- | -------- | ----------- |
| ClaimType | Sí | Una referencia a un **PartnerClaimType** de una notificación de salida. La notificación de salida debe definirse en la colección **OutputClaims** de la directiva del usuario de confianza. |

En el ejemplo siguiente se muestra cómo definir un usuario de confianza de OpenID Connect. La información sobre el nombre del asunto se configura como `objectId`:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```
El token JWT incluye la notificación `sub` con el objectId del usuario:

```JSON
{
  ...
  "sub": "6fbbd70d-262b-4b50-804c-257ae1706ef2",
  ...
}
```


