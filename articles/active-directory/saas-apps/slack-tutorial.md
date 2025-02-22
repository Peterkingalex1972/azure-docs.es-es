---
title: 'Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Slack | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 26e15f704fc9604bd18a1f4848e84065fc507314
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563115"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Tutorial: Integración del inicio de sesión único (SSO) de Azure Active Directory con Slack

En este tutorial, obtendrá información sobre cómo integrar Slack con Azure Active Directory (Azure AD). Al integrar Slack con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Slack.
* Permitir que los usuarios inicien sesión automáticamente en Slack con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en Slack.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Slack admite el inicio de sesión único iniciado por **SP**.
* Slack admite el aprovisionamiento de usuarios **Just-In-Time**
* Slack admite el [aprovisionamiento **automático** de usuarios](https://docs.microsoft.com/en-gb/azure/active-directory/saas-apps/slack-provisioning-tutorial)

## <a name="adding-slack-from-the-gallery"></a>Adición de Slack desde la galería

Para configurar la integración de Slack en Azure AD, tendrá que agregar Slack desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Slack** en el cuadro de búsqueda.
1. Seleccione **Slack** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Configuración y prueba del inicio de sesión único de Azure AD para Slack

Configure y pruebe el inicio de sesión único de Azure AD con Slack mediante un usuario de prueba llamado **B. Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Slack.

Para configurar y probar el inicio de sesión único de Azure AD con Slack, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
    1. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B.Simon.
    1. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
1. **[Configuración del inicio de sesión único en Slack](#configure-slack-sso)** , para configurar los valores de Inicio de sesión único en la aplicación.
    1. **[Creación de un usuario de prueba de Slack](#create-slack-test-user)** , para tener un homólogo de B. Simon en Slack que esté vinculado a su representación en Azure AD.
1. **[Prueba del inicio de sesión único](#test-sso)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de aplicaciones de **Slack**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, elija **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, especifique los valores de los siguientes campos:

    a. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://<companyname>.slack.com`

    b. En el cuadro de texto **Identificador (Id. de entidad)** , escriba una dirección URL: `https://slack.com`

    > [!NOTE]
    > El valor de la dirección URL de inicio de sesión no es real. Actualice el valor con la dirección URL de inicio de sesión real. Póngase en contacto con el [equipo de soporte técnico de Slack](https://slack.com/help/contact) para obtener el valor. También puede hacer referencia a los patrones que se muestran en la sección **Configuración básica de SAML** de Azure Portal.

1. La aplicación Slack espera las aserciones de SAML en un formato específico. Configure las siguientes notificaciones para esta aplicación. Puede administrar los valores de estos atributos en la sección **Atributos de usuario** de la página de integración de aplicaciones. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el botón **Editar** para abrir el cuadro de diálogo **Atributos de usuario**.

    ![imagen](common/edit-attribute.png)

    > [!NOTE]
    > Si tiene usuarios cuya **dirección de correo electrónico** asignada no está en una licencia de Office365, la notificación **User.Email** no aparecerá en el token de SAML. En estos casos, se recomienda usar en su lugar **user.userprincipalname** como valor del atributo **User.Email** para asignar como **identificador único**.

1. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, configure el atributo Token SAML como muestra la imagen anterior y realice los siguientes pasos:

    | NOMBRE | Atributo de origen |
    | --- | --- |
    | first_name | user.givenname |
    | last_name | user.surname |
    | User.Email | user.mail |
    | User.Username | user.userprincipalname |

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Deje **Espacio de nombres** en blanco.

    d. Seleccione **Atributo** como origen.

    e. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    f. Haga clic en **Aceptar**.

    g. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **Certificado (Base64)** y seleccione **Descargar** para descargarlo y guardarlo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

1. En la sección **Configurar Slack**, copie las direcciones URL adecuadas según sus necesidades.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B.Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B.Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Create**(Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B. Simon acceda a Slack mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Slack**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon** de la lista de usuarios y haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

## <a name="configure-slack-sso"></a>Configuración del inicio de sesión único en Slack

1. En otra ventana del explorador web, inicie sesión en su sitio de la compañía de Slack como administrador.

2. Vaya a **Microsoft Azure AD** y luego a **Configuración del equipo**.

     ![Configuración del inicio de sesión único en App Side](./media/slack-tutorial/tutorial_slack_001.png)

3. En la sección **Configuración del equipo**, haga clic en la pestaña **Autenticación** y luego en **Cambiar configuración**.

    ![Configuración del inicio de sesión único en App Side](./media/slack-tutorial/tutorial_slack_002.png)

4. En el cuadro de diálogo **Configuración de la autenticación SAML** , realice los pasos siguientes:

    ![Configuración del inicio de sesión único en App Side](./media/slack-tutorial/tutorial_slack_003.png)

    a.  En el cuadro de texto **SAML 2.0 Endpoint (HTTP)** [Punto de conexión SAML 2.0 (HTTP)], pegue el valor de la **dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    b.  En el cuadro de texto **Emisor de proveedor de identidades**, pegue el valor de **Identificador Azure AD** que ha copiado de Azure Portal.

    c.  Abra el archivo de certificado descargado en el Bloc de notas, copie el contenido del mismo en el Portapapeles y luego péguelo en el cuadro de texto **Certificado público**.

    d. Configure las tres opciones anteriores según corresponda para su equipo de Slack. Para obtener más información sobre la configuración, busque la **Guía de configuración de SSO de Slack** aquí. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Haga clic en **Guardar configuración**.

### <a name="create-slack-test-user"></a>Creación de un usuario de prueba de Slack

El objetivo de esta sección es crear un usuario de prueba llamado Britta Simon en Slack. Slack admite el aprovisionamiento Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Al intentar acceder a Slack, se crea un nuevo usuario, en caso de que no exista. Slack también admite el aprovisionamiento automático de usuarios. [Aquí](slack-provisioning-tutorial.md) puede encontrar más detalles sobre cómo configurar el aprovisionamiento automático de usuarios.

> [!NOTE]
> Si necesita crear manualmente un usuario, es preciso que se ponga en contacto con el [equipo de soporte técnico de Slack](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect es la herramienta de sincronización que puede sincronizar usuarios locales de Active Directory Identity con Azure AD y estos usuarios sincronizados también podrán usar las aplicaciones como otros usuarios en la nube.

## <a name="test-sso"></a>Prueba de SSO

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Slack en el panel de acceso y debería iniciar sesión automáticamente en la versión de Slack para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales acerca de cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a las aplicaciones y el inicio de sesión único con Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Pruebe Slack con Azure AD](https://aad.portal.azure.com/)