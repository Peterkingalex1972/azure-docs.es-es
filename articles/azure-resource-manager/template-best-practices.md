---
title: Procedimientos recomendados para plantillas de Azure Resource Manager
description: En este artículo se describen los enfoques recomendados para la creación de plantillas de Azure Resource Manager. Se ofrecen sugerencias para evitar problemas comunes al usar las plantillas.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2019
ms.author: tomfitz
ms.openlocfilehash: cf6a5b07dd72c4e2364281b755e77e642f8fe167
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69542987"
---
# <a name="azure-resource-manager-template-best-practices"></a>Procedimientos recomendados de plantillas de Azure Resource Manager

En este artículo se proporcionan recomendaciones sobre cómo construir la plantilla de Resource Manager. Estas recomendaciones ayudan a evitar problemas comunes al usar una plantilla para implementar una solución.

Para obtener recomendaciones sobre cómo controlar las suscripciones de Azure, vea [Scaffolding empresarial de Azure: gobernanza de suscripción prescriptiva](/azure/architecture/cloud-adoption/appendix/azure-scaffold?toc=%2Fen-us%2Fazure%2Fazure-resource-manager%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json).

Para obtener recomendaciones sobre cómo crear plantillas que funcionan en todos los entornos de nube de Azure, vea [Desarrollo de plantillas de Azure Resource Manager para mantener la coherencia en la nube](templates-cloud-consistency.md).

## <a name="template-limits"></a>Límites de plantilla

Limite el tamaño de la plantilla a 4 MB y cada archivo de parámetros a 64 KB. El límite de 4 MB se aplica al estado final de la plantilla una vez se ha ampliado con definiciones de recursos iterativas y los valores de variables y parámetros. 

También está limitado a:

* 256 parámetros
* 256 variables
* 800 recursos (incluido el recuento de copia)
* 64 valores de salida
* 24 576 caracteres en una expresión de plantilla

Puede superar algunos límites de plantilla utilizando una plantilla anidada. Para más información, consulte [Uso de plantillas vinculadas en la implementación de recursos de Azure](resource-group-linked-templates.md). Para reducir el número de parámetros, variables o salidas, puede combinar varios valores en un objeto. Para más información, consulte [Objetos como parámetros](resource-manager-objects-as-parameters.md).

## <a name="resource-group"></a>Resource group

Al implementar recursos en un grupo de recursos, este almacena metadatos sobre los recursos. Estos metadatos se almacenan en la ubicación del grupo de recursos.

Si la región del grupo de recursos no está disponible temporalmente, no puede actualizar los recursos del grupo de recursos porque los metadatos no están disponibles. Los recursos de otras regiones seguirán funcionando según lo previsto, pero no podrá actualizarlos. Para minimizar el riesgo, busque el grupo de recursos y los recursos en la misma región.

