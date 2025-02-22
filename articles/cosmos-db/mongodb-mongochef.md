---
title: Conexión a la API de Azure Cosmos DB para MongoDB mediante Studio 3T
titleSuffix: Azure Cosmos DB
description: Aprenda a conectarse a Azure Cosmos DB mediante Studio 3T y la API de Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.custom: seodec18
ms.openlocfilehash: 9b0a6393d2372c831fdc964dee18acbccfd39a77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61332044"
---
# <a name="connect-to-cosmos-account-using-studio-3t"></a>Conexión a una cuenta de Cosmos con Studio 3T

Para conectarse a una cuenta de Cosmos mediante la API de Azure Cosmos DB para MongoDB, debe hacer lo siguiente:

* Descargar e instalar [Studio 3T](https://studio3t.com/)
* Tener información de la [cadena de conexión](connect-mongodb-account.md) de Cosmos DB

## <a name="create-the-connection-in-studio-3t"></a>Creación de la conexión en Studio 3T
Para agregar la cuenta de Cosmos DB al administrador de conexiones de Studio 3T, siga estos pasos:

1. Recupere la información de conexión de la cuenta de Cosmos configurada con la API de Azure Cosmos DB para MongoDB mediante las instrucciones del artículo [Conectar una aplicación de MongoDB a Azure Cosmos DB](connect-mongodb-account.md).

    ![Captura de pantalla de la página Cadena de conexión.](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. Haga clic en **Connect** (Conectar) para abrir Connection Manager (Administrador de conexiones) y, después, haga clic en **New Connection** (Nueva conexión).

    ![Captura de pantalla del administrador de conexiones de Studio 3T.](./media/mongodb-mongochef/ConnectionManager.png)
3. En la ventana **New Connection** (Nueva conexión), en la pestaña **Server** (Servidor), escriba el HOST (FQDN) de la cuenta de Azure Cosmos DB y el PUERTO.

    ![Captura de pantalla de la pestaña de servidor del administrador de conexiones de Studio 3T.](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. En la ventana **New Connection** (Nueva conexión), en la pestaña **Authentication** (Autenticación), elija el modo de autenticación **Basic (MONGODB-CR or SCARM-SHA-1)** [Básico (MONGODB-CR o SCARM-SHA-1)] y escriba el NOMBRE DE USUARIO y la CONTRASEÑA.  Acepte la base de datos de autenticación predeterminada (admin) o proporcione su propio valor.

    ![Captura de pantalla de la pestaña de autenticación del administrador de conexiones de Studio 3T.](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. En la ventana **New Connection** (Nueva conexión), en la pestaña **SSL**, active la casilla **Use SSL protocol to connect** (Usar protocolo SSL para conectar) y el botón de radio **Accept server self-signed SSL certificates** (Aceptar certificados SSL autofirmados del servidor).

    ![Captura de pantalla de la pestaña SSL del administrador de conexiones de Studio 3T.](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. Haga clic en el botón **Test Connection** (Probar conexión) para validar la información de conexión, haga clic en **OK** (Aceptar) para volver a la ventana de la nueva conexión y, finalmente, haga clic en **Save** (Guardar).

    ![Captura de pantalla de la ventana de conexión de prueba de Studio 3T.](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>Uso de Studio 3T para crear una base de datos, una colección y unos documentos
Para crear una base de datos, una colección y unos documentos con Studio 3T, realice los pasos siguientes:

1. En **Connection Manager** (Administrador de conexiones), resalte la conexión y haga clic en **Connect** (Conectar).

    ![Captura de pantalla del administrador de conexiones de Studio 3T.](./media/mongodb-mongochef/ConnectToAccount.png)
2. Haga clic con el botón derecho en el host y elija **Add Database** (Agregar base de datos).  Especifique un nombre de base de datos y haga clic en **OK**(Aceptar).

    ![Captura de pantalla de la opción Agregar base de datos de Studio 3T.](./media/mongodb-mongochef/AddDatabase1.png)
3. Haga clic con el botón derecho en la base de datos y elija **Add Collection** (Agregar colección).  Especifique un nombre para la colección y haga clic en **Create**(Create).

    ![Captura de pantalla de la opción Agregar colección de Studio 3T.](./media/mongodb-mongochef/AddCollection.png)
4. Haga clic en el elemento de menú **Collection** (Colección) y en **Add Document** (Agregar documento).

    ![Captura de pantalla del elemento de menú Agregar documento de Studio 3T.](./media/mongodb-mongochef/AddDocument1.png)
5. En el cuadro de diálogo Add Document (Agregar documento), pegue lo siguiente y haga clic en **Add Document**(Agregar documento).

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. Agregue otro documento, esta vez con el contenido siguiente:

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. Ejecute una consulta de ejemplo. Por ejemplo, busque familias con el apellido 'Andersen' y devuelva los campos parents (padres) y state (estado).

    ![Captura de pantalla de resultados de la consulta de MongoChef.](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Robo 3T](mongodb-robomongo.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](mongodb-samples.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.
