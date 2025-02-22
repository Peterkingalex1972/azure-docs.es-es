---
title: Configuración del flujo de credenciales de contraseña de propietario del recurso en Azure Active Directory B2C | Microsoft Docs
description: Obtenga información sobre cómo configurar el flujo de credenciales de contraseña de propietario del recurso en Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d01e8ce894bfb1ece3555eddc714d2d3a80e44b5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164847"
---
# <a name="configure-the-resource-owner-password-credentials-flow-in-azure-active-directory-b2c-using-a-custom-policy"></a>Configure el flujo de credenciales de contraseña de propietario del recurso en Azure Active Directory B2C con una directiva personalizada.

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

En Azure Active Directory (Azure AD) B2C, el flujo de las credenciales de contraseña de propietario del recurso (ROPC) es un flujo de autenticación estándar de OAuth. En este flujo, una aplicación (que también se conoce como usuario de confianza), intercambia credenciales válidas por tokens. En las credenciales, se incluye un identificador de usuario y una contraseña. Los tokens devueltos son un token de identificador, un token de acceso y un token de actualización.

Las opciones siguientes se admiten en el flujo de ROPC:

- **Cliente nativo**: la interacción del usuario durante la autenticación se produce cuando se ejecuta el código en un dispositivo en el lado del usuario.
- **Flujo de cliente público**: en la llamada API, solo se envían credenciales de usuario recopiladas por una aplicación. No se envían las credenciales de la aplicación.
- **Add new claims (Agregar nuevas notificaciones)** : se puede cambiar el contenido del token de identificador para agregar nuevas notificaciones.

No se admiten los siguientes flujos:

- **Entre servidores**: el sistema de protección de identidades necesita una dirección IP de confianza que recopile el autor de la llamada (el cliente nativo) como parte de la interacción. En una llamada API del lado servidor, se utiliza sólo la dirección IP del servidor. Si se producen demasiados errores de inicios de sesión, el sistema de protección de identidades puede tratar de identificar una dirección IP repetida como un atacante.
- **Aplicación de página única**: una aplicación de front-end que se escribe principalmente en JavaScript. A menudo, la aplicación se escribe con un marco como AngularJS, Ember.js o Durandal.
- **Flujo de cliente confidencial**: se valida el identificador de cliente de la aplicación, pero no el secreto de aplicación.

## <a name="prerequisites"></a>Requisitos previos

Siga los pasos de [Introducción a las directivas personalizadas en Azure Active Directory B2C](active-directory-b2c-get-started-custom.md).

## <a name="register-an-application"></a>Registro de una aplicación

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Asegúrese de que usa el directorio que contiene el inquilino de Azure AD B2C. Para ello, haga clic en el **filtro de directorio y suscripción** en el menú superior y elija el directorio que contiene el inquilino.
3. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, y busque y seleccione **Azure AD B2C**.
4. Seleccione **Aplicaciones** y **Agregar**.
5. Escriba un nombre para la aplicación, como *ROPC_Auth_app*.
6. Seleccione **No** para **API web o aplicación web** y seleccione **Sí** para **Cliente nativo**.
7. Deje los demás valores como están y seleccione **Crear**.
8. Seleccione la nueva aplicación y anote el identificador de aplicación para usarlo más tarde.

##  <a name="create-a-resource-owner-policy"></a>Creación de una directiva del propietario del recurso

1. Abra el archivo *TrustFrameworkExtensions.xml*.
2. Si aún no existe, agregue un elemento **ClaimsSchema** y sus elementos secundarios como el primer elemento dentro del elemento **BuildingBlocks**:

    ```XML
    <ClaimsSchema>
      <ClaimType Id="logonIdentifier">
        <DisplayName>User name or email address that the user can use to sign in</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="resource">
        <DisplayName>The resource parameter passes to the ROPC endpoint</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokenIssuedOnDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
      <ClaimType Id="refreshTokensValidFromDateTime">
        <DisplayName>An internal parameter used to determine whether the user should be permitted to authenticate again using their existing refresh token.</DisplayName>
        <DataType>string</DataType>
      </ClaimType>
    </ClaimsSchema>
    ```

3. Después de **ClaimsSchema**, agregue un elemento **ClaimsTransformations** y sus elementos secundarios al elemento **BuildingBlocks**:

    ```XML
    <ClaimsTransformations>
      <ClaimsTransformation Id="CreateSubjectClaimFromObjectID" TransformationMethod="CreateStringClaim">
        <InputParameters>
          <InputParameter Id="value" DataType="string" Value="Not supported currently. Use oid claim." />
        </InputParameters>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="sub" TransformationClaimType="createdClaim" />
        </OutputClaims>
      </ClaimsTransformation>
    
      <ClaimsTransformation Id="AssertRefreshTokenIssuedLaterThanValidFromDate" TransformationMethod="AssertDateTimeIsGreaterThan">
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" TransformationClaimType="leftOperand" />
          <InputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" TransformationClaimType="rightOperand" />
        </InputClaims>
        <InputParameters>
          <InputParameter Id="AssertIfEqualTo" DataType="boolean" Value="false" />
          <InputParameter Id="AssertIfRightOperandIsNotPresent" DataType="boolean" Value="true" />
        </InputParameters>
      </ClaimsTransformation>
    </ClaimsTransformations>
    ```

