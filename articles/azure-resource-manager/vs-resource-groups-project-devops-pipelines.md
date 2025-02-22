---
title: CI/CD con Azure Pipelines y plantillas de Resource Manager
description: Se describe cómo configurar la integración continua de Azure Pipelines mediante proyectos de implementación del grupo de recursos de Azure en Visual Studio.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: ae896fa0820fbd25ed3f2d29c89fbcd56e7fd6f5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982455"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Integración de plantillas de Resource Manager con Azure Pipelines

Visual Studio proporciona el proyecto del grupo de recursos de Azure para crear plantillas e implementarlas en su suscripción de Azure. Este proyecto se puede integrar con Azure Pipelines para la integración e implementación continuas (CI/CD).

Hay dos maneras de implementar plantillas con Azure Pipelines:

* **Agregar una tarea que ejecute un script de Azure PowerShell**. Esta opción tiene la ventaja de ofrecer coherencia en todo el ciclo de vida de desarrollo debido a que usa el mismo script que se incluye en el proyecto de Visual Studio (Deploy-AzureResourceGroup.ps1). El script agrega los artefactos del proyecto al "stage" en una cuenta de almacenamiento a la que Resource Manager puede acceder. Los artefactos son elementos del proyecto tales como plantillas vinculadas, scripts y archivos binarios de aplicación. Luego, el script implementa la plantilla.

* **Agregar tareas para copiar e implementar tareas**. Esta opción ofrece una alternativa conveniente al script del proyecto. Configure dos tareas en la canalización. Una tarea agrega los artefactos al "stage" y la otra tarea implementa la plantilla.

En este artículo se contemplan ambos métodos.

## <a name="prepare-your-project"></a>Preparación del proyecto

En este artículo se presupone que su proyecto de Visual Studio y la organización de Azure DevOps están listos para crear la canalización. Los pasos siguientes muestran cómo asegurarse de que está listo:

