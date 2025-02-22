---
title: 'Introducción al lenguaje OData: Azure Search'
description: Introducción al lenguaje OData para las expresiones filter, select y order by en consultas de Azure Search.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 0bd446b0ffa97a758f68a0f85889b13da6e3d8b0
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650030"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-search"></a>Introducción al lenguaje OData para `$filter`, `$orderby` y `$select` en Azure Search

Azure Search admite un subconjunto de la sintaxis de expresiones de OData para las expresiones **$filter**, **$orderby** y **$select**. Las expresiones de filtro se evalúan durante el análisis de la consulta, restringiendo la búsqueda a campos específicos o agregando criterios de coincidencia que se usan durante los exámenes de índice. Las expresiones Order by se aplican como un paso posterior al procesamiento sobre un conjunto de resultados para ordenar los documentos que se devuelven. Las expresiones select determinan qué campos de documento se incluyen en el conjunto de resultados. La sintaxis de estas expresiones es distinta de la sintaxis de consulta [simple](query-simple-syntax.md) o [completa](query-lucene-syntax.md) que se usa en el parámetro **search**, si bien existe cierto solapamiento en la sintaxis de los campos de referencia.

En este artículo se proporciona información general sobre el lenguaje de expresiones OData usado en las expresiones filter, order by y select. El lenguaje se presenta en sentido ascendente, se comienza con los elementos más básicos y se construye sobre ellos. La sintaxis de nivel superior de cada parámetro se describe en otro artículo:

- [Sintaxis de $filter](search-query-odata-filter.md)
- [Sintaxis de $orderby](search-query-odata-orderby.md)
- [Sintaxis de $select](search-query-odata-select.md)

Las expresiones de OData pueden ser sencillas o muy complejas, pero todas comparten elementos comunes. Las partes más básicas de una expresión de OData en Azure Search son:

- **Rutas de acceso de campo**, que hacen referencia a campos específicos del índice.
- **Constantes**, que son valores literales de un tipo de datos determinado.

