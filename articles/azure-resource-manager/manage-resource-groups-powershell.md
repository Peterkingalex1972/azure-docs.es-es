---
title: Administración de grupos de Azure Resource Manager mediante Azure PowerShell | Microsoft Docs
description: Use Azure PowerShell para administrar grupos de Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 5197358e3bd8a3052fbf71cafc2f1e3acda46b26
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2019
ms.locfileid: "67721150"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Administración de grupos de recursos de Azure Resource Manager mediante Azure PowerShell

Obtenga información sobre cómo utilizar Azure PowerShell con [Azure Resource Manager](resource-group-overview.md) para administrar los grupos de recursos de Azure. Para administrar recursos de Azure, vea [Administrar recursos de Azure mediante Azure PowerShell](./manage-resources-powershell.md).

Otros artículos sobre cómo administrar grupos de recursos:

- [Administración de grupos de recursos de Azure con Azure Portal](./manage-resources-portal.md)
- [Administrar grupos de recursos de Azure Resource Manager mediante la CLI de Azure](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>¿Qué es un grupo de recursos?

Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El grupo de recursos puede incluir todos los recursos de la solución o solo aquellos que se desean administrar como grupo. Para decidir cómo asignar los recursos a los grupos de recursos, tenga en cuenta lo que más conviene a su organización. Por lo general, se recomienda agregar recursos que compartan el mismo ciclo de vida al mismo grupo de recursos para que los pueda implementar, actualizar y eliminar con facilidad como un grupo.

Los grupos de recursos almacenan metadatos acerca de los recursos. Por consiguiente, al especificar la ubicación del grupo de recursos, se especifica el lugar en que se almacenan dichos metadatos. Por motivos de compatibilidad, es posible que sea preciso asegurarse de que los datos se almacenan en una región concreta.

Los grupos de recursos almacenan metadatos acerca de los recursos. Al especificar la ubicación del grupo de recursos, se especifica el lugar en que dichos metadatos se almacenan.

## <a name="create-resource-groups"></a>Crear grupos de recursos

El siguiente script de PowerShell crea un grupo de recursos y, a continuación, lo muestra.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Enumeración de grupos de recursos

El siguiente script de PowerShell enumera los grupos de recursos de su suscripción.

```azurepowershell-interactive
Get-AzResourceGroup
```

Para obtener un grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Eliminación de grupos de recursos

El siguiente script de PowerShell elimina un grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Para obtener más información sobre cómo ordena Azure Resource Manager la eliminación de recursos, consulte [Eliminación del grupo de recursos en Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Implementación de recursos en un grupo de recursos existente

Consulte [Implementar recursos en un grupo de recursos](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Para validar la implementación de un grupo de recursos, consulte [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Implementación de un grupo de recursos y de recursos

Puede crear un grupo de recursos e implementar recursos en el grupo por medio de una plantilla de Resource Manager. Para más información, consulte [Creación de un grupo de recursos e implementación de recursos](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Nueva implementación cuando se produce un error en la implementación

Esta característica también es conocida como *reversión en caso de error*. Para obtener más información, consulte [Nueva implementación cuando se produce un error en la implementación](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Traslado a otro grupo de recursos o suscripción

Puede mover los recursos de un grupo a otro grupo de recursos. Para obtener más información, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](./resource-group-move-resources.md).

## <a name="lock-resource-groups"></a>Bloqueo de grupos de recursos

Los bloqueos impiden que otros usuarios de la organización eliminen o modifiquen por error recursos esenciales, como una suscripción de Azure, un grupo de recursos o un recurso. 

El script siguiente bloquea un grupo de recursos de manera que no se pueda eliminar.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

El script siguiente obtiene todos los bloqueos de un grupo de recursos:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Etiquetado de grupos de recursos

Puede aplicar etiquetas a los recursos y grupos de recursos para organizar de manera lógica los recursos. Para obtener información, vea [Uso de etiquetas para organizar los recursos de Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Exportación de grupos de recursos a plantillas

Después de configurar el grupo de recursos, puede ver una plantilla de Resource Manager para el grupo de recursos. Exportar la plantilla ofrece dos ventajas:

- Puede automatizar las futuras implementaciones de la solución, ya que la plantilla contiene la infraestructura completa.
- Obtenga más información sobre la sintaxis de la plantilla consultando la notación de objetos JavaScript (JSON) que representa la solución.

Para exportar todos los recursos de un grupo de recursos, use el cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) y proporcione el nombre del grupo de recursos.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

La plantilla se guarda como un archivo local.

En lugar de exportar todos los recursos del grupo de recursos, puede seleccionar los recursos que quiere exportar.

Para exportar un recurso, pase el identificador de ese recurso.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Para exportar más de un recurso, pase los identificadores de los recursos en una matriz.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

Al exportar la plantilla, puede especificar si se usan parámetros en ella. De forma predeterminada, se incluyen parámetros para los nombres de recursos, pero no tienen un valor predeterminado. Debe pasar ese valor de parámetro durante la implementación.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

En el recurso, el parámetro se usa para el nombre.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Si usa el parámetro `-IncludeParameterDefaultValue` al exportar la plantilla, el parámetro de la plantilla incluye un valor predeterminado que se establece en el valor actual. Puede usar ese valor predeterminado o pasar un valor diferente para sobrescribir el predeterminado.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Si usa el parámetro `-SkipResourceNameParameterization` al exportar la plantilla, los parámetros de los nombres de recursos no se incluyen en la plantilla. En cambio, el nombre del recurso se establece directamente en el recurso en su valor actual. No se puede personalizar el nombre durante la implementación.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Para más información, consulte [Exportación de uno y varios recursos a la plantilla en Azure Portal](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Administración del acceso a los grupos de recursos

El [control de acceso basado en rol (RBAC)](../role-based-access-control/overview.md) es la forma en que se administra el acceso a los recursos en Azure. Para obtener más información, consulte [Administración del acceso mediante RBAC y Azure PowerShell](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre Azure Resource Manager, consulte [Información general de Azure Resource Manager](./resource-group-overview.md).
- Para obtener información sobre la sintaxis de las plantillas de Resource Manager, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](./resource-group-authoring-templates.md).
- Para obtener información sobre cómo desarrollar plantillas, consulte los [tutoriales paso a paso](/azure/azure-resource-manager/).
- Para ver los esquemas de plantilla de Azure Resource Manager, vea la [referencia de plantilla](/azure/templates/).