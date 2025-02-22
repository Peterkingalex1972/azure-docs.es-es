---
title: 'Tutorial: Integración de Azure Active Directory con Amazon Web Services (AWS) | Microsoft Docs'
description: Obtenga información sobre cómo configurar el inicio de sesión único entre Azure Active Directory y Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/30/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 672a3571202b92232bd45a42254a43019f6a9796
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617346"
---
# <a name="tutorial-integrate-amazon-web-services-aws-with-azure-active-directory"></a>Tutorial: Integración de Amazon Web Services (AWS) con Azure Active Directory

En este tutorial, aprenderá a integrar Amazon Web Services (AWS) con Azure Active Directory (Azure AD). Al integrar AWS con Azure AD, puede hacer lo siguiente:

* Controlar en Azure AD quién tiene acceso a AWS.
* Permitir que los usuarios puedan iniciar sesión automáticamente en AWS con sus cuentas de Azure AD.
* Puede administrar sus cuentas en una ubicación central, Azure Portal.

Para más información sobre la integración de aplicaciones SaaS con Azure AD, consulte [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

![Diagrama de la relación de Azure AD y AWS](./media/amazon-web-service-tutorial/tutorial_amazonwebservices_image.png)

Puede configurar varios identificadores para varias instancias. Por ejemplo:

* `https://signin.aws.amazon.com/saml#1`

* `https://signin.aws.amazon.com/saml#2`

Con estos valores, Azure AD quitará el valor de **#** y enviará el valor correcto `https://signin.aws.amazon.com/saml` como dirección URL del público en el token de SAML.

Se recomienda este enfoque por las siguientes razones:

- Cada aplicación le proporciona un certificado X509 único. Cada instancia de aplicación de AWS puede tener una fecha de expiración del certificado diferente que se puede administrar de forma individual en cada cuenta de AWS. En este caso, la sustitución general del certificado es sencilla.

- Puede habilitar el aprovisionamiento de usuarios con la aplicación AWS en Azure AD y, luego, nuestro servicio capturará todos los roles de esa cuenta de AWS. No tiene que agregar ni actualizar manualmente los roles de AWS en la aplicación.

- Puede asignar el propietario de la aplicación de forma individual para la aplicación. Esta persona puede administrar la aplicación directamente en Azure AD.

> [!Note]
> Asegúrese de usar solo una aplicación de la galería.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesita los siguientes elementos:

* Una suscripción de Azure AD. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).
* Una suscripción habilitada para el inicio de sesión único (SSO) en AWS.

## <a name="scenario-description"></a>Descripción del escenario

En este tutorial, va a configurar y probar el inicio de sesión único de Azure AD en un entorno de prueba. AWS admite el SSO iniciado por **SP e IDP**.

## <a name="add-aws-from-the-gallery"></a>Adición de AWS desde la galería

Para configurar la integración de AWS en Azure AD, es preciso agregar AWS desde la galería a la lista de aplicaciones SaaS administradas.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta personal, profesional o educativa de Microsoft.
1. En el panel izquierdo, seleccione el servicio **Azure Active Directory**.
1. Vaya a **Aplicaciones empresariales** y seleccione **Todas las aplicaciones**.
1. Para agregar una nueva aplicación, seleccione **Nueva aplicación**.
1. En la sección **Agregar desde la galería**, escriba **Amazon Web Services (AWS)** en el cuadro de búsqueda.
1. Seleccione **Amazon Web Services (AWS)** en el panel de resultados y, luego, agregue la aplicación. Espere unos segundos mientras la aplicación se agrega al inquilino.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configuración y prueba del inicio de sesión único en Azure AD

Configure y pruebe el inicio de sesión único de Azure AD con AWS mediante un usuario de prueba llamado **B.Simon**. Para que el inicio de sesión único funcione, es preciso establecer una relación de vinculo entre un usuario de Azure AD y el usuario relacionado de AWS.

Para configurar y probar el inicio de sesión único de Azure AD con AWS, es preciso completar los siguientes bloques de creación:

1. **Configuración del inicio de sesión único de Azure AD**, para permitir que los usuarios puedan utilizar esta característica.
2. **Configuración de AWS**, para configurar las opciones del inicio de sesión único en la aplicación.
3. **Creación de un usuario de prueba de Azure AD**, para probar el inicio de sesión único de Azure AD con el usuario B.Simon.
4. **Asignación del usuario de prueba de Azure AD**, para habilitar a B.Simon para que use el inicio de sesión único de Azure AD.
5. **Creación de un usuario de prueba de AWS**, para tener un homólogo de B.Simon en AWS que esté vinculado a la representación del usuario en Azure AD.
6. **Comprobación del inicio de sesión único**, para verificar que la configuración funciona correctamente.

