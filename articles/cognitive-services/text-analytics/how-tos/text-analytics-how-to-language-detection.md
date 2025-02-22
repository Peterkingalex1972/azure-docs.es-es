---
title: Detección del idioma con la API REST de Text Analytics
titleSuffix: Azure Cognitive Services
description: Detecte el idioma con la API de REST de Text Analytics desde Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 07/30/2019
ms.author: aahi
ms.openlocfilehash: 361dc10aad6f65230e1e0f4c563534d44b45f902
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697805"
---
# <a name="example-detect-language-with-text-analytics"></a>Ejemplo: Detectar idioma con Text Analytics

La característica [Detección de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) de la API de REST de Azure Text Analytics valora la entrada de texto para cada documento y devuelve identificadores de idioma con una puntuación que indica la solidez del análisis.

Esta capacidad es útil para los almacenes de contenido que recopilan texto arbitrario, donde el idioma es desconocido. Puede analizar los resultados del análisis para determinar el idioma que se usa en el documento de entrada. La respuesta también devuelve una puntuación que refleja la confianza del modelo. El valor de puntuación se encuentra entre 0 y 1.

La característica Detección de idioma puede detectar una amplia gama de idiomas, variantes, dialectos y algunos idiomas regionales o culturales. La lista exacta de idiomas para esta característica no está publicada.

Si tiene contenido que se expresa en un idioma que se usa con menos frecuencia, puede probar la característica Detección de idioma para ver si devuelve un código. La respuesta para los idiomas que no se pueden detectar es `unknown`.

> [!TIP]
> Text Analytics proporciona también una imagen de contenedor de Docker basada en Linux para la detección del lenguaje, por lo que puede [instalar y ejecutar el contenedor de Text Analytics](text-analytics-how-to-install-containers.md) cerca de los datos.

## <a name="preparation"></a>Preparación

Debe tener documentos JSON en este formato: Identificador y texto.

El tamaño del documento debe ser inferior a 5.120 caracteres por documento. Puede tener hasta 1.000 elementos (identificadores) por colección. La colección se envía en el cuerpo de la solicitud. En el siguiente ejemplo se presenta contenido que se podría enviar para la detección del idioma:

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "This document is in English."
            },
            {
                "id": "2",
                "text": "Este documento está en inglés."
            },
            {
                "id": "3",
                "text": "Ce document est en anglais."
            },
            {
                "id": "4",
                "text": "本文件为英文"
            },
            {
                "id": "5",
                "text": "Этот документ на английском языке."
            }
        ]
    }
```

## <a name="step-1-structure-the-request"></a>Paso 1: Estructurar la solicitud

Para obtener más información sobre la definición de la solicitud, consulte [Llamada a la API de Text Analytics](text-analytics-how-to-call-api.md). Recapitulamos los siguientes puntos para su comodidad:

+ Cree una solicitud POST. Para revisar la documentación de la API para esta solicitud, consulte [API de Detección de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7).

+ Establezca el punto de conexión HTTP para la detección de idioma. Use un recurso de Text Analytics en Azure o un [contenedor de Text Analytics](text-analytics-how-to-install-containers.md) con instancias. Debe incluir el recurso `/languages`: `https://westus.api.cognitive.microsoft.com/text/analytics/v2.1/languages`.

+ Establezca un encabezado de solicitud para incluir la [clave de acceso](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) para las operaciones de Text Analytics.

+ En el cuerpo de la solicitud, proporcione la colección de documentos JSON que preparó para este análisis.

> [!Tip]
> Use [Postman](text-analytics-how-to-call-api.md) o abra la **consola de prueba de la API** en la [documentación](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) para estructurar y enviar una solicitud POST al servicio.

## <a name="step-2-post-the-request"></a>Paso 2: Publicar la solicitud

