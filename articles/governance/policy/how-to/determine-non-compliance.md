---
title: Determinación de las causas de incumplimiento
description: Cuando un recurso no es compatible, hay muchos motivos posibles para ello. Descubra qué es lo que provoca que no sea compatible.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fb7f238bb5c04bb03ee500b1b953895cc88c0596
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298922"
---
# <a name="determine-causes-of-non-compliance"></a>Determinación de las causas de incumplimiento

Cuando se determina que un recurso de Azure no cumple con una regla de directiva, resulta útil entender con qué parte de la regla no es compatible el recurso. También resulta útil para comprender cuál es el cambio que modificó un recurso compatible anteriormente y lo transformó en no compatible. Hay dos maneras de obtener esta información:

> [!div class="checklist"]
> - [Detalles de cumplimiento](#compliance-details)
> - [Historial de cambios (versión preliminar)](#change-history-preview)

## <a name="compliance-details"></a>Detalles de cumplimiento

Cuando un recurso no es compatible, los detalles de cumplimiento para ese recurso estarán disponibles en la página **Cumplimiento de directiva**. El panel de detalles de cumplimiento incluye la siguiente información:

- Detalles de recursos como el nombre, el tipo, la ubicación y el identificador del recurso.
- Estado de compatibilidad y marca de tiempo de la última evaluación para la asignación de la directiva actual.
- Una lista de _razones_ por las que el recurso no es compatible.

> [!IMPORTANT]
> Puesto que los detalles de cumplimiento para un recurso _no compatible_ muestran el valor correcto de las propiedades en ese recurso, el usuario debe realizar la operación de **lectura** en el **tipo** de recurso. Por ejemplo, si el recurso _no compatible_ es **Microsoft.Compute/virtualMachines**, el usuario debe realizar la operación **Microsoft.Compute/virtualMachines/read**. Si el usuario no realiza la operación necesaria, se muestra un error de acceso.

Para ver los detalles de cumplimiento, siga estos pasos:

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, haga clic en **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.

1. En la página **Información general** o **Cumplimiento**, seleccione una directiva que sea con un **estado de compatibilidad** que sea _No compatible_.

1. En la pestaña **Compatibilidad de recursos** de **Cumplimiento de directiva**, haga clic con el botón derecho o seleccione los puntos suspensivos de un recurso en un **estado de compatibilidad** que sea _No compatible_. A continuación, seleccione **Ver detalles de cumplimiento**.

   ![Ver opción de detalles de cumplimiento](../media/determine-non-compliance/view-compliance-details.png)

1. La página **Detalles de cumplimiento** muestra información de la última evaluación del recurso en la asignación de directiva actual. En este ejemplo, el campo **Microsoft.Sql/servers/version** tiene que ser _12.0_ y se espera que la definición de política sea _14.0_. Si el recurso no es compatible por varias razones, estas se muestran en este panel.

   ![Panel de detalles de cumplimiento y razones de no compatibilidad](../media/determine-non-compliance/compliance-details-pane.png)

   Para una definición de directiva **auditIfNotExists** o **deployIfNotExists**, los detalles incluyen la propiedad **details.type** y cualquier propiedad opcional. Para obtener una lista, consulte [auditIfNotExists propiedades](../concepts/effects.md#auditifnotexists-properties) y [deployIfNotExists propiedades](../concepts/effects.md#deployifnotexists-properties). **Último recurso evaluado** es un recurso relacionado de la sección de **detalles** de la definición.

   Definición **deployIfNotExists** parcial de ejemplo:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![Panel de detalles de cumplimiento: *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> Para proteger los datos, cuando un valor de propiedad es _secreto_, el valor actual muestra asteriscos.

Estos detalles explican por qué un recurso no es compatible actualmente, pero no muestran cuándo se realizó el cambio en el recurso que provocó que se transformara en no compatible. Para obtener esa información, consulte [Historial de cambios (versión preliminar)](#change-history-preview) a continuación.

### <a name="compliance-reasons"></a>Razones de cumplimiento

La siguiente matriz asigna cada _motivo_ posible a la [condición](../concepts/definition-structure.md#conditions) responsable en la definición de la directiva:

|Motivo | Condición |
|-|-|
|El valor actual debe contener el valor de destino como una clave. |containsKey o **no** notContainsKey |
|El valor actual debe contener el valor de destino. |contains o **no** notContains |
|El valor actual debe ser igual al valor de destino. |equals o **no** notEquals |
|El valor actual debe ser inferior al valor de destino. |less o **no** greaterOrEquals |
|El valor actual debe ser superior o igual al valor de destino. |greaterOrEquals o **no** less |
|El valor actual debe ser superior al valor de destino. |greater o **no** lessOrEquals |
|El valor actual debe ser inferior o igual al valor de destino. |lessOrEquals o **no** greater |
|El valor actual debe existir. |exists |
|El valor actual debe estar en el valor de destino. |in o **no** notIn |
|El valor actual debe ser como el valor de destino. |like o **no** notLike |
|El valor actual debe coincidir con distinción de mayúsculas y minúsculas con el valor de destino. |match o **no** notMatch |
|El valor actual debe coincidir sin distinción de mayúsculas y minúsculas con el valor de destino. |matchInsensitively o **no** notMatchInsensitively |
|El valor actual no debe contener el valor de destino como clave. |notContainsKey o **no** containsKey|
|El valor actual no debe contener el valor de destino. |notContains o **no** contains |
|El valor actual no debe ser igual que el valor de destino. |notEquals o **no** equals |
|El valor actual no debe existir. |**no** exists  |
|El valor actual no debe estar en el valor de destino. |notIn o **no** in |
|El valor actual no debe ser como el valor de destino. |notLike o **no** like |
|El valor actual no debe coincidir con distinción de mayúsculas y minúsculas con el valor de destino. |notMatch o **no** match |
|El valor actual no debe coincidir sin distinción de mayúsculas y minúsculas con el valor de destino. |notMatchInsensitively o **no** matchInsensitively |
|Ninguno de los recursos relacionados coincide con los detalles de vigencia de la definición de directiva. |Un recurso del tipo definido en **then.details.type** y relacionado con el recurso definido en la porción **if** de la regla de directiva no existe. |

## <a name="compliance-details-for-guest-configuration"></a>Detalles de cumplimiento de la configuración de invitado

Para las políticas de _auditoría_ en la categoría _Configuración de invitado_, podría haber varias configuraciones evaluadas dentro de la VM y tendrá que ver los detalles por configuración. Por ejemplo, si está realizando una auditoría para obtener una lista de aplicaciones instaladas y el estado de asignación es _No compatible_, deberá saber qué aplicaciones específicas faltan.

También es posible que no disponga de acceso para iniciar sesión en la VM directamente, pero tiene que informar sobre el motivo por el que la VM _no es compatible_. Por ejemplo, podría auditar esas VM unidas al dominio correcto e incluir la pertenencia al dominio actual en los detalles del informe.

### <a name="azure-portal"></a>Portal de Azure

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, haga clic en **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.

1. En la página **Introducción** o **Cumplimiento** , seleccione una asignación de directiva para las iniciativas que contengan una definición de directiva de configuración de invitado que sea _no compatible_.

1. Seleccione una directiva de _auditoría_ en la iniciativa que _no sea compatible_.

   ![Ver detalles de la definición de auditoría](../media/determine-non-compliance/guestconfig-audit-compliance.png)

1. En la pestaña **Compatibilidad de recursos**, se proporciona la siguiente información:

   - **Nombre**: el nombre de las asignaciones de configuración de invitado.
   - **Recurso principal**: la máquina virtual en un estado _No compatible_ para la asignación de configuración de invitado seleccionada.
   - **Tipo de recurso**: el nombre completo de _guestConfigurationAssignments_.
   - **Última evaluación**: la última vez que el servicio de configuración de invitado notificó a Azure Policy sobre el estado de la máquina virtual de destino.

   ![Ver detalles de cumplimiento](../media/determine-non-compliance/guestconfig-assignment-view.png)

1. Seleccione el nombre de asignación de configuración de invitado en la columna **Nombre** para abrir la página **Compatibilidad de recursos**.

1. Seleccione el **Ver recurso** situado en la parte superior de la página para abrir la página **Asignación de invitado**.

La página **Asignación de invitado** muestra todos los detalles de cumplimiento disponibles. Cada fila de la vista representa una evaluación que se realizó dentro de la máquina virtual. En la columna **Motivo**, se muestra una frase en la que se describe el motivo por el que la Asignación de invitado _no es compatible_. Por ejemplo, si está realizando una auditoría en la que las VM deben estar unidas a un dominio, la columna **Motivo** mostraría el texto incluido en la pertenencia al dominio actual.

![Ver detalles de cumplimiento](../media/determine-non-compliance/guestconfig-compliance-details.png)

### <a name="azure-powershell"></a>Azure PowerShell

También puede ver los detalles de cumplimiento en Azure PowerShell. En primer lugar, asegúrese de tener el módulo de configuración de invitado instalado.

```azurepowershell-interactive
Install-Module Az.GuestConfiguration
```

Puede ver el estado actual de todas las asignaciones de invitado para una VM mediante el comando siguiente:

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname>
```

```output
PolicyDisplayName                                                         ComplianceReasons
-----------------                                                         -----------------
Audit that an application is installed inside Windows VMs                 {[InstalledApplication]bwhitelistedapp}
Audit that an application is not installed inside Windows VMs.            {[InstalledApplication]NotInstalledApplica...
```

Para ver solo la frase del _motivo_ que describe la causa por la que la VM _no es compatible_, vuelva solo a la propiedad secundaria del motivo.

```azurepowershell-interactive
Get-AzVMGuestPolicyReport -ResourceGroupName <resourcegroupname> -VMName <vmname> | % ComplianceReasons | % Reasons | % Reason
```

```output
The following applications are not installed: '<name>'.
```

También puede generar un historial de cumplimiento de las asignaciones de invitado en el ámbito de la máquina virtual. La salida de este comando incluye los detalles de cada informe para la VM.

> [!NOTE]
> La salida puede devolver un gran volumen de datos. Se recomienda almacenar la salida en una variable.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname>
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
[Preview]: Audit that an application is installed inside Windows VMs      NonCompliant                       02/10/2019 12:00:38 PM 02/10/2019 12:00:41 PM VM01  ../17fg0...
<truncated>
```

Para simplificar esta vista, use el parámetro **ShowChanged**. La salida de este comando solo incluye los informes que tuvieron un cambio en el estado de compatibilidad.

```azurepowershell-interactive
$guestHistory = Get-AzVMGuestPolicyStatusHistory -ResourceGroupName <resourcegroupname> -VMName <vmname> -ShowChanged
$guestHistory
```

```output
PolicyDisplayName                                                         ComplianceStatus ComplianceReasons StartTime              EndTime                VMName LatestRepor
                                                                                                                                                                  tId
-----------------                                                         ---------------- ----------------- ---------              -------                ------ -----------
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/10/2019 10:00:38 PM 02/10/2019 10:00:41 PM VM01  ../12ab0...
Audit that an application is installed inside Windows VMs.                Compliant                          02/09/2019 11:00:38 AM 02/09/2019 11:00:39 AM VM01  ../e3665...
Audit that an application is installed inside Windows VMs                 NonCompliant                       02/09/2019 09:00:20 AM 02/09/2019 09:00:23 AM VM01  ../15ze1...
```

## <a name="a-namechange-historychange-history-preview"></a><a name="change-history"/>Historial de cambios (versión preliminar)

Como parte de una nueva **versión preliminar pública**, los últimos 14 días del historial de cambios están disponibles para todos los recursos de Azure que admiten la [eliminación de modo completa](../../../azure-resource-manager/complete-mode-deletion.md). El historial de cambios proporciona información acerca de cuándo se detectó un cambio y una _diferencia visual_ para cada cambio. Se desencadena una detección de cambios cuando se agregan, eliminan o modifican las propiedades de Resource Manager.

1. Inicie el servicio Azure Policy en Azure Portal. Para ello, haga clic en **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.

1. En la página **Información general** o **Cumplimiento**, seleccione una directiva con cualquier **estado de compatibilidad**.

1. En la pestaña **Compatibilidad de recursos**, de la página **Cumplimiento de directiva**, seleccione un recurso.

1. Seleccione la pestaña **Historial de cambios (versión preliminar)** en la página **Compatibilidad de recursos**. Se muestra una lista de cambios detectados, si existe alguna.

   ![Pestaña Historial de cambios de directiva de Azure en la página Compatibilidad de recursos](../media/determine-non-compliance/change-history-tab.png)

1. Seleccione uno de los cambios detectados. Las _diferencias visuales_ para el recurso se presentan en la página **Historial de cambios**.

   ![Diferencias visuales del historial de cambios de directiva de Azure en la página Historial de cambios](../media/determine-non-compliance/change-history-visual-diff.png)

Las _diferencias visuales_ ayudan a identificar los cambios de un recurso. Los cambios detectados pueden no estar relacionados con el estado de compatibilidad actual del recurso.

[Azure Resource Graph](../../resource-graph/overview.md) proporciona los datos del historial de cambios. Para consultar esta información fuera de Azure Portal, consulte [Obtención de cambios de recursos](../../resource-graph/how-to/get-resource-changes.md).

## <a name="next-steps"></a>Pasos siguientes

- Puede consultar ejemplos en [Ejemplos de Azure Policy](../samples/index.md).
- Revise la [estructura de definición de Azure Policy](../concepts/definition-structure.md).
- Vea la [Descripción de los efectos de directivas](../concepts/effects.md).
- Obtenga información acerca de cómo se pueden [crear directivas mediante programación](programmatically-create.md).
- Obtenga información sobre cómo [obtener datos de cumplimiento](getting-compliance-data.md).
- Obtenga información sobre cómo [corregir recursos no compatibles](remediate-resources.md).
- En [Organización de los recursos con grupos de administración de Azure](../../management-groups/overview.md), obtendrá información sobre lo que es un grupo de administración.