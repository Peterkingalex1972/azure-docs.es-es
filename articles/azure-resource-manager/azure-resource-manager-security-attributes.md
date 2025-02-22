---
title: Atributos de seguridad de Azure Resource Manager
description: Lista de comprobación de los atributos de seguridad para evaluar Azure Resource Manager
services: azure-resource-manager
author: msmbaldwin
manager: barbkess
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mbaldwin
ms.openlocfilehash: e3bfb79c54ff57adfa947f2dd0100f6c05c7af9f
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444148"
---
# <a name="security-attributes-for-azure-resource-manager"></a>Atributos de seguridad de Azure Resource Manager

En este artículo, se documentan los atributos de seguridad integrados en Azure Resource Manager.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevención

| Atributo de seguridad | Sí/No | Notas |
|---|---|--|
| Cifrado en reposo (por ejemplo, cifrado en el servidor, cifrado en el servidor con claves administradas por el cliente y otras características de cifrado)| Sí |  |
| Cifrado en tránsito (por ejemplo, cifrado de ExpressRoute, cifrado en red virtual y cifrado de red virtual a red virtual)| Sí | HTTPS/TLS. |
| Control de clave de cifrado (CMK, BYOK, etc.)| N/D | Azure Resource Manager no almacena ningún contenido de cliente, solo los datos de control. |
| Cifrado de nivel de columna (Azure Data Services)| Sí | |
| Llamadas a API cifradas| Sí | |

## <a name="network-segmentation"></a>Segmentación de red

| Atributo de seguridad | Sí/No | Notas |
|---|---|--|
| Compatibilidad con punto de conexión de servicio| Sin | |
| Compatibilidad con inserción de red virtual| Sí | |
| Compatibilidad con aislamiento de red y firewalls| Sin |  |
| Compatibilidad con la tunelización forzada| Sin |  |

## <a name="detection"></a>Detección

| Atributo de seguridad | Sí/No | Notas|
|---|---|--|
| Compatibilidad con supervisión de Azure (Log Analytics, Application Insights, etc.)| Sin | |

## <a name="identity-and-access-management"></a>Administración de identidades y acceso

| Atributo de seguridad | Sí/No | Notas|
|---|---|--|
| Authentication| Sí | Basado en [Azure Active Directory](/azure/active-directory).|
| Authorization| Sí | |


## <a name="audit-trail"></a>Pista de auditoría

| Atributo de seguridad | Sí/No | Notas|
|---|---|--|
| Registro y auditoría del plano de administración y de control| Sí | Los registros de actividad exponen todas operaciones de escritura (PUT, POST, DELETE) realizadas en los recursos; consulte [Visualización de registros de actividad para supervisar acciones sobre recursos](resource-group-audit.md). |
| Registro y auditoría del plano de datos| N/D | |

## <a name="configuration-management"></a>Administración de configuración

| Atributo de seguridad | Sí/No | Notas|
|---|---|--|
| Compatibilidad con la administración de configuración (control de versiones de configuración, etc.)| Sí |  |
