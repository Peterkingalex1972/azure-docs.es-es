---
title: 'Incorporación de un cliente a la administración de recursos delegados de Azure: Azure Lighthouse'
description: Obtenga información sobre cómo incorporar un cliente a la administración de recursos delegados de Azure, lo que permite administrar sus recursos y acceder a ellos desde su propio inquilino.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 08/22/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 35cf61897d012690f0a0f752a7cb36270e11e10e
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012058"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Incorporación de un cliente a la administración de recursos delegados de Azure

En este artículo se explica cómo, como proveedor de servicios, puede incorporar un cliente a la administración de recursos delegados de Azure, lo que permite tener acceso a sus recursos delegados (suscripciones o grupos de recursos) y administrarlos a través de su propio inquilino de Azure Active Directory (Azure AD). Aunque aquí nos referiremos a los proveedores de servicios y clientes, las empresas que administren varios inquilinos pueden usar el mismo proceso para consolidar su experiencia de administración.

Puede repetir este proceso si está administrando recursos para varios clientes. A continuación, cuando un usuario autorizado inicia sesión en el inquilino, se puede autorizar al usuario en los ámbitos del inquilino del cliente para realizar operaciones de administración sin tener que iniciar sesión en todos los inquilinos de cliente individuales.

Puede asociar el identificador de Microsoft Partner Network (MPN) con las suscripciones incorporadas para realizar un seguimiento de su impacto en las involucraciones de clientes y recibir el reconocimiento correspondiente. Para más información, consulte [Vinculación de un Id. de partner a cuentas de Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Tenga en cuenta que deberá realizar esta asociación por separado para cada inquilino de cliente cuyos recursos esté administrando. 

> [!NOTE]
> Los clientes se pueden incorporar automáticamente cuando compran una oferta de servicios administrados (pública o privada) publicada en Azure Marketplace. Para obtener más información, consulte [Publicar ofertas de servicios administrados en Azure Marketplace](publish-managed-services-offers.md). También puede usar el proceso de incorporación que se describe aquí con una oferta publicada en Azure Marketplace.

El proceso de incorporación requiere que se tomen medidas desde el inquilino del proveedor de servicios y del inquilino del cliente. Todos estos pasos se describen en este artículo.

> [!IMPORTANT]
> Actualmente, no se puede incorporar una suscripción (o un grupo de recursos de una suscripción) para la administración de recursos delegados de Azure si la suscripción usa Azure Databricks. Del mismo modo, si una suscripción se ha registrado para la incorporación con el proveedor de recursos **Microsoft.ManagedServices**, en este momento, no podrá crear un área de trabajo de Databricks para la suscripción.

## <a name="gather-tenant-and-subscription-details"></a>Recopilación de los detalles del inquilino y la suscripción

Para incorporar el inquilino de un cliente, este debe tener una suscripción activa a Azure. Necesitará saber:

- El identificador del inquilino del proveedor de servicios (donde va a administrar los recursos del cliente)
- El identificador del inquilino del cliente (cuyos recursos administrará el proveedor de servicios)
- Los identificadores de suscripción de cada suscripción específica del inquilino del cliente que administrará el proveedor de servicios (o que contenga los grupos de recursos que administrará el proveedor de servicios)

Si aún no tiene esta información, puede recuperarla de una de las siguientes maneras.

### <a name="azure-portal"></a>Portal de Azure

Para ver el identificador del inquilino, mantenga el mouse sobre el nombre de la cuenta en la parte superior derecha de Azure Portal o seleccione **Cambiar directorio**. Para seleccionar y copiar el identificador del inquilino, busque "Azure Active Directory" desde dentro del portal y, a continuación, seleccione **Propiedades** y copie el valor que se muestra en campo **Id. de directorio**. Para buscar el identificador de una suscripción, busque "Suscripciones" y, a continuación, seleccione el identificador de suscripción adecuado.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```


## <a name="ensure-the-customers-subscription-is-registered-for-onboarding"></a>Comprobar que la suscripción del cliente está registrada para la incorporación

Cada suscripción se debe autorizar para la incorporación registrando manualmente el proveedor de recursos **Microsoft.ManagedServices**. Para registrar una suscripción, el cliente puede seguir los pasos que se describen en [Tipos y proveedores de recursos de Azure](../../azure-resource-manager/resource-manager-supported-services.md).

El cliente puede confirmar que la suscripción está lista para la incorporación de una de las siguientes maneras.

### <a name="azure-portal"></a>Portal de Azure

1. En Azure Portal, seleccione la suscripción.
1. Seleccione **Proveedores de recursos**.
1. Confirme que **Microsoft.ManagedServices** aparece **Registrado**.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Set-AzContext -Subscription <subscriptionId>
Get-AzResourceProvider -ProviderNameSpace 'Microsoft.ManagedServices'
```

Esto debería devolver resultados similares a estos:

```output
ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationDefinitions}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {registrationAssignments}
Locations         : {}

ProviderNamespace : Microsoft.ManagedServices
RegistrationState : Registered
ResourceTypes     : {operations}
Locations         : {}
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set –subscription <subscriptionId>
az provider show --namespace "Microsoft.ManagedServices" --output table
```

Esto debería devolver resultados similares a estos:

```output
Namespace                  RegistrationState
-------------------------  -------------------
Microsoft.ManagedServices  Registered
```

## <a name="define-roles-and-permissions"></a>Definir roles y permisos

Como proveedor de servicios, es posible que quiera usar varias ofertas con un solo cliente, lo que requiere un acceso diferente para distintos ámbitos.

Para facilitar la administración, se recomienda usar grupos de usuarios de Azure AD para cada rol, lo que le permite agregar o quitar usuarios individuales en el grupo, en lugar de asignar permisos directamente a ese usuario. También es posible que quiera asignar roles a una entidad de servicio. Asegúrese de seguir el principio de privilegios mínimos para que los usuarios solo tengan los permisos necesarios para completar su trabajo, lo que ayuda a reducir la posibilidad de errores involuntarios. Para obtener más información, consulte [Recommended security practices](../concepts/recommended-security-practices.md) (Prácticas de seguridad recomendadas).

> [!NOTE]
> Las asignaciones de roles deben usar [roles integrados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) de control de acceso basado en rol (RBAC). Actualmente, todos los roles integrados se admiten con la administración de recursos delegados de Azure, excepto el rol Propietario y los roles integrados con el permiso [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions). El rol integrado de administrador de acceso de usuarios se admite con un uso limitado, tal y como se describe a continuación. Tampoco se admiten los roles personalizados y [los roles de administrador de la suscripción clásica](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators).

Para definir las autorizaciones, debe conocer los valores de identificador de cada usuario, grupo de usuarios o entidad de servicio al que quiera conceder acceso. También necesitará el identificador de definición de roles para cada rol integrado que quiera asignar. Si aún no los tiene, puede recuperarlos de una de las siguientes maneras.



### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```

## <a name="create-an-azure-resource-manager-template"></a>Creación de una plantilla de Azure Resource Manager

Para incorporar el cliente, deberá crear una plantilla de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) que incluya lo siguiente:

