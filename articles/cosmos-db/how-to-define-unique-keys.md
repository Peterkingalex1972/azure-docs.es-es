---
title: Definición de claves únicas para un contenedor de Azure Cosmos
description: Aprenda a definir claves únicas para un contenedor de Azure Cosmos.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: thweiss
ms.openlocfilehash: 67b6ca1914d1728930c29c4a0bbca6cf86753da9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093320"
---
# <a name="define-unique-keys-for-an-azure-cosmos-container"></a>Definición de claves únicas para un contenedor de Azure Cosmos

En este artículo se presentan las distintas formas de definir [claves únicas](unique-keys.md) al crear un contenedor de Azure Cosmos. Actualmente, es posible realizar esta operación mediante Azure Portal o uno de los SDK.

## <a name="use-the-azure-portal"></a>Uso de Azure Portal

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).

1. [Cree una cuenta de Azure Cosmos](create-sql-api-dotnet.md#create-account) o seleccione una ya existente.

1. Abra el panel **Data Explorer** y seleccione el contenedor en el que quiere trabajar.

1. Haga clic en **Nuevo contenedor**.

1. En el cuadro de diálogo **Agregar contenedor**, haga clic en **+ Add unique key** (+ Agregar clave única) para agregar una única entrada de clave.

1. Escriba la ruta o rutas de la restricción de clave única

1. Si es necesario, puede agregar más entradas de clave única si hace clic en **+ Add unique key** (+ Agregar clave única).

![Captura de pantalla de una entrada de restricción de clave única en Azure Portal](./media/how-to-define-unique-keys/unique-keys-portal.png)

## <a name="use-the-net-sdk-v2"></a>Uso de .NET SDK V2

Al crear un contenedor mediante [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/), se puede usar un objeto `UniqueKeyPolicy` para definir las restricciones de clave única.

```csharp
client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("database"), new DocumentCollection
{
    Id = "container",
    UniqueKeyPolicy = new UniqueKeyPolicy
    {
        UniqueKeys = new Collection<UniqueKey>(new List<UniqueKey>
        {
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/firstName", "/lastName", "emailAddress" }) },
            new UniqueKey { Paths = new Collection<string>(new List<string> { "/address/zipCode" }) }
        })
    }
});
```

## <a name="use-the-java-sdk"></a>Uso del SDK de Java

Al crear un contenedor mediante el [SDK de Java](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb), se puede usar un objeto `UniqueKeyPolicy` para definir las restricciones de clave única.

```java
// create a new DocumentCollection object
DocumentCollection container = new DocumentCollection();
container.setId("container");
// create array of strings and populate them with the unique key paths
Collection<String> uniqueKey1Paths = new ArrayList<String>();
uniqueKey1Paths.add("/firstName");
uniqueKey1Paths.add("/lastName");
uniqueKey1Paths.add("/emailAddress");
Collection<String> uniqueKey2Paths = new ArrayList<String>();
uniqueKey2Paths.add("/address/zipCode");
// create UniqueKey objects and set their paths
UniqueKey uniqueKey1 = new UniqueKey();
UniqueKey uniqueKey2 = new UniqueKey();
uniqueKey1.setPaths(uniqueKey1Paths);
uniqueKey2.setPaths(uniqueKey2Paths);
// create a new UniqueKeyPolicy object and set its unique keys
UniqueKeyPolicy uniqueKeyPolicy = new UniqueKeyPolicy();
Collection<UniqueKey> uniqueKeys = new ArrayList<UniqueKey>();
uniqueKeys.add(uniqueKey1);
uniqueKeys.add(uniqueKey2);
uniqueKeyPolicy.setUniqueKeys(uniqueKeys);
// set the unique key policy
container.setUniqueKeyPolicy(uniqueKeyPolicy);
// create the container
client.createCollection(String.format("/dbs/%s", "database"), container, null);
```

## <a name="use-the-nodejs-sdk"></a>Uso del SDK de Node.js

Al crear un contenedor mediante el [SDK de Node.js](https://www.npmjs.com/package/@azure/cosmos), se puede usar un objeto `UniqueKeyPolicy` para definir las restricciones de clave única.

```javascript
client.database('database').containers.create({
    id: 'container',
    uniqueKeyPolicy: {
        uniqueKeys: [
            { paths: ['/firstName', '/lastName', '/emailAddress'] },
            { paths: ['/address/zipCode'] }
        ]
    }
});
```

## <a name="use-the-python-sdk"></a>Uso del SDK de Python

Al crear un nuevo contenedor mediante el [SDK de Python](https://pypi.org/project/azure-cosmos/), se pueden especificar restricciones de clave única como parte del diccionario pasado como parámetro.

```python
client.CreateContainer('dbs/' + config['DATABASE'], {
    'id': 'container',
    'uniqueKeyPolicy': {
        'uniqueKeys': [
            {'paths': ['/firstName', '/lastName', '/emailAddress']},
            {'paths': ['/address/zipCode']}
        ]
    }
})
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [creación de particiones](partition-data.md).
- Explore [cómo funciona la indexación](index-overview.md).