---
title: Administración de IoT Central desde Azure PowerShell | Microsoft Docs
description: Administración de IoT Central desde Azure PowerShell.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/11/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 23243324c64519094432ee0c80d3e0cad447ef8b
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849050"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Administración de IoT Central desde Azure PowerShell

[!INCLUDE [iot-central-selector-manage](../../includes/iot-central-selector-manage.md)]

En lugar de crear y administrar aplicaciones de IoT Central desde la página [Application Manager](https://aka.ms/iotcentral) (Administrador de aplicaciones) de IoT Central, puede usar [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) para administrar las aplicaciones.

## <a name="prerequisites"></a>Requisitos previos

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si prefiere ejecutar Azure PowerShell en el equipo local, consulte [Install the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps) (Instalación del módulo de Azure PowerShell). Cuando ejecute Azure PowerShell localmente, use el cmdlet **Connect-AzAccount** para iniciar sesión en Azure antes de probar los cmdlets descritos en este artículo.

## <a name="install-the-iot-central-module"></a>Instalación del módulo de IoT Central

Ejecute el siguiente comando para comprobar si el [módulo de IoT Central](https://docs.microsoft.com/powershell/module/az.iotcentral/) está instalado en el entorno de PowerShell:

```powershell
Get-InstalledModule -name Az.I*
```

Si la lista de módulos instalados no incluye **Az.IotCentral**, ejecute el siguiente comando:

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>Creación de una aplicación

Use el cmdlet [New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) para crear una aplicación de IoT Central en su suscripción de Azure. Por ejemplo:

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "S1" -Template "iotc-demo@1.0.0" `
  -DisplayName "My Custom Display Name"
```

El script crea primero un grupo de recursos en la región Este de EE. UU. para la aplicación. En la tabla siguiente se describen los parámetros utilizados con el comando **New-AzIotCentralApp**:

|Parámetro         |DESCRIPCIÓN |
|------------------|------------|
|ResourceGroupName |Grupo de recursos que contiene a la aplicación. Este grupo de recursos ya debe existir en la suscripción. |
|Location |De forma predeterminada, este cmdlet usa la ubicación del grupo de recursos. Actualmente, puede crear una aplicación de IoT Central en las regiones **Este de EE. UU.** , **Oeste de EE. UU.** , **Europa del Norte** o **Europa Occidental**. |
|NOMBRE              |Nombre de la aplicación en Azure Portal. |
|Subdominio         |Subdominio en la dirección URL de la aplicación. En el ejemplo, la dirección URL de la aplicación es https://mysubdomain.azureiotcentral.com. |
|SKU               |Actualmente, el único valor es **S1** (nivel estándar). Consulte [Precios de Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/). |
|Plantilla          | Plantilla de aplicación que se va a usar. Para más información, vea la tabla siguiente: |
|DisplayName       |Nombre de la aplicación tal como se muestra en la interfaz de usuario. |

**Plantillas de aplicación**

|Nombre de la plantilla  |DESCRIPCIÓN |
|---------------|------------|
|iotc-default@1.0.0 |Permite crear una aplicación vacía para que pueda rellenarla con sus propias plantillas de dispositivo y dispositivos. |
|iotc-demo@1.0.0    |Crea una aplicación que incluye una plantilla de dispositivo que ya se ha creado para una máquina expendedora de refrigerados. Utilice esta plantilla para empezar a explorar Azure IoT Central. |
|iotc-devkit-sample@1.0.0 |Permite crear una aplicación con plantillas de dispositivo preparadas para que se conecte a un dispositivo MXChip o Raspberry Pi. Utilice esta plantilla si es un desarrollador de dispositivos que experimenta con alguno de estos dispositivos. |

## <a name="view-your-iot-central-applications"></a>Visualización de las aplicaciones de IoT Central

Use el cmdlet [Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) para enumerar las aplicaciones de IoT Central y ver los metadatos.

## <a name="modify-an-application"></a>Modificación de una aplicación

Use el cmdlet [Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) para actualizar los metadatos de una aplicación de IoT Central. Por ejemplo, para cambiar el nombre para mostrar de la aplicación, use:

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>Eliminación de una aplicación

Use el cmdlet [Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) para eliminar una aplicación de IoT Central. Por ejemplo:

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar aplicaciones de Azure IoT Central desde Azure PowerShell, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [Administrar su aplicación](howto-administer.md)
