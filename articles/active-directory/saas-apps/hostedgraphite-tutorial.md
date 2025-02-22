---
title: 'Tutorial: Integración de Azure Active Directory con Hosted Graphite | Microsoft Docs'
description: Aprenda a configurar el inicio de sesión único entre Azure Active Directory y Hosted Graphite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79a83ec34d8e9d577e6bde8421a11b4c7bf6b241
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67100908"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Tutorial: Integración de Azure Active Directory con Hosted Graphite

En este tutorial, aprenderá a integrar Hosted Graphite con Azure Active Directory (Azure AD).
La integración de Hosted Graphite con Azure AD proporciona las siguientes ventajas:

* Puede controlar en Azure AD quién tiene acceso a Hosted Graphite.
* Puede habilitar a los usuarios para que inicien sesión automáticamente en Hosted Graphite (inicio de sesión único) con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central: Azure Portal.

Si desea obtener más información sobre la integración de aplicaciones SaaS con Azure AD, vea [Qué es el acceso a las aplicaciones y el inicio de sesión único en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para configurar la integración de Azure AD con Hosted Graphite, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no dispone de un entorno de Azure AD, puede obtener una versión de prueba de un mes [aquí](https://azure.microsoft.com/pricing/free-trial/)
* Una suscripción habilitada para el inicio de sesión único en Hosted Graphite

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, puede configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba.

* Hosted Graphite admite el inicio de sesión único iniciado por **SP e IDP**
* Hosted Graphite admite el aprovisionamiento de usuarios **Just-In-Time**

## <a name="adding-hosted-graphite-from-the-gallery"></a>Adición de Hosted Graphite desde la galería

Para configurar la integración de Hosted Graphite en Azure AD, deberá agregar Hosted Graphite desde la galería a la lista de aplicaciones SaaS administradas.

**Para agregar Hosted Graphite desde la galería, realice los pasos siguientes:**

1. En el panel de navegación izquierdo de **[Azure Portal](https://portal.azure.com)** , haga clic en el icono de **Azure Active Directory**.

    ![Botón Azure Active Directory](common/select-azuread.png)

2. Vaya a **Aplicaciones empresariales** y seleccione la opción **Todas las aplicaciones**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

3. Para agregar una nueva aplicación, haga clic en el botón **Nueva aplicación** de la parte superior del cuadro de diálogo.

    ![Botón Nueva aplicación](common/add-new-app.png)

4. En el cuadro de búsqueda, escriba **Hosted Graphite**, seleccione **Hosted Graphite** en el panel de resultados y, luego, haga clic en el botón **Agregar** para agregar la aplicación.

     ![Hosted Graphite en la lista de resultados](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

En esta sección configurará y probará el inicio de sesión único de Azure AD con Hosted Graphite con un usuario de prueba llamado **Britta Simon**.
Para que el inicio de sesión único funcione, es preciso establecer una relación de vínculo entre un usuario de Azure AD y el usuario relacionado de Hosted Graphite.

Para configurar y probar el inicio de sesión único de Azure AD con Hosted Graphite, es preciso completar los siguientes bloques de creación:

1. **[Configuración del inicio de sesión único de Azure AD](#configure-azure-ad-single-sign-on)** : para que los usuarios puedan usar esta característica.
2. **[Configuración del inicio de sesión único de Hosted Graphite](#configure-hosted-graphite-single-sign-on)** : para configurar los valores de Inicio de sesión único en la aplicación.
3. **[Creación de un usuario de prueba de Azure AD](#create-an-azure-ad-test-user)** , para probar el inicio de sesión único de Azure AD con Britta Simon.
4. **[Asignación del usuario de prueba de Azure AD](#assign-the-azure-ad-test-user)** , para permitir que Britta Simon use el inicio de sesión único de Azure AD.
5. **[Creación de un usuario de prueba de Hosted Graphite](#create-hosted-graphite-test-user)** : para tener un homólogo de Britta Simon en Hosted Graphite que esté vinculado a la representación del usuario en Azure AD.
6. **[Prueba del inicio de sesión único](#test-single-sign-on)** : para comprobar si la configuración funciona.

### <a name="configure-azure-ad-single-sign-on"></a>Configuración del inicio de sesión único de Azure AD

En esta sección, habilitará el inicio de sesión único de Azure AD en Azure Portal.

Para configurar el inicio de sesión único de Azure AD con Hosted Graphite, realice los pasos siguientes:

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de aplicaciones de **Hosted Graphite**, seleccione **Inicio de sesión único**.

    ![Vínculo Configurar inicio de sesión único](common/select-sso.png)

2. En el cuadro de diálogo **Seleccionar un método de inicio de sesión único**, seleccione el modo **SAML/WS-Fed** para habilitar el inicio de sesión único.

    ![Modo de selección de inicio de sesión único](common/select-saml-option.png)

3. En la página **Configurar el inicio de sesión único con SAML**, haga clic en el icono **Editar** para abrir el cuadro de diálogo **Configuración básica de SAML**.

    ![Edición de la configuración básica de SAML](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, si desea configurar la aplicación en el modo iniciado por **IDP** siga estos pasos:

    ![Información de dominio y direcciones URL de inicio de sesión único de Hosted Graphite](common/idp-intiated.png)

    a. En el cuadro de texto **Identificador**, escriba una dirección URL con el patrón siguiente: `https://www.hostedgraphite.com/metadata/<user id>`

    b. En el cuadro de texto **URL de respuesta**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/complete/saml/<user id>`

5. Haga clic en **Establecer direcciones URL adicionales** y siga este paso si desea configurar la aplicación en el modo iniciado por **SP**:

    ![Información de dominio y direcciones URL de inicio de sesión único de Hosted Graphite](common/metadata-upload-additional-signon.png)

    En el cuadro de texto **URL de inicio de sesión**, escriba una dirección URL con el siguiente patrón: `https://www.hostedgraphite.com/login/saml/<user id>/`

    > [!NOTE]
    > Tenga en cuenta que estos no son valores reales. Tiene que actualizarlos con el identificador, la dirección URL de respuesta y la URL de inicio de sesión reales. Para obtener estos valores, puede ir a Acceso -> Configuración de SAML en la aplicación o ponerse en contacto con el [equipo de soporte técnico de Hosted Graphite](mailto:help@hostedgraphite.com).

6. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, haga clic en **Descargar** para descargar el **certificado (Base64)** de las opciones proporcionadas según sus requisitos y guárdelo en el equipo.

    ![Vínculo de descarga del certificado](common/certificatebase64.png)

7. En la sección **Configurar Hosted Graphite**, copie las direcciones URL adecuadas según sus requisitos.

    ![Copiar direcciones URL de configuración](common/copy-configuration-urls.png)

    a. URL de inicio de sesión

    b. Identificador de Azure AD

    c. URL de cierre de sesión

### <a name="configure-hosted-graphite-single-sign-on"></a>Configuración del inicio de sesión único de Hosted Graphite

1. Inicie la sesión en el inquilino de Hosted Graphite como administrador.

2. Vaya a la **página de configuración de SAML** en la barra lateral (**Acceso -> Configuración de SAML**).

    ![Configuración del inicio de sesión único en la aplicación](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

3. Confirme que estos identificadores URI coinciden con la configuración realizada en la sección **Configuración básica de SAML** de Azure Portal.

    ![Configuración del inicio de sesión único en la aplicación](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

4. En los cuadros de texto **Entity or Issuer ID** (Identificador de entidad o de emisor) y **SSO Login URL** (Dirección URL de inicio de sesión de SSO), pegue el valor de **Identificador de Azure AD** y **Dirección URL de inicio de sesión** que ha copiado de Azure Portal.

    ![Configuración del inicio de sesión único en la aplicación](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)

5. Seleccione **Read-only** (Solo lectura) en **Default User Role** (Rol de usuario predeterminado).

    ![Configuración del inicio de sesión único en la aplicación](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

6. Abra el certificado codificado en base 64 descargado de Azure Portal en el Bloc de notas, copie su contenido en el Portapapeles y luego péguelo en el cuadro de texto **Certificado X.509**.

    ![Configuración del inicio de sesión único en la aplicación](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

7. Haga clic en el botón **Guardar** .

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

El objetivo de esta sección es crear un usuario de prueba en Azure Portal llamado "Britta Simon".

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory**, **Usuarios** y **Todos los usuarios**.

    ![Vínculos "Usuarios y grupos" y "Todos los usuarios"](common/users.png)

2. Seleccione **Nuevo usuario** en la parte superior de la pantalla.

    ![Botón Nuevo usuario](common/new-user.png)

3. En las propiedades Usuario, siga estos pasos.

    ![Cuadro de diálogo Usuario](common/user-properties.png)

    a. En el campo **Nombre**, escriba **BrittaSimon**.
  
    b. En el campo **Nombre de usuario**, escriba **brittasimon\@yourcompanydomain.extension**.  
    Por ejemplo: BrittaSimon@contoso.com

    c. Active la casilla **Mostrar contraseña** y, después, anote el valor que se muestra en el cuadro Contraseña.

    d. Haga clic en **Create**(Crear).

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, habilitará a Britta Simon para que use el inicio de sesión único de Azure concediéndole acceso a Hosted Graphite.

1. En Azure Portal, seleccione **Aplicaciones empresariales**, **Todas las aplicaciones** y **Hosted Graphite**.

    ![Hoja Aplicaciones empresariales](common/enterprise-applications.png)

2. En la lista de aplicaciones, seleccione **Hosted Graphite**.

    ![El vínculo Hosted Graphite en la lista de aplicaciones](common/all-applications.png)

3. En el menú de la izquierda, seleccione **Usuarios y grupos**.

    ![Vínculo "Usuarios y grupos"](common/users-groups-blade.png)

4. Haga clic en el botón **Agregar usuario** y, después, seleccione **Usuarios y grupos** en el cuadro de diálogo **Agregar asignación**.

    ![Panel Agregar asignación](common/add-assign-user.png)

5. En el cuadro de diálogo **Usuarios y grupos**, seleccione **Britta Simon** en la lista Usuarios y, luego, haga clic en el botón **Seleccionar** en la parte inferior de la pantalla.

6. Si espera cualquier valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol** seleccione en la lista el rol adecuado para el usuario y, después, haga clic en el botón **Seleccionar** de la parte inferior de la pantalla.

7. En el cuadro de diálogo **Agregar asignación**, haga clic en el botón **Asignar**.

### <a name="create-hosted-graphite-test-user"></a>Creación de un usuario de prueba de Hosted Graphite

En esta sección se creará un usuario llamado Britta Simon en Hosted Graphite. Hosted Graphite admite el aprovisionamiento de usuarios Just-In-Time, que está habilitado de forma predeterminada. No hay ningún elemento de acción para usted en esta sección. Si un usuario no existe aún en Hosted Graphite, se crea uno nuevo después de la autenticación.

> [!NOTE]
> Si necesita crear un usuario manualmente, es preciso que se ponga contacto con el equipo de soporte técnico de Hosted Graphite a través de <mailto:help@hostedgraphite.com>.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

En esta sección, probará la configuración de inicio de sesión único de Azure AD mediante el Panel de acceso.

Al hacer clic en el icono de Hosted Graphite en el panel de acceso, se debería iniciar sesión automáticamente en la instancia de Hosted Graphite para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Recursos adicionales

- [Lista de tutoriales sobre cómo integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

