---
title: 'Creación de un grupo de hosts de Windows Virtual Desktop (versión preliminar) con PowerShell: Azure'
description: Cómo crear un grupo de hosts en Windows Virtual Desktop (versión preliminar) con cmdlets de PowerShell.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: helohr
ms.openlocfilehash: 1c365790e1633a74be9f5baf41098e7511f99a7d
ms.sourcegitcommit: 39d95a11d5937364ca0b01d8ba099752c4128827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69563276"
---
# <a name="create-a-host-pool-with-powershell"></a>Creación de un grupo host con PowerShell

Los grupos de hosts son una colección de una o más máquinas virtuales idénticas en entornos de inquilinos de Windows Virtual Desktop (versión preliminar). Cada grupo de hosts puede contener un grupo de aplicaciones con las que los usuarios pueden interactuar igual que harían en un equipo de escritorio físico.

## <a name="use-your-powershell-client-to-create-a-host-pool"></a>Uso del cliente de PowerShell para crear un grupo hosts

En primer lugar y, si aún no lo ha hecho, [descargue e importe el módulo de PowerShell para Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) que se usará en la sesión de PowerShell.

Ejecute el siguiente cmdlet para iniciar sesión en el entorno de Windows Virtual Desktop.

```powershell
Add-RdsAccount -DeploymentUrl https://rdbroker.wvd.microsoft.com
```

A continuación, ejecute este cmdlet para crear un nuevo grupo de hosts en el inquilino de Windows Virtual Desktop.

```powershell
New-RdsHostPool -TenantName <tenantname> -Name <hostpoolname>
```

Ejecute el siguiente cmdlet para crear un token de registro para autorizar que un host de sesión se una el grupo de hosts y guárdelo en un archivo nuevo en el equipo local. Puede especificar cuánto tiempo es válido el token de registro mediante el parámetro -ExpirationHours.

```powershell
New-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname> -ExpirationHours <number of hours> | Select-Object -ExpandProperty Token > <PathToRegFile>
```

Después, ejecute este cmdlet para agregar usuarios de Azure Active Directory al grupo de aplicaciones de escritorio predeterminado para el grupo de hosts.

```powershell
Add-RdsAppGroupUser -TenantName <tenantname> -HostPoolName <hostpoolname> -AppGroupName "Desktop Application Group" -UserPrincipalName <userupn>
```

El cmdlet **Add-RdsAppGroupUser** no admite la adición de grupos de seguridad y solo agrega un usuario a la vez al grupo de aplicaciones. Si quiere agregar varios usuarios al grupo de aplicaciones, vuelva a ejecutar el cmdlet con los nombres principales de usuario adecuados.

