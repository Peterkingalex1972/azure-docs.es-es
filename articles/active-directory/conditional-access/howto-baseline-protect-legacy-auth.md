---
title: 'Directiva de base de referencia: bloqueo de la autenticación heredada (versión preliminar) - Azure Active Directory'
description: Directiva de acceso condicional para bloquear los protocolos de autenticación heredados
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 413df402d5acc878130a47821efd5fa3a512257d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68931945"
---
# <a name="baseline-policy-block-legacy-authentication-preview"></a>Directiva de base de referencia: bloqueo de la autenticación heredada (versión preliminar)

Para brindar a los usuarios un acceso sencillo a las aplicaciones en la nube, Azure Active Directory (Azure AD) admite una amplia variedad de protocolos de autenticación, incluida la autenticación heredada. La autenticación heredada es un término que hace referencia a una solicitud de autenticación realizada por:

* Clientes de Office antiguos que no usan la autenticación moderna (por ejemplo, el cliente de Office 2010)
* Cualquier cliente que use protocolos de correo heredados, como IMAP, SMTP o POP3

Hoy en día, la mayoría de los intentos de inicio de sesión que ponen en peligro la seguridad proceden de la autenticación heredada. La autenticación heredada no admite la autenticación multifactor (MFA). Aunque tenga una directiva MFA habilitada en su directorio, un actor malintencionado puede autenticarse mediante un protocolo heredado y omitir MFA.

La mejor manera de proteger su cuenta frente a las solicitudes de autenticación malintencionadas que realizan los protocolos heredados es bloquear todos estos intentos. En un esfuerzo por facilitar la protección del entorno, hemos creado esta directiva de base de referencia para bloquear la autenticación heredada.

**Bloqueo de la autenticación heredada** es una [directiva de base de referencia](concept-baseline-protection.md) que bloquea todas las solicitudes de autenticación que se hacen mediante protocolos heredados. Todos los usuarios deben usar una autenticación moderna para iniciar sesión correctamente. Si se usa junto con las otras directivas de base de referencia, se bloquearán todas las solicitudes procedentes de protocolos heredados y todos los usuarios deberán usar MFA siempre que sea necesario. Esta directiva no bloquea a Exchange ActiveSync.

## <a name="identify-legacy-authentication-use"></a>Identificación del uso de la autenticación heredada

Para poder bloquear la autenticación heredada en su directorio, primero debe entender si los usuarios tienen aplicaciones que la usen y cómo afecta a su directorio global. Se pueden usar los registros de inicio de sesión de Azure AD para saber si usa la autenticación heredada.

1. Vaya a **Azure Portal** > **Azure Active Directory** > **Inicios de sesión**.
1. Agregue la columna Aplicación cliente si no se muestra; para ello, haga clic en **Columnas** > **Aplicación cliente**.
1. **Agregar filtros** > **Aplicación cliente** > seleccione todas las opciones de **Otros clientes** y haga clic en **Aplicar**.

Al filtrar solo se muestran los intentos de inicio de sesión que se realizaron con protocolos de autenticación heredada. Al hacer clic en cada intento de inicio de sesión individual se muestran detalles adicionales. El campo **Aplicación cliente** en la pestaña **Información básica** indicará qué protocolo de autenticación heredada se usó.

Estos registros indicarán qué usuarios dependen todavía de la autenticación heredada y qué aplicaciones usan protocolos heredados para realizar solicitudes de autenticación. Para los usuarios que no aparecen en estos registros y se confirme que no van a usar la autenticación heredada, implemente una directiva de acceso condicional o habilite la **directiva de base de referencia: bloqueo de la autenticación heredada** solo para estos usuarios.

## <a name="moving-away-from-legacy-authentication"></a>Retirada de la autenticación heredada

Una vez que tenga una idea más clara de quién está usando la autenticación heredada en su directorio y las aplicaciones que dependen de esta, el siguiente paso es actualizar a los usuarios para que usen la autenticación moderna. La autenticación moderna es un método de administración de identidades que ofrece una autenticación y autorización de usuario más seguras. Si tiene una directiva de MFA implementada en su directorio, la autenticación moderna garantiza que MFA se solicita al usuario cuando es necesario. Se trata de la alternativa más segura a los protocolos de autenticación heredados.

