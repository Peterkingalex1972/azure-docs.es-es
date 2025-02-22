---
title: 'Configuración de la autenticación de Azure Active Directory: Azure App Service'
description: Obtenga información acerca de cómo configurar la autenticación de Azure Active Directory para la aplicación de Servicios de aplicaciones
author: mattchenderson
services: app-service
documentationcenter: ''
manager: syntaxc4
editor: ''
ms.assetid: 6ec6a46c-bce4-47aa-b8a3-e133baef22eb
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/20/2019
ms.author: mahender
ms.custom: seodec18
ms.openlocfilehash: 41084c3532e29b3a52c121d48226c5a45857d5dc
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088230"
---
# <a name="configure-your-app-service-app-to-use-azure-active-directory-sign-in"></a>Configuración de una aplicación de App Service para usar la información de inicio de sesión de Azure Active Directory

> [!NOTE]
> En este momento, AAD V2 (incluido MSAL) no se admite para Azure App Services y Azure Functions. Compruebe si hay actualizaciones.
>

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este artículo se muestra cómo configurar Azure App Services para usar Azure Active Directory como proveedor de autenticación.

## <a name="express"> </a>Configuración rápida

1. En [Azure Portal], vaya a su aplicación de App Service. En el panel de navegación izquierdo, seleccione **Autenticación / Autorización**.
2. Si **Autenticación / Autorización** no está habilitado, seleccione **Activar**.
3. Seleccione **Azure Active Directory** y luego **Rápida**, en **Modo de administración**.
4. Seleccione **Aceptar** para registrar la aplicación de App Service en Azure Active Directory. Se crea un nuevo registro de aplicaciones. Si, por el contrario, desea elegir un registro de aplicación existente, haga clic en **Seleccionar una aplicación existente** y busque el nombre de un registro creado de aplicación anteriormente en el inquilino.
   Haga clic en el registro de aplicación para seleccionarlo y en **Aceptar**. A continuación, haga clic en **Aceptar** en la página de configuración de Azure Active Directory.
   De forma predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación.
5. (Opcional) Para restringir el acceso al sitio solo a los usuarios autenticados mediante Azure Active Directory, en **Acción necesaria cuando la solicitud no está autenticada**, seleccione **Iniciar sesión con Azure Active Directory**. Esto requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.

