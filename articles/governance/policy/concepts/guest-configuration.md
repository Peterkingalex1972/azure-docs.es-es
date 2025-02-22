---
title: Cómo auditar el contenido de una máquina virtual
description: Obtenga información sobre cómo Azure Policy usa Guest Configuration para auditar la configuración dentro de una máquina virtual de Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/18/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 6f51d2907738f49ace559f1b127458eda71de287
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624097"
---
# <a name="understand-azure-policys-guest-configuration"></a>Información sobre Guest Configuration de Azure Policy

Además de auditar y [corregir](../how-to/remediate-resources.md) los recursos de Azure, Azure Policy puede auditar la configuración en una máquina virtual. La validación se realiza mediante el cliente y la extensión Guest Configuration. La extensión, a través del cliente, valida las distintas opciones de configuración, como la configuración del sistema operativo, la presencia o configuración de la aplicación, la configuración del entorno y mucho más.

En este momento, la configuración de Azure Policy solo realiza una auditoría de la configuración dentro de la máquina.
Todavía no es posible aplicar configuraciones.

[!INCLUDE [az-powershell-update](../../../../includes/updated-for-az.md)]

## <a name="extension-and-client"></a>Extensión y cliente

Para auditar la configuración dentro de una máquina virtual, se habilita una [extensión de máquina virtual](../../../virtual-machines/extensions/overview.md). La extensión descarga la asignación de directiva aplicable y la definición de configuración correspondiente.

### <a name="register-guest-configuration-resource-provider"></a>Registrar proveedor de recursos de Guest Configuration

Para poder usar Guest Configuration debe registrar el proveedor de recursos. Puede registrarse mediante el portal o a través de PowerShell. El proveedor de recursos se registra automáticamente si la asignación de una directiva de configuración de invitado se realiza a través del portal.

#### <a name="registration---portal"></a>Registro: Portal

Para registrar el proveedor de recursos para Guest Configuration a través de Azure Portal, siga estos pasos:

1. Inicie Azure Portal y haga clic en **Todos los servicios**. Busque y seleccione **Suscripciones**.

1. Busque y haga clic en la suscripción que desea habilitar para Guest Configuration.

1. En el menú izquierdo de la página **Suscripción**, haga clic en **Proveedores de recursos**.

1. Filtre o desplácese hasta que encuentre **Microsoft.GuestConfiguration**, después haga clic en **Registrar** en la misma fila.

#### <a name="registration---powershell"></a>Registro: PowerShell

Para registrar el proveedor de recursos para Guest Configuration a través de PowerShell, ejecute el siguiente comando:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Herramientas de validación

Dentro de la máquina virtual, el cliente de Guest Configuration usa herramientas locales para ejecutar la auditoría.

En la tabla siguiente se muestra una lista herramienta locales usada en cada sistemas operativos compatibles:

|Sistema operativo|Herramienta de validación|Notas|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| La extensión Guest Configuration instala Ruby y Python. |

### <a name="validation-frequency"></a>Frecuencia de validación

