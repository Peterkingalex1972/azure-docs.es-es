---
title: Instrucciones para el planeamiento de red de Azure NetApp Files | Microsoft Docs
description: Describe las instrucciones que pueden ayudarle a diseñar una arquitectura de red eficaz con Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: b-juche
ms.openlocfilehash: 087ecee053069a02e4d4dd6f636d05ea15269e2e
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2019
ms.locfileid: "68383488"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Instrucciones para el planeamiento de red de Azure NetApp Files

La planeación de la arquitectura de red es un elemento clave del diseño de cualquier infraestructura de aplicación. En este artículo encontrará ayuda para diseñar una arquitectura de red eficaz para que sus cargas de trabajo se beneficien de las completas funcionalidades de Azure NetApp Files.

Los volúmenes de Azure NetApp Files están diseñados para incluirse en una subred de propósito especial llamada [subred delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) dentro de Azure Virtual Network. Por lo tanto, puede acceder a los volúmenes directamente desde la red virtual, desde redes virtuales emparejadas en la misma región o desde un entorno local a través de una puerta de enlace de red virtual (ExpressRoute o VPN Gateway) según sea necesario. La subred está dedicada a Azure NetApp Files y no hay ninguna conectividad a Internet ni a otros servicios de Azure.

## <a name="considerations"></a>Consideraciones  

Debe entender algunas consideraciones al planear de red de Azure NetApp Files.

### <a name="constraints"></a>Restricciones

Las características siguientes no se admiten actualmente en Azure NetApp Files: 

* Grupos de seguridad de red (NSG) aplicados a la subred delegada
* Rutas definidas por el usuario (UDR) con próximo salto como subred de Azure NetApp Files
* Directivas de Azure (por ejemplo, directivas de nomenclatura personalizadas) en la interfaz de Azure NetApp Files
* Equilibradores de carga para el tráfico de Azure NetApp Files

Las siguientes restricciones de red se aplican a Azure NetApp Files:

* El número de direcciones IP en uso en una red virtual con Azure NetApp Files (incluidas las redes virtuales emparejadas) no puede ser mayor que 1000.
* En cada red virtual de Azure (VNet), solo puede delegarse una subred a Azure NetApp Files.


### <a name="supported-network-topologies"></a>Topologías de red admitidas

En la tabla siguiente se describen las topologías de red compatibles con Azure NetApp Files.  También se describen las soluciones alternativas para las topologías no admitidas. 

|    Topologías    |    Se admite    |     Solución alternativa    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Conectividad al volumen en una red virtual local    |    Sí    |         |
|    Conectividad al volumen en una red virtual emparejada (misma región)    |    Sí    |         |
|    Conectividad al volumen en una red virtual emparejada (emparejamiento global o entre regiones)    |    Sin    |    None    |
|    Conectividad a un volumen a través de la puerta de enlace ExpressRoute    |    Sí    |         |
|    Conectividad del entorno local a un volumen en una red virtual de radio a través de una puerta de enlace ExpressRoute y emparejamiento de redes virtuales con tránsito de puerta de enlace    |    Sí    |        |
|    Conectividad del en el entorno local a un volumen en una red virtual de radio a través de puerta de enlace de VPN    |    Sí    |         |
|    Conectividad del entorno local a un volumen en una red virtual de radio a través de una puerta de enlace de VPN y emparejamiento de redes virtuales con tránsito de puerta de enlace    |    Sí    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Red virtual para volúmenes de Azure NetApp Files

En esta sección se explican los conceptos que le ayudarán a planear la red virtual.

### <a name="azure-virtual-networks"></a>Redes virtuales de Azure

