---
title: Introducción a Azure Automation State Configuration
description: Una introducción a Azure Automation State Configuration (DSC), sus condiciones y problemas conocidos
keywords: powershell dsc, configuración de estado deseado, powershell dsc azure
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 11/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: dd898397b4aaec2e62558d12a3547f7b61d6d3fd
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69533454"
---
# <a name="azure-automation-state-configuration-overview"></a>Introducción a Azure Automation State Configuration

Azure Automation State Configuration es un servicio de Azure que le permite escribir, administrar y compilar [configuraciones](/powershell/dsc/configurations) de PowerShell Desired State Configuration (DSC), importar [recursos de DSC](/powershell/dsc/resources) y asignar configuraciones a nodos de destino, todo en la nube.

## <a name="why-use-azure-automation-state-configuration"></a>Razones para usar Azure Automation State Configuration

Azure Automation State Configuration proporciona varias ventajas con respecto a DSC fuera de Azure.

### <a name="built-in-pull-server"></a>Servidor de extracción integrado

Azure Automation State Configuration proporciona un servidor de extracción de DSC similar a la [característica de Windows DSC-Service](/powershell/dsc/pullserver) para que los nodos de destino reciban automáticamente las configuraciones, se ajusten al estado deseado y devuelvan la información de cumplimiento. El servidor de extracción integrado en Azure Automation elimina la necesidad de configurar y mantener su propio servidor de extracción. Azure Automation puede tener como destino máquinas físicas o virtuales Windows o Linux, en la nube o en un entorno local.

### <a name="management-of-all-your-dsc-artifacts"></a>Administración de todos los artefactos de DSC

Azure Automation State Configuration proporciona la misma capa de administración a [PowerShell Desired State Configuration](/powershell/dsc/overview) que ofrece en la actualidad Azure Automation para el scripting de PowerShell.

Desde Azure Portal o desde PowerShell, puede administrar todas las configuraciones, recursos y nodos de destino de DSC.

![Captura de pantalla de la página de Azure Automation](./media/automation-dsc-overview/azure-automation-blade.png)

### <a name="import-reporting-data-into-azure-monitor-logs"></a>Importación de datos de informes en los registros de Azure Monitor

Los nodos que se administran con Azure Automation State Configuration envían datos de informe de estado detallados al servidor de extracción integrado. Puede configurar Azure Automation State Configuration para que envíe estos datos a su área de trabajo de Log Analytics. Para obtener información sobre cómo enviar datos de estado de State Configuration al área de trabajo de Log Analytics, consulte [Reenvío de datos de informes de Azure Automation State Configuration a los registros de Azure Monitor](automation-dsc-diagnostics.md).

## <a name="prerequisites"></a>Requisitos previos

Tenga en cuenta los siguientes requisitos al usar Azure Automation State Configuration (DSC).

### <a name="operating-system-requirements"></a>Requisitos de sistema operativo

Para nodos que ejecutan Windows, se admiten las siguientes versiones:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012R2
- Windows Server 2012
- Windows Server 2008 R2 SP1
- Windows 10
- Windows 8.1
- Windows 7

La SKU del producto independiente del [servidor de Hyper-V de Microsoft](/windows-server/virtualization/hyper-v/hyper-v-server-2016) no contiene una implementación de configuración de estado deseado, por lo que no se puede administrar con DSC de PowerShell o con Azure Automation State Configuration.

Para los nodos que ejecutan Linux, se admiten las siguientes versiones o distribuciones:

La extensión DSC de Linux admite todas las distribuciones de Linux [aprobadas en Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros), excepto:

Distribución | Versión
-|-
Debian  | Todas las versiones
Ubuntu  | 18,04

### <a name="dsc-requirements"></a>Requisitos de DSC

