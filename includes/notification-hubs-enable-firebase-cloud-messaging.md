---
title: archivo de inclusión
description: archivo de inclusión
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 02/05/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: fef6122eceda213fb6353ada53033d0d1e27fd7e
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509093"
---
1. Inicie sesión en la [consola Firebase](https://firebase.google.com/console/). Si aún no tiene uno, cree un nuevo proyecto de Firebase.
2. Después de crear el proyecto, seleccione **Add Firebase to your Android app** (Añade Firebase a tu aplicación de Android). 

    ![Agregar Firebase a una aplicación de Android](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-add-firebase-to-android-app.png)
3. En la página **Add Firebase to your Android app** (Agregar Firebase a la aplicación Android), haga lo siguiente: 
    1. En **Android package name** (Nombre del paquete Android), copie el valor de **applicationId** en el archivo build.gradle de la aplicación. En este ejemplo, es `com.fabrikam.fcmtutorial1app`. 

        ![Especifique el nombre del paquete.](./media/notification-hubs-enable-firebase-cloud-messaging/specify-package-name-fcm-settings.png)
    2. Seleccione **Registrar aplicación**. 
4. Seleccione **Descargar google-services.json**, guarde el archivo en la carpeta **app** del proyecto y, a continuación, seleccione **Siguiente**. 

    ![Descargue el archivo google-services.json.](./media/notification-hubs-enable-firebase-cloud-messaging/download-google-service-button.png)
5. Haga que los siguientes **cambios de configuración** en el proyecto en Android Studio. 
    1.  En el archivoproject-level build.gradle (&lt;project&gt;/build.gradle), agregue la siguiente instrucción a la sección **dependencies**. 

        ```
        classpath 'com.google.gms:google-services:4.0.1'
        ```
    2. En el archivoapp-level build.gradle (&lt;project&gt;/&lt;app-module&gt;/build.gradle), agregue la siguiente instrucción a la sección **dependencies**. 

        ```
        implementation 'com.google.firebase:firebase-core:16.0.1'
        ```

    3. Agregue la siguiente línea al final del archivo app-level build.gradle después de la sección de dependencias. 

        ```
        apply plugin: 'com.google.gms.google-services'
        ```        
    4. Seleccione **Sinc Now** (Sincronizar ahora) en la barra de herramientas. 
 
        ![cambios de configuración de build.gradle](./media/notification-hubs-enable-firebase-cloud-messaging/build-gradle-configurations.png)
6. Seleccione **Next** (Siguiente). 
7. Seleccione **Skip this step** (Omitir este paso). 

    ![Omita el último paso.](./media/notification-hubs-enable-firebase-cloud-messaging/skip-this-step.png)
8. En la consola Firebase, seleccione el icono de la rueda dentada del proyecto. Luego, seleccione **Project Settings** (Configuración del proyecto).

    ![Seleccionar Project Settings (Configuración del proyecto)](./media/notification-hubs-enable-firebase-cloud-messaging/notification-hubs-firebase-console-project-settings.png)
4. Si aún no ha descargado el archivo google-services.json en la carpeta **app** del proyecto de Android Studio, puede hacerlo en esta página. 
5. Cambie a la pestaña **Cloud Messaging** de la parte superior. 
6. Copie y guarde la **clave del servidor** para su uso posterior. Usará este valor para configurar el concentrador.