El análisis se realiza tras la recepción de la solicitud. Para obtener información sobre el tamaño y el número de solicitudes que puede enviar por minuto y segundo, consulte la sección de [límites de datos](../overview.md#data-limits) de la introducción.

Recuerde que el servicio no tiene estado. No se almacena ningún dato en su cuenta. Los resultados se devuelven inmediatamente en la respuesta.


## <a name="step-3-view-the-results"></a>Paso 3: View the results

Todas las solicitudes POST devolverán una respuesta con formato JSON con los identificadores y las propiedades detectadas.

La salida se devuelve inmediatamente. Puede transmitir los resultados a una aplicación que acepte JSON o guardar la salida en un archivo en el sistema local. A continuación, importe el resultado en una aplicación que puede usar para ordenar, buscar y manipular los datos.

Los resultados de la solicitud de ejemplo deben parecerse al siguiente JSON. Tenga en cuenta que es un documento con varios elementos. La salida está disponible en inglés. Los identificadores de idioma incluyen un nombre descriptivo y un código de idioma en formato [ISO 639-1](https://www.iso.org/standard/22109.html).

Una puntuación positiva de 1,0 expresa el nivel más alto de confianza posible del análisis.

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "Spanish",
                        "iso6391Name": "es",
                        "score": 1
                    }
                ]
            },
            {
                "id": "3",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            },
            {
                "id": "4",
                "detectedLanguages": [
                    {
                        "name": "Chinese_Simplified",
                        "iso6391Name": "zh_chs",
                        "score": 1
                    }
                ]
            },
            {
                "id": "5",
                "detectedLanguages": [
                    {
                        "name": "Russian",
                        "iso6391Name": "ru",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

### <a name="ambiguous-content"></a>Contenido ambiguo

En algunos casos, puede ser difícil eliminar la ambigüedad de los idiomas en función de la entrada. Puede usar el parámetro `countryHint` para especificar un código de país de dos letras. De forma predeterminada, la API usa "US" como valor de countryHint predeterminado; para quitar este comportamiento, puede restablecer este parámetro y configurarlo como una cadena `countryHint = ""` vacía.

Por ejemplo, "imposible" es igual en inglés que en francés, y si se proporciona con un contexto limitado, la respuesta se basará en la sugerencia de país "US". Si se sabe que el origen del texto procede de Francia, eso se puede proporcionar como sugerencia.

**Entrada**

```json
    {
        "documents": [
            {
                "id": "1",
                "text": "impossible"
            },
            {
                "id": "2",
                "text": "impossible",
                "countryHint": "fr"
            }
        ]
    }
```

El servicio tiene ahora contexto adicional para hacer un mejor juicio: 

**Salida**

```json
    {
        "documents": [
            {
                "id": "1",
                "detectedLanguages": [
                    {
                        "name": "English",
                        "iso6391Name": "en",
                        "score": 1
                    }
                ]
            },
            {
                "id": "2",
                "detectedLanguages": [
                    {
                        "name": "French",
                        "iso6391Name": "fr",
                        "score": 1
                    }
                ]
            }
        ],
        "errors": []
    }
```

Si el analizador no puede analizar la entrada, devuelve `(Unknown)`. Un ejemplo es si se envía un bloque de texto que consta únicamente de números arábigos.

```json
    {
        "id": "5",
        "detectedLanguages": [
            {
                "name": "(Unknown)",
                "iso6391Name": "(Unknown)",
                "score": "NaN"
            }
        ]
    }
```

### <a name="mixed-language-content"></a>Contenido en varios idiomas

El contenido en varios idiomas dentro del mismo documento devuelve el idioma con mayor representación en el contenido, pero con una clasificación positiva inferior. La clasificación refleja la fuerza marginal de la evaluación. En el ejemplo siguiente, la entrada es una combinación de inglés, español y francés. El analizador cuenta los caracteres de cada segmento para determinar el idioma predominante.

**Entrada**

```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
      ]
    }
```

**Salida**

La salida resultante está formada por el idioma predominante, con una puntuación de menos de 1,0, que indica un nivel de confianza más débil.

```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 0.9375
            }
          ]
        }
      ],
      "errors": []
    }
```

## <a name="summary"></a>Resumen

En este artículo, ha aprendido los conceptos y el flujo de trabajo de la detección del idioma con Text Analytics de Azure Cognitive Services. Se explicaron y mostraron los siguientes puntos:

+ [Detección de idioma](https://westcentralus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c7) está disponible para una amplia gama de idiomas, variantes, dialectos y algunos idiomas regionales o culturales.
+ Los documentos JSON del cuerpo de la solicitud incluyen un identificador y texto.
+ La solicitud POST se realiza a un punto de conexión `/languages`, con una [clave de acceso y un punto de conexión](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) personalizados, que son válidos para la suscripción.
+ La salida de respuesta consta de los identificadores de idioma para cada identificador de documento. La salida se puede transmitir a cualquier aplicación que acepte JSON. Las aplicaciones de ejemplo incluyen Excel y Power BI, por ejemplo.

## <a name="see-also"></a>Otras referencias

 [Introducción a Text Analytics](../overview.md) [Preguntas más frecuentes](../text-analytics-resource-faq.md)</br>
 [Página del producto de Text Analytics](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Análisis de opinión](text-analytics-how-to-sentiment-analysis.md)
