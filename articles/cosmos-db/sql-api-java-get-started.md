---
title: 'Tutorial de NoSQL: API de SQL para el SDK de Java para Azure Cosmos DB'
description: Un tutorial de NoSQL que crea una base de datos en línea y la aplicación de consola de Java mediante la API de SQL para Azure Cosmos DB. Azure SQL es una base de datos NoSQL para JSON.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: tutorial
ms.date: 12/22/2018
ms.author: sngun
ms.openlocfilehash: 269b2bd1c1457b9e70ea4ada758aacb2f2ae96f3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69614642"
---
# <a name="nosql-tutorial-build-a-sql-api-java-console-application"></a>Tutorial de NoSQL: Compilación de una aplicación de consola de Java para la API de SQL

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Le damos la bienvenida al tutorial de NoSQL sobre la API de SQL para el SDK de Java para Azure Cosmos DB. Después de seguir este tutorial, tendrá una aplicación de consola que crea recursos de Azure Cosmos DB y los consulta.

Trataremos los siguientes temas:

* Creación de una cuenta de Azure Cosmos DB y conexión a ella
* Configuración de la solución de Visual Studio
* Creación de una base de datos en línea
* Creación de una colección
* Creación de documentos JSON
* Consulta de la colección
* Creación de documentos JSON
* Consulta de la colección
* Sustitución de un documento
* Eliminación de un documento
* Eliminación de la base de datos

Comencemos.

## <a name="prerequisites"></a>Requisitos previos
Asegúrese de que dispone de lo siguiente:

