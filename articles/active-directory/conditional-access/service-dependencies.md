---
title: ¿Cuáles son las dependencias de servicio del acceso condicional de Azure Active Directory? | Microsoft Docs
description: Sepa cómo se usan las condiciones en el acceso condicional de Azure Active Directory para desencadenar una directiva.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9aca2e4ea5e107358ff72e83562057830ece2cc
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509350"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>¿Cuáles son las dependencias de servicio del acceso condicional de Azure Active Directory? 

Con las directivas de acceso condicional, puede especificar requisitos de acceso para sitios web y servicios. Por ejemplo, los requisitos de acceso pueden incluir el uso obligatorio de la autenticación multifactor (MFA) o [dispositivos administrados](require-managed-devices.md). 

Cuando se accede a un sitio o a un servicio directamente, suele ser fácil evaluar el impacto de una directiva relacionada. Por ejemplo, si tiene una directiva que requiere tener configurado MFA para SharePoint Online, MFA se aplica en cada inicio de sesión al portal web de SharePoint. Sin embargo, no siempre resulta sencillo evaluar el impacto de una directiva porque hay aplicaciones en la nube con dependencias de otras aplicaciones en la nube. Por ejemplo, Microsoft Teams puede proporcionar acceso a los recursos de SharePoint Online. Por lo tanto, cuando accede a Microsoft Teams en nuestro escenario actual, también está sujeto a la directiva de MFA de SharePoint.   

## <a name="policy-enforcement"></a>Aplicación de directivas 

Si tiene una dependencia de servicio configurada, la directiva puede aplicarse mediante el cumplimiento enlazado en tiempo de compilación o en tiempo de ejecución. 

- El **cumplimiento de directivas enlazadas en tiempo de compilación** significa que un usuario debe satisfacer la directiva de servicio dependiente para acceder a la aplicación que realiza la llamada. Por ejemplo, un usuario debe satisfacer la directiva de SharePoint antes de iniciar sesión en MS Teams. 
- El **cumplimiento de directivas enlazadas en tiempo de ejecución** se produce después de que el usuario inicia sesión en la aplicación que realiza la llamada. El cumplimiento se aplaza hasta que la aplicación que realiza la llamada solicita un token para el servicio de nivel inferior. Algunos ejemplos son el acceso de MS Teams a Planner y el acceso de Office.com a SharePoint. 

En el diagrama siguiente se ilustran las dependencias del servicio MS Teams. Las flechas continuas indican el cumplimiento enlazado en tiempo de compilación y la flecha discontinua para Planner indica el cumplimiento enlazado en tiempo de ejecución. 

![Dependencias del servicio MS Teams](./media/service-dependencies/01.png)

Como procedimiento recomendado, debe establecer directivas comunes en aplicaciones y servicios relacionados siempre que sea posible. Tener una posición de seguridad coherente proporciona la mejor experiencia de usuario. Por ejemplo, establecer una directiva común en Exchange Online, SharePoint Online, Microsoft Teams y Skype Empresarial reduce considerablemente los mensajes inesperados que pueden surgir por la aplicación de diferentes directivas a servicios de nivel inferior. 

En la tabla siguiente se enumeran las dependencias de servicio adicionales que deben satisfacer las aplicaciones cliente.  

| Aplicaciones cliente         | Servicio de nivel inferior                          | Cumplimiento |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Administración de Microsoft Azure (portal y API) | Con enlace en tiempo de compilación |
| Microsoft Classroom | Exchange                                    | Con enlace en tiempo de compilación |
|                     | SharePoint                                  | Con enlace en tiempo de compilación  |
| Equipos de Microsoft     | Exchange                                    | Con enlace en tiempo de compilación |
|                     | MS Planner                                  | Con enlace en tiempo de ejecución  |
|                     | SharePoint                                  | Con enlace en tiempo de compilación |
|                     | Skype Empresarial Online                   | Con enlace en tiempo de compilación |
| Portal de Office       | Exchange                                    | Con enlace en tiempo de ejecución  |
|                     | SharePoint                                  | Con enlace en tiempo de ejecución  |
| Outlook Groups      | Exchange                                    | Con enlace en tiempo de compilación |
|                     | SharePoint                                  | Con enlace en tiempo de compilación |
| PowerApps           | Administración de Microsoft Azure (portal y API) | Con enlace en tiempo de compilación |
|                     | Microsoft Azure Active Directory              | Con enlace en tiempo de compilación |
| proyecto             | Dynamics CRM                                | Con enlace en tiempo de compilación |
| Skype Empresarial  | Exchange                                    | Con enlace en tiempo de compilación |
| Visual Studio       | Administración de Microsoft Azure (portal y API) | Con enlace en tiempo de compilación |

## <a name="next-steps"></a>Pasos siguientes

Para saber cómo implementar el acceso condicional en su entorno, consulte [Planeamiento de la implementación del acceso condicional en Azure Active Directory](plan-conditional-access.md).
