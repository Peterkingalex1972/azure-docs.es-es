---
title: Esquema de eventos del registro de actividad de Azure
description: Consulte el esquema de eventos de los datos que se emiten en el registro de actividad
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 1/16/2019
ms.author: dukek
ms.subservice: logs
ms.openlocfilehash: abe2ed0d50ce26ddebeeeccb87c49fc20db43b2a
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515379"
---
# <a name="azure-activity-log-event-schema"></a>Esquema de eventos del registro de actividad de Azure
El **registro de actividad de Azure** es un registro que proporciona información de los eventos de nivel de suscripción que se han producido en Azure. En este artículo se describe el esquema de eventos por categoría de datos. El esquema de los datos es diferente en función de si va a leer los datos en el portal, PowerShell, la CLI o directamente mediante la API REST en comparación con la [transmisión de datos a Storage o Event Hubs mediante un perfil de registro](activity-log-export.md). Los ejemplos siguientes muestran el esquema puesto a disposición por el portal, PowerShell, la CLI y la API REST. Al final del artículo se proporciona una asignación de estas propiedades al [esquema de registros de diagnóstico de Azure](diagnostic-logs-schema.md).

## <a name="administrative"></a>Administrativo
Esta categoría contiene el registro de todas las operaciones de creación, actualización, eliminación y acción realizadas a través de Resource Manager. Los ejemplos de los tipos de eventos que aparecen en esta categoría incluyen "crear máquina virtual" y "eliminar grupo de seguridad de red". Cada acción realizada por un usuario o una aplicación mediante Resource Manager se modela como una operación en un tipo de recurso determinado. Si el tipo de operación es Write, Delete o Action, los registros de inicio y corrección o error de esa operación se registran en la categoría Administrativo. La categoría Administrativo también incluye los cambios realizados en el control de acceso basado en roles de una suscripción.

