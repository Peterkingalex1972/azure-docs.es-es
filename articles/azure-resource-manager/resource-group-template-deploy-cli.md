---
title: Implementación de recursos con la plantilla y la CLI de Azure | Microsoft Docs
description: Use Azure Resource Manager y la CLI de Azure para implementar recursos en Azure. Los recursos se definen en una plantilla de Resource Manager.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 93b1b16776bac6cb24996d6fa08a547318802f32
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853828"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Implementación de recursos con plantillas de Resource Manager y la CLI de Azure

En este artículo, se explica el uso de la CLI de Azure con plantillas de Resource Manager para implementar recursos en Azure. Si no está familiarizado con los conceptos asociados a la implementación y administración de las soluciones de Azure, consulte [Introducción a Azure Resource Manager](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Si no tiene instalada la CLI de Azure, puede usar [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Ámbito de la implementación

La implementación puede tener como destino una suscripción de Azure o un grupo de recursos dentro de una suscripción. En la mayoría de los casos, la implementación tendrá como destino un grupo de recursos. Use las implementaciones de la suscripción para aplicar directivas y asignaciones de roles en la suscripción. También puede usar las implementaciones de la suscripción para crear un grupo de recursos e implementar recursos en él. Según el ámbito de la implementación, usará comandos diferentes.

Para implementar en un **grupo de recursos**, use [az group deployment create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Para implementar en una **suscripción**, use [az deployment create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Actualmente, solo se admiten las implementaciones del grupo de administración a través de la API de REST. Vea [Implementación de recursos con las plantillas de Resource Manager y la API REST de Resource Manager](resource-group-template-deploy-rest.md).

Los ejemplos de este artículo usan las implementaciones del grupo de recursos. Para obtener más información sobre la implementación de suscripciones, vea [Creación de grupos de recursos y otros recursos en el nivel de suscripción](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Implementar una plantilla local

Al implementar recursos en Azure, siga estos pasos:

1. Inicio de sesión en la cuenta de Azure.
2. Cree un grupo de recursos que actúe como contenedor para los recursos implementados. El nombre del grupo de recursos solo puede incluir caracteres alfanuméricos, puntos, guiones bajos, guiones y paréntesis. Puede tener hasta 90 caracteres. No puede terminar en punto.
3. Implemente en el grupo de recursos la plantilla que define los recursos que se van a crear.

Una plantilla puede incluir parámetros que le permiten personalizar la implementación. Por ejemplo, puede proporcionar valores que están diseñados para un entorno concreto (como desarrollo, prueba y producción). La plantilla de ejemplo define un parámetro para la SKU de la cuenta de almacenamiento. 

En el ejemplo siguiente se crea un grupo de recursos y se implementa una plantilla desde la máquina local:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

La implementación puede demorar unos minutos en completarse. Cuando termine, verá un mensaje que incluye el resultado:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Implementar plantilla remota

En lugar de almacenar las plantillas de Resource Manager en el equipo local, quizás prefiera almacenarlas en una ubicación externa. Puede almacenar plantillas en un repositorio de control de código fuente (por ejemplo, GitHub). O bien, puede almacenarlas en una cuenta de Azure Storage para el acceso compartido en su organización.

Para implementar una plantilla externa, use el parámetro **template-uri**. Use el identificador URI en el ejemplo para implementar la plantilla de ejemplo de GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

En el ejemplo anterior, se requiere un identificador URI accesible públicamente para la plantilla, que funciona con la mayoría de los escenarios porque la plantilla no debe incluir datos confidenciales. Si tiene que especificar datos confidenciales (por ejemplo, una contraseña de administrador), pase ese valor como un parámetro seguro. Sin embargo, si no desea que la plantilla sea accesible públicamente, puede almacenarla en un contenedor de almacenamiento privado para protegerla. Para más información sobre la implementación de una plantilla que requiere un token de Firma de acceso compartido (SAS), consulte [Implementación de una plantilla privada con el token de SAS](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

En Cloud Shell, use los comandos siguientes:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Nueva implementación cuando se produce un error en la implementación

Esta característica también es conocida como *reversión en caso de error*. Cuando se produce un error en una implementación, puede ejecutar automáticamente desde el historial de implementación una implementación anterior que sea correcta. Para especificar una nueva implementación, utilice el parámetro `--rollback-on-error` en el comando de implementación. Esta funcionalidad es útil si tiene un estado correcto conocido para la implementación de la infraestructura y quiere volver a ese estado. Hay una serie de advertencias y restricciones:

- La reimplementación se ejecuta exactamente como se ejecutó previamente y con los mismos parámetros. Los parámetros no se pueden cambiar.
- La implementación anterior se ejecuta con el [modo completo](./deployment-modes.md#complete-mode). Los recursos no incluidos en la implementación anterior se eliminan, y se establecen las configuraciones del recurso al estado anterior. Asegúrese de que comprende perfectamente los [modos de implementación](./deployment-modes.md).
- La reimplementación solo afecta a los recursos, los cambios de datos no se ven afectados.
- Esta característica solo se admite en implementaciones del grupo de recursos, no en implementaciones en el nivel de suscripción. Para obtener más información sobre las implementaciones en el nivel de suscripción, vea [Creación de grupos de recursos y otros recursos en el nivel de suscripción](./deploy-to-subscription.md).

Para usar esta opción, las implementaciones deben tener nombres únicos para que se puedan identificar en el historial. Si no tienen nombres únicos, la implementación con error en cuestión podría sobrescribir la implementación anteriormente correcta en el historial. Solo se puede usar esta opción con las implementaciones de nivel de raíz. Las implementaciones de una plantilla anidada no están disponibles para volver a implementarse.

Para volver a implementar la última implementación correcta, agregue el parámetro `--rollback-on-error` como una marca.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Para volver a implementar una implementación específica, use el parámetro `--rollback-on-error` y proporcione el nombre de la implementación.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

La implementación especificada debe haberse realizado correctamente.

## <a name="parameters"></a>Parámetros

Para pasar valores de parámetros, puede usar parámetros en línea o un archivo de parámetros. Los ejemplos anteriores de este artículo muestran parámetros en línea.

### <a name="inline-parameters"></a>Parámetros en línea

Para pasar parámetros en línea, proporcione los valores en `parameters`. Por ejemplo, para pasar una cadena y una matriz a una plantilla en un shell de Bash, use:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Si usa la CLI de Azure con el símbolo del sistema de Windows (CMD) o PowerShell, pase la matriz con el formato `exampleArray="['value1','value2']"`.

También puede obtener el contenido del archivo y proporcionar ese contenido como un parámetro en línea.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Obtener un valor de parámetro de un archivo es útil cuando se necesita proporcionar valores de configuración. Por ejemplo, puede proporcionar [valores de cloud-init para una máquina virtual Linux](../virtual-machines/linux/using-cloud-init.md).

El formato de arrayContent.json es:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Archivos de parámetros

En lugar de pasar parámetros como valores en línea en el script, quizá le resulte más fácil usar un archivo JSON que contiene los valores de parámetro. El archivo de parámetros debe ser un archivo local. No se admiten los archivos de parámetros externos con la CLI de Azure.

El archivo de parámetros debe estar en el siguiente formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Tenga en cuenta que la sección de parámetros incluye un nombre de parámetro que coincide con el parámetro definido en la plantilla (storageAccountType). El archivo de parámetros contiene un valor para el parámetro. Este valor se pasa automáticamente a la plantilla durante la implementación. Puede crear varios archivos de parámetros y, después, pasar el archivo de parámetros adecuado para el escenario. 

Copie el ejemplo anterior y guárdelo como un archivo denominado `storage.parameters.json`.

Para pasar un archivo de parámetros local, use `@` para especificar un archivo local denominado storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Prioridad de parámetros

Puede usar parámetros en línea y un archivo de parámetros local en la misma operación de implementación. Por ejemplo, puede especificar algunos valores en el archivo de parámetros local y agregar otros valores en línea durante la implementación. Si proporciona valores para un parámetro en el archivo de parámetros local y en línea, el valor en línea tiene prioridad.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>Prueba de una implementación de plantilla

Para probar los valores de parámetro y de plantilla sin implementar realmente ningún recurso, use [az group deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Si no se detectan errores, el comando devuelve información sobre la implementación de prueba. En concreto, tenga en cuenta que el valor **error** es null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Si se detecta un error, el comando devuelve un mensaje de error. Por ejemplo, si se pasa un valor incorrecto para la SKU de la cuenta de almacenamiento, se devuelve el error siguiente:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Si la plantilla tiene un error de sintaxis, el comando devuelve un error que indica que la plantilla no se pudo analizar. El mensaje indica el número de línea y la posición del error de análisis.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

## <a name="next-steps"></a>Pasos siguientes

- Los ejemplos de este artículo implementan recursos en un grupo de recursos de su suscripción predeterminada. Para usar una suscripción diferente, consulte [Administración de varias suscripciones de Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
- Para especificar cómo controlar los recursos que existen en el grupo de recursos, pero que no están definidos en la plantilla, consulte [Modos de implementación de Azure Resource Manager](deployment-modes.md).
- Para entender cómo definir parámetros en la plantilla, consulte [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
- Para obtener sugerencias para resolver los errores de implementación más comunes, consulte [Solución de errores comunes de implementación de Azure con Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Para más información sobre la implementación de una plantilla que requiere un token de SAS, vea [Implementación de una plantilla privada con el token de SAS](resource-manager-cli-sas-token.md).
- Para el lanzamiento seguro del servicio en más de una región, consulte [Azure Deployment Manager](deployment-manager-overview.md).
