---
title: Preguntas más frecuentes (P+F) sobre Azure Active Directory B2C
description: Respuestas a preguntas frecuentes sobre Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c84f68a9af855f61523919069e1947e051b130b4
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622265"
---
# <a name="azure-ad-b2c-frequently-asked-questions-faq"></a>Azure AD B2C: Preguntas más frecuentes

Esta página responde a las preguntas más frecuentes sobre Azure Active Directory (Azure AD) B2C. Siga comprobando si hay actualizaciones.

### <a name="why-cant-i-access-the-azure-ad-b2c-extension-in-the-azure-portal"></a>¿Por qué no puedo acceder a la extensión de Azure AD B2C en Azure Portal?

Hay dos causas comunes de por qué la extensión de Azure AD no funciona en su caso. Azure AD B2C requiere que su rol de usuario en el directorio sea el de administrador global. Póngase en contacto con su administrador si cree que debería tener acceso. Si tiene privilegios de administrador global, asegúrese de que se encuentra en un directorio de Azure AD B2C y no en un directorio de Azure Active Directory. Puede consultar las instrucciones para [crear un inquilino de Azure AD B2C](tutorial-create-tenant.md).

### <a name="can-i-use-azure-ad-b2c-features-in-my-existing-employee-based-azure-ad-tenant"></a>¿Puedo usar las características de Azure AD B2C en mi inquilino de Azure de AD existente, basado en empleados?

Azure AD y Azure AD B2C son ofertas de producto independientes y no pueden coexistir en el mismo inquilino. Un inquilino de Azure AD representa una organización. Un inquilino de Azure AD B2C representa una colección de identidades para su uso con aplicaciones de usuario de confianza. Con las directivas personalizadas (en versión preliminar), Azure AD B2C puede federarse con Azure AD, lo que permite la autenticación de los empleados de una organización.

### <a name="can-i-use-azure-ad-b2c-to-provide-social-login-facebook-and-google-into-office-365"></a>¿Puedo usar Azure AD B2C para proporcionar un inicio de sesión social (Facebook y Google+) en Office 365?

Azure AD B2C no se puede usar para autenticar los usuarios de Microsoft Office 365. Azure AD es la solución de Microsoft para administrar el acceso de los empleados a las aplicaciones SaaS, y tiene características diseñadas para este propósito, como el acceso condicional y las licencias. Azure AD B2C proporciona una plataforma de administración de identidades y acceso para la creación de aplicaciones web y móviles. Cuando Azure AD B2C está configurado para federarse con un inquilino de Azure AD, el inquilino de Azure AD administra el acceso de los empleados a las aplicaciones que se basan en Azure AD B2C.

### <a name="what-are-local-accounts-in-azure-ad-b2c-how-are-they-different-from-work-or-school-accounts-in-azure-ad"></a>¿Qué son las cuentas locales en Azure AD B2C? ¿En qué se distinguen de las cuentas de trabajo o educativas en Azure AD?

En un inquilino de Azure AD, los usuarios que pertenecen al inquilino inician sesión con una dirección de correo electrónico con el formato `<xyz>@<tenant domain>`. `<tenant domain>` es uno de los dominios comprobados del inquilino o el dominio `<...>.onmicrosoft.com` inicial. Este tipo de cuenta es una cuenta profesional o educativa.

En un inquilino de Azure AD B2C, la mayoría de las aplicaciones desean que el usuario inicie sesión con cualquier dirección de correo electrónico arbitraria (por ejemplo, joe@comcast.net, bob@gmail.com, sarah@contoso.com o jim@live.com). Este tipo de cuenta es una cuenta local. También se admiten nombres de usuario arbitrarios tales como cuentas locales (por ejemplo, joe, bob, sarah o jim). Puede elegir uno de estos dos tipos de cuentas locales al configurar proveedores de identidades para Azure AD B2C en Azure Portal. En el inquilino de Azure AD B2C, seleccione **Proveedores de identidades**, **Cuenta local** y **Nombre de usuario**.

