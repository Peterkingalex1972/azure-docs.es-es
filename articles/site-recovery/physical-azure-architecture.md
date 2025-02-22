---
title: Arquitectura para la recuperación ante desastres de un servidor físico en Azure mediante Azure Site Recovery | Microsoft Docs
description: En este artículo se proporciona información general de los componentes y la arquitectura que se usan durante la recuperación ante desastres de servidores físicos locales en Azure con el servicio Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: raynew
ms.openlocfilehash: 354a68d7d4d07657baa7044566dde8b7ed77ca63
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66400068"
---
# <a name="physical-server-to-azure-disaster-recovery-architecture"></a>Arquitectura de recuperación ante desastres de un servidor físico en Azure

En este artículo se describen la arquitectura y los procesos que se usan al replicar, conmutar por error y recuperar servidores físicos Windows y Linux entre un sitio local y Azure, mediante el servicio [Azure Site Recovery](site-recovery-overview.md).


## <a name="architectural-components"></a>Componentes de la arquitectura

En la tabla y el gráfico siguientes se proporciona una visión general de los componentes que se usan para la replicación de servidores en Azure.  

**Componente** | **Requisito** | **Detalles**
--- | --- | ---
**Las tablas de Azure** | Una suscripción y una red de Azure. | Los datos replicados desde máquinas físicas locales se almacenan en discos administrados de Azure. Las máquinas virtuales de Azure se crean con los datos replicados cuando se ejecuta una conmutación por error desde el entorno local en Azure. Las máquinas virtuales de Azure se conectan a la red virtual de Azure cuando se crean.
**Servidor de configuración** | Se implementa una sola máquina física local o una máquina virtual de VMware para que ejecute todos los componentes locales de Site Recovery. La máquina virtual ejecuta el servidor de configuración, el servidor de procesos y el servidor de destino maestro. | El servidor de configuración coordina la comunicación entre el entorno local y Azure, además de administrar la replicación de datos.
 **Servidor de proceso**:  | Se instala de forma predeterminada junto con el servidor de configuración. | Actúa como puerta de enlace de replicación. Recibe datos de replicación, los optimiza con almacenamiento en caché, compresión y cifrado y los envía al almacenamiento de Azure.<br/><br/> El servidor de procesos también instala Mobility Service en los servidores que desee replicar.<br/><br/> A medida que crece la implementación, puede agregar más servidores de procesos independientes para controlar mayores volúmenes de tráfico de replicación.
 **Servidor de destino principal** | Se instala de forma predeterminada junto con el servidor de configuración. | Controla los datos de replicación durante la conmutación por recuperación desde Azure.<br/><br/> En el caso de las implementaciones de gran tamaño, puede agregar un servidor de destino maestro independiente adicional para la conmutación por recuperación.
**Servidores replicados** | Mobility Service se instala en cada servidor que se replique. | Se recomienda permitir la instalación automática desde el servidor de procesos. Además, puede instalar manualmente el servicio o usar un método de implementación automatizada, como System Center Configuration Manager.

**Arquitectura de dispositivo físico a Azure**

