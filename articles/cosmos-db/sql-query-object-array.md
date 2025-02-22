---
title: Trabajar con matrices y objetos en Azure Cosmos DB
description: Obtenga información sobre la sintaxis SQL de creación de matrices y objetos para Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 338f3b51edf38d20a963992e121b7e2dbd0c6873
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2019
ms.locfileid: "67343143"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Trabajar con matrices y objetos en Azure Cosmos DB

Una característica clave de la API de SQL de Azure Cosmos DB es la creación de matrices y objetos.

## <a name="arrays"></a>Matrices

Puede construir matrices como se muestra en los siguientes ejemplos:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

Los resultados son:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

También puede usar la [expresión ARRAY](sql-query-subquery.md#array-expression) para construir una matriz a partir de los resultados de la [subconsulta](sql-query-subquery.md). Esta consulta obtiene todos los nombres proporcionados distintos de los hijos en una matriz.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Iteración

La API de SQL proporciona compatibilidad con la iteración en las matrices JSON, donde se ha agregado una nueva construcción mediante la [palabra clave IN](sql-query-keywords.md#in) en el origen FROM. En el ejemplo siguiente:

```sql
    SELECT *
    FROM Families.children
```

Los resultados son:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

La siguiente consulta realiza la iteración en `children` en el contenedor `Families`. La matriz de salida es diferente de la consulta anterior. En este ejemplo se divide `children` y se reducen los resultados a una sola matriz:  

```sql
    SELECT *
    FROM c IN Families.children
```

Los resultados son:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

Puede filtrar aún más por cada entrada individual de la matriz, como se muestra en el ejemplo siguiente:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

Los resultados son:

```json
    [{
      "givenName": "Lisa"
    }]
```

También puede aplicar agregaciones al resultado de la iteración de una matriz. Por ejemplo, la consulta siguiente cuenta el número de hijos entre todas las familias:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

Los resultados son:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción](sql-query-getting-started.md)
- [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Combinaciones](sql-query-join.md)