|Campo  |Definición  |
|---------|---------|
|**mspName**     |Nombre del proveedor de servicios         |
|**mspOfferDescription**     |Breve descripción de la oferta (por ejemplo, "oferta de administración de VM de Contoso")      |
|**managedByTenantId**     |El identificador de inquilino         |
|**authorizations**     |Los valores de **principalId** para los usuarios/grupos/SPN del inquilino, cada uno de ellos con un valor de **principalIdDisplayName** para ayudar a su cliente a entender el propósito de la autorización y asignarla a un valor de **roleDefinitionId** integrado para especificar el nivel de acceso         |

Para incorporar una suscripción de cliente, use la plantilla adecuada de Azure Resource Manager que proporcionamos en nuestro [repositorio de ejemplos](https://github.com/Azure/Azure-Lighthouse-samples/), junto con un archivo de parámetros correspondiente que modifique para que coincida con la configuración y defina su autorizaciones. Se proporcionan plantillas independientes en función de si se incorpora una suscripción completa, un grupo de recursos o varios grupos de recursos dentro de una suscripción. También proporcionamos una plantilla que se puede usar para los clientes que han adquirido una oferta de servicios administrados que ha publicado en Azure Marketplace, si prefiere incorporar sus suscripciones de esta manera.

|**Para incorporar esto**  |**Use esta plantilla de Azure Resource Manager**  |**Y modifique este archivo de parámetros** |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Varios grupos de recursos de una suscripción   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Suscripción (al usar una oferta publicada en Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> El proceso que se describe aquí requiere una implementación independiente para cada suscripción que se incorpore.
> 
> También se necesitan implementaciones independientes si se incorporan varios grupos de recursos en distintas suscripciones. Sin embargo, la incorporación de varios grupos de recursos dentro de una sola suscripción puede realizarse en una implementación.

En el ejemplo siguiente se muestra un archivo **resourceProjection.parameters.json** modificado que se usará para incorporar una suscripción. Los archivos de parámetros del grupo de recursos (situados en la carpeta [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management)) son similares, pero también incluyen un parámetro **rgName** para identificar los grupos de recursos específicos que se incorporarán.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
La última autorización del ejemplo anterior agrega un valor de **principalId** con el rol de administrador de acceso de usuario (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Al asignar este rol, debe incluir la propiedad **delegatedRoleDefinitionIds** y uno o más roles integrados. El usuario creado en esta autorización podrá asignar estos roles integrados a las identidades administradas. Tenga en cuenta que no se aplicará a este usuario ningún otro permiso asociado normalmente al rol Administrador de acceso de usuario.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implementación de las plantillas de Azure Resource Manager

Una vez actualizado el archivo de parámetros, el cliente debe implementar la plantilla de administración de recursos en el inquilino de su cliente como implementación de nivel de suscripción. Se necesita una implementación independiente para cada suscripción que quiera incorporar a la administración de recursos delegados de Azure (o para cada suscripción que contenga grupos de recursos que quiera incorporar).

> [!IMPORTANT]
> La implementación debe realizarse desde una cuenta que no sea de invitado en el inquilino del cliente que tenga el [rol Propietario integrado](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para la suscripción que se va a incorporar (o que contiene los grupos de recursos que se están incorporando).

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Confirmar la incorporación correcta

Cuando una suscripción de cliente se incorpora correctamente a la administración de recursos delegados de Azure, los usuarios del inquilino del proveedor de servicios pueden ver la suscripción y sus recursos (si se les ha concedido acceso a ellos mediante el proceso anterior, ya sea individualmente o como miembro de un grupo de Azure AD con los permisos adecuados). Para confirmarlo, compruebe que la suscripción aparece de una de las siguientes maneras.  

### <a name="azure-portal"></a>Portal de Azure

En el inquilino del proveedor de servicios:

1. Vaya a la página [Mis clientes](view-manage-customers.md).
2. Seleccione **Clientes**.
3. Confirme que puede ver las suscripciones con el nombre de la oferta que proporcionó en la plantilla de Resource Manager.

En el inquilino del cliente:

1. Vaya a la [página Proveedores de servicios](view-manage-service-providers.md).
2. Seleccione **Ofertas del proveedor de servicios**.
3. Confirme que puede ver las suscripciones con el nombre de la oferta que proporcionó en la plantilla de Resource Manager.

> [!NOTE]
> Una vez completada la implementación, las actualizaciones pueden tardar unos minutos en verse reflejadas en Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
- [Vea y administre sus clientes](view-manage-customers.md) desde **Mis clientes**, en Azure Portal.
