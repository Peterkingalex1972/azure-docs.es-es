---
title: Creación de un grupo de hosts de Windows Virtual Desktop, versión preliminar, con una plantilla de Azure Resource Manager - Azure
description: Cómo crear un grupo de hosts en Windows Virtual Desktop, versión preliminar, con una plantilla de Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: cdc61aede6e650bce62768b7a97f8640affd594f
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2019
ms.locfileid: "67620483"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Creación de un grupo host con una plantilla de Azure Resource Manager

Los grupos de hosts son una colección de una o más máquinas virtuales idénticas en entornos de inquilinos de Windows Virtual Desktop (versión preliminar). Cada grupo de hosts puede contener un grupo de aplicaciones con las que los usuarios pueden interactuar igual que harían en un equipo de escritorio físico.

Siga las instrucciones de esta sección para crear un grupo de hosts de un inquilino de Windows Virtual Desktop con una plantilla de Azure Resource Manager proporcionada por Microsoft. En este artículo se explica cómo crear un grupo de hosts en Windows Virtual Desktop, cómo crear un grupo de recursos con máquinas virtuales en una suscripción de Azure, cómo unir esas máquinas virtuales al dominio de AD y cómo registrar las máquinas virtuales con Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>¿Qué se necesita para ejecutar la plantilla de Azure Resource Manager?

Asegúrese de que conoce los siguientes datos antes de ejecutar la plantilla de Azure Resource Manager:

- Dónde está el origen de la imagen que quiere usar. ¿Está en Azure Gallery o es personalizada?
- Sus credenciales de unión un dominio.
- Sus credenciales de Windows Virtual Desktop.

Cuando se crea un grupo de hosts de Windows Virtual Desktop con la plantilla de Azure Resource Manager, se puede crear una máquina virtual desde una imagen de Azure Gallery, una imagen administrada o una imagen no administrada. Para obtener más información sobre cómo crear imágenes de máquina virtual, vea [Preparación de un VHD o un VHDX de Windows para cargar en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) y [Creación de una imagen administrada de una máquina virtual generalizada en Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Ejecutar la plantilla de Azure Resource Manager para el aprovisionamiento de un nuevo grupo de hosts

Para empezar, vaya a [esta dirección URL de GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Implementación de la plantilla en Azure

Si va a realizar la implementación en una suscripción de Enterprise, desplácese hacia abajo, seleccione **Implementar en Azure** y, luego, vaya directamente a rellenar los parámetros según el origen de la imagen.

Si va a realizar la implementación en una suscripción de proveedor de soluciones en la nube, haga lo siguiente para implementar en Azure:

1. Desplácese hacia abajo, haga clic con el botón derecho en **Implementar en Azure** y, después, seleccione **Copy Link Location** (Copiar ubicación del vínculo).
2. Abra un editor de texto como el Bloc de notas y pegue el vínculo ahí.
3. Justo después de "https://portal.azure.com/" y antes de la almohadilla (#), escriba una arroba (@) seguida del nombre de dominio del inquilino. Este es un ejemplo del formato que debe usar: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Inicie sesión en el Azure Portal como un usuario con permisos de administrador o colaborador en la suscripción del proveedor de soluciones en la nube.
5. Pegue el vínculo que ha copiado en el editor de texto en la barra de direcciones.

Para obtener instrucciones sobre los parámetros que debe especificar en su caso, vea el [archivo Léame](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)de Windows Virtual Desktop. El archivo Léame siempre está actualizado con los cambios más recientes.

## <a name="assign-users-to-the-desktop-application-group"></a>Asignar usuarios al grupo de aplicaciones de escritorio

Cuando la plantilla de Azure Resource Manager de GitHub finalice, asigne accesos de usuario antes de empezar a comprobar los escritorios de sesión completos en sus máquinas virtuales.

En primer lugar y, si aún no lo ha hecho, [descargue e importe el módulo de PowerShell para Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) que se usará en la sesión de PowerShell.

Para asignar usuarios al grupo de aplicaciones de escritorio, abra una ventana de PowerShell y ejecute este cmdlet para iniciar sesión en el entorno de Windows Virtual Desktop:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Tras esto, agregue usuarios al grupo de aplicaciones de escritorio con este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

El UPN del usuario debe coincidir con la identidad del usuario en Azure Active Directory (por ejemplo, user1@contoso.com). Si desea agregar varios usuarios, debe ejecutar este cmdlet para cada usuario.

Después de haber completado estos pasos, los usuarios agregados al grupo de aplicaciones de escritorio pueden iniciar sesión en Windows Virtual Desktop con clientes de Escritorio remoto compatibles y ver un recurso de escritorio de sesión.

>[!IMPORTANT]
>Para ayudar a proteger su entorno de Windows Virtual Desktop en Azure, se recomienda no abrir el puerto de entrada 3389 en las máquinas virtuales. Windows Virtual Desktop no requiere un puerto de entrada abierto 3389 para que los usuarios accedan a máquinas virtuales del grupo host. Si debe abrir el puerto 3389 para solucionar problemas, se recomienda usar [acceso de máquina virtual Just-in-Time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).