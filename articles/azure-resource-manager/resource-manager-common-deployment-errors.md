---
title: Solución de errores comunes de implementación de Azure | Microsoft Docs
description: Describe cómo solucionar errores comunes al implementar recursos en Azure con Azure Resource Manager.
tags: top-support-issue
author: tfitzmac
keywords: error de implementación, implementación de Azure, implementar en Azure
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 07/28/2019
ms.author: tomfitz
ms.openlocfilehash: 639f6b3b29b7effa12de79335d44b0193f3f9932
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638536"
---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Solución de errores comunes de implementación de Azure con Azure Resource Manager

En este artículo se describen algunos errores comunes de implementación de Azure y se proporciona información sobre cómo resolverlos. Si no encuentra el código del error de implementación, consulte [Búsqueda de códigos de error](#find-error-code).

Si busca información sobre un código de error y esa información no se proporciona en este artículo, háganoslo saber. En la parte inferior de esta página, puede dejar comentarios. Se realiza un seguimiento de los comentarios con problemas de GitHub. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="error-codes"></a>Códigos de error

| Código de error | Mitigación | Más información |
| ---------- | ---------- | ---------------- |
| AccountNameInvalid | Siga las restricciones de nomenclatura para las cuentas de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](resource-manager-storage-account-name-errors.md) |
| AccountPropertyCannotBeSet | Consulte las propiedades disponibles para la cuenta de almacenamiento. | [storageAccounts](/azure/templates/microsoft.storage/storageaccounts) |
| AllocationFailed | El clúster o la región no tienen recursos disponibles o no admiten el tamaño de máquina virtual solicitado. Vuelva a realizar la solicitud más adelante o solicite otro tamaño de máquina virtual. | [Problemas de aprovisionamiento y asignación en Linux](../virtual-machines/linux/troubleshoot-deployment-new-vm.md), [Problemas de aprovisionamiento y asignación en Windows](../virtual-machines/windows/troubleshoot-deployment-new-vm.md) y [Solución de problemas de asignación](../virtual-machines/troubleshooting/allocation-failure.md)|
| AnotherOperationInProgress | Espere a que la operación simultánea finalice. | |
| AuthorizationFailed | La cuenta o entidad de servicio no dispone de acceso suficiente para completar la implementación. Compruebe el rol al que la cuenta pertenece y su acceso para el ámbito de implementación.<br><br>Puede recibir este error cuando un proveedor de recursos necesario no está registrado. | [Control de acceso basado en roles de Azure](../role-based-access-control/role-assignments-portal.md)<br><br>[Resolución de registros](resource-manager-register-provider-errors.md) |
| BadRequest | Envió valores de implementación que no coinciden con los que Resource Manager esperaba. Compruebe el mensaje de estado interno para obtener ayuda para solucionar el problema. | [Referencia de plantillas](/azure/templates/) y [ubicaciones admitidas](resource-group-authoring-templates.md#resource-location) |
| Conflicto | Se solicita una operación no permitida con el estado actual del recurso. Por ejemplo, solo se permite el cambio de tamaño del disco al crear una VM o al desasignar la VM. | |
| DeploymentActive | Espere a que la implementación simultánea de este grupo de recursos finalice. | |
| DeploymentFailed | El error DeploymentFailed es un error general que no proporciona la información necesaria para resolverlo. Mire en los detalles del error si hay un código de error que proporcione más información. | [Búsqueda de códigos de error](#find-error-code) |
| DeploymentQuotaExceeded | Si se alcanza el límite de 800 implementaciones por grupo de recursos, elimine las implementaciones que ya no necesite del historial. Puede eliminar las entradas del historial con [az group deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) en la CLI de Azure o [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) en PowerShell. Eliminar una entrada del historial de implementaciones no afecta a los recursos de implementación. | |
| DnsRecordInUse | El nombre del registro de DNS debe ser único. Escribe otro nombre. | |
| ImageNotFound | Compruebe la configuración de la imagen de máquina virtual. |  |
| InUseSubnetCannotBeDeleted | Este error puede aparecer al intentar actualizar un recurso y la solicitud se procesa mediante la eliminación y creación del recurso. Asegúrese de especificar todos los valores sin cambios. | [Actualización de recursos](/azure/architecture/building-blocks/extending-templates/update-resource) |
| InvalidAuthenticationTokenTenant | Obtenga el token de acceso para el inquilino adecuado. Solo puede obtener el token del inquilino al que pertenece su cuenta. | |
| InvalidContentLink | Probablemente ha tratado de agregar un vínculo a una plantilla anidada que no está disponible. Compruebe el URI proporcionado para la plantilla anidada. Si la plantilla se encuentra en una cuenta de almacenamiento, asegúrese de que puede accederse al URI. Debe pasar un token de SAS. | [Plantillas vinculadas](resource-group-linked-templates.md) |
| InvalidParameter | Uno de los valores proporcionados para un recurso no coincide con el valor esperado. Este error puede deberse a muchas condiciones diferentes. Por ejemplo, una contraseña puede ser insuficiente o un nombre de blob puede ser incorrecto. El mensaje de error debe indicar qué valor debe corregirse. | |
| InvalidRequestContent | Los valores de implementación incluyen valores que no se reconocen o valores requeridos que faltan. Confirme los valores para el tipo de recurso. | [Referencia de plantilla](/azure/templates/) |
| InvalidRequestFormat | Habilite el registro de depuración cuando se ejecute la implementación y compruebe el contenido de la solicitud. | [Registro de depuración](#enable-debug-logging) |
| InvalidResourceNamespace | Compruebe el espacio de nombres del recurso especificado en la propiedad **type**. | [Referencia de plantilla](/azure/templates/) |
| InvalidResourceReference | El recurso aún no existe o se hace referencia a él de forma incorrecta. Compruebe si tiene que agregar una dependencia. Compruebe que el uso de la función **reference** incluye los parámetros necesarios para su escenario. | [Resolución de dependencias](resource-manager-not-found-errors.md) |
| InvalidResourceType | Compruebe el tipo de recurso especificado en la propiedad **type**. | [Referencia de plantilla](/azure/templates/) |
| InvalidSubscriptionRegistrationState | Registre la suscripción con el proveedor de recursos. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| InvalidTemplate | Compruebe la sintaxis de la plantilla en busca de errores. | [Resolución de plantilla no válida](resource-manager-invalid-template-errors.md) |
| InvalidTemplateCircularDependency | Quite las dependencias innecesarias. | [Resolver dependencias circulares](resource-manager-invalid-template-errors.md#circular-dependency) |
| LinkedAuthorizationFailed | Compruebe si la cuenta pertenece al mismo inquilino que el grupo de recursos en que está realizando la implementación. | |
| LinkedInvalidPropertyId | El identificador de un recurso no se resuelve correctamente. Compruebe que ha proporcionado todos los valores necesarios para el identificador del recurso, entre otros, el identificador de la suscripción, el nombre del grupo de recursos, el tipo de recurso, el nombre del recurso principal (si procede) y el nombre del recurso. | |
| LocationRequired | Proporcione una ubicación para el recurso. | [Establecimiento de la ubicación](resource-group-authoring-templates.md#resource-location) |
| MismatchingResourceSegments | Asegúrese de que el recurso anidado tiene el número correcto de segmentos de nombre y tipo. | [Resolver los segmentos de recursos](resource-manager-invalid-template-errors.md#incorrect-segment-lengths)
| MissingRegistrationForLocation | Compruebe el estado de registro del proveedor de recursos y las ubicaciones admitidas. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| MissingSubscriptionRegistration | Registre la suscripción con el proveedor de recursos. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| NoRegisteredProviderFound | Compruebe el estado de registro del proveedor de recursos. | [Resolución de registros](resource-manager-register-provider-errors.md) |
| NotFound | Puede que esté intentando implementar un recurso dependiente en paralelo con un recurso principal. Compruebe si tiene que agregar una dependencia. | [Resolución de dependencias](resource-manager-not-found-errors.md) |
| OperationNotAllowed | La implementación trata de realizar una operación que excede la cuota de la suscripción, del grupo de recursos o de la región. Si es posible, revise la implementación para respetar las cuotas. De lo contrario, considere la posibilidad de solicitar un cambio de las cuotas. | [Resolución de cuotas](resource-manager-quota-errors.md) |
| ParentResourceNotFound | Asegúrese de que existe un recurso principal antes de crear los recursos secundarios. | [Resolución del recurso principal](resource-manager-parent-resource-errors.md) |
| PasswordTooLong | Puede que haya seleccionado una contraseña con demasiados caracteres, o que haya convertido el valor de contraseña en una cadena segura antes de pasarlo como parámetro. Si la plantilla incluye un parámetro de **cadena segura**, no es necesario convertir el valor en una cadena segura. Proporcione el valor de contraseña como texto. |  |
| PrivateIPAddressInReservedRange | La dirección IP especificada incluye un intervalo de direcciones requerido por Azure. Cambie la dirección IP para evitar el intervalo reservado. | [Direcciones IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PrivateIPAddressNotInSubnet | La dirección IP especificada está fuera del intervalo de subred. Cambie la dirección IP para que esté dentro del intervalo de subred. | [Direcciones IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) |
| PropertyChangeNotAllowed | Algunas propiedades no se pueden cambiar en un recurso implementado. Al actualizar un recurso, limite los cambios a las propiedades permitidas. | [Actualización de recursos](/azure/architecture/building-blocks/extending-templates/update-resource) |
| RequestDisallowedByPolicy | La suscripción incluye una directiva de recursos que impide una acción que está tratando de realizar durante la implementación. Busque la directiva que bloquea la acción. Si es posible, cambie la implementación para cumplir con las limitaciones de la directiva. | [Resolución de directivas](resource-manager-policy-requestdisallowedbypolicy-error.md) |
| ReservedResourceName | Proporcione un nombre de recurso que no incluya un nombre reservado. | [Nombres de recurso reservados](resource-manager-reserved-resource-name.md) |
| ResourceGroupBeingDeleted | Espere a que la eliminación finalice. | |
| ResourceGroupNotFound | Compruebe el nombre del grupo de recursos de destino para la implementación. El grupo de recursos de destino ya debe existir en la suscripción. Compruebe el contexto de la suscripción. | [CLI de Azure](/cli/azure/account?#az-account-set) [PowerShell](/powershell/module/Az.Accounts/Set-AzContext) |
| ResourceNotFound | La implementación hace referencia a un recurso que no se puede resolver. Compruebe que el uso de la función **reference** incluye los parámetros necesarios para su escenario. | [Resolución de referencias](resource-manager-not-found-errors.md) |
| ResourceQuotaExceeded | La implementación trata de crear recursos que exceden la cuota de la suscripción, del grupo de recursos o de la región. Si es posible, revise la infraestructura para respetar las cuotas. De lo contrario, considere la posibilidad de solicitar un cambio de las cuotas. | [Resolución de cuotas](resource-manager-quota-errors.md) |
| SkuNotAvailable | Seleccione la SKU (como el tamaño de la máquina virtual) que está disponible para la ubicación seleccionada. | [Resolución de SKU](resource-manager-sku-not-available-errors.md) |
| StorageAccountAlreadyExists | Proporcione un nombre único para la cuenta de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](resource-manager-storage-account-name-errors.md)  |
| StorageAccountAlreadyTaken | Proporcione un nombre único para la cuenta de almacenamiento. | [Resolución del nombre de la cuenta de almacenamiento](resource-manager-storage-account-name-errors.md) |
| StorageAccountNotFound | Compruebe la suscripción, el grupo de recursos y el nombre de la cuenta de almacenamiento que intenta utilizar. | |
| SubnetsNotInSameVnet | Una máquina virtual solo puede tener una red virtual. Al implementar varias NIC, asegúrese de que pertenecen a la misma red virtual. | [Varias NIC](../virtual-machines/windows/multiple-nics.md) |
| TemplateResourceCircularDependency | Quite las dependencias innecesarias. | [Resolver dependencias circulares](resource-manager-invalid-template-errors.md#circular-dependency) |
| TooManyTargetResourceGroups | Reduzca el número de grupos de recursos de una sola implementación. | [Implementación en varios grupos de recursos](resource-manager-cross-resource-group-deployment.md) |

## <a name="find-error-code"></a>Búsqueda de códigos de error

Hay dos tipos de errores que puede recibir:

* Errores de validación
* Errores de implementación

Los errores de validación que provienen de escenarios que se pueden determinar antes de la implementación incluyen los errores de sintaxis en la plantilla o tratar de implementar los recursos que superarían las cuotas de suscripción. Errores de implementación que provengan de condiciones que se producen durante el proceso de implementación. Incluyen intentar acceder a un recurso que se está implementando en paralelo.

Los dos tipos de errores devuelven un código de error que utiliza para solucionar problemas de implementación. Ambos aparecen en el [registro de actividad](resource-group-audit.md). Sin embargo, los errores de validación no aparecen en el historial de implementación porque la implementación nunca se inicia.

### <a name="validation-errors"></a>Errores de validación

Al implementar a través del portal, verá un error de validación después de enviar sus valores.

![vista del error de validación en el portal](./media/resource-manager-common-deployment-errors/validation-error.png)

Seleccione el mensaje para más detalles. En la siguiente imagen, verá un error **InvalidTemplateDeployment** y un mensaje que indica que una directiva está bloqueando la implementación.

![vista de los detalles de validación](./media/resource-manager-common-deployment-errors/validation-details.png)

### <a name="deployment-errors"></a>Errores de implementación

Cuando la operación pasa la validación, pero se produce un error durante la implementación, recibirá un error de implementación.

Para ver los mensajes y códigos de error de implementación con PowerShell, use:

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName exampledeployment -ResourceGroupName examplegroup).Properties.statusMessage
```

Para ver los mensajes y códigos de error de implementación con la CLI de Azure, use:

```azurecli-interactive
az group deployment operation list --name exampledeployment -g examplegroup --query "[*].properties.statusMessage"
```

En el portal, seleccione la notificación.

![notificación de error](./media/resource-manager-common-deployment-errors/notification.png)

Verá más detalles acerca de la implementación. Seleccione la opción para más información sobre el error.

![error de implementación](./media/resource-manager-common-deployment-errors/deployment-failed.png)

Verá el mensaje de error y los códigos de error. Observe que hay dos códigos de error. El primero (**DeploymentFailed**) es un error general que no proporciona la información que necesita para resolverlo. El segundo código de error (**StorageAccountNotFound**) proporciona los detalles que necesita. 

![detalles del error](./media/resource-manager-common-deployment-errors/error-details.png)

## <a name="enable-debug-logging"></a>Habilitación del registro de depuración

En ocasiones necesitará más información sobre la solicitud y respuesta para descubrir qué ha salido mal. Durante la implementación, puede solicitar que la información adicional se registre durante una implementación. 

### <a name="powershell"></a>PowerShell

En PowerShell, establezca el parámetro **DeploymentDebugLogLevel** en Todos, ResponseContent o RequestContent.

```powershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile c:\Azure\Templates\storage.json `
  -DeploymentDebugLogLevel All
```

Examine el contenido de la solicitud con el siguiente cmdlet:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.request `
| ConvertTo-Json
```

O bien, la respuesta de contenido con:

```powershell
(Get-AzResourceGroupDeploymentOperation `
-DeploymentName exampledeployment `
-ResourceGroupName examplegroup).Properties.response `
| ConvertTo-Json
```

Esta información puede ayudarlo a determinar si un valor en la plantilla se está estableciendo de forma incorrecta.

### <a name="azure-cli"></a>CLI de Azure

Actualmente, la CLI de Azure no admite activar el registro de depuración, pero sí puede recuperarlo.

Examine las operaciones de implementación con el siguiente comando:

```azurecli
az group deployment operation list \
  --resource-group examplegroup \
  --name exampledeployment
```

Examine el contenido de la solicitud con el siguiente comando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.request
```

Examine el contenido de la respuesta con el siguiente comando:

```azurecli
az group deployment operation list \
  --name exampledeployment \
  -g examplegroup \
  --query [].properties.response
```

### <a name="nested-template"></a>Plantilla anidada

Para registrar la información de depuración de una plantilla anidada, use el elemento **debugSetting**.

```json
{
    "apiVersion": "2016-09-01",
    "name": "nestedTemplate",
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "templateLink": {
            "uri": "{template-uri}",
            "contentVersion": "1.0.0.0"
        },
        "debugSetting": {
           "detailLevel": "requestContent, responseContent"
        }
    }
}
```

## <a name="create-a-troubleshooting-template"></a>Creación de una plantilla de solución de problemas

En algunos casos, la manera más fácil de solucionar problemas de plantillas es comprobando sus elementos. Puede crear una plantilla simplificada que le permita centrarse en lo que crea que está provocando el error. Por ejemplo, supongamos que se recibe un error al hacer referencia a un recurso. En lugar de trabajar directamente con una plantilla de toda, cree una plantilla que devuelva la parte que pueda estar causando el problema. Esto puede ayudarlo a determinar si está pasando los parámetros correctos con las funciones de plantilla correctamente y obteniendo el recurso esperado.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

Supongamos que se están produciendo errores de implementación que cree que están relacionados con dependencias establecidas incorrectamente. Pruebe la plantilla dividiéndola en plantillas simplificadas. En primer lugar, cree una plantilla que implemente solo un único recurso (por ejemplo, un servidor SQL Server). Cuando esté seguro de que tiene dicho recurso definido correctamente, agregue un recurso que dependa de él (por ejemplo, SQL Database). Cuando esos dos recursos se definan correctamente, agregue otros recursos dependientes (por ejemplo, las directivas de auditoría). Entre cada implementación de prueba, elimine el grupo de recursos para asegurarse de que se prueban adecuadamente las dependencias.


## <a name="next-steps"></a>Pasos siguientes

* Para realizar un tutorial de solución de problemas, consulte [Tutorial: Troubleshoot Resource Manager template deployments](./resource-manager-tutorial-troubleshoot.md) (Tutorial: Solución de problemas con las implementaciones de plantillas de Resource Manager)
* Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](resource-group-audit.md).
* Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](resource-manager-deployment-operations.md).