Ejecute el siguiente cmdlet para exportar el token de registro a una variable, que usará más adelante en el [Registro de máquinas virtuales en el grupo de hosts de Windows Virtual Desktop](#register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool).

```powershell
$token = (Export-RdsRegistrationInfo -TenantName <tenantname> -HostPoolName <hostpoolname>).Token
```

## <a name="create-virtual-machines-for-the-host-pool"></a>Creación de máquinas virtuales para el grupo de hosts

Ahora puede crear una máquina virtual de Azure que puede unirse al grupo de hosts de Windows Virtual Desktop.

Puede crear una máquina virtual de varias maneras:

- [Crear una máquina virtual desde una imagen de la galería de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#create-virtual-machine)
- [Crear una máquina virtual desde una imagen administrada](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-generalized-managed)
- [Crear una máquina virtual desde una imagen no administrada](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image)

Una vez que haya creado las máquinas virtuales de host de sesión, [aplique una licencia de Windows a una máquina virtual de host de sesión](./apply-windows-license.md#apply-a-windows-license-to-a-session-host-vm) para ejecutar las máquinas virtuales Windows o Windows Server sin pagar por otra licencia. 

## <a name="prepare-the-virtual-machines-for-windows-virtual-desktop-preview-agent-installations"></a>Preparación de las máquinas virtuales para las instalaciones de los agentes de Windows Virtual Desktop (versión preliminar)

Deberá hacer lo siguiente para preparar las máquinas virtuales antes de poder instalar los agentes de Windows Virtual Desktop y registrar las máquinas virtuales en el grupo de hosts de Windows Virtual Desktop:

- Debe unir la máquina al dominio. Esto permite que los usuarios entrantes de Windows Virtual Desktop se asignen desde sus cuentas de Azure Active Directory a su cuenta de Active Directory, así como tener acceso correctamente a la máquina virtual.
- Debe instalar el rol de host de sesión de Escritorio remoto (RDSH) si la máquina virtual está ejecutando un sistema operativo de Windows Server. El rol de RDSH permite que los agentes de Windows Virtual Desktop se instalen correctamente.

Para realizar correctamente una unión a un dominio, realice los siguientes pasos en cada máquina virtual:

1. [Conéctese a la máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con las credenciales que proporcionó al crear la máquina virtual.
2. En la máquina virtual, inicie el **Panel de control** y seleccione **Sistema**.
3. Seleccione **Nombre del equipo**, seleccione **Cambiar configuración** y, luego, seleccione **Cambiar…** .
4. Seleccione **Dominio** y, luego, escriba el dominio de Active Directory en la red virtual.
5. Autentíquese con una cuenta de dominio que tenga privilegios en máquinas unidas a dominio.

    >[!NOTE]
    > Si va a unir sus máquinas virtuales a un entorno de Azure Active Directory Domain Services, asegúrese de que su usuario de unión a un dominio también es miembro del [grupo de administradores de controlador de dominio de AAD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

## <a name="register-the-virtual-machines-to-the-windows-virtual-desktop-preview-host-pool"></a>Registro de las máquinas virtuales en el grupo de hosts de Windows Virtual Desktop (versión preliminar)

El registro de las máquinas virtuales en un grupo de hosts de Windows Virtual Desktop es tan sencillo como instalar los agentes de Windows Virtual Desktop.

Para registrar los agentes de Windows Virtual Desktop, realice los siguientes pasos en cada máquina virtual:

1. [Conéctese a la máquina virtual](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal#connect-to-virtual-machine) con las credenciales que proporcionó al crear la máquina virtual.
2. Descargue e instale el agente de Windows Virtual Desktop.
   - Descargue el [agente de Windows Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrmXv).
   - Haga clic en el instalador descargado, seleccione **Propiedades**, seleccione **Desbloquear** y, a continuación, seleccione **Aceptar**. Esto permitirá que el sistema confíe en el instalador.
   - Ejecute al programa de instalación. Cuando el instalador le solicite el token de registro, escriba el valor obtenido del cmdlet **Export-RdsRegistrationInfo**.
3. Descargue e instale el cargador de arranque del agente de Windows Virtual Desktop.
   - Descargue el [cargador de arranque del agente de Windows Virtual Desktop](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RWrxrH).
   - Haga clic con el botón derecho en el instalador descargado, seleccione **Propiedades**, seleccione **Desbloquear** y, a continuación, seleccione **Aceptar**. Esto permitirá que el sistema confíe en el instalador.
   - Ejecute al programa de instalación.

>[!IMPORTANT]
>Para ayudar a proteger su entorno de Windows Virtual Desktop en Azure, se recomienda no abrir el puerto de entrada 3389 en las máquinas virtuales. Windows Virtual Desktop no requiere un puerto de entrada abierto 3389 para que los usuarios accedan a máquinas virtuales del grupo host. Si debe abrir el puerto 3389 para solucionar problemas, se recomienda usar [acceso de máquina virtual Just-in-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha creado un grupo de hosts, puede rellenarlo con RemoteApp. Para obtener más información sobre cómo administrar las aplicaciones de Windows Virtual Desktop, consulte el tutorial Administración de grupos de aplicaciones.

> [!div class="nextstepaction"]
> [Tutorial: Administración de grupos de aplicaciones](./manage-app-groups.md)
