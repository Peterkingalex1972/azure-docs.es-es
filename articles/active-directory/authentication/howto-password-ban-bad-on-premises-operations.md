---
title: 'Operaciones de la protección con contraseña de Azure AD e informes: Azure Active Directory'
description: Operaciones de versión preliminar de la protección con contraseña posterior a la implementación de Azure AD e informes
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca85007bb016cc98d1be61ce08865945e699ad4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60358197"
---
# <a name="azure-ad-password-protection-operational-procedures"></a>Procedimientos operativos de protección con contraseña de Azure AD

Después de haber completado la [instalación de la protección con contraseña de Azure AD](howto-password-ban-bad-on-premises-deploy.md) en el entorno local, hay algunos elementos que se deben configurar en Azure Portal.

## <a name="configure-the-custom-banned-password-list"></a>Configuración de la lista personalizada de contraseñas prohibidas

Siga las instrucciones del artículo [Configuración de la lista personalizada de contraseñas prohibidas](howto-password-ban-bad-configure.md) para conocer los pasos para personalizar la lista de contraseñas prohibidas de su organización.

## <a name="enable-password-protection"></a>Habilitación de la protección con contraseña

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Azure Active Directory**, **Métodos de autenticación** y **Protección con contraseña**.
1. Establezca **Habilitar protección con contraseña en Windows Server Active Directory** en **Sí**.
1. Como se mencionó en la [Guía de implementación](howto-password-ban-bad-on-premises-deploy.md#deployment-strategy), inicialmente se recomienda establecer el **Modo** en **Auditoría**
   * Cuando se sienta cómodo con la característica, puede cambiar el **Modo** a **Forzado**
1. Haga clic en **Guardar**

![Habilitación de componentes de la protección con contraseña de Azure AD en Azure Portal](./media/howto-password-ban-bad-on-premises-operations/authentication-methods-password-protection-on-prem.png)

## <a name="audit-mode"></a>Modo de auditoría

El modo de auditoría está previsto como una forma de ejecutar el software en un modo "what if". Cada servicio de agente de controlador de dominio evalúa una contraseña de entrada según la directiva activa en el momento. Si la directiva activa se ha configurado para estar en modo de auditoría, las contraseñas "incorrectas" se traducen en mensajes del registro de errores pero son aceptadas. Esta es la única diferencia entre el modo de auditoría y el modo forzado; todas las demás operaciones se ejecutan de la misma manera.

> [!NOTE]
> Microsoft recomienda que la implementación y las pruebas iniciales comiencen siempre en el modo auditoría. Los eventos del registro de eventos se deben supervisar después para tantear si alguno de los procesos operativos existentes se vería perturbado una vez que se habilite el modo forzado.

## <a name="enforce-mode"></a>Modo forzado

El modo forzado está previsto como la configuración final. Como en el anterior modo de auditoría, cada servicio de agente de controlador de dominio evalúa las contraseñas de entrada según la directiva activa en el momento. Aunque si el modo forzado está habilitado, una contraseña que se considera no segura según la directiva se rechaza.

Cuando un agente de controlador de dominio de la protección con contraseña de Azure AD DC rechaza una contraseña en modo de aplicación, el impacto visible que percibe un usuario final es idéntico al que percibiría si su contraseña fuese rechazada por la aplicación tradicional de complejidad de la contraseña local. Por ejemplo, un usuario podría ver el siguiente mensaje de error tradicional en la pantalla de inicio de sesión/cambio de contraseña de Windows:

`Unable to update the password. The value provided for the new password does not meet the length, complexity, or history requirements of the domain.`

Este mensaje es solo un ejemplo de varios resultados posibles. El mensaje de error específico puede variar según el escenario o software real que intenta establecer una contraseña no segura.

Puede que los usuarios finales afectados tengan que trabajar con su personal de TI para comprender los nuevos requisitos y tener más capacidad de elegir contraseñas seguras.

## <a name="enable-mode"></a>Modo de habilitación

Por lo general, este ajuste debe dejarse en su estado habilitado predeterminado (Sí). Si se deshabilita (No), todos los agentes de controlador de dominio de protección con contraseña de Azure AD entrarán en modo inactivo y todas las contraseñas se aceptarán tal cual. No se ejecutará ninguna actividad de validación (por ejemplo, ni siquiera se emitirán los eventos de auditoría).

## <a name="next-steps"></a>Pasos siguientes

[Supervisión de la protección de contraseñas de Azure AD](howto-password-ban-bad-on-premises-monitor.md)
