---
title: 'Aplicación móvil que llama a las API web: obtención de un token para la aplicación - Plataforma de identidad de Microsoft'
description: Obtenga información sobre cómo compilar una aplicación móvil que llama a otras API web (para obtener un token para la aplicación).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49717355cab5441d26608fa12333bd1b8b73d44
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413538"
---
# <a name="mobile-app-that-calls-web-apis---get-a-token"></a>Aplicación móvil que llama a las API web: obtener un token

Antes de poder empezar a llamar a las API web protegidas, la aplicación necesitará un token de acceso. Este artículo le guiará a través del proceso para obtener un token usando la biblioteca de autenticación de Microsoft (MSAL).

## <a name="scopes-to-request"></a>Solicitud de ámbitos

Cuando solicite un token, deberá definir un ámbito. El ámbito determina a qué datos puede acceder la aplicación.  

El enfoque más sencillo consiste en combinar el `App ID URI` de la API web deseada con el ámbito `.default`. Al hacerlo, se indica a la plataforma de identidad de Microsoft que su aplicación requiere todos los ámbitos establecidos en el portal.

#### <a name="android"></a>Android
```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
```

#### <a name="ios"></a>iOS
```swift
let scopes: [String] = ["https://graph.microsoft.com/.default"]
```

#### <a name="xamarin"></a>Xamarin
```CSharp 
var scopes = new [] {"https://graph.microsoft.com/.default"};
```

## <a name="get-tokens"></a>Obtención de tokens

### <a name="via-msal"></a>A través de MSAL

MSAL permite que las aplicaciones adquieran tokens de forma silenciosa e interactiva. Simplemente llame a estos métodos y MSAL devolverá un token de acceso para los ámbitos solicitados. El patrón correcto consiste en realizar una solicitud silenciosa y revertir a una solicitud interactiva.

#### <a name="android"></a>Android

```Java
String[] SCOPES = {"https://graph.microsoft.com/.default"};
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);

// Check if there are any accounts we can sign in silently.
// Result is in the silent callback (success or error).
sampleApp.getAccounts(new PublicClientApplication.AccountsLoadedCallback() {
    @Override
    public void onAccountsLoaded(final List<IAccount> accounts) {

        if (accounts.isEmpty() && accounts.size() == 1) {
            // TODO: Create a silent callback to catch successful or failed request.
            sampleApp.acquireTokenSilentAsync(SCOPES, accounts.get(0), getAuthSilentCallback());
        } else {
            /* No accounts or > 1 account. */
        }
    }
});    

[...]

// No accounts found. Interactively request a token.
// TODO: Create an interactive callback to catch successful or failed request.
sampleApp.acquireToken(getActivity(), SCOPES, getAuthInteractiveCallback());        
```

#### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)

