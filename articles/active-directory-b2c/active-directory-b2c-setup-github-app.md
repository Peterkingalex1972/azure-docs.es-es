---
title: Configuración de la suscripción y el inicio de sesión con una cuenta de GitHub mediante Azure Active Directory B2C
description: Permita suscribirse e iniciar sesión a los clientes con cuentas de GitHub en las aplicaciones con Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 056570db89fbe1a3db55c138b46e5b73acc282f8
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622438"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Configuración de la suscripción y del inicio de sesión con una cuenta de GitHub mediante Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Creación de una aplicación GitHub OAuth

Para usar una cuenta de GitHub como [proveedor de identidades](active-directory-b2c-reference-oauth-code.md) en Azure Active Directory (Azure AD) B2C, tiene que crear una aplicación en su inquilino que la represente. Si aún no tiene una cuenta de GitHub, puede obtenerla en [https://www.github.com/](https://www.github.com/).

1. Inicie sesión en el sitio Web [para desarrolladores de GitHub](https://github.com/settings/developers) con sus credenciales de GitHub.
1. Seleccione **OAuth Apps** (Aplicaciones OAuth) y elija **New OAuth App** (Nueva aplicación OAuth).
1. Escriba un **nombre de aplicación** y la **dirección URL de la página principal**.
1. Escriba la información de `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` en **Authorization callback URL** (Dirección URL de devolución de llamada de autorización). Reemplace `your-tenant-name` por el nombre del inquilino de Azure AD B2C. Cuando especifique el nombre de inquilino, escriba todas las letras en minúscula, aunque se haya definido con letras en mayúscula en Azure AD B2C.
1. Haga clic en **Register application** (Registrar aplicación).
1. Copie los valores de **Client ID** y **Client Secret**. Necesitará los dos para agregar el proveedor de identidades a su inquilino.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Configuración de una cuenta de GitHub como proveedor de identidades

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) como administrador global del inquilino de Azure AD B2C.
1. Asegúrese de usar el directorio que contiene el inquilino de Azure AD B2C. Para ello, seleccione el filtro **Directorio y suscripción** en el menú superior y luego el directorio que contiene el inquilino.
1. Elija **Todos los servicios** en la esquina superior izquierda de Azure Portal, busque y seleccione **Azure AD B2C**.
1. Seleccione **Proveedores de identidades** y luego **GitHub (versión preliminar)** .
1. Escriba un **nombre**. Por ejemplo, *GitHub*.
1. En **Id. de cliente**, escriba el identificador de cliente de la aplicación de GitHub que creó anteriormente.
1. En **Secreto de cliente**, escriba el secreto de cliente que ha anotado.
1. Seleccione **Guardar**.
