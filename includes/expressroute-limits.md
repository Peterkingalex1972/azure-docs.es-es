---
title: archivo de inclusión
description: archivo de inclusión
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2019
ms.locfileid: "67622123"
---
| Recurso | Límite predeterminado/máximo |
| --- | --- |
| Circuitos ExpressRoute por suscripción |10 |
| Circuitos ExpressRoute por región y suscripción, con Azure Resource Manager |10 |
| Número máximo de rutas anunciadas para emparejamiento privado de Azure con ExpressRoute estándar |4\.000 |
| Número máximo de rutas anunciadas para emparejamiento privado de Azure con complemento de ExpressRoute premium |10 000 |
| Número máximo de rutas anunciadas desde el emparejamiento privado de Azure a partir del espacio de direcciones de red virtual de una conexión ExpressRoute |200 |
| Número máximo de rutas anunciadas para emparejamiento de Microsoft con ExpressRoute estándar |200 |
| Número máximo de rutas anunciadas para emparejamiento de Microsoft con el complemento de ExpressRoute premium |200 |
| Número máximo de circuitos ExpressRoute vinculados a la misma red virtual en la misma ubicación de emparejamiento |4 |
| Número máximo de circuitos ExpressRoute vinculado a la misma red virtual en distintas ubicaciones de emparejamiento |4 |
| Número de vínculos de red virtual permitidos por circuito ExpressRoute |Consulte la tabla [Número de redes virtuales por circuito ExpressRoute](#vnetpercircuit).  |

#### <a name="vnetpercircuit"></a> Número de redes virtuales por circuito ExpressRoute
| **Tamaño del circuito** | **Número de vínculos de red virtual para estándar** | **Número de vínculos de red virtual con complemento premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

**Solo ExpressRoute Direct de 100 Gbps*
