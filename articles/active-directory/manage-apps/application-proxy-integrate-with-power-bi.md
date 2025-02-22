---
title: Habilitación del acceso remoto a Power BI con Azure AD Application Proxy | Microsoft Docs
description: En este artículo se explican los conceptos básicos sobre cómo integrar una instancia de Power BI local con Azure AD Application Proxy.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9073e00f5c3702e43665541bd8ff9e66c2bc505b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68968399"
---
# <a name="enable-remote-access-to-power-bi-mobile-with-azure-ad-application-proxy"></a>Habilitación del acceso remoto a Power BI Mobile con Azure AD Application Proxy

En este artículo se describe cómo usar Azure AD Application Proxy para habilitar la conexión de la aplicación Power BI Mobile a Power BI Report Server (PBIRS) y SQL Server Reporting Services (SSRS) 2016 y versiones posteriores. A través de esta integración, los usuarios que están fuera de la red corporativa pueden acceder a sus informes de Power BI desde la aplicación Power BI Mobile y estar protegidos mediante la autenticación de Azure AD. Esta protección incluye [ventajas de seguridad](application-proxy-security.md#security-benefits) como el acceso condicional y la autenticación multifactor.  

## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por hecho que ya se han implementado los servicios de informes y se ha  [habilitado Application Proxy](application-proxy-add-on-premises-application.md).

- La habilitación de Application Proxy requiere instalar un conector en un servidor de Windows y cumplir los [requisitos previos](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) para que el conector pueda comunicarse con los servicios de Azure AD.  
- Al publicar Power BI, se recomienda usar los mismos dominios internos y externos. Para más información sobre los dominios personalizados, consulte el artículo [Uso de dominios personalizados en el proxy de la aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain).
- Esta integración está disponible para la aplicación **Power BI Mobile para iOS y Android**.

## <a name="step-1-configure-kerberos-constrained-delegation-kcd"></a>Paso 1: Configuración de la delegación restringida de Kerberos (KCD)

Para las aplicaciones locales que usan la autenticación de Windows, puede realizar el inicio de sesión único (SSO) mediante el protocolo de autenticación Kerberos y una característica denominada delegación restringida de Kerberos (KCD). Cuando está configurada, la KCD permite que el conector de Application Proxy obtenga un token de Windows para un usuario, incluso aunque el usuario no haya iniciado sesión en Windows directamente. Para obtener más información sobre KCD, consulte  [Información general sobre la delegación restringida de Kerberos](https://technet.microsoft.com/library/jj553400.aspx) y [Delegación restringida de Kerberos para el inicio de sesión único para las aplicaciones con Proxy de aplicación](application-proxy-configure-single-sign-on-with-kcd.md).

En lo que respecta a Reporting Services, no hay mucho que configurar. Simplemente asegúrese de tener un nombre de entidad de seguridad de servicio (SPN) válido para permitir que se produzca la autenticación Kerberos adecuada. Asegúrese también de que el servidor de Reporting Services esté habilitado para la autenticación de negociación.

Para configurar KCD para Reporting Services, continúe con los pasos siguientes.

### <a name="configure-the-service-principal-name-spn"></a>Configuración del nombre de entidad de seguridad de servicio (SPN)

El SPN es un identificador único para un servicio que usa la autenticación Kerberos. Deberá asegurarse de tener un SPN HTTP adecuado presente para el servidor de informes. Para obtener información sobre cómo configurar el nombre de entidad de seguridad de servicio (SPN) adecuado para el servidor de informes, consulte  [Registrar un nombre principal de servicio (SPN) para un servidor de informes](https://msdn.microsoft.com/library/cc281382.aspx).
Puede comprobar que el SPN se agregó mediante la ejecución del comando Setspn con la opción -l. Para obtener más información sobre este comando, vea  [Setspn](https://social.technet.microsoft.com/wiki/contents/articles/717.service-principal-names-spn-setspn-syntax.aspx).

### <a name="enable-negotiate-authentication"></a>Habilitación de la autenticación de negociación

Para habilitar el uso de la autenticación Kerberos por parte de un servidor de informes, configure el tipo de autenticación del servidor de informes para que sea RSWindowsNegotiate. Configure esta opción mediante el archivo rsreportserver.config.

```xml
<AuthenticationTypes>
    <RSWindowsNegotiate />
    <RSWindowsKerberos />
    <RSWindowsNTLM />
</AuthenticationTypes>
```

Para obtener más información, consulte [Modificar un archivo de configuración de Reporting Services (RSreportserver.config)](https://msdn.microsoft.com/library/bb630448.aspx) y [Configurar la autenticación de Windows en el servidor de informes](https://msdn.microsoft.com/library/cc281253.aspx).

### <a name="ensure-the-connector-is-trusted-for-delegation-to-the-spn-added-to-the-reporting-services-application-pool-account"></a>Asegúrese de que el conector es de confianza para delegación en el SPN que se agrega a la cuenta del grupo de aplicaciones Reporting Services
Configure KCD para que el servicio de Azure AD Application Proxy pueda delegar las identidades de usuario en la cuenta de agrupación de la aplicación Reporting Services. Para configurar KCD, habilite el conector del proxy de aplicación para recuperar los vales Kerberos para los usuarios que se autenticaron en Azure AD. A continuación, ese servidor pasa el contexto a la aplicación de destino, o a Reporting Services en este caso.

Para configurar KCD, repita los pasos siguientes para cada equipo de conexión:

1. Inicie sesión como administrador de dominio en un controlador de dominio y, después, abra  **Usuarios y equipos de Active Directory**.
2. Busque el equipo en el que se ejecuta el conector.  
3. Haga doble clic en el equipo y seleccione la pestaña  **Delegación** .
4. Establezca la configuración de delegación en **Confiar en este equipo para la delegación solo en los servicios especificados**. Después, seleccione  **Usar cualquier protocolo de autenticación**.
5. Seleccione **Agregar** y, luego, **Usuarios o equipos**.
6. Escriba la cuenta de servicio que está usando para Reporting Services. Se trata de la cuenta a la que agregó el SPN en la configuración de Reporting Services.
7. Haga clic en  **Aceptar**. Para guardar los cambios, haga clic en  **Aceptar**  de nuevo.

Para obtener más información, consulte [Delegación restringida de Kerberos para el inicio de sesión único para las aplicaciones con Proxy de aplicación](application-proxy-configure-single-sign-on-with-kcd.md).

## <a name="step-2-publish-report-services-through-azure-ad-application-proxy"></a>Paso 2: Publicación de servicios de informes a través de Azure AD Application Proxy

Ahora ya está a punto para configurar Azure AD Application Proxy.

1. Publique los servicios de informes a través de Application Proxy con la configuración siguiente. Para obtener instrucciones paso a paso sobre cómo publicar una aplicación a través de Application Proxy, consulte el artículo sobre la  [publicación de aplicaciones con Azure AD Application Proxy](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad).
   - **Dirección URL interna**: Escriba la dirección URL del servidor de informes al cual el conector puede acceder en la red corporativa. Asegúrese de que esta dirección URL sea accesible desde el servidor en el que está instalado el conector. Un procedimiento recomendado es el uso de un dominio de nivel superior como `https://servername/` para evitar problemas con subrutas (por ejemplo, `https://servername/reports/` y `https://servername/reportserver/`) no publicadas a través de Application Proxy.
     > [!NOTE]
     > Se recomienda usar una conexión HTTPS segura con el servidor de informes. Consulte [Configuración de conexiones SSL en un servidor de informes en modo nativo](https://docs.microsoft.com/sql/reporting-services/security/configure-ssl-connections-on-a-native-mode-report-server?view=sql-server-2017) para obtener información sobre cómo hacerlo.
   - **Dirección URL externa**: escriba la dirección URL pública a la que se conectará la aplicación Power BI Mobile. Por ejemplo, puede ser similar a `https://reports.contoso.com` si se utiliza un dominio personalizado. Para usar un dominio personalizado, cargue un certificado para el dominio y apunte un registro DNS al dominio msappproxy.net predeterminado de la aplicación. Para obtener los pasos detallados, consulte [Uso de dominios personalizados en Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

   - **Método de autenticación previa**: Azure Active Directory

2. Una vez publicada la aplicación, establezca la configuración de inicio de sesión único según los pasos siguientes:

   a. En la página de la aplicación en el portal, seleccione  **Inicio de sesión único**.

   b. Para el **modo de inicio de sesión único**, seleccione  **Autenticación de Windows integrada**.

   c. Establezca el **SPN de la aplicación interno** en el valor establecido anteriormente.  

   d. Elija la **Identidad de inicio de sesión delegada**  que va a usar el conector en nombre de los usuarios. Para más información, vea  [Trabajar con identidades en la nube y locales diferentes](application-proxy-configure-single-sign-on-with-kcd.md#working-with-different-on-premises-and-cloud-identities).

   e. Haga clic en **Guardar** para guardar los cambios.

Para finalizar la configuración de la aplicación, vaya a la sección **Usuarios y grupos**  y asigne usuarios para que accedan a esta aplicación.

## <a name="step-3-register-the-native-app-and-grant-access-to-the-api"></a>Paso 3: Registrar la aplicación nativa y conceder acceso a la API

Las aplicaciones nativas son programas desarrollados para usarlos en un dispositivo o una plataforma. Para que la aplicación Power BI Mobile pueda conectarse y acceder a una API, debe registrarla en Azure AD.  

1. Registre la aplicación en Azure AD siguiendo el [paso 2 de Habilitación de las aplicaciones cliente nativas para interactuar con el proxy de aplicaciones](application-proxy-configure-native-client-application.md#step-2-register-your-native-application).

   Al registrar la aplicación para la versión de Power BI Mobile para **iOS**, agregue los siguientes URI de redireccionamiento:
   - `msauth://code/mspbi-adal%3a%2f%2fcom.microsoft.powerbimobile`
   - `msauth://code/mspbi-adalms%3a%2f%2fcom.microsoft.powerbimobilems`
   - `mspbi-adal://com.microsoft.powerbimobile`
   - `mspbi-adalms://com.microsoft.powerbimobilems`
   
   Al registrar la aplicación para la versión de Power BI Mobile para **Android**, agregue los siguientes URI de redireccionamiento:
   - `urn:ietf:wg:oauth:2.0:oob`

   > [!IMPORTANT]
   > Se deben agregar los URI de redirección para que la aplicación funcione correctamente.

Ahora que ha registrado su aplicación nativa, puede dar acceso a otras de su directorio, en este caso, para tener acceso a los servicios de informes publicados a través de Application Proxy. Siga los pasos descritos en el [paso 3, relativo a la concesión de acceso a la aplicación de proxy](application-proxy-configure-native-client-application.md#step-3-grant-access-to-your-proxy-application).

## <a name="step-4-connect-from-the-power-bi-mobile-app"></a>Paso 4: Conexión desde la aplicación Power BI Mobile

1. En la aplicación Power BI Mobile, conéctese a la instancia de Reporting Services. Para ello, escriba la  **dirección URL externa**  de la aplicación publicada a través de Application Proxy.

   ![Aplicación Power BI Mobile con dirección URL externa](media/application-proxy-integrate-with-power-bi/app-proxy-power-bi-mobile-app.png)

2. Seleccione **Conectar**. Se le dirigirá a la página de inicio de sesión de Azure Active Directory.

3. Escriba las credenciales válidas para el usuario y seleccione  **Iniciar sesión**. Verá los elementos del servidor de Reporting Services.

## <a name="step-5-configure-intune-policy-for-managed-devices-optional"></a>Paso 5: Configuración de la directiva de Intune para dispositivos administrados (opcional)

> [!NOTE]
> Esta funcionalidad no estará disponible hasta el 31 de julio de 2019.

Puede usar Microsoft Intune para administrar las aplicaciones cliente que utiliza el personal de su empresa. Intune permite usar funcionalidades como el cifrado de datos y requisitos de acceso adicionales. Para obtener más información sobre la administración de aplicaciones a través de Intune, consulte Administración de aplicaciones de Intune. Para habilitar la aplicación Power BI Mobile para que funcione con la directiva de Intune, siga estos pasos.

1. Vaya a **Azure Active Directory**  y a  **Registros de aplicaciones**.
2. Seleccione la aplicación configurada en el paso 3 al registrar la aplicación cliente nativa.
3. En la página de la aplicación, seleccione **Permisos de API**.
4. Haga clic en **Agregar un permiso**. 
5. En **API que mi organización usa**, busque la opción "Administración de aplicaciones móviles de Microsoft" y selecciónela.
6. Adición del permiso **DeviceManagementManagedApps.ReadWrite** a la aplicación
7. Haga clic en **Conceder consentimiento de administrador** para conceder el permiso de acceso a la aplicación.
8. Configure la directiva de Intune que quiera consultando el [procedimiento de creación y asignación de directivas de protección de aplicaciones](https://docs.microsoft.com/intune/app-protection-policies).

## <a name="troubleshooting"></a>solución de problemas

Si la aplicación devuelve una página de error después de intentar cargar un informe durante varios minutos, puede que tenga que cambiar la configuración del tiempo de espera. De forma predeterminada, Application Proxy admite aplicaciones que tardan un máximo de 85 segundos en responder a una solicitud. Para aumentar este valor a 180 segundos, seleccione el tiempo de espera de back-end como **Largo** en la página de configuración de Application Proxy para la aplicación. Para obtener sugerencias sobre procedimientos para crear informes rápidos y de confianza, consulte los [procedimientos recomendados para informes de Power BI](https://docs.microsoft.com/power-bi/power-bi-reports-performance).

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de las aplicaciones de cliente nativas para interactuar con el proxy de aplicaciones](application-proxy-configure-native-client-application.md)
- [Visualización de informes y KPI del servidor de informes local en las aplicaciones Power BI Mobile](https://docs.microsoft.com/power-bi/consumer/mobile/mobile-app-ssrs-kpis-mobile-on-premises-reports)
