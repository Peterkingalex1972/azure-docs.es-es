---
title: Usar Azure Portal para administrar los derechos de acceso de Azure AD a los datos de blob y cola con RBAC - Azure Storage | Microsoft Docs
description: Use el control de acceso basado en roles (RBAC) de Azure Portal para asignar el acceso a contenedores y colas a distintas entidades de seguridad. Azure Storage admite roles RBAC tanto integrados como personalizados para la autenticación a través de Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4c558da6b0a9267c03b26ca1b5f57eb5e7444881
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68515004"
---
# <a name="grant-access-to-azure-blob-and-queue-data-with-rbac-in-the-azure-portal"></a>Conceder acceso a datos de blob y cola de Azure con RBAC en Azure Portal

Azure Active Directory (Azure AD) autoriza derechos de acceso a los recursos protegidos mediante el [control de acceso basado en rol (RBAC)](../../role-based-access-control/overview.md). Azure Storage define un conjunto de roles RBAC integrados que abarcan conjuntos comunes de permisos utilizados para acceder a los datos de blob o de cola. 

Cuando un rol RBAC se asigna a una entidad de seguridad de Azure AD, Azure concede acceso a esos recursos a esa entidad de seguridad. El acceso se puede limitar al nivel de la suscripción, el grupo de recursos, la cuenta de almacenamiento o un contenedor individual o una cola. Una entidad de seguridad de Azure AD puede ser un usuario, un grupo, una entidad de servicio de aplicación o una [identidad de servicio administrada para recursos de Azure](../../active-directory/managed-identities-azure-resources/overview.md).

En este artículo se describe cómo usar Azure Portal para asignar roles RBAC. Azure Portal proporciona una interfaz sencilla para asignar roles RBAC y administrar el acceso a los recursos de almacenamiento. También se pueden asignar roles RBAC relativos a recursos de blob y cola mediante las herramientas de línea de comandos de Azure o las API de administración de Azure Storage. Para obtener más información sobre los roles RBAC relativos a recursos de almacenamiento, vea [Autenticación del acceso a Azure Storage con Azure Active Directory, versión preliminar](storage-auth-aad.md). 

## <a name="rbac-roles-for-blobs-and-queues"></a>Roles RBAC para blobs y colas

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Determinar el ámbito de recursos 

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="assign-rbac-roles-using-the-azure-portal"></a>Asignar roles RBAC con Azure Portal

Después de determinar el ámbito adecuado de una asignación de roles, vaya a ese recurso en Azure Portal. Acceda a la configuración **Control de acceso (IAM)** del recurso y siga estas instrucciones para administrar las asignaciones de roles:

1. Asigne el rol RBAC de Azure Storage adecuado para conceder acceso a una entidad de seguridad de Azure AD.

