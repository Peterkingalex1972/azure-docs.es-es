---
title: 'Incorporación de una puerta de enlace de red virtual a una red virtual para ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Este artículo lo ayudará a agregar una puerta de enlace de red virtual a una red virtual de Resource Manager ya creada para ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6ab701adcc2122270c607604f222ac5edbceeb37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730505"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configuración de una puerta de enlace de red virtual para ExpressRoute con PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager: Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Clásico: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Vídeo: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Este artículo lo ayuda a agregar, cambiar el tamaño y quitar una puerta de enlace de red virtual para una red virtual existente previamente. Los pasos de esta configuración se aplican a redes virtuales creadas con el modelo de implementación de Resource Manager de una configuración ExpressRoute. Para más información, consulte [Puertas de enlace de red virtual para ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Antes de comenzar

### <a name="working-with-powershell"></a>Uso de PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Lista de referencia de configuración

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Pasos siguientes
Después de crear la puerta de enlace de red virtual, puede vincular la red virtual a un circuito ExpressRoute. Consulte el artículo [Vinculación de la red virtual a circuitos ExpressRoute](expressroute-howto-linkvnet-arm.md).