El cliente de Guest Configuration busca contenido nuevo cada 5 minutos. Una vez que se recibe una asignación de invitado, se comprueban los valores en un intervalo de 15 minutos. Los resultados se envían al proveedor de recursos de Guest Configuration tan pronto como finaliza la auditoría. Cuando se produce una directiva del tipo [desencadenador evaluación](../how-to/get-compliance-data.md#evaluation-triggers), el estado de la máquina se escribe en el proveedor de recursos de Guest Configuration. Esto hace que Azure Policy evalúe las propiedades de Azure Resource Manager. Una evaluación de Azure Policy a petición recupera el valor más reciente del proveedor de recursos de la configuración de invitado. Sin embargo, no desencadena una nueva auditoría de la configuración en la máquina virtual.

### <a name="supported-client-types"></a>Tipos de cliente admitidos

En la tabla siguiente se muestra una lista de sistemas operativos compatibles en imágenes de Azure:

|Publicador|NOMBRE|Versiones|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|Credativ|Debian|8, 9|
|Microsoft|Windows Server|2012 Datacenter, 2012 R2 Datacenter, 2016 Datacenter, 2019 Datacenter|
|Microsoft|Cliente Windows|Windows 10|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|Suse|SLES|12 SP3|

> [!IMPORTANT]
> La configuración de invitado puede auditar los nodos que ejecutan un sistema operativo compatible. Si desea auditar máquinas virtuales que utilizan una imagen personalizada, debe duplicar la definición **DeployIfNotExists** y modificar la sección **If** para incluir las propiedades de la imagen.

### <a name="unsupported-client-types"></a>Tipos de cliente no admitidos

No se admite ninguna versión de Windows Server Nano Server.

### <a name="guest-configuration-extension-network-requirements"></a>Requisitos de red de la extensión de configuración de invitado

Para comunicarse con el proveedor de recursos de la configuración de invitado en Azure, las máquinas virtuales requieren acceso de salida a los centros de datos Azure en el puerto **443**. Si utiliza una red virtual privada en Azure y no permite el tráfico de salida, las excepciones deben configurarse mediante las reglas del [grupo de seguridad de red](../../../virtual-network/manage-network-security-group.md#create-a-security-rule). En este momento, no existe una etiqueta de servicio para la configuración de invitado de Azure Policy.

Para ver las listas de direcciones IP, puede descargar los [intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Este archivo se actualiza semanalmente y tiene los intervalos implementados en ese momento y los próximos cambios en los intervalos de direcciones IP. Solo necesita permitir el acceso de salida a las direcciones IP de las regiones donde están implementadas las máquinas virtuales.

> [!NOTE]
> El archivo XML de direcciones IP de los centros de datos de Azure enumera los intervalos de direcciones IP que se usan en los centros de datos de Microsoft Azure. El archivo incluye el proceso, SQL y los intervalos de almacenamiento. Semanalmente, se publica un archivo actualizado. El archivo refleja los intervalos implementados actualmente y los próximos cambios en los intervalos IP. Los nuevos intervalos que aparecen en el archivo no se utilizan en los centros de datos durante al menos una semana. Descargar el archivo XML nuevo cada semana es una buena idea. A continuación, actualice el sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Azure ExpressRoute deberían observar que este archivo se usa para actualizar la publicidad del Protocolo de puerta de enlace de borde (BGP) del espacio de Azure la primera semana de cada mes.

## <a name="guest-configuration-definition-requirements"></a>Requisitos de definición de Guest Configuration

Cada auditoría ejecutada por la configuración de invitado requiere dos definiciones de directiva, una definición **DeployIfNotExists** y una definición **Audit**. La definición **DeployIfNotExists** se utiliza para preparar la máquina virtual con el agente de la configuración de invitado y otros componentes para admitir las [herramientas de validación](#validation-tools).

La definición de directiva **DeployIfNotExists** valida y corrige los siguientes elementos:

- Comprueba que se ha asignado una configuración a la máquina virtual para evaluar. Si actualmente no hay ninguna asignación, obtiene la asignación y prepara la máquina virtual:
  - Autenticando la máquina virtual con una [identidad administrada](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalando la versión más reciente de la extensión **Microsoft.GuestConfiguration**
  - Instalando las [herramientas de validación](#validation-tools) y dependencias, si es necesario

Si la asignación **DeployIfNotExists** no es conforme, se puede usar una [tarea de corrección](../how-to/remediate-resources.md#create-a-remediation-task).

Una vez que la asignación **DeployIfNotExists** es compatible, la asignación de la directiva **Audit** usa las herramientas de validación local para determinar si la asignación de configuración es compatible o no compatible.
La herramienta de validación proporciona los resultados al cliente de Guest Configuration. El cliente envía los resultados a la extensión de Guest, que hace que estén disponibles a través del proveedor de recursos de Guest Configuration.

Azure Policy usa la propiedad **complianceStatus** de los proveedores de recursos de Guest Configuration para notificar el cumplimiento en el nodo **Compliance**. Para más información, vea [Obtención de datos de cumplimiento](../how-to/getting-compliance-data.md).

> [!NOTE]
> La directiva **DeployIfNotExists** es necesaria para que la directiva **Audit** devuelva resultados.
> Sin **DeployIfNotExists**, la directiva **Audit** muestra "0 de 0" recursos como estado.

Se incluyen todas las directivas integradas para Guest Configuration en una iniciativa para agrupar las definiciones para su uso en las asignaciones. La iniciativa integrada denominada *[Versión preliminar]: Configuración de seguridad de la contraseña de la auditoría dentro de máquinas virtuales Linux y Windows* contiene 18 directivas. Hay seis pares **DeployIfNotExists** y **Audit** para Windows y tres pares para Linux. En cada caso, la lógica dentro de la definición valida que solo el sistema operativo de destino se evalúa según definición de la [regla de directiva](definition-structure.md#policy-rule).

## <a name="multiple-assignments"></a>Asignaciones múltiples

Actualmente, las directivas de configuración de invitado solo admiten la asignación de la misma asignación de invitado una vez por cada máquina virtual, incluso si la asignación de directiva usa parámetros diferentes.

## <a name="client-log-files"></a>Archivos de registro de cliente

La extensión de la configuración de invitado escribe archivos de registro en las siguientes ubicaciones:

Windows: `C:\Packages\Plugins\Microsoft.GuestConfiguration.ConfigurationforWindows\<version>\dsc\logs\dsc.log`

Linux: `/var/lib/waagent/Microsoft.GuestConfiguration.ConfigurationforLinux-<version>/GCAgent/logs/dsc.log`

Donde `<version>` se refiere al número de versión actual.

## <a name="guest-configuration-samples"></a>Ejemplos de configuración de invitado

Los ejemplos para la configuración de invitado de Policy están disponibles en las siguientes ubicaciones:

- [Índice de ejemplos: configuración de invitado](../samples/index.md#guest-configuration)
- [Repositorio de GitHub de ejemplos de Azure Policy](https://github.com/Azure/azure-policy/tree/master/samples/GuestConfiguration)

## <a name="next-steps"></a>Pasos siguientes

- Puede consultar ejemplos en [Ejemplos de Azure Policy](../samples/index.md).
- Revise la [estructura de definición de Azure Policy](definition-structure.md).
- Vea la [Descripción de los efectos de directivas](effects.md).
- Obtenga información acerca de cómo se pueden [crear directivas mediante programación](../how-to/programmatically-create.md).
- Obtenga información sobre cómo [obtener datos de cumplimiento](../how-to/getting-compliance-data.md).
- Obtenga información sobre cómo [corregir recursos no compatibles](../how-to/remediate-resources.md).
- En [Organización de los recursos con grupos de administración de Azure](../../management-groups/index.md), obtendrá información sobre lo que es un grupo de administración.
