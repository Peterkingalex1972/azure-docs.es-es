---
title: Envío de métricas de SO invitado al almacén de métricas de Azure Monitor con una plantilla de Resource Manager para una máquina virtual Windows
description: Envío de métricas de SO invitado al almacén de métricas de Azure Monitor con una plantilla de Resource Manager para una máquina virtual Windows
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 85f7395cbfa4ef2ba6ab448c9541b3f107eb0e96
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68249819"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-using-a-resource-manager-template-for-a-windows-virtual-machine"></a>Envío de métricas de SO invitado al almacén de métricas de Azure Monitor con una plantilla de Resource Manager para una máquina virtual Windows

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Con la [extensión Diagnostics](diagnostics-extension-overview.md) de Azure Monitor, puede recopilar métricas y registros del sistema operativo invitado (SO invitado) que se ejecuta como parte de un clúster de Service Fabric, un servicio en la nube o una máquina virtual. La extensión puede enviar datos de telemetría a [muchas ubicaciones diferentes](https://docs.microsoft.com/azure/monitoring/monitoring-data-collection?toc=/azure/azure-monitor/toc.json).

En este artículo se describe el proceso de envío de métricas de rendimiento del SO invitado para una máquina virtual Windows al almacén de datos de Azure Monitor. A partir de Diagnostics versión 1.11, puede escribir las métricas directamente en el almacén de métricas de Azure Monitor, donde ya se recopilan métricas de la plataforma estándar.

Almacenarlas en esta ubicación permite tener acceso a las mismas acciones para las métricas de la plataforma. Las acciones incluyen la generación de alertas casi en tiempo real, la creación de gráficos, el enrutamiento, el acceso desde una API REST y mucho más. Anteriormente, la extensión Diagnostics se escribía en Azure Storage, pero no en el almacén de datos de Azure Monitor.

Si no está familiarizado con las plantillas de Resource Manager, obtenga información sobre las [implementaciones de plantilla](../../azure-resource-manager/resource-group-overview.md) y su estructura y sintaxis.

## <a name="prerequisites"></a>Requisitos previos

- La suscripción debe estar registrada en [Microsoft.Insights](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-supported-services).

- Debe tener instalado [Azure PowerShell](/powershell/azure) o [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview).


## <a name="set-up-azure-monitor-as-a-data-sink"></a>Configuración de Azure Monitor como receptor de datos
La extensión Azure Diagnostics usa una característica denominada "receptores de datos" para enrutar las métricas y los registros a distintas ubicaciones. En los pasos siguientes se muestra cómo usar una plantilla de Resource Manager y PowerShell para implementar una VM con el nuevo receptor de datos "Azure Monitor".

## <a name="author-resource-manager-template"></a>Creación de una plantilla de Resource Manager
En este ejemplo, puede usar una plantilla de ejemplo disponible públicamente. Las plantillas iniciales se encuentran en https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows.

- **Azuredeploy.json** es una plantilla de Resource Manager configurada previamente para la implementación de una máquina virtual.

- **Azuredeploy.Parameters.json** es un archivo de parámetros que almacena información como el nombre de usuario y la contraseña que le gustaría establecer para la VM. Durante la implementación, la plantilla de Resource Manager usa los parámetros establecidos en este archivo.

Descargue y guarde ambos archivos localmente.

### <a name="modify-azuredeployparametersjson"></a>Modificación de azuredeploy.parameters.json
Abra el archivo *azuredeploy.parameters.json*.

1. Especifique los valores de **adminUsername** y **adminPassword** para la VM. Estos parámetros se utilizan para el acceso remoto a la VM. Para evitar el secuestro de la VM, NO use los valores de esta plantilla. Los bots buscan por Internet los nombres de usuario y las contraseñas en repositorios públicos de GitHub. Es probable que sean VM de prueba con estos valores predeterminados.

1. Cree un dnsname único para la VM.

### <a name="modify-azuredeployjson"></a>Modificación de azuredeploy.json

Abra el archivo *azuredeploy.json*.

Agregue un identificador de cuenta de almacenamiento a la sección de **variables** de la plantilla después de la entrada para **storageAccountName**.

```json
// Find these lines.
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'sawinvm')]",

// Add this line directly below.
    "accountid": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
```

Agregue esta extensión de Managed Service Identity (MSI) a la plantilla en la parte superior de la sección **resources**. La extensión garantiza que Azure Monitor acepte las métricas que se emiten.

```json
//Find this code.
"resources": [
// Add this code directly below.
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'), '/', 'WADExtensionSetup')]",
        "apiVersion": "2017-12-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]" ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    },
```

Agregue la configuración de **identity** en el recurso de VM para asegurarse de que Azure asigne a la extensión MSI una identidad del sistema. Este paso garantiza que la VM pueda emitir métricas de invitado sobre sí misma a Azure Monitor.

```json
// Find this section
                "subnet": {
            "id": "[variables('subnetRef')]"
            }
        }
        }
    ]
    }
},
{
    "apiVersion": "2017-03-30",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    // add these 3 lines below
    "identity": {
    "type": "SystemAssigned"
    },
    //end of added lines
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
    "hardwareProfile": {
    ...
```

Agregue la siguiente configuración para habilitar la extensión de diagnóstico en una máquina virtual Windows. En una máquina virtual sencilla basada en Resource Manager, se puede agregar la configuración de extensión a la matriz de recursos de la máquina virtual. La línea "sinks"&mdash; "AzMonSink" y la correspondiente "SinksConfig" más adelante en la sección&mdash;permiten que la extensión emita métricas directamente a Azure Monitor. No dude en agregar o quitar contadores de rendimiento según sea necesario.


```json
        "networkProfile": {
            "networkInterfaces": [
            {
                "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
            }
            ]
        },
"diagnosticsProfile": {
    "bootDiagnostics": {
    "enabled": true,
    "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob]"
    }
}
},
//Start of section to add
"resources": [
{
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'), '/', 'Microsoft.Insights.VMDiagnosticsSettings')]",
            "apiVersion": "2017-12-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
            "publisher": "Microsoft.Azure.Diagnostics",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "1.12",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "WadCfg": {
                "DiagnosticMonitorConfiguration": {
    "overallQuotaInMB": 4096,
    "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
        },
                    "Directories": {
                    "scheduledTransferPeriod": "PT1M",
    "IISLogs": {
                        "containerName": "wad-iis-logfiles"
                    },
                    "FailedRequestLogs": {
                        "containerName": "wad-failedrequestlogs"
                    }
                    },
                    "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "sinks": "AzMonSink",
                    "PerformanceCounterConfiguration": [
                        {
                        "counterSpecifier": "\\Memory\\Available Bytes",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\% Committed Bytes In Use",
                        "sampleRate": "PT15S"
                        },
                        {
                        "counterSpecifier": "\\Memory\\Committed Bytes",
                        "sampleRate": "PT15S"
                        }
                    ]
                    },
                    "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                    "DataSource": [
                        {
                        "name": "Application!*"
                        }
                    ]
                    },
                    "Logs": {
                    "scheduledTransferPeriod": "PT1M",
                    "scheduledTransferLogLevelFilter": "Error"
                    }
                },
                "SinksConfig": {
                    "Sink": [
                    {
                        "name" : "AzMonSink",
                        "AzureMonitor" : {}
                    }
                    ]
                }
                },
                "StorageAccount": "[variables('storageAccountName')]"
            },
            "protectedSettings": {
                "storageAccountName": "[variables('storageAccountName')]",
                "storageAccountKey": "[listKeys(variables('accountid'),'2015-06-15').key1]",
                "storageAccountEndPoint": "https://core.windows.net/"
            }
            }
        }
        ]
//End of section to add
```


Guarde y cierre ambos archivos.


## <a name="deploy-the-resource-manager-template"></a>Implementación de la plantilla de Resource Manager

> [!NOTE]
> Debe estar ejecutando la versión de la extensión 1.5 o superior de Azure Diagnostics Y tener la propiedad **autoUpgradeMinorVersion**: establecida en "true" en la plantilla de Resource Manager. A continuación, Azure carga la extensión adecuada cuando se inicia la VM. Si no tiene estas opciones en la plantilla, modifíquelas y vuelva a implementar la plantilla.


Para implementar la plantilla de Resource Manager, se usa Azure PowerShell.

1. Inicie PowerShell.
1. Inicie sesión en Azure con `Login-AzAccount`.
1. Obtenga una lista de suscripciones con `Get-AzSubscription`.
1. Establezca la suscripción que se usa para crear o actualizar la máquina virtual en:

   ```powershell
   Select-AzSubscription -SubscriptionName "<Name of the subscription>"
   ```
1. Para crear un nuevo grupo de recursos para la VM que se está implementando, ejecute el comando siguiente:

   ```powershell
    New-AzResourceGroup -Name "<Name of Resource Group>" -Location "<Azure Region>"
   ```
   > [!NOTE]
   > Recuerde [usar una región de Azure que esté habilitada para las métricas personalizadas](metrics-custom-overview.md).

1. Ejecute los comandos siguientes para implementar la máquina virtual mediante la plantilla de Resource Manager.
   > [!NOTE]
   > Si quiere actualizar una VM existente, simplemente agregue *-Mode Incremental* al final del comando siguiente.

   ```powershell
   New-AzResourceGroupDeployment -Name "<NameThisDeployment>" -ResourceGroupName "<Name of the Resource Group>" -TemplateFile "<File path of your Resource Manager template>" -TemplateParameterFile "<File path of your parameters file>"
   ```

1. Una vez que la implementación se realice correctamente, la máquina virtual debe estar en Azure Portal, emitiendo métricas a Azure Monitor.

   > [!NOTE]
   > Puede que surjan errores en torno al parámetro vmSkuSize seleccionado. Si ocurre, vuelva al archivo azuredeploy.json y actualice el valor predeterminado del parámetro vmSkuSize. En este caso, se recomienda probar "Standard_DS1_v2").

## <a name="chart-your-metrics"></a>Gráfico de las métricas

1. Inicie sesión en el Portal de Azure.

2. En el menú de la izquierda, seleccione **Monitor**.

3. En la página Monitor, seleccione **Métricas**.

   ![Página Métricas](media/collect-custom-metrics-guestos-resource-manager-vm/metrics.png)

4. Cambie el período de agregación a **Últimos 30 minutos**.

5. En el menú desplegable de recursos, seleccione la VM que ha creado. Si no ha cambiado el nombre de la plantilla, debe ser *SimpleWinVM2*.

6. En el menú desplegable de espacios de nombres, seleccione **azure.vm.windows.guest**.

7. En el menú desplegable de métricas, seleccione **Memory\%Committed Bytes in Use** (Memoria%Bytes confirmados en uso).


## <a name="next-steps"></a>Pasos siguientes
- Más información acerca de las [métricas personalizadas](metrics-custom-overview.md).