Antes de aprovisionar un volumen de Azure NetApp Files, deberá crear una red virtual de Azure (VNet) o usar una ya existente en su suscripción. La red virtual define los límites de red del volumen.  Para más información acerca de cómo crear redes virtuales, consulte la [documentación de Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Subredes

Las subredes segmentan la red virtual en espacios de direcciones independientes utilizables por los recursos de Azure que se encuentran en ellos.  Los volúmenes de Azure NetApp Files están incluidos en una subred de propósito especial llamada [subred delegada](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

La delegación de la subred proporciona al servicio Azure NetApp Files permisos explícitos para crear recursos específicos del servicio en la subred.  Usa un identificador único para implementar el servicio. En este caso, se crea una interfaz de red para habilitar la conectividad a Azure NetApp Files.

Si usa una red virtual nueva, puede crear una subred y delegar la subred a Azure NetApp Files según las instrucciones de [Delegación de una subred en Azure NetApp Files](azure-netapp-files-delegate-subnet.md). También puede delegar una subred vacía existente que no esté ya delegada a otros servicios.

Si la red virtual está emparejada con otra red virtual, no puede expandir el espacio de direcciones de la red virtual. Por ese motivo, la nueva subred delegada debe crearse dentro del espacio de direcciones de la red virtual. Si necesita expandir el espacio de direcciones, debe eliminar el emparejamiento de redes virtuales antes de expandir el espacio de direcciones.

### <a name="udrs-and-nsgs"></a>UDR y NSG

Las rutas definidas por el usuario (UDR) y los grupos de seguridad de red (NSG) no se admiten en las subredes delegadas de Azure NetApp Files.

Como alternativa, puede aplicar los NSG a otras subredes que permitan o rechacen el tráfico hacia y desde la subred delegada de Azure NetApp Files.  

## <a name="azure-native-environments"></a>Entornos nativos de Azure

En el siguiente diagrama se ilustra un entorno nativo de Azure:

![Entorno de conexión nativo de Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Red virtual local

Un escenario básico consiste en crear o conectarse a un volumen de Azure NetApp Files desde una máquina virtual (VM) en la misma red virtual. Para la red virtual 2 en el diagrama anterior, el volumen 1 se crea en una subred delegada y puede montarse en la VM 1 en la subred predeterminada.

### <a name="vnet-peering"></a>Emparejamiento de VNET

Si en la misma región tiene redes virtuales adicionales que deben acceder a los recursos de las demás, las redes virtuales se pueden conectar mediante [emparejamiento de redes virtuales](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) para habilitar la conectividad segura a través de la infraestructura de Azure. 

Piense en la red virtual 2 y la red virtual 3 en el diagrama anterior. Si la VM 2 tiene que conectarse a la VM 3 o al volumen 2, o si la VM 3 tiene que conectarse a la VM 2 o al volumen 1, tiene que habilitar el emparejamiento de redes virtuales entre la red virtual 2 y la red virtual 3. 

Además, considere un escenario donde la red virtual 1está emparejada con la red virtual 2, y la red virtual 2 está emparejada con la red virtual 3 en la misma región. Los recursos de la red virtual 1 pueden conectarse a los recursos de la red virtual 2, pero no pueden conectarse a los recursos de la red virtual 3, a menos que la red virtual 1 y la red virtual 3 estén emparejadas. 

En el diagrama anterior, aunque la VM 3 puede conectarse al volumen 1, la VM 4 no puede conectarse al volumen 2.  El motivo para esto es que las redes virtuales de radio no están emparejadas, y _no se admite el enrutamiento de tránsito a través del emparejamiento de redes virtuales_.

## <a name="hybrid-environments"></a>Entornos híbridos

En el siguiente diagrama se ilustra un entorno híbrido: 

![Entorno de conexión híbrido](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

En el escenario híbrido, las aplicaciones de centros de datos locales necesitan acceso a los recursos de Azure.  Esto es así si quiere expandir su centro de datos a Azure, o si quiere usar los servicios nativos de Azure o para la recuperación ante desastres. Consulte las [opciones de planeamiento de VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) para información sobre cómo conectar varios recursos locales a los recursos en Azure a través de una VPN de sitio a sitio o ExpressRoute.

En una topología de red en estrella tipo hub-and-spoke híbrida, la red virtual del centro (hub) en Azure actúa como un punto central de conectividad para la red local. Los radios (spokes) son redes virtuales emparejadas con el centro y pueden usarse para aislar las cargas de trabajo.

En función de la configuración, puede conectar los recursos locales a los recursos del centro y los radios.

En la topología que se ilustra anteriormente, la red local está conectada a una red virtual de radio en Azure, y hay 2 redes virtuales de radio en la misma región emparejadas con la red virtual del centro.  En este escenario, las opciones de conectividad admitidas para los volúmenes de Azure NetApp Files son las siguientes:

* Los recursos locales VM 1 y VM 2 pueden conectarse al volumen 1 en el centro a través de una VPN de sitio a sitio o un circuito ExpressRoute. 
* Los recursos locales VM 1 y VM 2 pueden conectarse al volumen 2 o volumen 3 a través de una VPN de sitio a sitio y emparejamiento de redes virtuales regional.
* La VM 3 en el centro puede conectarse al volumen 2 en la red virtual 1 de radio y el volumen 3 en la red virtual 2 de radio.
* La VM 4 de la red virtual 1 de radio y la VM 5 de la red virtual 2 de radio pueden conectarse al volumen 1 en la red virtual del centro.

La VM 4 en la red virtual 1 de radio no se puede conectar al volumen 3 en la red virtual 2 de radio. Además, la VM 5 en la red virtual 2 de radio no se puede conectar al volumen 2 en la red virtual 1 de radio. Esto se debe a que las redes virtuales de radio no están emparejadas, y _no se admite el enrutamiento de tránsito a través del emparejamiento de redes virtuales_.

## <a name="next-steps"></a>Pasos siguientes

[Delegación de una subred en Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
