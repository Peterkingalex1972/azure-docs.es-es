---
title: Estructura y sintaxis de las plantillas de Azure Resource Manager | Microsoft Docs
description: Describe la estructura y las propiedades de plantillas de Azure Resource Manager mediante la sintaxis declarativa de JSON.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 08/02/2019
ms.author: tomfitz
ms.openlocfilehash: 9858e8a52888304edd48893db02faa992b356b3b
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774899"
---
# <a name="understand-the-structure-and-syntax-of-azure-resource-manager-templates"></a>Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager

En este artículo se describe la estructura de una plantilla de Azure Resource Manager. Presenta las distintas secciones de una plantilla y las propiedades que están disponibles en esas secciones. La plantilla consta de JSON y expresiones que puede usar para generar valores para su implementación.

Este artículo está destinado a los usuarios que ya estén familiarizados con las plantillas de Resource Manager. Proporciona información detallada sobre la estructura y la sintaxis de la plantilla. Si desea ver una presentación sobre cómo crear una plantilla, consulte [Creación de la primera plantilla de Azure Resource Manager](resource-manager-create-first-template.md).

## <a name="template-format"></a>Formato de plantilla

En la estructura más simple, una plantilla tiene los siguientes elementos:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "",
  "apiProfile": "",
  "parameters": {  },
  "variables": {  },
  "functions": [  ],
  "resources": [  ],
  "outputs": {  }
}
```

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--- |:--- |:--- |
| $schema |Sí |Ubicación del archivo de esquema JSON que describe la versión del idioma de la plantilla.<br><br> Para las implementaciones de grupos de recursos, use: `https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#`.<br><br>Para las implementaciones de suscripciones, use: `https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#`. |
| contentVersion |Sí |Versión de la plantilla (por ejemplo, 1.0.0.0). Puede especificar cualquier valor para este elemento. Use este valor para documentar los cambios importantes de la plantilla. Al implementar los recursos con la plantilla, este valor se puede usar para asegurarse de que se está usando la plantilla correcta. |
| apiProfile |Sin | Una versión de API que actúa como una colección de versiones de API para los tipos de recursos. Use este valor para evitar tener que especificar las versiones de API para cada recurso de la plantilla. Si especifica una versión del perfil de API y no especifica una versión de API para el tipo de recurso, Resource Manager usa la versión de API para el tipo de recurso que está definido en el perfil.<br><br>La propiedad del perfil de API es especialmente útil al implementar una plantilla en diferentes entornos, como Azure Stack y Azure global. Use la versión del perfil de API para asegurarse de que la plantilla utilice automáticamente las versiones que se admiten en ambos entornos. Para obtener una lista de las versiones del perfil de API actuales y de las versiones de API de recursos definidas en el perfil, consulte [API Profile](https://github.com/Azure/azure-rest-api-specs/tree/master/profile) (Perfil de API).<br><br>Para más información, consulte [Seguimiento de versiones mediante perfiles de API](templates-cloud-consistency.md#track-versions-using-api-profiles). |
| [parameters](#parameters) |Sin |Valores que se proporcionan cuando se ejecuta la implementación para personalizar la implementación de recursos. |
| [variables](#variables) |Sin |Valores que se usan como fragmentos JSON en la plantilla para simplificar expresiones de idioma de la plantilla. |
| [functions](#functions) |Sin |Funciones definidas por el usuario que están disponibles dentro de la plantilla. |
| [resources](#resources) |Sí |Tipos de servicios que se implementan o actualizan en un grupo de recursos o suscripción. |
| [outputs](#outputs) |Sin |Valores que se devuelven después de la implementación. |

Cada elemento tiene propiedades que puede configurar. En este artículo se describen las secciones de la plantilla con más detalle.

## <a name="syntax"></a>Sintaxis

La sintaxis básica de la plantilla es JSON. Sin embargo, puede utilizar expresiones para ampliar los valores JSON disponibles en la plantilla.  Las expresiones empiezan y terminan con corchetes: `[` y `]`, respectivamente. El valor de la expresión se evalúa cuando se implementa la plantilla. Una expresión puede devolver una cadena, un entero, un booleano, una matriz o un objeto. En el ejemplo siguiente se muestra una expresión en el valor predeterminado de un parámetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

En de la expresión, la sintaxis `resourceGroup()` llama a una de las funciones que Resource Manager proporciona para utilizarla dentro de una plantilla. Al igual que en JavaScript, las llamadas de función tienen el formato `functionName(arg1,arg2,arg3)`. La sintaxis `.location` recupera una propiedad del objeto devuelto por esta función.

Las funciones de plantilla y sus parámetros no distinguen mayúsculas de minúsculas. Por ejemplo, Resource Manager resuelve **variables('var1')** y **VARIABLES('VAR1')** de la misma manera. Cuando se evalúa, a menos que la función modifique expresamente las mayúsculas (como toUpper o toLower), la función conserva las mayúsculas. Es posible que determinados tipos de recursos tengan requisitos de mayúsculas independientemente de cómo se evalúen las funciones.

Para que una cadena literal empiece con un corchete izquierdo `[` y termine con un corchete derecho `]`, pero no se interprete como una expresión, agregue otro corchete para que la cadena comience con `[[`. Por ejemplo, la variable:

```json
"demoVar1": "[[test value]"
```

Se resuelve como `[test value]`.

Sin embargo, si la cadena literal no termina con un corchete de cierre, no utilice un carácter de escape en el primer corchete. Por ejemplo, la variable:

```json
"demoVar2": "[test] value"
```

Se resuelve como `[test] value`.

Para pasar un valor de cadena como parámetro a una función, use comillas simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

Para utilizar un carácter de escape en las comillas dobles en una expresión, como, por ejemplo, al agregar un objeto JSON a la plantilla, use la barra diagonal inversa.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Las expresiones de plantilla no pueden superar los 24 576 caracteres.

Para obtener la lista completa de las funciones de plantilla, consulte [Funciones de la plantilla del Administrador de recursos de Azure](resource-group-template-functions.md). 

## <a name="parameters"></a>Parámetros

En la sección de parámetros de la plantilla, especifique los valores que el usuario puede introducir al implementar los recursos. Estos valores de parámetros permiten personalizar la implementación al proporcionar valores que son específicos para un entorno concreto (por ejemplo, desarrollo, prueba y producción). No tiene que especificar parámetros en la plantilla, pero sin parámetros la plantilla implementaría siempre los mismos recursos con los mismos nombres, ubicaciones y propiedades.

Está limitado a 256 parámetros en una plantilla. Puede reducir el número de parámetros mediante el uso de objetos que contienen varias propiedades, tal como se muestra en este artículo.

### <a name="available-properties"></a>Propiedades disponibles

Las propiedades disponibles para un parámetro son:

```json
"parameters": {
  "<parameter-name>" : {
    "type" : "<type-of-parameter-value>",
    "defaultValue": "<default-value-of-parameter>",
    "allowedValues": [ "<array-of-allowed-values>" ],
    "minValue": <minimum-value-for-int>,
    "maxValue": <maximum-value-for-int>,
    "minLength": <minimum-length-for-string-or-array>,
    "maxLength": <maximum-length-for-string-or-array-parameters>,
    "metadata": {
      "description": "<description-of-the parameter>" 
    }
  }
}
```

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--- |:--- |:--- |
| parameterName |Sí |Nombre del parámetro. Debe ser un identificador válido de JavaScript. |
| type |Sí |Tipo del valor del parámetro. Los tipos y valores permitidos son **string**, **secureString**, **int**, **bool**, **objet**, **secureObject** y **array**. |
| defaultValue |Sin |Valor predeterminado del parámetro, si no se proporciona ningún valor. |
| allowedValues |Sin |Matriz de valores permitidos para el parámetro para asegurarse de que se proporciona el valor correcto. |
| minValue |Sin |El valor mínimo de parámetros de tipo int, este valor es inclusivo. |
| maxValue |Sin |El valor máximo de parámetros de tipo int, este valor es inclusivo. |
| minLength |Sin |Longitud mínima de los parámetros de tipo cadena, cadena segura y matriz; este valor es inclusivo. |
| maxLength |Sin |Longitud máxima de los parámetros de tipo cadena, cadena segura y matriz; este valor es inclusivo. |
| description |Sin |Descripción del parámetro que se muestra a los usuarios a través del portal. Para más información, consulte [Comentarios en plantillas](#comments). |

### <a name="define-and-use-a-parameter"></a>Definición y uso de un parámetro

En el ejemplo siguiente se muestra una definición simple de parámetro. Se define el nombre del parámetro y se especifica que toma un valor de cadena. El parámetro solo acepta valores que tienen sentido para el uso previsto. Cuando no se proporciona ningún valor durante la implementación, se especifica un valor predeterminado. Por último, el parámetro incluye una descripción de su uso.

```json
"parameters": {
  "storageSKU": {
    "type": "string",
    "allowedValues": [
      "Standard_LRS",
      "Standard_ZRS",
      "Standard_GRS",
      "Standard_RAGRS",
      "Premium_LRS"
    ],
    "defaultValue": "Standard_LRS",
    "metadata": {
      "description": "The type of replication to use for the storage account."
    }
  }   
}
```

En la plantilla,debe hacer referencia al valor del parámetro con la sintaxis siguiente:

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    ...
  }
]
```

### <a name="template-functions-with-parameters"></a>Funciones de plantilla con parámetros

Cuando se especifica el valor predeterminado de un parámetro, puede usar la mayoría de las funciones de plantilla. Puede usar otro valor de parámetro para compilar un valor predeterminado. La plantilla siguiente muestra el uso de funciones en el valor predeterminado:

```json
"parameters": {
  "siteName": {
    "type": "string",
    "defaultValue": "[concat('site', uniqueString(resourceGroup().id))]",
    "metadata": {
      "description": "The site name. To use the default value, do not specify a new value."
    }
  },
  "hostingPlanName": {
    "type": "string",
    "defaultValue": "[concat(parameters('siteName'),'-plan')]",
    "metadata": {
      "description": "The host name. To use the default value, do not specify a new value."
    }
  }
}
```

No puede usar la función `reference` en la sección de parámetros. Los parámetros se evalúan antes de la implementación, por lo que la función `reference` no puede obtener el estado de tiempo de ejecución de un recurso. 

### <a name="objects-as-parameters"></a>Objetos como parámetros

Puede ser más fácil organizar los valores relacionados pasándolos como objetos. Con este enfoque también se reduce el número de parámetros de la plantilla.

Defina el parámetro en la plantilla y especifique un objeto JSON en lugar de un valor único durante la implementación. 

```json
"parameters": {
  "VNetSettings": {
    "type": "object",
    "defaultValue": {
      "name": "VNet1",
      "location": "eastus",
      "addressPrefixes": [
        {
          "name": "firstPrefix",
          "addressPrefix": "10.0.0.0/22"
        }
      ],
      "subnets": [
        {
          "name": "firstSubnet",
          "addressPrefix": "10.0.0.0/24"
        },
        {
          "name": "secondSubnet",
          "addressPrefix": "10.0.1.0/24"
        }
      ]
    }
  }
},
```

Después, haga referencia a las subpropiedades del parámetro con el operador punto.

```json
"resources": [
  {
    "apiVersion": "2015-06-15",
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[parameters('VNetSettings').name]",
    "location": "[parameters('VNetSettings').location]",
    "properties": {
      "addressSpace":{
        "addressPrefixes": [
          "[parameters('VNetSettings').addressPrefixes[0].addressPrefix]"
        ]
      },
      "subnets":[
        {
          "name":"[parameters('VNetSettings').subnets[0].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[0].addressPrefix]"
          }
        },
        {
          "name":"[parameters('VNetSettings').subnets[1].name]",
          "properties": {
            "addressPrefix": "[parameters('VNetSettings').subnets[1].addressPrefix]"
          }
        }
      ]
    }
  }
]
```

### <a name="parameter-example-templates"></a>Plantillas de ejemplo de parámetros

Estas plantillas de ejemplo muestran algunos escenarios para usar los parámetros. Impleméntelos para probar cómo se controlan los parámetros en diferentes escenarios.

|Plantilla  |DESCRIPCIÓN  |
|---------|---------|
|[parámetros con funciones para los valores predeterminados](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterswithfunctions.json) | Muestra cómo utilizar las funciones de plantilla al definir valores predeterminados para parámetros. La plantilla no implementa ningún recurso. Genera valores de parámetro y devuelve dichos valores. |
|[objeto de parámetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/parameterobject.json) | Muestra cómo utilizar un objeto para un parámetro. La plantilla no implementa ningún recurso. Genera valores de parámetro y devuelve dichos valores. |

## <a name="variables"></a>variables

En la sección de variables, se crean valores que pueden usarse en toda la plantilla. No es necesario definir las variables, pero a menudo simplifican la plantilla reduciendo expresiones complejas.

### <a name="available-definitions"></a>Definiciones disponibles

En el ejemplo siguiente se muestran las opciones disponibles para definir una variable:

```json
"variables": {
  "<variable-name>": "<variable-value>",
  "<variable-name>": { 
    <variable-complex-type-value> 
  },
  "<variable-object-name>": {
    "copy": [
      {
        "name": "<name-of-array-property>",
        "count": <number-of-iterations>,
        "input": <object-or-value-to-repeat>
      }
    ]
  },
  "copy": [
    {
      "name": "<variable-array-name>",
      "count": <number-of-iterations>,
      "input": <object-or-value-to-repeat>
    }
  ]
}
```

Si desea información sobre el uso de `copy` para crear varios valores para una variable, consulte [Iteración de variables](resource-group-create-multiple.md#variable-iteration).

### <a name="define-and-use-a-variable"></a>Definición y uso de una variable

El ejemplo siguiente muestra una variable de definición. Crea un valor de cadena para un nombre de cuenta de almacenamiento. Usa varias funciones de plantilla para obtener un valor de parámetro y lo concatena a una cadena única.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Use la variable al definir el recurso.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

### <a name="configuration-variables"></a>Variables de configuración

Puede usar tipos complejos de JSON para definir los valores relacionados con un entorno.

```json
"variables": {
  "environmentSettings": {
    "test": {
      "instanceSize": "Small",
      "instanceCount": 1
    },
    "prod": {
      "instanceSize": "Large",
      "instanceCount": 4
    }
  }
},
```

En parámetros, se crea un valor que indica que valores de configuración usar.

```json
"parameters": {
  "environmentName": {
    "type": "string",
    "allowedValues": [
      "test",
      "prod"
    ]
  }
},
```

Puede recuperar la configuración actual con:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

### <a name="variable-example-templates"></a>Plantillas de ejemplo de variables

Estas plantillas de ejemplo muestran algunos escenarios para usar las variables. Impleméntelos para probar cómo se controlan las variables en diferentes escenarios. 

|Plantilla  |DESCRIPCIÓN  |
|---------|---------|
| [definiciones de variable](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Muestra los diferentes tipos de variables. La plantilla no implementa ningún recurso. Genera valores de variable y devuelve esos valores. |
| [variable de configuración](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Muestra el uso de una variable que define los valores de configuración. La plantilla no implementa ningún recurso. Genera valores de variable y devuelve esos valores. |
| [reglas de seguridad de red](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) y [archivo de parámetro](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Genera una matriz en el formato correcto para asignar las reglas de seguridad a un grupo de seguridad de red. |


## <a name="functions"></a>Functions

Dentro de la plantilla, puede crear sus propias funciones. Estas funciones están disponibles para su uso en la plantilla. Normalmente, definirá una expresión compleja que no desea repetir en toda la plantilla. Creará las funciones definidas por el usuario a partir de las expresiones y [funciones](resource-group-template-functions.md) que se admiten en las plantillas.

Al definir una función de usuario, hay algunas restricciones:

* La función no puede acceder a las variables.
* La función solo puede usar los parámetros que se definen en la función. Cuando usa la función [parameters](resource-group-template-functions-deployment.md#parameters) dentro de una función definida por el usuario, los parámetros de esa función serán los que limiten sus acciones.
* La función no puede llamar a otras funciones definidas por el usuario.
* La función no puede usar la [función de referencia](resource-group-template-functions-resource.md#reference).
* Los parámetros de la función no pueden tener valores predeterminados.

Las funciones requieren un valor de espacio de nombres para evitar conflictos de nomenclatura con las funciones de plantilla. En el ejemplo siguiente se muestra una función que devuelve un nombre de cuenta de almacenamiento:

```json
"functions": [
  {
    "namespace": "contoso",
    "members": {
      "uniqueName": {
        "parameters": [
          {
            "name": "namePrefix",
            "type": "string"
          }
        ],
        "output": {
          "type": "string",
          "value": "[concat(toLower(parameters('namePrefix')), uniqueString(resourceGroup().id))]"
        }
      }
    }
  }
],
```

Se llama a la función con:

```json
"resources": [
  {
    "name": "[contoso.uniqueName(parameters('storageNamePrefix'))]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "location": "South Central US",
    "tags": {},
    "properties": {}
  }
]
```

## <a name="resources"></a>Recursos
En la sección de recursos, se define que los recursos se implementan o se actualizan.

### <a name="available-properties"></a>Propiedades disponibles

Defina recursos con la siguiente estructura:

```json
"resources": [
  {
      "condition": "<true-to-deploy-this-resource>",
      "apiVersion": "<api-version-of-resource>",
      "type": "<resource-provider-namespace/resource-type-name>",
      "name": "<name-of-the-resource>",
      "location": "<location-of-resource>",
      "tags": {
          "<tag-name1>": "<tag-value1>",
          "<tag-name2>": "<tag-value2>"
      },
      "comments": "<your-reference-notes>",
      "copy": {
          "name": "<name-of-copy-loop>",
          "count": <number-of-iterations>,
          "mode": "<serial-or-parallel>",
          "batchSize": <number-to-deploy-serially>
      },
      "dependsOn": [
          "<array-of-related-resource-names>"
      ],
      "properties": {
          "<settings-for-the-resource>",
          "copy": [
              {
                  "name": ,
                  "count": ,
                  "input": {}
              }
          ]
      },
      "sku": {
          "name": "<sku-name>",
          "tier": "<sku-tier>",
          "size": "<sku-size>",
          "family": "<sku-family>",
          "capacity": <sku-capacity>
      },
      "kind": "<type-of-resource>",
      "plan": {
          "name": "<plan-name>",
          "promotionCode": "<plan-promotion-code>",
          "publisher": "<plan-publisher>",
          "product": "<plan-product>",
          "version": "<plan-version>"
      },
      "resources": [
          "<array-of-child-resources>"
      ]
  }
]
```

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--- |:--- |:--- |
| condition | Sin | Valor booleano que indica si el recurso se aprovisionará durante esta implementación. Si es `true`, el recurso se crea durante la implementación. Si es `false`, el recurso se omite para esta implementación. Consulte [condition](#condition). |
| apiVersion |Sí |Versión de la API de REST que debe usar para crear el recurso. Para determinar los valores disponibles, consulte la [referencia de plantilla](/azure/templates/). |
| type |Sí |Tipo de recurso. Este valor es una combinación del espacio de nombres del proveedor de recursos y el tipo de recurso (como **Microsoft.Storage/storageAccounts**). Para determinar los valores disponibles, consulte la [referencia de plantilla](/azure/templates/). Para un recurso secundario, el formato del tipo depende de si está anidado dentro del recurso primario o se ha definido fuera del recurso primario. Consulte [Establecimiento del nombre y el tipo de recursos secundarios](child-resource-name-type.md). |
| Nombre |Sí |Nombre del recurso. El nombre debe cumplir las restricciones de componente URI definidas en RFC3986. Además, los servicios de Azure que exponen el nombre del recurso a partes externas validan el nombre para asegurarse de que no es un intento de suplantar otra identidad. Para un recurso secundario, el formato del nombre depende de si está anidado dentro del recurso primario o se ha definido fuera del recurso primario. Consulte [Establecimiento del nombre y el tipo de recursos secundarios](child-resource-name-type.md). |
| location |Varía |Ubicaciones geográficas compatibles del recurso proporcionado. Puede seleccionar cualquiera de las ubicaciones disponibles, pero normalmente tiene sentido elegir aquella que esté más cerca de los usuarios. Normalmente, también tiene sentido colocar los recursos que interactúan entre sí en la misma región. La mayoría de los tipos de recursos requieren una ubicación, pero algunos (por ejemplo, una asignación de roles) no la necesitan. |
| etiquetas |Sin |Etiquetas asociadas al recurso. Aplique etiquetas para organizar de forma lógica los recursos en su suscripción. |
| comentarios |Sin |Notas para documentar los recursos de la plantilla. Para más información, consulte [Comentarios en plantillas](resource-group-authoring-templates.md#comments). |
| copia |Sin |Si se necesita más de una instancia, el número de recursos que se crearán. El modo predeterminado es paralelo. Si no desea que todos los recursos se implementen al mismo tiempo, especifique el modo serie. Para obtener más información, consulte [Creación de varias instancias de recursos en Azure Resource Manager](resource-group-create-multiple.md). |
| dependsOn |Sin |Recursos que se deben implementar antes de implementar este. Resource Manager evalúa las dependencias entre recursos y los implementa en su orden correcto. Cuando no hay recursos dependientes entre sí, se implementan en paralelo. El valor puede ser una lista separada por comas de nombres de recursos o identificadores de recursos únicos. Solo los recursos de lista que se implementan en esta plantilla. Deben existir los recursos que no estén definidos en esta plantilla. Evite agregar dependencias innecesarias, ya que pueden ralentizar la implementación y crear dependencias circulares. Para obtener instrucciones sobre la configuración de dependencias, consulte [Definición de dependencias en plantillas de Azure Resource Manager](resource-group-define-dependencies.md). |
| properties |Sin |Opciones de configuración específicas de recursos. Los valores de las propiedades son exactamente los mismos valores que se especifican en el cuerpo de la solicitud de la operación de API de REST (método PUT) para crear el recurso. También puede especificar una matriz de copia para crear varias instancias de una propiedad. Para determinar los valores disponibles, consulte la [referencia de plantilla](/azure/templates/). |
| sku | Sin | Algunos recursos permiten valores que definen la SKU que se va a implementar. Por ejemplo, puede especificar el tipo de redundancia para una cuenta de almacenamiento. |
| kind | Sin | Algunos recursos permiten un valor que define el tipo de recurso que va a implementar. Por ejemplo, puede especificar el tipo de instancia de Cosmos DB que va a crear. |
| plan | Sin | Algunos recursos permiten valores que definen el plan que se va a implementar. Por ejemplo, puede especificar la imagen de Marketplace para una máquina virtual. | 
| resources |Sin |Recursos secundarios que dependen del recurso que se está definiendo. Proporcione solo tipos de recursos que permita el esquema del recurso principal. La dependencia del recurso principal no está implícita. Debe definirla explícitamente. Consulte [Establecimiento del nombre y el tipo de recursos secundarios](child-resource-name-type.md). |

### <a name="condition"></a>Condición

Si debe decidir durante la implementación si debe crear un recurso, use el elemento `condition`. El valor de este elemento se resuelve como true o false. Cuando el valor es true, el recurso se crea. Cuando el valor es false, el recurso no se crea. El valor solo se puede aplicar a todo el recurso.

Por lo general, este valor se usa cuando desea crear un nuevo recurso o usar uno existente. Por ejemplo, para especificar si se implementa una nueva cuenta de almacenamiento o se usa una cuenta de almacenamiento existente, use:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

Para una plantilla de ejemplo completo que usa el elemento `condition`, consulte [VM with a new or existing Virtual Network, Storage, and Public IP](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) (Máquina virtual con una red virtual nueva o existente, almacenamiento y dirección IP pública).

Si usa una función [reference](resource-group-template-functions-resource.md#reference) o [list](resource-group-template-functions-resource.md#list) con un recurso que se implementa de forma condicional, se puede evaluar la función incluso si el recurso no está implementado. Se genera un error si la función hace referencia a un recurso que no existe. Use la función [if](resource-group-template-functions-logical.md#if) para asegurarse de que la función se evalúa solo para las condiciones en las que se implementa el recurso. Consulte la función [if](resource-group-template-functions-logical.md#if) para una plantilla de ejemplo que use if y haga referencia con un recurso implementado de forma condicional.

### <a name="resource-names"></a>Nombres de recurso

Por lo general, trabaja con tres tipos de nombres de recursos en Resource Manager:

* Nombres de recurso que deben ser únicos.
* Nombres de recurso que no tienen que ser únicos, pero decide proporcionarles un nombre que pueda ayudarle a identificarlos.
* Nombres de recurso que pueden ser genéricos.

Asigne un **nombre de recurso único** para cualquier tipo de recurso que tenga un punto de conexión de acceso a datos. Entre los tipos de recursos comunes que requieren un nombre único se incluyen los siguientes:

* Azure Storage<sup>1</sup> 
* Característica Web Apps de Azure App Service
* SQL Server
* Azure Key Vault
* Azure Cache for Redis
* Azure Batch
* Administrador de tráfico de Azure
* Azure Search
* HDInsight de Azure

<sup>1</sup> Los nombres de las cuentas de almacenamiento deben estar en minúsculas, tener 24 caracteres o menos y no incluir guiones.

Al establecer el nombre, puede crear un nombre único de forma manual o usar la función [uniqueString()](resource-group-template-functions-string.md#uniquestring) para generarlo. También puede que quiera agregar un prefijo o sufijo al resultado **uniqueString**. Modificar el nombre único podría ayudarlo a identificar más fácilmente el tipo de recurso a partir del nombre. Por ejemplo, puede generar un nombre único para una cuenta de almacenamiento si usa la variable siguiente:

```json
"variables": {
  "storageAccountName": "[concat(uniqueString(resourceGroup().id),'storage')]"
}
```

Para algunos tipos de recursos, es posible que desee proporcionar un **nombre de identificación**, aunque el nombre no tiene que ser único. Para estos tipos de recursos, proporcione un nombre que describa su uso o características.

```json
"parameters": {
  "vmName": { 
    "type": "string",
    "defaultValue": "demoLinuxVM",
    "metadata": {
      "description": "The name of the VM to create."
    }
  }
}
```

En los tipos de recursos a los que tiene acceso mayoritariamente por medio de otro recurso, puede usar un **nombre genérico** que esté codificado de forma rígida en la plantilla. Por ejemplo, puede establecer un nombre genérico estándar para reglas de firewall en un servidor SQL:

```json
{
  "type": "firewallrules",
  "name": "AllowAllWindowsAzureIps",
  ...
}
```

### <a name="resource-location"></a>Ubicación del recurso

Al implementar una plantilla, debe proporcionar una ubicación para cada recurso. Se admiten diferentes tipos de recursos en diferentes ubicaciones. Para obtener las ubicaciones admitidas para un tipo de recurso, consulte [Tipos de recursos y proveedores de recursos de Azure](resource-manager-supported-services.md).

Use un parámetro para especificar la ubicación de recursos y establecer el valor predeterminado en `resourceGroup().location`.

En el ejemplo siguiente se muestra una cuenta de almacenamiento que está implementada en una ubicación especificada como parámetro:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
    }
  ],
  "outputs": {
    "storageAccountName": {
      "type": "string",
      "value": "[variables('storageAccountName')]"
    }
  }
}
```

## <a name="outputs"></a>Salidas

En la sección de salidas, especifique valores que se devuelven de la implementación. Normalmente, devuelve valores de los recursos implementados.

### <a name="available-properties"></a>Propiedades disponibles

En el ejemplo siguiente se muestra la estructura de una definición de salida:

```json
"outputs": {
  "<outputName>" : {
    "condition": "<boolean-value-whether-to-output-value>",
    "type" : "<type-of-output-value>",
    "value": "<output-value-expression>"
  }
}
```

| Nombre del elemento | Obligatorio | DESCRIPCIÓN |
|:--- |:--- |:--- |
| outputName |Sí |Nombre del valor de salida. Debe ser un identificador válido de JavaScript. |
| condition |Sin | Valor booleano que indica si se va a devolver este valor de salida. Si es `true`, el valor se incluye en la salida de la implementación. Si es `false`, el recurso se omite en esta implementación. Si no se especifica, el valor predeterminado es `true`. |
| type |Sí |Tipo del valor de salida. Los valores de salida admiten los mismos tipos que los parámetros de entrada de plantilla. Si especifica **securestring** para el tipo de salida, el valor no se muestra en el historial de implementación y no se puede recuperar desde otra plantilla. Para usar un valor de secreto en más de una plantilla, almacene el secreto en un almacén de claves y haga referencia al secreto en el archivo de parámetros. Para más información, consulte [Uso de Azure Key Vault para pasar el valor de parámetro seguro durante la implementación](resource-manager-keyvault-parameter.md). |
| value |Sí |Expresión de lenguaje de plantilla que se evaluará y devolverá como valor de salida. |

### <a name="define-and-use-output-values"></a>Definición y uso de valores de salida

En el ejemplo siguiente se muestra cómo devolver el identificador de recurso para una dirección IP pública:

```json
"outputs": {
  "resourceID": {
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

En el ejemplo siguiente, se muestra cómo se devuelve condicionalmente el identificador de recurso de una dirección IP pública en función de si se ha implementado una nueva:

```json
"outputs": {
  "resourceID": {
    "condition": "[equals(parameters('publicIpNewOrExisting'), 'new')]",
    "type": "string",
    "value": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddresses_name'))]"
  }
}
```

Para obtener un ejemplo sencillo de salida condicional, consulte la [plantilla de salida condicional](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/conditional-output/azuredeploy.json).

Después de la implementación, puede recuperar el valor con el script. Para PowerShell, use:

```powershell
(Get-AzResourceGroupDeployment -ResourceGroupName <resource-group-name> -Name <deployment-name>).Outputs.resourceID.value
```

Para la CLI de Azure, utilice:

```azurecli-interactive
az group deployment show -g <resource-group-name> -n <deployment-name> --query properties.outputs.resourceID.value
```

Puede recuperar el valor de salida de una plantilla vinculada mediante la función [reference](resource-group-template-functions-resource.md#reference). Para obtener un valor de salida de una plantilla vinculada, recupere el valor de propiedad con sintaxis de esta manera: `"[reference('deploymentName').outputs.propertyName.value]"`.

Al obtener una propiedad de salida a partir de una plantilla vinculada, el nombre de propiedad no puede incluir un guión.

En el ejemplo siguiente, se muestra cómo establecer la dirección IP en un equilibrador de carga mediante la recuperación de un valor desde una plantilla vinculada.

```json
"publicIPAddress": {
  "id": "[reference('linkedTemplate').outputs.resourceID.value]"
}
```

No se puede usar la función `reference` en la sección de salidas de una [plantilla anidada](resource-group-linked-templates.md#link-or-nest-a-template). Para devolver los valores de un recurso implementado en una plantilla anidada, convierta la plantilla anidada en una plantilla vinculada.

### <a name="output-example-templates"></a>Plantillas de ejemplo de salidas

|Plantilla  |DESCRIPCIÓN  |
|---------|---------|
|[Variables de copia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) | Crea variables complejas y genera esos valores. No implementa ningún recurso. |
|[Dirección IP pública](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip.json) | Crea una dirección IP pública y genera el identificador de recurso. |
|[Equilibrador de carga](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/linkedtemplates/public-ip-parentloadbalancer.json) | Crea vínculos a la plantilla anterior. Usa el identificador de recurso de la salida al crear el equilibrador de carga. |


<a id="comments" />

## <a name="comments-and-metadata"></a>Comentarios y metadatos

Tiene unas cuantas opciones para agregar comentarios y metadatos a la plantilla.

Puede agregar un objeto `metadata` prácticamente en cualquier parte de la plantilla. Resource Manager omite el objeto, pero el editor JSON puede advertirle de que la propiedad no es válida. En el objeto, defina las propiedades que necesita.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "metadata": {
    "comments": "This template was developed for demonstration purposes.",
    "author": "Example Name"
  },
```

Para **parameters**, agregue un objeto `metadata` con una propiedad `description`.

```json
"parameters": {
  "adminUsername": {
    "type": "string",
    "metadata": {
      "description": "User name for the Virtual Machine."
    }
  },
```

Al implementar la plantilla a través del portal, el texto que proporciona en la descripción se usa automáticamente como sugerencia para ese parámetro.

![Mostrar sugerencia de parámetros](./media/resource-group-authoring-templates/show-parameter-tip.png)

Para **resources**, agregue un elemento `comments` o un objeto de metadatos. En el ejemplo siguiente se muestra un elemento de comentarios y un objeto de metadatos.

```json
"resources": [
  {
    "comments": "Storage account used to store VM disks",
    "apiVersion": "2018-07-01",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[concat('storage', uniqueString(resourceGroup().id))]",
    "location": "[parameters('location')]",
    "metadata": {
      "comments": "These tags are needed for policy compliance."
    },
    "tags": {
      "Dept": "[parameters('deptName')]",
      "Environment": "[parameters('environment')]"
    },
    "sku": {
      "name": "Standard_LRS"
    },
    "kind": "Storage",
    "properties": {}
  }
]
```

Para **outputs**, agregue un objeto de metadatos para el valor de salida.

```json
"outputs": {
  "hostname": {
    "type": "string",
    "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]",
    "metadata": {
      "comments": "Return the fully qualified domain name"
    }
  },
```

No se puede agregar un objeto de metadatos a las funciones definidas por el usuario.

Para los comentarios en línea, puede usar `//`, pero esta sintaxis no funciona con todas las herramientas. No se puede usar la CLI de Azure para implementar la plantilla con comentarios insertados. Y no se puede usar el editor de plantillas del portal para trabajar en las plantillas con comentarios insertados. Si agrega este estilo de comentarios, asegúrese de que las herramientas que use admitan los comentarios JSON insertados.

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[parameters('location')]", //defaults to resource group location
  "apiVersion": "2018-10-01",
  "dependsOn": [ // storage account and network interface must be deployed first
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

En VS Code, puede establecer el modo de lenguaje en JSON con comentarios. Los comentarios insertados ya no están marcados como no válidos. Para cambiar el modo:

1. Abra la selección del modo de lenguaje (presione Ctrl + K M)

1. Seleccione **JSON with Comments** (JSON con comentarios).

   ![Seleccionar modo de lenguaje](./media/resource-group-authoring-templates/select-json-comments.png)

[!INCLUDE [arm-tutorials-quickstarts](../../includes/resource-manager-tutorials-quickstarts.md)]

## <a name="next-steps"></a>Pasos siguientes
* Para ver plantillas completas de muchos tipos diferentes de soluciones, consulte [Plantillas de inicio rápido de Azure](https://azure.microsoft.com/documentation/templates/).
* Para obtener información detallada sobre las funciones que se pueden usar dentro de una plantilla, consulte [Funciones de plantilla de Azure Resource Manager](resource-group-template-functions.md).
* Para combinar varias plantillas durante la implementación, consulte [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para más recomendaciones sobre creación de platillas, consulte [Azure Resource Manager template best practices](template-best-practices.md) (Procedimientos recomendados para plantillas de Azure Resource Manager).
* Para recomendaciones sobre cómo crear plantillas de Resource Manager que puede usar en todos los entornos de Azure y Azure Stack, consulte [Desarrollo de plantillas de Azure Resource Manager para mantener la coherencia en la nube](templates-cloud-consistency.md).
