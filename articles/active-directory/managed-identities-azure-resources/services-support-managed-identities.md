---
title: Servicios de Azure que admiten identidades administradas para recursos de Azure
description: Lista de servicios que admiten identidades administradas para recursos de Azure y autenticación de Azure AD
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5630c157ef7843726bcc17455a1e683e58b88ffd
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69905238"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Servicios que admiten identidades administradas para recursos de Azure

Las identidades administradas para los recursos de Azure proporcionan a los servicios de Azure una identidad administrada automáticamente en Azure Active Directory. Mediante una identidad administrada puede autenticarse en cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. Estamos en el proceso de integración de identidades administradas para recursos de Azure y de la autenticación de Azure AD en Azure. Compruebe con frecuencia si existen actualizaciones.

> [!NOTE]
> Identidades administradas para recursos de Azure es el nombre con el que ahora se conoce al servicio Managed Service Identity (MSI).

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Servicios de Azure que admiten identidades administradas de recursos de Azure

Los siguientes servicios de Azure admiten identidades administradas para recursos de Azure:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | Vista previa | Vista previa | Vista previa | 
| Asignado por el usuario | Vista previa | Vista previa | Vista previa | Vista previa |

Consulte la lista siguiente para configurar la identidad administrada para Azure Virtual Machines (en las regiones donde esté disponible):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI de Azure](qs-configure-cli-windows-vm.md)
- [Plantillas del Administrador de recursos de Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Conjuntos de escalado de máquinas virtuales de Azure

|Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | Vista previa | Vista previa | Vista previa |
| Asignado por el usuario | Vista previa | Vista previa | Vista previa | Vista previa |

Consulte la lista siguiente para configurar la identidad administrada para Azure Virtual Machine Scale Sets (en las regiones donde esté disponible):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [CLI de Azure](qs-configure-cli-windows-vm.md)
- [Plantillas del Administrador de recursos de Azure](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | Disponible | Disponible | Disponible |
| Asignado por el usuario | Disponible | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure App Service (en las regiones donde esté disponible):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI de Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Plantilla de Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprint

|Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Vista previa | No disponible | No disponible | No disponible |
| Asignado por el usuario | Vista previa | No disponible | No disponible | No disponible |

Consulte la siguiente lista para usar una identidad administrada con [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure Portal: asignación de plano técnico](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [API de REST: asignación de plano técnico](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Tipo de identidad administrada |Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | Disponible | Disponible | Disponible |
| Asignado por el usuario | Disponible | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure Functions (en las regiones donde esté disponible):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [CLI de Azure](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Plantilla de Azure Resource Manager](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Vista previa | Vista previa | No disponible | Vista previa |
| Asignado por el usuario | No disponible | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure Logic Apps (en las regiones donde esté disponible):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#azure-portal)
- [Plantilla de Azure Resource Manager](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | No disponible | No disponible | No disponible |
| Asignado por el usuario | No disponible | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure Data Factory V2 (en las regiones donde esté disponible):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | Disponible | No disponible | No disponible |
| Asignado por el usuario | No disponible | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure API Management (en las regiones donde esté disponible):

- [Plantilla de Azure Resource Manager](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Linux: Vista previa<br>Windows: No disponible | No disponible | No disponible | No disponible |
| Asignado por el usuario | Linux: Vista previa<br>Windows: No disponible | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure Container Instances (en las regiones donde esté disponible):

- [CLI de Azure](~/articles/container-instances/container-instances-managed-identity.md)
- [Plantilla de Azure Resource Manager](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Tareas de Azure Container Registry

Tipo de identidad administrada | Regiones globales de Azure<br>con disponibilidad general | Azure Government | Azure Alemania | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Asignado por el sistema | Disponible | No disponible | No disponible | No disponible |
| Asignado por el usuario | Vista previa | No disponible | No disponible | No disponible |

Consulte la lista siguiente para configurar la identidad administrada para Azure Container Registry Tasks (en las regiones donde esté disponible):

- [CLI de Azure](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Servicios de Azure que admiten la autenticación de Azure AD

Los siguientes servicios admiten la autenticación de Azure AD y se han probado con los servicios de cliente que usan identidades administradas para recursos de Azure.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Consulte la siguiente lista para configurar el acceso a Azure Resource Manager:

- [Asignar el acceso a través de Azure Portal](howto-assign-access-portal.md)
- [Asignar el acceso a través de Powershell](howto-assign-access-powershell.md)
- [Asignar el acceso a través de la CLI de Azure](howto-assign-access-CLI.md)
- [Asignar el acceso a través de una plantilla de Azure Resource Manager](../../role-based-access-control/role-assignments-template.md)

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Disponible |
| Azure Government | `https://management.usgovcloudapi.net/` | Disponible |
| Azure Alemania | `https://management.microsoftazure.de/` | Disponible |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Disponible |

### <a name="azure-key-vault"></a>Azure Key Vault

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Disponible |
| Azure Government | `https://vault.usgovcloudapi.net` | Disponible |
| Azure Alemania |  `https://vault.microsoftazure.de` | Disponible |
| Azure China 21Vianet | `https://vault.azure.cn` | Disponible |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Disponible |
| Azure Government |  | No disponible |
| Azure Alemania |   | No disponible |
| Azure China 21Vianet |  | No disponible |

### <a name="azure-sql"></a>Azure SQL 

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Disponible |
| Azure Government | `https://database.usgovcloudapi.net/` | Disponible |
| Azure Alemania | `https://database.cloudapi.de/` | Disponible |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Disponible |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Vista previa |
| Azure Government |  | No disponible |
| Azure Alemania |   | No disponible |
| Azure China 21Vianet |  | No disponible |

### <a name="azure-service-bus"></a>Azure Service Bus

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Vista previa |
| Azure Government |  | No disponible |
| Azure Alemania |   | No disponible |
| Azure China 21Vianet |  | No disponible |

### <a name="azure-storage-blobs-and-queues"></a>Colas y blobs de Azure Storage

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponible |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponible |
| Azure Alemania | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponible |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Disponible |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Nube | Id. de recurso | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Disponible |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Disponible |
| Azure Alemania | `https://*.asazure.cloudapi.de` | Disponible |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Disponible |
