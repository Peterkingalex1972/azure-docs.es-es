---
title: Establecimiento de la expiración de grupos de Office 365 en Azure Active Directory | Microsoft Docs
description: Configuración de la expiración de grupos de Office 365 en Azure Active Directory
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
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 074b9ec06818363a97253a587ac451a38999832f
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837923"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configuración de la directiva de expiración de grupos de Office 365

En este artículo se indica cómo puede administrar el ciclo de vida de los grupos de Office 365 mediante el establecimiento de una directiva de expiración. Puede configurar la directiva de expiración solo para grupos de Office 365 en Azure Active Directory (Azure AD).

Una vez establecido un grupo para establecer la expiración:

- Los propietarios del grupo reciben una notificación para renovar el grupo ya que se acerca la expiración
- Se eliminará cualquier grupo que no se renueve
- Los propietarios del grupo o el administrador pueden restaurar, dentro de un plazo de 30 días, los grupos de Office 365 que se hayan eliminado

Actualmente, solo se puede configurar una directiva de expiración para grupos de Office 365 en un inquilino.

> [!NOTE]
> Para configurar y usar la directiva de expiración en grupos de Office 365, es necesario poseer las licencias de Azure AD Premium de todos los miembros de los grupos a los que se aplica la directiva de expiración.

Para más información sobre cómo descargar e instalar los cmdlets de PowerShell de Azure AD, consulte [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell para Graph 2.0.0.137).

## <a name="roles-and-permissions"></a>Roles y permisos

Estos son los roles en los que puede configurar y usar la expiración de grupos de Office 365 en Azure AD.

Rol | Permisos
-------- | --------
administrador global o administrador de usuarios. | Puede crear, consultar, actualizar o eliminar la configuración de la directiva de expiración de grupos de Office 365.<br>Puede renovar cualquier grupo de Office 365
Usuario | Puede renovar un grupo de Office 365 de su propiedad.<br>Puede restaurar un grupo de Office 365 de su propiedad.<br>Puede leer la configuración de la directiva de expiración