Para todos los nodos de Windows que se ejecutan en Azure, [WMF 5.1](https://docs.microsoft.com/powershell/wmf/setup/install-configure) se instalará durante la incorporación.  Para nodos que ejecutan Windows Server 2012 y Windows 7, [se habilitará WinRM](https://docs.microsoft.com/powershell/dsc/troubleshooting/troubleshooting#winrm-dependency).

Para todos los nodos de Linux que se ejecutan en Azure, [PowerShell DSC para Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) se instalará durante la incorporación.

### <a name="network-planning"></a>Configuración de redes privadas

Si los nodos se encuentran en una red privada, los siguientes puertos y direcciones URL son necesarias para que State Configuration (DSC) se comunique con Automation:

* Puerto: solo se requiere el puerto TCP 443 para el acceso a Internet.
* URL global: *.azure-automation.net
* Direcciones URL globales de US Gov Virginia: *.azure-automation.us
* Servicio de agente: https://\<workspaceId\>.agentsvc.azure-automation.net

Esto proporciona conectividad de red para que el nodo administrado se comunique con Azure Automation.
Si usa recursos de DSC que se comunican entre nodos, como los [recursos WaitFor*](https://docs.microsoft.com/powershell/dsc/reference/resources/windows/waitForAllResource), también deberá permitir el tráfico entre nodos.
Consulte la documentación de cada recurso de DSC para comprender los requisitos de red.

#### <a name="proxy-support"></a>Compatibilidad con proxy

La compatibilidad con proxy para el agente DSC está disponible en Windows, versión 1809 y posteriores.
Para configurar esta opción, establezca el valor de **ProxyURL** y **ProxyCredential** en el [script de metaconfiguración](automation-dsc-onboarding.md#generating-dsc-metaconfigurations) que se usa para registrar los nodos.
El proxy no está disponible en DSC para versiones anteriores de Windows.

Para los nodos de Linux, el agente de DSC admite proxy y utilizará la variable http_proxy para determinar la dirección URL.

#### <a name="azure-state-configuration-network-ranges-and-namespace"></a>Rangos y espacios de nombre de red de Azure State Configuration

Se recomienda utilizar las direcciones mostradas al definir las excepciones. Puede descargar los [intervalos de direcciones IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Este archivo se actualiza semanalmente y tiene los intervalos implementados en ese momento y los próximos cambios en los intervalos de direcciones IP.

Si tiene una cuenta de Automation definida para una región específica, puede restringir la comunicación con ese centro de datos regional. En la tabla siguiente se proporciona el registro de DNS para cada región:

| **Región** | **Registro de DNS** |
| --- | --- |
| Centro occidental de EE.UU. | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Centro-Sur de EE. UU |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| East US   | eus-jobruntimedata-prod-su1.azure-automation.net</br>eus-agentservice-prod-1.azure-automation.net |
| Este de EE. UU. 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Centro de Canadá |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa occidental |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Europa del Norte |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Sudeste de Asia |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| India Central |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Este de Japón |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Sudeste de Australia |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Sur de Reino Unido 2 | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| Gobierno de EE. UU. - Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Para obtener una lista de direcciones IP de regiones en lugar de nombres de regiones, descargue el archivo XML [Direcciones IP de los centros de datos de Azure](https://www.microsoft.com/download/details.aspx?id=41653) desde el Centro de descarga de Microsoft.

> [!NOTE]
> El archivo XML de direcciones IP de los centros de datos de Azure enumera los intervalos de direcciones IP que se usan en los centros de datos de Microsoft Azure. El archivo incluye el proceso, SQL y los intervalos de almacenamiento.
>
>Semanalmente, se publica un archivo actualizado. El archivo refleja los intervalos implementados actualmente y los próximos cambios en los intervalos IP. Los nuevos intervalos que aparecen en el archivo no se utilizan en los centros de datos durante al menos una semana.
>
> Descargar el archivo XML nuevo cada semana es una buena idea. A continuación, actualice el sitio para identificar correctamente los servicios que se ejecutan en Azure. Los usuarios de Azure ExpressRoute deberían observar que este archivo se usa para actualizar la publicidad del Protocolo de puerta de enlace de borde (BGP) del espacio de Azure la primera semana de cada mes.

## <a name="next-steps"></a>Pasos siguientes

- Para empezar a usar Azure Automation State Configuration, consulte [Introducción a Azure Automation State Configuration](automation-dsc-getting-started.md)
- Para aprender a incorporar nodos, consulte [Incorporación de máquinas para administrarlas con Azure Automation State Configuration](automation-dsc-onboarding.md)
- Para obtener información acerca de la compilación de configuraciones de DSC para que poder asignarlas a los nodos de destino, consulte [Compilación de configuraciones en Azure Automation State Configuration](automation-dsc-compile.md).
- Para la referencia de cmdlets de PowerShell, consulte [cmdlets de Azure Automation State Configuration](/powershell/module/azurerm.automation/#automation)
- Para obtener información de precios, consulte [Precios de Azure Automation State Configuration](https://azure.microsoft.com/pricing/details/automation/)
- Para ver un ejemplo del uso de Azure Automation State Configuration en una canalización de implementación continua, consulte [Implementación continua mediante Azure Automation State Configuration y Chocolatey](automation-dsc-cd-chocolatey.md)
