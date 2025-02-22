---
title: 'Funciones de la plantilla de Azure Resource Manager: cadena | Microsoft Docs'
description: Describe las funciones para usar en una plantilla de Azure Resource Manager para trabajar con cadenas.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 07/31/2019
ms.author: tomfitz
ms.openlocfilehash: 1db1eb9be3586f76a8d3abc48a78a1e691da9924
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698188"
---
# <a name="string-functions-for-azure-resource-manager-templates"></a>Funciones de cadena para las plantillas de Azure Resource Manager

El Administrador de recursos ofrece las siguientes funciones para trabajar con cadenas:

* [base64](#base64)
* [base64ToJson](#base64tojson)
* [base64ToString](#base64tostring)
* [concat](#concat)
* [contains](#contains)
* [dataUri](#datauri)
* [dataUriToString](#datauritostring)
* [empty](#empty)
* [endsWith](#endswith)
* [first](#first)
* [format](#format)
* [guid](#guid)
* [indexOf](#indexof)
* [last](#last)
* [lastIndexOf](#lastindexof)
* [length](#length)
* [newGuid](#newguid)
* [padLeft](#padleft)
* [replace](#replace)
* [skip](#skip)
* [split](#split)
* [startsWith](#startswith)
* [string](#string)
* [substring](#substring)
* [take](#take)
* [toLower](#tolower)
* [toUpper](#toupper)
* [trim](#trim)
* [uniqueString](#uniquestring)
* [uri](#uri)
* [uriComponent](#uricomponent)
* [uriComponentToString](#uricomponenttostring)
* [utcNow](#utcnow)

## <a name="base64"></a>base64

`base64(inputString)`

Devuelve la representación de base64 de la cadena de entrada.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| inputString |Sí |string |Valor que se va a devolver como una representación de base64. |

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene la representación en base64.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) siguiente se muestra cómo utilizar la función de base64.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| base64Output | Cadena | b25lLCB0d28sIHRocmVl |
| toStringOutput | Cadena | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tojson"></a>base64ToJson

`base64tojson`

Convierte una representación en base64 a un objeto JSON.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| base64Value |Sí |string |La representación en base64 para convertir en un objeto JSON. |

### <a name="return-value"></a>Valor devuelto

Un objeto JSON.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) siguiente se utiliza la función base64ToJson para convertir un valor base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| base64Output | Cadena | b25lLCB0d28sIHRocmVl |
| toStringOutput | Cadena | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="base64tostring"></a>base64ToString

`base64ToString(base64Value)`

Convierte una representación en base64 en una cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| base64Value |Sí |string |La representación en base64 para convertir en una cadena. |

### <a name="return-value"></a>Valor devuelto

Una cadena del valor convertido de base64.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/base64.json) siguiente se utiliza la función base64ToString para convertir un valor base64:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringData": {
            "type": "string",
            "defaultValue": "one, two, three"
        },
        "jsonFormattedData": {
            "type": "string",
            "defaultValue": "{'one': 'a', 'two': 'b'}"
        }
    },
    "variables": {
        "base64String": "[base64(parameters('stringData'))]",
        "base64Object": "[base64(parameters('jsonFormattedData'))]"
    },
    "resources": [
    ],
    "outputs": {
        "base64Output": {
            "type": "string",
            "value": "[variables('base64String')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[base64ToString(variables('base64String'))]"
        },
        "toJsonOutput": {
            "type": "object",
            "value": "[base64ToJson(variables('base64Object'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| base64Output | Cadena | b25lLCB0d28sIHRocmVl |
| toStringOutput | Cadena | one, two, three |
| toJsonOutput | Object | {"one": "a", "two": "b"} |

## <a name="concat"></a>concat

`concat (arg1, arg2, arg3, ...)`

Combina varios valores de cadena y devuelve la cadena concatenada, o combina varias matrices y devuelve la matriz concatenada.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |cadena o matriz |El primer valor para la concatenación. |
| argumentos adicionales |Sin |string |Valores adicionales en orden secuencial para la concatenación. |

### <a name="return-value"></a>Valor devuelto
Una cadena o matriz de valores concatenados.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-string.json) siguiente se muestra cómo combinar dos valores de cadena y devolver una cadena concatenada.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "prefix": {
            "type": "string",
            "defaultValue": "prefix"
        }
    },
    "resources": [],
    "outputs": {
        "concatOutput": {
            "value": "[concat(parameters('prefix'), '-', uniqueString(resourceGroup().id))]",
            "type" : "string"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| concatOutput | Cadena | prefix-5yj4yjf5mbg72 |

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/concat-array.json) siguiente se muestra cómo combinar dos matrices.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { 
        "firstArray": { 
            "type": "array", 
            "defaultValue": [ 
                "1-1", 
                "1-2", 
                "1-3" 
            ] 
        },
        "secondArray": {
            "type": "array", 
            "defaultValue": [ 
                "2-1", 
                "2-2",
                "2-3" 
            ] 
        }
    },
    "resources": [
    ],
    "outputs": {
        "return": {
            "type": "array",
            "value": "[concat(parameters('firstArray'), parameters('secondArray'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| return | Array | ["1-1", "1-2", "1-3", "2-1", "2-2", "2-3"] |

## <a name="contains"></a>contains

`contains (container, itemToFind)`

Comprueba si una matriz contiene un valor, un objeto contiene una clave o una cadena contiene una subcadena. La comparación de cadena distingue mayúsculas de minúsculas. Pero, cuando se prueba si un objeto contiene una clave, la comparación no distingue mayúsculas de minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| container |Sí |matriz, objeto o cadena |El valor que contiene el valor para buscar. |
| itemToFind |Sí |cadena o entero |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el elemento se encuentra; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/contains.json) siguiente se muestra cómo utilizar contains con diferentes tipos:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "OneTwoThree"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {"one": "a", "two": "b", "three": "c"}
        },
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "stringTrue": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'e')]"
        },
        "stringFalse": {
            "type": "bool",
            "value": "[contains(parameters('stringToTest'), 'z')]"
        },
        "objectTrue": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'one')]"
        },
        "objectFalse": {
            "type": "bool",
            "value": "[contains(parameters('objectToTest'), 'a')]"
        },
        "arrayTrue": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'three')]"
        },
        "arrayFalse": {
            "type": "bool",
            "value": "[contains(parameters('arrayToTest'), 'four')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| stringTrue | Booleano | True |
| stringFalse | Bool | False |
| objectTrue | Bool | True |
| objectFalse | Bool | False |
| arrayTrue | Bool | True |
| arrayFalse | Bool | False |

## <a name="datauri"></a>dataUri

`dataUri(stringToConvert)`

Convierte un valor en un identificador URI de datos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToConvert |Sí |string |El valor para convertir en un identificador URI de datos. |

### <a name="return-value"></a>Valor devuelto

Una cadena con formato de identificador URI de datos.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) siguiente se convierte un valor en un identificador URI de datos, y se convierte un identificador URI de datos en una cadena:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| dataUriOutput | Cadena | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Cadena | Hola mundo. |

## <a name="datauritostring"></a>dataUriToString

`dataUriToString(dataUriToConvert)`

Convierte un valor con formato de identificador URI de datos en una cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| dataUriToConvert |Sí |string |El valor del identificador URI para convertir. |

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene el valor convertido.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/datauri.json) siguiente se convierte un valor en un identificador URI de datos, y se convierte un identificador URI de datos en una cadena:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "stringToTest": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "dataFormattedString": {
            "type": "string",
            "defaultValue": "data:;base64,SGVsbG8sIFdvcmxkIQ=="
        }
    },
    "resources": [],
    "outputs": {
        "dataUriOutput": {
            "value": "[dataUri(parameters('stringToTest'))]",
            "type" : "string"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[dataUriToString(parameters('dataFormattedString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| dataUriOutput | Cadena | data:text/plain;charset=utf8;base64,SGVsbG8= |
| toStringOutput | Cadena | Hola mundo. |

## <a name="empty"></a>empty

`empty(itemToTest)`

Determina si una matriz, un objeto o una cadena están vacíos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| itemToTest |Sí |matriz, objeto o cadena |El valor para comprobar si está vacío. |

### <a name="return-value"></a>Valor devuelto

Devuelve **True** si el valor está vacío; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/empty.json) siguiente se comprueba si una matriz, un objeto y una cadena están vacíos.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": []
        },
        "testObject": {
            "type": "object",
            "defaultValue": {}
        },
        "testString": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testArray'))]"
        },
        "objectEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testObject'))]"
        },
        "stringEmpty": {
            "type": "bool",
            "value": "[empty(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| arrayEmpty | Bool | True |
| objectEmpty | Bool | True |
| stringEmpty | Bool | True |

## <a name="endswith"></a>endsWith

`endsWith(stringToSearch, stringToFind)`

Determina si una cadena termina con un valor. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |string |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el último carácter o caracteres de la cadena coinciden con el valor; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) siguiente se muestra cómo utilizar las funciones startsWith y endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="first"></a>first

`first(arg1)`

Devuelve el primer carácter de la cadena o el primer elemento de la matriz.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |El valor para recuperar el primer elemento o carácter. |

### <a name="return-value"></a>Valor devuelto

Una cadena del primer carácter, o el tipo (cadena, entero, matriz u objeto) del primer elemento en una matriz.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/first.json) siguiente se muestra cómo utilizar la primera función con una matriz y una cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[first(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[first('One Two Three')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| arrayOutput | Cadena | one |
| stringOutput | Cadena | O |

## <a name="format"></a>format

`format(formatString, arg1, arg2, ...)`

Crea una cadena con formato a partir de valores de entrada.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| formatString | Sí | string | La cadena de formato compuesta. |
| arg1 | Sí | valor booleano, entero o cadena | El valor que se va a incluir en la cadena con formato. |
| argumentos adicionales | Sin | valor booleano, entero o cadena | Valores adicionales que se van a incluir en la cadena con formato. |

### <a name="remarks"></a>Comentarios

Utilice esta función para dar formato a una cadena en la plantilla. Usa las mismas opciones de formato que el método [System.String.Format](/dotnet/api/system.string.format) en. NET.

### <a name="examples"></a>Ejemplos

En la plantilla de ejemplo siguiente se muestra cómo usar la función de formato.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "greeting": {
            "type": "string",
            "defaultValue": "Hello"
        },
        "name": {
            "type": "string",
            "defaultValue": "User"
        },
        "numberToFormat": {
            "type": "int",
            "defaultValue": 8175133
        }
    },
    "resources": [
    ],
    "outputs": {
        "formatTest": {
            "type": "string",
            "value": "[format('{0}, {1}. Formatted number: {2:N0}', parameters('greeting'), parameters('name'), parameters('numberToFormat'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| formatTest | Cadena | Hola, usuario. Número con formato: 8 175 133 |

## <a name="guid"></a>GUID

`guid(baseString, ...)`

Crea un valor en el formato de un identificador único global en función de los valores proporcionados como parámetros.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| baseString |Sí |string |El valor utilizado en la función hash para crear el GUID. |
| parámetros adicionales según sea necesario |Sin |string |Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad. |

### <a name="remarks"></a>Comentarios

Esta función es útil cuando se necesita crear un valor en el formato de un identificador único global. Proporciona valores de parámetros que limitan el ámbito de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación.

El valor devuelto no es una cadena aleatoria, sino que es el resultado de una función hash en los parámetros. El valor devuelto tiene 36 caracteres. No es único globalmente. Para crear un nuevo GUID que no se base en el valor hash de los parámetros, use la función [newGuid](#newguid).

En los ejemplos siguientes se muestra cómo utilizar un GUID para crear un valor único para niveles de uso común.

Único basado en la suscripción

```json
"[guid(subscription().subscriptionId)]"
```

Único basado en el grupo de recursos

```json
"[guid(resourceGroup().id)]"
```

Único basado en la implementación de un grupo de recursos

```json
"[guid(resourceGroup().id, deployment().name)]"
```

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene 36 caracteres en el formato de un identificador único global.

### <a name="examples"></a>Ejemplos

La [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/guid.json) siguiente devuelve resultados de GUID:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [],
    "outputs": {
        "guidPerSubscription": {
            "value": "[guid(subscription().subscriptionId)]",
            "type": "string"
        },
        "guidPerResourceGroup": {
            "value": "[guid(resourceGroup().id)]",
            "type": "string"
        },
        "guidPerDeployment": {
            "value": "[guid(resourceGroup().id, deployment().name)]",
            "type": "string"
        }
    }
}
```

## <a name="indexof"></a>indexOf

`indexOf(stringToSearch, stringToFind)`

Devuelve la primera posición de un valor dentro de una cadena. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |string |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa la posición del elemento que se va a buscar. El valor está basado en cero. Si no se encuentra el elemento, se devuelve -1.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) siguiente se muestra cómo utilizar las funciones indexOf y lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="last"></a>last

`last (arg1)`

Devuelve el último carácter de la cadena, o el último elemento de la matriz.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz o cadena |El valor para recuperar el último elemento o carácter. |

### <a name="return-value"></a>Valor devuelto

Una cadena del último carácter, o el tipo (cadena, entero, matriz u objeto) del último elemento de una matriz.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/last.json) siguiente se muestra cómo utilizar la última función con una matriz y una cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": ["one", "two", "three"]
        }
    },
    "resources": [
    ],
    "outputs": {
        "arrayOutput": {
            "type": "string",
            "value": "[last(parameters('arrayToTest'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[last('One Two Three')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| arrayOutput | Cadena | three |
| stringOutput | Cadena | e |

## <a name="lastindexof"></a>lastIndexOf

`lastIndexOf(stringToSearch, stringToFind)`

Devuelve la última posición de un valor dentro de una cadena. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |string |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

Un entero que representa la última posición del elemento que se va a buscar. El valor está basado en cero. Si no se encuentra el elemento, se devuelve -1.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/indexof.json) siguiente se muestra cómo utilizar las funciones indexOf y lastIndexOf:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "firstT": {
            "value": "[indexOf('test', 't')]",
            "type" : "int"
        },
        "lastT": {
            "value": "[lastIndexOf('test', 't')]",
            "type" : "int"
        },
        "firstString": {
            "value": "[indexOf('abcdef', 'CD')]",
            "type" : "int"
        },
        "lastString": {
            "value": "[lastIndexOf('abcdef', 'AB')]",
            "type" : "int"
        },
        "notFound": {
            "value": "[indexOf('abcdef', 'z')]",
            "type" : "int"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| firstT | Int | 0 |
| lastT | int | 3 |
| firstString | Int | 2 |
| lastString | Int | 0 |
| notFound | Int | -1 |

## <a name="length"></a>length

`length(string)`

Devuelve el número de caracteres en una cadena, elementos en una matriz o propiedades de nivel raíz en un objeto.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| arg1 |Sí |matriz, cadena u objeto |La matriz que se usará para obtener el número de elementos, la cadena que se usará para obtener el número de caracteres o el objeto que se usará para obtener el número de propiedades del nivel raíz. |

### <a name="return-value"></a>Valor devuelto

Un entero. 

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/length.json) siguiente se muestra cómo utilizar length con una matriz y una cadena:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "arrayToTest": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "stringToTest": {
            "type": "string",
            "defaultValue": "One Two Three"
        },
        "objectToTest": {
            "type": "object",
            "defaultValue": {
                "propA": "one",
                "propB": "two",
                "propC": "three",
                "propD": {
                    "propD-1": "sub",
                    "propD-2": "sub"
                }
            }
        }
    },
    "resources": [],
    "outputs": {
        "arrayLength": {
            "type": "int",
            "value": "[length(parameters('arrayToTest'))]"
        },
        "stringLength": {
            "type": "int",
            "value": "[length(parameters('stringToTest'))]"
        },
        "objectLength": {
            "type": "int",
            "value": "[length(parameters('objectToTest'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| arrayLength | Int | 3 |
| stringLength | Int | 13 |
| objectLength | Int | 4 |

## <a name="newguid"></a>newGuid

`newGuid()`

Devuelve un valor en el formato de un identificador único global. **Esta función sólo puede utilizarse en el valor predeterminado para un parámetro.**

### <a name="remarks"></a>Comentarios

Solo puede usar esta función dentro de una expresión para el valor predeterminado de un parámetro. El uso de esta función en cualquier otro lugar de una plantilla genera un error. La función no se permite en otras partes de la plantilla porque devuelve un valor diferente cada vez que se le llama. La implementación de la misma plantilla con los mismos parámetros no produciría de forma confiable los mismos resultados.

La función newGuid difiere de la función [guid](#guid) en que no toma ningún parámetro. Cuando se llama a guid con el mismo parámetro, devuelve el mismo identificador cada vez. Use guid cuando necesite generar de forma confiable el mismo GUID para un entorno específico. Use newGuid cuando necesite un identificador diferente cada vez, como en la implementación de recursos en un entorno de prueba.

Si usa la [opción de volver a implementar una implementación que se completó correctamente en un momento anterior](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails) y esa implementación anterior incluye un parámetro que usa newGuid, el parámetro no se vuelve a evaluar. En su lugar, el valor del parámetro de la implementación anterior se reutiliza automáticamente en la implementación de reversión.

En un entorno de prueba, es posible que deba implementar repetidamente recursos que solo duran un corto tiempo. En lugar de construir nombres únicos, puede usar newGuid con [uniqueString](#uniquestring) para crear nombres únicos.

Tenga cuidado al volver a implementar una plantilla que se base en la función newGuid para un valor predeterminado. Si vuelve a implementar y no proporciona un valor para el parámetro, la función se vuelve a evaluar. Si desea actualizar un recurso existente en lugar de crear uno nuevo, pase el valor de parámetro de la implementación anterior.

### <a name="return-value"></a>Valor devuelto

Una cadena que contiene 36 caracteres en el formato de un identificador único global.

### <a name="examples"></a>Ejemplos

La plantilla de ejemplo siguiente muestra un parámetro con un nuevo identificador.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "guidOutput": {
            "type": "string",
            "value": "[parameters('guidValue')]"
        }
    }
}
```

El resultado del ejemplo anterior varía para cada implementación, pero será similar a:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| guidOutput | string | b76a51fc-bd72-4a77-b9a2-3c29e7d2e551 |

En el ejemplo siguiente se usa la función newGuid para crear un nombre único para una cuenta de almacenamiento. Esta plantilla puede funcionar en el entorno de prueba donde la cuenta de almacenamiento existe durante un breve tiempo y no se vuelve a implementar.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "guidValue": {
            "type": "string",
            "defaultValue": "[newGuid()]"
        }
    },
    "variables": {
        "storageName": "[concat('storage', uniqueString(parameters('guidValue')))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('storageName')]",
            "location": "West US",
            "apiVersion": "2018-07-01",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "nameOutput": {
            "type": "string",
            "value": "[variables('storageName')]"
        }
    }
}
```

El resultado del ejemplo anterior varía para cada implementación, pero será similar a:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| nameOutput | string | storagenziwvyru7uxie |


## <a name="padleft"></a>padLeft

`padLeft(valueToPad, totalLength, paddingCharacter)`

Devuelve una cadena alineada a la derecha agregando caracteres a la izquierda hasta alcanzar la longitud total especificada.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| valueToPad |Sí |cadena o entero |Valor que se va a alinear a la derecha. |
| totalLength |Sí |int |El número total de caracteres de la cadena devuelta. |
| paddingCharacter |Sin |carácter individual |El carácter que se va a usar para el relleno a la izquierda hasta alcanza la longitud total. El valor predeterminado es un espacio. |

Si la cadena original es mayor que el número de caracteres que se va a rellenar, no se agrega ningún carácter.

### <a name="return-value"></a>Valor devuelto

Una cadena con al menos el número de caracteres especificados.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/padleft.json) siguiente se muestra cómo rellenar el valor del parámetro proporcionado por el usuario agregando el carácter cero hasta que alcance el número total de caracteres. 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123"
        }
    },
    "resources": [],
    "outputs": {
        "stringOutput": {
            "type": "string",
            "value": "[padLeft(parameters('testString'),10,'0')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| stringOutput | Cadena | 0000000123 |

## <a name="replace"></a>replace

`replace(originalString, oldString, newString)`

Devuelve una nueva cadena con todas las instancias de una cadena reemplazadas por otra cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| originalString |Sí |string |Valor que tiene todas las instancias de una cadena reemplazadas por otra cadena. |
| oldString |Sí |string |Cadena que se va a quitar de la cadena original. |
| newString |Sí |string |La cadena que se va a agregar en lugar de la cadena eliminada. |

### <a name="return-value"></a>Valor devuelto

Una cadena con los caracteres reemplazados.

### <a name="examples"></a>Ejemplos

La [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/replace.json) siguiente muestra cómo quitar todos los guiones de la cadena proporcionada por el usuario y cómo reemplazar parte de la cadena por otra cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "123-123-1234"
        }
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'-', '')]"
        },
        "secondOutput": {
            "type": "string",
            "value": "[replace(parameters('testString'),'1234', 'xxxx')]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| firstOutput | Cadena | 1231231234 |
| secondOutput | Cadena | 123-123-xxxx |

## <a name="skip"></a>skip

`skip(originalValue, numberToSkip)`

Devuelve una cadena con todos los caracteres después del número especificado de caracteres, o una matriz con todos los elementos después del número especificado de elementos.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| originalValue |Sí |matriz o cadena |La matriz o cadena que se usará para la omisión. |
| numberToSkip |Sí |int |El número de elementos o caracteres que se van a omitir. Si este valor es 0 o un valor inferior, se devuelven todos los elementos o caracteres del valor. Si es mayor que la longitud de la matriz o la cadena, se devuelve una matriz o cadena vacía. |

### <a name="return-value"></a>Valor devuelto

Una matriz o cadena.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/skip.json) siguiente se omite el número especificado de elementos de la matriz, y el número especificado de caracteres de la cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToSkip": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToSkip": {
            "type": "int",
            "defaultValue": 4
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[skip(parameters('testArray'),parameters('elementsToSkip'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[skip(parameters('testString'),parameters('charactersToSkip'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| arrayOutput | Array | ["three"] |
| stringOutput | Cadena | two three |

## <a name="split"></a>split

`split(inputString, delimiter)`

Devuelve una matriz de cadenas que contiene las subcadenas de la cadena de entrada que están delimitadas por los delimitadores especificados.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| inputString |Sí |string |La cadena que se va a dividir. |
| delimiter |Sí |cadena o matriz de cadenas |Delimitador que se utilizará para dividir la cadena. |

### <a name="return-value"></a>Valor devuelto

Una matriz de cadenas.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/split.json) siguiente se divide la cadena de entrada con una coma, y con una coma o un punto y coma.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "firstString": {
            "type": "string",
            "defaultValue": "one,two,three"
        },
        "secondString": {
            "type": "string",
            "defaultValue": "one;two,three"
        }
    },
    "variables": {
        "delimiters": [ ",", ";" ]
    },
    "resources": [],
    "outputs": {
        "firstOutput": {
            "type": "array",
            "value": "[split(parameters('firstString'),',')]"
        },
        "secondOutput": {
            "type": "array",
            "value": "[split(parameters('secondString'),variables('delimiters'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| firstOutput | Array | ["one", "two", "three"] |
| secondOutput | Array | ["one", "two", "three"] |

## <a name="startswith"></a>startsWith

`startsWith(stringToSearch, stringToFind)`

Determina si una cadena empieza con un valor. La comparación distingue entre mayúsculas y minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToSearch |Sí |string |El valor que contiene el elemento para buscar. |
| stringToFind |Sí |string |El valor para buscar. |

### <a name="return-value"></a>Valor devuelto

**True** si el primer carácter o caracteres de la cadena coinciden con el valor; en caso contrario, **False**.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/startsendswith.json) siguiente se muestra cómo utilizar las funciones startsWith y endsWith:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "startsTrue": {
            "value": "[startsWith('abcdef', 'ab')]",
            "type" : "bool"
        },
        "startsCapTrue": {
            "value": "[startsWith('abcdef', 'A')]",
            "type" : "bool"
        },
        "startsFalse": {
            "value": "[startsWith('abcdef', 'e')]",
            "type" : "bool"
        },
        "endsTrue": {
            "value": "[endsWith('abcdef', 'ef')]",
            "type" : "bool"
        },
        "endsCapTrue": {
            "value": "[endsWith('abcdef', 'F')]",
            "type" : "bool"
        },
        "endsFalse": {
            "value": "[endsWith('abcdef', 'e')]",
            "type" : "bool"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| startsTrue | Bool | True |
| startsCapTrue | Bool | True |
| startsFalse | Bool | False |
| endsTrue | Bool | True |
| endsCapTrue | Bool | True |
| endsFalse | Bool | False |

## <a name="string"></a>string

`string(valueToConvert)`

Convierte el valor especificado en cadena.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| valueToConvert |Sí | Any |El valor que se convierte en cadena. Se puede convertir cualquier tipo de valor, incluidos objetos y matrices. |

### <a name="return-value"></a>Valor devuelto

Cadena del valor convertido.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/string.json) siguiente se muestra cómo convertir distintos tipos de valores en cadenas:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testObject": {
            "type": "object",
            "defaultValue": {
                "valueA": 10,
                "valueB": "Example Text"
            }
        },
        "testArray": {
            "type": "array",
            "defaultValue": [
                "a",
                "b",
                "c"
            ]
        },
        "testInt": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "resources": [],
    "outputs": {
        "objectOutput": {
            "type": "string",
            "value": "[string(parameters('testObject'))]"
        },
        "arrayOutput": {
            "type": "string",
            "value": "[string(parameters('testArray'))]"
        },
        "intOutput": {
            "type": "string",
            "value": "[string(parameters('testInt'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| objectOutput | Cadena | {"valueA":10,"valueB":"Example Text"} |
| arrayOutput | Cadena | ["a","b","c"] |
| intOutput | Cadena | 5 |

## <a name="substring"></a>substring

`substring(stringToParse, startIndex, length)`

Devuelve una subcadena que empieza en la posición de carácter especificada y que contiene el número especificado de caracteres.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToParse |Sí |string |La cadena original desde la que se extrae la subcadena. |
| startIndex |Sin |int |La posición de carácter inicial basado en cero de la subcadena. |
| length |Sin |int |El número de caracteres de la subcadena. Debe hacer referencia a una ubicación dentro de la cadena. Debe ser cero o mayor. |

### <a name="return-value"></a>Valor devuelto

Subcadena. O bien, una cadena vacía si la longitud es cero.

### <a name="remarks"></a>Comentarios

La función genera un error cuando la subcadena supera el final de la cadena, o bien cuando la longitud es menor que cero. En el ejemplo siguiente se produce el error "Los parámetros index y length deben hacer referencia a una ubicación dentro de la cadena. El parámetro index: "0", el parámetro length: "11", la longitud del parámetro string: "10".

```json
"parameters": {
    "inputString": { "type": "string", "value": "1234567890" }
},
"variables": { 
    "prefix": "[substring(parameters('inputString'), 0, 11)]"
}
```

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/substring.json) siguiente se extrae una subcadena de un parámetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        }
    },
    "resources": [],
    "outputs": {
        "substringOutput": {
            "value": "[substring(parameters('testString'), 4, 3)]",
            "type": "string"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| substringOutput | Cadena | two |

## <a name="take"></a>take

`take(originalValue, numberToTake)`

Devuelve una cadena con el número especificado de caracteres desde el inicio de la cadena, o una matriz con el número especificado de elementos desde el inicio de la matriz.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| originalValue |Sí |matriz o cadena |La matriz o cadena de la que se van a tomar los elementos. |
| numberToTake |Sí |int |El número de elementos o caracteres que se van a tomar. Si este valor es 0 o un valor inferior, se devolverá una matriz o cadena vacía. Si es mayor que la longitud de la matriz o cadena especificada, se devuelven todos los elementos de la matriz o cadena. |

### <a name="return-value"></a>Valor devuelto

Una matriz o cadena.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/take.json) siguiente se toma el número especificado de elementos de la matriz y de caracteres de la cadena.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testArray": {
            "type": "array",
            "defaultValue": [
                "one",
                "two",
                "three"
            ]
        },
        "elementsToTake": {
            "type": "int",
            "defaultValue": 2
        },
        "testString": {
            "type": "string",
            "defaultValue": "one two three"
        },
        "charactersToTake": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "resources": [],
    "outputs": {
        "arrayOutput": {
            "type": "array",
            "value": "[take(parameters('testArray'),parameters('elementsToTake'))]"
        },
        "stringOutput": {
            "type": "string",
            "value": "[take(parameters('testString'),parameters('charactersToTake'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| arrayOutput | Array | ["one", "two"] |
| stringOutput | Cadena | en |

## <a name="tolower"></a>toLower

`toLower(stringToChange)`

Convierte la cadena especificada a minúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToChange |Sí |string |Valor que se va a convertir a minúsculas. |

### <a name="return-value"></a>Valor devuelto

Cadena convertida a minúsculas.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) siguiente se convierte un valor de parámetro a minúsculas y a mayúsculas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| toLowerOutput | Cadena | one two three |
| toUpperOutput | Cadena | ONE TWO THREE |

## <a name="toupper"></a>toUpper

`toUpper(stringToChange)`

Convierte la cadena especificada a mayúsculas.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToChange |Sí |string |Valor que se va a convertir a mayúsculas. |

### <a name="return-value"></a>Valor devuelto

Cadena convertida a mayúsculas.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/tolower.json) siguiente se convierte un valor de parámetro a minúsculas y a mayúsculas.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "One Two Three"
        }
    },
    "resources": [],
    "outputs": {
        "toLowerOutput": {
            "value": "[toLower(parameters('testString'))]",
            "type": "string"
        },
        "toUpperOutput": {
            "type": "string",
            "value": "[toUpper(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| toLowerOutput | Cadena | one two three |
| toUpperOutput | Cadena | ONE TWO THREE |

## <a name="trim"></a>trim

`trim (stringToTrim)`

Quita todos los caracteres de espacio en blanco iniciales y finales de la cadena especificada.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToTrim |Sí |string |Valor que se recortará. |

### <a name="return-value"></a>Valor devuelto

Cadena sin caracteres de espacio en blanco iniciales ni finales.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/trim.json) siguiente se recortan los caracteres de espacio en blanco del parámetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "testString": {
            "type": "string",
            "defaultValue": "    one two three   "
        }
    },
    "resources": [],
    "outputs": {
        "return": {
            "type": "string",
            "value": "[trim(parameters('testString'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| return | Cadena | one two three |

## <a name="uniquestring"></a>uniqueString

`uniqueString (baseString, ...)`

Crea una cadena de hash determinista basada en los valores proporcionados como parámetros. 

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| baseString |Sí |string |Valor utilizado en la función hash para crear una cadena única. |
| parámetros adicionales según sea necesario |Sin |string |Puede agregar tantas cadenas como necesite para crear el valor que especifica el nivel de unicidad. |

### <a name="remarks"></a>Comentarios

Esta función es útil cuando se debe crear un nombre único para un recurso. Proporciona valores de parámetros que limitan el ámbito de unicidad del resultado. Puede especificar si el nombre es único para la suscripción, el grupo de recursos o la implementación. 

El valor devuelto no es una cadena aleatoria, sino que es el resultado de una función hash. El valor devuelto tiene 13 caracteres. No es único globalmente. Puede que desee combinar el valor con un prefijo de su convención de nomenclatura para crear un nombre que sea más fácil de reconocer. En el ejemplo siguiente se muestra el formato del valor devuelto. El valor real varía según los parámetros proporcionados.

    tcvhiyu5h2o5o

En los ejemplos siguientes se muestra cómo utilizar uniqueString a fin de crear un valor único para niveles de uso común.

Único basado en la suscripción

```json
"[uniqueString(subscription().subscriptionId)]"
```

Único basado en el grupo de recursos

```json
"[uniqueString(resourceGroup().id)]"
```

Único basado en la implementación de un grupo de recursos

```json
"[uniqueString(resourceGroup().id, deployment().name)]"
```

En el ejemplo siguiente se muestra cómo crear un nombre único para una cuenta de almacenamiento basada en el grupo de recursos. Dentro del grupo de recursos, el nombre no es único si se crea de la misma manera.

```json
"resources": [{ 
    "name": "[concat('storage', uniqueString(resourceGroup().id))]", 
    "type": "Microsoft.Storage/storageAccounts", 
    ...
```

Si necesita crear un nuevo nombre único cada vez que implemente una plantilla y no tiene intención de actualizar el recurso, puede usar la función [utcNow](#utcnow) con uniqueString. Podría utilizar este enfoque en un entorno de prueba. Para ver un ejemplo, consulte [utcNow](#utcnow).

### <a name="return-value"></a>Valor devuelto

Cadena que contiene 13 caracteres.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uniquestring.json) siguiente se devuelven los resultados de uniquestring:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [],
    "outputs": {
        "uniqueRG": {
            "value": "[uniqueString(resourceGroup().id)]",
            "type" : "string"
        },
        "uniqueDeploy": {
            "value": "[uniqueString(resourceGroup().id, deployment().name)]",
            "type" : "string"
        }
    }
}
```

## <a name="uri"></a>uri

`uri (baseUri, relativeUri)`

Crea un URI absoluto mediante la combinación de la cadena de relativeUri y baseUri.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| baseUri |Sí |string |La cadena de uri base. |
| relativeUri |Sí |string |La cadena de uri relativo que se agregará a la cadena de uri base. |

El valor del parámetro **baseUri** puede incluir un archivo específico, pero al construir el identificador URI, solo se usa la ruta de acceso base. Por ejemplo, al pasar `http://contoso.com/resources/azuredeploy.json` como parámetro baseUri, se obtiene como resultado un identificador URI base de `http://contoso.com/resources/`.

### <a name="return-value"></a>Valor devuelto

Una cadena que representa el identificador URI absoluto para los valores base y relativos.

### <a name="examples"></a>Ejemplos

En el ejemplo siguiente se muestra cómo construir un vínculo a una plantilla anidada en función del valor de la plantilla principal.

```json
"templateLink": "[uri(deployment().properties.templateLink.uri, 'nested/azuredeploy.json')]"
```

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) siguiente se muestra cómo usar uri, uriComponent y uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| uriOutput | Cadena | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Cadena | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Cadena | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponent"></a>uriComponent

`uricomponent(stringToEncode)`

Codifica un identificador URI.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| stringToEncode |Sí |string |El valor para codificar. |

### <a name="return-value"></a>Valor devuelto

Una cadena del valor codificado por el identificador URI.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) siguiente se muestra cómo usar uri, uriComponent y uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| uriOutput | Cadena | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Cadena | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Cadena | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="uricomponenttostring"></a>uriComponentToString

`uriComponentToString(uriEncodedString)`

Devuelve una cadena del valor codificado por el identificador URI.

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| uriEncodedString |Sí |string |El valor codificado por el identificador URI para convertir en una cadena. |

### <a name="return-value"></a>Valor devuelto

Una cadena descodificada del valor codificado por el identificador URI.

### <a name="examples"></a>Ejemplos

En la [plantilla de ejemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/functions/uri.json) siguiente se muestra cómo usar uri, uriComponent y uriComponentToString:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "variables": {
        "uriFormat": "[uri('http://contoso.com/resources/', 'nested/azuredeploy.json')]",
        "uriEncoded": "[uriComponent(variables('uriFormat'))]" 
    },
    "resources": [
    ],
    "outputs": {
        "uriOutput": {
            "type": "string",
            "value": "[variables('uriFormat')]"
        },
        "componentOutput": {
            "type": "string",
            "value": "[variables('uriEncoded')]"
        },
        "toStringOutput": {
            "type": "string",
            "value": "[uriComponentToString(variables('uriEncoded'))]"
        }
    }
}
```

La salida del ejemplo anterior con el valor predeterminado es:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| uriOutput | Cadena | http://contoso.com/resources/nested/azuredeploy.json |
| componentOutput | Cadena | http%3A%2F%2Fcontoso.com%2Fresources%2Fnested%2Fazuredeploy.json |
| toStringOutput | Cadena | http://contoso.com/resources/nested/azuredeploy.json |

## <a name="utcnow"></a>utcNow

`utcNow(format)`

Devuelve el valor de fecha y hora (UTC) actual en el formato especificado. Si no se proporciona ningún formato, se utiliza el formato ISO 8601 (yyyyMMddTHHmmssZ). **Esta función solo puede utilizarse en el valor predeterminado para un parámetro.**

### <a name="parameters"></a>Parámetros

| Parámetro | Obligatorio | type | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| format |Sin |string |El valor codificado por el identificador URI para convertir en una cadena. Use [cadenas de formato estándar](https://docs.microsoft.com/dotnet/standard/base-types/standard-date-and-time-format-strings) o [cadenas de formato personalizado](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### <a name="remarks"></a>Comentarios

Solo puede usar esta función dentro de una expresión para el valor predeterminado de un parámetro. El uso de esta función en cualquier otro lugar de una plantilla genera un error. La función no se permite en otras partes de la plantilla porque devuelve un valor diferente cada vez que se le llama. La implementación de la misma plantilla con los mismos parámetros no produciría de forma confiable los mismos resultados.

Si usa la [opción de volver a implementar una implementación que se completó correctamente en un momento anterior](resource-group-template-deploy-rest.md#redeploy-when-deployment-fails) y esa implementación anterior incluye un parámetro que usa utcNow, el parámetro no se vuelve a evaluar. En su lugar, el valor del parámetro de la implementación anterior se reutiliza automáticamente en la implementación de reversión.

Tenga cuidado al volver a implementar una plantilla que se base en la función utcNow para un valor predeterminado. Si vuelve a implementar y no proporciona un valor para el parámetro, la función se vuelve a evaluar. Si desea actualizar un recurso existente en lugar de crear uno nuevo, pase el valor de parámetro de la implementación anterior.

### <a name="return-value"></a>Valor devuelto

El valor de fecha y hora UTC actual.

### <a name="examples"></a>Ejemplos

La plantilla de ejemplo siguiente muestra formatos diferentes para el valor de fecha y hora.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcValue": {
            "type": "string",
            "defaultValue": "[utcNow()]"
        },
        "utcShortValue": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "utcCustomValue": {
            "type": "string",
            "defaultValue": "[utcNow('M d')]"
        }
    },
    "resources": [
    ],
    "outputs": {
        "utcOutput": {
            "type": "string",
            "value": "[parameters('utcValue')]"
        },
        "utcShortOutput": {
            "type": "string",
            "value": "[parameters('utcShortValue')]"
        },
        "utcCustomOutput": {
            "type": "string",
            "value": "[parameters('utcCustomValue')]"
        }
    }
}
```

El resultado del ejemplo anterior varía para cada implementación, pero será similar a:

| NOMBRE | type | Valor |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 05/03/2019 |
| utcCustomOutput | string | 3 5 |

El ejemplo siguiente muestra cómo usar un valor de la función cuando se establece un valor de etiqueta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "rgName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/resourceGroups",
            "apiVersion": "2018-05-01",
            "name": "[parameters('rgName')]",
            "location": "westeurope",
            "tags":{
                "createdDate": "[parameters('utcShort')]"
            },
            "properties":{}
        }
    ],
    "outputs": {
        "utcShort": {
            "type": "string",
            "value": "[parameters('utcShort')]"
        }
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una descripción de las secciones de una plantilla de Azure Resource Manager, vea [Creación de plantillas de Azure Resource Manager](resource-group-authoring-templates.md).
* Para combinar varias plantillas, vea [Uso de plantillas vinculadas con Azure Resource Manager](resource-group-linked-templates.md).
* Para iterar una cantidad de veces específica al crear un tipo de recurso, vea [Creación de varias instancias de recursos en el Administrador de recursos de Azure](resource-group-create-multiple.md).
* Para saber cómo implementar la plantilla que creó, consulte [Implementación de una aplicación con una plantilla de Azure Resource Manager](resource-group-template-deploy.md).

