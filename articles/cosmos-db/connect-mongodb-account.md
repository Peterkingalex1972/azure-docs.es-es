---
title: Conectar una aplicación de MongoDB a Azure Cosmos DB
description: Aprenda a conectar la aplicación de MongoDB a Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: dabce19d60a380b47d3583dedb5c11303f416ce7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65978723"
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>Conectar una aplicación de MongoDB a Azure Cosmos DB
Aprenda a conectar su aplicación de MongoDB a una instancia de Azure Cosmos DB mediante una cadena de conexión de MongoDB. Después, puede usar una base de datos de Azure Cosmos como almacén de datos de la aplicación MongoDB. 

En este tutorial se proporcionan dos maneras de recuperar información de la cadena de conexión:

- [El método de inicio rápido](#QuickstartConnection), para su uso con controladores de .NET, Node.js, MongoDB Shell, Java y Python.
- [El método de la cadena de conexión personalizada](#GetCustomConnection), para su uso con otros controladores.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una cuenta de Azure, cree ahora una [cuenta de Azure gratuita](https://azure.microsoft.com/free/). 
- Una cuenta de Cosmos. Para obtener instrucciones, consulte [Compilación de una aplicación web con la API de Azure Cosmos DB para MongoDB y .NET SDK](create-mongodb-dotnet.md).

## <a id="QuickstartConnection"></a>Obtener la cadena de conexión de MongoDB mediante el menú de inicio rápido
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la API. 
3. En el panel izquierdo de la hoja de la cuenta, haga clic en **Inicio rápido**. 
4. Elija la plataforma ( **.NET**, **Node.js**, **Shell de MongoDB**, **Java**, **Python**). Si no ve el controlador o la herramienta en la lista, no se preocupe, documentamos constantemente más fragmentos de código de conexión. Comente a continuación lo que le gustaría ver. Para aprender a crear su propia conexión, lea la sección sobre cómo [obtener información de la cadena de conexión de la cuenta](#GetCustomConnection).
5. Copie y pegue el fragmento de código en la aplicación MongoDB.

    ![Hoja de inicio rápido](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a> Obtención de la cadena de conexión de MongoDB para personalizar
1. En un explorador de Internet, inicie sesión en [Azure Portal](https://portal.azure.com).
2. En la hoja **Azure Cosmos DB**, seleccione la API. 
3. En el panel izquierdo de la hoja de la cuenta, haga clic en **Cadena de conexión**. 
4. Se abre la hoja **Cadena de conexión**, que contiene toda la información necesaria para conectarse a la cuenta con un controlador para MongoDB, incluida una cadena de conexión precreada.

    ![Hoja Cadena de conexión](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>Requisitos de la cadena de conexión
> [!Important]
> Azure Cosmos DB tiene estándares y requisitos de seguridad estrictos. Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura a través de *SSL*. 
>
>

Azure Cosmos DB es compatible con el formato URI de la cadena de conexión de MongoDB estándar con un par de requisitos específicos: Las cuentas de Azure Cosmos DB requieren autenticación y comunicación segura a través de SSL. Por tanto, el formato de la cadena de conexión es:

    mongodb://username:password@host:port/[database]?ssl=true

Los valores de esta cadena están disponibles en la hoja **Cadena de conexión** mostrada antes:

* Username (obligatorio): nombre de la cuenta de Cosmos.
* Password (obligatorio): contraseña de la cuenta de Cosmos.
* Host (obligatorio): FQDN de la cuenta de Cosmos.
* Port (obligatorio): 10255.
* Database (opcional): base de datos que usa la conexión. Si no se proporciona ninguna base de datos, la base de datos predeterminada es "test".
* ssl=true (obligatorio)

Por ejemplo, considere la cuenta que aparece en la hoja **Cadena de conexión**. Una cadena de conexión válida es:

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [usar Studio 3T](mongodb-mongochef.md) con la API de Azure Cosmos DB para MongoDB.
- Aprenda a [usar Robo 3T](mongodb-robomongo.md) con la API de Azure Cosmos DB para MongoDB.
- Explore [ejemplos](mongodb-samples.md) de MongoDB con la API de Azure Cosmos DB para MongoDB.
