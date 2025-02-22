---
title: Cómo elegir el tipo de aplicación que se debe usar al agregar una aplicación | Microsoft Docs
description: Describir los tipos admitidos de aplicaciones que se pueden integrar con Azure AD y sus opciones de configuración relacionadas
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/08/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: fb2c49d6436a14e9b6cbb0a92eb0dfba077c8e4d
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68424259"
---
# <a name="choosing-the-application-type-when-adding-an-application-in-azure-active-directory"></a>Elección del tipo de aplicación al agregar una aplicación a Azure Active Directory

Obtenga información sobre los cuatro tipos de aplicaciones que puede agregar a Azure Active Directory (Azure AD). Al agregar una aplicación en Azure Active Directory, se le pedirá que elija uno de los cuatro tipos de aplicación.

## <a name="what-are-the-types-of-applications"></a>¿Cuáles son los tipos de aplicaciones?

Azure AD admite cuatro tipos de aplicaciones principales que puede agregar mediante la característica **Agregar** que se encuentra en **Aplicaciones empresariales**. Entre ellas se incluyen las siguientes:

- **Aplicaciones de la Galería de Azure AD**: una aplicación que se ha integrado previamente para el inicio de sesión único con Azure AD.

- **Aplicaciones de proxy de aplicación**: una aplicación que se ejecuta en su entorno local en la que quiere que se proporcione inicio de sesión seguro externamente.

- **Aplicaciones personalizadas**: una aplicación que su organización desea desarrollar en la plataforma de desarrollo de aplicaciones de Azure AD, pero que no existe aún.

- **Aplicaciones que no son de la Galería**: ¡traiga sus propias aplicaciones! Cualquier vínculo web que quiera, o cualquier aplicación que represente un campo de nombre de usuario y contraseña, admita los protocolos SAML u OpenID Connect, o admita SCIM, que quiera integrar para el inicio de sesión único con Azure AD.

## <a name="features-and-capabilities-supported-by-the-application-types"></a>Características y funcionalidades admitidas por los tipos de aplicaciones

Las siguientes características se admiten en cualquiera de los 4 tipos de aplicaciones anteriores en Azure AD:

