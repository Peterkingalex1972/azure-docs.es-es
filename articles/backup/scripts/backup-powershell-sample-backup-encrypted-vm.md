---
title: 'Ejemplo de script de Azure PowerShell: copia de seguridad de una máquina virtual de Azure | Microsoft Docs'
description: 'Ejemplo de script de Azure PowerShell: copia de seguridad de una máquina virtual de Azure'
documentationcenter: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: sample
ms.date: 03/05/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: 59661f849567f3fef9f56a208334ed0baeef07fd
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639436"
---
# <a name="back-up-an-encrypted-azure-virtual-machine-with-powershell"></a>Copia de seguridad de una máquina virtual de Azure cifrada con PowerShell

Este script crea un almacén de Recovery Services con almacenamiento con redundancia geográfica (GRS) para una máquina virtual de Azure cifrada. Se aplica la directiva de protección predeterminada al almacén. La directiva genera una copia de seguridad diaria de la máquina virtual y conserva cada copia de seguridad durante 30 días. El script también desencadena el punto de recuperación inicial para la máquina virtual y retiene ese punto de recuperación durante 365 días.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/backup/backup-encrypted-vm/backup-encrypted-vm.ps1 "Back up encrypted virtual machine")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```powershell
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear la implementación. Cada elemento de la tabla incluye vínculos a la documentación específica del comando.


| Get-Help | Notas | 
|---|---| 
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. | 
| [New-AzRecoveryServicesVault](https://docs.microsoft.com/powershell/module/az.recoveryservices/new-azrecoveryservicesvault) | Crea un almacén de Recovery Services para almacenar las copias de seguridad. | 
| [Set-AzRecoveryServicesBackupProperty](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupproperty) | Establece las propiedades de almacenamiento de la copia de seguridad en el almacén de Recovery Services. | 
| [New-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Crea la directiva de protección mediante la directiva de programación y la directiva de retención en el almacén de Recovery Services. | 
| [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) | Establece permisos en el almacén Key Vault para conceder acceso a la entidad de servicio a las claves de cifrado. | 
| [Enable-AzRecoveryServicesBackupProtection](https://docs.microsoft.com/powershell/module/az.recoveryservices/enable-azrecoveryservicesbackupprotection) | Habilita la copia de seguridad de un elemento con una directiva de protección de copia de seguridad especificada. | 
| [Set-AzRecoveryServicesBackupProtectionPolicy](https://docs.microsoft.com/powershell/module/az.recoveryservices/set-azrecoveryservicesbackupprotectionpolicy)| Modifica una directiva de protección de copia de seguridad existente. | 
| [Backup-AzRecoveryServicesBackupItem](https://docs.microsoft.com/powershell/module/az.recoveryservices/backup-azrecoveryservicesbackupitem) | Inicia una copia de seguridad para un elemento protegido de Azure Backup que no está asociado a la programación de copia de seguridad. |
| [Wait-AzRecoveryServicesBackupJob](https://docs.microsoft.com/powershell/module/az.recoveryservices/wait-azrecoveryservicesbackupjob) | Espera hasta que finalice un trabajo de Azure Backup. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Quita un grupo de recursos y todos los recursos incluidos en él. | 

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az).