> [!NOTE]
> La terminología de Azure Search se diferencia del [estándar de OData](https://www.odata.org/documentation/) de varias maneras. Lo que llamamos un **campo** en Azure Search se denomina una **propiedad** en OData y lo mismo para **ruta de acceso de campo** frente a **ruta de acceso de propiedad**. Un **índice** que contiene **documentos** en Azure Search se conoce más generalmente en OData como un **conjunto de entidades** que contiene **entidades**. A lo largo de esta referencia se usará la terminología de Azure Search.

## <a name="field-paths"></a>Rutas de acceso de campo

La siguiente EBNF ([notación de Backus-Naur extendida](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define la gramática de las rutas de acceso de campo:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

También está disponible un diagrama de sintaxis interactivo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxis de OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Consulte [Referencia de sintaxis de expresiones OData para Azure Search](search-query-odata-syntax-reference.md) para obtener la EBNF completa.

Una ruta de acceso de campo se compone de uno o varios **identificadores** separados por barras diagonales. Cada identificador es una secuencia de caracteres que debe comenzar con una letra ASCII o un carácter de subrayado y contener solo letras ASCII, dígitos o caracteres de subrayado. Las letras pueden ser mayúsculas o minúsculas.

Un identificador puede hacer referencia al nombre de un campo o a una **variable de rango** en el contexto de una [expresión de colección](search-query-odata-collection-operators.md) (`any` o `all`) en un filtro. Una variable de rango es como una variable de bucle que representa el elemento actual de la colección. Si las colecciones son complejas, esa variable representa un objeto, que es el motivo de que pueda usar rutas de acceso de campo para hacer referencia a subcampos de la variable. Esta sintaxis es análoga a la notación de puntos de muchos lenguajes de programación.

En la tabla siguiente se muestran ejemplos de rutas de acceso de campo:

| Ruta de acceso de campo | DESCRIPCIÓN |
| --- | --- |
| `HotelName` | Hace referencia a un campo de nivel superior del índice. |
| `Address/City` | Hace referencia al subcampo `City` de un campo complejo en el índice; `Address` es de tipo `Edm.ComplexType` en este ejemplo. |
| `Rooms/Type` | Hace referencia al subcampo `Type` de un campo de colección complejo en el índice; `Rooms` es de tipo `Collection(Edm.ComplexType)` en este ejemplo. |
| `Stores/Address/Country` | Hace referencia al subcampo `Country` del subcampo `Address` de un campo de colección complejo en el índice; `Stores` es de tipo `Collection(Edm.ComplexType)` y `Address` es de tipo `Edm.ComplexType` en este ejemplo. |
| `room/Type` | Hace referencia al subcampo `Type` de la variable de rango `room`, por ejemplo, en la expresión de filtro `Rooms/any(room: room/Type eq 'deluxe')`. |
| `store/Address/Country` | Hace referencia al subcampo `Country` del subcampo `Address` de la variable de rango `store`, por ejemplo, en la expresión de filtro `Stores/any(store: store/Address/Country eq 'Canada')`. |

El significado de una ruta de acceso de campo varía según el contexto. En los filtros, una ruta de acceso de campo hace referencia al valor de una *única instancia* de un campo en el documento actual. En otros contextos, como **$orderby**, **$select** o en la [búsqueda clasificada por campos de la sintaxis completa de Lucene](query-lucene-syntax.md#bkmk_fields), una ruta de acceso de campo hace referencia al campo propiamente dicho. Esta diferencia tiene algunas consecuencias en cómo se usan las rutas de acceso de campo en los filtros.

Considere la ruta de acceso de campo `Address/City`. En un filtro, esta ruta hacer referencia a una única ciudad del documento actual, como "San Francisco". En cambio, `Rooms/Type` hace referencia al subcampo `Type` para muchas habitaciones (por ejemplo, "standard" para la primera habitación, "deluxe" para la segunda, etc.). Puesto que `Rooms/Type` no hace referencia a una *única instancia* del subcampo `Type`, no se puede usar directamente en un filtro. Así que, para filtrar por el tipo de habitación, usaría una [expresión lambda](search-query-odata-collection-operators.md) con una variable de rango, como esta:

    Rooms/any(room: room/Type eq 'deluxe')

En este ejemplo, la variable de rango `room` aparece en la ruta de acceso de campo `room/Type`. De este modo, `room/Type` hace referencia al tipo de habitación actual en el documento actual. Como es una única instancia del subcampo `Type`, se puede usar directamente en el filtro.

### <a name="using-field-paths"></a>Uso de rutas de acceso de campo

Las rutas de acceso de campo se usan en numerosos parámetros de [Azure Search API](https://docs.microsoft.com/rest/api/searchservice/). En la tabla siguiente se enumeran todos los lugares donde pueden usarse, además de las restricciones sobre su uso:

| API | Nombre de parámetro | Restricciones |
| --- | --- | --- |
| [Create](https://docs.microsoft.com/rest/api/searchservice/create-index) or [Update](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `suggesters/sourceFields` | None |
| [Create](https://docs.microsoft.com/rest/api/searchservice/create-index) or [Update](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `scoringProfiles/text/weights` | Solo puede hacer referencia a campos que **permiten realizar búsquedas**. |
| [Create](https://docs.microsoft.com/rest/api/searchservice/create-index) or [Update](https://docs.microsoft.com/rest/api/searchservice/update-index) Index | `scoringProfiles/functions/fieldName` | Solo puede hacer referencia a campos **filtrables**. |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search` cuando `queryType` es `full` | Solo puede hacer referencia a campos que **permiten realizar búsquedas**. |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Solo puede hacer referencia a campos **clasificables** |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Solo puede hacer referencia a campos que **permiten realizar búsquedas**. |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Solo puede hacer referencia a campos que **permiten realizar búsquedas**. |
| [Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) y [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Solo puede hacer referencia a campos que forman parte de un [proveedor de sugerencias](index-add-suggesters.md). |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) y [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Solo puede hacer referencia a campos **filtrables**. |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents) y [Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Solo puede hacer referencia a campos **ordenables**. |
| [Search](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Suggest](https://docs.microsoft.com/rest/api/searchservice/suggestions) y [Lookup](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Solo puede hacer referencia a campos **recuperables** |

## <a name="constants"></a>Constantes

Las constantes en OData son valores literales de un determinado tipo de [Entity Data Model](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM). Consulte [Tipos de datos admitidos](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) para ver una lista de tipos admitidos en Azure Search. No se admiten constantes de tipos de colección.

En la tabla siguiente se muestran ejemplos de constantes para cada uno de los tipos de datos admitidos por Azure Search:

| Tipo de datos | Constantes de ejemplo |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

El siguiente EBNF ([notación de Backus-Naur extendida](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define la gramática de la mayoría de las constantes que se muestran en la tabla anterior. La gramática para los tipos geoespaciales se puede encontrar en [OData geo-spatial functions in Azure Search](search-query-odata-geo-spatial-functions.md) (Funciones geoespaciales de OData en Azure Search).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

También está disponible un diagrama de sintaxis interactivo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxis de OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Consulte [Referencia de sintaxis de expresiones OData para Azure Search](search-query-odata-syntax-reference.md) para obtener la EBNF completa.

## <a name="building-expressions-from-field-paths-and-constants"></a>Generación de expresiones a partir de rutas de acceso de campo y constantes

Las rutas de acceso de campo y las constantes son la parte más básica de una expresión de OData, pero ya son expresiones completas por derecho propio. De hecho, el parámetro **$select** de Azure Search no es más que una lista separada por comas de las rutas de acceso de campo y **$orderby** no es mucho más complicado que **$select**. Si resulta que tiene un campo de tipo `Edm.Boolean` en el índice, puede escribir incluso un filtro que no sea si no la ruta de acceso de ese campo. Las constantes `true` y `false` son igualmente filtros válidos.

Sin embargo, casi siempre necesitará expresiones más complejas que hagan referencia a más de un campo y más de una constante. Estas expresiones se compilan de diferentes maneras según el parámetro.

La siguiente EBNF ([notación de Backus-Naur extendida](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) define la gramática de los parámetros **$filter**, **$orderby** y **$select**. Estos parámetros se generan a partir de expresiones más sencillas que hacen referencia a las rutas de acceso de campo y a las constantes:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

También está disponible un diagrama de sintaxis interactivo:

> [!div class="nextstepaction"]
> [Diagrama de sintaxis de OData para Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Consulte [Referencia de sintaxis de expresiones OData para Azure Search](search-query-odata-syntax-reference.md) para obtener la EBNF completa.

Los parámetros **$orderby** y **$select** son ambos listas separadas por comas de expresiones más sencillas. El parámetro **$filter** es una expresión booleana que se compone de subexpresiones más sencillas. Estas subexpresiones se combinan mediante operadores lógicos, como [ `and`, `or`, y `not` ](search-query-odata-logical-operators.md), operadores de comparación, como [`eq`, `lt`, `gt`, etc.](search-query-odata-comparison-operators.md) y operadores de colección, como [`any` y `all`](search-query-odata-collection-operators.md).

Los parámetros **$filter**, **$orderby**, y **$select** se exploran con más detalle en los siguientes artículos:

- [Sintaxis de $filter de OData en Azure Search](search-query-odata-filter.md)
- [Sintaxis de $orderby de OData en Azure Search](search-query-odata-orderby.md)
- [Sintaxis de $select de OData en Azure Search](search-query-odata-select.md)

## <a name="see-also"></a>Otras referencias  

- [Navegación por facetas en Azure Search](search-faceted-navigation.md)
- [Filtros de Azure Search](search-filters.md)
- [Search Documents &#40;Azure Search Service REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) (Búsqueda en documentos [API REST de Azure Search Service])
- [Sintaxis de consulta de Lucene](query-lucene-syntax.md)
- [Simple query syntax in Azure Search](query-simple-syntax.md) (Sintaxis de consulta simple en Azure Search)