### <a name="sample-event"></a>Evento de ejemplo
```json
{
    "authorization": {
        "action": "Microsoft.Network/networkSecurityGroups/write",
        "scope": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG"
    },
    "caller": "rob@contoso.com",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "_claim_names": "{\"groups\":\"src1\"}",
        "_claim_sources": "{\"src1\":{\"endpoint\":\"https://graph.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/users/f409edeb-4d29-44b5-9763-ee9348ad91bb/getMemberObjects\"}}",
        "http://schemas.microsoft.com/claims/authnclassreference": "1",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "rsa,mfa",
        "appid": "355249ed-15d9-460d-8481-84026b065942",
        "appidacr": "2",
        "http://schemas.microsoft.com/2012/01/devicecontext/claims/identifier": "10845a4d-ffa4-4b61-a3b4-e57b9b31cdb5",
        "e_exp": "262800",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Robertson",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Rob",
        "ipaddr": "111.111.1.111",
        "name": "Rob Robertson",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "onprem_sid": "S-1-5-21-4837261184-168309720-1886587427-18514304",
        "puid": "18247BBD84827C6D",
        "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "rob@contoso.com",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "rob@contoso.com",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Administrative",
        "localizedValue": "Administrative"
    },
    "eventTimestamp": "2018-01-29T20:42:31.3810679Z",
    "id": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG/events/d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d/ticks/636528553513810679",
    "level": "Informational",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Network/networkSecurityGroups/write",
        "localizedValue": "Microsoft.Network/networkSecurityGroups/write"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Network",
        "localizedValue": "Microsoft.Network"
    },
    "resourceType": {
        "value": "Microsoft.Network/networkSecurityGroups",
        "localizedValue": "Microsoft.Network/networkSecurityGroups"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourcegroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNSG",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-01-29T20:42:50.0724829Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "statusCode": "Created",
        "serviceRequestId": "a4c11dbd-697e-47c5-9663-12362307157d",
        "responseBody": "",
        "requestbody": ""
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| authorization |Blob de propiedades RBAC del evento. Normalmente incluye las propiedades "action", "role" y "scope". |
| caller |Dirección de correo electrónico del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. |
| canales nueva |Uno de los valores siguientes: “Admin”, “Operation” |
| claims |Token de JWT que se usa en Active Directory para autenticar el usuario o la aplicación para llevar a cabo esta operación en Resource Manager. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos que comparten correlationId pertenecen a la misma acción general. |
| description |Descripción de texto estático de un evento. |
| eventDataId |Identificador único de un evento. |
| eventName | Nombre descriptivo del evento administrativo. |
| category | Siempre "Administrativo" |
| httpRequest |Blob que describe la solicitud HTTP. Normalmente incluye "clientRequestId", "clientIpAddress" y "method" (método HTTP). Por ejemplo, PUT). |
| level |Nivel del evento. Uno de los valores siguientes: “Critical”, “Error”, “Warning” e “Informational” |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado. |
| resourceProviderName |Nombre del proveedor de recursos del recurso afectado. |
| resourceType | Tipo de recurso que se vio afectado por un evento administrativo. |
| resourceId |Identificador de recurso del recurso afectado. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| status |Cadena que describe el estado de la operación. Entre los valores habituales, se incluyen: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus |Normalmente el código de estado HTTP de la llamada REST correspondiente, pero también puede incluir otras cadenas que describen un subestado, como estos valores habituales: Correcto (código de estado HTTP: 200), Creado (código de estado HTTP: 201), Aceptado (código de estado HTTP: 202), Sin contenido (código de estado HTTP: 204), Solicitud incorrecta (código de estado HTTP: 400), No encontrado (código de estado HTTP): 404), Conflicto (código de estado HTTP: 409), Error interno del servidor (código de estado HTTP: 500), Servicio no disponible (código de estado HTTP: Tiempo de espera agotado para la puerta de enlace (código de estado HTTP: 503) 504). |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de suscripción de Azure |

## <a name="service-health"></a>Estado del servicio
Esta categoría contiene el registro de los incidentes de estado del servicio que se han producido en Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "SQL Azure en el este de EE. UU. está experimentando un tiempo de inactividad". Los eventos de estado del servicio pueden encuadrarse dentro de cinco variedades: Acción requerida, Recuperación asistida, Incidente, Mantenimiento, Información o Seguridad, y solo aparecen si tiene un recurso en la suscripción que se podría ver afectado por el evento.

### <a name="sample-event"></a>Evento de ejemplo
```json
{
  "channels": "Admin",
  "correlationId": "c550176b-8f52-4380-bdc5-36c1b59d3a44",
  "description": "Active: Network Infrastructure - UK South",
  "eventDataId": "c5bc4514-6642-2be3-453e-c6a67841b073",
  "eventName": {
      "value": null
  },
  "category": {
      "value": "ServiceHealth",
      "localizedValue": "Service Health"
  },
  "eventTimestamp": "2017-07-20T23:30:14.8022297Z",
  "id": "/subscriptions/<subscription ID>/events/c5bc4514-6642-2be3-453e-c6a67841b073/ticks/636361902148022297",
  "level": "Warning",
  "operationName": {
      "value": "Microsoft.ServiceHealth/incident/action",
      "localizedValue": "Microsoft.ServiceHealth/incident/action"
  },
  "resourceProviderName": {
      "value": null
  },
  "resourceType": {
      "value": null,
      "localizedValue": ""
  },
  "resourceId": "/subscriptions/<subscription ID>",
  "status": {
      "value": "Active",
      "localizedValue": "Active"
  },
  "subStatus": {
      "value": null
  },
  "submissionTimestamp": "2017-07-20T23:30:34.7431946Z",
  "subscriptionId": "<subscription ID>",
  "properties": {
    "title": "Network Infrastructure - UK South",
    "service": "Service Fabric",
    "region": "UK South",
    "communication": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "incidentType": "Incident",
    "trackingId": "NA0F-BJG",
    "impactStartTime": "2017-07-20T21:41:00.0000000Z",
    "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"UK South\"}],\"ServiceName\":\"Service Fabric\"}]",
    "defaultLanguageTitle": "Network Infrastructure - UK South",
    "defaultLanguageContent": "Starting at approximately 21:41 UTC on 20 Jul 2017, a subset of customers in UK South may experience degraded performance, connectivity drops or timeouts when accessing their Azure resources hosted in this region. Engineers are investigating underlying Network Infrastructure issues in this region. Impacted services may include, but are not limited to App Services, Automation, Service Bus, Log Analytics, Key Vault, SQL Database, Service Fabric, Event Hubs, Stream Analytics, Azure Data Movement, API Management, and Azure Search. Multiple engineering teams are engaged in multiple workflows to mitigate the impact. The next update will be provided in 60 minutes, or as events warrant.",
    "stage": "Active",
    "communicationId": "636361902146035247",
    "version": "0.1.1"
  }
}
```
Consulte el artículo sobre las [notificaciones de estado de servicio](./../../azure-monitor/platform/service-notifications.md) para la documentación sobre los valores de las propiedades.

## <a name="resource-health"></a>Estado de los recursos
Esta categoría contiene el registro de los eventos de estado del servicio que se han producido en los recursos de Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "El estado de mantenimiento de la máquina virtual se cambió a No disponible". Los eventos de mantenimiento de recursos pueden representar uno de los cuatro estados de mantenimiento siguientes: Disponible, No disponible, Degradado y Desconocido. Además, los eventos de mantenimiento de recursos se pueden clasificar como iniciados por la plataforma o por el usuario.

### <a name="sample-event"></a>Evento de ejemplo

```json
{
    "channels": "Admin, Operation",
    "correlationId": "28f1bfae-56d3-7urb-bff4-194d261248e9",
    "description": "",
    "eventDataId": "a80024e1-883d-37ur-8b01-7591a1befccb",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "ResourceHealth",
        "localizedValue": "Resource Health"
    },
    "eventTimestamp": "2018-09-04T15:33:43.65Z",
    "id": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>/events/a80024e1-883d-42a5-8b01-7591a1befccb/ticks/636716720236500000",
    "level": "Critical",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Resourcehealth/healthevent/Activated/action",
        "localizedValue": "Health Event Activated"
    },
    "resourceGroupName": "<resource group>",
    "resourceProviderName": {
        "value": "Microsoft.Resourcehealth/healthevent/action",
        "localizedValue": "Microsoft.Resourcehealth/healthevent/action"
    },
    "resourceType": {
        "value": "Microsoft.Compute/virtualMachines",
        "localizedValue": "Microsoft.Compute/virtualMachines"
    },
    "resourceId": "/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-09-04T15:36:24.2240867Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "stage": "Active",
        "title": "Virtual Machine health status changed to unavailable",
        "details": "Virtual machine has experienced an unexpected event",
        "healthStatus": "Unavailable",
        "healthEventType": "Downtime",
        "healthEventCause": "PlatformInitiated",
        "healthEventCategory": "Unplanned"
    },
    "relatedEvents": []
}
```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| canales nueva | Siempre es "Admin, Operation". |
| correlationId | GUID en formato de cadena. |
| description |Descripción de texto estático del evento de alerta. |
| eventDataId |Identificador único del evento de alerta. |
| category | Siempre "Resource Health". |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| level |Nivel del evento. Uno de los valores siguientes: “Critical”, “Error”, “Warning”, “Informational” y “Verbose” |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| resourceGroupName |Nombre del grupo de recursos que contiene el recurso. |
| resourceProviderName |Siempre "Microsoft.Resourcehealth/healthevent/action". |
| resourceType | Tipo de recurso que se vio afectado por un evento de Resource Health. |
| resourceId | Id. del recurso afectado. |
| status |Cadena que describe el estado del evento de estado. Los valores pueden ser: Active, Resolved, InProgress, Updated. |
| subStatus | Normalmente es null para las alertas. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de suscripción de Azure |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento.|
| properties.title | Cadena fácil de usar que describe el estado del recurso. |
| properties.details | Cadena fácil de usar que aporta detalles adicionales sobre el evento. |
| properties.currentHealthStatus | Estado de mantenimiento actual del recurso. Uno de los valores siguientes: "Available", "Unavailable", "Degraded", and "Unknown". |
| properties.previousHealthStatus | Estado de mantenimiento anterior del recurso. Uno de los valores siguientes: "Available", "Unavailable", "Degraded", and "Unknown". |
| properties.type | Descripción del tipo de evento de estado del recurso. |
| properties.cause | Descripción de la causa del evento de estado del recurso. Ya sea "UserInitiated" o "PlatformInitiated". |


## <a name="alert"></a>Alerta
Esta categoría contiene el registro de todas las activaciones de alertas de Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "El % de CPU en myVM ha estado por encima de 80 durante los últimos 5 minutos". Varios sistemas de Azure tienen un concepto de alerta: puede definir una regla de algún tipo y recibir una notificación cuando las condiciones coincidan con esa regla. Cada vez que un tipo de alerta de Azure compatible "se activa" o se cumplen las condiciones para generar una notificación, también se inserta un registro de la activación en esta categoría del Registro de actividad.

### <a name="sample-event"></a>Evento de ejemplo

```json
{
  "caller": "Microsoft.Insights/alertRules",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/alertRules"
  },
  "correlationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "description": "'Disk read LessThan 100000 ([Count]) in the last 5 minutes' has been resolved for CloudService: myResourceGroup/Production/Event.BackgroundJobsWorker.razzle (myResourceGroup)",
  "eventDataId": "149d4baf-53dc-4cf4-9e29-17de37405cd9",
  "eventName": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "category": {
    "value": "Alert",
    "localizedValue": "Alert"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle/events/149d4baf-53dc-4cf4-9e29-17de37405cd9/ticks/636362258535221920",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "Microsoft.ClassicCompute",
    "localizedValue": "Microsoft.ClassicCompute"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/Event.BackgroundJobsWorker.razzle",
  "resourceType": {
    "value": "Microsoft.ClassicCompute/domainNames/slots/roles",
    "localizedValue": "Microsoft.ClassicCompute/domainNames/slots/roles"
  },
  "operationId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert/incidents/L3N1YnNjcmlwdGlvbnMvZGY2MDJjOWMtN2FhMC00MDdkLWE2ZmItZWIyMGM4YmQxMTkyL3Jlc291cmNlR3JvdXBzL0NzbUV2ZW50RE9HRk9PRC1XZXN0VVMvcHJvdmlkZXJzL21pY3Jvc29mdC5pbnNpZ2h0cy9hbGVydHJ1bGVzL215YWxlcnQwNjM2MzYyMjU4NTM1MjIxOTIw",
  "operationName": {
    "value": "Microsoft.Insights/AlertRules/Resolved/Action",
    "localizedValue": "Microsoft.Insights/AlertRules/Resolved/Action"
  },
  "properties": {
    "RuleUri": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/alertrules/myalert",
    "RuleName": "myalert",
    "RuleDescription": "",
    "Threshold": "100000",
    "WindowSizeInMinutes": "5",
    "Aggregation": "Average",
    "Operator": "LessThan",
    "MetricName": "Disk read",
    "MetricUnit": "Count"
  },
  "status": {
    "value": "Resolved",
    "localizedValue": "Resolved"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T09:24:13.522192Z",
  "submissionTimestamp": "2017-07-21T09:24:15.6578651Z",
  "subscriptionId": "<subscription ID>"
}
```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| caller | Siempre es Microsoft.Insights/alertRules. |
| canales nueva | Siempre es "Admin, Operation". |
| claims | Blob JSON con el SPN (nombre de entidad de seguridad de servicio), o tipo de recurso, del motor de alertas. |
| correlationId | GUID en formato de cadena. |
| description |Descripción de texto estático del evento de alerta. |
| eventDataId |Identificador único del evento de alerta. |
| category | Siempre "Alerta" |
| level |Nivel del evento. Uno de los valores siguientes: “Critical”, “Error”, “Warning” e “Informational” |
| resourceGroupName |Nombre del grupo de recursos del recurso afectado si se trata de una alerta de métrica. En otros tipos de alertas, es el nombre del grupo de recursos que contiene la propia alerta. |
| resourceProviderName |Nombre del proveedor de recursos del recurso afectado si se trata de una alerta de métrica. En otros tipos de alertas, es el nombre del proveedor de recursos de la propia alerta. |
| resourceId | Nombre del identificador del recurso afectado si se trata de una alerta de métrica. En otros tipos de alertas, es el identificador del propio recurso de alerta. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| status |Cadena que describe el estado de la operación. Entre los valores habituales, se incluyen: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Normalmente es null para las alertas. |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de suscripción de Azure |

### <a name="properties-field-per-alert-type"></a>Campo Propiedades por tipo de alerta
El campo Propiedades contendrá valores diferentes dependiendo del origen del evento de alerta. Dos proveedores de eventos de alerta comunes son las alertas de registro de actividad y las alertas de métrica.

#### <a name="properties-for-activity-log-alerts"></a>Propiedades de las alertas del registro de actividad
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| properties.subscriptionId | Identificador de suscripción del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.eventDataId | Identificador de datos del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.resourceGroup | Grupo de recursos del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.resourceId | Identificador de recurso del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.eventTimestamp | Marca de tiempo del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.operationName | Nombre de la operación del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad. |
| properties.status | Estado del evento del registro de actividad que hizo que se activara esta regla de alertas del registro de actividad.|

#### <a name="properties-for-metric-alerts"></a>Propiedades de las alertas de métrica
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| properties.RuleUri | Identificador de recurso de la propia regla de alerta de métrica. |
| properties.RuleName | Nombre de la regla de alertas de métrica. |
| properties.RuleDescription | Descripción de la regla de alertas de métrica (tal y como se define en la regla de alertas). |
| properties.Threshold | Valor de umbral que se usa en la evaluación de la regla de alertas de métrica. |
| properties.WindowSizeInMinutes | Tamaño de la ventana que se usa en la evaluación de la regla de alertas de métrica. |
| properties.Aggregation | Tipo de agregación definido en la regla de alertas de métrica. |
| properties.Operator | Operador condicional usado en la evaluación de la regla de alertas de métrica. |
| properties.MetricName | Nombre de la métrica usada en la evaluación de la regla de alertas de métrica. |
| properties.MetricUnit | Unidad de la métrica usada en la evaluación de la regla de alertas de métrica. |

## <a name="autoscale"></a>Escalado automático
Esta categoría contiene el registro de los eventos relacionados con el funcionamiento del motor de escalado automático en función de cualquier configuración de escalado automático que haya definido en la suscripción. Un ejemplo del tipo de evento que aparece en esta categoría es "Error de acción de escalado automático". Con el escalado automático puede escalar horizontalmente o reducir horizontalmente de forma automática el número de instancias de un tipo de recurso compatible en función de la hora del día o cargar datos (métricas) mediante una configuración de escalado automático. Cuando se cumplen las condiciones para escalar o reducir verticalmente, se registran los eventos de inicio y corrección o error en esta categoría.

### <a name="sample-event"></a>Evento de ejemplo
```json
{
  "caller": "Microsoft.Insights/autoscaleSettings",
  "channels": "Admin, Operation",
  "claims": {
    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn": "Microsoft.Insights/autoscaleSettings"
  },
  "correlationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
  "eventDataId": "a5b92075-1de9-42f1-b52e-6f3e4945a7c7",
  "eventName": {
    "value": "AutoscaleAction",
    "localizedValue": "AutoscaleAction"
  },
  "category": {
    "value": "Autoscale",
    "localizedValue": "Autoscale"
  },
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup/events/a5b92075-1de9-42f1-b52e-6f3e4945a7c7/ticks/636361956518681572",
  "level": "Informational",
  "resourceGroupName": "myResourceGroup",
  "resourceProviderName": {
    "value": "microsoft.insights",
    "localizedValue": "microsoft.insights"
  },
  "resourceId": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/microsoft.insights/autoscalesettings/myResourceGroup-Production-myResource-myResourceGroup",
  "resourceType": {
    "value": "microsoft.insights/autoscalesettings",
    "localizedValue": "microsoft.insights/autoscalesettings"
  },
  "operationId": "fc6a7ff5-ff68-4bb7-81b4-3629212d03d0",
  "operationName": {
    "value": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action",
    "localizedValue": "Microsoft.Insights/AutoscaleSettings/Scaledown/Action"
  },
  "properties": {
    "Description": "The autoscale engine attempting to scale resource '/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource' from 3 instances count to 2 instances count.",
    "ResourceName": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ClassicCompute/domainNames/myResourceGroup/slots/Production/roles/myResource",
    "OldInstancesCount": "3",
    "NewInstancesCount": "2",
    "LastScaleActionTime": "Fri, 21 Jul 2017 01:00:51 GMT"
  },
  "status": {
    "value": "Succeeded",
    "localizedValue": "Succeeded"
  },
  "subStatus": {
    "value": null
  },
  "eventTimestamp": "2017-07-21T01:00:51.8681572Z",
  "submissionTimestamp": "2017-07-21T01:00:52.3008754Z",
  "subscriptionId": "<subscription ID>"
}

```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| caller | Siempre es Microsoft.Insights/autoscaleSettings. |
| canales nueva | Siempre es "Admin, Operation". |
| claims | Blob JSON con el SPN (nombre de entidad de seguridad de servicio), o tipo de recurso, del motor de escalado automático. |
| correlationId | GUID en formato de cadena. |
| description |Descripción de texto estático del evento de escalado automático. |
| eventDataId |Identificador único del evento de escalado automático. |
| level |Nivel del evento. Uno de los valores siguientes: “Critical”, “Error”, “Warning” e “Informational” |
| resourceGroupName |Nombre del grupo de recursos para la configuración del escalado automático. |
| resourceProviderName |Nombre del proveedor de recursos para la configuración del escalado automático. |
| resourceId |Identificador de recurso de la configuración del escalado automático. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. |
| properties.Description | Descripción detallada de lo que hacía el motor de escalado automático. |
| properties.ResourceName | Identificador del recurso afectado (recurso en el que se estaba llevando a cabo la acción de escalado) |
| properties.OldInstancesCount | Número de instancias antes de que tuviera efecto la acción de escalado automático. |
| properties.NewInstancesCount | Número de instancias después de que tuviera efecto la acción de escalado automático. |
| properties.LastScaleActionTime | Marca de tiempo de cuándo se produjo la acción de escalado automático. |
| status |Cadena que describe el estado de la operación. Entre los valores habituales, se incluyen: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Normalmente es null para el escalado automático. |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de suscripción de Azure |

## <a name="security"></a>Seguridad
Esta categoría contiene el registro de todas las alertas generado por Azure Security Center. Un ejemplo del tipo de evento que vería en esta categoría es "Se ejecutó un archivo de extensión doble sospechoso".

### <a name="sample-event"></a>Evento de ejemplo
```json
{
    "channels": "Operation",
    "correlationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "description": "Suspicious double extension file executed. Machine logs indicate an execution of a process with a suspicious double extension.\r\nThis extension may trick users into thinking files are safe to be opened and might indicate the presence of malware on the system.",
    "eventDataId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "eventName": {
        "value": "Suspicious double extension file executed",
        "localizedValue": "Suspicious double extension file executed"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2017-10-18T06:02:18.6179339Z",
    "id": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/events/965d6c6a-a790-4a7e-8e9a-41771b3fbc38/ticks/636439033386179339",
    "level": "Informational",
    "operationId": "965d6c6a-a790-4a7e-8e9a-41771b3fbc38",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Microsoft.Security/locations/alerts/activate/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/<subscription ID>/providers/Microsoft.Security/locations/centralus/alerts/2518939942613820660_a48f8653-3fc6-4166-9f19-914f030a13d3",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": null
    },
    "submissionTimestamp": "2017-10-18T06:02:52.2176969Z",
    "subscriptionId": "<subscription ID>",
    "properties": {
        "accountLogonId": "0x2r4",
        "commandLine": "c:\\mydirectory\\doubleetension.pdf.exe",
        "domainName": "hpc",
        "parentProcess": "unknown",
        "parentProcess id": "0",
        "processId": "6988",
        "processName": "c:\\mydirectory\\doubleetension.pdf.exe",
        "userName": "myUser",
        "UserSID": "S-3-2-12",
        "ActionTaken": "Detected",
        "Severity": "High"
    },
    "relatedEvents": []
}