4. Busque el elemento **ClaimsProvider** que tenga como **DisplayName** `Local Account SignIn` y agregue el siguiente perfil técnico:

    ```XML
    <TechnicalProfile Id="ResourceOwnerPasswordCredentials-OAUTH2">
      <DisplayName>Local Account SignIn</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
        <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
        <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
        <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
        <Item Key="response_types">id_token</Item>
        <Item Key="response_mode">query</Item>
        <Item Key="scope">email openid</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="logonIdentifier" PartnerClaimType="username" Required="true" DefaultValue="{OIDC:Username}"/>
        <InputClaim ClaimTypeReferenceId="password" Required="true" DefaultValue="{OIDC:Password}" />
        <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
        <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
        <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
        <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="00000000-0000-0000-0000-000000000000" />
        <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="00000000-0000-0000-0000-000000000000" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
        <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
    ```

    Reemplace el valor **DefaultValue** de **client_id** por el identificador de aplicación de la aplicación ProxyIdentityExperienceFramework que ha creado en el tutorial de requisitos previos. Reemplace el valor **DefaultValue** de **resource_id** por el identificador de aplicación de la aplicación ProxyIdentityExperienceFramework que también ha creado en el tutorial de requisitos previos.  

5. Agregue los siguientes elementos de **ClaimsProvider** con sus perfiles técnicos al elemento **ClaimsProviders**:

    ```XML
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-UserReadUsingObjectId-CheckRefreshTokenDate">
          <Metadata>
            <Item Key="Operation">Read</Item>
            <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
          </Metadata>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokensValidFromDateTime" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="AssertRefreshTokenIssuedLaterThanValidFromDate" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromObjectID" />
          </OutputClaimsTransformations>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Session Management</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SM-RefreshTokenReadAndSetup">
          <DisplayName>Trustframework Policy Engine Refresh Token Setup Technical Profile</DisplayName>
          <Protocol Name="None" />
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="objectId" />
            <OutputClaim ClaimTypeReferenceId="refreshTokenIssuedOnDateTime" />
          </OutputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>

    <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="JwtIssuer">
          <Metadata>
            <!-- Point to the redeem refresh token user journey-->
            <Item Key="RefreshTokenUserJourneyId">ResourceOwnerPasswordCredentials-RedeemRefreshToken</Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

6. Agregue un elemento **UserJourneys** y sus elementos secundarios al elemento **TrustFrameworkPolicy**:

    ```XML
    <UserJourney Id="ResourceOwnerPasswordCredentials">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="ResourceOwnerFlow" TechnicalProfileReferenceId="ResourceOwnerPasswordCredentials-OAUTH2" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    <UserJourney Id="ResourceOwnerPasswordCredentials-RedeemRefreshToken">
      <PreserveOriginalAssertion>false</PreserveOriginalAssertion>
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="RefreshTokenSetupExchange" TechnicalProfileReferenceId="SM-RefreshTokenReadAndSetup" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="CheckRefreshTokenDateFromAadExchange" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId-CheckRefreshTokenDate" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="3" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
    </UserJourney>
    ```

7. En la página **Directivas personalizadas** del inquilino de Azure AD B2C, seleccione **Cargar directiva**.
8. Habilite **Sobrescribir la directiva, si existe**, y busque y seleccione el archivo *TrustFrameworkExtensions.xml*.
9. Haga clic en **Cargar**.

## <a name="create-a-relying-party-file"></a>Creación de un archivo de usuario de confianza

Después, actualice el archivo del usuario de confianza que inicia el recorrido del usuario que ha creado:

1. Realice una copia del archivo *SignUpOrSignin.xml* en el directorio de trabajo y cambie el nombre a *ROPC_Auth.xml*.
2. Abra el nuevo archivo y actualice el valor del atributo **PolicyId** del elemento **TrustFrameworkPolicy** con un valor único. El identificador de directiva es el nombre de la directiva. Por ejemplo, **B2C_1A_ROPC_Auth**.
3. Cambie el valor del atributo **ReferenceId** en **DefaultUserJourney** a `ResourceOwnerPasswordCredentials`.
4. Cambie el elemento **OutputClaims** para que solo contenga las siguientes notificaciones:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="displayName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="givenName" DefaultValue="" />
    <OutputClaim ClaimTypeReferenceId="surname" DefaultValue="" />
    ```

5. En la página **Directivas personalizadas** del inquilino de Azure AD B2C, seleccione **Cargar directiva**.
6. Habilite **Sobrescribir la directiva, si existe**, y busque y seleccione el archivo *ROPC_Auth.xml*.
7. Haga clic en **Cargar**.

## <a name="test-the-policy"></a>Prueba de la directiva

Use su aplicación favorita de desarrollo de API para generar una llamada de API y revise la respuesta para depurar la directiva. Cree una llamada como en este ejemplo con la siguiente información como el cuerpo de la solicitud POST:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Reemplace `your-tenant-name` por el nombre del inquilino de Azure AD B2C.
- Reemplace `B2C_1A_ROPC_Auth` por el nombre completo de la directiva de credenciales de contraseña del propietario de recursos.

| Clave | Valor |
| --- | ----- |
| nombre de usuario | `user-account` |
| contraseña | `password1` |
| grant_type | contraseña |
| scope | openid `application-id` offline_access |
| client_id | `application-id` |
| response_type | id_token del token |

- Reemplace `user-account` por el nombre de una cuenta de usuario en el inquilino.
- Reemplace `password1` por la contraseña de la cuenta de usuario.
- Reemplace `application-id` por el identificador de aplicación del registro *ROPC_Auth_app*.
- *Offline_access* es opcional si quiere recibir un token de actualización.

La solicitud POST real es similar al ejemplo siguiente:

```HTTPS
POST /yourtenant.onmicrosoft.com/oauth2/v2.0/token?B2C_1_ROPC_Auth HTTP/1.1
Host: yourtenant.b2clogin.com
Content-Type: application/x-www-form-urlencoded

username=contosouser.outlook.com.ws&password=Passxword1&grant_type=password&scope=openid+bef22d56-552f-4a5b-b90a-1988a7d634ce+offline_access&client_id=bef22d56-552f-4a5b-b90a-1988a7d634ce&response_type=token+id_token
```

Una respuesta correcta con acceso sin conexión se parece al siguiente ejemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki...",
    "token_type": "Bearer",
    "expires_in": "3600",
    "refresh_token": "eyJraWQiOiJacW9pQlp2TW5pYVc2MUY0TnlfR3REVk1EVFBLbUJLb0FUcWQ1ZWFja1hBIiwidmVyIjoiMS4wIiwiemlwIjoiRGVmbGF0ZSIsInNlciI6Ij...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ik9YQjNhdTNScWhUQWN6R0RWZDM5djNpTmlyTWhqN2wxMjIySnh6TmgwRlki..."
}
```

## <a name="redeem-a-refresh-token"></a>Canjear un token de actualización

Cree una llamada POST como la que se muestra aquí. Use la información de la tabla siguiente como el cuerpo de la solicitud:

`https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token?p=B2C_1_ROPC_Auth`

- Reemplace `your-tenant-name` por el nombre del inquilino de Azure AD B2C.
- Reemplace `B2C_1A_ROPC_Auth` por el nombre completo de la directiva de credenciales de contraseña del propietario de recursos.

| Clave | Valor |
| --- | ----- |
| grant_type | refresh_token |
| response_type | ID_token |
| client_id | `application-id` |
| resource | `application-id` |
| refresh_token | `refresh-token` |

- Reemplace `application-id` por el identificador de aplicación del registro *ROPC_Auth_app*.
- Reemplace `refresh-token` por el **refresh_token** que se ha vuelto a enviar en la respuesta anterior.

Una respuesta correcta se parece al siguiente ejemplo:

```JSON
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQndhT...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsImtpZCI6Ilg1ZVhrNHh5b2pORnVtMWtsMll0djhkbE5QNC1jNTdkTzZRR1RWQn...",
    "token_type": "Bearer",
    "not_before": 1533672990,
    "expires_in": 3600,
    "expires_on": 1533676590,
    "resource": "bef2222d56-552f-4a5b-b90a-1988a7d634c3",
    "id_token_expires_in": 3600,
    "profile_info": "eyJ2ZXIiOiIxLjAiLCJ0aWQiOiI1MTZmYzA2NS1mZjM2LTRiOTMtYWE1YS1kNmVlZGE3Y2JhYzgiLCJzdWIiOm51bGwsIm5hbWUiOiJEYXZpZE11IiwicHJlZmVycmVkX3VzZXJuYW1lIjpudWxsLCJpZHAiOiJMb2NhbEFjY291bnQifQ",
    "refresh_token": "eyJraWQiOiJjcGltY29yZV8wOTI1MjAxNSIsInZlciI6IjEuMCIsInppcCI6IkRlZmxhdGUiLCJzZXIiOiIxLjAi...",
    "refresh_token_expires_in": 1209600
}
```

## <a name="use-a-native-sdk-or-app-auth"></a>Usar un SDK nativo o autenticación de aplicación

La implementación de Azure AD B2C cumple los estándares de OAuth 2.0 para las credenciales de contraseña de propietario de recursos del cliente público y es compatible con la mayoría de los SDK de cliente. Para obtener la información más reciente, consulte [Procedimientos recomendados de implementación modernos para el SDK de aplicaciones nativas para OAuth 2.0 y OpenID Connect](https://appauth.io/).

## <a name="next-steps"></a>Pasos siguientes

- Vea un ejemplo completo de este escenario en el [Paquete inicial de directiva personalizada de Azure Active Directory B2C](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/source/aadb2c-ief-ropc).
- Obtenga más información sobre los tokens usados por Azure Active Directory B2C en la [Referencia de tokens](active-directory-b2c-reference-tokens.md).
