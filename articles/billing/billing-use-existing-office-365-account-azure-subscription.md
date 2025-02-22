---
title: Suscripción a Azure con la cuenta de Office 365 | Microsoft Docs
description: Obtenga información acerca de cómo crear una suscripción de Azure mediante una cuenta de Office 365
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 44ff08c84b3e95a5b598ebf4279fc2ffc46313e3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369521"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrarse para obtener una suscripción de Azure con su cuenta de Office 365
Si tiene una suscripción de Office 365, puede usar su cuenta de Office 365 para crear una suscripción de Azure. Inicie sesión en [Azure Portal](https://portal.azure.com/) con su nombre de usuario y contraseña de Office 365. Si desea configurar máquinas virtuales o usar otros servicios de Azure, debe suscribirse a una suscripción de Azure. Puede compartir su suscripción de Azure con otras personas y [usar el control de acceso basado en roles para administrar el acceso a su suscripción de Azure y a sus recursos](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Si ya tiene una cuenta de Office 365 y una suscripción de Azure, consulte [Asociación de un inquilino de Office 365 a una suscripción de Azure](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obtención de una suscripción de Azure con su cuenta de Office 365

Ahorre tiempo y evite la proliferación de cuentas al suscribirse a Azure con el nombre de usuario y la contraseña de Office 365. 

1. Suscríbase en [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Inicie sesión con su nombre de usuario y contraseña de Office 365. La cuenta que use no necesita tener permisos de administrador. Si tiene más de una cuenta de Office 365, asegúrese de utilizar las credenciales de la cuenta de Office 365 que desea asociar a su suscripción de Azure. 

   ![Captura de pantalla que muestra la página de registro.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Escriba la información necesaria para completar el proceso de suscripción. Puede que alguna información no sea necesaria si ya tiene una cuenta de Office 365.

    ![Captura de pantalla que muestra el formulario de registro.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Si necesita agregar otras personas de su organización a la suscripción de Azure, consulte [Introducción a la administración de acceso en Azure Portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Más información acerca de las suscripciones de Azure y Office 365</a>
Office 365 y Azure usan el servicio Azure Active Directory para administrar usuarios y suscripciones. El directorio de Azure es como un contenedor en el que puede agrupar usuarios y suscripciones. Para poder usar las mismas cuentas de usuario con sus suscripciones de Azure y Office 365, asegúrese de que las suscripciones de Azure se hayan creado en el mismo directorio que las de Office 365. Tenga en cuenta los siguientes puntos:

* Una suscripción se crea en un directorio
* Los usuarios pertenecen a directorios
* Una suscripción se incluye en el directorio del usuario que la crea. Por lo que la suscripción a Office 365 está asociada a la misma cuenta que la suscripción de Azure.
* Las suscripciones de Azure son propiedad de usuarios individuales del directorio
* Las suscripciones de Office 365 pertenecen al propio directorio. Los usuarios con los permisos adecuados en el directorio pueden administrar estas suscripciones.

![Captura de pantalla que muestra la relación entre el directorio, los usuarios y las suscripciones.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Para más información, consulte [Asociación de las suscripciones de Azure con Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).