* Tiene una organización de Azure DevOps. Si no tiene ninguna, [cree una gratis](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Si su equipo ya tiene una organización de Azure DevOps, compruebe que es administrador del proyecto de Azure DevOps que quiere utilizar.

* Ha configurado una [conexión al servicio](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) en su suscripción de Azure. Las tareas en la canalización se ejecutan con la identidad de la entidad de servicio. Para conocer los pasos para crear la conexión, consulte [Crear un proyecto de DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* Tiene un proyecto de Visual Studio que se creó a partir de la plantilla de inicio **Grupo de recursos de Azure**. Para más información sobre cómo crear ese tipo de proyecto, consulte [Creación e implementación de grupos de recursos de Azure mediante Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* El proyecto de Visual Studio está [conectado a un proyecto de Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Creación de una canalización

1. Si no ha agregado una canalización anteriormente, tiene que crear una. En la organización de Azure DevOps, seleccione **Canalizaciones** y **Nueva canalización**.

   ![Adición de una nueva canalización](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Especifique dónde está almacenado el código. En la imagen siguiente se muestra la selección de **GIT de Azure Repos**.

   ![Selección del origen del código](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Desde ese origen, seleccione el repositorio que tiene el código para el proyecto.

   ![Selección del repositorio](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Seleccione el tipo de canalización que quiere crear. Puede seleccionar **Canalización inicial**.

   ![Selección de la canalización](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Está listo para agregar una tarea de Azure PowerShell o las tareas de copia de archivos y de implementación.

## <a name="azure-powershell-task"></a>Tarea de Azure PowerShell

En esta sección se muestra cómo configurar la implementación continua mediante el uso de una sola tarea que ejecuta el script de PowerShell en el proyecto. El siguiente archivo YAML crea una [tarea de Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Al establecer la tarea en `AzurePowerShell@3`, la canalización usa comandos del módulo AzureRM para autenticar la conexión. De forma predeterminada, el script de PowerShell del proyecto usa el módulo AzureRM. Si ha actualizado el script para usar el [módulo Az](/powershell/azure/new-azureps-module-az), defina la tarea como `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

En `azureSubscription`, proporcione el nombre de la conexión de servicio que creó.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Para `scriptPath`, proporcione la ruta de acceso relativa desde el archivo de canalización hasta el script. Puede buscar la ruta de acceso en el repositorio.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Si no tiene que agregar artefactos al "stage", simplemente pase el nombre y la ubicación de un grupo de recursos que se usará para la implementación. El script de Visual Studio crea el grupo de recursos si aún no existe.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Si tiene que agregar artefactos al "stage" en una cuenta de almacenamiento existente, use:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Ahora que ya sabe cómo crear la tarea, vamos a seguir los pasos para editar la canalización.

1. Abra la canalización y reemplace el contenido por el de YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Seleccione **Guardar**.

   ![Guardar la canalización](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Proporcione un mensaje para la confirmación y confirme directamente en **maestro**.

1. Al seleccionar **Guardar**, la canalización de compilación se ejecuta automáticamente. Vuelva al resumen de la canalización de compilación y vea el estado.

   ![Visualización de los resultados](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Puede seleccionar la canalización en ejecución para ver información detallada sobre las tareas. Cuando haya terminado, verá los resultados de cada paso.

## <a name="copy-and-deploy-tasks"></a>Tareas de copia y de implementación

En esta sección se muestra cómo configurar la implementación continua mediante el uso de dos tareas para agregar artefactos al "stage" e implementar la plantilla. 

El siguiente código YAML muestra la [tarea de copia de archivos de Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Hay varias partes de esta tarea que debe revisar para su entorno. El elemento `SourcePath` indica la ubicación de los artefactos en relación con el archivo de canalización. En este ejemplo, los archivos se encuentran en una carpeta denominada `AzureResourceGroup1`, que era el nombre del proyecto.

```yaml
SourcePath: '<path-to-artifacts>'
```

En `azureSubscription`, proporcione el nombre de la conexión de servicio que creó.

```yaml
azureSubscription: '<your-connection-name>'
```

En cuanto al almacenamiento y el nombre del contenedor, proporcione los nombres de la cuenta de almacenamiento y del contenedor que quiere usar para almacenar los artefactos. La cuenta de almacenamiento debe existir.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

El siguiente código YAML muestra la [tarea de implementación de un grupo de recursos de Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Hay varias partes de esta tarea que debe revisar para su entorno. En `azureSubscription`, proporcione el nombre de la conexión de servicio que creó.

```yaml
azureSubscription: '<your-connection-name>'
```

En `resourceGroupName` y `location`, proporcione el nombre y la ubicación del grupo de recursos en el que quiera realizar la implementación. La tarea crea el grupo de recursos si no existe.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

La tarea de implementación se vincula a una plantilla denominada `WebSite.json` y a un archivo de parámetros denominado WebSite.parameters.json. Utilice los nombres de la plantilla y el archivo de parámetros.

Ahora que ya sabe cómo crear la tarea, vamos a seguir los pasos para editar la canalización.

1. Abra la canalización y reemplace el contenido por el de YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Seleccione **Guardar**.

1. Proporcione un mensaje para la confirmación y confirme directamente en **maestro**.

1. Al seleccionar **Guardar**, la canalización de compilación se ejecuta automáticamente. Vuelva al resumen de la canalización de compilación y vea el estado.

   ![Visualización de los resultados](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Puede seleccionar la canalización en ejecución para ver información detallada sobre las tareas. Cuando haya terminado, verá los resultados de cada paso.

## <a name="next-steps"></a>Pasos siguientes

Para ver el proceso paso a paso sobre el uso de Azure Pipelines con plantillas de Resource Manager, consulte [Tutorial: Integración continua de plantillas de Azure Resource Manager en Azure Pipelines](resource-manager-tutorial-use-azure-pipelines.md).
