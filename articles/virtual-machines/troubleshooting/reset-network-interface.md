---
title: Restablecimiento de la interfaz de red para VM de Microsoft Azure| Microsoft Docs
description: Muestra cómo restablecer la interfaz de red para VM de Microsoft Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/16/2018
ms.author: genli
ms.openlocfilehash: 3a8e005f8678deef9fc4aebd2d620619fe6074bc
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60307332"
---
# <a name="how-to-reset-network-interface-for-azure-windows-vm"></a>Restablecimiento de la interfaz de red para VM de Microsoft Azure 

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

En este artículo, se muestra cómo restablecer la interfaz de red de la máquina virtual Windows de Azure para resolver problemas cuando no se puede conectar a la máquina virtual Windows de Microsoft Azure después de:

* Deshabilitar la interfaz de red (NIC) predeterminada. 
* Establecer manualmente una dirección IP estática para la NIC. 

[!INCLUDE [support-disclaimer](../../../includes/support-disclaimer.md)]

## <a name="reset-network-interface"></a>Restablecimiento de la interfaz de red

### <a name="for-vms-deployed-in-resource-group-model"></a>Para máquinas virtuales implementadas en el módulo de grupo de recursos

1.  Vaya a [Azure Portal](https://ms.portal.azure.com).
2.  Seleccione la máquina virtual afectada.
3.  Seleccione **Redes** y, a continuación, seleccione la interfaz de red de la máquina virtual.

    ![Ubicación de la interfaz de red](./media/reset-network-interface/select-network-interface-vm.png)
    
4.  Seleccione **Configuraciones IP**.
5.  Seleccione la IP. 
6.  Si la **Asignación de IP privada** no es  **Estática**, cámbiela a **Estática**.
7.  Cambie la **Dirección IP** a otra dirección IP disponible en la subred.
8. La máquina virtual se reiniciará para inicializar la nueva NIC en el sistema.
9.  Intente utilizar RDP en la máquina. Si la operación se realiza correctamente, puede volver a la dirección IP privada original si lo desea. De lo contrario, manténgala. 

#### <a name="use-azure-powershell"></a>Uso de Azure PowerShell

1. Asegúrese de que tiene la versión de [Azure PowerShell más reciente](https://docs.microsoft.com/powershell/azure/overview) instalada
2. Abra una sesión de Azure PowerShell con privilegios elevados (ejecución como administrador). Ejecute los comandos siguientes:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $ResourceGroup = "<Resource Group>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzAccount
    Select-AzSubscription -SubscriptionId $SubscriptionId 
    
    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP

    #Add/Change static IP. This process will not change MAC address
    Get-AzVM -ServiceName $ResourceGroup -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP | Update-AzVM
    ```
3. Intente utilizar RDP en la máquina.  Si la operación se realiza correctamente, puede volver a la dirección IP privada original si lo desea. De lo contrario, manténgala.

### <a name="for-classic-vms"></a>Para máquinas virtuales clásicas

Para restablecer la interfaz de red, siga estos pasos:

#### <a name="use-azure-portal"></a>Usar Azure Portal

1.  Vaya a [Azure Portal]( https://ms.portal.azure.com).
2.  Seleccione **Máquinas virtuales (clásico)** .
3.  Seleccione la máquina virtual afectada.
4.  Seleccione **Direcciones IP**.
5.  Si la **Asignación de IP privada** no es  **Estática**, cámbiela a **Estática**.
6.  Cambie la **Dirección IP** a otra dirección IP disponible en la subred.
7.  Seleccione **Guardar**.
8.  La máquina virtual se reiniciará para inicializar la nueva NIC en el sistema.
9.  Intente utilizar RDP en la máquina. Si la operación se realiza correctamente, puede volver a la dirección IP privada original si lo desea.  

#### <a name="use-azure-powershell"></a>Uso de Azure PowerShell

1. Asegúrese de que tiene la versión de [Azure PowerShell más reciente](https://docs.microsoft.com/powershell/azure/overview) instalada.
2. Abra una sesión de Azure PowerShell con privilegios elevados (ejecución como administrador). Ejecute los comandos siguientes:

    ```powershell
    #Set the variables 
    $SubscriptionID = "<Subscription ID>"
    $VM = "<VM Name>"
    $CloudService = "<Cloud Service>"
    $VNET = "<Virtual Network>"
    $IP = "NEWIP"

    #Log in to the subscription 
    Add-AzureAccount
    Select-AzureSubscription -SubscriptionId $SubscriptionId 

    #Check whether the new IP address is available in the virtual network.
    Test-AzureStaticVNetIP –VNetName $VNET –IPAddress  $IP
    
    #Add/Change static IP. This process will not change MAC address
    Get-AzureVM -ServiceName $CloudService -Name $VM | Set-AzureStaticVNetIP -IPAddress $IP |Update-AzureVM
    ```
3. Intente utilizar RDP en la máquina. Si la operación se realiza correctamente, puede volver a la dirección IP privada original si lo desea. De lo contrario, manténgala. 

## <a name="delete-the-unavailable-nics"></a>Eliminación de las NIC no disponibles
Cuando pueda utilizar el escritorio remoto en la máquina, debe eliminar las NIC antiguas para evitar el posible problema:

1.  Abra el Administrador de dispositivos.
2.  Seleccione **Vista** > **Mostrar dispositivos ocultos**.
3.  Seleccione **Adaptadores de red**. 
4.  Compruebe que los adaptadores tengan el nombre de "Adaptador de red Microsoft Hyper-V".
5.  Puede que vea un adaptador no disponible atenuado. Haga clic con el botón derecho en el adaptador y, a continuación, seleccione Desinstalar.

    ![la imagen de la NIC](media/reset-network-interface/nicpage.png)

    > [!NOTE]
    > Solo desinstale los adaptadores no disponibles con el nombre "Adaptador de red de Microsoft Hyper-V". Si desinstala alguno de los adaptadores ocultos, podrían producirse problemas adicionales.
    >
    >

6.  Ahora deben borrarse todos los adaptadores no disponibles del sistema.
