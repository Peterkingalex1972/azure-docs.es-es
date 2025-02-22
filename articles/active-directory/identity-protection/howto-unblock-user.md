---
title: Desbloqueo de usuarios con Azure Active Directory Identity Protection | Microsoft Docs
description: Aprenda a desbloquear usuarios bloqueados por una directiva de Azure Active Directory Identity Protection.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c28e30b8e44b6888cdb7416b9c7b563b955a68ce
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335383"
---
# <a name="how-to-unblock-users"></a>Instrucciones: Desbloqueo de usuarios

Con Azure Active Directory Identity Protection se pueden configurar directivas para bloquear a los usuarios si se cumplen las condiciones configuradas. Normalmente, los usuarios bloqueados se ponen en contacto con el departamento de soporte técnico para que se desbloqueen. En este artículo se explican los pasos que puede seguir para desbloquear un usuario bloqueado.

## <a name="determine-the-reason-for-blocking"></a>Determinación del motivo de bloqueo

Como primer paso para desbloquear un usuario, es preciso que determine el tipo de directiva que ha bloqueado al usuario, ya que los siguientes pasos dependen de él.
Con Azure Active Directory Identity Protection, se puede bloquear a un usuario con una directiva de riesgo de inicio de sesión o con una directiva de riesgo de usuario.

Puede obtener el tipo de directiva que ha bloqueado un usuario del título del cuadro de diálogo que se presentó al usuario en un intento de inicio de sesión:

| Directiva | Cuadro de diálogo usuario |
| --- | --- |
| Riesgo de inicio de sesión |![Inicio de sesión bloqueado](./media/howto-unblock-user/02.png) |
| Riesgo de usuario |![Cuenta bloqueada](./media/howto-unblock-user/104.png) |

Un usuario que está bloqueado por:

* Una directiva de riesgo de inicio de sesión también es conocida como inicio de sesión sospechoso
* Una directiva de riesgo de usuario también es conocida como una cuenta en riesgo

## <a name="unblocking-suspicious-sign-ins"></a>Desbloqueo de inicios de sesión sospechosos

Para desbloquear un inicio de sesión sospechoso, tiene las siguientes opciones:

1. **Iniciar sesión desde una ubicación o dispositivo conocidos**: una razón común para el bloqueo de inicios de sesión sospechosos es que se intente iniciar sesión desde ubicaciones o dispositivos desconocidos. Los usuarios pueden determinar rápidamente si esta es la razón del bloqueo. Para ello, deben intentar iniciar sesión desde una ubicación o dispositivo conocidos.
2. **Excluir de la directiva** : si piensa que la configuración actual de la directiva de inicio de sesión provoca problemas a usuarios concretos, puede excluir los usuarios de ella. Para obtener más información, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
3. **Deshabilitar directiva** : si piensa que la configuración de la directiva provoca problemas a todos los usuarios, puede deshabilitar la directiva. Para obtener más información, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="unblocking-accounts-at-risk"></a>Desbloqueo de cuentas en riesgo

Para desbloquear una cuenta en riesgo, tiene las siguientes opciones:

1. **Restablecer contraseña** : puede restablecer la contraseña del usuario. 
2. **Descartar todos los eventos de riesgo** : la directiva de riesgo de usuario bloquea un usuario si se ha alcanzado el nivel de riesgo del usuario configurado para bloquear el acceso. Para reducir el nivel de riesgo de un usuario, cierre manualmente los eventos de riesgo notificados. 
3. **Excluir de la directiva** : si piensa que la configuración actual de la directiva de inicio de sesión provoca problemas a usuarios concretos, puede excluir los usuarios de ella. Para obtener más información, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
4. **Deshabilitar directiva** : si piensa que la configuración de la directiva provoca problemas a todos los usuarios, puede deshabilitar la directiva. Para obtener más información, consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).

## <a name="next-steps"></a>Pasos siguientes
 
¿Desea más información acerca de Azure AD Identity Protection? Consulte [Azure Active Directory Identity Protection](../active-directory-identityprotection.md).