```

### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| canales nueva | Siempre "Operation" |
| correlationId | GUID en formato de cadena. |
| description |Descripción de texto estático del evento de seguridad. |
| eventDataId |Identificador único del evento de seguridad. |
| eventName |Nombre descriptivo del evento de seguridad. |
| category | Siempre "Seguridad" |
| id |Identificador único del recurso del evento de seguridad. |
| level |Nivel del evento. Uno de los valores siguientes: “Critical”, “Error”, “Warning” o “Informational” |
| resourceGroupName |Nombre del grupo de recursos del recurso. |
| resourceProviderName |Nombre del proveedor de recursos de Azure Security Center. Siempre "Microsoft.Security". |
| resourceType |Tipo de recurso que generó el evento de seguridad, por ejemplo, "Microsoft.Security/locations/alerts" |
| resourceId |Identificador de recurso de la alerta de seguridad. |
| operationId |GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName |Nombre de la operación. |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describen los detalles del evento. Estas propiedades variarán según el tipo de alerta de seguridad. Vea [esta página](../../security-center/security-center-alerts-overview.md) para obtener una descripción de los tipos de alertas que proceden de Security Center. |
| properties.Severity |Nivel de gravedad. Los valores posibles son "High," "Medium" o "Low". |
| status |Cadena que describe el estado de la operación. Entre los valores habituales, se incluyen: Started, In Progress, Succeeded, Failed, Active, Resolved. |
| subStatus | Normalmente es null para los eventos de seguridad. |
| eventTimestamp |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de suscripción de Azure |

## <a name="recommendation"></a>Recomendación
Esta categoría contiene el registro de cualquier recomendación nueva que se genere para los servicios. Un ejemplo de recomendación sería "Use conjuntos de disponibilidad para obtener una tolerancia a errores mejorada". Hay cuatro tipos de eventos de recomendación que se pueden generar: Alta disponibilidad, Rendimiento, Seguridad y Optimización de costos. 

### <a name="sample-event"></a>Evento de ejemplo
```json
{
    "channels": "Operation",
    "correlationId": "92481dfd-c5bf-4752-b0d6-0ecddaa64776",
    "description": "The action was successful.",
    "eventDataId": "06cb0e44-111b-47c7-a4f2-aa3ee320c9c5",
    "eventName": {
        "value": "",
        "localizedValue": ""
    },
    "category": {
        "value": "Recommendation",
        "localizedValue": "Recommendation"
    },
    "eventTimestamp": "2018-06-07T21:30:42.976919Z",
    "id": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM/events/06cb0e44-111b-47c7-a4f2-aa3ee320c9c5/ticks/636640038429769190",
    "level": "Informational",
    "operationId": "",
    "operationName": {
        "value": "Microsoft.Advisor/generateRecommendations/action",
        "localizedValue": "Microsoft.Advisor/generateRecommendations/action"
    },
    "resourceGroupName": "MYRESOURCEGROUP",
    "resourceProviderName": {
        "value": "MICROSOFT.COMPUTE",
        "localizedValue": "MICROSOFT.COMPUTE"
    },
    "resourceType": {
        "value": "MICROSOFT.COMPUTE/virtualmachines",
        "localizedValue": "MICROSOFT.COMPUTE/virtualmachines"
    },
    "resourceId": "/SUBSCRIPTIONS/<Subscription ID>/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.COMPUTE/VIRTUALMACHINES/MYVM",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2018-06-07T21:30:42.976919Z",
    "subscriptionId": "<Subscription ID>",
    "properties": {
        "recommendationSchemaVersion": "1.0",
        "recommendationCategory": "Security",
        "recommendationImpact": "High",
        "recommendationRisk": "None"
    },
    "relatedEvents": []
}

