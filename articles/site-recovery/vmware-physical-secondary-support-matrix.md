---
title: Matriz de compatibilidad para la recuperación ante desastres de máquinas virtuales de VMware o servidores físicos en un sitio secundario de VMware con Azure Site Recovery | Microsoft Docs
description: Resume la compatibilidad de la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en un sitio secundario de Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: article
ms.date: 08/22/2019
ms.author: raynew
ms.openlocfilehash: c330afb2c5d315b3d386d1477669f1aab2f6e6f9
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972075"
---
# <a name="support-matrix-for-disaster-recovery-of-vmware-vms-and-physical-servers-to-a-secondary-site"></a>Matriz de compatibilidad para la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en un sitio secundario.

En este artículo, se resumen los elementos compatibles cuando se usa el servicio [Azure Site Recovery](site-recovery-overview.md) para la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos Windows o Linux en un sitio de VMware secundario.

- Si quiere replicar máquinas virtuales de VMware o servidores físicos en Azure, consulte [esta matriz de compatibilidad](vmware-physical-azure-support-matrix.md).
- Si quiere replicar máquinas virtuales de Hyper-V en un sitio secundario, consulte [esta matriz de compatibilidad](hyper-v-azure-support-matrix.md).

> [!NOTE]
> La replicación de servidores físicos y máquinas virtuales de VMware locales se realiza a través de InMage Scout. InMage Scout está incluido en la suscripción al servicio Azure Site Recovery.


## <a name="host-servers"></a>Servidores host

**Sistema operativo** | **Detalles**
--- | ---
Servidor vCenter | vCenter 5.5, 6.0 y 6.5<br/><br/> Si ejecuta la versión 6.0 o 6.5, tenga en cuenta que solo se admiten las características de la versión 5.5.


## <a name="replicated-vm-support"></a>Compatibilidad con máquinas virtuales replicadas

En la tabla siguiente se resume la compatibilidad del sistema operativo con las máquinas replicadas con Site Recovery. Cualquier carga de trabajo puede ejecutarse en el sistema operativo compatible.

**Sistema operativo** | **Detalles**
--- | ---
Windows Server | Windows Server 2016 de 64 bits, Windows Server 2012 R2, Windows Server 2012 y Windows Server 2008 R2 con al menos SP1.
Linux | Red Hat Enterprise Linux 6.7, 6.8, 6.9, 7.1 o 7.2 <br/><br/> CentOS 6.5, 6.6, 6.7, 6.8, 6.9, 7.0, 7.1 o 7.2 <br/><br/> Oracle Enterprise Linux 6.4, 6.5 o 6.8 que ejecuten el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3 (UEK3) <br/><br/> SUSE Linux Enterprise Server 11 SP3 u 11 SP4 


## <a name="linux-machine-storage"></a>Almacenamiento de máquinas Linux

Solo se pueden replicar máquinas Linux con el almacenamiento siguiente:

- Sistema de archivos: EXT3, ETX4, ReiserFS, XFS
- Software de múltiples rutas: asignador de dispositivos
- Administrador de volúmenes: LVM2
- No se admiten servidores físicos con almacenamiento de controlador HP CCISS.
- El sistema de archivos ReiserFS solo se admite en SUSE Linux Enterprise Server 11 SP3.

## <a name="network-configuration---hostguest-vm"></a>Configuración de red: máquina virtual host e invitada

**Configuración** | **Compatible**  
--- | --- 
Host: formación de equipos NIC | Sí 
Host: VLAN | Sí 
Host: IPv4 | Sí 
Host: IPv6 | Sin 
VM invitada: formación de equipos NIC | Sin
VM invitada: IPv4 | Sí
VM invitada: IPv6 | Sin
VM invitada: Windows/Linux - dirección IP estática | Sí
VM invitada: múltiples NIC | Sí


## <a name="storage"></a>Storage

### <a name="host-storage"></a>Almacenamiento de host

**Storage (host)** | **Compatible** 
--- | --- 
NFS | Sí 
SMB 3.0 | N/D 
SAN (ISCSI) | Sí 
Varias rutas (MPIO) | Sí 

### <a name="guest-or-physical-server-storage"></a>Almacenamiento de servidor físico o invitado

**Configuración** | **Compatible** 
--- | --- 
VMDK | Sí 
VHD/VHDX | N/D 
VM de 2 generación | N/D 
Disco en clúster compartido | Sí 
Disco cifrado | Sin 
UEFI| Sí 
NFS | Sin 
SMB 3.0 | Sin 
RDM | Sí 
Disco > 1 TB | Sí 
Volumen con disco en bandas > 1 TB<br/><br/> LVM | Sí 
Espacios de almacenamiento | Sin 
Agregar/quitar disco en caliente | Sí 
Excluir el disco | Sí 
Varias rutas (MPIO) | N/D 

## <a name="vaults"></a>Almacenes

**Acción** | **Compatible** 
--- | --- 
Migrar los almacenes entre los grupos de recursos (dentro de las suscripciones o entre ellas) | Sin 
Migrar el almacenamiento, la red y las VM de Azure entre los grupos de recursos (dentro de las suscripciones o entre ellas) | Sin 

## <a name="mobility-service-and-updates"></a>Mobility Service y actualizaciones

Mobility Service coordina la replicación entre los servidores físicos o los servidores de VMware locales y el sitio secundario. Cuando configure la replicación, debe asegurarse de que cuenta con la última versión de Mobility Service y otros componentes.

| **Actualizar** | **Detalles** |
| --- | --- |
|Actualizaciones de Scout | Las actualizaciones de Scout son acumulativas. <br/><br/> [Conozca y descargue](vmware-physical-secondary-disaster-recovery.md#updates) las últimas actualizaciones de Scout |
|Actualizaciones de componentes | Las actualizaciones de Scout contienen actualizaciones para todos los componentes, como el servidor de RX, el servidor de configuración, el servidor de destino de proceso, el servidor de destino maestro, los servidores de vContinuum y los servidores de origen que desee proteger.<br/><br/> [Más información](vmware-physical-secondary-disaster-recovery.md#download-and-install-component-updates).|


## <a name="next-steps"></a>Pasos siguientes

Descargue la [guía del usuario de InMage Scout](https://aka.ms/asr-scout-user-guide).

- [Replicar las máquinas virtuales de Hyper-V en nubes VMM en una nube secundaria](tutorial-vmm-to-vmm.md)
- [Replicación de máquinas virtuales de VMware y servidores físicos en un sitio secundario](tutorial-vmware-to-vmware.md)
