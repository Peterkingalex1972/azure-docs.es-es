---
title: Exportación del registro de actividad de Azure
description: Exporte el registro de actividad de Azure al almacenamiento para archivar o a Azure Event Hubs para la exportación fuera de Azure.
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: d34040722ac8793fd4bbb02f2d3fa59247f8267c
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639628"
---
# <a name="export-azure-activity-log-to-storage-or-azure-event-hubs"></a>Exportación del registro de actividad de Azure al almacenamiento o a Azure Event Hubs
El [registro de actividad de Azure](activity-logs-overview.md) proporciona información de los eventos de nivel de suscripción que se han producido en la suscripción de Azure. Además de ver el registro de actividades en Azure Portal o copiarlo en un área de trabajo de Log Analytics donde se puede analizar con otros datos recopilados por Azure Monitor, puede crear un perfil de registro para archivar el registro de actividad en una cuenta de almacenamiento de Azure o transmitirlo a un centro de eventos.

## <a name="archive-activity-log"></a>Archivo del registro de actividad
El archivo del registro de actividad en una cuenta de almacenamiento es útil si desea conservar los datos de registro más de 90 días (con control total sobre la directiva de retención) para auditorías, análisis estáticos o copias de seguridad. Si solo necesita conservar los eventos durante 90 días o menos, no es necesario configurar el archivado en una cuenta de almacenamiento, ya que los eventos del registro de actividades se conservan en la plataforma de Azure durante 90 días.

## <a name="stream-activity-log-to-event-hub"></a>Transmisión del registro de actividad al centro de eventos
[Azure Event Hubs](/azure/event-hubs/) es una plataforma de streaming y servicio de ingesta de eventos capaz de recibir y procesar millones de eventos por segundo. Los datos enviados a un centro de eventos se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes y almacenamiento. A continuación se indican dos maneras en que puede usar la funcionalidad de streaming para el registro de actividad:
* **Transmisión a sistemas de registro y telemetría de terceros**: con el tiempo, el streaming de Azure Event Hubs se convertirá en el mecanismo para canalizar el registro de actividad a sistemas de información de seguridad y administración de eventos (SIEM) y soluciones de análisis de registros de terceros.
* **Creación de una plataforma personalizada de registro y telemetría**: si ya tiene una plataforma de telemetría integrada personalizada o está pensando en crear una, la gran escalabilidad en cuanto a suscripción y publicación de Event Hubs permite ingerir el registro de actividad de manera flexible. 

## <a name="prerequisites"></a>Requisitos previos

### <a name="storage-account"></a>Cuenta de almacenamiento
Si va a archivar el registro de actividad, deberá [crear una cuenta de almacenamiento](../../storage/common/storage-quickstart-create-account.md) si aún no tiene una. No debe utilizar una cuenta de almacenamiento existente que tenga otros datos sin supervisión almacenados en ella, para que pueda controlar mejor el acceso a los datos de supervisión. Sin embargo, si también va a archivar las métricas y los registros de diagnóstico en una cuenta de almacenamiento, puede que tenga sentido utilizar esa misma cuenta de almacenamiento para mantener todos los datos de supervisión en una ubicación central.

La cuenta de almacenamiento no tiene que estar en la misma suscripción que la que emite los registros, siempre que el usuario que configura la configuración tenga acceso RBAC adecuado a ambas suscripciones.
> [!NOTE]
>  Actualmente no puede archivar datos en una cuenta de almacenamiento que está detrás de una red virtual protegida.

### <a name="event-hubs"></a>Event Hubs
Si va a enviar el registro de actividad a un centro de eventos, deberá [crear un centro de eventos](../../event-hubs/event-hubs-create.md) si aún no tiene uno. Si anteriormente transmitió los eventos del registro de actividad a este espacio de nombres de Event Hubs, se reutilizará ese centro de eventos.

La directiva de acceso compartido define los permisos que tiene el mecanismo de transmisión. Para transmitir a Event Hubs, se necesitan permisos de administración, envío y escucha. Puede crear o modificar directivas de acceso compartido para el espacio de nombres de Event Hubs en Azure Portal, en la pestaña Configurar.

