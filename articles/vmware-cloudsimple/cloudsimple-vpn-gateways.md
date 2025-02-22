---
title: 'Solución de Azure VMware de CloudSimple: puertas de enlace de VPN'
description: Obtenga información acerca de los conceptos de VPN de sitio a sitio y de VPN de punto a sitio de CloudSimple.
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 88c27b920817da5edc2cefe780903c2b94695807
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877654"
---
# <a name="vpn-gateways-overview"></a>Información general sobre puertas de enlace de VPN

Una puerta de enlace de VPN se usa para enviar tráfico cifrado entre la red de una región de CloudSimple en una ubicación local o en un equipo a través de Internet pública.  Cada región puede tener una puerta de enlace de VPN, que puede admitir varias conexiones. Al crear varias conexiones a la misma instancia de VPN Gateway, todos los túneles VPN comparten el ancho de banda disponible de la puerta de enlace.

CloudSimple proporciona dos tipos de puerta de enlace de VPN:

* Puerta de enlace de VPN de sitio a sitio
* Puerta de enlace de VPN de punto a sitio

## <a name="site-to-site-vpn-gateway"></a>Puerta de enlace de VPN de sitio a sitio

Una puerta de enlace de VPN de sitio a sitio se usa para enviar tráfico cifrado entre la red de una región de CloudSimple y un centro de datos local. Use esta conexión para definir las subredes o el intervalo de CIDR para el tráfico de red entre su red local y la red de la región de CloudSimple.

La puerta de enlace VPN permite consumir los servicios desde el entorno local en la nube privada, y los servicios en la nube privada, desde la red local.  CloudSimple proporciona un servidor VPN basado en directivas para establecer la conexión desde su red local.

Casos de uso de VPN de sitio a sitio:

* Accesibilidad de su vCenter de nube privada desde cualquier estación de trabajo en la red local.
* Uso de Active Directory local como origen de identidad de vCenter.
* Transferencia cómoda de plantillas de VM, imágenes ISO y otros archivos de los recursos locales a vCenter de la nube privada.
* Accesibilidad de las cargas de trabajo que se ejecutan en la nube privada desde la red local.

![Topología de conexiones VPN de sitio a sitio](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>Parámetros criptográficos

Una conexión VPN de sitio a sitio usa los siguientes parámetros criptográficos predeterminados para establecer una conexión segura.  Cuando crea una conexión desde un dispositivo VPN local, use cualquiera de los siguientes parámetros admitidos por la puerta de enlace de VPN local.

#### <a name="phase-1-proposals"></a>Propuestas de la fase 1

| Parámetro | Propuesta 1 | Propuesta 2 | Propuesta 3 |
|-----------|------------|------------|------------|
| Versión de IKE | IKEv1 | IKEv1 | IKEv1 |
| Cifrado | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo Diffie Hellman (grupo DH) | 2 | 2 | 2 |
| Tiempo de vida | 28.800 segundos | 28.800 segundos | 28.800 segundos |
| Tamaño de datos | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>Propuestas de la fase 2

| Parámetro | Propuesta 1 | Propuesta 2 | Propuesta 3 |
|-----------|------------|------------|------------|
| Cifrado | AES 128 | AES 256 | AES 256 |
| Algoritmo hash| SHA 256 | SHA 256 | SHA 1 |
| Grupo de confidencialidad directa total (grupo PFS) | None | None | None |
| Tiempo de vida | 1800 segundos | 1800 segundos | 1800 segundos |
| Tamaño de datos | 4 GB | 4 GB | 4 GB |

## <a name="point-to-site-vpn-gateway"></a>Puerta de enlace de VPN de punto a sitio

Una VPN de punto a sitio se usa para enviar tráfico cifrado entre la red de una región de CloudSimple y un equipo cliente.  La VPN de punto a sitio es la manera más fácil de acceder a la red de la nube privada, incluido vCenter y las VM de carga de trabajo de su nube privada.  Use la conectividad de VPN de punto a sitio si se conecta a la nube privada de forma remota.

## <a name="next-steps"></a>Pasos siguientes

* [Configurar puerta de enlace de VPN](vpn-gateway.md)