Las cuentas de usuario de las aplicaciones siempre se deben crear mediante un flujo de usuario de registro, un flujo de usuario de registro o inicio de sesión, o mediante Azure AD Graph API. Las cuentas de usuario creadas en Azure Portal solo se usan para administrar el inquilino.

### <a name="which-social-identity-providers-do-you-support-now-which-ones-do-you-plan-to-support-in-the-future"></a>¿Qué proveedores de identidades sociales se admiten ahora? ¿Cuáles se prevén que se van a admitir en el futuro?

Actualmente admitimos Facebook, Google+, LinkedIn, Amazon, Twitter (versión preliminar), WeChat (versión preliminar), Weibo (versión preliminar) y QQ (versión preliminar). Agregaremos compatibilidad con otros proveedores de identidades sociales conocidos en función de la demanda del cliente.

Azure AD B2C también ha agregado compatibilidad para [directivas personalizadas](active-directory-b2c-overview-custom.md). Estas directivas personalizadas permiten a un desarrollador crear su propia directiva con cualquier proveedor de identidades que admita [OpenID Connect](https://openid.net/specs/openid-connect-core-1_0.html) o SAML.

Empiece a trabajar con directivas personalizadas consultando nuestro [paquete de inicio de directivas personalizadas](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack).

### <a name="can-i-configure-scopes-to-gather-more-information-about-consumers-from-various-social-identity-providers"></a>¿Puedo configurar ámbitos para recopilar más información acerca de los consumidores de distintos proveedores de identidades sociales?

No. Los ámbitos predeterminados usados para el conjunto de proveedores de identidades sociales admitidos son:

* Facebook: correo electrónico
* Google+: correo electrónico
* Cuenta Microsoft: perfil de correo electrónico de OpenID
* Amazon: perfil
* LinkedIn: r_emailaddress r_basicprofile

### <a name="does-my-application-have-to-be-run-on-azure-for-it-work-with-azure-ad-b2c"></a>¿Tiene mi aplicación que ejecutarse en Azure para que funcione con Azure AD B2C?

No, puede hospedar la aplicación en cualquier lugar (en la nube o de forma local). Todo lo que necesita para interactuar con Azure AD B2C es la capacidad de enviar y recibir solicitudes HTTP en puntos de conexión de acceso público.

### <a name="i-have-multiple-azure-ad-b2c-tenants-how-can-i-manage-them-on-the-azure-portal"></a>Tengo varios inquilinos de Azure AD B2C. ¿Cómo puedo administrarlos en Azure Portal?

Antes de abrir "Azure AD B2C" en el menú de la izquierda de Azure Portal, debe cambiarse al directorio que desea administrar. Cambie los directorios haciendo clic en su identidad en la esquina superior derecha de Azure Portal y después elija un directorio en la lista desplegable que aparece.

### <a name="how-do-i-customize-verification-emails-the-content-and-the-from-field-sent-by-azure-ad-b2c"></a>¿Cómo puedo personalizar los mensajes de correo electrónico de comprobación (el contenido y el campo "De:") enviados por Azure AD B2C?

Puede utilizar la [característica de personalización de marca de compañía](../active-directory/fundamentals/customize-branding.md) para personalizar el contenido de los mensajes de correo electrónico de comprobación. En concreto, se pueden personalizar estos dos elementos del correo electrónico:

* **Logotipo del banner**: se muestra en la esquina inferior derecha.
* **Color de fondo**: se muestra en la parte superior.

    ![Captura de pantalla de un correo electrónico de comprobación personalizado](./media/active-directory-b2c-faqs/company-branded-verification-email.png)

La firma de correo electrónico contiene el nombre del inquilino de Azure AD B2C que proporcionó cuando lo creó por primera vez. Puede cambiar el nombre siguiendo estas instrucciones:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global.
1. Abra la hoja **Azure Active Directory**.
1. Haga clic en la pestaña **Propiedades**.
1. Cambie el campo **Nombre**.
1. Haga clic en **Guardar** en la parte superior de la página.

En estos momentos no se puede cambiar el valor del campo De del correo electrónico.

### <a name="how-can-i-migrate-my-existing-user-names-passwords-and-profiles-from-my-database-to-azure-ad-b2c"></a>¿Cómo puedo migrar mis nombres de usuario, contraseñas y perfiles existentes desde la base de datos a Azure AD B2C?

Puede usar Graph API de Azure AD para escribir la herramienta de migración. Consulte la [guía de migración para el usuario](active-directory-b2c-user-migration.md) para detalles.

### <a name="what-password-user-flow-is-used-for-local-accounts-in-azure-ad-b2c"></a>¿Qué flujo de usuario de contraseñas se usa para las cuentas locales en Azure AD B2C?

El flujo de usuario de Azure AD B2C para cuentas locales se basa en la directiva para Azure AD. Los flujos de usuario de restablecimiento de la contraseña, inicio de sesión, registro e inicio de sesión de Azure AD B2C usan la seguridad de la contraseña "segura" y las contraseñas no caducan. Lea [Directiva de contraseñas en Azure AD](/previous-versions/azure/jj943764(v=azure.100)) para obtener más información. Para información sobre los bloqueos de cuentas y las contraseñas, consulte [Administración de amenazas para recursos y datos de Azure Active Directory B2C](active-directory-b2c-reference-threat-management.md).

### <a name="can-i-use-azure-ad-connect-to-migrate-consumer-identities-that-are-stored-on-my-on-premises-active-directory-to-azure-ad-b2c"></a>¿Puedo usar Azure AD Connect para migrar identidades de consumidores almacenadas en mi entorno Active Directory local a Azure AD B2C?

No, Azure AD Connect no está diseñado para funcionar con Azure AD B2C. Considere la posibilidad de usar [Graph API de Azure AD](active-directory-b2c-devquickstarts-graph-dotnet.md) para la migración de usuarios. Consulte la [guía de migración para el usuario](active-directory-b2c-user-migration.md) para detalles.

### <a name="can-my-app-open-up-azure-ad-b2c-pages-within-an-iframe"></a>¿Mi aplicación puede abrir páginas de Azure AD B2C dentro de un iFrame?

No, por motivos de seguridad, las páginas de Azure AD B2C no se pueden abrir dentro de un iFrame. Nuestro servicio se comunica con el explorador para prohibir iFrames. La comunidad de seguridad en general y la especificación OAUTH2 recomiendan no usar iFrames para experiencias de identidad debido al riesgo de secuestro de clics, también conocido como "clickjacking".

### <a name="does-azure-ad-b2c-work-with-crm-systems-such-as-microsoft-dynamics"></a>¿Funciona Azure AD B2C con sistemas CRM, como Microsoft Dynamics?

La integración básica con el portal de Microsoft Dynamics 365 está disponible. Consulte [Configuración del proveedor Azure AD B2C para portales](https://docs.microsoft.com/dynamics365/customer-engagement/portals/azure-ad-b2c).

### <a name="does-azure-ad-b2c-work-with-sharepoint-on-premises-2016-or-earlier"></a>¿Funciona Azure AD B2C con SharePoint local 2016 o una versión anterior?

Azure AD B2C no se ha diseñado para escenarios de uso compartido con asociados externos de SharePoint; en su lugar, consulte [Azure AD B2B](https://cloudblogs.microsoft.com/enterprisemobility/2015/09/15/learn-all-about-the-azure-ad-b2b-collaboration-preview/).

### <a name="should-i-use-azure-ad-b2c-or-b2b-to-manage-external-identities"></a>¿Debo utilizar Azure AD B2C o B2B para administrar identidades externas?

Lea este artículo sobre [identidades externas](../active-directory/active-directory-b2b-compare-external-identities.md) para obtener más información sobre cómo aplicar las características apropiadas a los escenarios de identidades externas.

### <a name="what-reporting-and-auditing-features-does-azure-ad-b2c-provide-are-they-the-same-as-in-azure-ad-premium"></a>¿Qué características de auditoría e informes proporciona Azure AD B2C? ¿Son las mismas que en Azure AD Premium?

No, Azure AD B2C no admite el mismo conjunto de informes que Azure AD Premium. Sin embargo, hay muchos elementos en común:

* Los **informes de inicio de sesión** proporcionan un registro de cada inicio de sesión con menos detalles.
* Los **informes de auditoría** incluyen tanto la actividad administrativa como la actividad de la aplicación.
* Los **informes de usuario** incluyen el número de usuarios, el número de inicios de sesión y el volumen de MFA.

### <a name="can-i-localize-the-ui-of-pages-served-by-azure-ad-b2c-what-languages-are-supported"></a>¿Puedo localizar la interfaz de usuario de páginas servidas por Azure AD B2C? ¿Qué idiomas se admiten?

Sí.  Obtenga información sobre la [personalización de lenguaje](active-directory-b2c-reference-language-customization.md), que se encuentra en versión preliminar pública. Se ofrecen traducciones a 36 idiomas, y puede invalidar cualquier cadena para satisfacer sus necesidades.

### <a name="can-i-use-my-own-urls-on-my-sign-up-and-sign-in-pages-that-are-served-by-azure-ad-b2c-for-instance-can-i-change-the-url-from-loginmicrosoftonlinecom-to-logincontosocom"></a>¿Puedo usar mis propias direcciones URL en las páginas de registro y de inicio que proporciona Azure AD B2C? Por ejemplo, ¿puedo cambiar las direcciones URL de login.microsoftonline.com a login.contoso.com?

Actualmente, no. Esta característica está en nuestro mapa de ruta. Comprobar el dominio en la pestaña **Dominios** de Azure Portal no logra este objetivo.

### <a name="how-do-i-delete-my-azure-ad-b2c-tenant"></a>¿Cómo puedo eliminar al inquilino de Azure AD B2C?

Siga estos pasos para eliminar al inquilino de Azure AD B2C:

1. Elimine todos los **flujos de usuario (directivas)** del inquilino de Azure AD B2C.
1. Elimine todas las **aplicaciones** que haya registrado en el inquilino de Azure AD B2C.
1. Después, inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador de la suscripción. Use la misma cuenta profesional o educativa o la misma cuenta de Microsoft que ha usado para registrarse en Azure.
1. Cambie al inquilino de Azure AD B2C que desea eliminar.
1. Seleccione **Azure Active Directory** en el menú de la izquierda.
1. En **Administrar**, seleccione **Usuarios**.
1. Seleccione cada usuario de uno en uno (excluya al administrador de suscripciones con el que inició sesión). Seleccione **Eliminar** en la parte inferior de la página y seleccione **SÍ** cuando se le pida confirmación.
1. En **Administrar**, seleccione **Registros de aplicaciones** (o **Registros de aplicaciones (característica heredada)** ).
1. Seleccione **Ver todas las aplicaciones**.
1. Seleccione la aplicación denominada **b2c-extensions-app**, seleccione **Eliminar** y después **Sí** cuando se le pida.
1. En **Administrar**, seleccione **Configuración del usuario**.
1. En **Conexiones de cuenta de LinkedIn**, seleccione **No** y luego **Guardar**.
1. En **Administrar**, seleccione **Propiedades**.
1. En **Administración del acceso para los recursos de Azure**, seleccione **Sí** y luego **Guardar**.
1. Cierre la sesión de Azure Portal y vuelva a iniciar sesión para actualizar el acceso.
1. Seleccione **Azure Active Directory** en el menú de la izquierda.
1. En la página **Información general**, seleccione **Eliminar directorio**. Siga las instrucciones que aparecen en pantalla para completar el proceso.

### <a name="can-i-get-azure-ad-b2c-as-part-of-enterprise-mobility-suite"></a>¿Puedo obtener Azure AD B2C como parte de Enterprise Mobility Suite?

No, Azure AD B2C es un servicio de Azure de pago por uso y no forma parte de Enterprise Mobility Suite.

### <a name="how-do-i-report-issues-with-azure-ad-b2c"></a>¿Cómo puedo informar sobre problemas con Azure AD B2C?

Consulte [Versión preliminar de Azure Active Directory B2C: presentación de solicitudes de soporte técnico](active-directory-b2c-support.md).