Para actualizar el perfil de registro del registro de actividad para incluir el streaming, debe tener el permiso ListKey sobre esa regla de autorización de Event Hubs. El espacio de nombres de Event Hubs no tiene que estar necesariamente en la misma suscripción que la suscripción que emite los registros, siempre que el usuario que configure el valor tenga el acceso RBAC adecuado a ambas suscripciones y estas se encuentren en el mismo inquilino de AAD.

Transmita el registro de actividad a un centro de eventos mediante la [creación de un perfil de registro](#create-a-log-profile).

## <a name="create-a-log-profile"></a>Creación de un perfil de registro
Defina cómo se exporta el registro de actividad de Azure mediante un **perfil de registro**. Cada suscripción de Azure solo puede tener un perfil de registro. Estas opciones se pueden configurar a través de la opción **Exportar** en la hoja de registro de actividad en el portal. También pueden configurarse mediante programación [con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), los cmdlets de PowerShell o con la CLI.

El perfil de registro define lo siguiente.

**Dónde se debe enviar el registro de actividad.** Actualmente, las opciones disponibles son la cuenta de almacenamiento o Event Hubs.

**Qué categorías de eventos deben enviarse.** El significado de *categoría* en los perfiles de registro y los eventos de registro de actividad es distinto. En el perfil de registro, *Categoría* representa el tipo de operación (escritura, eliminación, acción). En un evento de registro de actividad, la propiedad de *categoría** representa el origen o el tipo de un evento (por ejemplo, Administration, ServiceHealth, Alert, etc.).

**Qué regiones (ubicaciones) se deben exportar.** Debe incluir todas las ubicaciones, ya que muchos eventos del registro de actividad son eventos globales.

**Cuánto tiempo se debe conservar el registro de actividad en una cuenta de almacenamiento.** Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.

Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado, las directivas de retención no surten ningún efecto. Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy. El proceso de eliminación empieza a medianoche (UTC), pero tenga en cuenta que eliminar los registros de la cuenta de almacenamiento puede tardar hasta 24 horas.



> [!WARNING]
> El formato de los datos de registro de la cuenta de almacenamiento ha cambiado a JSON Lines el 1 de noviembre de 2018. [Consulte este artículo para obtener una descripción de la repercusión y del modo de actualizar las herramientas para administrar el nuevo formato.](diagnostic-logs-append-blobs.md)


> [!IMPORTANT]
> Es posible que reciba un error al crear un perfil de registro si el proveedor de recursos de Microsoft.Insights no está registrado. Para registrar este proveedor y tipos, consulte [Tipos y proveedores de recursos de Azure](../../azure-resource-manager/resource-manager-supported-services.md).

### <a name="create-log-profile-using-the-azure-portal"></a>Creación del perfil de registro mediante Azure Portal

Cree o edite un perfil de registro con la opción **Exportar a Centro de eventos**  de Azure Portal.

1. En el menú **Monitor** de Azure Portal, seleccione **Exportar a Centro de eventos** .

    ![Botón Exportar en el portal](media/activity-log-export/portal-export.png)

3. En la hoja que aparece, especifique lo siguiente:
   * Regiones con los eventos para exportar. Debe seleccionar todas las regiones para asegurarse de no perderse eventos clave, ya que el registro de actividad es un registro global (no regional) y, por lo tanto, la mayoría de los eventos no tienen una región asociada. 
   * Si desea escribir en la cuenta de almacenamiento:
       * La cuenta de almacenamiento en la que desea guardar los eventos.
       * El número de días que desea conservar estos eventos en el almacenamiento. Un valor de 0 días conserva los registros para siempre.
   * Si desea escribir en el centro de eventos:
       * El espacio de nombres de Service Bus en el que quiere que se cree un centro de eventos para transmitir estos eventos.

     ![Exportar en hoja de registro de actividad](./media/activity-logs-overview/activity-logs-portal-export-blade.png)


4. Haga clic en **Guardar** para guardar la configuración. La configuración se aplica inmediatamente a la suscripción.


### <a name="configure-log-profile-using-powershell"></a>Configuración del perfil de registro mediante PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Si ya existe un perfil de registro, primero debe quitarlo y luego crear uno nuevo.

1. Use `Get-AzLogProfile` para identificar si existe un perfil de registro.  Si existe un perfil de registro, busque la propiedad *name*.

1. Use `Remove-AzLogProfile` para quitar el perfil de registro mediante el valor de la propiedad *name*.

    ```powershell
    # For example, if the log profile name is 'default'
    Remove-AzLogProfile -Name "default"
    ```

3. Use `Add-AzLogProfile` para crear un nuevo perfil de registro:

    ```powershell
    Add-AzLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus -RetentionInDays 90 -Category Write,Delete,Action
    ```

    | Propiedad | Obligatorio | DESCRIPCIÓN |
    | --- | --- | --- |
    | Name |Sí |Nombre de su perfil de registro. |
    | StorageAccountId |Sin |Identificador de recurso de la cuenta de almacenamiento donde se debe guardar el registro de actividad. |
    | serviceBusRuleId |Sin |Identificador de regla de Service Bus para el espacio de nombres de Service Bus donde desea que se creen centros de eventos. Se trata de una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
    | Location |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. |
    | RetentionInDays |Sí |Número de días que deben retenerse los eventos en la cuenta de almacenamiento, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |
    | Categoría |Sin |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son _Write_, _Delete_ y _Action_. |

### <a name="example-script"></a>Script de ejemplo
A continuación se muestra un script de ejemplo de PowerShell para crear un perfil de registro que escribe el registro de actividad en una cuenta de almacenamiento y en un centro de eventos.

   ```powershell
   # Settings needed for the new log profile
   $logProfileName = "default"
   $locations = (Get-AzLocation).Location
   $locations += "global"
   $subscriptionId = "<your Azure subscription Id>"
   $resourceGroupName = "<resource group name your event hub belongs to>"
   $eventHubNamespace = "<event hub namespace>"

   # Build the service bus rule Id from the settings above
   $serviceBusRuleId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.EventHub/namespaces/$eventHubNamespace/authorizationrules/RootManageSharedAccessKey"

   # Build the storage account Id from the settings above
   $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

   Add-AzLogProfile -Name $logProfileName -Location $locations -ServiceBusRuleId $serviceBusRuleId
   ```


### <a name="configure-log-profile-using-azure-cli"></a>Configuración del perfil de registro mediante la CLI de Azure

Si ya existe un perfil de registro, primero debe quitar el perfil de registro existente y luego crear un nuevo perfil de registro.

1. Use `az monitor log-profiles list` para identificar si existe un perfil de registro.
2. Use `az monitor log-profiles delete --name "<log profile name>` para quitar el perfil de registro mediante el valor de la propiedad *name*.
3. Use `az monitor log-profiles create` para crear un nuevo perfil de registro:

   ```azurecli-interactive
   az monitor log-profiles create --name "default" --location null --locations "global" "eastus" "westus" --categories "Delete" "Write" "Action"  --enabled false --days 0 --service-bus-rule-id "/subscriptions/<YOUR SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.EventHub/namespaces/<EVENT HUB NAME SPACE>/authorizationrules/RootManageSharedAccessKey"
   ```

    | Propiedad | Obligatorio | DESCRIPCIÓN |
    | --- | --- | --- |
    | name |Sí |Nombre de su perfil de registro. |
    | storage-account-id |Sí |Identificador de recurso de la cuenta de almacenamiento donde se deben guardar los registros de actividades. |
    | locations |Sí |Lista separada por espacios de las regiones para las que desea recopilar eventos del registro de actividad. Puede ver una lista de todas las regiones para la suscripción con `az account list-locations --query [].name`. |
    | days |Sí |Número de días que deben retenerse los eventos, entre 1 y 365. Con el valor cero, se almacenarán los registros indefinidamente (de manera indefinida).  Si el valor es cero, el parámetro habilitado se debe establecer en true. |
    |enabled | Sí |True o False.  Se usa para habilitar o deshabilitar la directiva de retención.  Si el valor es True, el parámetro de días debe ser un valor mayor que 0.
    | categories |Sí |Lista separada por espacios de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |



## <a name="activity-log-schema"></a>Esquema de registro de actividad
Ya sea que se envíen al almacenamiento de Azure o al centro de eventos, los datos del registro de actividad se escribirán en JSON con el siguiente formato.

``` JSON
{
    "records": [
        {
            "time": "2015-01-21T22:14:26.9792776Z",
            "resourceId": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
            "operationName": "microsoft.support/supporttickets/write",
            "category": "Write",
            "resultType": "Success",
            "resultSignature": "Succeeded.Created",
            "durationMs": 2826,
            "callerIpAddress": "111.111.111.11",
            "correlationId": "c776f9f4-36e5-4e0e-809b-c9b3c3fb62a8",
            "identity": {
                "authorization": {
                    "scope": "/subscriptions/s1/resourceGroups/MSSupportGroup/providers/microsoft.support/supporttickets/115012112305841",
                    "action": "microsoft.support/supporttickets/write",
                    "evidence": {
                        "role": "Subscription Admin"
                    }
                },
                "claims": {
                    "aud": "https://management.core.windows.net/",
                    "iss": "https://sts.windows.net/72f988bf-86f1-41af-91ab-2d7cd011db47/",
                    "iat": "1421876371",
                    "nbf": "1421876371",
                    "exp": "1421880271",
                    "ver": "1.0",
                    "http://schemas.microsoft.com/identity/claims/tenantid": "1e8d8218-c5e7-4578-9acc-9abbd5d23315 ",
                    "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
                    "http://schemas.microsoft.com/identity/claims/objectidentifier": "2468adf0-8211-44e3-95xq-85137af64708",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "admin@contoso.com",
                    "puid": "20030000801A118C",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "9vckmEGF7zDKk1YzIY8k0t1_EAPaXoeHyPRn6f413zM",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "John",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "Smith",
                    "name": "John Smith",
                    "groups": "cacfe77c-e058-4712-83qw-f9b08849fd60,7f71d11d-4c41-4b23-99d2-d32ce7aa621c,31522864-0578-4ea0-9gdc-e66cc564d18c",
                    "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": " admin@contoso.com",
                    "appid": "c44b4083-3bq0-49c1-b47d-974e53cbdf3c",
                    "appidacr": "2",
                    "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
                    "http://schemas.microsoft.com/claims/authnclassreference": "1"
                }
            },
            "level": "Information",
            "location": "global",
            "properties": {
                "statusCode": "Created",
                "serviceRequestId": "50d5cddb-8ca0-47ad-9b80-6cde2207f97c"
            }
        }
    ]
}
```
Los elementos de este código JSON se describen en la tabla siguiente.

| Nombre del elemento | DESCRIPCIÓN |
| --- | --- |
| time |Marca de tiempo de cuándo el servicio de Azure generó el evento que procesó la solicitud correspondiente al evento. |
| ResourceId |Identificador de recurso del recurso afectado. |
| operationName |Nombre de la operación. |
| category |Categoría de la acción, p. ej. Write, Read, Action. |
| resultType |Tipo del resultado, p. ej. Success, Failure, Start |
| resultSignature |Depende del tipo de recurso. |
| durationMs |Duración de la operación en milisegundos |
| callerIpAddress |La dirección IP del usuario que ha realizado la operación, la notificación de UPN o la notificación de SPN basada en la disponibilidad. |
| correlationId |Normalmente, un GUID en formato de cadena. Los eventos que comparten correlationId pertenecen a la misma acción general. |
| identity |Blob JSON que describe la autorización y las notificaciones. |
| authorization |Blob de propiedades RBAC del evento. Normalmente incluye las propiedades "action", "role" y "scope". |
| level |Nivel del evento. Uno de los valores siguientes: _Critical_, _Error_, _Warning_, _Informational_ y _Verbose_ |
| location |Región en la que se ha producido la ubicación (o global). |
| properties |Conjunto de pares `<Key, Value>` (es decir, diccionario) que describe los detalles del evento. |

> [!NOTE]
> Las propiedades y el uso de estas propiedades pueden variar según el recurso.



## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre el registro de actividad](../../azure-resource-manager/resource-group-audit.md)
* [Recopilación del registro de actividad en registros de Azure Monitor](activity-log-collect.md)
