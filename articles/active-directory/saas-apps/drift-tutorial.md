---
title: 'Tutorial: Integración de Azure Active Directory con Drift | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Drift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 39dcbb95-c192-448c-86a1-cedede1c0972
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4728ad4fcd44c754a62ec19037562e63d92ec304
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67656607"
---
# <a name="tutorial-integrate-drift-with-azure-active-directory"></a>Tutorial: Integración de Drift con Azure Active Directory

En este tutorial, aprenderá a integrar Drift con Azure Active Directory (Azure AD). Al integrar Drift con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a Drift.
* Permitir que los usuarios puedan iniciar sesión automáticamente en Drift con sus cuentas de Azure AD.
* Administrar las cuentas desde una ubicación central (Azure Portal).

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Suscripción habilitada para el inicio de sesión único (SSO) en Drift.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. Drift admite el SSO iniciado por **SP e IDP**, así como el aprovisionamiento de usuarios **Just In Time**.

## <a name="adding-drift-from-the-gallery"></a>Adición de Drift desde la galería

Para configurar la integración de Drift en Azure AD, tendrá que agregar Drift desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel de navegación de la izquierda, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Drift** en el cuadro de búsqueda.
1. Seleccione **Drift** en el panel de resultados y agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

Configure y pruebe el inicio de sesión único de Azure AD con Drift utilizando un usuario de prueba llamado **B. Simon**. Para que el SSO funcione, es necesario establecer una relación de vinculación entre un usuario de Azure AD y el usuario relacionado de Drift.

Para configurar y probar el inicio de sesión único de Azure AD con Drift, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-sso)** , para permitir que los usuarios puedan utilizar esta característica.
2. **[Configuración de Drift](#configure-drift)** , para configurar el inicio de sesión único en la aplicación.
3. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con B. Simon.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para habilitar a B. Simon para que use el inicio de sesión único de Azure AD.
5. **[Creación de un usuario de prueba en Drift](#create-drift-test-user)** , para tener un homólogo de B. Simon en Drift que esté vinculado a la representación del usuario en Azure AD.
6. **[Comprobación del inicio de sesión único](#test-sso)** , para verificar que la configuración funciona correctamente.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de la aplicación **Drift**, busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono de edición o con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Edición de la configuración básica de SAML](common/edit-urls.png)

1. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada y las direcciones URL necesarias ya se han rellenado previamente con Azure. Para guardar la configuración, el usuario debe hacer clic en el botón **Guardar** y realizar los pasos siguientes:

    a. Haga clic en **Establecer direcciones URL adicionales**.
 
    b. En el cuadro de texto **Estado de la retransmisión**, escriba una dirección URL: `https://app.drift.com` 

    c. Si quiere configurar la aplicación en modo iniciado por **SP**, realice el siguiente paso:

    d. En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL: `https://start.drift.com`

6. La aplicación Drift espera las aserciones de SAML en un formato específico, lo que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token SAML. La siguiente captura de muestra la lista de atributos predeterminados. Haga clic en el icono **Editar** para abrir el cuadro de diálogo Atributos de usuario.

    ![imagen](common/edit-attribute.png)

7. Además de lo anterior, la aplicación Drift espera que se usen algunos atributos más en la respuesta de SAML. En la sección Notificaciones del usuario del cuadro de diálogo Atributos de usuario, haga lo siguiente para agregar el atributo Token SAML como se muestra en la tabla siguiente: 

    | NOMBRE | Atributo de origen|
    | ---------------| --------------- |    
    | NOMBRE | user.displayname |

    a. Haga clic en **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![imagen](common/new-save-attribute.png)

    ![imagen](common/new-attribute-details.png)

    b. En el cuadro de texto **Nombre**, escriba el nombre que se muestra para la fila.

    c. Deje **Espacio de nombres** en blanco.

    d. Seleccione **Atributo** como origen.

    e. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    f. Haga clic en **Aceptar**.

    g. Haga clic en **Save**(Guardar).

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación** y seleccione **Descargar** para descargar el certificado y guardarlo en su equipo.

   ![Vínculo de descarga del certificado](common/metadataxml.png)

1. En la sección **Configurar Drift**, copie las direcciones URL adecuadas según sus necesidades.

   ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

### <a name="configure-drift"></a>Configuración de Drift

1. Para automatizar la configuración en Drift, debe instalar la **extensión del explorador de inicio de sesión seguro de Aplicaciones**. Para ello, haga clic en **Instalar la extensión**.

    ![Extensión Mis aplicaciones](common/install-myappssecure-extension.png)

2. Después de agregar la extensión al explorador, haga clic en **Configurar Drift** para ir a la aplicación Drift. En ella, escriba las credenciales de administrador para iniciar sesión en Drift. La extensión de explorador configurará automáticamente la aplicación y automatizará los pasos 3 y 4.

    ![Configuración](common/setup-sso.png)

3. Si quiere configurar Drift manualmente, abra una nueva ventana del explorador web, inicie sesión en el sitio de empresa de Drift como administrador y haga lo siguiente:

4. En la parte izquierda de la barra de menús, haga clic en el **icono de configuración** > **App Settings** > **Authentication** (Configuración de la aplicación > Autenticación) y realice los pasos siguientes:

    ![Vínculo de administración](./media/drift-tutorial/tutorial_drift_admin.png)

    a. Cargue el **XML de metadatos de la federación** que ha descargado de Azure Portal en el cuadro de texto **Upload Identity Provider metadata file** (Cargar archivo de metadatos del proveedor de identidades).

    b. Después de cargar el archivo de metadatos, los valores restantes se rellenan automáticamente en la página.

    c. Haga clic en **Enable SAML**(Habilitar SAML).

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, va a crear un usuario de prueba llamado B. Simon en Azure Portal.

1. En el panel izquierdo de Azure Portal, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   1. En el campo **Nombre**, escriba `B. Simon`.  
   1. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B. Simon@contoso.com`.
   1. Active la casilla **Show password** (Mostrar contraseña) y, después, anote el valor que se muestra en el cuadro **Contraseña**.
   1. Haga clic en **Create**(Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B. Simon acceda a Drift utilizando el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Drift**.
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. A continuación, en el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Vínculo de Agregar usuario](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B. Simon** en la lista de usuarios y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. Si espera que haya un valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione en la lista el rol adecuado para el usuario y haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.
1. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="create-drift-test-user"></a>Creación de un usuario de prueba en Drift

En esta sección, se crea un usuario llamado a Britta Simon en Drift. Drift admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario no existe en Drift, se crea otro después de la autenticación.

>[!Note]
>Si necesita crear manualmente un usuario, póngase en contacto con el [equipo de soporte técnico de Drift](mailto:integrations@drift.com).

### <a name="test-sso"></a>Prueba de SSO

Al seleccionar el icono de Drift en el Panel de acceso, debería iniciar sesión automáticamente en la instancia de Drift para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)