En esta sección se ofrece una introducción paso a paso sobre cómo actualizar el entorno para la autenticación moderna. Lea los pasos siguientes antes de habilitar una directiva de bloqueo de la autenticación heredada en su organización.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Paso 1: Habilitación de la autenticación moderna en su directorio

El primer paso para habilitar la autenticación moderna es asegurarse de que el directorio admite la autenticación moderna. La autenticación moderna está habilitada de forma predeterminada para los directorios que creó el día 1 de agosto de 2017 o a partir de esta fecha. Si el directorio se creó antes de esta fecha, deberá habilitar manualmente la autenticación moderna para su directorio mediante los pasos siguientes:

1. Compruebe si el directorio ya admite la autenticación moderna mediante la ejecución de `Get-CsOAuthConfiguration` desde el [módulo de PowerShell de Skype Empresarial en la Web](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell).
1. Si el comando devuelve una propiedad `OAuthServers` vacía, la autenticación moderna se deshabilita. Actualice la configuración para habilitar la autenticación moderna mediante `Set-CsOAuthConfiguration`. Si su propiedad `OAuthServers` contiene una entrada, significa que está listo para empezar.

Asegúrese de completar este paso antes de continuar. Es fundamental cambiar primero las configuraciones de directorio, ya que estas dictan qué protocolo usarán todos los clientes de Office. Aunque use clientes de Office que admitan la autenticación moderna, usarán de manera predeterminada protocolos heredados si la autenticación moderna está deshabilitada en su directorio.

### <a name="step-2-office-applications"></a>Paso 2: Aplicaciones de Office

Una vez que haya habilitado la autenticación moderna en su directorio, puede empezar a actualizar las aplicaciones mediante la habilitación de la autenticación moderna para clientes de Office. Office 2016 o los clientes posteriores admiten la autenticación moderna de forma predeterminada. No se requiere ningún paso adicional.

Si usa clientes de Windows con Office 2013 o una versión anterior, se recomienda actualizar a Office 2016 o posterior. Incluso después de completar el paso anterior para habilitar la autenticación moderna en su directorio, las aplicaciones de Office anteriores seguirán usando protocolos de autenticación heredados. Si usa clientes de Office 2013 y no puede actualizar inmediatamente a Office 2016 o posterior, siga los pasos descritos en el siguiente artículo para [Habilitar la autenticación moderna para Office 2013 en los dispositivos Windows](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication). Para ayudar a proteger su cuenta mientras usa la autenticación heredada, le recomendamos que use contraseñas seguras en su directorio. Eche un vistazo al artículo sobre [protección con contraseña de Azure AD](../authentication/concept-password-ban-bad.md) para prohibir las contraseñas no seguras en su directorio.

Office 2010 no admite la autenticación moderna. Deberá actualizar todos los usuarios con Office 2010 a una versión más reciente de Office. Le recomendamos que actualice a Office 2016 o posterior, ya que bloquea la autenticación heredada de forma predeterminada.

Si usa MacOS, le recomendamos que actualice a Office para Mac 2016 o posterior. Si usa el cliente de correo electrónico nativo, deberá tener la versión de MacOS 10.14 o posterior en todos los dispositivos.

### <a name="step-3-exchange-and-sharepoint"></a>Paso 3: Exchange y SharePoint

Para que los clientes de Outlook basado en Windows usen la autenticación moderna, Exchange Online también debe tener habilitada la autenticación moderna. Si se deshabilita la autenticación moderna para Exchange Online, los clientes de Outlook basado en Windows que admiten la autenticación moderna (Outlook 2013 o versiones posteriores) usarán la autenticación básica para conectarse a buzones de Exchange Online.

SharePoint Online está habilitado de forma predeterminada para la autenticación moderna. En el caso de los directorios creados a partir del 1 de agosto de 2017, la autenticación moderna está habilitada de forma predeterminada en Exchange Online. Sin embargo, si anteriormente tenía deshabilitada la autenticación moderna o usa un directorio creado antes de esta fecha, siga los pasos descritos en el artículo [Habilitar la autenticación moderna en Exchange Online](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online).

### <a name="step-4-skype-for-business"></a>Paso 4: Skype Empresarial

Para evitar las solicitudes de autenticación heredadas realizadas por Skype Empresarial, es necesario habilitar la autenticación moderna para Skype Empresarial en la Web. En el caso de los directorios creados a partir del 1 de agosto de 2017, la autenticación moderna está habilitada de forma predeterminada en Skype Empresarial.

Le sugerimos que realice la transición a Microsoft Teams, que admite la autenticación moderna de forma predeterminada. Sin embargo, si no puede migrar en este momento, deberá habilitar la autenticación moderna para Skype Empresarial en la Web para que Skype Empresarial empiece a usar la autenticación moderna. Consulte los pasos del artículo [Skype for Business topologies supported with Modern Authentication](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported) (Topologías de Skype Empresarial admitidas con la autenticación moderna) para habilitar la autenticación moderna para Skype Empresarial.

Además de habilitar la autenticación moderna para Skype Empresarial en la Web, le recomendamos que habilite la autenticación moderna para Exchange Online cuando habilite la autenticación moderna para Skype Empresarial. Este proceso permitirá sincronizar el estado de la autenticación moderna en Exchange Online y Skype Empresarial en la Web y evitará que se realicen varias solicitudes de inicio de sesión de los clientes de Skype Empresarial.

### <a name="step-5-using-mobile-devices"></a>Paso 5: Uso de dispositivos móviles

Las aplicaciones de su dispositivo móvil también necesitan bloquear la autenticación heredada. Le recomendamos usar Outlook para dispositivos móviles. Outlook para móviles admite la autenticación moderna de forma predeterminada y cumplirá otras directivas de protección de la base de referencia de MFA.

Para usar el cliente de correo electrónico nativo de iOS, deberá ejecutar la versión 11.0 u otra posterior de iOS para asegurarse de que el cliente de correo electrónico se ha actualizado a fin de bloquear la autenticación heredada.

### <a name="step-6-on-premises-clients"></a>Paso 6: Clientes locales

Si es un cliente híbrido que usa Exchange Server y Skype Empresarial en el entorno local, ambos servicios deberán actualizarse para habilitar la autenticación moderna. Cuando usa la autenticación moderna en un entorno híbrido, igualmente autentica a los usuarios en el entorno local. Sin embargo, el proceso para autorizar su acceso a los recursos (archivos o mensajes de correo electrónico) cambia.

Antes de poder empezar a habilitar la autenticación moderna en el entorno local, asegúrese de que cumple los requisitos previos.
Ahora está listo para habilitar la autenticación moderna en el entorno local.

Los pasos para habilitar la autenticación moderna se encuentran en los artículos siguientes:

* [Cómo configurar Exchange Server local para usar la autenticación moderna híbrida](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Cómo usar la autenticación moderna (ADAL) con Skype Empresarial](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="enable-the-baseline-policy"></a>Habilitación de la directiva de base de referencia

La **directiva de base de referencia: bloqueo de la autenticación heredada (versión preliminar)** viene preconfigurada y se mostrará en la parte superior cuando navegue a la hoja Acceso condicional en Azure Portal. Esta configuración solo surte efecto después de un inicio de sesión correcto, de modo que los usuarios seguirán teniendo la opción de intentar usar la autenticación heredada.

Para habilitar esta directiva y proteger a su organización:

1. Inicie sesión en  **Azure Portal** con uno de los siguientes roles: Administrador global, Administrador de seguridad o Administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Acceso condicional**.
1. En la lista de directivas, seleccione **Baseline policy: Block legacy authentication (preview)** (Directiva de base de referencia: Bloquear la autenticación heredada [versión preliminar]).
1. Establezca **Habilitar directiva** en **Usar la directiva inmediatamente**.
1. Haga clic en  **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

* [Directivas de protección de base de referencia de acceso condicional](concept-baseline-protection.md)
* [Cinco pasos para asegurar su infraestructura de identidad](../../security/fundamentals/steps-secure-identity.md)
* [¿Qué es el acceso condicional en Azure Active Directory?](overview.md)
