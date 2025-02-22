---
title: Tipos de recursos y proveedores de recursos de Azure | Microsoft Docs
description: Describe los proveedores de recursos que admiten el Administrador de recursos, sus esquemas y las versiones disponibles de API y las regiones que pueden hospedar los recursos.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2019
ms.author: tomfitz
ms.openlocfilehash: a3c6eca548eb61d6b7b239b4292b9c77ca2dec6f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702699"
---
# <a name="azure-resource-providers-and-types"></a>Tipos y proveedores de recursos de Azure

Al implementar los recursos, con frecuencia necesitará recuperar información sobre los tipos y proveedores de recursos. En este artículo, aprenderá a:

* Ver todos los proveedores de recursos de Azure
* Comprobar el estado de registro de un proveedor de recursos
* Registrar un proveedor de recursos
* Ver los tipos de recursos de un proveedor
* Ver las ubicaciones válidas de un tipo de recurso
* Ver las versiones de API válidas de un tipo de recurso

Puede llevar a cabo estos pasos en Azure Portal, Azure PowerShell o la CLI de Azure.

Para obtener una lista que asigna proveedores de recursos con servicios de Azure, consulte [Proveedores de recursos para servicios de Azure](azure-services-resource-providers.md).

## <a name="azure-portal"></a>Portal de Azure

Para ver todos los proveedores de recursos y el estado de registro de su suscripción:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios**.

    ![selección de suscripciones](./media/resource-manager-supported-services/select-subscriptions.png)
3. En el cuadro **Todos los servicios**, escriba **suscripción** y, a continuación, seleccione **Suscripciones**.
4. Seleccione la suscripción en la lista de suscripción para verla.
5. Seleccione **Proveedores de recursos** y consulte la lista de proveedores de recursos disponibles.

    ![vista de los proveedores de recursos](./media/resource-manager-supported-services/show-resource-providers.png)

6. Al registrar un proveedor de recursos se configura la suscripción para que funcione con este. El ámbito de registro es siempre la suscripción. De forma predeterminada, muchos proveedores de recursos se registran automáticamente. Pero es posible que tenga que registrar manualmente algunos. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación `/register/action` para este. Esta operación está incluida en los roles Colaborador y Propietario. Para registrar un proveedor de recursos, seleccione **Registro**. En la captura de pantalla anterior, el vínculo **Registrar** se resalta para **Microsoft.Blueprint**.

    No se puede anular el registro de un proveedor de recursos si todavía dispone de tipos de recursos de dicho proveedor en la suscripción.

Para ver información de un proveedor de recursos concreto:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios**.

    ![seleccione Todos los servicios](./media/resource-manager-supported-services/more-services.png)

3. En el cuadro **Todos los servicios**, escriba **Explorador de recursos** y, a continuación, seleccione **Explorador de recursos**.
4. Expanda **Proveedores**; para ello, seleccione la flecha derecha.

    ![Selección de proveedores](./media/resource-manager-supported-services/select-providers.png)

5. Expanda un proveedor de recursos y el tipo de recurso que desea ver.

    ![Selección del tipo de recurso](./media/resource-manager-supported-services/select-resource-type.png)

6. El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso. El explorador de recursos muestra las ubicaciones válidas para el tipo de recurso.

    ![Vista de las ubicaciones](./media/resource-manager-supported-services/show-locations.png)

7. La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST. El explorador de recursos muestra las versiones de API válidas para el tipo de recurso.

    ![Vista de las versiones de API](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para ver todos los proveedores de recursos de Azure y el estado de registro de su suscripción, use:

```azurepowershell-interactive
Get-AzResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

Que devuelve resultados similares a:

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Al registrar un proveedor de recursos se configura la suscripción para que funcione con este. El ámbito de registro es siempre la suscripción. De forma predeterminada, muchos proveedores de recursos se registran automáticamente. Pero es posible que tenga que registrar manualmente algunos. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación `/register/action` para este. Esta operación está incluida en los roles Colaborador y Propietario.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que devuelve resultados similares a:

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

No se puede anular el registro de un proveedor de recursos si todavía dispone de tipos de recursos de dicho proveedor en la suscripción.

Para ver información de un proveedor de recursos concreto, use:

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Batch
```

Que devuelve resultados similares a:

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

Para ver los tipos de recursos de un proveedor, use:

```azurepowershell-interactive
(Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

Que devuelve:

```powershell
batchAccounts
operations
locations
locations/quotas
```

La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST.

Para obtener las versiones de API de un tipo de recurso, use:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

Que devuelve:

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso.

Para obtener las ubicaciones compatibles con un tipo de recurso, use:

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

Que devuelve:

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI

Para ver todos los proveedores de recursos de Azure y el estado de registro de su suscripción, use:

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

Que devuelve resultados similares a:

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

Al registrar un proveedor de recursos se configura la suscripción para que funcione con este. El ámbito de registro es siempre la suscripción. De forma predeterminada, muchos proveedores de recursos se registran automáticamente. Pero es posible que tenga que registrar manualmente algunos. Para registrar un proveedor de recursos, debe tener permiso para realizar la operación `/register/action` para este. Esta operación está incluida en los roles Colaborador y Propietario.

```azurecli
az provider register --namespace Microsoft.Batch
```

Que devuelve un mensaje que indica que el registro está en curso.

No se puede anular el registro de un proveedor de recursos si todavía dispone de tipos de recursos de dicho proveedor en la suscripción.

Para ver información de un proveedor de recursos concreto, use:

```azurecli
az provider show --namespace Microsoft.Batch
```

Que devuelve resultados similares a:

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

Para ver los tipos de recursos de un proveedor, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

Que devuelve:

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

La versión de API se corresponde a una versión de operaciones de API de REST que se publican por el proveedor de recursos. Conforme un proveedor de recursos habilite nuevas características, publicará una nueva versión de la API de REST.

Para obtener las versiones de API de un tipo de recurso, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

Que devuelve:

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

El Administrador de recursos se admite en todas las regiones, pero puede que los recursos que implementa no se admitan en todas las regiones. Además, puede haber limitaciones en su suscripción que le impidan utilizar algunas regiones que admiten el recurso.

Para obtener las ubicaciones compatibles con un tipo de recurso, use:

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

Que devuelve:

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre la creación de plantillas del Administrador de recursos, consulte [Creación de plantillas del Administrador de recursos de Azure](resource-group-authoring-templates.md). 
* Para ver los esquemas de plantilla de proveedor de recursos, consulte [Referencia de plantilla](/azure/templates/).
* Para obtener una lista que asigna proveedores de recursos con servicios de Azure, consulte [Proveedores de recursos para servicios de Azure](azure-services-resource-providers.md).
* Para ver las operaciones de un proveedor de recursos, consulte [API de REST de Azure](/rest/api/).
