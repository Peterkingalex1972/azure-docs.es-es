---
title: Constantes de SQL en Azure Cosmos DB
description: Obtenga información sobre las constantes de SQL en Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: ed337c8330eb8b3e4ad97c92cca1d0c5977f9588
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343196"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Constantes de consulta SQL de Azure Cosmos DB  

 Una constante, también conocida como valor literal o escalar, es un símbolo que representa un valor de datos específicos. El formato de una constante depende del tipo de datos del valor que representa.  
  
 **Tipos de datos escalares admitidos:**  
  
|**Tipo**|**Orden de los valores**|  
|-|-|  
|**Undefined**|Valor único: **undefined**|  
|**Null**|Valor único: **null**|  
|**Boolean**|Valores: **false**, **true**.|  
|**Number**|Número de punto flotante de precisión doble, estándar IEEE 754.|  
|**String**|Secuencia de cero o más caracteres Unicode. Las cadenas deben ir entre comillas sencillas o dobles.|  
|**Array**|Secuencia de cero o más elementos. Cada elemento puede ser un valor de cualquier tipo de datos escalar, excepto **Undefined**.|  
|**Object**|Conjunto desordenado de cero o más pares nombre/valor. Nombre es una cadena Unicode, el valor puede ser de cualquier tipo de datos escalar, excepto **Undefined**.|  
  
## <a name="bk_syntax"></a>Sintaxis
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="bk_arguments"></a> Argumentos
  
* `<undefined_constant>; Undefined`  
  
  Representa un valor sin definir de tipo Undefined.  
  
* `<null_constant>; null`  
  
  Representa el valor **null** de tipo **Null**.  
  
* `<boolean_constant>`  
  
  Representa la constante de tipo booleano.  
  
* `false`  
  
  Representa el valor **false** de tipo booleano.  
  
* `true`  
  
  Representa el valor **true** de tipo booleano.  
  
* `<number_constant>`  
  
  Representa una constante.  
  
* `decimal_literal`  
  
  Los literales decimales son números representados mediante notación decimal o científica.  
  
* `hexadecimal_literal`  
  
  Los literales hexadecimales son números representados mediante el prefijo "0x-" seguido de uno o más dígitos hexadecimales.  
  
* `<string_constant>`  
  
  Representa una constante de tipo cadena.  
  
* `string _literal`  
  
  Los literales de cadena son cadenas Unicode representadas por una secuencia de cero o varios caracteres Unicode o secuencias de escape. Los literales de cadena se encierran entre comillas simples (apóstrofo: ') o comillas dobles (comillas: ").  
  
  Se permiten las secuencias de escape siguientes:  
  
|**Secuencia de escape**|**Descripción**|**Carácter Unicode**|  
|-|-|-|  
|\\'|apóstrofo (')|U + 0027|  
|\\"|comillas dobles (")|U + 0022|  
|\\\ |barra inclinada inversa (\\)|U + 005C|  
|\\/|barra inclinada (/)|U + 002F|  
|\b|retroceso|U + 0008|  
|\f|avance de página|U + 000C|  
|\n|avance de línea|U + 000A|  
|\r|retorno de carro|U + 000D|  
|\t|tabulador|U + 0009|  
|\uXXXX|Carácter Unicode definidos por 4 dígitos hexadecimales.|U + XXXX|  

## <a name="next-steps"></a>Pasos siguientes

- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Modelado de datos de documentos](modeling-data.md)
