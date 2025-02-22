---
title: 'Aptitud de búsqueda cognitiva Conformador: Azure Search'
description: Extraiga metadatos e información estructurada a partir de datos no estructurados y confórmelos como un tipo complejo en una canalización de enriquecimiento de Azure Search.
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: c67080cf5ffb06cd0b0ee45ea673bc45557570b0
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69638900"
---
#   <a name="shaper-cognitive-skill"></a>Habilidad cognitiva Conformador

La aptitud **Conformador** consolida varias entradas en un [tipo complejo](search-howto-complex-data-types.md) al que se puede hacer referencia más adelante en la canalización de enriquecimiento. La aptitud **Conformador** básicamente permite crear una estructura, definir el nombre de los miembros de esa estructura y asignar valores a cada miembro. Los ejemplos de campos consolidados útiles en escenarios de búsqueda incluyen la combinación de un nombre y apellido, de una ciudad y un estado o de un nombre y una fecha de nacimiento en una sola estructura para establecer una identidad exclusiva.

Además, la habilidad **Conformador** ilustrada en el [escenario 3](#nested-complex-types) agrega una propiedad opcional *sourceContext* a la entrada. Las propiedades *source* y *sourceContext* son mutuamente excluyentes. Si la entrada está en el contexto de la aptitud, simplemente use *source*. Si la entrada está en un contexto *diferente* al de la aptitud, use *sourceContext*. *sourceContext* exige definir una entrada anidada con el elemento específico considerado como origen. 

El nombre de la salida es siempre "salida". Internamente, la canalización puede asignar otro nombre, como "analyzedText", como se muestra en los ejemplos siguientes, pero la aptitud **Conformador** devuelve "output" en la respuesta. Esto podría ser importante si está depurando documentos enriquecidos y observa la discrepancia de nombre, o si compila una habilidad personalizada y estructura la respuesta por sí mismo.

> [!NOTE]
> La aptitud **Conformador** no está enlazada a una API de Cognitive Services y no se le cobra por usarla. Sin embargo, debe [adjuntar un recurso de Cognitive Services](cognitive-search-attach-cognitive-services.md) para invalidar la opción del recurso **Gratis**, que tiene un límite de unos pocos enriquecimientos al día.

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>Escenario 1: tipos complejos

Piense en un escenario en el que quiere crear una estructura denominada *analyzedText* con dos miembros: *text* y *sentiment*, respectivamente. En un índice de Azure Search, un campo de búsqueda de varias partes se denomina *tipo complejo* y se suele crear cuando el origen de datos tiene una estructura compleja correspondiente que se asigna a él.

Pero otro método para crear tipos complejos es mediante la aptitud **Conformador**. Al incluir esta aptitud en un conjunto de aptitudes, las operaciones en memoria durante el procesamiento del conjunto de aptitudes pueden generar formas de datos con estructuras anidadas, que luego pueden asignarse a un tipo complejo del índice. 

En el ejemplo siguiente, la definición de aptitud proporciona los nombres de miembro como entrada. 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>Índice de ejemplo

Un indizador invoca a un conjunto de aptitudes, y un indizador requiere un índice. Una representación de campo complejo del índice podría ser similar al ejemplo siguiente. 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>Entrada de aptitud

Un documento JSON entrante que proporcionara una entrada útil para esta aptitud **Conformador** podría ser:

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>Salida de aptitud

La aptitud *Conformador* genera un nuevo elemento llamado **analyzedText** con los elementos combinados de *text* y *sentiment*. Esta salida se ajusta al esquema de índice. Se va a importar e indexar en un índice de Azure Search.

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>Escenario 2: consolidación de entrada

En otro ejemplo, imagine que en distintas fases del procesamiento de la canalización ha extraído el título de un libro y los títulos de capítulos de distintas páginas del libro. Ahora podría crear una única estructura compuesta por estas distintas entradas.

La definición de aptitud **Conformador** de este escenario podría ser similar al ejemplo siguiente:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Salida de aptitud
En este caso, **Conformador** acopla todos los títulos de capítulos para crear una matriz única. 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>Escenario 3: consolidación de entrada de contextos anidados

Imagine que tiene el título, los capítulos y el contenido de un libro y ha ejecutado el reconocimiento de entidades y frases clave en el contenido; ahora debe agregar los resultados de las distintas aptitudes en una sola forma con el nombre del capítulo, las entidades y las frases clave.

La definición de aptitud **Conformador** de este escenario podría ser similar al ejemplo siguiente:

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>Salida de aptitud
En este caso, el **Conformador** crea un tipo complejo. Esta estructura existe en memoria. Si quiere guardarla en un [almacén de conocimiento](knowledge-store-concept-intro.md), tiene que crear una proyección en el conjunto de aptitudes que defina las características de almacenamiento.

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>Otras referencias

+ [Aptitudes predefinidas](cognitive-search-predefined-skills.md)
+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Cómo usar tipos complejos](search-howto-complex-data-types.md)
+ [Información general de Knowledge Store](knowledge-store-concept-intro.md)
+ [Introducción a Knowledge Store](knowledge-store-howto.md)