![Componentes](./media/physical-azure-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Proceso de replicación

1. Configure la implementación, incluidos los componentes de Azure y locales. En el almacén de Recovery Services, especifica el origen y el destino de la replicación, configura el servidor de configuración, crea una directiva de replicación y habilita la replicación.
2. Las máquinas se replican de acuerdo con la directiva de replicación y una copia inicial de los datos del servidor se replica en Azure Storage.
3. Una vez terminada la replicación inicial, comienza la de los cambios incrementales en Azure. Las marcas de revisión de una máquina se conservan en un archivo .hrl.
    - Las máquinas se comunican con el servidor de configuración en el puerto HTTPS 443 entrante para la administración de la replicación.
    - Las máquinas envían los datos de replicación al servidor de procesos en el puerto HTTPS 9443 entrante (se puede modificar).
    - El servidor de configuración organiza la administración de la replicación con Azure a través del puerto HTTPS 443 saliente.
    - El servidor de procesos recibe datos de las máquinas de origen, los optimiza y los cifra y los envía al almacenamiento de Azure a través del puerto 443 saliente.
    - Si habilita la coherencia entre varias máquinas virtuales, las máquinas del grupo de replicación se comunican entre sí a través del puerto 20004. La implementación de varias máquinas virtuales se usa si agrupa varias máquinas en grupos de replicación que tienen puntos de recuperación coherentes con los bloqueos y coherentes con la aplicación cuando conmutan por error. Esto resulta útil si las máquinas ejecutan la misma carga de trabajo y necesitan ser coherentes.
4. El tráfico se replica en los puntos de conexión públicos del almacenamiento de Azure a través de Internet. Como alternativa, puede usar el [emparejamiento público](../expressroute/expressroute-circuit-peerings.md#publicpeering) de Azure ExpressRoute. No se admite la replicación del tráfico a través de una VPN de sitio a sitio desde un sitio local en Azure.


**Proceso de replicación de dispositivo físico a Azure**

![Proceso de replicación](./media/physical-azure-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Proceso de conmutación por error y conmutación por recuperación

Una vez que la replicación está configurada y que se ejecutó una exploración de la recuperación ante desastres (conmutación por error de prueba) para comprobar que todo funciona según lo previsto, puede ejecutar la conmutación por error y la conmutación por recuperación según sea necesario. Observe lo siguiente:

- No se admite la conmutación por error planeada.
- Debe conmutar por recuperación a una máquina virtual de VMware local. Esto significa que necesita una infraestructura de VMware local, aunque replique servidores físicos locales en Azure.
- La conmutación por error se realiza en una sola máquina, o bien se crean planes de recuperación para realizar la conmutación por error en varias máquinas a la vez.
- Cuando se ejecuta una conmutación por error, se crean máquinas virtuales de Azure a partir de los datos replicados en Azure Storage.
- Después de desencadenar la conmutación por error inicial, debe confirmarla para que se inicie. Para ello, acceda a la carga de trabajo desde la máquina virtual de Azure.
- Cuando el sitio local principal esté disponible de nuevo, podrá realizar una conmutación por recuperación.
- Debe configurar una infraestructura de conmutación por recuperación, que incluya:
    - **Servidor de procesos temporal de Azure**: para realizar una conmutación por recuperación desde Azure, debe configurar una máquina virtual de Azure para que actúe como servidor de procesos y controle la replicación desde Azure. Dicha máquina virtual se puede eliminar cuando finalice la conmutación por recuperación.
    - **Conexión VPN**: necesita una conmutación VPN o Azure ExpressRoute desde la red de Azure al sitio local.
    - **Servidor de destino maestro independiente**: de manera predeterminada, el servidor de destino maestro que se instaló con el servidor de configuración en la máquina virtual local de VMware controla la conmutación por recuperación. Sin embargo, si necesita la conmutación por recuperación en grandes volúmenes de tráfico, debe configurar un servidor de destino maestro local independiente para este propósito.
    - **Directiva de conmutación por recuperación**: para replicar de nuevo en el sitio local, necesita una directiva de conmutación por recuperación. Esta directiva se creó automáticamente cuando creó la directiva de replicación desde el entorno local a Azure.
    - **Infraestructura de VMware**: se necesita una infraestructura de VMware para conmutación por recuperación. No se puede realizar la conmutación por recuperación a un servidor físico.
- Una vez instalados los componentes, la conmutación por recuperación se produce en tres fases:
    - Fase 1: Vuelva a proteger las máquinas virtuales de modo que realicen la replicación desde Azure de vuelta a las máquinas virtuales VMware locales.
    - Fase 2: Ejecute una conmutación por error en el sitio local.
    - Fase 3: Una vez que las cargas de trabajo realizaron la conmutación por recuperación, vuelve a habilitar la replicación.

**Conmutación por recuperación VMware desde Azure**

![Conmutación por recuperación](./media/physical-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Pasos siguientes

Siga [este tutorial](physical-azure-disaster-recovery.md) para habilitar la replicación del servidor físico en Azure.