Si necesita más información sobre los permisos para restaurar los grupos eliminados, consulte [Restauración de un grupo eliminado de Office 365 en Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Establecimiento de la expiración de grupo

1. Abra el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta que tenga el rol de administrador global en la organización de Azure AD.

2. Seleccione **Grupos** y, luego, **Expiración** para abrir la configuración de expiración.
  
   ![Configuración de expiración para grupos](./media/groups-lifecycle/expiration-settings.png)

3. En la página **Expiración**, puede:

  - Establecer la duración del grupo en días. Puede seleccionar uno de los valores predeterminados o un valor personalizado (debe ser de 31 días o más).
  - Especificar una dirección de correo electrónico a la que deben enviarse notificaciones de renovación y expiración cuando el grupo no tiene ningún propietario.
  - Seleccionar los grupos de Office 365 que expiran. Puede establecer la expiración de:
    - **Todos** los grupos de Office 365
    - Una lista de grupos **seleccionados** de Office 365
    - **Ninguno** para restringir la expiración de todos los grupos
  - Guardar la configuración cuando haya terminado seleccionando **Guardar**.

> [!NOTE]
> Cuando se configura por primera vez la expiración, todos los grupos que sean más antiguos que el intervalo de expiración se establecen en 30 días hasta la expiración. El primer correo de notificación de renovación se envía en un día. Por ejemplo, el grupo A se creó hace 400 días y el intervalo de expiración se ha establecido en 180 días. Cuando se aplica la directiva de expiración, el grupo A tiene 30 días antes de que se elimine, salvo que el propietario lo renueve.
> Cuando un grupo dinámico se elimina y restaura, se considera un nuevo grupo y se vuelve a rellenar de acuerdo con la regla. Este proceso puede tardar hasta 24 horas.

## <a name="email-notifications"></a>Notificaciones por correo electrónico

Se envían notificaciones por correo electrónico como esta a los propietarios del grupo de Office 365 con 30 días, 15 días y 1 día de antelación a la expiración del grupo. El idioma del correo electrónico viene determinado por el idioma preferido del propietario o la configuración de idioma de Azure AD. Si el propietario del grupo ha definido un idioma preferido o varios propietarios tienen el mismo idioma preferido, se usa ese idioma. En todos los demás casos, se utilizará la configuración de idioma de Azure AD.

![Notificaciones por correo electrónico de expiración](./media/groups-lifecycle/expiration-notification.png)

En el correo electrónico de notificación **Renovar grupo**, los propietarios de los grupos pueden acceder directamente a la página de detalles del grupo en el Panel de acceso. Ahí, los usuarios pueden obtener más información sobre el grupo, como su descripción, cuando se renovó por última vez, cuándo expira y la posibilidad de renovarlo. La página de detalles del grupo ahora incluye también vínculos a los recursos de grupos de Office 365, así el propietario del grupo puede ver cómodamente el contenido y la actividad de su grupo.

Cuando un grupo expira, el grupo se elimina un día después de la fecha de expiración. Se envía una notificación por correo electrónico como esta a los propietarios del grupo de Office 365 donde se informa sobre la expiración y la posterior eliminación del grupo de Office 365.

![Notificaciones por correo electrónico de eliminación del grupo](./media/groups-lifecycle/deletion-notification.png)

El grupo se puede restaurar a los 30 días de su eliminación mediante la selección de **Restaurar grupo** o por medio de los cmdlets de PowerShell, tal y como se describe en [Restauración de un grupo eliminado de Office 365 en Azure Active Directory](groups-restore-deleted.md). Tenga en cuenta que el período de 30 días de restauración del grupo no es personalizable.

Si el grupo que restaura contiene documentos, sitios de SharePoint u otros objetos persistentes, el proceso completo de restauración del grupo y su contenido podría demorar hasta 24 horas.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Cómo recuperar la fecha de expiración del grupo de Office 365
Además de a través del Panel de acceso, donde los usuarios pueden ver los detalles del grupo (incluida la fecha de expiración y la última fecha de renovación), la fecha de expiración de un grupo de Office 365 puede obtenerse mediante la versión beta de la API de REST de Microsoft Graph. Se ha habilitado expirationDateTime como una propiedad de grupo en la versión beta de Microsoft Graph y puede recuperarse con una solicitud GET. Para obtener más información, consulte [este ejemplo](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Para administrar las pertenencias a grupos en el Panel de acceso, la opción "Restringir el acceso a grupos en el Panel de acceso" debe establecerse en "No" en la configuración general de grupos de Azure Active Directory.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Cómo funciona la expiración de grupos de Office 365 con un buzón en suspensión legal
Cuando un grupo expira y se elimina, 30 días después de su eliminación se eliminan de forma permanente los datos del grupo de aplicaciones como Planner, Sitios o Equipos, pero el buzón del grupo que está en suspensión legal se conserva y no se elimina de forma permanente. El administrador puede usar cmdlets de Exchange para restaurar el buzón para capturar los datos. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Cómo funciona la expiración de grupos de Office 365 con la directiva de retención
La directiva de retención se configura por medio del Centro de seguridad y cumplimiento. Si ha configurado una directiva de retención para grupos de Office 365, cuando un grupo expira y se elimina, sus conversaciones en el buzón de correo del grupo y los archivos en el sitio del grupo se conservan en el contenedor de retención durante los días especificados en la directiva de retención. Los usuarios no verán el grupo ni su contenido tras la expiración, pero pueden recuperar los datos del sitio y del buzón mediante eDiscovery.

## <a name="powershell-examples"></a>Ejemplos de PowerShell
Estos son ejemplos de cómo puede usar cmdlets de PowerShell para configurar los valores de expiración de los grupos de Office 365 en su organización de Azure AD:

1. Instale el módulo de PowerShell v2.0 e inicie sesión en el símbolo del sistema de PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configure los valores de expiración. Use el cmdlet New-AzureADMSGroupLifecyclePolicy para establecer la duración de todos los grupos de Office 365 de la organización de Azure AD en 365 días. Las notificaciones de renovación de los grupos de Office 365 sin propietario se envían a "emailaddress@contoso.com".
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recupere la directiva existente Get-AzureADMSGroupLifecyclePolicy: este cmdlet recupera los valores actuales de expiración de los grupos de Office 365 que se han configurado. En este ejemplo, puede ver:

   - El identificador de directiva.
   - La duración de todos los grupos de Office 365 en la organización de Azure AD se establece en 365 días.
   - Las notificaciones de renovación de grupos de Office 365 sin propietario se envían a "emailaddress@contoso.com".
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Actualice la directiva existente Set-AzureADMSGroupLifecyclePolicy: Este cmdlet se usa para actualizar una directiva existente. En el ejemplo siguiente, se cambia la duración del grupo de la directiva existente de 365 días a 180 días.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Agregue grupos específicos a la directiva Add-AzureADMSLifecyclePolicyGroup: este cmdlet agrega un grupo a la directiva de ciclo de vida. Por ejemplo:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Elimine la directiva existente Remove-AzureADMSGroupLifecyclePolicy: Este cmdlet elimina la configuración de expiración del grupo de Office 365, pero requiere el identificador de directiva. Esto deshabilita la expiración de grupos de Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
Los siguientes cmdlets se pueden usar para configurar la directiva con más detalle. Para más información, consulte la [documentación de PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Pasos siguientes

En estos artículos se proporciona información adicional sobre los grupos de Azure AD.

- [Consulta de los grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Administración de la configuración de un grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Administrar miembros de un grupo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Administrar la pertenencia a grupos](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Administrar reglas dinámicas de los usuarios de un grupo](groups-dynamic-membership.md)
