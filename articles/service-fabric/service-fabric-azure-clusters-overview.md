---
title: Creación de clústeres de Service Fabric de Azure en Windows Server y Linux | Microsoft Docs
description: Los clústeres de Service Fabric se ejecutan en Windows Server y Linux, lo que significa que podrá implementar y hospedar aplicaciones de Service Fabric en cualquier lugar donde sea posible ejecutar Windows Server y Linux.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: d1681aee9dc11f0dbd3133bced0b919a8c1623b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60310933"
---
# <a name="overview-of-service-fabric-clusters-on-azure"></a>Introducción a los clústeres de Service Fabric en Azure
Un clúster de Service Fabric es un conjunto de máquinas físicas o virtuales conectadas a la red, en las que se implementan y administran los microservicios. Una máquina física o virtual que forma parte de un clúster se denomina nodo del clúster. Los clústeres pueden escalarse a miles de nodos. Si agrega nuevos nodos al clúster, Service Fabric reequilibra las réplicas e instancias de la partición del servicio en el número aumentado de nodos. El rendimiento general de la aplicación mejora y se reduce la contención para el acceso a la memoria. Si los nodos del clúster no se usan de forma eficaz, puede reducir su número de nodos. Service Fabric vuelve a reequilibrar las réplicas e instancias de la partición en el número reducido de nodos para aprovechar mejor el hardware de cada nodo.

