---
title: 'Aprobación o rechazo de solicitudes de roles de Azure AD en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a aprobar o rechazar solicitudes para los roles de Azure AD en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
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
ms.openlocfilehash: f83cb38567feb51ba7959ada7730d66ded677bf9
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476532"
---
# <a name="approve-or-deny-requests-for-azure-ad-roles-in-pim"></a>Aprobación o rechazo de solicitudes para los roles de Azure AD en PIM

Con Azure Active Directory (Azure AD) Privileged Identity Management (PIM), puede configurar roles para requerir la aprobación para la activación y elegir uno o varios usuarios o grupos como aprobadores delegados. Los aprobadores delegados tienen 24 horas para aprobar las solicitudes. Si no se aprueba una solicitud en un plazo de 24 horas, el usuario apto debe volver a enviar una nueva solicitud. El período de tiempo de aprobación de 24 horas no es configurable.

Siga los pasos que se describen en este artículo para aprobar o denegar solicitudes para los roles de Azure AD.

## <a name="view-pending-requests"></a>Ver solicitudes en espera

Como aprobador delegado, recibirá una notificación por correo electrónico cuando una solicitud de rol de Azure AD está pendiente de su aprobación. Puede ver estas solicitudes pendientes en PIM.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

1. Abra **Azure AD Privileged Identity Management**.

1. Haga clic en **Roles de Azure AD**.

1. Haga clic en **Aprobar solicitudes**.

    ![Roles de Azure AD: Aprobar solicitudes](./media/azure-ad-pim-approval-workflow/pim-directory-roles-approve-requests.png)

    Verá una lista de solicitudes pendientes de su aprobación.

## <a name="approve-requests"></a>Aprobar solicitudes

1. Seleccione las solicitudes que quiera aprobar y luego haga clic en **Aprobar** para abrir el panel Aprobar las solicitudes seleccionadas.

    ![Lista de Aprobar solicitudes con la opción Aprobar resaltada](./media/azure-ad-pim-approval-workflow/pim-approve-requests-list.png)

1. En el cuadro **Razón de la aprobación**, escriba una razón.

    ![Panel de Aprobar las solicitudes seleccionadas con una razón de la aprobación](./media/azure-ad-pim-approval-workflow/pim-approve-selected-requests.png)

1. Haga clic en **Approve** (Aprobar).

    El símbolo de estado se actualizará con la aprobación.

    ![Panel de Aprobar las solicitudes seleccionadas tras hacer clic en el botón Aprobar](./media/azure-ad-pim-approval-workflow/pim-approve-status.png)

## <a name="deny-requests"></a>Denegar solicitudes

1. Seleccione las solicitudes que quiera denegar y luego haga clic en **Denegar** para abrir el panel Denegar las solicitudes seleccionadas.

    ![Lista de Aprobar solicitudes con la opción Denegar resaltada](./media/azure-ad-pim-approval-workflow/pim-deny-requests-list.png)

1. En el cuadro **Razón de la denegación**, escriba una razón.

    ![Panel de Aprobar las solicitudes seleccionadas con una razón de la denegación](./media/azure-ad-pim-approval-workflow/pim-deny-selected-requests.png)

1. Haga clic en **Denegar**.

    El símbolo de estado se actualizará con la denegación.

## <a name="next-steps"></a>Pasos siguientes

- [Notificaciones por correo electrónico en PIM](pim-email-notifications.md)
- [Aprobación o rechazo de solicitudes de roles de recursos de Azure en PIM](pim-resource-roles-approval-workflow.md)