> [!CAUTION]
> Este método de restricción del acceso se aplica a todas las llamadas a la aplicación, lo que puede no ser deseable para las aplicaciones que necesitan una página de inicio disponible públicamente, como muchas aplicaciones de una sola página. Para tales aplicaciones, puede ser preferible **permitir las solicitudes anónimas (sin acción)** y que la aplicación inicie manualmente el inicio de sesión, tal como se describe [aquí](overview-authentication-authorization.md#authentication-flow).

6. Haga clic en **Save**(Guardar).

## <a name="advanced"> </a>Configuración avanzada

Los valores de configuración también se pueden especificar manualmente. Esta es la solución preferida si el inquilino de Azure Active Directory que desea usar no con el que inicia sesión en Azure. Para completar la configuración, primero debe crear un registro en Azure Active Directory y luego proporcionar algunos de los detalles de registro a App Service.

### <a name="register"> </a>Registro de una aplicación de App Service con Azure Active Directory

1. Inicie sesión en [Azure Portal] y vaya a la aplicación App Service. Copie la dirección **URL** de su aplicación. Se usará para configurar el registro de la aplicación de Azure Active Directory.
2. Navegue a **Active Directory**, seleccione **Registros de aplicaciones** y haga clic en **Nuevo registro de aplicaciones** en la parte superior para iniciar un nuevo registro de aplicaciones. 
3. En la página **Crear**, escriba un **nombre** para el registro de su aplicación, seleccione el tipo **Web App / API** y, en el cuadro **Dirección URL de inicio de sesión**, pegue la dirección URL de la aplicación (del paso 1). Luego haga clic en **Crear**.
4. En cuestión de segundos, debería ver aparecer el nuevo registro de aplicación recién creado.
5. Una vez que se agrega el registro de aplicación, haga clic en el nombre del registro de aplicación, en **Configuración** en la parte superior y, luego, en **Propiedades** 
6. En el cuadro **URI del identificador de la aplicación**, pegue la dirección URL de la aplicación (del paso 1) y péguela también en **Dirección URL de la página principal** y, luego, haga clic en **Guardar**
7. Ahora haga clic en las **direcciones URL de respuesta**, edite la **dirección URL de respuesta**, péguela en Dirección URL de la aplicación (del paso 1) y luego anexe */.auth/login/aad/callback* al final de la dirección URL (por ejemplo, `https://contoso.azurewebsites.net/.auth/login/aad/callback`). Haga clic en **Save**(Guardar).

   > [!NOTE]
   > Puede usar el mismo registro de aplicación para varios dominios mediante la adición de más **direcciones URL de respuesta**. Asegúrese de que modela cada instancia de App Service con su propio registro, de forma que tenga sus propios permisos y consentimiento. Considere también la posibilidad de usar registros de aplicaciones para ranuras de sitio independientes. Esto es para evitar que se compartan permisos entre entornos, de forma que si se produce un error en el nuevo código que está probando este no afecte a producción.
    
8. En este punto, copie el **identificador de la aplicación** en cuestión. Consérvelo para usarlo más adelante. Lo necesitará para configurar la aplicación de App Service.
9. Cierre la página **Aplicación registrada**. En la página **Registros de aplicaciones**, haga clic en el botón **Puntos de conexión** de la parte superior y luego copie la dirección URL de **PUNTO DE CONEXIÓN DE INICIO DE SESIÓN DE WS-FEDERATION**, pero quite `/wsfed` al final. El resultado final debería ser así: `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000`. El nombre de dominio puede ser diferente en el caso de una nube soberana. Esto servirá como dirección URL del emisor para su uso posterior.

### <a name="secrets"> </a>Incorporación de información de Azure Active Directory a la aplicación de App Service

1. Vuelva a [Azure Portal] y vaya a su aplicación de App Service. Haga clic en **Autenticación y autorización**. Si esta característica no está habilitada, mueva el interruptor a la posición de **activada**. Haga clic en **Azure Active Directory**, en Proveedores de autenticación, para configurar la aplicación.

    (Opcional) De manera predeterminada, App Service ofrece autenticación pero no restringe el acceso autorizado al contenido del sitio y a las API. Debe autorizar a los usuarios en el código de la aplicación. Establezca **Acción necesaria cuando la solicitud no está autenticada**, en **Iniciar sesión con Azure Active Directory**. Esta opción requiere que todas las solicitudes se autentiquen y que todas las solicitudes no autenticadas se redirijan a Azure Active Directory para la autenticación.
2. En la configuración de autenticación de Active Directory, haga clic en **Avanzado** en el **Modo de administración**. Pegue el identificador de la aplicación en el cuadro Id. de cliente (del paso 8) y pegue la dirección URL (del paso 9) en el valor de URL del emisor. A continuación, haga clic en **Aceptar**.
3. En la página de configuración de la autenticación de Active Directory, haga clic en **Guardar**.

Ahora está preparado para usar Azure Active Directory para realizar la autenticación en la aplicación de App Service.

## <a name="configure-a-native-client-application"></a>Configuración de una aplicación de cliente nativo
Puede registrar a los clientes nativos, lo que proporciona mayor control sobre la asignación de permisos. Esto es necesario si desea realizar inicios de sesión mediante una biblioteca cliente como la **Biblioteca de autenticación de Active Directory**.

1. Vaya a **Active Directory** en [Azure Portal].
2. En el panel de la izquierda, seleccione **Registros de aplicaciones**. Haga clic en **Nuevo registro de aplicación** en la parte superior.
4. En la página **crear**, escriba un **nombre** para el registro de aplicación. Seleccione **Nativo** en **Tipo de aplicación**.
5. En el cuadro **URI de redirección**, especifique el punto de conexión */.auth/login/done* del sitio, con el esquema HTTPS. El valor debería parecerse al siguiente: *https://contoso.azurewebsites.net/.auth/login/done* . Si crea una aplicación de Windows, en su lugar, use el valor de [SID del paquete](../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#package-sid) como URI.
5. Haga clic en **Create**(Crear).
6. Una vez que se ha agregado el registro de aplicación, selecciónelo para abrirlo. Busque el **Identificador de aplicación** y tome nota de este valor.
7. Haga clic en **Toda la configuración** > **Permisos necesarios** > **Agregar** > **Seleccionar una API**.
8. Escriba el nombre de la aplicación de App Service que registró anteriormente para buscar en ella, selecciónela y haga clic en **Seleccionar**.
9. Seleccione **Acceso a \<app_name>** . Después, haga clic en **Seleccionar**. A continuación, haga clic en **Hecho**.

Ahora ha configurado una aplicación cliente nativa que puede acceder a la aplicación de App Service.

## <a name="related-content"></a>Contenido relacionado

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]

<!-- Images. -->

[0]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-url.png
[1]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app_registration.png
[2]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-create.png
[3]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-appidurl.png
[4]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-app-registration-config-replyurl.png
[5]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints.png
[6]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-aad-endpoints-fedmetadataxml.png
[7]: ./media/app-service-mobile-how-to-configure-active-directory-authentication/app-service-webapp-auth.png
[8]: ./media/configure-authentication-provider-aad/app-service-webapp-auth-config.png



<!-- URLs. -->

[Azure Portal]: https://portal.azure.com/
[alternative method]:#advanced