Un tipo de nodo define el tamaño, el número y las propiedades de un conjunto de nodos (máquinas virtuales) en el clúster. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente. Cada uno tiene diferentes conjuntos de puertos abiertos y puede tener distintas métricas de capacidad. Los tipos de nodo se utilizan para definir los roles para un conjunto de nodos de clúster, por ejemplo, "front-end" o "back-end". El clúster puede tener más de un tipo de nodo, pero el tipo de nodo principal debe tener al menos cinco máquinas virtuales para los clústeres de producción (o al menos tres máquinas virtuales en clústeres de prueba). [Los servicios del sistema de Service Fabric](service-fabric-technical-overview.md#system-services) se colocan en los dos del tipo de nodo principal. 

## <a name="cluster-components-and-resources"></a>Componentes y recursos del clúster
Un clúster de Service Fabric en Azure es un recurso de Azure que usa otros recursos de Azure e interactúa con ellos:
* máquinas virtuales y tarjetas de red virtual
* conjuntos de escalado de máquinas virtuales
* redes virtuales
* equilibradores de carga
* cuentas de almacenamiento
* direcciones IP públicas

![Clúster de Service Fabric][Image]

### <a name="virtual-machine"></a>Máquina virtual
Una [máquina virtual](/azure/virtual-machines/) que forma parte de un clúster se denomina nodo aunque, técnicamente, un nodo de clúster es un proceso de entorno de ejecución de Service Fabric. A cada nodo se le asigna un nombre de nodo (una cadena). Los nodos tienen características, como las [propiedades de colocación](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints). Cada máquina física o virtual tiene un servicio de inicio automático, *FabricHost.exe*, que empieza a ejecutarse en el arranque y luego inicia dos ejecutables: *Fabric.exe* y *FabricGateway.exe*, que componen el nodo. Una implementación de producción es un nodo por máquina física o virtual. En los escenarios de prueba, se pueden hospedar varios nodos en una sola máquina física o virtual mediante la ejecución de varias instancias de *Fabric.exe* y *FabricGateway.exe*.

Cada máquina virtual está asociada con una tarjeta de interfaz de red (NIC) virtual y a cada NIC se le asigna una dirección IP privada.  Una máquina virtual se asigna a una red virtual y el equilibrador de carga local a través de la NIC.

Todas las máquinas virtuales de un clúster se colocan en una red virtual.  Todos los nodos del mismo conjunto de escalado o tipo de nodo se colocan en la misma subred de la red virtual.  Estos nodos solo tienen direcciones IP privadas y no son directamente direccionables fuera de la red virtual.  Los clientes pueden tener acceso a servicios en los nodos a través del equilibrador de carga de Azure.

### <a name="scale-setnode-type"></a>Tipo de nodo o conjunto de escalado
Al crear el clúster, se definen uno o más tipos de nodo.  Los nodos, o máquinas virtuales, de un tipo de nodo tienen el mismo tamaño y características como el número de CPU, memoria, número de discos y E/S de disco.  Por ejemplo, un tipo de nodo podría ser para VM pequeñas y de front-end con puertos abiertos a Internet, mientras que otro tipo de nodo podría ser para VM grandes y de back-end que procesan datos. En clústeres de Azure, cada tipo de nodo se asigna a un [conjunto de escalado de máquinas virtuales](/azure/virtual-machine-scale-sets/).

Puede usarlos para implementar y administrar una colección de máquinas virtuales como conjunto. Cada tipo de nodo que defina en un clúster de Azure Service Fabric configura un conjunto de escalado independiente. El entorno de ejecución de Service Fabric se arranca en cada máquina virtual en el conjunto de escalado mediante extensiones de máquina virtual de Azure. Cada tipo de nodo se puede escalar o reducir verticalmente de forma independiente; puede cambiar la SKU del sistema operativo que se ejecuta en cada nodo de clúster, tener diferentes conjuntos de puertos abiertos y usar distintas métricas de capacidad. Un conjunto de escalado tiene cinco [dominios de actualización](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) y cinco [dominios de error](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains) y puede tener hasta 100 máquinas virtuales.  Crea clústeres de más de 100 nodos mediante la creación de varios tipos de nodo/conjuntos de escalado.

> [!IMPORTANT]
> La elección del número de tipos de nodo para el clúster y las propiedades de cada tipo de nodo (tamaño, principal, accesible desde Internet, número de máquinas virtuales, etc.) es una tarea importante.  Para más información, consulte las [consideraciones de planeación de capacidad del clúster de Service Fabric](service-fabric-cluster-capacity.md).

Para más información, consulte los [tipos de nodos y conjuntos de escalado de máquinas virtuales de Azure Service Fabric](service-fabric-cluster-nodetypes.md).

### <a name="azure-load-balancer"></a>Azure Load Balancer
Las instancias de máquina virtual se unen detrás de una instancia de [Azure Load Balancer](/azure/load-balancer/load-balancer-overview), que está asociada a una [dirección IP pública](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses) y una etiqueta DNS.  Cuando aprovisiona un clúster con un *&lt;nombreclúster&gt;* , el nombre DNS, *&lt;nombreclúster&gt;.&lt;ubicación&gt;.cloudapp.azure.com* es la etiqueta DNS asociada con el equilibrador de carga delante del conjunto de escalado.

Las máquinas virtuales en un clúster solo tienen [direcciones IP privadas](/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses).  El tráfico de administración y el tráfico del servicio se enrutan a través del equilibrador de carga orientado al público.  El tráfico de red se enruta a estas máquinas a través de las reglas NAT (los clientes se conectan a instancias o nodos específicos) o las reglas de equilibrio de carga (el tráfico se dirige a las máquinas virtuales en round robin).  Un equilibrador de carga tiene una dirección IP pública asociada con un nombre DNS en el formato: *&lt;nombreclúster&gt;.&lt; ubicación&gt;.cloudapp.azure.com*.  Una dirección IP pública es otro recurso de Azure en el grupo de recursos.  Si define varios tipos de nodo en un clúster, se crea un equilibrador de carga para cada conjunto de escalado o tipo de nodo. O bien, puede configurar un solo equilibrador de carga para varios tipos de nodo.  El tipo de nodo principal tiene la etiqueta DNS *&lt;nombreclúster&gt;.&lt;ubicación&gt;.cloudapp.azure.com*, otros tipos de nodos tienen la etiqueta DNS  *&lt;nombreclúster&gt;-&lt;tiponodo&gt;.&lt;ubicación&gt;.cloudapp.azure.com*.

### <a name="storage-accounts"></a>Cuentas de almacenamiento
Cada tipo de nodo de clúster es compatible con una [cuenta de almacenamiento de Azure](/azure/storage/common/storage-introduction) y discos administrados.

## <a name="cluster-security"></a>Seguridad de clúster
Un clúster de Service Fabric es un recurso que usted posee.  Tiene la responsabilidad de proteger los clústeres para impedir que usuarios no autorizados se conecten a ellos. Proteger el clúster es especialmente importante si en él se ejecutan cargas de trabajo de producción. 

### <a name="node-to-node-security"></a>Seguridad de nodo a nodo
La seguridad de nodo a nodo protege la comunicación entre las máquinas virtuales o los equipos de un clúster. Este escenario de seguridad garantiza que solo los equipos autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster. Service Fabric usa certificados X.509 para proteger un clúster y proporcionar características de seguridad de las aplicaciones.  Se necesita un certificado de clúster para proteger el tráfico de clúster y proporcionar autenticación de servidor y clúster.  Los certificados autofirmados se pueden usar para los clústeres de prueba, pero se debe utilizar un certificado de una entidad de certificación de confianza para proteger los clústeres de producción.

Para obtener más información, lea sobre la [seguridad de nodo a nodo](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>Seguridad de cliente a nodo
La seguridad de cliente a nodo autentica los clientes y ayuda a proteger la comunicación entre un cliente y los nodos individuales del clúster. Este tipo de seguridad ayuda a garantizar que solo los usuarios autorizados accedan al clúster y a las aplicaciones implementadas en él. Los clientes se identifican de forma exclusiva mediante sus credenciales de seguridad del certificado X.509. Puede utilizarse cualquier número de certificados de cliente opcionales para autenticar a los clientes administradores o usuarios con el clúster.

Además de los certificados de cliente, Azure Active Directory también puede configurarse para autenticar a los clientes con el clúster.

Para obtener más información, lea sobre la [seguridad de cliente a nodo](service-fabric-cluster-security.md#client-to-node-security).

### <a name="role-based-access-control"></a>Control de acceso basado en roles
El control de acceso basado en rol (RBAC) le permite asignar controles de acceso específicos en los recursos de Azure.  Puede asignar diferentes reglas de acceso a las suscripciones, los grupos de recursos y los recursos.  A menos que se reemplacen a un nivel inferior, se heredan las reglas de RBAC a lo largo de la jerarquía de recursos.  Puede asignar reglas RBAC a cualquier usuario o grupo de usuarios de su entorno AAD para que los usuarios y grupos designados puedan modificar el clúster.  Para obtener más información, lea la [introducción a RBAC en Azure](/azure/role-based-access-control/overview).

Service Fabric también admite el control de acceso para limitarlo a determinadas operaciones de clúster para los diferentes grupos de usuarios. Esto ayuda a que el clúster esté más protegido. Se admiten dos tipos de control de acceso para los clientes que se conectan a un clúster: rol de administrador y rol de usuario.  

Para obtener más información, consulte [Control de acceso basado en rol (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac).

### <a name="network-security-groups"></a>Grupos de seguridad de red 
Los grupos de seguridad de red (NSG) controlan el tráfico entrante y saliente de una subred, VM o NIC específica.  De forma predeterminada, cuando se colocan varias máquinas virtuales en la misma red virtual, pueden comunicarse entre sí a través de cualquier puerto.  Si desea restringir las comunicaciones entre las máquinas, puede definir los NSG para segmentar la red o aislar las máquinas virtuales entre sí.  Si tiene varios tipos de nodo en un clúster, puede aplicar los NSG a subredes para impedir que las máquinas que pertenecen a distintos tipos de nodos se comuniquen entre sí.  

Para obtener más información, consulte la información sobre [grupos de seguridad](/azure/virtual-network/security-overview).

## <a name="scaling"></a>Escalado

Las necesidades de las aplicaciones cambian a lo largo del tiempo. Puede necesitar aumentar los recursos del clúster para cumplir con aumentos del tráfico de red o de la carga de trabajo de la aplicación o reducir los recursos del clúster cuando la demanda baja. Después de crear un clúster de Service Fabric, puede escalar el clúster horizontalmente (cambiar el número de nodos) o verticalmente (cambiar los recursos de los nodos). Puede escalar el clúster en cualquier momento, incluso con cargas de trabajo en ejecución en el clúster. Según se escala el clúster, las aplicaciones se escalan automáticamente.

Para obtener más información, lea [Escalado de clústeres de Azure](service-fabric-cluster-scaling.md).

## <a name="upgrading"></a>Actualizando
Un clúster de Azure Service Fabric es un recurso de su propiedad que está parcialmente administrado por Microsoft. Microsoft es responsable de la aplicación de revisiones del sistema operativo subyacente y de realizar actualizaciones del entorno de ejecución de Service Fabric en el clúster. Puede configurar el clúster para recibir las actualizaciones automáticas del entorno de ejecución cuando Microsoft publique una versión nueva, o bien seleccionar una versión del entorno de ejecución compatible que desee. Además de las actualizaciones del entorno de ejecución, también puede actualizar la configuración de clúster, como los certificados o los puertos de la aplicación.

Para obtener más información, lea sobre la [actualización de clústeres](service-fabric-cluster-upgrade.md).

## <a name="supported-operating-systems"></a>Sistemas operativos compatibles
Puede crear clústeres en máquinas virtuales que ejecuten estos sistemas operativos:

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Windows Server 1803
* Linux Ubuntu 16.04
* Red Hat Enterprise Linux 7.4 (compatibilidad de la versión preliminar)

> [!NOTE]
> Si decide implementar Service Fabric en Windows Server 1709, tenga en cuenta que (1) no es una rama de mantenimiento a largo plazo, así que puede que tenga que mover versiones en el futuro, y (2) si implementa contenedores, los integrados en Windows Server 2016 no funcionarán en Windows Server 1709, y viceversa (tendrá que volver a compilarlos para implementarlos).
>


## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre [proteger](service-fabric-cluster-security.md), [escalar](service-fabric-cluster-scaling.md) y [actualizar](service-fabric-cluster-upgrade.md) clústeres de Azure.

Más información sobre las [opciones de soporte técnico de Service Fabric](service-fabric-support.md).

[Image]: media/service-fabric-azure-clusters-overview/Cluster.PNG