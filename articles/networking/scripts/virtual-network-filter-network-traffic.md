---
title: 'Ejemplo de script de la CLI de Azure: filtrar el tráfico de red de VM | Microsoft Docs'
description: 'Ejemplo de script de la CLI de Azure: filtrar el tráfico de red de VM entrante y saliente.'
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/07/2017
ms.author: timlt
ms.openlocfilehash: 5f33490a360089a301b28479b7707d4b62c7dd83
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875472"
---
# <a name="filter-inbound-and-outbound-vm-network-traffic"></a>Filtrar el tráfico de red de VM entrante y saliente

Este ejemplo de script crea una red virtual con subredes de front-end y back-end. El tráfico de red entrante hacia la subred de front-end se limita a HTTP, HTTPS y SSH, mientras que el tráfico saliente hacia Internet desde la subred back-end no se permite. Después de ejecutar el script, tendrá una máquina virtual con dos NIC. Cada NIC se conecta a una subred diferente.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de ejemplo


[!code-azurecli-interactive[main](../../../cli_scripts/virtual-network/filter-network-traffic/filter-network-traffic.sh  "Filter VM network traffic")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Ejecute el siguiente comando para quitar el grupo de recursos, la máquina virtual y todos los recursos relacionados.

```azurecli
az group delete --name MyResourceGroup --yes
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos para crear un grupo de recursos, una red virtual y grupos de seguridad de red. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Get-Help | Notas |
|---|---|
| [az group create](/cli/azure/group) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [az network vnet create](/cli/azure/network/vnet) | Crea una subred de front-end y una red virtual de Azure. |
| [az network subnet create](/cli/azure/network/vnet/subnet) | Crea una subred de back-end. |
| [az network vnet subnet update](/cli/azure/network/vnet/subnet) | Asocia NSG a subredes. |
| [az network public-ip create](/cli/azure/network/public-ip) | Crea una dirección IP pública para acceder a la VM desde Internet. |
| [az network nic create](/cli/azure/network/nic) | Crea interfaces de red virtual y las asocia a subredes de front-end y back-end de la red virtual. |
| [az network nsg create](/cli/azure/network/nsg) | Crea grupos de seguridad de red (NSG) que están asociados a las subredes de front-end y back-end. |
| [az network nsg rule create](/cli/azure/network/nsg/rule) |Crea reglas NSG que permiten o bloquean puertos específicos para subredes concretas. |
| [az vm create](/cli/azure/vm) | Crea máquinas virtuales se crea y asocia una NIC a cada VM. Este comando también especifica la imagen de máquina virtual que se usará y las credenciales administrativas. |
| [az group delete](/cli/azure/group) | Elimina un grupo de recursos y todos los recursos que contiene. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

En la [documentación de la información general de redes de Azure](../cli-samples.md) puede encontrar ejemplos adicionales de script de la CLI de redes.
