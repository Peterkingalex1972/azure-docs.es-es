---
title: Requisitos previos de la API | Azure Marketplace
description: Requisitos previos para usar las API de Cloud Partner Portal.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: a973ab0a406168756af61900fd35947c8be6d03b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935378"
---
<a name="api-prerequisites"></a>Requisitos previos de la API
================

Hay dos recursos de programación que necesita tener para usar las API de Cloud Partner Portal: una entidad de servicio y un token de acceso de Azure Active Directory (Azure AD).


<a name="create-a-service-principal-in-your-azure-active-directory-tenant"></a>Creación de una entidad de servicio en su inquilino de Azure Active Directory
----------------------------------------------------------------

En primer lugar, deberá crear una entidad de servicio en el inquilino de Azure AD. A este inquilino se le asignará su propio conjunto de permisos de Cloud Partner Portal. Su código llamará a las API mediante este inquilino en lugar de mediante sus credenciales personales.  Para obtener una descripción completa sobre cómo crear una entidad de servicio, consulte [Use portal to create an Azure Active Directory application and service principal that can access resources](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) (Uso del portal para crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos).


<a name="add-the-service-principal-to-your-account"></a>Adición de la entidad de servicio a su cuenta
-----------------------------------------

Ahora que ha creado la entidad de servicio en su inquilino, puede agregarla como un usuario a su cuenta de Cloud Partner Portal. Al igual que un usuario, la entidad de servicio puede ser un propietario o un colaborador en el portal.

Siga los siguientes pasos para agregar la entidad de servicio:

1. Inicie sesión en Cloud Partner Portal. 
2. Haga clic en **Usuarios** en la barra de menús izquierda y elija **Agregar usuario**.

   ![Adición de un usuario al portal](./media/cloud-partner-portal-api-prerequisites/add-user.jpg)

3. En la lista desplegable **Tipo**, seleccione **Entidad de servicio** y agregue los detalles siguientes:

-   Un **nombre descriptivo** para la entidad de servicio, por ejemplo, `spAccount`.
-   El **id. de la aplicación**. Para encontrar este identificador, vaya a [Azure Portal](https://portal.azure.com), haga clic en **Azure Active Directory**, elija **Registros de aplicaciones** y haga clic en su aplicación.
-   El **id. de inquilino**, también conocido como **id. de directorio**, para el inquilino de Azure AD. Puede encontrar este identificador en la página de Azure Active Directory de [Azure Portal](https://portal.azure.com) en **Propiedades**.
-   El **id. de objeto** del objeto de su entidad de servicio. Puede obtener este identificador en Azure Portal. Vaya a **Azure Active Directory**, elija **Registros de aplicaciones**, haga clic en la aplicación y haga clic en el nombre de la aplicación en **Aplicación administrada en directorio local**. A continuación, vaya a la página **Propiedades** para buscar el identificador de objeto. Asegúrese de que no elige el identificador de objeto inicial que se encuentra en la aplicación, sino el identificador de objeto de la aplicación administrada.
-   El **rol** asociado con la cuenta, que se usará para RBAC.

     ![Adición de una aplicación administrada en el portal](./media/cloud-partner-portal-api-prerequisites/managedapp.png)

1. Haga clic en **Agregar** para agregar la entidad de servicio a su cuenta.

   ![Adición de una entidad de servicio](./media/cloud-partner-portal-api-prerequisites/add-service-principal.jpg)


<a name="get-an-azure-ad-access-token"></a>Obtención de un token de acceso de Azure AD
----------------------------

Las API de Cloud Partner Portal usan los siguientes protocolos y recursos durante la autenticación:

- Un token de portador de JSON Web Token (JWT) para solicitar acceso a los recursos.
- El protocolo [OpenID Connect](https://openid.net/connect/) (OIDC) para verificar la identidad.
- [Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-whatis) como autoridad de la identidad.

Existen dos métodos principales para adquirir mediante programación un token de JWT:

- Use la biblioteca de autenticación de Microsoft (MSAL) para .NET ([MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)).  Este método de nivel superior se recomienda para los desarrolladores de .NET. 
- Realice una solicitud **HTTP POST** para el punto de conexión de **token** de OAuth de Azure AD, que toma el formato siguiente:

``` HTTP
POST https://login.microsoftonline.com/<tenant-id>/oauth2/token
    client_id: <application-id>
    client_secret:<application-secret>
    grant_type: client_credentials
    resource: https://cloudpartner.azure.com
```

Ahora, puede pasar este token como parte del encabezado de autorización de las solicitudes a la API.

``` HTTP
GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview 
    Accept: application/json
    Authorization: Bearer <access-token>
```
> [!NOTE]
> Para todas las API de esta referencia, siempre se supone que el encabezado de autorización se ha pasado, por lo que no se menciona explícitamente.

Si experimenta errores de autenticación en la solicitud, consulte [Troubleshooting authentication errors](./cloud-partner-portal-api-troubleshooting-authentication-errors.md) (Solución de problemas de errores de autenticación).
