---
title: Administración de áreas de trabajo de Log Analytics en Azure Monitor | Microsoft Docs
description: Puede administrar las áreas de trabajo de Log Analytics en Azure Monitor con diferentes tareas administrativas en usuarios, cuentas, áreas de trabajo y cuentas de Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: 59e5bbaf8deccdd8218e9c5590266070ed3b5ebb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624334"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Administración de los datos de registro y las áreas de trabajo en Azure Monitor

Azure Monitor almacena los datos de [registro](data-platform-logs.md) en un área de trabajo de Log Analytics, que es básicamente un contenedor que incluye información de configuración y datos. Para administrar el acceso a los datos de registro, realice varias tareas administrativas relacionadas con su área de trabajo.

En este artículo se explica cómo administrar el acceso a los registros y las áreas de trabajo que los contienen, lo que incluye:

* Cómo conceder acceso a los usuarios que necesitan acceder a los datos de registro de recursos concretos mediante el control de acceso basado en rol (RBAC) de Azure.

* Cómo conceder acceso al área de trabajo mediante los permisos de área de trabajo.

* Cómo conceder acceso a los usuarios que necesitan acceder a los datos de registro de una tabla específica en el área de trabajo mediante Azure RBAC.

## <a name="define-access-control-mode"></a>Definición del modo de control de acceso

Puede ver el modo de control de acceso configurado en un área de trabajo desde Azure Portal o con Azure PowerShell.  Puede cambiar esta configuración con uno de estos métodos compatibles:

* Portal de Azure

* Azure PowerShell

* Plantilla del Administrador de recursos de Azure

### <a name="configure-from-the-azure-portal"></a>Configuración desde Azure Portal

Puede ver el modo de control de acceso del área de trabajo actual en la página **Introducción** del área de trabajo en el menú **Área de trabajo de Log Analytics**.

![Visualización del modo de control de acceso del área de trabajo](media/manage-access/view-access-control-mode.png)

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).
1. En Azure Portal, seleccione Áreas de trabajo de Log Analytics > su área de trabajo.

Este valor se puede cambiar en la página **Propiedades** del área de trabajo. No podrá cambiar la configuración si no tiene permisos para configurar el área de trabajo.

![Cambio del modo de acceso del área de trabajo](media/manage-access/change-access-control-mode.png)

### <a name="configure-using-powershell"></a>Configuración mediante PowerShell

Use el comando siguiente para examinar el modo de control de acceso para todas las áreas de trabajo en la suscripción:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

La salida debe ser similar a la siguiente:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Un valor de `False` significa que el área de trabajo se configura con el modo de acceso contexto del área de trabajo.  Un valor de `True` significa que el área de trabajo está configurada con el modo de acceso contexto del área de trabajo.

> [!NOTE]
> Si un área de trabajo se devuelve sin un valor booleano y está en blanco, también coincide con los resultados de un valor `False`.
>

Use el siguiente script para establecer el modo de control de acceso de un área de trabajo específica al permiso de contexto del recurso:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Use el siguiente script para establecer el modo de control de acceso en todas las áreas de trabajo de la suscripción al permiso de contexto del recurso:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="configure-using-a-resource-manager-template"></a>Configuración con una plantilla de Resource Manager

Para configurar el modo de acceso en una plantilla de Azure Resource Manager, establezca la marca de característica **enableLogAccessUsingOnlyResourcePermissions** en el área de trabajo en uno de los siguientes valores.

* **false**: establece el área de trabajo en los permisos de contexto del área de trabajo. Se trata de la configuración predeterminada si no se establece la marca.
* **true**: establece el área de trabajo en los permisos de contexto del recurso.

## <a name="manage-accounts-and-users"></a>Administración de cuentas y usuarios

