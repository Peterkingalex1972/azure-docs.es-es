---
title: 'Conexión a la API de Azure Media Services v3: Java'
description: Aprenda a conectarse a la API de Media Services v3 con Java.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: juliako
ms.openlocfilehash: b7ee54c852ce3332415b69ca6105b472dab0ab8a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66480256"
---
# <a name="connect-to-media-services-v3-api---java"></a>Conexión a la API de Media Services v3: Java

En este artículo se muestra cómo conectarse al SDK de Java de Azure Media Services v3 con el método de inicio de sesión de la entidad de servicio.

En este artículo se utiliza Visual Studio Code para desarrollar la aplicación de ejemplo.

## <a name="prerequisites"></a>Requisitos previos

- Siga el artículo [Writing Java with Visual Studio Code](https://code.visualstudio.com/docs/java/java-tutorial) (Escritura en Java con Visual Studio Code) para instalar:

   - JDK
   - Apache Maven
   - Paquete de la extensión de Java
- Asegúrese de establecer las variables de entorno `JAVA_HOME` y `PATH`.
- [Cree una cuenta de Media Services](create-account-cli-how-to.md). Asegúrese de recordar el nombre del grupo de recursos y el nombre de la cuenta de Media Services.
- Siga los pasos descritos en el tema [Acceso a la API](access-api-cli-how-to.md). Registre el identificador de suscripción, el identificador de aplicación (el identificador de cliente), la clave de autenticación (el secreto) y el identificador de inquilino que necesitará en un paso posterior.

Además, revise lo siguiente:

- [Java in Visual Studio Code](https://code.visualstudio.com/docs/languages/java) (Java en Visual Studio Code)
- [Java Project Management in VS Code](https://code.visualstudio.com/docs/java/java-project) (Administración de proyectos de Java en VS Code)

## <a name="create-a-maven-project"></a>Creación de un proyecto de Maven

Abra una herramienta de línea de comandos y `cd` en el directorio donde desee crear el proyecto.
    
```
mvn archetype:generate -DgroupId=com.azure.ams -DartifactId=testAzureApp -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
```

Al ejecutar el comando, se crean `pom.xml`, `App.java` y otros archivos. 

## <a name="add-dependencies"></a>Adición de dependencias

1. En Visual Studio Code, abra la carpeta donde está el proyecto
1. Busque y abra `pom.xml`
1. Agregue las dependencias necesarias

    ```xml
   <dependency>
     <groupId>com.microsoft.azure.mediaservices.v2018_07_01</groupId>
     <artifactId>azure-mgmt-media</artifactId>
     <version>1.0.0-beta-3</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.rest</groupId>
     <artifactId>client-runtime</artifactId>
     <version>1.6.6</version>
   </dependency>
   <dependency>
     <groupId>com.microsoft.azure</groupId>
     <artifactId>azure-client-authentication</artifactId>
     <version>1.6.6</version>
   </dependency>
    ```

## <a name="connect-to-the-java-client"></a>Conexión con el cliente de Java

1. Abra el archivo `App.java` en `src\main\java\com\azure\ams` y asegúrese de que el paquete esté en la parte superior:

    ```java
    package com.azure.ams;
    ```
1. En la instrucción del paquete, agregue estas instrucciones de importación:
   
   ```java
   import com.microsoft.azure.AzureEnvironment;
   import com.microsoft.azure.credentials.ApplicationTokenCredentials;
   import com.microsoft.azure.management.mediaservices.v2018_07_01.implementation.MediaManager;
   import com.microsoft.rest.LogLevel;
   ```
1. Para crear las credenciales de Active Directory que necesita para realizar solicitudes, agregue este código al método Main de la clase App y establezca los valores que obtuvo de las [API de acceso](access-api-cli-how-to.md):
   
   ```java
   final String clientId = "00000000-0000-0000-0000-000000000000";
   final String tenantId = "00000000-0000-0000-0000-000000000000";
   final String clientSecret = "00000000-0000-0000-0000-000000000000";
   final String subscriptionId = "00000000-0000-0000-0000-000000000000";

   try {
      ApplicationTokenCredentials credentials = new ApplicationTokenCredentials(clientId, tenantId, clientSecret, AzureEnvironment.AZURE);
      credentials.withDefaultSubscriptionId(subscriptionId);

      MediaManager manager = MediaManager
              .configure()
              .withLogLevel(LogLevel.BODY_AND_HEADERS)
              .authenticate(credentials, credentials.defaultSubscriptionId());
      System.out.println("signed in");
   }
   catch (Exception e) {
      System.out.println("Exception encountered.");
      System.out.println(e.toString());
   }
   ```
1. Ejecute la aplicación.

## <a name="see-also"></a>Otras referencias

- [Conceptos de Azure Media Services](concepts-overview.md)
- [SDK de Java](https://aka.ms/ams-v3-java-sdk)
- [Referencia de Java](https://aka.ms/ams-v3-java-ref)
- [com.microsoft.azure.mediaservices.v2018_07_01:azure-mgmt-media](https://search.maven.org/artifact/com.microsoft.azure.mediaservices.v2018_07_01/azure-mgmt-media/1.0.0-beta/jar)

## <a name="next-steps"></a>Pasos siguientes

Ahora puede incluir `import com.microsoft.azure.management.mediaservices.v2018_07_01.*;` y comenzar a manipular las entidades.
