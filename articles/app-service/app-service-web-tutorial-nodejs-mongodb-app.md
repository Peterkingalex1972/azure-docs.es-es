---
title: 'Node.js (MEAN.js) con MongoDB: Azure App Service | Microsoft Docs'
description: Aprenda a empezar a trabajar con una aplicación Node.js en Azure, con conexión a una base de datos Cosmos DB con una cadena de conexión de MongoDB. MEAN.js se utiliza en el tutorial.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: erikre
editor: ''
ms.assetid: 0b4d7d0e-e984-49a1-a57a-3c0caa955f0e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 05/04/2017
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 361e921af65b33ac0a7a8d12e28db1cb305b0fa1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "66138910"
---
# <a name="tutorial-build-a-nodejs-and-mongodb-app-in-azure"></a>Tutorial: Compilación de una aplicación Node.js y MongoDB en Azure

> [!NOTE]
> En este artículo se implementa una aplicación en App Service en Windows. Para realizar implementaciones en App Service en _Linux_, consulte [Compilación de una aplicación Node.js y MongoDB en Azure App Service en Linux](./containers/tutorial-nodejs-mongodb-app.md).
>

Azure App Service proporciona un servicio de hospedaje web muy escalable y con aplicación de revisiones de un modo automático. En este tutorial se muestra cómo crear una aplicación Node.js en App Service y conectarla a una base de datos de MongoDB. Cuando haya terminado, tendrá una aplicación MEAN (MongoDB, Express, AngularJS y Node.js) que se ejecuta en [Azure App Service](overview.md). Por sencillez, la aplicación de ejemplo usa el [marco web MEAN.js ](https://meanjs.org/).

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Temas que se abordarán:

> [!div class="checklist"]
> * Crear una base de datos MongoDB en Azure
> * Conectar una aplicación Node.js a MongoDB
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros de diagnóstico desde Azure
> * Administrar la aplicación en Azure Portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial:

1. [Instalación de Git](https://git-scm.com/)
2. [Instalación de Node.js y NPM](https://nodejs.org/)
3. [Instale Bower](https://bower.io/) (necesario para [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
4. [Instale Gulp.js](https://gulpjs.com/) (necesario para [MEAN.js](https://meanjs.org/docs/0.5.x/#getting-started))
5. [Descarga y ejecución de MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) 

## <a name="test-local-mongodb"></a>Prueba de la base de datos MongoDB local

Abra la ventana de terminal y use `cd` para cambiar al directorio `bin` de la instalación de MongoDB. Esta ventana de terminal se puede usar para ejecutar todos los comandos de este tutorial.

Ejecute `mongo` en el terminal para conectarse a su servidor local de MongoDB.

```bash
mongo
```

Si la conexión se realiza correctamente, la base de datos de MongoDB estará ya funcionando. En caso contrario, asegúrese de que la base de datos de MongoDB local se inicia con los pasos descritos en [Install MongoDB Community Edition](https://docs.mongodb.com/manual/administration/install-community/) (Instalación de MongoDB Community Edition). Con frecuencia, MongoDB ya estará instalado, pero aun así deberá iniciarlo ejecutando `mongod`. 

Cuando haya terminado de probar la base de datos de MongoDB, escriba `Ctrl+C` en el terminal. 

## <a name="create-local-nodejs-app"></a>Creación de una aplicación local Node.js

En este paso, configurará el proyecto local de Node.js.

### <a name="clone-the-sample-application"></a>Clonación de la aplicación de ejemplo

En la ventana del terminal, use `cd` para cambiar a un directorio de trabajo.  

Ejecute el comando siguiente para clonar el repositorio de ejemplo. 

```bash
git clone https://github.com/Azure-Samples/meanjs.git
```

Este repositorio de ejemplo contiene una copia del [repositorio MEAN.js](https://github.com/meanjs/mean). Se ha modificado para ejecutarse App Service (para más información, consulte el [archivo LÉAME](https://github.com/Azure-Samples/meanjs/blob/master/README.md) del repositorio MEAN.js).

### <a name="run-the-application"></a>Ejecución de la aplicación

Instale los comandos siguientes para instalar los paquetes necesarios e inicie la aplicación.

```bash
cd meanjs
npm install
npm start
```

Cuando la aplicación se haya cargado completamente, verá algo similar al siguiente mensaje:

```console
--
MEAN.JS - Development Environment

Environment:     development
Server:          http://0.0.0.0:3000
Database:        mongodb://localhost/mean-dev
App version:     0.5.0
MEAN.JS version: 0.5.0
--
```

Vaya a `http://localhost:3000` en un explorador. Haga clic en **Registrarse** en el menú superior y cree un usuario de prueba. 

La aplicación de ejemplo MEAN.js almacena datos de usuario en la base de datos. Si crea un usuario e inicia sesión con él correctamente, la aplicación escribe datos en la base de datos de MongoDB local.

![MEAN.js se conecta correctamente a MongoDB](./media/app-service-web-tutorial-nodejs-mongodb-app/mongodb-connect-success.png)

Seleccione **Admin (Administrador) > Manage Articles (Administrar artículos)** para agregar algunos artículos.

Para detener Node.js en cualquier momento, presione `Ctrl+C` en el terminal. 

> [!NOTE]
> En la [guía de inicio rápido de Node.js](app-service-web-get-started-nodejs.md) se menciona la necesidad de un archivo web.config en el directorio raíz de la aplicación. Sin embargo, en este tutorial, App Service generará este archivo automáticamente al implementar los archivos mediante la [implementación de Git local](deploy-local-git.md), en lugar de la implementación de un archivo ZIP. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-mongodb"></a>Creación de una base de datos de MongoDB de producción

En este paso, creará una base de datos MongoDB en Azure. Cuando la aplicación se implementa en Azure, utiliza esta base de datos en la nube.

Para MongoDB, en este tutorial se usa [Azure Cosmos DB](/azure/documentdb/). Cosmos DB admite las conexiones del cliente de MongoDB.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)] 

### <a name="create-a-cosmos-db-account"></a>Creación de una cuenta de Cosmos DB

> [!NOTE]
> Hay un costo por la creación de las bases de datos de Azure Cosmos DB en este tutorial en su propia suscripción de Azure. Para usar una cuenta gratuita de Azure Cosmos DB durante siete días, puede usar la experiencia [Pruebe gratis Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/). Simplemente haga clic en el botón **Crear** en el icono de MongoDB para crear una base de datos gratuita de MongoDB en Azure. Una vez creada la base de datos, vaya a **Cadena de conexión** en el portal y recupere la cadena de conexión de Azure Cosmos DB para su uso posterior en el tutorial.
>

En Cloud Shell, cree una cuenta de Cosmos DB con el comando [`az cosmosdb create`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-create).

En el siguiente comando, sustituya un nombre único de Cosmos DB por el marcador de posición *\<cosmosdb_name>* . Este nombre se usa como parte del punto de conexión de Cosmos DB, `https://<cosmosdb_name>.documents.azure.com/`, por lo que el nombre debe ser único en todas las cuentas de Cosmos DB de Azure. El nombre debe contener solo letras minúsculas, números y el carácter de guión (-), y debe tener una longitud de entre 3 y 50 caracteres.

```azurecli-interactive
az cosmosdb create --name <cosmosdb_name> --resource-group myResourceGroup --kind MongoDB
```

El parámetro *--kind MongoDB* habilita las conexiones de cliente de MongoDB.

Cuando se crea la cuenta de Cosmos DB, la CLI de Azure muestra información similar a la del ejemplo siguiente:

```json
{
  "consistencyPolicy":
  {
    "defaultConsistencyLevel": "Session",
    "maxIntervalInSeconds": 5,
    "maxStalenessPrefix": 100
  },
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb_name>.documents.azure.com:443/",
  "failoverPolicies": 
  ...
  < Output truncated for readability >
}
```

## <a name="connect-app-to-production-mongodb"></a>Conexión de la aplicación a la base de datos MongoDB de producción

En este paso, conectará la aplicación de ejemplo MEAN.js a la base de datos Cosmos DB que acaba de crear mediante una cadena de conexión de MongoDB. 

### <a name="retrieve-the-database-key"></a>Recuperación de la clave de base de datos

Para conectarse a la base de datos Cosmos DB, necesita la clave de base de datos. En Cloud Shell, use el comando [`az cosmosdb list-keys`](/cli/azure/cosmosdb?view=azure-cli-latest#az-cosmosdb-list-keys) para recuperar la clave principal.

```azurecli-interactive
az cosmosdb list-keys --name <cosmosdb_name> --resource-group myResourceGroup
```

La CLI de Azure muestra información similar a la del ejemplo siguiente:

```json
{
  "primaryMasterKey": "RS4CmUwzGRASJPMoc0kiEvdnKmxyRILC9BWisAYh3Hq4zBYKr0XQiSE4pqx3UchBeO4QRCzUt1i7w0rOkitoJw==",
  "primaryReadonlyMasterKey": "HvitsjIYz8TwRmIuPEUAALRwqgKOzJUjW22wPL2U8zoMVhGvregBkBk9LdMTxqBgDETSq7obbwZtdeFY7hElTg==",
  "secondaryMasterKey": "Lu9aeZTiXU4PjuuyGBbvS1N9IRG3oegIrIh95U6VOstf9bJiiIpw3IfwSUgQWSEYM3VeEyrhHJ4rn3Ci0vuFqA==",
  "secondaryReadonlyMasterKey": "LpsCicpVZqHRy7qbMgrzbRKjbYCwCKPQRl0QpgReAOxMcggTvxJFA94fTi0oQ7xtxpftTJcXkjTirQ0pT7QFrQ=="
}
```

Copie el valor de `primaryMasterKey`. Esta información la necesitará en el siguiente paso.

<a name="devconfig"></a>
### <a name="configure-the-connection-string-in-your-nodejs-application"></a>Configuración de la cadena de conexión en la aplicación Node.js

En la carpeta _config/env/_ del repositorio MEAN.js local, cree un archivo llamado _local-production.js_. De forma predeterminada, _.gitignore_ está configurado para mantener este archivo fuera del repositorio. 

Copie en él el código siguiente. Asegúrese de reemplazar los dos marcadores de posición *\<cosmosdb_name>* por el nombre de la base de datos Cosmos DB y de reemplazar el marcador de posición *\<primary_master_key>* por la clave que copió en el paso anterior.

```javascript
module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false'
  }
};
```

La opción `ssl=true` se requiere porque [Cosmos DB requiere SSL](../cosmos-db/connect-mongodb-account.md#connection-string-requirements). 

Guarde los cambios.

### <a name="test-the-application-in-production-mode"></a>Prueba de la aplicación en modo de producción 

Ejecute el siguiente comando para minimizar y agrupar scripts para el entorno de producción. Este proceso genera los archivos que necesita dicho entorno.

```bash
gulp prod
```

Ejecute el siguiente comando para usar la cadena de conexión que configuró en _config/env/local-production.js_.

```bash
# Bash
NODE_ENV=production node server.js

# Windows PowerShell
$env:NODE_ENV = "production" 
node server.js
```

`NODE_ENV=production` establece la variable de entorno que indica a Node.js que se ejecute en el entorno de producción.  `node server.js` inicia el servidor de Node.js con `server.js` en la raíz del repositorio. Así es como se carga la aplicación de Node.js en Azure. 

Cuando se cargue la aplicación, compruebe que se ejecuta en el entorno de producción:

```console
--
MEAN.JS

Environment:     production
Server:          http://0.0.0.0:8443
Database:        mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true&sslverifycertificate=false
App version:     0.5.0
MEAN.JS version: 0.5.0
```

Vaya a `http://localhost:8443` en un explorador. Haga clic en **Registrarse** en el menú superior y cree un usuario de prueba. Si crea un usuario e inicia sesión con él correctamente, la aplicación escribe datos en la base de datos de Cosmos DB en Azure. 

En el terminal, detenga Node.js escribiendo `Ctrl+C`. 

## <a name="deploy-app-to-azure"></a>Implementación de aplicación en Azure

En este paso, implementará la aplicación Node.js conectada a MongoDB en Azure App Service.

### <a name="configure-a-deployment-user"></a>Configuración de un usuario de implementación

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

[!INCLUDE [Create app service plan no h](../../includes/app-service-web-create-app-service-plan-no-h.md)]

<a name="create"></a>
### <a name="create-a-web-app"></a>Creación de una aplicación web

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-nodejs-no-h.md)] 

### <a name="configure-an-environment-variable"></a>Configuración de una variable de entorno

De forma predeterminada, el proyecto MEAN.js mantiene _config/env/local-production.js_ fuera del repositorio de Git. Por lo tanto, para su aplicación de Azure, use la configuración de aplicación para definir la cadena de conexión de MongoDB.

Para establecer la configuración de la aplicación, utilice el comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) en Cloud Shell. 

En el ejemplo siguiente se configura una configuración de aplicación `MONGODB_URI` en la aplicación de Azure. Reemplace los marcadores de posición  *\<app_name >* ,  *\<cosmosdb_name >* y  *\<primary_master_key >* .

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group myResourceGroup --settings MONGODB_URI="mongodb://<cosmosdb_name>:<primary_master_key>@<cosmosdb_name>.documents.azure.com:10250/mean?ssl=true"
```

En el código de Node.js, accederá a este valor de aplicación con `process.env.MONGODB_URI`, al igual que accedería a cualquier variable de entorno. 

En el repositorio MEAN.js local, abra _config/env/production.js_ (no _config/env/local-production.js_), que tiene la configuración específica para el entorno de producción. La aplicación MEAN.js predeterminada ya está configurada para usar la variable de entorno `MONGODB_URI` que ha creado.

```javascript
db: {
  uri: ... || process.env.MONGODB_URI || ...,
  ...
},
```

### <a name="push-to-azure-from-git"></a>Inserción en Azure desde Git

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash
Counting objects: 5, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (5/5), 489 bytes | 0 bytes/s, done.
Total 5 (delta 3), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6c7c716eee'.
remote: Running custom deployment command...
remote: Running deployment command...
remote: Handling node.js deployment.
.
.
.
remote: Deployment successful.
To https://<app_name>.scm.azurewebsites.net/<app_name>.git
 * [new branch]      master -> master
``` 

Puede observar que el proceso de implementación ejecuta [Gulp](https://gulpjs.com/) después de `npm install`. App Service no ejecuta tareas Gulp o Grunt durante la implementación, así que este repositorio de ejemplo tiene dos archivos adicionales en su directorio raíz para permitirlo: 

- _.deployment_: este archivo indica a App Service que ejecute `bash deploy.sh` como script de implementación personalizado.
- _deploy.sh_: se trata del script de implementación personalizado. Si revisa el archivo, verá que se ejecuta `gulp prod` después de `npm install` y `bower install`. 

Puede usar este enfoque para agregar cualquier paso a la implementación basada en Git. Si reinicia la aplicación de Azure en cualquier momento, App Service no vuelve a ejecutar estas tareas de automatización.

### <a name="browse-to-the-azure-app"></a>Navegación hasta la aplicación de Azure 

Vaya a la aplicación implementada mediante el explorador web. 

```bash 
http://<app_name>.azurewebsites.net 
``` 

Haga clic en **Registrarse** en el menú superior y cree un usuario ficticio. 

Si se realiza correctamente y la aplicación inicia automáticamente la sesión del usuario creado, la aplicación MEAN.js de Azure tiene conectividad con la base de datos de MongoDB (Cosmos DB). 

![Aplicación MEAN.js que se ejecuta en Azure App Service](./media/app-service-web-tutorial-nodejs-mongodb-app/meanjs-in-azure.png)

Seleccione **Admin (Administrador) > Manage Articles (Administrar artículos)** para agregar algunos artículos. 

**¡Enhorabuena!** Está ejecutando una aplicación Node.js orientada a datos en Azure App Service.

## <a name="update-data-model-and-redeploy"></a>Actualización del modelo de datos y nueva implementación

En este paso, se cambia el modelo de datos de `article` y se publica el cambio en Azure.

### <a name="update-the-data-model"></a>Actualización del modelo de datos

Abra _modules/articles/server/models/article.server.model.js_.

En `ArticleSchema`, agregue un tipo `String` llamado `comment`. Cuando haya finalizado, su código de esquema tendrá este aspecto:

```javascript
const ArticleSchema = new Schema({
  ...,
  user: {
    type: Schema.ObjectId,
    ref: 'User'
  },
  comment: {
    type: String,
    default: '',
    trim: true
  }
});
```

### <a name="update-the-articles-code"></a>Actualización del código de artículos

Actualice el resto del código de `articles` para usar `comment`.

Hay cinco archivos que es necesario modificar: el controlador del servidor y las cuatro vistas del cliente. 

Abra _modules/articles/server/controllers/articles.server.controller.js_.

En la función `update`, agregue una asignación para `article.comment`. El código siguiente muestra la función `update` completada:

```javascript
exports.update = function (req, res) {
  let article = req.article;

  article.title = req.body.title;
  article.content = req.body.content;
  article.comment = req.body.comment;

  ...
};
```

Abra _modules/articles/client/views/view-article.client.view.html_.

Justo antes de la etiqueta `</section>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="lead" ng-bind="vm.article.comment"></p>
```

Abra _modules/articles/client/views/list-articles.client.view.html_.

Justo antes de la etiqueta `</a>` de cierre, agregue la línea siguiente para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/list-articles.client.view.html_.

Dentro del elemento `<div class="list-group">` e inmediatamente encima de la etiqueta `</a>` de cierre, agregue la siguiente línea para mostrar `comment` junto con el resto de los datos de artículo:

```HTML
<p class="list-group-item-text" data-ng-bind="article.comment"></p>
```

Abra _modules/articles/client/views/admin/form-article.client.view.html_.

Busque el elemento `<div class="form-group">` que contiene el botón de envío, que es similar a este:

```HTML
<div class="form-group">
  <button type="submit" class="btn btn-default">{{vm.article._id ? 'Update' : 'Create'}}</button>
</div>
```

Inmediatamente encima de esta etiqueta, agregue otro elemento `<div class="form-group">` que permita a los usuarios editar el campo `comment`. El nuevo elemento debe ser como este:

```HTML
<div class="form-group">
  <label class="control-label" for="comment">Comment</label>
  <textarea name="comment" data-ng-model="vm.article.comment" id="comment" class="form-control" cols="30" rows="10" placeholder="Comment"></textarea>
</div>
```

### <a name="test-your-changes-locally"></a>Prueba de los cambios localmente

Guarde todos los cambios.

En la ventana de terminal local, pruebe de nuevo los cambios en el modo de producción.

```bash
# Bash
gulp prod
NODE_ENV=production node server.js

# Windows PowerShell
gulp prod
$env:NODE_ENV = "production" 
node server.js
```

Vaya a `http://localhost:8443` en un explorador y asegúrese de que ha iniciado sesión.

Seleccione **Admin (Administrador) > Manage Articles (Administrar artículos)** y luego haga clic en el botón **+** para agregar un artículo.

Ahora verá el nuevo cuadro de texto `Comment`.

![Campo de comentario agregado a los artículos](./media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field.png)

En el terminal, detenga Node.js escribiendo `Ctrl+C`. 

### <a name="publish-changes-to-azure"></a>Publicación de los cambios en Azure

En la ventana del terminal local, confirme los cambios en Git e inserte los cambios de código en Azure.

```bash
git commit -am "added article comment"
git push azure master
```

Una vez que `git push` esté completo, vaya a la aplicación de Azure y pruebe la nueva funcionalidad.

![Modelo y cambios en la base de datos publicados en Azure](media/app-service-web-tutorial-nodejs-mongodb-app/added-comment-field-published.png)

Si agregó anteriormente artículos, aún puede verlos. Los datos existentes en Cosmos DB no se pierden. Además, se actualiza el esquema de datos y los datos existentes permanecen sin cambios.

## <a name="stream-diagnostic-logs"></a>Transmisión de registros de diagnóstico 

Mientras se ejecuta la aplicación de Node.js en Azure App Service, los registros de la consola se canalizan a su terminal. De este modo, puede obtener los mismos mensajes de diagnóstico para ayudarle a depurar errores de la aplicación.

Para iniciar la transmisión del registro, use el comando [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) en Cloud Shell.

```azurecli-interactive
az webapp log tail --name <app_name> --resource-group myResourceGroup
``` 

Cuando se inicie la secuencia de registro, actualice la aplicación de Azure en el explorador para obtener algún tráfico web. Ahora verá los registros de la consola canalizados a su terminal.

Para detener las secuencias de registro en cualquier momento, escriba `Ctrl+C`. 

## <a name="manage-your-azure-app"></a>Administración de la aplicación de Azure

Vaya a [Azure Portal](https://portal.azure.com) para ver la aplicación que ha creado.

En el menú izquierdo, haga clic en **App Services** y, luego, en el nombre de la aplicación de Azure.

![Navegación en el portal a la aplicación de Azure](./media/app-service-web-tutorial-nodejs-mongodb-app/access-portal.png)

De manera predeterminada, el portal muestra la página **Información general** de la aplicación. Esta página proporciona una visión del funcionamiento de la aplicación. En este caso, también puede realizar tareas de administración básicas como examinar, detener, iniciar, reiniciar y eliminar. Las pestañas del lado izquierdo de la página muestran las diferentes páginas de configuración que puede abrir.

![Página de App Service en Azure Portal](./media/app-service-web-tutorial-nodejs-mongodb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>Pasos siguientes

¿Qué ha aprendido?

> [!div class="checklist"]
> * Crear una base de datos MongoDB en Azure
> * Conectar una aplicación Node.js a MongoDB
> * Implementación de la aplicación en Azure
> * Actualizar el modelo de datos y volver a implementar la aplicación
> * Transmitir registros desde Azure a un terminal
> * Administrar la aplicación en Azure Portal

Pase al siguiente tutorial para aprender cómo asignar un nombre DNS personalizado a la aplicación.

> [!div class="nextstepaction"] 
> [Asignación de un nombre DNS personalizado existente a Azure App Service](app-service-web-tutorial-custom-domain.md)