Los permisos que se aplican al área de trabajo de un usuario determinado se definen mediante su [modo de acceso](design-logs-deployment.md#access-mode) y el [ modo de control de acceso](design-logs-deployment.md#access-control-mode) del área de trabajo. Con **Contexto del área de trabajo**, puede ver todos los registros del área de trabajo en los que tiene permiso, ya que las consultas en este modo se limitan a todos los datos de todas las tablas del área de trabajo. Con **Contexto del área de trabajo**, ve los datos de registro en el área de trabajo de un recurso, grupo de recursos o suscripción concretos al realizar una búsqueda directamente desde el recurso de Azure Portal al que tiene acceso. Las consultas de este modo se limitan solo a los datos asociados a ese recurso.

### <a name="workspace-permissions"></a>Permisos de área de trabajo

Cada área de trabajo puede tener varias cuentas asociadas, y cada cuenta puede tener acceso a varias áreas de trabajo. El acceso se administra mediante el [acceso basado en rol de Azure](../../role-based-access-control/role-assignments-portal.md).

Las siguientes actividades también requieren permisos de Azure:

|. |Permisos de Azure necesarios |Notas |
|-------|-------------------------|------|
| Agregar y quitar soluciones de supervisión | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Es necesario conceder estos permisos en el nivel de suscripción o grupo de recursos. |
| Cambiar el plan de tarifa | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver los datos en los iconos de soluciones *Backup* y *Site Recovery* | Administrador o coadministrador | Accede a los recursos implementados mediante el modelo de implementación clásica |
| Creación de un área de trabajo en Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Ver las propiedades básicas del área de trabajo y especificar la hoja del área de trabajo en el portal | `Microsoft.OperationalInsights/workspaces/read` ||
| Consultar registros con cualquier interfaz | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Acceder a todos los tipos de registros mediante consultas | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Acceder a una tabla de registro específica | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Leer las claves del área de trabajo para permitir el envío de registros a este área de trabajo | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Administración del acceso mediante los permisos de Azure

Para conceder acceso al área de trabajo de Log Analytics mediante permisos de Azure, siga los pasos que se describen en [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../../role-based-access-control/role-assignments-portal.md).

Azure tiene dos roles de usuario integrados para áreas de trabajo de Log Analytics:

* Lector de Log Analytics
* Colaborador de Log Analytics

Los miembros del rol *Lector de Log Analytics* pueden:

* Ver y buscar todos los datos de supervisión
* Ver la configuración de supervisión, incluida la visualización de la configuración de Azure Diagnostics en todos los recursos de Azure.

El rol Lector de Log Analytics incluye las siguientes acciones de Azure:

| type    | Permiso | DESCRIPCIÓN |
| ------- | ---------- | ----------- |
| . | `*/read`   | Capacidad para ver todos los recursos de Azure y la configuración de los recursos. Incluye la visualización de: <br> Estado de la extensión de la máquina virtual <br> Configuración de Azure Diagnostics en los recursos <br> Todas las propiedades y configuraciones de todos los recursos <br> En el caso de las áreas de trabajo, permite permisos completos sin restricciones para leer la configuración del área de trabajo y realizar consultas en los datos. Consulte opciones más específicas anteriormente. |
| . | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | En desuso, no es necesario asignarlos a los usuarios. |
| . | `Microsoft.OperationalInsights/workspaces/search/action` | En desuso, no es necesario asignarlos a los usuarios. |
| . | `Microsoft.Support/*` | Capacidad de abrir casos de soporte técnico |
|No acción | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Evita la lectura de la clave del área de trabajo necesaria para usar la API de recopilación de datos e instalar agentes. Esto impide que los usuarios agreguen nuevos recursos al área de trabajo. |

Los miembros del rol *Colaborador de Log Analytics* pueden:

* Leer todos los datos de supervisión como puede el lector de Log Analytics
* Crear y configurar cuentas de Automation
* Agregar y quitar soluciones de administración

    > [!NOTE]
    > Para llevar a cabo correctamente estas dos últimas acciones, este permiso debe concederse en el nivel de suscripción o grupo de recursos.

* Leer las claves de las cuentas de almacenamiento
* Configurar la recopilación de registros de Azure Storage
* Editar la configuración de supervisión de los recursos de Azure:
  * Agregar la extensión de máquina virtual a las máquinas virtuales
  * Configurar Azure Diagnostics en todos los recursos de Azure

> [!NOTE]
> Puede usar la capacidad para agregar una extensión de máquina virtual a una máquina virtual para obtener el control total sobre una máquina virtual.

El rol Colaborador de Log Analytics incluye las siguientes acciones de Azure:

| Permiso | DESCRIPCIÓN |
| ---------- | ----------- |
| `*/read`     | Capacidad para ver todos los recursos y la configuración de los recursos. Incluye la visualización de: <br> Estado de la extensión de la máquina virtual <br> Configuración de Azure Diagnostics en los recursos <br> Todas las propiedades y configuraciones de todos los recursos <br> En el caso de las áreas de trabajo, permite conceder permisos completos sin restricciones para leer el valor del área de trabajo y realizar consultas en los datos. Consulte opciones más específicas anteriormente. |
| `Microsoft.Automation/automationAccounts/*` | Capacidad para crear y configurar cuentas de Azure Automation, incluido agregar y editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Agregar, actualizar y eliminar extensiones de máquina virtual, incluida la extensión de Microsoft Monitoring Agent y el agente de OMS para la extensión de Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Ver la clave de la cuenta de almacenamiento. Necesaria para configurar Log Analytics para leer los registros de cuentas de Azure Storage |
| `Microsoft.Insights/alertRules/*` | Agregar, actualizar y eliminar reglas de alertas |
| `Microsoft.Insights/diagnosticSettings/*` | Agregar, actualizar y eliminar la configuración de diagnósticos en los recursos de Azure |
| `Microsoft.OperationalInsights/*` | Agregar, actualizar y eliminar la configuración de áreas de trabajo de Log Analytics |
| `Microsoft.OperationsManagement/*` | Agregar y eliminar soluciones de administración |
| `Microsoft.Resources/deployments/*` | Crear y eliminar implementaciones. Necesario para agregar y eliminar soluciones, áreas de trabajo y cuentas de Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crear y eliminar implementaciones. Necesario para agregar y eliminar soluciones, áreas de trabajo y cuentas de Automation |

Para agregar y quitar usuarios de un rol de usuario, es necesario tener permisos `Microsoft.Authorization/*/Delete` y `Microsoft.Authorization/*/Write`.

Use estos roles para conceder a los usuarios acceso en distintos ámbitos:

* Suscripción: acceso a todas las áreas de trabajo de la suscripción
* Grupo de recursos: acceso a todas las áreas de trabajo del grupo de recursos
* Recurso: acceso solo al área de trabajo especificada

Debe realizar las asignaciones en el nivel de recurso (área de trabajo) para asegurarse de que el control de acceso es preciso.  Use [roles personalizados](../../role-based-access-control/custom-roles.md) para crear roles con los permisos específicos necesarios.

### <a name="resource-permissions"></a>Permisos de recurso

Cuando los usuarios consulten los registros desde un área de trabajo mediante el acceso de contexto del recurso, tendrán los siguientes permisos en el recurso:

| Permiso | DESCRIPCIÓN |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Ejemplos:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Capacidad para ver todos los datos de registro para el recurso.  |
| `Microsoft.Insights/diagnosticSettings/write` | Capacidad para configurar diagnósticos a fin de permitir la configuración de registros para este recurso. |

El permiso `/read`se suele conceder desde un rol que incluye los permisos _/read o\*_ _\*_ como los roles [Lector](../../role-based-access-control/built-in-roles.md#reader) y [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) integrados. Tenga en cuenta que los roles personalizados que contienen acciones específicas o roles integrados dedicados no incluyen este permiso.

Consulte [Definición del control de acceso por tabla](#table-level-rbac) a continuación, si desea crear un control de acceso diferente para las distintas tablas.

## <a name="table-level-rbac"></a>RBAC de nivel de tabla

**RBAC de nivel de tabla** le permite definir un control más pormenorizado de los datos de un área de trabajo de Log Analytics, además de los restantes permisos. Este control le permite definir tipos de datos específicos que son accesibles solo a un conjunto concreto de usuarios.

El control de acceso de la tabla se implementa con [roles personalizados de Azure](../../role-based-access-control/custom-roles.md) para conceder o denegar un acceso específico a las [tablas](../log-query/logs-structure.md) del área de trabajo. Estos roles se aplican a las áreas de trabajo con los [modos de control de acceso](design-logs-deployment.md#access-control-mode) contexto del área de trabajo o contexto del recurso, independientemente del [modo de acceso](design-logs-deployment.md#access-mode) del usuario.

Cree un [rol personalizado](../../role-based-access-control/custom-roles.md) con las acciones siguientes para definir el acceso al control de acceso de la tabla.

* Para conceder acceso a una tabla, inclúyala en la sección **Actions** de la definición de rol.
* Para denegar el acceso a una tabla, inclúyala en la sección **NotActions** de la definición de rol.
* Use * para especificar todas las tablas.

Por ejemplo, para crear un rol con acceso a las tablas _Heartbeat_ y _AzureActivity_, cree un rol personalizado con las siguientes acciones:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Para crear un rol con acceso solo a las tablas _SecurityBaseline_ y otras, cree un rol personalizado con las siguientes acciones:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Registros personalizados

 Los registros personalizados se crean a partir de orígenes de datos, como los registros personalizados y la API HTTP Data Collector. La manera más fácil de identificar el tipo de registro es mediante la comprobación de las tablas que aparecen en [Registros personalizados del esquema del registro](../log-query/get-started-portal.md#understand-the-schema).

 Actualmente no se puede conceder o denegar el acceso a los registros personalizados individuales, pero puede conceder o denegar el acceso a todos los registros personalizados. Para crear un rol con acceso a todos los registros personalizados, cree un rol personalizado con las siguientes acciones:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Consideraciones

* Si se concede a un usuario permiso de lectura global con los roles Lector o Colaborador estándar que incluyen la acción _\*/read_, invalidará el control de acceso por tabla y les otorgará acceso a todos los datos de registro.
* Si se concede a un usuario el permiso de acceso para cada tabla, pero ningún otro, los usuarios podrían acceder a los datos de registro de la API, pero no desde Azure Portal. Para proporcionar acceso desde Azure Portal, use Lector de Log Analytics como su rol base.
* Los administradores de la suscripción tendrán acceso a todos los tipos de datos, independientemente de cualquier otra configuración de permisos.
* Los propietarios del área de trabajo son tratados como cualquier otro usuario para controlar el acceso por tabla.
* Debe asignar roles a los grupos de seguridad en lugar de usuarios individuales para reducir el número de asignaciones. Esto también le ayudará a usar las herramientas de administración de grupo existentes para configurar y comprobar el acceso.

## <a name="next-steps"></a>Pasos siguientes

* Consulte la [información general sobre el agente de Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) para recopilar datos de los equipos de su centro de datos u otro entorno de nube.

* Consulte [Recopilación de datos de máquinas virtuales de Azure](../../azure-monitor/learn/quick-collect-azurevm.md) para configurar la recopilación de datos de máquinas virtuales de Azure.