1. Asigne el rol [Lector](../../role-based-access-control/built-in-roles.md#reader) de Azure Resource Manager a aquellos usuarios que necesiten tener acceso a los contenedores o colas a través de Azure Portal con sus credenciales de Azure AD. 

En las siguientes secciones se describe cada uno de estos pasos con más detalle.

> [!NOTE]
> Como propietario de la cuenta de Azure Storage, no se le asignan automáticamente permisos para tener acceso a datos. Tiene que asignarse a sí mismo de forma explícita un rol RBAC para Azure Storage. Puede asignarlo al nivel de su suscripción, grupo de recursos, cuenta de almacenamiento o un contenedor o cola.
> 
> No se puede asignar un rol que tenga como ámbito un contenedor o una cola si la cuenta de almacenamiento tiene habilitados los espacios de nombres jerárquicos.

### <a name="assign-a-built-in-rbac-role"></a>Asignar un rol RBAC integrado

Antes de asignar un rol a una entidad de seguridad, asegúrese de haber tenido en cuenta el ámbito de los permisos que se van a conceder. Revise la sección [Determinar el ámbito de recursos](#determine-resource-scope) para decidir cuál es el ámbito adecuado.

El procedimiento mostrado aquí asigna un rol limitado a un contenedor, pero puede seguir los mismos pasos para asignar un rol limitado a una cola: 

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento y muestre la **Introducción** para la cuenta.
1. En Servicios, seleccione **Blobs**. 
1. Busque el contenedor para el que desea asignar un rol y visualice la configuración del contenedor. 
1. Seleccione **Control de acceso (IAM)** para mostrar la configuración del control de acceso del contenedor. Seleccione la pestaña **Asignaciones de roles** para ver la lista de asignaciones de roles.

    ![Captura de pantalla que muestra la configuración de control de acceso del contenedor](media/storage-auth-aad-rbac-portal/portal-access-control-container.png)

1. Haga clic en el botón **Agregar asignación de roles** para agregar un rol nuevo.
1. En la ventana **Agregar asignación de roles**, seleccione el rol de Azure Storage que quiera asignar. Después, realice una búsqueda para localizar la entidad de seguridad a la que quiere asignar ese rol.

    ![Captura de pantalla que muestra cómo asignar un rol de RBAC](media/storage-auth-aad-rbac-portal/add-rbac-role.png)

1. Haga clic en **Save**(Guardar). La identidad a la que ha asignado el rol aparece enumerada debajo de ese rol. Por ejemplo, la siguiente imagen muestra que el usuario agregado tiene ahora permisos de lectura para los datos del contenedor llamado *sample-container*.

    ![Captura de pantalla con una lista de los usuarios asignados a un rol](media/storage-auth-aad-rbac-portal/container-scoped-role.png)

Puede realizar este mismo procedimiento para asignar un rol que tenga como ámbito una cuenta de almacenamiento, un grupo de recursos o una suscripción.

### <a name="assign-the-reader-role-for-portal-access"></a>Asignar el rol Lector para acceder a Portal

Al asignar un rol integrado o personalizado de Azure Storage a una entidad de seguridad, está concediendo permisos a esa entidad de seguridad para realizar operaciones en los datos de su cuenta de almacenamiento. Los roles **Lector de datos** integrados proporcionan permisos de lectura en los datos de un contenedor o una cola, mientras que los roles **Colaborador de datos** integrados proporcionan permisos de lectura, escritura y eliminación en un contenedor o cola. Los permisos se limitan al ámbito del recurso especificado.  

Por ejemplo, si asigna el rol **Colaborador de datos de Storage Blob** al usuario María en el nivel de un contenedor denominado **contenedor-ejemplo**, María tendrá acceso de lectura, escritura y eliminación en todos los blobs de ese contenedor.

Pero si María quiere ver un blob en Azure Portal, el rol **Colaborador de datos de Storage Blob** por sí solo no le proporcionará suficientes permisos para desplazarse por Portal hasta el blob para poder verlo. Se necesitan más permisos de Azure AD para desplazarse por Portal y ver los otros recursos que estén visibles allí.

Si los usuarios necesitan poder acceder a blobs de Azure Portal, asígneles un rol RBAC adicional (el rol [Lector](../../role-based-access-control/built-in-roles.md#reader)) en el nivel de la cuenta de almacenamiento o por encima de esta. El rol **Lector** es un rol de Azure Resource Manager que permite a los usuarios ver recursos de la cuenta de almacenamiento, pero no modificarlos. No proporciona permisos de lectura en los datos de Azure Storage, sino únicamente en los recursos de administración de la cuenta.

Siga estos pasos para asignar el rol **Lector** para que un usuario pueda acceder a los blobs de Azure Portal. En este ejemplo, la asignación se limita al ámbito de la cuenta de almacenamiento:

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento.
1. Seleccione **Control de acceso (IAM)** para mostrar la configuración del control de acceso de la cuenta de almacenamiento. Seleccione la pestaña **Asignaciones de roles** para ver la lista de asignaciones de roles.
1. En la ventana **Agregar asignación de roles**, seleccione el rol **Lector**. 
1. En el campo **Asignar acceso a**, seleccione **Usuario, grupo o entidad de servicio de Azure AD**.
1. Realice una búsqueda para localizar la entidad de seguridad a la que quiere asignar el rol.
1. Guarde la asignación de roles.

> [!NOTE]
> Asignar el rol Lector solo es necesario con aquellos usuarios que necesiten tener acceso a blobs o colas mediante Azure Portal. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre los roles RBAC relativos a recursos de almacenamiento, vea [Autenticación del acceso a Azure Storage con Azure Active Directory, versión preliminar](storage-auth-aad.md). 
- Para más información acerca de RBAC, consulte el artículo [¿Qué es el control de acceso basado en rol (RBAC)?](../../role-based-access-control/overview.md).
- Para información sobre cómo asignar y administrar las asignaciones de roles RBAC con Azure PowerShell, la CLI de Azure, o la API de REST, consulte estos artículos:
    - [Administración del control de acceso basado en rol (RBAC) con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
    - [Administración del control de acceso basado en rol (RBAC) con la CLI de Azure](../../role-based-access-control/role-assignments-cli.md)
    - [Administración del control de acceso basado en rol (RBAC) con la API de REST](../../role-based-access-control/role-assignments-rest.md)
- Para información sobre la autorización de acceso a los contenedores y las colas desde las aplicaciones de almacenamiento, consulte el artículo sobre el [uso de Azure AD con aplicaciones de Azure Storage](storage-auth-aad-app.md).
