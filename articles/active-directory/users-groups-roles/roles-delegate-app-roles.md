---
title: 'Delegación de los permisos de creación y administración del administrador de aplicaciones: Azure Active Directory | Microsoft Docs'
description: Concesión de permisos para la administración del acceso a las aplicaciones en Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/06/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e15fa8c79663fc2517039124f9be8c1ecd57b8a8
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837882"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegación de permisos de registro de aplicaciones en Azure Active Directory

En este artículo se describe cómo usar los permisos de aplicación en roles personalizados de Azure Active Directory (Azure AD) para satisfacer las necesidades de administración de aplicaciones. Azure Active Directory (Azure AD) le permite delegar los permisos de creación y administración de aplicaciones de las siguientes maneras:

- [Restricción de quién puede crear aplicaciones](#restrict-who-can-create-applications) y administración de las aplicaciones que crean. De forma predeterminada, en Azure AD, todos los usuarios pueden registrar registros de aplicación y administrar todos los aspectos de las aplicaciones que crean. Esto se puede restringir para permitir que solo los usuarios seleccionados tengan permiso.
- [Asignación de uno o varios propietarios a una aplicación](#assign-application-owners). Esta es una manera sencilla de conceder a alguien la posibilidad de administrar todos los aspectos de la configuración de Azure AD de una aplicación específica.
- [Asignación de un rol administrativo integrado](#assign-built-in-application-admin-roles) que conceda acceso para administrar la configuración de todas las aplicaciones en Azure AD. Esta es la manera recomendada de conceder a los expertos de TI acceso para administrar amplios permisos de configuración de aplicaciones sin conceder acceso para administrar otras partes de Azure AD no relacionadas con la configuración de aplicaciones.
- [Creación de un rol](#create-and-assign-a-custom-role-preview) personalizado que defina permisos muy específicos y asignación a alguien en el ámbito de una sola aplicación como propietario limitado o en el ámbito de directorio (todas las aplicaciones) como administrador limitado.

Es importante considerar la posibilidad de conceder acceso mediante uno de los métodos anteriores por dos motivos. En primer lugar, la delegación de la capacidad de realizar tareas administrativas reduce la sobrecarga del administrador global. En segundo lugar, el uso de permisos limitados mejora su nivel de seguridad y reduce la posibilidad de accesos no autorizados. En [Delegación de la administración en Azure Active Directory](roles-concept-delegation.md), se describen problemas de delegación y criterios generales.

## <a name="restrict-who-can-create-applications"></a>Restricción de quién puede crear aplicaciones

De forma predeterminada, en Azure AD, todos los usuarios pueden registrar registros de aplicación y administrar todos los aspectos de las aplicaciones que crean. Todos los usuarios tienen también la posibilidad de dar consentimiento a las aplicaciones para acceder a los datos de la empresa en su nombre. Puede decidir conceder de manera selectiva esos permisos estableciendo los modificadores globales en "No" y agregando los usuarios seleccionados al rol Desarrollador de aplicaciones.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>Deshabilitar la capacidad predeterminada de crear registros de aplicación o dar consentimiento a las aplicaciones

1. Inicie sesión en la organización de Azure AD con una cuenta que sea válida para el rol de administrador global de esa organización.
1. Cuando haya obtenido permisos suficientes, establezca una o ambas de las siguientes opciones:

    - En la  [página Configuración de usuario de su organización](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings), establezca el valor **Los usuarios pueden registrar aplicaciones** en No. Esto deshabilitará la capacidad predeterminada de los usuarios de crear registros de aplicación.
    - En la  [configuración de usuario de las aplicaciones empresariales](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/), establezca el valor **Los usuarios pueden permitir que las aplicaciones accedan a los datos de la compañía en su nombre**, en No. Esto deshabilitará la capacidad predeterminada de los usuarios de dar su consentimiento a las aplicaciones que acceden a los datos de la compañía en su nombre.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Concesión de permisos individuales para crear y dar consentimiento a las aplicaciones cuando la capacidad predeterminada está deshabilitada

Asigne el rol Desarrollador de aplicaciones para conceder la capacidad de crear registros de aplicación cuando el valor **Los usuarios pueden registrar aplicaciones** está establecido en No. Esta función también concede permiso para dar consentimiento en nombre propio cuando la opción **Los usuarios pueden permitir que las aplicaciones accedan a los datos de la compañía en su nombre** está establecida en No. Como comportamiento del sistema, cuando un usuario crea un nuevo registro de aplicación, se agrega automáticamente como primer propietario. Los permisos de propiedad proporcionan al usuario la posibilidad de administrar todos los aspectos de un registro de aplicación o de una aplicación empresarial de su propiedad.

## <a name="assign-application-owners"></a>Asignación de propietarios de la aplicación

La asignación de propietarios es una manera sencilla de conceder la posibilidad de administrar todos los aspectos de la configuración de Azure AD para un registro de aplicación o aplicación empresarial específicos. Como comportamiento del sistema, cuando un usuario crea un nuevo registro de aplicación, se agrega automáticamente como primer propietario. Los permisos de propiedad proporcionan al usuario la posibilidad de administrar todos los aspectos de un registro de aplicación o de una aplicación empresarial de su propiedad. Se puede eliminar el propietario original y agregar otros propietarios.

### <a name="enterprise-application-owners"></a>Propietarios de aplicaciones empresariales

Como propietario, un usuario puede administrar la configuración de la aplicación empresarial que es específica de la organización, como la configuración del inicio de sesión único, el aprovisionamiento y las asignaciones de usuarios. Un propietario también puede agregar o quitar otros propietarios. A diferencia de los administradores globales, los propietarios solo pueden administrar las aplicaciones empresariales que poseen.

En algunos casos, las aplicaciones empresariales creadas desde la galería de aplicaciones incluyen una aplicación empresarial y un registro de aplicación. Cuando esto es así, al agregar un propietario a la aplicación empresarial, se agrega automáticamente el propietario al registro de aplicación correspondiente como propietario.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>Para asignar un propietario a una aplicación empresarial

1. Inicie sesión en su [organización de Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview)  con una cuenta que sea válida para el administrador de aplicaciones o el administrador de aplicaciones en la nube de la organización.
1. En la  [página Registros de aplicaciones](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) de la organización, seleccione una aplicación para abrir su página Información general.
1. Seleccione  **Propietarios**  para ver la lista de los propietarios de la aplicación.
1. Seleccione  **Agregar**  para seleccionar uno o varios propietarios para agregar a la aplicación.

> [!IMPORTANT]
> Los usuarios y las entidades de servicio pueden ser propietarios de registros de aplicaciones. Solo los usuarios pueden ser propietarios de aplicaciones empresariales. No se pueden asignar grupos como propietarios en ninguno de los dos casos.
>
> Los propietarios pueden agregar credenciales a una aplicación y usarlas para suplantar la identidad de la aplicación. La aplicación puede tener más permisos que el propietario y, por tanto, se trataría de una elevación de privilegios con respecto a lo que el propietario tiene acceso como usuario o entidad de servicio. Un propietario de aplicación podría crear o actualizar usuarios u otros objetos durante la suplantación de la aplicación, según los permisos de la aplicación.

## <a name="assign-built-in-application-admin-roles"></a>Asignación de roles de administrador de aplicaciones integrados

Azure AD dispone de un conjunto de roles de administrador integrados para conceder acceso para administrar en Azure AD la configuración de todas las aplicaciones. Estos roles son la manera recomendada de conceder a los expertos de TI acceso para administrar amplios permisos de configuración de aplicaciones sin conceder acceso para administrar otras partes de Azure AD no relacionadas con la configuración de aplicaciones.

- Administrador de aplicaciones: los usuarios con este rol pueden crear y administrar todos los aspectos de las aplicaciones empresariales, los registros de aplicaciones y la configuración del proxy de aplicación. Este rol proporciona igualmente la capacidad de dar el consentimiento para permisos delegados y permisos de aplicaciones, excepto Microsoft Graph y Azure AD Graph. Los usuarios asignados a este rol no se agregan como propietarios al crear nuevos registros de aplicaciones o aplicaciones empresariales.
- Administrador de aplicaciones en la nube: los usuarios con este rol tienen los mismos permisos que el rol de administrador de la aplicación, excluida la capacidad de administrar el proxy de aplicación. Los usuarios asignados a este rol no se agregan como propietarios al crear nuevos registros de aplicaciones o aplicaciones empresariales.

Para más información y para ver la descripción de esos roles, consulte  [Roles disponibles](directory-assign-admin-roles.md#available-roles).

Siga las instrucciones de la guía paso a paso [Asignación de roles a usuarios con Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md) para asignar los roles Administrador de aplicaciones o Administrador de aplicaciones en la nube.

> [!IMPORTANT]
> Los administradores de aplicaciones y los administradores de aplicaciones en la nube pueden agregar credenciales a una aplicación y usarlas para suplantar la identidad de la aplicación. La aplicación puede tener permisos que sean una elevación de privilegios con respecto a los permisos del rol de administrador. Un administrador de este rol podría crear o actualizar usuarios u otros objetos durante la suplantación de la aplicación, según los permisos de la aplicación.
> Ningún rol concede la capacidad para administrar la configuración de acceso condicional.

## <a name="create-and-assign-a-custom-role-preview"></a>Creación y asignación de un rol personalizado (versión preliminar)

La creación y la asignación de roles personalizados son pasos independientes:

- [Cree una *definición de rol personalizada*](roles-create-custom.md) y [agréguele permisos a partir de una lista preestablecida](roles-custom-available-permissions.md). Estos son los mismos permisos que se usan en los roles integrados.
- [Cree una *asignación de roles*](roles-assign-powershell.md) para asignar el rol personalizado.

Esta separación le permite crear una definición de roles única y, a continuación, asignarla muchas veces en *ámbitos* diferentes. Un rol personalizado se puede asignar en un ámbito de toda la organización o en un ámbito de objeto en caso de que exista un único objeto de Azure AD. Un ejemplo de un ámbito de objeto es un registro de aplicación único. Mediante el uso de diferentes ámbitos, se puede asignar la misma definición de roles a Sally en todos los registros de aplicaciones de la organización y, a continuación, a Naveen solo en el registro de aplicación de informes de gastos de Contoso.

Sugerencias para crear y usar roles personalizados para delegar la administración de aplicaciones:
- Los roles personalizados solo conceden acceso a las hojas de registro de aplicaciones más recientes del portal de Azure AD. No conceden acceso a las hojas de registros de aplicaciones heredadas.
- Los roles personalizados no conceden acceso al portal de Azure AD si la opción de usuario "Restringir el acceso al portal de administración de Azure AD" está establecida en Sí.
- Los registros de aplicaciones a los que el usuario tiene acceso mediante las asignaciones de roles solo aparecen en la pestaña "Todas las aplicaciones" de la página Registro de aplicación. No aparecen en la pestaña "Aplicaciones propias".

Para más información sobre los aspectos básicos de los roles personalizados, consulte la [introducción sobre los roles personalizados](roles-custom-overview.md), y también cómo [crear un rol personalizado](roles-create-custom.md) y cómo [asignar un rol](roles-assign-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

- [Permisos y subtipos del registro de aplicaciones](roles-custom-available-permissions.md)
- [Referencia de rol de administrador de Azure AD](directory-assign-admin-roles.md)
