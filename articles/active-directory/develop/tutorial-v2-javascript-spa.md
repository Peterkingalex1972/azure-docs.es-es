---
title: Configuración guiada de una aplicación de página única de JavaScript en Azure AD v2.0 | Microsoft Docs
description: Cómo pueden llamar las aplicaciones SPA de JavaScript a una API que requiera tokens de acceso mediante el punto de conexión de Azure Active Directory v2.0.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: cee0884ef20ef9cfd63d81d6f223705d34c65ccb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69511782"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Inicio de sesión de usuarios y llamada a Microsoft Graph API desde una aplicación de página única (SPA) de JavaScript

En esta guía se muestra cómo una aplicación de una sola página (SPA) de JavaScript puede iniciar sesión en cuentas personales, profesionales y educativas, obtener un token de acceso y llamar a Microsoft Graph API u otras API que requieran tokens de acceso desde el punto de conexión de la Plataforma de identidad de Microsoft.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funcionamiento de la aplicación de ejemplo generada por esta guía

![Muestra cómo funciona la aplicación de ejemplo generada por este tutorial.](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Más información

La aplicación de ejemplo que se crea con esta guía permite que una aplicación de página única de JavaScript haga consultas a Microsoft Graph API o a una API web que acepte tokens de un punto de conexión de la Plataforma de identidad de Microsoft. En este escenario, después de que un usuario inicia sesión, se solicita un token de acceso y se agrega a las solicitudes HTTP mediante el encabezado de autorización. La adquisición y la renovación de los tokens se realizan por medio de la biblioteca de autenticación de Microsoft (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Bibliotecas

Esta guía utiliza la siguiente biblioteca:

|Biblioteca|DESCRIPCIÓN|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Biblioteca de autenticación de Microsoft para la vista preliminar de JavaScript|

> [!NOTE]
> *msal.js* apunta al *punto de conexión de la Plataforma de identidad de Microsoft*, lo que permite que las cuentas personales, profesionales y educativas inicien sesión y adquieran tokens. El *punto de conexión de la Plataforma de identidad de Microsoft* tiene [algunas limitaciones](azure-ad-endpoint-comparison.md#limitations).
> Para comprender las diferencias entre los puntos de conexión v1.0 y v2.0, lea la [guía de comparación entre puntos de conexión](azure-ad-endpoint-comparison.md).

<!--end-collapse-->

## <a name="set-up-your-web-server-or-project"></a>Configuración de un proyecto o servidor web

> ¿Prefiere descargar este proyecto de ejemplo en su lugar? Realice cualquiera de las siguientes acciones:
> 
> - Para ejecutar el proyecto con un servidor web local, como Node.js, [descargue los archivos del proyecto](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip).
>
> - (Opcional) Parar ejecutar el proyecto con el servidor IIS [descargue el proyecto de Visual Studio](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).
>
> Luego, para configurar el ejemplo de código antes de ejecutarlo, vaya al [paso de configuración](#register-your-application).

## <a name="prerequisites"></a>Requisitos previos

* Para ejecutar este tutorial, necesita un servidor web local, como [Node.js](https://nodejs.org/en/download/), [.NET Core](https://www.microsoft.com/net/core) o la integración de IIS Express con [Visual Studio 2017](https://www.visualstudio.com/downloads/).

* Si usa Node.js para ejecutar el proyecto, instale un entorno de desarrollo integrado (IDE), como [Visual Studio Code](https://code.visualstudio.com/download), para editar los archivos del proyecto.

* Las instrucciones de esta guía se basan tanto en Node.js como en Visual Studio 2017, pero puede usar cualquier otro entorno de desarrollo o servidor web.

## <a name="create-your-project"></a>Creación del proyecto

> ### <a name="option-1-nodejs-or-other-web-servers"></a>Opción 1: Node.js u otros servidores web
> Asegúrese de haber instalado [Node.js](https://nodejs.org/en/download/) y haga lo siguiente:
> - Cree una carpeta para hospedar la aplicación.
>
> ### <a name="option-2-visual-studio"></a>Opción 2: Visual Studio
> Si usa Visual Studio y crea un proyecto, haga lo siguiente:
> 1. Abra Visual Studio, seleccione **Archivo** > **Nuevo** > **Proyecto**.
> 1. En **Visual C#\Web**, seleccione **Aplicación web ASP.NET (.NET Framework)** .
> 1. Escriba un nombre para la aplicación y seleccione **Aceptar**.
> 1. En **Nueva aplicación web ASP.NET**, seleccione **Vacía**.

## <a name="create-the-spa-ui"></a>Creación de la interfaz de usuario de SPA
1. Cree un archivo *index.html* para JavaScript SPA. Si usa Visual Studio, seleccione el proyecto (la carpeta raíz del proyecto), haga clic con el botón derecho y seleccione **Agregar** > **Nuevo elemento** > **Página HTML** y asígnele el nombre *index.html*.

1. En el archivo *index.html*, agregue el código siguiente:

   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>Quickstart for MSAL JS</title>
       <script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js"></script>
       <script src="https://secure.aadcdn.microsoftonline-p.com/lib/1.0.0/js/msal.js"></script>
   </head>
   <body>
       <h2>Welcome to MSAL.js Quickstart</h2><br/>
       <h4 id="WelcomeMessage"></h4>
       <button id="SignIn" onclick="signIn()">Sign In</button><br/><br/>
       <pre id="json"></pre>
       <script>
           //JS code
       </script>
   </body>
   </html>
   ```

   > [!TIP]
   > Puede reemplazar la versión de MSAL.js del script anterior con la versión más reciente en la sección de [versiones de MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases).

## <a name="use-the-microsoft-authentication-library-msal-to-sign-in-the-user"></a>Uso de la biblioteca de autenticación de Microsoft (MSAL) para iniciar la sesión del usuario

1. Agregue el siguiente código al archivo `index.html`, dentro de las etiquetas `<script></script>`:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "Enter_the_Application_Id_here"
            authority: "https://login.microsoftonline.com/Enter_the_Tenant_Info_Here"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };

    var graphConfig = {
        graphMeEndpoint: "https://graph.microsoft.com/v1.0/me"
    };

    // this can be used for login or token request, however in more complex situations
    // this can have diverging options
    var requestObj = {
        scopes: ["user.read"]
    };

    var myMSALObj = new Msal.UserAgentApplication(msalConfig);
    // Register Callbacks for redirect flow
    myMSALObj.handleRedirectCallback(authRedirectCallBack);


    function signIn() {

        myMSALObj.loginPopup(requestObj).then(function (loginResponse) {
            //Login Success
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }).catch(function (error) {
            console.log(error);
        });
    }

    function acquireTokenPopupAndCallMSGraph() {
        //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
        myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
            callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
        }).catch(function (error) {
            console.log(error);
            // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
            // Call acquireTokenPopup(popup window)
            if (requiresInteraction(error.errorCode)) {
                myMSALObj.acquireTokenPopup(requestObj).then(function (tokenResponse) {
                    callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
                }).catch(function (error) {
                    console.log(error);
                });
            }
        });
    }


    function graphAPICallback(data) {
        document.getElementById("json").innerHTML = JSON.stringify(data, null, 2);
    }


    function showWelcomeMessage() {
        var divWelcome = document.getElementById('WelcomeMessage');
        divWelcome.innerHTML = 'Welcome ' + myMSALObj.getAccount().userName + "to Microsoft Graph API";
        var loginbutton = document.getElementById('SignIn');
        loginbutton.innerHTML = 'Sign Out';
        loginbutton.setAttribute('onclick', 'signOut();');
    }


    //This function can be removed if you do not need to support IE
    function acquireTokenRedirectAndCallMSGraph() {
         //Always start with acquireTokenSilent to obtain a token in the signed in user from cache
         myMSALObj.acquireTokenSilent(requestObj).then(function (tokenResponse) {
             callMSGraph(graphConfig.graphMeEndpoint, tokenResponse.accessToken, graphAPICallback);
         }).catch(function (error) {
             console.log(error);
             // Upon acquireTokenSilent failure (due to consent or interaction or login required ONLY)
             // Call acquireTokenRedirect
             if (requiresInteraction(error.errorCode)) {
                 myMSALObj.acquireTokenRedirect(requestObj);
             }
         });
     }


    function authRedirectCallBack(error, response) {
        if (error) {
            console.log(error);
        }
        else {
            if (response.tokenType === "access_token") {
                callMSGraph(graphConfig.graphEndpoint, response.accessToken, graphAPICallback);
            } else {
                console.log("token type is:" + response.tokenType);
            }
        }
    }

    function requiresInteraction(errorCode) {
        if (!errorCode || !errorCode.length) {
            return false;
        }
        return errorCode === "consent_required" ||
            errorCode === "interaction_required" ||
            errorCode === "login_required";
    }

    // Browser check variables
    var ua = window.navigator.userAgent;
    var msie = ua.indexOf('MSIE ');
    var msie11 = ua.indexOf('Trident/');
    var msedge = ua.indexOf('Edge/');
    var isIE = msie > 0 || msie11 > 0;
    var isEdge = msedge > 0;
    //If you support IE, our recommendation is that you sign-in using Redirect APIs
    //If you as a developer are testing using Edge InPrivate mode, please add "isEdge" to the if check
    // can change this to default an experience outside browser use
    var loginType = isIE ? "REDIRECT" : "POPUP";

    if (loginType === 'POPUP') {
        if (myMSALObj.getAccount()) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenPopupAndCallMSGraph();
        }
    }
    else if (loginType === 'REDIRECT') {
        document.getElementById("SignIn").onclick = function () {
            myMSALObj.loginRedirect(requestObj);
        };
        if (myMSALObj.getAccount() && !myMSALObj.isCallback(window.location.hash)) {// avoid duplicate code execution on page load in case of iframe and popup window.
            showWelcomeMessage();
            acquireTokenRedirectAndCallMSGraph();
        }
    } else {
        console.error('Please set a valid login type');
    }
    ```

<!--start-collapse-->
### <a name="more-information"></a>Más información

Cuando un usuario hace clic en el botón **Iniciar sesión** por primera vez, el método `signIn` llama a `loginPopup` para iniciar la sesión del usuario. Este método hace que se abra una ventana emergente con el *punto de conexión de la plataforma de identidad de Microsoft* para pedir y validar las credenciales del usuario. Como resultado de un inicio de sesión correcto, se redirige al usuario a la página *index.html* original y se recibe un token, el que `msal.js` procesa, y se almacena en caché la información que contiene el token. Este token se conoce como el *token de identificador* y contiene información básica sobre el usuario, como su nombre. Si piensa utilizar los datos proporcionados por este token para algún propósito, debe asegurarse de que el servidor backend lo valide para garantizar que el token se emitió a un usuario válido para la aplicación.

La instancia de SPA generada por esta guía llama a `acquireTokenSilent` o a `acquireTokenPopup` para adquirir un *token de acceso* que se usa para consultar la información del perfil de usuario a Microsoft Graph API. Si necesita obtener un ejemplo que valide el token de identificador, eche un vistazo a [esta](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2 "aplicación de ejemplo Github active-directory-javascript-singlepageapp-dotnet-webapi-v2") en GitHub (se usa una ASP.NET WEB API para la validación del token).

#### <a name="getting-a-user-token-interactively"></a>Obtención de un token de usuario interactivamente

Después del inicio de sesión inicial, no desea pedir a los usuarios que se vuelvan a autenticar cada vez que tengan que solicitar un token para acceder a un recurso, por lo que se usará *acquireTokenSilent* la mayor parte del tiempo para adquirir los tokens. Sin embargo, hay situaciones en las que resulta necesario forzar a que los usuarios interactúen con el punto de conexión de la plataforma de identidad de Microsoft. Algunos ejemplos de esto son los siguientes:

- Es posible que los usuarios deban volver a escribir las credenciales porque la contraseña expiró
- La aplicación solicita acceso a un recurso para el cual el usuario necesita consentimiento
- Se requiere la autenticación en dos fases

Al llamar a *acquireTokenPopup*, se abre una ventana emergente (o al llamar a *acquireTokenRedirect*, se redirige a los usuarios al punto de conexión de la plataforma de identidad de Microsoft), donde los usuarios deben confirmar las credenciales, dar su consentimiento al recurso requerido o completar la autenticación en dos fases.

#### <a name="getting-a-user-token-silently"></a>Obtención de un token de usuario en silencio

El método `acquireTokenSilent` controla la renovación y las adquisiciones de tokens sin la interacción del usuario. Después de ejecutar `loginPopup` (o `loginRedirect`) por primera vez, `acquireTokenSilent` es el método usado habitualmente para obtener los tokens empleados para acceder a recursos protegidos en las llamadas posteriores, ya que las llamadas para solicitar o renovar los tokens se realizan en modo silencioso.
`acquireTokenSilent` puede generar errores en algunos casos, por ejemplo, si la contraseña del usuario expiró. La aplicación puede abordar esta excepción de dos maneras:

1. Realizar una llamada a `acquireTokenPopup` inmediatamente, lo que ocasiona que el usuario tenga que iniciar sesión. Este patrón se da comúnmente en aplicaciones en línea en las que no hay ningún contenido no autenticado en la aplicación disponible para el usuario. El ejemplo generado por esta instalación guiada usa este patrón.

2. Las aplicaciones también pueden hacer una indicación visual al usuario de que se requiere un inicio de sesión interactivo, de manera que el usuario pueda seleccionar el momento adecuado para iniciar sesión, o la aplicación puede reintentar `acquireTokenSilent` en un momento posterior. Esto se usa habitualmente cuando el usuario puede utilizar otras funciones de la aplicación sin que se le interrumpa: por ejemplo, hay contenido no autenticado disponible en la aplicación. En este caso, el usuario puede decidir cuando desea iniciar sesión para acceder al recurso protegido o para actualizar la información obsoleta.

> [!NOTE]
> En esta guía de inicio rápido, se usan los métodos `loginRedirect` y `acquireTokenRedirect` cuando el explorador utilizado es Internet Explorer, debido a un [problema conocido](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki/Known-issues-on-IE-and-Edge-Browser#issues) con la manera en que Internet Explorer controla las ventanas emergentes.
<!--end-collapse-->

## <a name="call-the-microsoft-graph-api-using-the-token-you-just-obtained"></a>Llamada a Microsoft Graph API con el token que acaba de obtener

Agregue el siguiente código al archivo `index.html`, dentro de las etiquetas `<script></script>`:

```javascript
function callMSGraph(theUrl, accessToken, callback) {
    var xmlHttp = new XMLHttpRequest();
    xmlHttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200)
            callback(JSON.parse(this.responseText));
    }
    xmlHttp.open("GET", theUrl, true); // true for asynchronous
    xmlHttp.setRequestHeader('Authorization', 'Bearer ' + accessToken);
    xmlHttp.send();
}
```
<!--start-collapse-->

### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>Más información acerca de cómo realizar una llamada de REST a una API protegida

En la aplicación de ejemplo creada en esta guía, el método `callMSGraph()` se usa para realizar una solicitud HTTP `GET` a un recurso protegido que requiere un token y, a continuación, devolver el contenido al autor de la llamada. Este método agrega el token adquirido al *encabezado de autorización HTTP*. En este ejemplo de aplicación creada en esta guía, el recurso es el punto de conexión *me* de Microsoft Graph API, que muestra información del perfil del usuario.

<!--end-collapse-->

## <a name="add-a-method-to-sign-out-the-user"></a>Adición de un método para cerrar la sesión del usuario

Agregue el siguiente código al archivo `index.html`, dentro de las etiquetas `<script></script>`:

```javascript
/**
 * Sign out the user
 */
 function signOut() {
     myMSALObj.logout();
 }
```

## <a name="register-your-application"></a>Registrar su aplicación

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

1. Si la cuenta proporciona acceso a más de un inquilino, selecciónela en la esquina superior derecha y establezca la sesión del portal en el inquilino de Azure AD que desee utilizar.
1. Vaya a la página [Registros de aplicaciones](https://go.microsoft.com/fwlink/?linkid=2083908) de la plataforma de identidad de Microsoft para desarrolladores.
1. Cuando se abra la página **Registrar una aplicación**, escriba el nombre de su aplicación.
1. En **Supported account types** (Tipos de cuenta compatibles), seleccione **Accounts in any organizational directory and personal Microsoft accounts** (Cuentas en cualquier directorio de organización y cuentas personales de Microsoft).
1. En la lista desplegable de la sección **URI de redireccionamiento**, seleccione la plataforma **Web** y, luego, establezca el valor en la dirección URL de la aplicación que se basa en el servidor web. 

   Para información sobre cómo establecer y obtener la dirección URL de redireccionamiento en Visual Studio y Node.js, consulte las dos secciones siguientes.

1. Seleccione **Registrar**.
1. En la página de **información general** de la aplicación, anote el valor del **Identificador de aplicación (cliente)** para su uso posterior.
1. Para esta guía, se requiere que habilite el [flujo de concesión implícita](v2-oauth2-implicit-grant-flow.md). En el panel de navegación izquierdo de la aplicación registrada, seleccione **Autenticación**.
1. En **Configuración avanzada**, en **Concesión implícita**, active las casillas **Tokens de id.** y **Tokens de acceso**. Los tokens de identificador y los tokens de acceso son necesarios, ya que esta aplicación tiene que iniciar la sesión de los usuarios y llamar a una API.
1. Seleccione **Guardar**.

> #### <a name="set-a-redirect-url-for-nodejs"></a>Configuración de una dirección URL de redireccionamiento para Node.js
> Para Node.js, puede establecer el puerto del servidor web en el archivo *server.js*. En este tutorial se usa el puerto 30662 como referencia, pero puede usar cualquier otro puerto que esté disponible. 
>
> Para configurar una dirección URL de redireccionamiento en la información de registro de aplicación, vuelva al panel **Registro de aplicación** y realice una de las acciones siguientes:
>
> - Establezca *`http://localhost:30662/`* como la **dirección URL de redireccionamiento**.
> - Si usa un puerto TCP personalizado, use *`http://localhost:<port>/`* (donde *\<port>* es el número de puerto TCP personalizado).
>
> #### <a name="set-a-redirect-url-for-visual-studio"></a>Configuración de una dirección URL de redireccionamiento para Visual Studio
> Para obtener la dirección URL de redireccionamiento de Visual Studio, haga lo siguiente:
> 1. En el **Explorador de soluciones**, seleccione el proyecto.
>
>    Se abre la ventana **Propiedades**. Si no se abre, presione **F4**.
>
>    ![La ventana Propiedades del proyecto JavaScriptSPA](media/active-directory-develop-guidedsetup-javascriptspa-configure/vs-project-properties-screenshot.png)
>
> 1. Copie el valor **URL**.
 
> 1. Vuelva al panel **Registro de aplicación** y pegue el valor copiado como la **Dirección URL de redireccionamiento**.

#### <a name="configure-your-javascript-spa"></a>Configuración de JavaScript SPA

1. En el archivo *index.html* creado durante la configuración del proyecto, agregue la información del registro de aplicación. En la parte superior del archivo, dentro de las etiquetas `<script></script>`, agregue el código siguiente:

    ```javascript
    var msalConfig = {
        auth: {
            clientId: "<Enter_the_Application_Id_here>",
            authority: "https://login.microsoftonline.com/<Enter_the_Tenant_info_here>"
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    ```

    Donde:
    - *\<Enter_the_Application_Id_here>* es el **identificador de aplicación (cliente)** de la aplicación que ha registrado.
    - *\<Enter_the_Tenant_info_here >* está establecido en una de las opciones siguientes:
       - Si la aplicación admite *solo las cuentas de este directorio organizativo*, reemplace este valor por el **identificador de inquilino** o el **nombre de inquilino** (por ejemplo, *contoso.microsoft.com*).
       - Si la aplicación admite *cuentas en cualquier directorio organizativo*, reemplace este valor por **organizaciones**.
       - Si la aplicación admite *cuentas en cualquier directorio organizativo y cuentas Microsoft personales*, reemplace este valor por **común**. Para restringir la compatibilidad a *Personal Microsoft accounts only* (Solo cuentas Microsoft personales), reemplace este valor por **consumidores**.

## <a name="test-your-code"></a>Prueba del código

Para probar el código, use cualquiera de estos entornos.

### <a name="test-with-nodejs"></a>Prueba con Node.js

Si no está utilizando Visual Studio, asegúrese de que el servidor web está activado.

1. Configure el servidor para que escuche un puerto TCP que esté basado en la ubicación del archivo *index.html*. Para Node.js, inicie el servidor web para que escuche el puerto; para ello, ejecute los siguientes comandos desde la carpeta de la aplicación en un símbolo del sistema de la línea de comandos:

    ```bash
    npm install
    node server.js
    ```
1. En el explorador, escriba **http://\<span>\</span>localhost:30662** o **http://\<span>\</span>localhost:{port}** , donde *port* es el puerto de escucha del servidor web. Verá el contenido del archivo *index.html* y el botón **Iniciar sesión**.

### <a name="test-with-visual-studio"></a>Pruebas con Visual Studio

Si usa Visual Studio, seleccione la solución del proyecto y, luego, F5 para ejecutar el proyecto. El explorador se abre en la ubicación http://<span></span>localhost:{port} y el botón **Iniciar sesión** debería verse.

## <a name="test-your-application"></a>Prueba de la aplicación

Cuando el explorador haya cargado el archivo *index.html*, seleccione **Iniciar sesión**. Se le pedirá que inicie sesión con el punto de conexión de la plataforma de identidad de Microsoft:

![La ventana de inicio de sesión en la cuenta de SPA de JavaScript](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspascreenshot1.png)

### <a name="provide-consent-for-application-access"></a>Consentimiento para el acceso a la aplicación

La primera vez que inicie sesión en la aplicación, se le pedirá que le dé acceso a su perfil e inicie su sesión:

![La ventana "Permisos solicitados"](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptspaconsent.png)

### <a name="view-application-results"></a>Visualización de los resultados de la aplicación

Una vez iniciada la sesión, la información del perfil de usuario se devuelve en la respuesta de Microsoft Graph API que se muestra en la página.

![Resultados de la llamada a Microsoft Graph API](media/active-directory-develop-guidedsetup-javascriptspa-test/javascriptsparesults.png)

<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Más información sobre los ámbitos y permisos delegados

Microsoft Graph API requiere el ámbito *user.read* para leer el perfil del usuario. Este ámbito se agrega automáticamente de forma predeterminada en todas las aplicaciones que se registran en el portal de registro. Otras API de Microsoft Graph, así como las API personalizadas para el servidor back-end, pueden requerir ámbitos adicionales. Por ejemplo, Microsoft Graph API requiere el ámbito *Calendars.Read* para mostrar los calendarios del usuario.

Para tener acceso a los calendarios del usuario en el contexto de una aplicación, agregue el permiso delegado *Calendars.Read* a la información del registro de la aplicación. A continuación, agregue el ámbito *Calendars.Read* a la llamada `acquireTokenSilent`.

>[!NOTE]
>Es posible que se pida al usuario algún consentimiento adicional a medida que aumente el número de ámbitos.

Si una API de back-end no requiere un ámbito (no se recomienda), puede usar *clientId* como ámbito en las llamadas de adquisición de tokens.

<!--end-collapse-->

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

Ayúdenos a mejorar la Plataforma de identidad de Microsoft. Rellene una breve encuesta de dos preguntas y háganos saber su opinión.

> [!div class="nextstepaction"]
> [Encuesta sobre la Plataforma de identidad de Microsoft](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)