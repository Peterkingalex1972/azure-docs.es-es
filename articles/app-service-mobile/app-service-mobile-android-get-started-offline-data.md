---
title: Activar la sincronización sin conexión para la aplicación móvil de Azure (Android)
description: Obtenga información sobre cómo usar App Service Mobile Apps para almacenar en caché y sincronizar datos sin conexión en su aplicación de Android
documentationcenter: android
author: elamalani
manager: crdun
services: app-service\mobile
ms.assetid: 32a8a079-9b3c-4faf-8588-ccff02097224
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3fe5b176d864fd4cdd1ff49d8c064495663aa3b0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443578"
---
# <a name="enable-offline-sync-for-your-android-mobile-app"></a>Activar la sincronización sin conexión para la aplicación móvil de Android
[!INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]

> [!NOTE]
> Visual Studio App Center está invirtiendo en servicios nuevos e integrados fundamentales para el desarrollo de aplicaciones móviles. Los desarrolladores pueden usar los servicios de **compilación**, **prueba** y **distribución** para configurar canalizaciones de integración y entrega continuas. Una vez que se ha implementado la aplicación, los desarrolladores pueden supervisar el estado y el uso de su aplicación con los servicios de **análisis** y **diagnóstico**, e interactuar con los usuarios que usan el servicio de **Push** (inserción). Además, los desarrolladores pueden aprovechar **Auth** para autenticar a los usuarios y el servicio de **datos** para almacenar y sincronizar los datos de la aplicación en la nube. Visite [App Center](https://appcenter.ms/?utm_source=zumo&utm_campaign=app-service-mobile-android-get-started-offline-data) hoy mismo.
>

## <a name="overview"></a>Información general
En este tutorial se explica la característica de sincronización sin conexión de Azure Mobile Apps para Android. La sincronización sin conexión permite a los usuarios finales interactuar con una aplicación móvil &mdash;ver, agregar o modificar datos&mdash; aun cuando no haya conexión de red. Los cambios se almacenan en una base de datos local. Una vez que el dispositivo se vuelve a conectar, estos cambios se sincronizan con el back-end remoto.

Si esta es la primera vez que usa Azure Mobile Apps, primero debería completar el tutorial [Creación de una aplicación de Android]. Si no usa el proyecto de servidor de inicio rápido descargado, debe agregar paquetes de extensión de acceso de datos al proyecto. Para obtener más información acerca de los paquetes de extensión de servidor, consulte [Trabajar con el SDK del servidor back-end de .NET para Aplicaciones móviles de Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

Para obtener más información acerca de la característica de sincronización sin conexión, consulte el tema [Sincronización de datos sin conexión en Aplicaciones móviles de Azure].

## <a name="update-the-app-to-support-offline-sync"></a>Actualización de la aplicación para que admita la sincronización sin conexión
Con la sincronización sin conexión, se lee y se escribe desde una *tabla de sincronización* (usando la interfaz *IMobileServiceSyncTable*), que forma parte de una base de datos **SQLite** en el dispositivo.

Para insertar y extraer los cambios entre el dispositivo y Azure Mobile Services, se usa un *contexto de sincronización* (*MobileServiceClient.SyncContext*), que se inicializa con la base de datos local usada para almacenar los datos localmente.

1. En `TodoActivity.java`, convierta en comentario la definición existente de `mToDoTable` y quite la marca de comentario de la versión de la tabla de sincronización:
   
        private MobileServiceSyncTable<ToDoItem> mToDoTable;
2. En el método `onCreate`, convierta en comentario la inicialización existente de `mToDoTable` y quite la marca de comentario de esta definición:
   
        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);
3. En `refreshItemsFromTable`, convierta en comentario la definición de `results` y quite la marca de comentario de esta definición:
   
        // Offline Sync
        final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();
4. Convierta en comentario la definición de `refreshItemsFromMobileServiceTable`.
5. Quite la marca de comentario de la definición de `refreshItemsFromMobileServiceTableSyncTable`:
   
        private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
            //sync the data
            sync().get();
            Query query = QueryOperations.field("complete").
                    eq(val(false));
            return mToDoTable.read(query).get();
        }
6. Quite la marca de comentario de la definición de `sync`:
   
        private AsyncTask<Void, Void, Void> sync() {
            AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
                @Override
                protected Void doInBackground(Void... params) {
                    try {
                        MobileServiceSyncContext syncContext = mClient.getSyncContext();
                        syncContext.push().get();
                        mToDoTable.pull(null).get();
                    } catch (final Exception e) {
                        createAndShowDialogFromTask(e, "Error");
                    }
                    return null;
                }
            };
            return runAsyncTask(task);
        }

## <a name="test-the-app"></a>Prueba de la aplicación
En esta sección, probará el comportamiento con la red inalámbrica activada y, después, desactivará la red inalámbrica para crear un escenario sin conexión.

Al agregar elementos de datos, se guardan en el almacén local de SQLite, pero no se sincronizarán con el servicio móvil hasta que presione el botón **Actualizar** . Otras aplicaciones pueden tener requisitos diferentes con respecto a cuándo deben sincronizarse los datos, pero para los fines de demostración de este tutorial, haga que el usuario lo solicite expresamente.

Cuando presione ese botón, se iniciará una nueva tarea en segundo plano. En primer lugar, inserta todos los cambios realizados en el almacén local mediante el contexto de sincronización y, a continuación, extrae todos los datos cambiados de Azure a la tabla local.

### <a name="offline-testing"></a>Pruebas sin conexión
1. Coloque el dispositivo o el simulador en *Modo avión*. Esto crea un escenario sin conexión.
2. Agregue algunos elementos *ToDo* o marque algunos elementos como completados. Salga del dispositivo o del simulador (o fuerce el cierre de la aplicación) y reinicie. Compruebe que los cambios se conservan en el dispositivo porque se guardan en el almacén local de SQLite.
3. Visualice los contenidos de la tabla *TodoItem* de Azure con una herramienta SQL, como *SQL Server Management Studio* o con un cliente REST como *Fiddler* o *Postman*. Compruebe que los nuevos elementos *no* se han sincronizado con el servidor:
   
       + En un back-end de Node.js, vaya a [Azure Portal](https://portal.azure.com/) y, en el back-end de Mobile App, haga clic en **Tablas fáciles** > **TodoItem** para ver el contenido de la tabla `TodoItem`.
       + En el back-end de .NET, vea el contenido de tabla con una herramienta SQL, como *SQL Server Management Studio*, o con un cliente REST como *Fiddler* o *Postman*.
4. Active la red inalámbrica en el dispositivo o el simulador. Después, presione el botón **Actualizar** .
5. Vea los datos de TodoItem de nuevo en el portal de Azure. Ahora deberían aparecer los elementos de TodoItems nuevos y modificados.

## <a name="additional-resources"></a>Recursos adicionales
* [Sincronización de datos sin conexión en Aplicaciones móviles de Azure]
* [Cloud Cover: sincronización sin conexión en Azure Mobile Services] \((Nota: El vídeo trata sobre Mobile Services, pero la sincronización sin conexión funciona de forma similar en Azure Mobile Apps)\)

<!-- URLs. -->

[Sincronización de datos sin conexión en Aplicaciones móviles de Azure]: app-service-mobile-offline-data-sync.md

[Creación de una aplicación de Android]: app-service-mobile-android-get-started.md

[Cloud Cover: sincronización sin conexión en Azure Mobile Services]: https://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: https://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

