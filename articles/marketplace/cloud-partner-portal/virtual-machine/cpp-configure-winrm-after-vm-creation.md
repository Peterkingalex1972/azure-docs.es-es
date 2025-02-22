---
title: Configuración de WinRM después de la creación de una máquina virtual de Azure | Azure Marketplace
description: Explica cómo configurar Administración remota de Windows (WinRM) después de la creación de una máquina virtual hospedada en Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: pabutler
ms.openlocfilehash: 4a4248efcfda76dfd8907069e167fdfa144d0365
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938516"
---
# <a name="configure-winrm-after-virtual-machine-creation"></a>Configuración de WinRM después de la creación de una máquina virtual

En este artículo se explica cómo configurar una máquina virtual (VM) hospedada en Azure existente para habilitar WinRM a través de HTTPS.  Esta configuración solo se aplica a las VM basadas en Windows y requiere el siguiente proceso de dos pasos:

1. Habilite el tráfico del puerto para el WinRM a través del protocolo HTTPS.  Establecerá esta configuración para la VM en Azure Portal.
2. Para configurar la VM para habilitar WinRM, ejecute los scripts de PowerShell proporcionados.


## <a name="enabling-port-traffic"></a>Habilitación del tráfico de puerto

WinRM a través del protocolo HTTPS usa el puerto 5896, que no está habilitado de forma predeterminada en las VM Windows preconfiguradas que se ofrecen en Azure Marketplace. Para habilitar este protocolo, siga estos pasos para agregar una nueva regla para el grupo de seguridad de red (NSG) con [Azure Portal](https://portal.azure.com).  Para más información sobre NSG, consulte [Grupos de seguridad](https://docs.microsoft.com/azure/virtual-network/security-overview).

1.  Vaya a la hoja **Máquinas virtuales >**   <*vm-name*>   **> Configuración/Redes**.
2.  Haga clic en el nombre del NSG (en este ejemplo, **testvm11002**) para mostrar sus propiedades:

    ![Propiedades del grupo de seguridad de red](./media/nsg-properties.png)
 
3. En **Configuración**, seleccione **Reglas de seguridad de entrada** para mostrar esta hoja.
4. Haga clic en **+ Agregar** para crear una regla nueva denominada `WinRM_HTTPS` para el puerto TCP 5986.

    ![Adición de una regla de seguridad de red de entrada](./media/nsg-new-rule.png)

5. Haga clic en **Aceptar** cuando haya terminado de proporcionar los valores.  La lista de reglas de seguridad de entrada debe contener las siguientes entradas nuevas.

    ![Enumeración de reglas de seguridad de red de entrada](./media/nsg-new-inbound-listing.png)


## <a name="configure-vm-to-enable-winrm"></a>Configuración de VM para habilitar WinRM 

Siga estos pasos para habilitar y configurar la característica Administración remota de Windows en la VM Windows.   

1. Establezca una conexión de Escritorio remoto a la VM hospedada en Azure.  Para más información, consulte [Conexión a una máquina virtual de Azure donde se ejecuta Windows Server e inicio de sesión en ella](https://docs.microsoft.com/azure/virtual-machines/windows/connect-logon).  Los pasos restantes se ejecutarán en la VM.
2. Descargue los siguientes archivos y guárdelos en una carpeta en la VM:
    - [ConfigureWinRM.ps1](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/ConfigureWinRM.ps1)
    - [makecert.exe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/makecert.exe)
    - [winrmconf.cmd](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-winrm-windows/winrmconf.cmd)
3. Abra la **Consola de PowerShell** con privilegios elevados (**Ejecutar como administrador**). 
4. Ejecute el comando siguiente, proporcionando el parámetro requerido: el nombre de dominio completo (FQDN) para la VM: <br/>
   `ConfigureWinRM.ps1 <vm-domain-name>`

    ![Script de configuración 1 de PowerShell](./media/powershell-file1.png)

    Este script depende de los otros dos archivos que están en la misma carpeta.


## <a name="next-steps"></a>Pasos siguientes

Una vez haya configurado WinRM, estará listo para [implementar la VM desde los VHD constituyentes](./cpp-deploy-vm-vhd.md).