## <a name="parameters"></a>Parámetros
La información en esta sección puede ser útil cuando se trabaja con [parámetros](resource-group-authoring-templates.md#parameters).

### <a name="general-recommendations-for-parameters"></a>Recomendaciones generales para parámetros

* Minimice el uso de los parámetros. En su lugar, use las variables o valores literales de propiedades que no deben especificarse durante la implementación.

* Use una mezcla de mayúsculas y minúsculas para los nombres de parámetro.

* Use parámetros para configuraciones que varían según el entorno, como la SKU, el tamaño o la capacidad.

* Use parámetros para nombres de recurso que quiera especificar para facilitar la identificación.

* Proporcione una descripción de cada parámetro en los metadatos:

   ```json
   "parameters": {
       "storageAccountType": {
           "type": "string",
           "metadata": {
               "description": "The type of the new storage account created to store the VM disks."
           }
       }
   }
   ```

* Defina valores predeterminados para los parámetros que no son confidenciales. Al especificar un valor predeterminado, resulta más fácil implementar la plantilla y los usuarios de esta ven un ejemplo de un valor adecuado. Cualquier valor predeterminado para un parámetro debe ser válido para todos los usuarios en la configuración de implementación predeterminada. 
   
   ```json
   "parameters": {
        "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_GRS",
            "metadata": {
                "description": "The type of the new storage account created to store the VM disks."
            }
        }
   }
   ```

* Para especificar un parámetro opcional, no use una cadena vacía como valor predeterminado. En su lugar, use un valor literal o una expresión de lenguaje para construir un valor.

   ```json
   "storageAccountName": {
     "type": "string",
     "defaultValue": "[concat('storage', uniqueString(resourceGroup().id))]",
     "metadata": {
       "description": "Name of the storage account"
     }
   },
   ```

* Evite usar un parámetro en la versión de API de un tipo de recurso. Las propiedades y los valores de los recursos pueden variar en función del número de la versión. La función IntelliSense en un editor de código no puede determinar el esquema correcto si la versión de API se establece como un parámetro. En lugar de ello, debe codificar de forma rígida la versión de la API en la plantilla.

* Use `allowedValues` con moderación. Úselo solo cuando deba asegurarse de que algunos valores no están incluidos en las opciones permitidas. Si usa `allowedValues` de forma demasiado amplia, podría bloquear implementaciones válidas al no mantener actualizada la lista.

* Cuando un nombre de parámetro en la plantilla coincide con un parámetro en el comando de PowerShell de implementación, Resource Manager resuelve este conflicto de nomenclatura agregando el postfijo **FromTemplate** al parámetro de plantilla. Por ejemplo, si incluye un parámetro llamado **ResourceGroupName** en la plantilla, entra en conflicto con el parámetro **ResourceGroupName** del cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Durante la implementación, se le pide que proporcione un valor para **ResourceGroupNameFromTemplate**.

### <a name="security-recommendations-for-parameters"></a>Recomendaciones de seguridad para parámetros

* Use siempre los parámetros para los nombres de usuario y contraseñas (o secretos).

* Use `securestring` para todas las contraseñas y secretos. Si pasa datos confidenciales en un objeto JSON, use el tipo `secureObject`. No se pueden leer los parámetros con los tipos secureString o secureObject después de la implementación de recursos. 
   
   ```json
   "parameters": {
       "secretValue": {
           "type": "securestring",
           "metadata": {
               "description": "The value of the secret to store in the vault."
           }
       }
   }
   ```

* No proporcione valores predeterminados para los nombres de usuario, contraseñas o cualquier valor que requiera un tipo `secureString`.

* No proporcione valores predeterminados para las propiedades que aumenten la superficie de ataque de la aplicación.

### <a name="location-recommendations-for-parameters"></a>Recomendaciones de ubicación para parámetros

* Use un parámetro para especificar la ubicación de recursos y establecer el valor predeterminado en `resourceGroup().location`. Proporcionar un parámetro de ubicación permite a los usuarios de la plantilla especificar una ubicación en la que tienen permiso para implementar.

   ```json
   "parameters": {
     "location": {
       "type": "string",
       "defaultValue": "[resourceGroup().location]",
       "metadata": {
         "description": "The location in which the resources should be deployed."
       }
     }
   },
   ```

* No especifique `allowedValues` para el parámetro de ubicación. Las ubicaciones que especifique pueden no estar disponibles en todas las nubes.

* Utilice el valor del parámetro de ubicación para los recursos que pueden estar en la misma ubicación. Con este enfoque se minimiza la cantidad de veces que se les pide a los usuarios que proporcionen información de ubicación.

* Para los recursos que no están disponibles en todas las ubicaciones, utilice un parámetro independiente o especifique un valor de ubicación literal.

## <a name="variables"></a>variables

La siguiente información puede ser útil cuando se trabaja con [variables](resource-group-authoring-templates.md#variables):

* Use mayúsculas y minúsculas combinadas para los nombres de variables.

* Use las variables para los valores que deba utilizar más de una vez en una plantilla. Si un valor se usa solo una vez, codificarlo de forma rígida hace que la plantilla resulte más fácil de leer.

* Use variables para los valores que construya a partir de una organización compleja de funciones de plantilla. La plantilla es más fácil de leer cuando la expresión compleja aparece solo en las variables.

* No use variables para `apiVersion` en un recurso. La versión de API determina el esquema del recurso. A menudo, no se puede cambiar la versión sin cambiar las propiedades del recurso.

* No se puede usar la función [reference](resource-group-template-functions-resource.md#reference) en la sección **variables** de la plantilla. La función **reference** deriva su valor desde el estado de tiempo de ejecución del recurso. Sin embargo, las variables se resuelven durante el análisis inicial de la plantilla. Construya valores que requieran la función **reference** directamente en las secciones **resources** u **outputs** de la plantilla.

* Incluya variables para los nombres de recursos que deben ser únicos.

* Use un [bucle de copia en variables](resource-group-create-multiple.md#variable-iteration) para crear un patrón repetido de objetos JSON.

* Quite las variables no utilizadas.

## <a name="resource-dependencies"></a>Dependencias de recursos

A la hora de decidir qué [dependencias](resource-group-define-dependencies.md) establecer, use las siguientes directrices:

* Use la función **reference** y pase el nombre del recurso para establecer dependencias implícitas entre los recursos que deben compartir una propiedad. No agregue un elemento `dependsOn` explícito cuando ya haya definido una dependencia implícita. Este enfoque reduce el riesgo de que se tengan dependencias innecesarias.

* Establezca un recurso secundario como dependiente de su recurso principal.

* Los recursos con el [elemento condition](resource-group-authoring-templates.md#condition) establecido en false se quitan automáticamente de la orden de dependencia. Establezca las dependencias como si siempre se implementase el recurso.

* Permita dependencias en cascada sin establecerlas explícitamente. Por ejemplo, la máquina virtual depende de una interfaz de red virtual y la interfaz de red virtual depende de una red virtual y las direcciones IP públicas. Por lo tanto, la máquina virtual se implementa después de los tres recursos, pero no se establece explícitamente la máquina virtual como dependiente de los tres recursos. Este enfoque aclara el orden de dependencia y facilita el cambio de la plantilla más adelante.

* Si no se puede determinar un valor antes de la implementación, intente implementar el recurso sin una dependencia. Por ejemplo, si un valor de configuración necesita el nombre de otro recurso, quizás no necesite una dependencia. Esta guía no siempre funciona porque algunos recursos comprueban la existencia de los otros. Si recibe un error, agregue una dependencia.

## <a name="resources"></a>Recursos

La información siguiente puede ser útil cuando se trabaja con [recursos](resource-group-authoring-templates.md#resources):

* Para ayudar a otros colaboradores a comprender el propósito del recurso, especifique **comments** para cada recurso de la plantilla:
   
   ```json
   "resources": [
     {
         "name": "[variables('storageAccountName')]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "location": "[resourceGroup().location]",
         "comments": "This storage account is used to store the VM disks.",
         ...
     }
   ]
   ```

* Si usa un *punto de conexión público* en la plantilla (como un punto de conexión público de Azure Blob Storage), *no codifique de forma rígida* el espacio de nombres. Use la función **reference** para recuperar dinámicamente el espacio de nombres. Puede usar este enfoque para implementar la plantilla en diversos entornos de espacios de nombres públicos sin cambiar manualmente el punto de conexión de la plantilla. Establezca la versión de API en la misma que usa para la cuenta de almacenamiento de la plantilla:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si la cuenta de almacenamiento se implementa en la misma plantilla que se está creando, no necesitará especificar el espacio de nombres del proveedor cuando haga referencia al recurso. En el siguiente ejemplo se muestra la sintaxis simplificada:
   
   ```json
   "osDisk": {
       "name": "osdisk",
       "vhd": {
           "uri": "[concat(reference(variables('storageAccountName'), '2016-01-01').primaryEndpoints.blob, variables('vmStorageAccountContainerName'), '/',variables('OSDiskName'),'.vhd')]"
       }
   }
   ```
   
   Si tiene otros valores en la plantilla que estén configurados para usar un espacio de nombres público, cámbielos para que reflejen la misma función **reference**. Por ejemplo, puede establecer la propiedad **storageUri** del perfil de diagnóstico de la máquina virtual:
   
   ```json
   "diagnosticsProfile": {
       "bootDiagnostics": {
           "enabled": "true",
           "storageUri": "[reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
       }
   }
   ```
   
   También puede hacer referencia a una cuenta de almacenamiento existente que se encuentra en un grupo de recursos distinto:

   ```json
   "osDisk": {
       "name": "osdisk", 
       "vhd": {
           "uri":"[concat(reference(resourceId(parameters('existingResourceGroup'), 'Microsoft.Storage/storageAccounts/', parameters('existingStorageAccountName')), '2016-01-01').primaryEndpoints.blob,  variables('vmStorageAccountContainerName'), '/', variables('OSDiskName'),'.vhd')]"
       }
   }
   ```

* Asigne direcciones IP públicas a una máquina virtual solo cuando lo requiera una aplicación. Para conectarse a una máquina virtual (VM) para la depuración o con fines administrativos, use reglas NAT de entrada, una puerta de enlace de red o Jumpbox.
   
     Para obtener más información sobre cómo conectarse a máquinas virtuales, consulte:
   
   * [Ejecución de máquinas virtuales para una arquitectura de n niveles en Azure](../guidance/guidance-compute-n-tier-vm.md)
   * [Configuración de acceso a WinRM para máquinas virtuales en Azure Resource Manager](../virtual-machines/windows/winrm.md)
   * [Habilitación del acceso externo a la máquina virtual mediante Azure Portal](../virtual-machines/windows/nsg-quickstart-portal.md)
   * [Habilitación del acceso externo a la máquina virtual mediante PowerShell](../virtual-machines/windows/nsg-quickstart-powershell.md)
   * [Habilitación del acceso externo a la máquina virtual Linux mediante la CLI de Azure](../virtual-machines/virtual-machines-linux-nsg-quickstart.md)

* La propiedad **domainNameLabel** para las direcciones IP públicas debe ser única. El valor **domainNameLabel** debe contener entre 3 y 63 caracteres y seguir las reglas especificadas por esta expresión regular: `^[a-z][a-z0-9-]{1,61}[a-z0-9]$`. Como la función **uniqueString** genera una cadena de 13 caracteres, el parámetro **dnsPrefixString** se limita a no más de 50 caracteres:

   ```json
   "parameters": {
       "dnsPrefixString": {
           "type": "string",
           "maxLength": 50,
           "metadata": {
               "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$"
           }
       }
   },
   "variables": {
       "dnsPrefix": "[concat(parameters('dnsPrefixString'),uniquestring(resourceGroup().id))]"
   }
   ```

* Cuando agrega una contraseña a una extensión de script personalizada, use la propiedad **commandToExecute** en la propiedad **protectedSettings**:
   
   ```json
   "properties": {
       "publisher": "Microsoft.Azure.Extensions",
       "type": "CustomScript",
       "typeHandlerVersion": "2.0",
       "autoUpgradeMinorVersion": true,
       "settings": {
           "fileUris": [
               "[concat(variables('template').assets, '/lamp-app/install_lamp.sh')]"
           ]
       },
       "protectedSettings": {
           "commandToExecute": "[concat('sh install_lamp.sh ', parameters('mySqlPassword'))]"
       }
   }
   ```
   
   > [!NOTE]
   > Para garantizar que los secretos se cifran cuando se transmiten como parámetros a máquinas virtuales y extensiones, use la propiedad **protectedSettings** de las extensiones pertinentes.
   > 
   > 

## <a name="outputs"></a>Salidas

Si usa una plantilla para crear direcciones IP públicas incluya una [sección de salidas](resource-group-authoring-templates.md#outputs) en la que se devuelvan detalles de la dirección IP y el nombre de dominio completo (FQDN). Puede usar valores de salida para recuperar fácilmente los detalles sobre las direcciones IP públicas y los nombres FQDN después de la implementación.

```json
"outputs": {
    "fqdn": {
        "value": "[reference(parameters('publicIPAddresses_name')).dnsSettings.fqdn]",
        "type": "string"
    },
    "ipaddress": {
        "value": "[reference(parameters('publicIPAddresses_name')).ipAddress]",
        "type": "string"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre la estructura del archivo de plantillas de Resource Manager vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para obtener recomendaciones sobre cómo crear plantillas que funcionan en todos los entornos de nube de Azure, vea [Desarrollo de plantillas de Azure Resource Manager para mantener la coherencia en la nube](templates-cloud-consistency.md).