* Una cuenta de Azure activa. Si no tiene una, puede registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/). 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Git](https://git-scm.com/downloads).
* [Kit de desarrollo de Java (JDK) 7+](https://aka.ms/azure-jdks).
* [Maven](https://maven.apache.org/download.cgi).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Paso 1: Creación de una cuenta de Azure Cosmos DB
Vamos a crear una cuenta de Azure Cosmos DB. Si ya tiene una cuenta que desea usar, puede ir directamente a [Clonar el proyecto de GitHub](#GitClone). Si está usando el Emulador de Azure Cosmos DB, siga los pasos que se indican en el [Emulador de Azure Cosmos DB](local-emulator.md) para configurar el emulador y vaya directamente a [Clonar el proyecto de GitHub](#GitClone).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="GitClone"></a>Paso 2: Clonar el proyecto de GitHub
En primer lugar, clone el repositorio de GitHub para [empezar a trabajar con Azure Cosmos DB y Java](https://github.com/Azure-Samples/documentdb-java-getting-started). Por ejemplo, desde un directorio local, ejecute lo siguiente para recuperar el proyecto de ejemplo localmente.

    git clone git@github.com:Azure-Samples/azure-cosmos-db-documentdb-java-getting-started.git

    cd azure-cosmos-db-documentdb-java-getting-started

El directorio contiene un archivo `pom.xml` para el proyecto y una carpeta `src` que contiene el código fuente de Java, incluido `Program.java`, que muestra cómo realizar operaciones sencillas con Azure Cosmos DB, como crear documentos y consultar datos dentro de una colección. El archivo `pom.xml` incluye una dependencia en el [SDK de Java para Azure Cosmos DB en Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-documentdb).

    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-documentdb</artifactId>
        <version>LATEST</version>
    </dependency>

## <a id="Connect"></a>Paso 3: Conexión a una cuenta de Azure Cosmos DB
A continuación, vuelva a [Azure Portal](https://portal.azure.com) para recuperar el punto de conexión y la clave maestra principal. El punto de conexión y la clave principal de Azure Cosmos DB son necesarios para que la aplicación sepa a dónde debe conectarse y para que Azure Cosmos DB confíe en la conexión de la aplicación.

En Azure Portal, vaya a la cuenta de Azure Cosmos DB y haga clic en **Claves**. Copie el URI desde el portal y péguelo en `https://FILLME.documents.azure.com`, en el archivo Program.java. Después, copie la CLAVE PRINCIPAL del Portal y péguela en `FILLME`.

    this.client = new DocumentClient(
        "https://FILLME.documents.azure.com",
        "FILLME"
        , new ConnectionPolicy(),
        ConsistencyLevel.Session);

![Captura de pantalla de Azure Portal usado por el tutorial de NoSQL para crear una aplicación de consola de Java. Muestra una cuenta de Azure Cosmos DB, con el centro ACTIVO resaltado, el botón CLAVES resaltado en la hoja de la cuenta de Azure Cosmos DB y los valores de URI, CLAVE PRINCIPAL y CLAVE SECUNDARIA resaltados en la hoja Claves][keys]

## <a name="step-4-create-a-database"></a>Paso 4: Creación de una base de datos
Para crear una [base de datos](databases-containers-items.md#azure-cosmos-databases) de Azure Cosmos DB, puede usar el método [createDatabase](/java/api/com.microsoft.azure.documentdb.documentclient.createdatabase) de la clase **DocumentClient**. Una base de datos es un contenedor lógico de almacenamiento de documentos JSON particionado en recopilaciones.

    Database database = new Database();
    database.setId("familydb");
    this.client.createDatabase(database, null);

## <a id="CreateColl"></a>Paso 5: Creación de una colección
> [!WARNING]
> **createCollection** creará una colección con un rendimiento reservado, lo que tiene implicaciones en los precios. Para más información, visite nuestra [página de precios](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 
> 

Para crear una colección, puede usar el método [createCollection](/java/api/com.microsoft.azure.documentdb.documentclient.createcollection) de la clase **DocumentClient**. Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.


    DocumentCollection collectionInfo = new DocumentCollection();
    collectionInfo.setId("familycoll");

    // Azure Cosmos containers can be reserved with throughput specified in request units/second. 
    // Here we create a collection with 400 RU/s.
    RequestOptions requestOptions = new RequestOptions();
    requestOptions.setOfferThroughput(400);

    this.client.createCollection("/dbs/familydb", collectionInfo, requestOptions);

## <a id="CreateDoc"></a>Paso 6: Creación de documentos JSON
Para crear un documento, puede usar el método [createDocument](/java/api/com.microsoft.azure.documentdb.documentclient.createdocument) de la clase **DocumentClient**. Los documentos son contenido JSON definido por el usuario (arbitrario). Ahora podemos insertar uno o varios documentos. Si ya dispone de datos que desea almacenar en la base de datos, puede usar la [herramienta de migración de datos](import-data.md) de Azure Cosmos DB para importar los datos en una base de datos.

    // Insert your Java objects as documents 
    Family andersenFamily = new Family();
    andersenFamily.setId("Andersen.1");
    andersenFamily.setLastName("Andersen")

    // More initialization skipped for brevity. You can have nested references
    andersenFamily.setParents(new Parent[] { parent1, parent2 });
    andersenFamily.setDistrict("WA5");
    Address address = new Address();
    address.setCity("Seattle");
    address.setCounty("King");
    address.setState("WA");

    andersenFamily.setAddress(address);
    andersenFamily.setRegistered(true);

    this.client.createDocument("/dbs/familydb/colls/familycoll", family, new RequestOptions(), true);

![Diagrama que muestra la relación jerárquica entre la cuenta, la base de datos en línea, la colección y los documentos usados por el tutorial de NoSQL para crear una aplicación de consola de Java.](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Paso 7: Consulta de los recursos de Azure Cosmos DB
Azure Cosmos DB admite [consultas](how-to-sql-query.md) enriquecidas en los documentos JSON que se almacenan en las colecciones.  El código de ejemplo siguiente muestra cómo consultar documentos de Azure Cosmos DB mediante la sintaxis SQL con el método [queryDocuments](/java/api/com.microsoft.azure.documentdb.documentclient.querydocuments).

    FeedResponse<Document> queryResults = this.client.queryDocuments(
        "/dbs/familydb/colls/familycoll",
        "SELECT * FROM Family WHERE Family.lastName = 'Andersen'", 
        null);

    System.out.println("Running SQL query...");
    for (Document family : queryResults.getQueryIterable()) {
        System.out.println(String.format("\tRead %s", family));
    }

## <a id="ReplaceDocument"></a>Paso 8: Reemplazo de un documento JSON
Azure Cosmos DB permite actualizar documentos JSON con el método [replaceDocument](/java/api/com.microsoft.azure.documentdb.documentclient.replacedocument).

    // Update a property
    andersenFamily.Children[0].Grade = 6;

    this.client.replaceDocument(
        "/dbs/familydb/colls/familycoll/docs/Andersen.1", 
        andersenFamily,
        null);

## <a id="DeleteDocument"></a>Paso 9: Eliminación de un documento JSON
Análogamente, Azure Cosmos DB permite eliminar documentos JSON con el método [deleteDocument](/java/api/com.microsoft.azure.documentdb.documentclient.deletedocument).  

    this.client.delete("/dbs/familydb/colls/familycoll/docs/Andersen.1", null);

## <a id="DeleteDatabase"></a>Paso 10: Eliminación de la base de datos
La eliminación de la base de datos creada quitará la base de datos y todos los recursos secundarios (colecciones, documentos, etc.).

    this.client.deleteDatabase("/dbs/familydb", null);

## <a id="Run"></a>Paso 11: Ejecución de la aplicación de consola de Java
Para ejecutar la aplicación desde la consola, navegue hasta la carpeta del proyecto y realice la compilación con Maven:
    
    mvn package

La ejecución de `mvn package` descarga la biblioteca de Azure Cosmos DB más reciente de Maven y genera `GetStarted-0.0.1-SNAPSHOT.jar`. A continuación, ejecute la aplicación con:

    mvn exec:java -D exec.mainClass=GetStarted.Program

Felicidades. Ha completado este tutorial de NoSQL y tiene una aplicación de consola de Java en funcionamiento.

## <a name="next-steps"></a>Pasos siguientes
* ¿Desea un tutorial de las aplicaciones web de Java? Consulte [Creación de una aplicación web de Java mediante Azure Cosmos DB](sql-api-java-application.md).
* Información acerca de cómo [supervisar una cuenta de Azure Cosmos DB](monitor-accounts.md).
* Ejecute las consultas en nuestro conjunto de datos de ejemplo en el [área de consultas](https://www.documentdb.com/sql/demo).

[keys]: media/sql-api-get-started/nosql-tutorial-keys.png
