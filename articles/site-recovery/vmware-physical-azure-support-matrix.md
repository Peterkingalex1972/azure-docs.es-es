---
title: Matriz de compatibilidad para la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en Azure con Azure Site Recovery | Microsoft Docs
description: Resume la compatibilidad de la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en Azure mediante Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: raynew
ms.openlocfilehash: fd24d0d9f05855cf22da547f95b16da0a8d2c788
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69617645"
---
# <a name="support-matrix-for-disaster-recovery--of-vmware-vms-and-physical-servers-to-azure"></a>Matriz de compatibilidad para la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en Azure.

En este artículo se resumen los valores y los componentes admitidos para la recuperación ante desastres de máquinas virtuales de VMware y servidores físicos en Azure mediante [Azure Site Recovery](site-recovery-overview.md).

- [Más información](vmware-azure-architecture.md) sobre la arquitectura de recuperación ante desastres de una máquina virtual de VMware o un servidor físico.
- Siga nuestros [tutoriales](tutorial-prepare-azure.md) para probar la recuperación ante desastres.

## <a name="deployment-scenarios"></a>Escenarios de implementación

**Escenario** | **Detalles**
--- | ---
Recuperación ante desastres de máquinas virtuales de VMware | Replicación de máquinas virtuales de VMware locales en Azure. Puede implementar este escenario en Azure Portal o mediante [PowerShell](vmware-azure-disaster-recovery-powershell.md).
Recuperación ante desastres de servidores físicos | Replicación de servidores Windows/Linux físicos locales en Azure. Puede implementar este escenario en Azure Portal.

## <a name="on-premises-virtualization-servers"></a>Servidores de virtualización locales

**Servidor** | **Requisitos** | **Detalles**
--- | --- | ---
vCenter Server | Versión 6.7, 6.5, 6.0 o 5.5 | Se recomienda que utilice un servidor vCenter en su implementación de recuperación ante desastres.
Hosts de vSphere | Versión 6.7, 6.5, 6.0 o 5.5 | Se recomienda que los hosts de vSphere y los servidores vCenter se encuentren en la misma red que el servidor de procesos. De forma predeterminada, el servidor de procesos se ejecuta en el servidor de configuración. [Más información](vmware-physical-azure-config-process-server-overview.md).


## <a name="site-recovery-configuration-server"></a>Servidor de configuración de Site Recovery

El servidor de configuración es una máquina local que ejecuta componentes de Site Recovery locales, incluido el servidor de configuración, el servidor de procesos y el servidor de destino maestro.

- Para las máquinas virtuales de VMware puede establecer el servidor de configuración mediante la descarga de una plantilla de OVF para crear una máquina virtual de VMware.
- Para los servidores físicos, la máquina del servidor de configuración se configura manualmente.