// Get tokens.
let parameters = MSALSilentTokenParameters(scopes: kScopes, account: account)
applicationContext.acquireTokenSilent(with: parameters) { (result, error) in
    if let error = error {
        let nsError = error as NSError

        // interactionRequired means you need to ask the user to sign in. This usually happens
        // when the user's refresh token is expired or when the user has changed the password,
        // among other possible reasons.
        if (nsError.domain == MSALErrorDomain) {
            if (nsError.code == MSALError.interactionRequired.rawValue) {    
                DispatchQueue.main.async {
                    guard let applicationContext = self.applicationContext else { return }
                    let parameters = MSALInteractiveTokenParameters(scopes: kScopes)
                    applicationContext.acquireToken(with: parameters) { (result, error) in
                        if let error = error {
                            self.updateLogging(text: "Could not acquire token: \(error)")
                            return
                        }

                        guard let result = result else {
                            self.updateLogging(text: "Could not acquire token: No result returned")
                            return
                        }
                        
                        // Token is ready via interaction!
                        self.accessToken = result.accessToken
                    }
                }
                return
            }
        }

        self.updateLogging(text: "Could not acquire token silently: \(error)")
        return
    }
    guard let result = result else {
        self.updateLogging(text: "Could not acquire token: No result returned")
        return
    }

    // Token is ready via silent acquisition.
    self.accessToken = result.accessToken
}
```

#### <a name="xamarin"></a>Xamarin

El ejemplo siguiente muestra un código mínimo para obtener un token de forma interactiva para leer el perfil del usuario con Microsoft Graph.

```CSharp
string[] scopes = new string[] {"user.read"};
var app = PublicClientApplicationBuilder.Create(clientId).Build();
var accounts = await app.GetAccountsAsync();
AuthenticationResult result;
try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
             .ExecuteAsync();
}
catch(MsalUiRequiredException)
{
 result = await app.AcquireTokenInteractive(scopes)
             .ExecuteAsync();
}
```

### <a name="mandatory-parameters"></a>Parámetros obligatorios

`AcquireTokenInteractive` solo tiene un parámetro obligatorio ``scopes``, que contiene una enumeración de cadenas que definen los ámbitos para los que se requiere un token. Si el token es para Microsoft Graph, los ámbitos necesarios se pueden encontrar en la sección denominada "Permisos" en la referencia de API de cada API de Microsoft Graph. Por ejemplo, para [enumerar los contactos del usuario](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_list_contacts), necesitará usar el ámbito "User.Read", "Contacts.Read". Vea también [Referencia de permisos de Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/permissions_reference).

Si no lo ha especificado al compilar la aplicación, en Android, también deberá especificar la actividad primaria (mediante `.WithParentActivityOrWindow`, vea más abajo) para que el token vuelva a esa actividad principal tras la interacción. Si no se especifica, se producirá una excepción al llamar a `.ExecuteAsync()`.

### <a name="specific-optional-parameters"></a>Parámetros opcionales específicos

#### <a name="withprompt"></a>WithPrompt

`WithPrompt()` se usa para controlar la interactividad con el usuario especificando un símbolo del sistema.

<img src="https://user-images.githubusercontent.com/13203188/53438042-3fb85700-39ff-11e9-9a9e-1ff9874197b3.png" width="25%" />

La clase define las constantes siguientes:

- ``SelectAccount``: forzará el STS para que presente el cuadro de diálogo de selección de cuenta que contiene las cuentas para las que el usuario tiene una sesión. Esta opción es útil cuando los desarrolladores de aplicaciones quieren permitir que el usuario elija entre diferentes identidades. Esta opción dirige a MSAL para que envíe ``prompt=select_account`` al proveedor de identidades. Esta opción es el valor predeterminado y funciona bien para proporcionar la mejor experiencia posible en función de la información disponible (cuenta, presencia de una sesión para el usuario, etc. ). No la cambie a menos que tenga buena razón para hacerlo.
- ``Consent``: permite que el desarrollador de la aplicación obligue al usuario a que se le pida consentimiento incluso si ya lo otorgó anteriormente. En este caso, MSAL envía `prompt=consent` al proveedor de identidades. Esta opción se puede usar en algunas aplicaciones centradas en la seguridad donde la gobernanza de la organización exige que se presente al usuario el cuadro de diálogo de consentimiento cada vez que se utiliza la aplicación.
- ``ForceLogin``: permite que el desarrollador de la aplicación haga que el servicio solicite al usuario las credenciales incluso si esta petición al usuario no es necesaria. Esta opción puede ser útil si se produce un error al adquirir un token, para permitir que el usuario vuelva a iniciar sesión. En este caso, MSAL envía `prompt=login` al proveedor de identidades. De nuevo, se ha utilizado en algunas aplicaciones centradas en la seguridad donde la gobernanza de la organización exige que el usuario vuelva a iniciar sesión cada vez que accede a determinadas partes de una aplicación.
- ``Never`` (solo para .NET 4.5 y WinRT) no preguntará al usuario, pero intentará usar en su lugar la cookie almacenada en la vista web incrustada oculta (vea a continuación: Vistas web en MSAL.NET). Esta opción puede producir un error, en cuyo caso `AcquireTokenInteractive` generará una excepción para notificar que es necesaria una interacción de la interfaz de usuario y deberá utilizar otro parámetro `Prompt`.
- ``NoPrompt``: No enviará ninguna solicitud al proveedor de identidades. Esta opción solo es útil para las directivas de perfil de edición de Azure AD B2C (consulte las [características específicas de B2C](https://aka.ms/msal-net-b2c-specificities)).

#### <a name="withextrascopetoconsent"></a>WithExtraScopeToConsent

Este modificador se usa en un escenario avanzado donde quiere que el usuario dé previamente su consentimiento a varios recursos por adelantado (y no quiere utilizar el consentimiento incremental, que normalmente se utiliza con MSAL.NET / la plataforma de identidad de Microsoft v2.0). Para obtener más detalles, vea [Cómo tener el consentimiento del usuario por adelantado para varios recursos](scenario-desktop-production.md#how-to-have--the-user-consent-upfront-for-several-resources).

```CSharp
var result = await app.AcquireTokenInteractive(scopesForCustomerApi)
                     .WithExtraScopeToConsent(scopesForVendorApi)
                     .ExecuteAsync();
```

#### <a name="other-optional-parameters"></a>Otros parámetros opcionales

Obtenga más información sobre los demás parámetros opcionales para `AcquireTokenInteractive` en la documentación de referencia para [AcquireTokenInteractiveParameterBuilder](/dotnet/api/microsoft.identity.client.acquiretokeninteractiveparameterbuilder?view=azure-dotnet-preview#methods).

### <a name="via-the-protocol"></a>A través del protocolo

No se recomienda usar el protocolo directamente. Si lo hace, la aplicación no admitirá algunos inicios de sesión únicos (SSO), la administración de dispositivos ni los escenarios de acceso condicional.

Cuando use el protocolo para obtener tokens para aplicaciones móviles, deberá realizar dos solicitudes: obtener un código de autorización y cambiarlo por un token.

#### <a name="get-authorization-code"></a>Obtener un código de autorización

```Text
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize?
client_id=<CLIENT_ID>
&response_type=code
&redirect_uri=<ENCODED_REDIRECT_URI>
&response_mode=query
&scope=openid%20offline_access%20https%3A%2F%2Fgraph.microsoft.com%2F.default
&state=12345
```

#### <a name="get-access-and-refresh-token"></a>Obtener un token de acceso y actualización

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1
Host: https://login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=<CLIENT_ID>
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=<ENCODED_REDIRECT_URI>
&grant_type=authorization_code
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Llamada a una API web](scenario-mobile-call-api.md)
