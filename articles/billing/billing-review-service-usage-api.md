---
title: Consulta del uso de recursos de los servicios de Azure con la API REST | Microsoft Docs
description: Aprenda a utilizar las API REST de Azure para consultar el uso de recursos por parte de los servicios de Azure.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: banders
ms.openlocfilehash: 47e19fae26d6e3bc465799980c587d7bb7ed5e92
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443059"
---
# <a name="review-azure-resource-usage-using-the-rest-api"></a>Consulta del uso de recursos de Azure con la API REST

Azure Cost Management API le ayuda a consultar y administrar el consumo de los recursos de Azure.

En este artículo, aprenderá a crear un informe diario que generará un documento de valores separados por comas con los datos de uso por hora. También aprenderá a utilizar filtros para personalizar el informe de forma que pueda consultar el uso de las máquinas virtuales, las bases de datos y los recursos etiquetados de un grupo de recursos de Azure.

>[!NOTE]
> Actualmente, Cost Management API está disponible como versión preliminar privada.

## <a name="create-a-basic-cost-management-report"></a>Creación de un informe básico sobre administración de costos

Utilice la operación `reports` de Cost Management API para especificar cómo se van a generar los informes de costos y dónde se van a publicar.

```http
https://management.azure.com/subscriptions/{subscriptionGuid}/providers/Microsoft.CostManagement/reports/{reportName}?api-version=2018-09-01-preview
Content-Type: application/json   
Authorization: Bearer
```

El parámetro `{subscriptionGuid}` es obligatorio y debe contener un identificador de suscripción que pueda leerse con las credenciales proporcionadas en el token de la API. `{reportName}`

Los siguientes encabezados son obligatorios: 

|Encabezado de solicitud|DESCRIPCIÓN|  
|--------------------|-----------------|  
|*Content-Type:*| Necesario. Establézcalo en `application/json`. |  
|*Authorization:*| Necesario. Debe establecerse en un token `Bearer` válido. |

Configure los parámetros del informe en el cuerpo de la solicitud HTTP. En el ejemplo siguiente, el informe se ha configurado para que se genere cada día cuando esté activo, es un archivo CSV que se escribe en un contenedor de blobs de Azure Storage y contiene información sobre los costos por hora de todos los recursos del grupo de recursos `westus`.

```json
{
    "properties": {
        "schedule": {
            "status": "Inactive",
            "recurrence": "Daily",
            "recurrencePeriod": {
                "from": "2018-08-21",
                "to": "2019-10-31"
            }
        },
        "deliveryInfo": {
            "destination": {
                "resourceId": "/subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}",
                "container": "MyReportContainer",
                "rootFolderPath": "MyScheduleTest"
            }
        },
        "format": "Csv",
        "definition": {
            "type": "Usage",
            "timeframe": "MonthToDate",
            "dataSet": {
                "granularity": "Hourly",
                "filter": {
                    "dimensions": {
                        "name": "ResourceLocation",
                        "operator": "In",
                        "values": [
                            "westus"
                        ]
                    }
                }
            }
        }
    }
}
```

Tenga en cuenta que

## <a name="filtering-reports"></a>Filtrado de informes

Cuando se crea un informe, la sección `filter` y `dimensions` del cuerpo de la solicitud permiten concentrarse en los costos de ciertos tipos de recursos. En el cuerpo de la solicitud anterior, se muestra cómo se puede filtrar por todos los recursos de una región. 

### <a name="get-all-compute-usage"></a>Consulta de toda la información sobre el uso de procesos

Utilice la dimensión `ResourceType` para crear informes sobre los costos de las máquinas virtuales de Azure generados en su suscripción en todas las regiones.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.ClassicCompute/virtualMachines", 
                "Microsoft.Compute/virtualMachines"
        ] 
    }
}
```

### <a name="get-all-database-usage"></a>Consulta de toda la información sobre el uso de bases de datos

Utilice la dimensión `ResourceType` para crear informes sobre los costos de Azure SQL Database generados en su suscripción en todas las regiones.

```json
"filter": {
    "dimensions": {
        "name": "ResourceType",
        "operator": "In",
        "values": [
                "Microsoft.Sql/servers"
        ] 
    }
}
```

### <a name="report-on-specific-instances"></a>Informes sobre instancias específicas

La dimensión `Resource` le permite crear informes sobre los costos de determinados recursos.

```json
"filter": {
    "dimensions": {
        "name": "Resource",
        "operator": "In",
        "values": [
            "subscriptions/{subscriptionGuid}/resourceGroups/{resourceGroup}/providers/Microsoft.ClassicCompute/virtualMachines/{ResourceName}"
        ]
    }
}
```

### <a name="changing-timeframes"></a>Modificación de los períodos de tiempo

Establezca la definición de `timeframe` en `Custom` para definir un período de tiempo diferente al de las opciones integradas "Semana hasta la fecha" y "Mes hasta la fecha".

```json
"timeframe": "Custom",
"timePeriod": {
    "from": "2017-12-31T00:00:00.000Z",
    "to": "2018-12-30T00:00:00.000Z"
}
```

## <a name="next-steps"></a>Pasos siguientes
- [Get started with Azure REST API](https://docs.microsoft.com/rest/api/azure/) (Introducción a la API REST de Azure)   