**Componente** | **Requisitos**
--- |---
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | 3 discos<br/><br/> Los discos incluyen el disco del sistema operativo, el disco de memoria caché del servidor de procesos y la unidad de retención para la conmutación por recuperación.
Espacio libre en el disco | 600 GB de espacio para la memoria caché del servidor de procesos.
Espacio libre en el disco | 600 GB de espacio para la unidad de retención.
Sistema operativo  | Windows Server 2012 R2 o Windows Server 2016 con experiencia de escritorio |
Configuración regional del sistema operativo | Español (es-es)
[PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) | No es necesaria para la configuración la versión [9.14](https://support.microsoft.com/help/4091311/update-rollup-23-for-azure-site-recovery) u otra posterior del servidor. 
Roles de Windows Server | No habilite Active Directory Domain Services, Internet Information Services ni Hyper-V. 
Directivas de grupo| - Impedir el acceso al símbolo del sistema. <br/> - Impedir el acceso a herramientas de edición del Registro. <br/> - Confiar en la lógica de datos adjuntos de archivos. <br/> - Activar la ejecución de scripts. <br/> - [Más información](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
IIS | Asegúrese de lo siguiente:<br/><br/> - No hay ningún sitio web preexistente predeterminado <br/> - Habilitar la [autenticación anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br/> - Habilitar la configuración de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br/> - No hay ningún sitio web o aplicación preexistente que escuche en el puerto 443<br/>
Tipo de NIC | VMXNET3 (cuando se implementa como una máquina virtual VMware)
Tipo de dirección IP | estática
Puertos | 443 se usa para la orquestación del canal de control<br/>9443 se usa para el transporte de datos

## <a name="replicated-machines"></a>Máquinas replicadas

Site Recovery admite la replicación de cualquier carga de trabajo que se ejecute en una máquina compatible.

**Componente** | **Detalles**
--- | ---
Configuración del equipo | Las máquinas que se replican en Azure deben cumplir con los [requisitos de Azure](#azure-vm-requirements).
Carga de trabajo del equipo | Site Recovery admite la replicación de cualquier carga de trabajo que se ejecute en una máquina compatible. [Más información](https://aka.ms/asr_workload).
Windows | - Windows Server 2019 (admitido desde el [paquete acumulativo de actualizaciones 34](https://support.microsoft.com/help/4490016) (versión 9.22 de Mobility Service) y versiones posteriores.<br/> - Windows Server 2016 (Server Core de 64 bits y Server con Experiencia de escritorio)<br/> - Windows Server 2012 R2, Windows Server 2012.<br/> - Windows Server 2008 R2 con al menos SP1.<br/> - Windows Server 2008, de 64 y 32 bits con al menos SP2. Se admiten para la migración solo. [Más información](migrate-tutorial-windows-server-2008.md).<br/> - Windows Server 10, Windows 8.1, Windows 8, Windows 7 de 64 bits (admitido desde el [paquete acumulativo de actualizaciones 36](https://support.microsoft.com/help/4503156) (versión 9.22 de Mobility Service y versiones posteriores). No se admite Windows 7 RTM. 
Linux | Solo se admite un sistema de 64 bits. No se admite uno de 32 bits.<br/><br/>Todos los servidores de Linux deberían tener los [componentes de los servicios de integración de Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) instalados. Es necesario para arrancar el servidor en Azure después de una conmutación por error o de una conmutación por error de prueba. Si faltan componentes de LIS, asegúrese de instalar los [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar la replicación para que las máquinas arranquen en Azure. <br/><br/> Site Recovery organiza la conmutación por error para ejecutar servidores Linux en Azure. Sin embargo, los proveedores de Linux podrían limitar la compatibilidad a solo las versiones de distribución que no hayan alcanzado el final del ciclo de vida.<br/><br/> En las distribuciones de Linux, solo se admiten kernels de stock que forman parte del lanzamiento o la actualización de la versión secundaria de la distribución.<br/><br/> La actualización de máquinas protegidas entre distribuciones de versiones principales de Linux no se admite. Para actualizar, deshabilite la replicación, actualice el sistema operativo y, a continuación, habilite la replicación de nuevo.<br/><br/> [Más información](https://support.microsoft.com/help/2941892/support-for-linux-and-open-source-technology-in-azure) sobre la compatibilidad con Linux y la tecnología de código abierto en Azure.
Linux Red Hat Enterprise | 5.2 a 5.11</b><br/> 6.1 a 6.10</b><br/> 7.0 a 7.6<br/> <br/> Los servidores que ejecutan Red Hat Enterprise Linux 5.2-5.11 y 6.1-6.10 no incluyen [componentes de los servicios de integración de Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) preinstalados. Asegúrese de instalar los [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar la replicación para que las máquinas arranquen en Azure.
Linux: CentOS | 5.2 a 5.11</b><br/> 6.1 a 6.10</b><br/> 7.0 a 7.6<br/> <br/> Los servidores que ejecutan CentOS 5.2-5.11 y 6.1-6.10 no incluyen [componentes de los servicios de integración de Linux (LIS)](https://www.microsoft.com/download/details.aspx?id=55106) preinstalados. Asegúrese de instalar los [componentes](https://www.microsoft.com/download/details.aspx?id=55106) antes de habilitar la replicación para que las máquinas arranquen en Azure.
Ubuntu | Servidor Ubuntu 14.04 LTS[ (revise las versiones de kernel admitidas)](#ubuntu-kernel-versions)<br/><br/>Servidor Ubuntu 16.04 LTS[ (revise las versiones de kernel admitidas)](#ubuntu-kernel-versions)
Debian | Debian 7/Debian 8 [(revise versiones de kernel admitidas)](#debian-kernel-versions)
SUSE Linux | SUSE Linux Enterprise Server 12 SP1, SP2, SP3, SP4 [ (revise versiones de kernel admitidas)](#suse-linux-enterprise-server-12-supported-kernel-versions)<br/> SUSE Linux Enterprise Server 11 SP3, SUSE Linux Enterprise Server 11 SP4<br/> No se admite la actualización de máquinas replicadas de SUSE Linux Enterprise Server 11 SP3 a SP4. Para actualizar, deshabilite la replicación y habilítela de nuevo después de la actualización.
Oracle Linux | 6.4, 6.5, 6.6, 6.7, 6.8, 6.9, 6.10, 7.0, 7.1, 7.2, 7.3, 7.4, 7.5, 7.6<br/><br/> que ejecuten el kernel compatible de Red Hat o Unbreakable Enterprise Kernel Release 3, 4 y 5 (UEK3, UEK4, UEK5) 


### <a name="ubuntu-kernel-versions"></a>Versiones de kernel de Ubuntu


**Versión compatible** | **Versión de Mobility service** | **Versión de kernel** |
--- | --- | --- |
14.04 LTS | [9.27][9.27 UR]| 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.26][9.26 UR]| 3.13.0-24-generic a 3.13.0-170-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-148-generic,<br/>4.15.0-1023-azure a 4.15.0-1045-azure |
14.04 LTS | [9.25][9.25 UR]  | 3.13.0-24-generic a 3.13.0-169-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-146-generic,<br/>4.15.0-1023-azure a 4.15.0-1042-azure |
14.04 LTS | [9.24][9.24 UR] | 3.13.0-24-generic a 3.13.0-167-generic,<br/>3.16.0-25-generic a 3.16.0-77-generic,<br/>3.19.0-18-generic a 3.19.0-80-generic,<br/>4.2.0-18-generic a 4.2.0-42-generic,<br/>4.4.0-21-generic a 4.4.0-143-generic,<br/>4.15.0-1023-azure a 4.15.0-1040-azure |
|||
16.04 LTS | [9.27][9.27 UR] | 4.4.0-21-generic a 4.4.0-154-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-54-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1050-azure|
16.04 LTS | [9.26][9.26 UR] | 4.4.0-21-generic a 4.4.0-148-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-50-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1045-azure|
16.04 LTS | [9.25][9.25 UR] | 4.4.0-21-generic a 4.4.0-146-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-48-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1042-azure|
16.04 LTS | [9.24][9.24 UR] | 4.4.0-21-generic a 4.4.0-143-generic,<br/>4.8.0-34-generic a 4.8.0-58-generic,<br/>4.10.0-14-generic a 4.10.0-42-generic,<br/>4.11.0-13-generic to 4.11.0-14-generic,<br/>4.13.0-16-generic a 4.13.0-45-generic,<br/>4.15.0-13-generic a 4.15.0-46-generic<br/>4.11.0-1009-azure to 4.11.0-1016-azure,<br/>4.13.0-1005-azure a 4.13.0-1018-azure <br/>4.15.0-1012-azure a 4.15.0-1040-azure|

### <a name="debian-kernel-versions"></a>Versiones de kernel de Debian


**Versión compatible** | **Versión de Mobility service** | **Versión de kernel** |
--- | --- | --- |
Debian 7 | [9.24][9.24 UR], [9.25][9.25 UR],[9.26][9.26 UR], [9.27][9.27 UR]| 3.2.0-4-amd64 a 3.2.0-6-amd64, 3.16.0-0.bpo.4-amd64 |
|||
Debian 8 | [9.27][9.27 UR] | 3.16.0-4-amd64 a 3.16.0-9-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.9-amd64 |
Debian 8 | [9.24][9.24 UR], [9.25][9.25 UR], [9.26][9.26 UR] | 3.16.0-4-amd64 a 3.16.0-8-amd64, 4.9.0-0.bpo.4-amd64 a 4.9.0-0.bpo.8-amd64 |

### <a name="suse-linux-enterprise-server-12-supported-kernel-versions"></a>Versiones de kernel admitidas de SUSE Linux Enterprise Server 12

**Versión** | **Versión de Mobility service** | **Versión de kernel** |
--- | --- | --- |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.27][9.27 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.115-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default a 4.4.121-92.114-default</br></br>SP3 4.4.73-5-default a 4.4.180-94.97-default</br></br>SP3 4.4.138-4.7-azure a 4.4.180-4.31-azure</br></br>SP4 4.12.14-94.41-default a 4.12.14-95.19-default</br>SP4 4.12.14-6.3-azure a 4.12.14-6.15-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.26][9.26 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.110-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default a 4.4.121-92.109-default</br></br>SP3 4.4.73-5-default a 4.4.178-94.91-default</br></br>SP3 4.4.138-4.7-azure a 4.4.178-4.28-azure</br></br>SP4 4.12.14-94.41-default a 4.12.14-95.16-default</br>SP4 4.12.14-6.3-azure a 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.25][9.25 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default a 4.4.121-92.104-default</br></br>SP3 4.4.73-5-default a 4.4.176-94.88-default</br></br>SP3 4.4.138-4.7-azure a 4.4.176-4.25-azure</br></br>SP4 4.12.14-94.41-default a 4.12.14-95.13-default</br>SP4 4.12.14-6.3-azure a 4.12.14-6.9-azure |
SUSE Linux Enterprise Server 12 (SP1, SP2, SP3, SP4) | [9.24][9.24 UR] | SP1 3.12.49-11-default a 3.12.74-60.64.40-default</br></br> SP1(LTSS) 3.12.74-60.64.45-default a 3.12.74-60.64.107-default</br></br> SP2 4.4.21-69-default a 4.4.120-92.70-default</br></br>SP2(LTSS) 4.4.121-92.73-default a 4.4.121-92.101-default</br></br>SP3 4.4.73-5-default a 4.4.175-94.79-default</br></br>SP4 4.12.14-94.41-default a 4.12.14-95.6-default |


## <a name="linux-file-systemsguest-storage"></a>Sistemas de archivos Linux/almacenamiento de invitados

**Componente** | **Compatible**
--- | ---
Sistemas de archivos | ext3, ext4, XFS
Administrador de volúmenes | - Se admite LVM.<br/> - Se admite /boot en LVM desde el [paquete acumulativo de actualizaciones 31](https://support.microsoft.com/help/4478871/) (versión 9.20 de Mobility Service) y versiones posteriores. No se admite en versiones anteriores de Mobility Service.<br/> - No se admiten varios discos de sistema operativo.
Dispositivos de almacenamiento paravirtualizados | No se admiten dispositivos que se hayan exportado con controladores paravirtualizados.
Dispositivos de E/S de bloque multicola | No compatible.
Servidores físicos con controlador de almacenamiento HP CCISS | No compatible.
Convención de nomenclatura de punto de montaje/dispositivo | El nombre de dispositivo o el nombre de punto de montaje debe ser único.<br/> Asegúrese de que los nombres de dos dispositivos o puntos de montaje no solo se diferencien en las mayúsculas y minúsculas. Por ejemplo, no puede asignar a dispositivos de la misma máquina virtual los nombres *device1* y *Device1*.
Directorios | Si está ejecutando una versión de Mobility Service anterior a la versión 9.20 (publicada en el [paquete acumulativo de actualizaciones 31](https://support.microsoft.com/help/4478871/)) se aplicarán estas restricciones:<br/><br/> - Estos directorios (si se configuran como sistemas de archivos o particiones independientes) deben ubicarse en el mismo disco de sistema operativo del servidor de origen: /(root), /boot, /usr, /usr/local, /var, /etc.</br> - El directorio /boot debe estar en una partición de disco y no en un volumen de LVM.<br/><br/> De la versión 9.20 en adelante, estas restricciones no se aplican. 
Directorio raíz | - Los discos de arranque no debe estar en formato de partición GPT. Se trata de una limitación de la arquitectura de Azure. Los discos GPT se admiten como discos de datos.<br/><br/> No se admiten varios discos de arranque en una VM.<br/><br/> - No se admite /boot en un volumen de LVM en más de un disco.<br/> - Una máquina sin un disco de arranque no se puede replicar.
Espacio en disco necesario| 2 GB en la partición /root <br/><br/> 250 MB en la carpeta de instalación
XFSv5 | Las características de XFSv5 en sistemas de archivos XFS, como la suma de comprobación de metadatos, se admiten a partir de la versión 9.10 de Mobility Service.<br/> Puede usar la utilidad xfs_info para comprobar el superbloque XFS de la partición. Si `ftype` está establecido en 1, entonces se usan las características de XFSv5.
BTRFS | Se admite BTRFS desde el [paquete acumulativo de actualizaciones 34](https://support.microsoft.com/help/4490016) (versión 9.22 de Mobility Service) y versiones posteriores. No se admite BTRFS si:<br/><br/> - El subvolumen del sistema de archivos BTRFS se cambia después de habilitar la protección.</br> - El sistema de archivos BTRFS está repartido entre varios discos.</br> - El sistema de archivos BTRFS es compatible con RAID.

## <a name="vmdisk-management"></a>Administración de máquinas virtuales o discos

**Acción** | **Detalles**
--- | ---
Cambiar el tamaño de disco en una máquina virtual replicada | Se admite.
Agregar disco a una máquina virtual replicada | No compatible.<br/> Deshabilite la replicación para la máquina virtual, agregue el disco y vuelva a habilitar la replicación.

## <a name="network"></a>Red

**Componente** | **Compatible**
--- | ---
Formación de equipos de adaptador de red de la red de host | Se admite en máquinas virtuales de VMware. <br/><br/>No se admite en la replicación de máquinas físicas.
VLAN de la red de host | Sí.
IPv4 de la red de host | Sí.
IPv6 de la red de host | No.
Formación de equipos de adaptador de red de la red de invitado o de servidor | No.
IPv4 de la red de invitado o de servidor | Sí.
IPv6 de la red de invitado o de servidor | No.
IP estática de la red de invitado o de servidor (Windows) | Sí.
IP estática de la red de invitado o de servidor (Linux) | Sí. <br/><br/>Las máquinas virtuales se configuran para usar DHCP en la conmutación por recuperación.
Varios adaptadores de red de la red de invitado o de servidor | Sí.


## <a name="azure-vm-network-after-failover"></a>Red de máquinas virtuales de Azure (después de la conmutación por error)

**Componente** | **Compatible**
--- | ---
Azure ExpressRoute | Sí
ILB | Sí
ELB | Sí
Administrador de tráfico de Azure | Sí
Varias NIC | Sí
Dirección IP reservada | Sí
IPv4 | Sí
Conservar la dirección IP de origen | Sí
Punto de conexión de servicio de red virtual de Azure<br/> | Sí
Redes aceleradas | Sin

## <a name="storage"></a>Storage
**Componente** | **Compatible**
--- | ---
Disco dinámico | El disco del sistema operativo debe ser un disco básico. <br/><br/>Los discos de datos pueden ser discos dinámicos.
Configuración del disco de Docker | Sin
NFS de host | Sí para VMware<br/><br/> No para servidores físicos
SAN de host (ISCSI/FC) | Sí
vSAN de host | Sí para VMware<br/><br/> N/D para servidores físicos
Varias rutas de host (MPIO) | Sí, probado con DSM de Microsoft, EMC PowerPath 5.7 SP4, EMC PowerPath DSM para CLARiiON.
Hospedaje de volúmenes virtuales (VVols) | Sí para VMware<br/><br/> N/D para servidores físicos
VMDK de invitado/servidor | Sí
Disco de clúster compartido de invitado/servidor | Sin
Disco cifrado de invitado/servidor | Sin
NFS de invitado/servidor | Sin
Invitado/servidor iSCSI | Para migración: sí<br/>Para recuperación ante desastres: no, iSCSI realiza la conmutación por recuperación como un disco conectado a la máquina virtual
SMB 3.0 de invitado/servidor | Sin
RDM de invitado/servidor | Sí<br/><br/> N/D para servidores físicos
Disco de invitado/servidor > 1 TB | Sí, el disco debe ser de un tamaño superior a 1024 MB.<br/><br/>Hasta 8192 GB al replicar en discos administrados (versión 9.26 en adelante)<br></br> Hasta 4095 GB al replicar en cuentas de almacenamiento
Disco de invitado/servidor con tamaño de sector físico de 4K y lógico de 4K | Sin
Disco de invitado/servidor con tamaño de sector físico de 512 bytes y lógico de 4K | Sin
Volumen de invitado/servidor con disco seccionado > 4 TB <br/><br/>Administración de volúmenes lógicos (LVM)| Sí
Invitado/servidor: espacios de almacenamiento | Sin
Invitado/servidor: adición/eliminación de disco en caliente | Sin
Invitado/servidor: disco de exclusión | Sí
Varias rutas (MPIO) de invitado/servidor | Sin
Particiones GPT de invitado/servidor | Se admiten cinco particiones desde el [paquete acumulativo de actualizaciones 37](https://support.microsoft.com/help/4508614/) (versión 9.25 de Mobility Service) y versiones posteriores. Antes se admitían cuatro.
Arranque de EFI/UEFI de invitado/servidor | - Se admite si se está ejecutando la versión de Mobility Service 9.13 o posterior.<br/> - Se admite al migrar máquinas virtuales de VMware o servidores físicos que ejecutan Windows Server 2012 o versiones posteriores a Azure.<br/> - Solo puede replicar máquinas virtuales para la migración. No se admite la conmutación por recuperación a entornos locales.<br/> - Solo se admite NTFS. <br/> - No se admite el tipo de arranque seguro de UEFI. <br/> - El tamaño de sector de disco debe ser de 512 bytes por sector físico.

## <a name="replication-channels"></a>Canales de replicación

|**Tipos de replicación**   |**Compatible**  |
|---------|---------|
|Transferencias de datos descargados (ODX)    |       Sin  |
|Propagación sin conexión        |   Sin      |
| Azure Data Box | Sin

## <a name="azure-storage"></a>Almacenamiento de Azure

**Componente** | **Compatible**
--- | ---
Almacenamiento con redundancia local | Sí
Almacenamiento con redundancia geográfica | Sí
Almacenamiento con redundancia geográfica con acceso de lectura | Sí
Almacenamiento de acceso esporádico | Sin
Almacenamiento de acceso frecuente| Sin
Blobs en bloques | Sin
Cifrado en reposo (SSE)| Sí
Premium Storage | Sí
Servicio Import/Export | Sin
Firewalls de Azure Storage para redes virtuales | Sí.<br/> Configurados en la cuenta de almacenamiento o la cuenta de almacenamiento en caché de destino (se usa para almacenar los datos de replicación).
Cuentas de almacenamiento de uso general v2 (capas de acceso frecuente y esporádico) | Sí (los costos de transacción son sustancialmente más elevados para V2 en comparación con V1)

## <a name="azure-compute"></a>Azure Compute

**Característica** | **Compatible**
--- | ---
Conjuntos de disponibilidad | Sí
Zonas de disponibilidad | Sin
CONCENTRADOR | Sí
Discos administrados | Sí

## <a name="azure-vm-requirements"></a>Requisitos de VM de Azure

Las máquinas virtuales locales que se replican en Azure deben cumplir con los requisitos de VM de Azure que se resumen en esta tabla. Cuando Site Recovery ejecuta una comprobación de requisitos previos para la replicación, se producirá un error si no se cumplen algunos de los requisitos.

**Componente** | **Requisitos** | **Detalles**
--- | --- | ---
Sistema operativo invitado | Compruebe los [sistemas operativos compatibles](#replicated-machines) con máquinas replicadas. | Se produce un error en la comprobación si no es compatible.
Arquitectura del sistema operativo invitado | 64 bits | Se produce un error en la comprobación si no es compatible.
Tamaño del disco del sistema operativo | Hasta 2048 GB | Se produce un error en la comprobación si no es compatible.
Número de discos del sistema operativo | 1 | Se produce un error en la comprobación si no es compatible.
Número de discos de datos | 64 o menos | Se produce un error en la comprobación si no es compatible.
Tamaño del disco de datos | Hasta 8192 GB al replicar en discos administrados (versión 9.26 en adelante)<br></br>Hasta 4095 GB al replicar en cuentas de almacenamiento| Se produce un error en la comprobación si no es compatible.
Adaptadores de red | Se admiten varios adaptadores. |
VHD compartido | No compatible. | Se produce un error en la comprobación si no es compatible.
Disco FC | No compatible. | Se produce un error en la comprobación si no es compatible.
BitLocker | No compatible. | Debe deshabilitar BitLocker antes de habilitar la replicación de una máquina. |
Nombre de la máquina virtual | Entre 1 y 63 caracteres.<br/><br/> Restringido a letras, números y guiones.<br/><br/> El nombre de la máquina debe empezar y terminar con una letra o un número. |  Actualice el valor de las propiedades de la máquina en Site Recovery.

## <a name="churn-limits"></a>Límites de renovación

En la tabla siguiente se proporcionan los límites de Azure Site Recovery. 
- Estos límites se basan en nuestras pruebas, pero no cubren todas las combinaciones de E/S posibles de la aplicación.
- Los resultados reales pueden variar en función de la combinación de E/S de la aplicación.
- Para obtener mejores resultados, es muy recomendable que ejecute la [herramienta Deployment Planner](site-recovery-deployment-planner.md) y realice pruebas de la aplicación de forma exhaustiva mediante una conmutación por error de prueba para obtener una imagen real del rendimiento de la aplicación.

**Destino de replicación** | **Tamaño medio de E/S de disco de origen** |**Actividad de datos media de disco de origen** | **Actividad de datos de disco de origen total por día**
---|---|---|---
Standard Storage | 8 KB | 2 MB/s | 168 GB por disco
Disco Premium P10 o P15 | 8 KB  | 2 MB/s | 168 GB por disco
Disco Premium P10 o P15 | 16 KB | 4 MB/s |  336 GB por disco
Disco Premium P10 o P15 | 32 KB, o más | 8 MB/s | 672 GB por disco
Disco Premium P20, P30, P40 o P50 | 8 KB    | 5 MB/s | 421 GB por disco
Disco Premium P20, P30, P40 o P50 | 16 KB, o más |20 MB/s | 1684 GB por disco


**Actividad de datos de origen** | **Límite máximo**
---|---
Actividad de datos media por máquina virtual| 25 MB/s
Actividad de datos máxima entre todos los discos de una máquina virtual | 54 MB/s
Actividad de datos máxima por día admitida por un servidor de procesos | 2 TB

- Estos son los números promedio si la superposición de E/S es del 30 %.
- Site Recovery es capaz de controlar un mayor rendimiento en función de la relación de superposición, tamaños de escritura mayores y el comportamiento real de E/S de la carga de trabajo.
- Estos números asumen un trabajo pendiente típico de aproximadamente cinco minutos. Es decir, una vez que se cargan los datos, se procesan y se crea un punto de recuperación en menos de cinco minutos.

## <a name="vault-tasks"></a>Tareas de almacén

**Acción** | **Compatible**
--- | ---
Mover el almacén entre grupos de recursos | Sin
Mueva el almacén dentro de las suscripciones y entre ellas | Sin
Mover el almacenamiento, la red y las máquinas virtuales de Azure entre grupos de recursos | Sin
Migre el almacenamiento, la red y las VM de Azure dentro de las suscripciones y entre ellas. | Sin


## <a name="obtain-latest-components"></a>Obtenga los componentes más recientes

**Nombre** | **Descripción** | **Detalles**
--- | --- | ---
Servidor de configuración | Instalado en el entorno local.<br/> Coordina las comunicaciones entre servidores o máquinas físicas de VMware locales y Azure. | - [Más información](vmware-physical-azure-config-process-server-overview.md) acerca del servidor de configuración.<br/> - [Más información](vmware-azure-manage-configuration-server.md#upgrade-the-configuration-server) acerca de la actualización a la versión más reciente.<br/> - [Más información](vmware-azure-deploy-configuration-server.md) acerca de la configuración del servidor de configuración. 
Servidor de proceso | Se instala de forma predeterminada en el servidor de configuración.<br/> Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía a Azure.<br/> A medida que crece la implementación, puede agregar más servidores de procesos para controlar mayores volúmenes de tráfico de replicación. | - [Más información](vmware-physical-azure-config-process-server-overview.md) acerca del servidor de procesos.<br/> - [Más información](vmware-azure-manage-process-server.md#upgrade-a-process-server) acerca de la actualización a la versión más reciente.<br/> - [Más información](vmware-physical-large-deployment.md#set-up-a-process-server) acerca de la configuración de servidores de procesos de escalado horizontal.
Mobility Service | Se instala en cada máquina virtual de VMware o servidor físico que se va a replicar.<br/> Coordina la replicación entre servidores de VMware locales o servidores físicos y Azure.| - [Más información](vmware-physical-mobility-service-overview.md) acerca de Mobility Service.<br/> - [Más información](vmware-physical-manage-mobility-service.md#update-mobility-service-from-azure-portal) acerca de la actualización a la versión más reciente.<br/> 



## <a name="next-steps"></a>Pasos siguientes
[Aprenda](tutorial-prepare-azure.md) a preparar Azure para la recuperación ante desastres de las máquinas virtuales de VMware.

[9.27 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.26 UR]: https://support.microsoft.com/en-in/help/4513507/update-rollup-38-for-azure-site-recovery
[9.25 UR]: https://support.microsoft.com/en-in/help/4508614/update-rollup-37-for-azure-site-recovery
[9.24 UR]: https://support.microsoft.com/en-in/help/4503156
[9.23 UR]: https://support.microsoft.com/en-in/help/4494485/update-rollup-35-for-azure-site-recovery
[9.22 UR]: https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery
[9.21 UR]: https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery
[9.20 UR]: https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery
[9.19 UR]: https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30
[9.18 UR]: https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery
