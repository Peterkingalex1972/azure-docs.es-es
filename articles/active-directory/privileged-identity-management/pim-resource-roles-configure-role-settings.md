---
title: 'Configuración de roles de recursos de Azure en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar los roles de recursos de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/09/2019
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6da92ccd50707e676a1f5d583fe22ae8b3d8b73
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476316"
---
# <a name="configure-azure-resource-role-settings-in-pim"></a>Configuración de roles de recurso de Azure en PIM

Cuando establece la configuración de roles de recursos de Azure, se define la configuración predeterminada que se aplica a las asignaciones de roles de recursos de Azure en Azure Active Directory (Azure AD) Privileged Identity Management (PIM). Para configurar el flujo de trabajo de aprobación y especificar quién puede aprobar o denegar solicitudes, use los siguientes procedimientos.

## <a name="open-role-settings"></a>Apertura de la configuración de roles

Siga estos pasos para abrir la configuración de un rol de recursos de Azure.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con un usuario que sea miembro del [con Administrador de rol con privilegios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

1. Abra **Azure AD Privileged Identity Management**.

1. Haga clic en **Recursos de Azure**.

1. Haga clic en el recurso que quiera administrar, por ejemplo, una suscripción o un grupo de administración.

    ![Página de recursos de Azure que muestra los recursos que se pueden administrar](./media/pim-resource-roles-configure-role-settings/resources-list.png)

1. Haga clic en **Configuración de roles**.

    ![Página de configuración de roles que muestra los roles de recursos de Azure](./media/pim-resource-roles-configure-role-settings/resources-role-settings.png)

1. Haga clic en el rol cuya configuración desea configurar.

    ![Página de detalles de configuración de roles que muestra varias opciones de asignación y activación](./media/pim-resource-roles-configure-role-settings/resources-role-setting-details.png)

1. Haga clic en **Editar** para abrir el panel Configuración de roles.

    ![Página de edición de configuración de roles con opciones para actualizar la configuración de asignación y activación](./media/pim-resource-roles-configure-role-settings/resources-role-settings-edit.png)

    En el panel Configuración de roles de cada rol, hay varias opciones que puede configurar.

## <a name="assignment-duration"></a>Duración de la asignación

Puede elegir entre dos opciones de duración de asignación para cada tipo de asignación (Apto y Activo) cuando se configuran las opciones para un rol. Estas opciones se convierten en la duración máxima predeterminada cuando se asigna un rol a un miembro en PIM.

Puede elegir uno de estas opciones de duración de asignación tipo **Apto**:

| | |
| --- | --- |
| **Permitir asignación elegible permanente** | Los administradores de recursos pueden asignar una pertenencia elegible permanente. |
| **Hacer que las asignaciones elegibles expiren después de** | Los administradores de recursos pueden requerir que todas las asignaciones elegibles tengan una fecha de inicio y finalización especificada. |

Además, puede elegir una de estas opciones de duración de asignación tipo **Activo**:

| | |
| --- | --- |
| **Permitir asignaciones activas permanentes** | Los administradores de recursos pueden asignar una pertenencia activa permanente. |
| **Hacer que las asignaciones activas expiren después de** | Los administradores de recursos pueden requerir que todas las asignaciones activas tengan una fecha de inicio y finalización especificada. |

> [!NOTE] 
> Los administradores de recursos pueden renovar todas las asignaciones que tienen una fecha de finalización específica. Asimismo, los miembros pueden iniciar solicitudes de autoservicio para [extender o renovar las asignaciones](pim-resource-roles-renew-extend.md).

## <a name="require-multi-factor-authentication"></a>Requerir autenticación multifactor

PIM proporciona la aplicación opcional de Azure Multi-Factor Authentication (MFA) para dos escenarios distintos.

### <a name="require-multi-factor-authentication-on-active-assignment"></a>Requerir autenticación multifactor para las asignaciones activas

En algunos casos, es posible que quiera asignar un miembro a un rol durante un breve período (por ejemplo, un día). En este caso, no se necesitan los miembros asignados para solicitar la activación. En este escenario, PIM no puede aplicar MFA cuando el miembro usa su asignación de roles porque ya está activo en el rol desde el momento de su asignación.

Para asegurarse de que el administrador de recursos que cumple la asignación sea quien dice ser, puede aplicar MFA en la asignación activa al comprobar el cuadro **Requerir autenticación multifactor para las asignaciones activas**.

### <a name="require-multi-factor-authentication-on-activation"></a>Requerir Multi-Factor Authentication durante la activación

Puede requerir que los miembros elegibles de un rol ejecutan MFA antes de que se puedan activar. Este proceso garantiza que el usuario que solicita la activación sea quien dice ser con una exactitud razonable. Aplicar esta opción protege los recursos críticos en situaciones en las que es posible que la cuenta de usuario se haya puesto en peligro.

Para requerir que un miembro elegible ejecute MFA antes de la activación, active la casilla **Requerir Multi-Factor Authentication durante la activación** cuadro.

Para obtener más información, consulte [Autenticación multifactor (MFA) y PIM](pim-how-to-require-mfa.md).

## <a name="activation-maximum-duration"></a>Duración máxima de la activación

Use control deslizante **Duración máxima de la activación (horas)** para establecer el tiempo máximo, en horas, que un rol permanece activo antes de expirar. Este valor puede oscilar entre 1 y 24 horas.

## <a name="require-justification"></a>Requerir justificación

Puede requerir que los miembros especifiquen una justificación para las asignaciones activas o cuando se activen. Para requerir justificación, active la casilla **Requerir justificación para las asignaciones activas** o la casilla **Requerir justificación en las activaciones**.

## <a name="require-approval-to-activate"></a>Solicitud de aprobación para activar

Si desea solicitar aprobación para activar un rol, siga estos pasos.

1. Active la casilla **Se requiere aprobación para activar**.

1. Haga clic en **Seleccionar aprobadores** para abrir el panel Seleccionar un miembro o grupo.

    ![Selección de un panel de miembros o grupos para seleccionar aprobadores](./media/pim-resource-roles-configure-role-settings/resources-role-settings-select-approvers.png)

1. Seleccione al menos un miembro o un grupo y luego haga clic en **Seleccionar**. Puede agregar cualquier combinación de miembros y grupos. Debe seleccionar al menos un aprobador. No hay aprobadores predeterminados.

    Su elección aparecerá en la lista de aprobadores seleccionados.

1. Una vez que haya especificado toda la configuración de su rol, haga clic en **Guardar** para guardar los cambios.

## <a name="next-steps"></a>Pasos siguientes

- [Asignación de roles de recursos de Azure en PIM](pim-resource-roles-assign-roles.md)
- [Configuración de alertas de seguridad para los roles de recurso de Azure en PIM](pim-resource-roles-configure-alerts.md)