### <a name="configure-azure-ad-sso"></a>Configuración del inicio de sesión único de Azure AD

Siga estos pasos para habilitar el inicio de sesión único de Azure AD en Azure Portal.

1. En [Azure Portal](https://portal.azure.com/), en la página de integración de aplicaciones de **Amazon Web Services (AWS)** , busque la sección **Administrar** y seleccione **Inicio de sesión único**.
1. En la página **Seleccione un método de inicio de sesión único**, seleccione **SAML**.
1. En la página **Configurar el inicio de sesión único con SAML**, seleccione el icono con forma de lápiz para abrir el cuadro de diálogo **Configuración básica de SAML** y modificar la configuración.

   ![Captura de pantalla de la página Configurar el inicio de sesión único con SAML, con el icono de lápiz resaltado](common/edit-urls.png)

4. En la sección **Configuración básica de SAML**, la aplicación está preconfigurada y las direcciones URL necesarias ya se han rellenado previamente con Azure. El usuario debe guardar la configuración, para lo que debe seleccionar el botón **Guardar**.

5. Cuando se configure más de una instancia, proporcione un valor de identificador. A partir de la segunda instancia, use el formato siguiente, incluyendo el símbolo **#** para especificar un valor de SPN único.

    `https://signin.aws.amazon.com/saml#2`

6. La aplicación AWS espera las aserciones de SAML en un formato específico, que requiere que se agreguen asignaciones de atributos personalizados a la configuración de los atributos del token de SAML. La siguiente captura de muestra la lista de atributos predeterminados. Seleccione el icono de lápiz para abrir el cuadro de diálogo Atributos de usuario.

    ![Captura de pantalla del cuadro de diálogo Atributos de usuario, con el icono de lápiz resaltado](common/edit-attribute.png)

7. Además de los atributos anteriores, la aplicación AWS espera que se usen algunos atributos más en la respuesta de SAML. En la sección **Notificaciones del usuario** del cuadro de diálogo **Atributos de usuario**, haga lo siguiente para agregar el atributo del token SAML.

    | NOMBRE  | Atributo de origen  | Espacio de nombres |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Role            | user.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "proporcione un valor comprendido entre 900 segundos (15 minutos) y 43200 segundos (12 horas)" |  https://aws.amazon.com/SAML/Attributes |

    a. Seleccione **Agregar nueva notificación** para abrir el cuadro de diálogo **Administrar las notificaciones del usuario**.

    ![Captura de pantalla de la sección Notificaciones del usuario, con las opciones Agregar nueva notificación y Guardar resaltadas](common/new-save-attribute.png)

    ![Captura de pantalla del cuadro de diálogo Administrar las notificaciones del usuario](common/new-attribute-details.png)

    b. En **Nombre**, escriba el nombre del atributo que se muestra para esa fila.

    c. En **Espacio de nombres**, escriba el valor del espacio de nombres que se muestra para esa fila.

    d. En **Origen**, seleccione **Atributo**.

    e. En la lista **Atributo de origen**, escriba el valor de atributo que se muestra para esa fila.

    f. Seleccione **Aceptar**.

    g. Seleccione **Guardar**.

1. En la página **Configurar el inicio de sesión único con SAML**, en la sección **Certificado de firma de SAML**, busque **XML de metadatos de federación**. Seleccione **Descargar** para descargar el certificado y guárdelo en el equipo.

   ![Captura de pantalla de la sección Certificado de firma de SAML, con la opción Descargar resaltada](common/metadataxml.png)

1. En la sección **Configurar Amazon Web Services (AWS)** , copie las direcciones URL adecuadas según sus necesidades.

   ![Captura de pantalla de la sección Configurar Amazon Web Services (AWS), con las direcciones URL de configuración resaltadas](common/copy-configuration-urls.png)

### <a name="configure-aws"></a>Configuración de AWS

1. En otra ventana del explorador, inicie sesión en su sitio de la compañía de AWS como administrador.

2. Seleccione **AWS Home** (Página principal de AWS).

    ![Captura de pantalla del sitio de la compañía de AWS, con el icono de la página principal de AWS resaltado][11]

3. Seleccione **Identity and Access Management** (Administración de identidades y accesos).

    ![Captura de pantalla de la página de servicios de AWS con la opción IAM resaltada][12]

4. Seleccione **Identity Providers** > **Create Provider** (Proveedores de identidades > Crear proveedor).

    ![Captura de pantalla de la página de IAM, con las opciones Identity Providers (Proveedores de identidades) y Create Provider (Crear proveedor) resaltadas][13]

5. En la página **Configure Provider** (Configurar proveedor), realice los pasos siguientes:

    ![Captura de pantalla de Configure Provider (Configurar proveedor)][14]

    a. En **Provider Type** (Tipo de proveedor), seleccione **SAML**.

    b. En **Provider Name** (Nombre de proveedor), escriba un nombre de proveedor (por ejemplo, *WAAD*).

    c. Para cargar el **archivo de metadatos** descargado de Azure Portal, seleccione **Choose file** (Elegir archivo).

    d. Seleccione **Next Step** (Siguiente paso).

6. En la página **Verify Provider Information** (Comprobar la información del proveedor), seleccione **Create** (Crear).

    ![Captura de pantalla de Verify Provider Information (Comprobar la información del proveedor), con la opción Create (Crear) resaltada][15]

7. Seleccione **Roles** > **Create role** (Crear rol).

    ![Captura de pantalla de la página Roles][16]

8. En la página **Crear rol**, realice los pasos siguientes:  

    ![Captura de pantalla de la página Create role (Crear rol)][19]

    a. En **Select type of trusted entity** (Seleccionar tipo de entidad de confianza), seleccione **SAML 2.0 federation** (Federación de SAML 2.0).

    b. En **Choose a SAML 2.0 Provider** (Elegir un proveedor de SAML 2.0), seleccione el **proveedor de SAML** que ha creado anteriormente (por ejemplo, *WAAD*).

    c. Seleccione **Allow programmatic and AWS Management Console access** (Permitir acceso mediante programación y a consola de AWS Management Console).
  
    d. Seleccione **Siguiente: Permisos**.

9. En el cuadro de diálogo **Attach Permissions Policies** (Adjuntar directivas de permisos), adjunte la directiva adecuada según su organización. Después, seleccione **Next: Review** (Siguiente: revisar).  

    ![Captura de pantalla del cuadro de diálogo Attach Permissions Policies (Adjuntar directivas de permisos)][33]

10. En el cuadro de diálogo **Review** (Revisar), realice los pasos siguientes:

    ![Captura de pantalla del cuadro de diálogo Review (Revisar)][34]

    a. En **Role name** (Nombre de rol), escriba el nombre de su rol.

    b. En **Role description** (Descripción del rol), escriba la descripción.

    c. Seleccione **Create Role** (Crear rol).

    d. Cree tantos roles como sea necesario y asígnelos al proveedor de identidades.

11. Use credenciales de cuenta de servicio de AWS para obtener los roles de cuenta de AWS en aprovisionamiento de usuarios de Azure AD. Para ello, abra la página principal de la consola de AWS.

12. Seleccione **Servicios**. En **Security, Identity & Compliance** (Seguridad, identidad y cumplimiento), seleccione **IAM**.

    ![Captura de pantalla de la página principal de la consola de AWS, con las opciones Services (Servicios) e IAM resaltadas](./media/amazon-web-service-tutorial/fetchingrole1.png)

13. En la sección IAM, seleccione **Policies** (Directivas).

    ![Captura de pantalla de la sección IAM, con la pestaña Policies (Directivas) resaltada](./media/amazon-web-service-tutorial/fetchingrole2.png)

14. Para crear una nueva directiva, seleccione **Create policy** (Crear directiva) para recuperar los roles de la cuenta de AWS de aprovisionamiento de usuarios de Azure AD.

    ![Captura de pantalla de la página Create rol (Crear rol), con la opción Create policy (Crear directiva) resaltada](./media/amazon-web-service-tutorial/fetchingrole3.png)

15. Cree su propia directiva para obtener todos los roles de cuentas de AWS.

    ![Captura de pantalla de la página Create policy (Crear directiva), con la opción JSON resaltada](./media/amazon-web-service-tutorial/policy1.png)

    a. En **Create policy** (Crear directiva), seleccione la pestaña **JSON**.

    b. En el documento de directiva, agregue el siguiente JSON:

    ```json
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Effect": "Allow",
                "Action": [
                "iam:ListRoles"
                ],
                "Resource": "*"
            }
        ]
    }
    ```

    c. Seleccione **Review policy** (Revisar directiva) para validar la directiva.

    ![Captura de pantalla de la página Create policy (Crear directiva)](./media/amazon-web-service-tutorial/policy5.png)

16. Defina la nueva directiva.

    ![Captura de pantalla de la página Create policy (Crear directiva), con los campos de nombre y descripción resaltados](./media/amazon-web-service-tutorial/policy2.png)

    a. En **Name** (Nombre), escriba **AzureAD_SSOUserRole_Policy**.

    b. En **Description** (Descripción), escriba **Esta directiva permitirá obtener los roles de cuentas de AWS**.

    c. Seleccione **Crear una directiva**.

17. Cree una nueva cuenta de usuario en el servicio IAM de AWS.

    a. En la consola de IAM de AWS, seleccione **Users** (Usuarios).

    ![Captura de pantalla de la consola IAM de AWS, con la opción Users (Usuarios) resaltada](./media/amazon-web-service-tutorial/policy3.png)

    b. Para crear un nuevo usuario, seleccione **Add user** (Agregar usuario).

    ![Captura de pantalla del botón Add user (Agregar usuario)](./media/amazon-web-service-tutorial/policy4.png)

    c. En la sección **Add user** (Agregar usuario):

    ![Captura de pantalla de la página Add user (Agregar usuario), con las opciones de nombre de usuario y tipo de acceso resaltadas](./media/amazon-web-service-tutorial/adduser1.png)

    * Escriba el nombre de usuario como **AzureADRoleManager**.

    * Para el tipo de acceso, seleccione **Programmatic access** (Acceso mediante programación). De este modo, el usuario puede llamar a las API y obtener los roles de la cuenta de AWS.

    * Seleccione **Next Permissions** (Siguientes permisos).

18. Cree una nueva directiva para este usuario.

    ![Captura de pantalla de Add user (Agregar usuario)](./media/amazon-web-service-tutorial/adduser2.png)

    a. Seleccione **Attach existing policies directly** (Asociar directivas existentes directamente).

    b. Busque la directiva recién creada en la sección de filtro **AzureAD_SSOUserRole_Policy**.

    c. Seleccione la directiva y, a continuación, seleccione **Next: Review** (Siguiente: revisar).

19. Revise la directiva del usuario asociado.

    ![Captura de pantalla de la página Add user (Agregar usuario), con la opción Create user (crear usuario) resaltada](./media/amazon-web-service-tutorial/adduser3.png)

    a. Revise el nombre de usuario, el tipo de acceso y la directiva asociada al usuario.

    b. Seleccione **Create User** (Crear usuario).

20. Descargue las credenciales de usuario de un usuario.

    ![Captura de pantalla de Add user (Agregar usuario)](./media/amazon-web-service-tutorial/adduser4.png)

    a. Copie el **identificador de la clave de acceso** y la **clave de acceso secreta** del usuario.

    b. Escriba estas credenciales en la sección de aprovisionamiento de usuarios de Azure AD para obtener los roles de la consola de AWS.

    c. Seleccione **Cerrar**.

21. En el portal de administración de Azure AD, en la aplicación AWS, vaya a **Aprovisionamiento**.

    ![Captura de pantalla de la aplicación AWS, con la opción Aprovisionamiento resaltada](./media/amazon-web-service-tutorial/provisioning.png)

22. Escriba la clave de acceso y la clave secreta en los campos **Secreto de cliente** y **Token secreto** respectivamente.

    ![Captura de pantalla del cuadro de diálogo Credenciales de administrador](./media/amazon-web-service-tutorial/provisioning1.png)

    a. Escriba la clave de acceso de usuario de AWS en el campo **Secreto de cliente**.

    b. Escriba el secreto de usuario de AWS en el campo **Token secreto**.

    c. Seleccione **Test Connection** (Probar conexión).

    d. Seleccione **Guardar** para guardar la configuración.

23. En la sección **Configuración**, en **Estado de aprovisionamiento**, seleccione **Activado**. Después, seleccione **Guardar**.

    ![Captura de pantalla de la sección Configuración, con la opción Activado resaltada](./media/amazon-web-service-tutorial/provisioning2.png)

### <a name="create-an-azure-ad-test-user"></a>Creación de un usuario de prueba de Azure AD

En esta sección, creará un usuario de prueba llamado B. Simon en Azure Portal.

1. En Azure Portal, en el panel izquierdo, seleccione **Azure Active Directory** > **Usuarios** > **Todos los usuarios**.
1. Seleccione **Nuevo usuario** en la parte superior de la pantalla.
1. En las propiedades del **usuario**, siga estos pasos:
   
   a. En el campo **Nombre**, escriba `B.Simon`.  
   b. En el campo **Nombre de usuario**, escriba username@companydomain.extension. Por ejemplo, `B.Simon@contoso.com`.   
   c. Seleccione **Mostrar contraseña** y anótela. Seleccione **Crear**.

### <a name="assign-the-azure-ad-test-user"></a>Asignación del usuario de prueba de Azure AD

En esta sección, va a permitir que B.Simon acceda a AWS mediante el inicio de sesión único de Azure.

1. En Azure Portal, seleccione sucesivamente **Aplicaciones empresariales** y **Todas las aplicaciones**.
1. En la lista de aplicaciones, seleccione **Amazon Web Services (AWS)** .
1. En la página de información general de la aplicación, busque la sección **Administrar** y seleccione **Usuarios y grupos**.

   ![Captura de pantalla de la sección Administrar, con la opción Usuarios y grupos resaltada](common/users-groups-blade.png)

1. Seleccione **Agregar usuario**. En el cuadro de diálogo **Agregar asignación**, seleccione **Usuarios y grupos**.

    ![Captura de pantalla de Add user (Agregar usuario)](common/add-assign-user.png)

1. En el cuadro de diálogo **Usuarios y grupos**, seleccione **B.Simon**. A continuación, elija **Seleccionar**.
1. Si espera algún valor de rol en la aserción de SAML, en el cuadro de diálogo **Seleccionar rol**, seleccione el rol adecuado para el usuario en la lista. A continuación, elija **Seleccionar**.
1. En el cuadro de diálogo **Agregar asignación**, seleccione **Asignar**.

### <a name="test-single-sign-on"></a>Prueba de inicio de sesión único

Al seleccionar el icono de AWS en el Panel de acceso, debería iniciar sesión automáticamente en la instancia de AWS para la que configuró el inicio de sesión único. Para más información sobre el Panel de acceso, consulte [Introducción al Panel de acceso](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="known-issues"></a>Problemas conocidos

 * En la sección **Aprovisionamiento**, en la subsección **Asignaciones** aparece un mensaje del tipo "Cargando..." y no se muestran las asignaciones de atributos. El único flujo de trabajo de aprovisionamiento que se admite actualmente es la importación de roles desde AWS a Azure AD para su selección durante la asignación de usuarios o grupos. Las asignaciones de atributos para esto vienen predeterminadas y no se pueden configurar.
 
 * La sección **Aprovisionamiento** solo admite escribir un conjunto de credenciales para un inquilino de AWS cada vez. Todos los roles importados se escriben en la propiedad `appRoles` del [objeto `servicePrincipal`](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) de Azure AD para el inquilino de AWS. 
 
   Se pueden agregar varios inquilinos de AWS (representados por `servicePrincipals`) a Azure AD desde la galería para el aprovisionamiento. Sin embargo, existe un problema conocido que impide escribir automáticamente todos los roles importados desde los diversos objetos `servicePrincipals` de AWS usados para el aprovisionamiento en el único objeto `servicePrincipal` usado para SSO. 
   
   Una posible solución alternativa consiste en usar [Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta) para extraer todos los objetos `appRoles` importados en cada objeto `servicePrincipal` de AWS en los que esté configurado el aprovisionamiento. Posteriormente, puede agregar estas cadenas de roles a objeto `servicePrincipal` de AWS donde se configura el inicio de sesión único.
 
* Los roles deben cumplir los siguientes requisitos para que se puedan importar desde AWS en Azure AD:

  * Los roles deben tener exactamente un proveedor SAML definido en AWS

  * La longitud combinada del rol ARN y el ARN del proveedor de SAML para un rol que se va a importar debe ser de 119 caracteres, como máximo

## <a name="additional-resources"></a>Recursos adicionales

- [Tutoriales para integrar aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [¿Qué es el acceso a aplicaciones y el inicio de sesión único con Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [¿Qué es el acceso condicional en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/amazon-web-service-tutorial/ic795031.png
[12]: ./media/amazon-web-service-tutorial/ic795032.png
[13]: ./media/amazon-web-service-tutorial/ic795033.png
[14]: ./media/amazon-web-service-tutorial/ic795034.png
[15]: ./media/amazon-web-service-tutorial/ic795035.png
[16]: ./media/amazon-web-service-tutorial/ic795022.png
[17]: ./media/amazon-web-service-tutorial/ic795023.png
[18]: ./media/amazon-web-service-tutorial/ic795024.png
[19]: ./media/amazon-web-service-tutorial/ic795025.png
[32]: ./media/amazon-web-service-tutorial/ic7950251.png
[33]: ./media/amazon-web-service-tutorial/ic7950252.png
[35]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/amazon-web-service-tutorial/ic7950253.png
[36]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/amazon-web-service-tutorial/tutorial_amazonwebservices_provisioning_on.png
