---
title: 'Creación de un grupo de hosts de Windows Virtual Desktop (versión preliminar) con Azure Marketplace: Azure'
description: Creación de un grupo de hosts de Windows Virtual Desktop (versión preliminar) con Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f692303140db1441aa34aacef62523d7f596dba1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204731"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Tutorial: Creación de un grupo host con Azure Marketplace

Los grupos de hosts son una colección de una o más máquinas virtuales idénticas en entornos de inquilinos de Windows Virtual Desktop (versión preliminar). Cada grupo de hosts puede contener un grupo de aplicaciones con las que los usuarios pueden interactuar igual que harían en un equipo de escritorio físico.

En este tutorial se describe cómo crear un grupo de hosts dentro de un inquilino de Windows Virtual Desktop con una oferta de Microsoft Azure Marketplace. Dichas tareas incluyen:

> [!div class="checklist"]
> * Crear un grupo de hosts en Windows Virtual Desktop.
> * Crear un grupo de recursos con máquinas virtuales en una suscripción de Azure.
> * Unir las máquinas virtuales al dominio de Active Directory.
> * Registrar las máquinas virtuales con Windows Virtual Desktop.

Antes de empezar, y si aún no lo ha hecho, [descargue e importe el módulo de PowerShell para Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) que se usará en la sesión de PowerShell.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en el [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Ejecución de la oferta de Azure Marketplace para aprovisionar un nuevo grupo de hosts

Para ejecutar la oferta de Azure Marketplace para aprovisionar un nuevo grupo de hosts:

1. Seleccione **+** o **+ Crear un recurso**.
2. Escriba **Windows Virtual Desktop** en la ventana de búsqueda de Marketplace.
3. Seleccione **Windows Virtual Desktop - Provision a host pool** (Windows Virtual Desktop: aprovisionar un grupo de hosts) y seleccione **Crear**.

Siga las instrucciones para escribir la información en las hojas correspondientes.

### <a name="basics"></a>Aspectos básicos

En la hoja **Aspectos básicos**, haga lo siguiente:

1. Escriba un nombre para el grupo de hosts que sea un nombre único dentro del inquilino de Windows Virtual Desktop.
2. Seleccione la opción adecuada para un escritorio personal. Si selecciona **Sí**, cada usuario que se conecte a este grupo de hosts se asignará permanentemente a una máquina virtual.
3. Escriba una lista separada por comas de los usuarios que pueden iniciar sesión en los clientes de Windows Virtual Desktop y acceder a un escritorio una vez finalizada la oferta de Azure Marketplace. Por ejemplo, si quiere asignar acceso a user1@contoso.com y user2@contoso.com, escriba "user1@contoso.com,user2@contoso.com."
4. Seleccione **Crear nuevo** y escriba un nombre para el nuevo grupo de recursos.
5. En **Ubicación**, seleccione la misma ubicación que la red virtual que tenga conectividad con el servidor de Active Directory.
6. Seleccione **Aceptar**.

### <a name="configure-virtual-machines"></a>Configuración de máquinas virtuales

En la hoja **Configuración de las máquinas virtuales**:

1. Acepte los valores predeterminados o personalice el número y tamaño de las máquinas virtuales.
2. Escriba un prefijo para los nombres de las máquinas virtuales. Por ejemplo, si escribe el nombre "prefijo", las máquinas virtuales se llamarán "prefijo-0", "prefijo-1" y así sucesivamente.
3. Seleccione **Aceptar**.

### <a name="virtual-machine-settings"></a>Configuración de máquina virtual

En la hoja **Configuración de la máquina virtual**:

>[!NOTE]
> Si va a unir sus máquinas virtuales a un entorno de Azure Active Directory Domain Services (Azure AD DS), asegúrese de que su usuario de unión a un dominio también es miembro del [grupo de administradores del controlador de dominio de AAD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

1. En **Origen de la imagen**, seleccione el origen y escriba la información adecuada para encontrarla y almacenarla. Si decide no usar discos administrados, seleccione la cuenta de almacenamiento que contiene el archivo .vhd.
2. Escriba el nombre principal de usuario y la contraseña de la cuenta de dominio que unirá las máquinas virtuales al dominio de Active Directory. Este mismo nombre de usuario y contraseña se creará en las máquinas virtuales como una cuenta local. Puede restablecer estas cuentas locales más adelante.
3. Seleccione la red virtual que tenga conectividad con el servidor de Active Directory y, después, elija la subred donde se hospedarán las máquinas virtuales.
4. Seleccione **Aceptar**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Información del inquilino de Windows Virtual Desktop (versión preliminar)

En la hoja **Información del inquilino de Windows Virtual Desktop**:

1. En **Nombre de grupo de inquilinos de Windows Virtual Desktop**, escriba el nombre del grupo de inquilinos que contiene su inquilino. A menos que se le haya proporcionado un nombre de grupo de inquilinos específico, deje el valor predeterminado.
2. En **Nombre de inquilino de Windows Virtual Desktop**, escriba el nombre del inquilino donde se creará este grupo de hosts.
3. Especificar el tipo de credenciales que desea usar para autenticarse como propietario de RDS del inquilino de Windows Virtual Desktop. Si completó el [tutorial Creación de entidades de servicio y asignaciones de roles con PowerShell](./create-service-principal-role-powershell.md), seleccione **serviceprincipal**. Cuando aparece **Identificador de inquilino de Azure AD**, escriba el identificador de la instancia de Azure Active Directory que contiene la entidad de servicio.
4. Escriba las credenciales de la cuenta de administrador del inquilino. Solo se admiten entidades de servicio con credenciales de contraseña.
5. Seleccione **Aceptar**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Finalización de la instalación y creación de la máquina virtual

En las dos últimas hojas:

1. En la hoja **Resumen**, revise la información de configuración. Si necesita cambiar algo, vuelva a la hoja correspondiente y realice el cambio antes de continuar. Si la información es correcta, seleccione **Aceptar**.
2. En la hoja **Comprar**, revise la información adicional sobre la compra en Azure Marketplace.
3. Seleccione **Crear** para implementar el grupo de hosts.

Según cuántas máquinas virtuales esté creando, este proceso puede tardar 30 minutos o más en completarse.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Asignación de usuarios adicionales al grupo de aplicaciones de escritorio

Una vez finalizada la oferta de Azure Marketplace, puede asignar más usuarios al grupo de aplicaciones de escritorio antes de empezar a probar los escritorios con una sesión completa en sus máquinas virtuales. Si ya ha agregado usuarios predeterminados en la oferta de Azure Marketplace y no desea agregar más, puede omitir esta sección.

Para asignar usuarios al grupo de aplicaciones de escritorio, primero debe abrir una ventana de PowerShell. Después, deberá especificar los siguientes dos cmdlets.

Ejecute el siguiente cmdlet para iniciar sesión en el entorno de Windows Virtual Desktop:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Agregue usuarios al grupo de aplicaciones de escritorio con este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

El UPN del usuario debe coincidir con la identidad del usuario en Azure Active Directory (por ejemplo, user1@contoso.com). Si desea agregar varios usuarios, debe ejecutar este cmdlet para cada usuario.

Después de haber completado estos pasos, los usuarios agregados al grupo de aplicaciones de escritorio pueden iniciar sesión en Windows Virtual Desktop con clientes de Escritorio remoto compatibles y ver un recurso de escritorio de sesión.

Estos son los clientes compatibles actualmente:

- [Cliente de Escritorio remoto para Windows 7 y Windows 10](connect-windows-7-and-10.md)
- [Cliente web de Windows Virtual Desktop](connect-web.md)

>[!IMPORTANT]
>Para ayudar a proteger su entorno de Windows Virtual Desktop en Azure, se recomienda no abrir el puerto de entrada 3389 en las máquinas virtuales. Windows Virtual Desktop no requiere un puerto de entrada abierto 3389 para que los usuarios accedan a máquinas virtuales del grupo host. Si debe abrir el puerto 3389 para solucionar problemas, se recomienda usar [acceso de máquina virtual Just-in-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Pasos siguientes

Una vez creado el grupo de hosts y con los usuarios asignados para acceder a su escritorio, puede rellenar el grupo de hosts con programas de RemoteApp. Para más información sobre cómo administrar las aplicaciones en Windows Virtual Desktop, consulte este tutorial:

> [!div class="nextstepaction"]
> [Tutorial: Administración de grupos de aplicaciones](./manage-app-groups.md)