- **Inicio rápido**: comience a trabajar con una aplicación rápidamente siguiendo [sencillos pasos de implementación](https://docs.microsoft.com/azure/active-directory/active-directory-integrating-applications-getting-started).

- **Administración de propiedades generales**: obtenga un [vínculo profundo directo](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) a una aplicación, [personalice la marca](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-change-app-logo-user-azure-portal) de una aplicación o [deshabilite la aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-disable-app-azure-portal) para todos los usuarios.

- **Administración de grupos y usuarios**: [asigne](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal) o [quite](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-remove-assignment-azure-portal) usuarios y grupos en una aplicación y, opcionalmente, asigne los roles de aplicación específicos a los que estos usuarios y grupos tienen acceso.

- **Acceso de autoservicio a las aplicaciones**: permita que los usuarios soliciten [acceso de autoservicio a las aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) para una aplicación desde sus paneles de acceso de aplicaciones, bien agregando una aplicación directamente o [mediante la unión a un grupo habilitado para autoservicio](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management). Opcionalmente, es posible que durante el proceso se exija aprobación empresarial.

- **Registros de inicio de sesión**: consulte [todos los inicios de sesión en una aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) o en todas las aplicaciones.

- **Registros de auditoría**: consulte [registros de auditoría detallados sobre las modificaciones realizadas en una aplicación](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs) o en todas las aplicaciones.

- **Acceso condicional y basado en riesgo**: establezca [reglas de acceso basado en condiciones](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access) que sean eficaces, que se exijan cuando los usuarios intenten iniciar sesión en una ubicación específica.

- **Vista de permisos**: vea cualquiera de los [permisos de OAuth2](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent) a los que una aplicación tiene acceso desde un único lugar.

## <a name="single-sign-on-and-provisioning-modes-supported-by-specific-application-types"></a>Modos de inicio de sesión único y de aprovisionamiento admitidos por tipos de aplicaciones específicas

En la tabla siguiente se describen los diferentes modos de inicio de sesión único y aprovisionamiento que admite cada uno de los tipos de aplicaciones anteriores. Puede usar esta tabla para ayudarle a comprender qué aplicación debe agregar para admitir un objetivo específico.

  ![Tabla: Diferentes modos de SSO y aprovisionamiento admitidos por cada tipo de aplicación](./media/choose-application-type/table1.png)

## <a name="how-to-choose-a-single-sign-on-mode"></a>Cómo elegir un modo de inicio de sesión único

A continuación, se enumeran los modos de **inicio de sesión único** admitidos para aplicaciones de Azure AD.

- **Se desactivó el inicio de sesión único de Azure AD.** : elija el **modo de inicio de sesión único** Se desactivó el inicio de sesión único de Azure AD, si aún no está preparado para integrar esta aplicación con el inicio de sesión único de Azure AD, o simplemente la está probando.

- **Inicio de sesión vinculado**: elija el **modo de inicio de sesión**[Inicio de sesión vinculado](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) si tiene una aplicación que ya está conectada con una solución de inicio de sesión único existente, o si solo desea publicar un simple vínculo para sus usuarios en su [Panel de acceso a las aplicaciones](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) o en su [selector de aplicaciones de Office 365](https://login.microsoftonline.com/common/oauth2/authorize?response_mode=form_post&response_type=id_token&scope=openid&nonce=d508a995-f6d6-4b8a-81b8-825c71f1be46.636253878097046923&state=https%3a%2f%2fsupport.office.com%2farticle%2fMeet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a%3fui%3den-US%26rs%3den-US%26ad%3dUS&client_id=4b233688-031c-404b-9a80-a4f3f2351f90&redirect_uri=https%3a%2f%2fsupport.office.com%2fauth%2fsignin&login_hint=asteen%40microsoft.com&prompt=none).

- **Inicio de sesión basado en contraseña**: elija el **modo de inicio de sesión único**[Inicio de sesión con contraseña](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) si su aplicación representa un campo de nombre de usuario y contraseña HTML y quiere almacenar ese nombre de usuario y contraseña de forma segura para reproducirlos en la aplicación más adelante.

- **Inicio de sesión basado en SAML**: elija el modo de inicio de sesión único [Inicio de sesión basado en SAML](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) si su aplicación admite los protocolos SAML o OpenID Connect, o si desea poder asignar usuarios a roles de aplicación específicos en función de reglas definidas en sus notificaciones SAML*.

  >[!NOTE]
  >Esta opción no está disponible cuando se configura el proxy de aplicación para una aplicación.

- **Inicio de sesión basado en el encabezado**: elija el modo de inicio de sesión único [Inicio de sesión basado en el encabezado](application-proxy-configure-single-sign-on-with-ping-access.md) si tiene una aplicación que usa PingAccess que admite la autenticación basada en encabezados HTTP y en la que quiere realizar el inicio de sesión único.

  >[!NOTE]
  >Esta opción solo está disponible cuando se configuran el proxy de aplicación y PingAccess para una aplicación.

- **Autenticación de Windows integrada**: elija el modo de inicio de sesión único [Autenticación de Windows integrada](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd) al exponer una aplicación WIA local en la que quiere realizar el inicio de sesión único.

  >[!NOTE]
  >Esta opción solo está disponible cuando se configura el proxy de aplicación para una aplicación.

## <a name="single-sign-on-modes-for-custom-developed-applications"></a>Modos de inicio de sesión únicos para aplicaciones personalizadas

Las aplicaciones que ha desarrollado de forma personalizada mediante la experiencia de aplicación de desarrollo personalizado también admiten modos de inicio de sesión único adicionales que no se indican en la lista anterior, entre los que se incluyen:

- Inicio de sesión basado en [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code)

- Inicio de sesión basado en [OpenID Connect 1.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code)

- Inicio de sesión basado en [WS-Federation 1.2](https://docs.oasis-open.org/wsfed/federation/v1.2/os/ws-federation-1.2-spec-os.html)

- Inicio de sesión basado en [SAML 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-protocol-reference)

Lea la [guía del desarrollador de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide) para aprender a crear una aplicación de desarrollo personalizado que admita estos modos de inicio de sesión único.

## <a name="how-to-set-an-applications-single-sign-on-mode"></a>Cómo establecer el modo de inicio de sesión único de una aplicación

Para establecer el modo de inicio de sesión único de una aplicación, siga estas instrucciones:

1. Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador.**
1. Abra la **extensión de Azure Active Directory** haciendo clic en **Todos los servicios** en la parte superior del menú de navegación izquierdo principal.
1. Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.
1. Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.
1. Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

1. Seleccione la aplicación para la que desea configurar el inicio de sesión único.
1. Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

## <a name="how-to-choose-a-provisioning-mode"></a>Cómo elegir un modo de aprovisionamiento

- **Aprovisionamiento manual**: elija el modo de aprovisionamiento [Manual](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#provisioning-modes) si dispone de cuentas existentes o desea administrar cuentas para esta aplicación fuera de Azure AD.

- **Aprovisionamiento automático**: elija el **modo de aprovisionamiento**[Automático](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning#configuring-automatic-user-account-provisioning) si quiere habilitar el aprovisionamiento automático basado en API o el desaprovisionamiento de las cuentas de usuario para esta aplicación. 

  >[!NOTE]
  >Esta opción solo está disponible para aplicaciones dentro de la categoría **destacada** de la [Galería de aplicaciones de Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal).

- **Aprovisionamiento automático basado en SCIM**: use [Aprovisionamiento automático basado en SCIM](https://docs.microsoft.com/azure/active-directory/active-directory-scim-provisioning) si su aplicación admite el protocolo SCIM para detectar los cambios en usuarios y grupos. Los cambios realizados en cualquier aplicación integrada con Azure AD se emiten automáticamente. 

  >[!NOTE]
  >Esta opción no aparece como un modo de aprovisionamiento específico, pero está habilitada de forma predeterminada para todas las aplicaciones que se integran con Azure AD.

## <a name="how-to-set-an-applications-provisioning-mode"></a>Cómo configurar un modo de aprovisionamiento de la aplicación

Para establecer el modo de **aprovisionamiento** de una aplicación, siga estas instrucciones:

1. Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global** o **coadministrador.**
1. Abra la **extensión de Azure Active Directory** haciendo clic en **Todos los servicios** en la parte superior del menú de navegación izquierdo principal.
1. Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.
1. Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.
1. Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

1. Seleccione la aplicación para la que desea configurar el aprovisionamiento.
1. Cuando se cargue la aplicación, haga clic en **Aprovisionamiento** desde el menú de navegación izquierdo de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

[Administración de aplicaciones con Azure Active Directory](what-is-application-management.md)
