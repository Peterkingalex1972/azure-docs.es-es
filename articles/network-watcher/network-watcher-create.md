---
title: Creación de una instancia de Azure Network Watcher | Microsoft Docs
description: Aprenda a habilitar Network Watcher en una región de Azure.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: c97f6dff17896b8a58c17aed9063e0b2b5733503
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64681575"
---
# <a name="create-an-azure-network-watcher-instance"></a>Creación de una instancia de Azure Network Watcher

Network Watcher es un servicio regional que permite supervisar y diagnosticar problemas en un nivel de escenario de red mediante Azure. La supervisión del nivel de escenario permite diagnosticar problemas en una vista de nivel de red de un extremo a otro. Las herramientas de visualización y diagnóstico de red que incluye Network Watcher le ayudan a conocer, diagnosticar y obtener información acerca de cualquier red de Azure. Network Watcher se habilita al crear un recurso de Network Watcher. Este recurso permite utilizar las funcionalidades de Network Watcher.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="network-watcher-is-automatically-enabled"></a>Network Watcher se habilita automáticamente
Al crear o actualizar una red virtual en su suscripción, Network Watcher se habilitará automáticamente en la región de la red virtual. El hecho de habilitar Network Watcher de forma automática no afecta a sus recursos ni supone un cargo adicional.

#### <a name="opt-out-of-network-watcher-automatic-enablement"></a>Abandono de la habilitación automática de Network Watcher
Si desea dejar de usar la habilitación automática de Network Watcher, puede hacerlo al ejecutar los siguientes comandos:

> [!WARNING]
> Dejar de usar la habilitación automática de Network Watcher es un cambio permanente. Una vez realizada la operación, no se puede volver a utilizar sin [contactar con el servicio técnico](https://azure.microsoft.com/support/options/).

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName DisableNetworkWatcherAutocreation -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

```azurecli-interactive
az feature register --name DisableNetworkWatcherAutocreation --namespace Microsoft.Network
az provider register -n Microsoft.Network
```



## <a name="create-a-network-watcher-in-the-portal"></a>Creación de una instancia de Network Watcher en el portal

Navegue hasta **Todos los servicios** > **Redes** > **Network Watcher**. Puede seleccionar todas las suscripciones para las que desee habilitar Network Watcher. Esta acción crea una instancia de Network Watcher en todas las regiones que estén disponibles.

![crear una instancia de network watcher](./media/network-watcher-create/figure1.png)

Cuando habilita Network Watcher mediante el portal, el nombre de la instancia de Network Watcher se establece automáticamente en *NetworkWatcher_nombre_región*, donde *nombre_región* corresponde a la región de Azure donde se habilita la instancia. Por ejemplo, una instancia de Network Watcher habilitada en la región Centro-oeste de EE. UU. se denomina *NetworkWatcher_westcentralus*.

La instancia de Network Watcher se crea automáticamente en un grupo de recursos denominado *NetworkWatcherRG*. El grupo de recursos se crea si todavía no existe.

Si desea personalizar el nombre de una instancia de Network Watcher y el grupo de recursos en que se encuentra, puede usar PowerShell, la CLI de Azure, la API REST o los métodos de ARMClient que se describen en la sección siguiente. En cada opción, el grupo de recursos debe existir antes de crear una instancia de Network Watcher ahí.  

## <a name="create-a-network-watcher-with-powershell"></a>Creación de una instancia de Network Watcher con PowerShell

Para crear una instancia de Network Watcher, ejecute el siguiente ejemplo:

```powershell
New-AzNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Creación de una instancia de Network Watcher con la CLI de Azure

Para crear una instancia de Network Watcher, ejecute el siguiente ejemplo:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Creación de una instancia de Network Watcher con la API de REST

ARMclient se usa para llamar a la API REST con PowerShell. ARMClient se encuentra en Chocolatey en [ARMClient en Chocolatey](https://chocolatey.org/packages/ARMClient).

### <a name="log-in-with-armclient"></a>Inicio de sesión con ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Creación de Network Watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que tiene una instancia de Network Watcher, obtenga información acerca de las características disponibles:

* [Topología](network-watcher-topology-overview.md)
* [Captura de paquetes](network-watcher-packet-capture-overview.md)
* [Comprobación de flujo de IP](network-watcher-ip-flow-verify-overview.md)
* [Próximo salto](network-watcher-next-hop-overview.md)
* [Vista de grupo de seguridad](network-watcher-security-group-view-overview.md)
* [Registro de flujo de NSG](network-watcher-nsg-flow-logging-overview.md)
* [Solución de problemas de una puerta de enlace de Virtual Network](network-watcher-troubleshoot-overview.md)

Una vez que hay una instancia de Network Watcher, puede habilitar la captura de paquetes dentro de máquinas virtuales. Para información sobre cómo hacerlo, consulte [Uso de capturas de paquetes para realizar la supervisión proactiva de la red con alertas y Azure Functions](network-watcher-alert-triggered-packet-capture.md).