```
### <a name="property-descriptions"></a>Descripciones de propiedades
| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| canales nueva | Siempre "Operation" |
| correlationId | GUID en formato de cadena. |
| description |Descripción de texto estático del evento de recomendación |
| eventDataId | Identificador único del evento de recomendación. |
| category | Siempre "Recomendación" |
| id |Identificador único del recurso del evento de recomendación. |
| level |Nivel del evento. Uno de los valores siguientes: “Critical”, “Error”, “Warning” o “Informational” |
| operationName |Nombre de la operación.  Siempre "Microsoft.Advisor/generateRecommendations/action"|
| resourceGroupName |Nombre del grupo de recursos del recurso. |
| resourceProviderName |Nombre del proveedor de recursos del recurso al que se aplica esta recomendación, como "MICROSOFT.COMPUTE" |
| resourceType |Nombre del tipo de recurso del recurso al que se aplica esta recomendación, como "MICROSOFT.COMPUTE/virtualmachines" |
| resourceId |Identificador de recurso del recurso al que se aplica la recomendación |
| status | Siempre "Activo" |
| submissionTimestamp |Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId |Identificador de suscripción de Azure |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describe los detalles de la recomendación.|
| properties.recommendationSchemaVersion| Versión de esquema de las propiedades de recomendación publicadas en la entrada Registro de actividad |
| properties.recommendationCategory | Categoría de la recomendación. Los valores posibles son "High Availability", "Performance", "Security" y "Cost" |
| properties.recommendationImpact| Impacto de la recomendación. Los valores posibles son "High", "Medium", "Low" |
| properties.recommendationRisk| Riesgo de la recomendación. Los valores posibles son "Error", "Warning", "None" |

## <a name="policy"></a>Directiva

Esta categoría contiene registros de todas las operaciones de acción de efecto realizadas por [Azure Policy](../../governance/policy/overview.md). Algunos ejemplos de los tipos de eventos que aparecen en esta categoría son _Auditoría_ y _Denegación_. Cada acción llevada a cabo por Azure Policy se modela como una operación en un recurso.

### <a name="sample-policy-event"></a>Evento de Azure Policy de ejemplo

```json
{
    "authorization": {
        "action": "Microsoft.Resources/checkPolicyCompliance/read",
        "scope": "/subscriptions/<subscriptionID>"
    },
    "caller": "33a68b9d-63ce-484c-a97e-94aef4c89648",
    "channels": "Operation",
    "claims": {
        "aud": "https://management.azure.com/",
        "iss": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "iat": "1234567890",
        "nbf": "1234567890",
        "exp": "1234567890",
        "aio": "A3GgTJdwK4vy7Fa7l6DgJC2mI0GX44tML385OpU1Q+z+jaPnFMwB",
        "appid": "1d78a85d-813d-46f0-b496-dd72f50a3ec0",
        "appidacr": "2",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "https://sts.windows.net/1114444b-7467-4144-a616-e3a5d63e147b/",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "f409edeb-4d29-44b5-9763-ee9348ad91bb",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "b-24Jf94A3FH2sHWVIFqO3-RSJEiv24Jnif3gj7s",
        "http://schemas.microsoft.com/identity/claims/tenantid": "1114444b-7467-4144-a616-e3a5d63e147b",
        "uti": "IdP3SUJGtkGlt7dDQVRPAA",
        "ver": "1.0"
    },
    "correlationId": "b5768deb-836b-41cc-803e-3f4de2f9e40b",
    "description": "",
    "eventDataId": "d0d36f97-b29c-4cd9-9d3d-ea2b92af3e9d",
    "eventName": {
        "value": "EndRequest",
        "localizedValue": "End request"
    },
    "category": {
        "value": "Policy",
        "localizedValue": "Policy"
    },
    "eventTimestamp": "2019-01-15T13:19:56.1227642Z",
    "id": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy/events/13bbf75f-36d5-4e66-b693-725267ff21ce/ticks/636831551961227642",
    "level": "Warning",
    "operationId": "04e575f8-48d0-4c43-a8b3-78c4eb01d287",
    "operationName": {
        "value": "Microsoft.Authorization/policies/audit/action",
        "localizedValue": "Microsoft.Authorization/policies/audit/action"
    },
    "resourceGroupName": "myResourceGroup",
    "resourceProviderName": {
        "value": "Microsoft.Sql",
        "localizedValue": "Microsoft SQL"
    },
    "resourceType": {
        "value": "Microsoft.Resources/checkPolicyCompliance",
        "localizedValue": "Microsoft.Resources/checkPolicyCompliance"
    },
    "resourceId": "/subscriptions/<subscriptionID>/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/contososqlpolicy",
    "status": {
        "value": "Succeeded",
        "localizedValue": "Succeeded"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-01-15T13:20:17.1077672Z",
    "subscriptionId": "<subscriptionID>",
    "properties": {
        "isComplianceCheck": "True",
        "resourceLocation": "westus2",
        "ancestors": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "policies": "[{\"policyDefinitionId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.
            Authorization/policyDefinitions/5775cdd5-d3d3-47bf-bc55-bb8b61746506/\",\"policyDefiniti
            onName\":\"5775cdd5-d3d3-47bf-bc55-bb8b61746506\",\"policyDefinitionEffect\":\"Deny\",\"
            policyAssignmentId\":\"/subscriptions/<subscriptionID>/providers/Microsoft.Authorization
            /policyAssignments/991a69402a6c484cb0f9b673/\",\"policyAssignmentName\":\"991a69402a6c48
            4cb0f9b673\",\"policyAssignmentScope\":\"/subscriptions/<subscriptionID>\",\"policyAssig
            nmentParameters\":{}}]"
    },
    "relatedEvents": []
}
```

### <a name="policy-event-property-descriptions"></a>Descripciones de las propiedades del evento de Azure Policy

| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| authorization | Matriz de propiedades RBAC del evento. Para los nuevos recursos, se trata de la acción y el ámbito de la solicitud que activó la evaluación. Para los recursos existentes, la acción es "Microsoft.Resources/checkPolicyCompliance/read". |
| caller | Para los nuevos recursos, la identidad que ha iniciado una implementación. Para los recursos existentes, el GUID de Microsoft Azure Policy Insights RP. |
| canales nueva | Los eventos de Azure Policy utilizan solo el canal "Operation". |
| claims | Token de JWT que se usa en Active Directory para autenticar el usuario o la aplicación para llevar a cabo esta operación en Resource Manager. |
| correlationId | Normalmente, un GUID en formato de cadena. Los eventos que comparten correlationId pertenecen a la misma acción general. |
| description | Este campo está en blanco para los eventos de Azure Policy. |
| eventDataId | Identificador único de un evento. |
| eventName | "BeginRequest" o "EndRequest". "BeginRequest" se usa para las evaluaciones auditIfNotExists y deployIfNotExists diferidas y cuando un efecto deployIfNotExists inicia una implementación de plantilla. Todas las demás operaciones devuelven "EndRequest". |
| category | Declara que el evento de registro de actividad pertenece a "Azure Policy". |
| eventTimestamp | Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| id | Identificador único del evento en el recurso específico. |
| level | Nivel del evento. Auditoría usa "Warning" y la denegación usa "Error". Un error auditIfNotExists o deployIfNotExists puede generar un mensaje "Warning" o "Error" según la gravedad. Todos los demás eventos de Azure Policy utilizan "Informational". |
| operationId | GUID compartido entre los eventos correspondientes a una sola operación. |
| operationName | Nombre de la operación que se correlaciona directamente con el efecto de Azure Policy. |
| resourceGroupName | Nombre del grupo de recursos del recurso evaluado. |
| resourceProviderName | Nombre del proveedor de recursos del recurso evaluado. |
| resourceType | Para los nuevos recursos, es el tipo que se va a evaluar. Para los recursos existentes, devuelve "Microsoft.Resources/checkPolicyCompliance". |
| resourceId | Identificador de recurso del recurso evaluado. |
| status | Cadena que describe el estado del resultado de evaluación de Azure Policy. La mayoría de las evaluaciones de Azure Policy devuelven "Succeeded", pero un efecto de denegación devuelve "Failed". Los errores de auditIfNotExists o deployIfNotExists también devuelven "Failed". |
| subStatus | Este campo está en blanco para los eventos de Policy. |
| submissionTimestamp | Marca de tiempo de cuándo el evento empezó a estar disponible para las consultas. |
| subscriptionId | Identificador de suscripción de Azure |
| properties.isComplianceCheck | Devuelve "False" cuando se implementa un nuevo recurso o se actualizan las propiedades de Resource Manager de un recurso existente. Todos los demás [desencadenadores de evaluación](../../governance/policy/how-to/get-compliance-data.md#evaluation-triggers) dan "True" como resultado. |
| properties.resourceLocation | Región de Azure del recurso que se va a evaluar. |
| properties.ancestors | Lista separada por comas que contiene los grupos de administración primarios ordenados del primario directo al principal más lejano. |
| properties.policies | Incluye detalles sobre la definición, asignación, efecto y parámetros de directiva que ocasionaron esta evaluación de Azure Policy. |
| relatedEvents | Este campo está en blanco para los eventos de Azure Policy. |

## <a name="mapping-to-diagnostic-logs-schema"></a>Asignación a esquema de registros de diagnósticos

Al realizar la transmisión del registro de actividad de Azure a una cuenta de almacenamiento o espacio de nombres de Event Hubs, los datos siguen el [esquema de registros de diagnóstico de Azure](./diagnostic-logs-schema.md). Esta es la asignación de propiedades del esquema anterior para el esquema de registros de diagnóstico:

| Propiedad del esquema de registros de diagnósticos | Propiedad del esquema de API REST de registro de actividad | Notas |
| --- | --- | --- |
| time | eventTimestamp |  |
| resourceId | resourceId | subscriptionId, resourceType, resourceGroupName se deducen todos de resourceId. |
| operationName | operationName.value |  |
| category | Parte del nombre de la operación | Desglose del tipo de operación: "Write"/"Delete"/"Action" |
| resultType | status.value | |
| resultSignature | substatus.value | |
| resultDescription | description |  |
| durationMs | N/D | Siempre 0 |
| callerIpAddress | httpRequest.clientIpAddress |  |
| correlationId | correlationId |  |
| identity | notificaciones y propiedades de autorización |  |
| Nivel | Nivel |  |
| location | N/D | Ubicación de donde se procesó el evento. *Esto no es la ubicación del recurso, sino el lugar donde se procesó el evento. Esta propiedad se quitará en una futura actualización.* |
| properties (Propiedades) | properties.eventProperties |  |
| properties.eventCategory | category | Si properties.eventCategory no está presente, la categoría será "Administrative" |
| properties.eventName | eventName |  |
| properties.operationId | operationId |  |
| properties.eventProperties | properties |  |


## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre el registro de actividad](activity-logs-overview.md)
* [Exportar el registro de actividad a Azure Storage o Event Hubs](activity-log-export.md)

