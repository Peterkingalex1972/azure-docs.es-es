---
title: Solución de problemas relacionados con Azure Application Insights Snapshot Debugger | Microsoft Docs
description: Este artículo muestra información y los pasos a seguir para la solución de problemas para ayudar a los desarrolladores que tienen dificultades para habilitar o usar Application Insights Snapshot Debugger.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: 25ccf20fc78a9ec00d4dfe23a60e824e96d12945
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444544"
---
# <a id="troubleshooting"></a> Solucionar problemas de habilitación de Application Insights Snapshot Debugger o ver instantáneas
Si habilitó Application Insights Snapshot Debugger para la aplicación, pero no puede ver las instantáneas para las excepciones, puede usar estas instrucciones para solucionar problemas. Puede haber muchas razones diferentes de por qué no se generan las instantáneas. Puede ejecutar la comprobación de estado de instantáneas para identificar algunas de las posibles causas comunes.

## <a name="use-the-snapshot-health-check"></a>Uso de la comprobación de estado de instantáneas
Algunos problemas comunes provocan que no se muestre la opción Abrir instantánea de depuración. Por ejemplo, el uso de una instancia de Snapshot Collector no actualizada, el hecho de alcanzar el límite diario de carga o, quizás, la tardanza de la instantánea en cargarse. Use la comprobación de estado de instantáneas para solucionar problemas comunes.

Hay un vínculo en el panel de excepción de la vista de seguimiento de un extremo a otro que le lleva a la comprobación de estado de instantáneas.

![Acceso a la comprobación de estado de instantáneas](./media/snapshot-debugger/enter-snapshot-health-check.png)

La interfaz interactiva, similar a un chat, busca problemas comunes y le guía para corregirlos.

![Comprobación de estado](./media/snapshot-debugger/healthcheck.png)

Si no se soluciona el problema, consulte los siguientes pasos de solución de problemas de forma manual.

## <a name="verify-the-instrumentation-key"></a>Comprobar la clave de instrumentación

Asegúrese de que está usando la clave de instrumentación correcta en la aplicación publicada. Por lo general, la clave de instrumentación se lee desde el archivo ApplicationInsights.config. Compruebe que el valor es igual que la clave de instrumentación para el recurso de Application Insights que ve en el portal.

## <a name="preview-versions-of-net-core"></a>Versiones preliminares de .NET Core
Si la aplicación usa una versión preliminar de .NET Core y Snapshot Debugger se ha habilitado mediante el [panel de Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json) del portal, puede que Snapshot Debugger no se inicie. Siga primero las instrucciones del artículo sobre la [Habilitación de Snapshot Debugger para otros entornos](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json) para incluir el paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) con la aplicación ***además de*** habilitarlo mediante el [panel de Application Insights](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json).


## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>Actualización a la versión más reciente del paquete NuGet

Si se habilitó Snapshot Debugger con el [panel Application Insights en el portal](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), la aplicación ya debería estar ejecutando el paquete NuGet más reciente. Si se habilitó Snapshot Debugger mediante la inclusión del paquete NuGet [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector), use el administrador de paquetes NuGet de Visual Studio para asegurarse de que usa la versión más reciente de Microsoft.ApplicationInsights.SnapshotCollector. Puede encontrar notas de la versión en https://github.com/Microsoft/ApplicationInsights-Home/issues/167

## <a name="check-the-uploader-logs"></a>Comprobar los registros de usuario de carga

Después de crear una instantánea, se crea un archivo de minivolcado (.dmp) en el disco. Un proceso de usuario de carga independiente crea ese archivo de minivolcado y lo carga, junto con los archivos PDB asociados, en el almacenamiento de Snapshot Debugger de Application Insights. Después de cargar correctamente el minivolcado, se elimina del disco. Los archivos de registro del proceso de usuario de carga se conservan en el disco. En un entorno de App Service, puede encontrar estos registros en `D:\Home\LogFiles`. Use el sitio de administración de Kudu para App Service para buscar estos archivos de registro.

1. Abra la aplicación App Service en Azure Portal.
2. Haga clic en **Herramientas avanzadas** o busque **Kudu**.
3. Haga clic en **Ir**.
4. En el cuadro de lista desplegable **Consola de depuración**, seleccione **CMD**.
5. Haga clic en **LogFiles**.

Debería ver al menos un archivo con un nombre que comienza con `Uploader_` o `SnapshotUploader_` y una extensión `.log`. Haga clic en el icono adecuado para descargar los archivos de registro o abrirlos en un explorador.
El nombre de archivo incluye un sufijo único que identifica la instancia de App Service. Si la instancia de App Service se hospeda en más de un equipo, hay archivos de registro independientes para cada equipo. Cuando el usuario de carga detecta un nuevo archivo de minivolcado, se registra en el archivo de registro. Este es un ejemplo de una instantánea y una carga correctas:

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> El ejemplo anterior es de la versión 1.2.0 del paquete NuGet Microsoft.ApplicationInsights.SnapshotCollector. En versiones anteriores, el proceso de usuario de carga se llama `MinidumpUploader.exe` y el registro está menos detallado.

En el ejemplo anterior, la clave de instrumentación es `c12a605e73c44346a984e00000000000`. Este valor debe coincidir con la clave de instrumentación de la aplicación.
El minivolcado está asociado a una instantánea con el identificador `139e411a23934dc0b9ea08a626db16c5`. Puede usar este identificador más adelante para buscar la telemetría de excepciones asociada en Application Insights Analytics.

El usuario de carga busca nuevos archivos PDB una vez cada 15 minutos. Este es un ejemplo:

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

Para las aplicaciones que _no_ están hospedadas en App Service, los registros de usuario de carga están en la misma carpeta que los minivolcados: `%TEMP%\Dumps\<ikey>` (donde `<ikey>` es la clave de instrumentación).

## <a name="troubleshooting-cloud-services"></a>Solución de problemas de Cloud Services
Para los roles de Cloud Services, la carpeta temporal predeterminada puede ser demasiado pequeña para contener los archivos de minivolcado, dando lugar a pérdida de instantáneas.
El espacio necesario depende del espacio de trabajo total de la aplicación y el número de instantáneas simultáneas.
El espacio de trabajo de un rol web ASP.NET de 32 bits está normalmente entre 200 y 500 MB.
Debe disponer del espacio necesario para al menos dos instantáneas simultáneas.
Por ejemplo, si la aplicación usa 1 GB de espacio de trabajo total, debe asegurarse de que hay al menos 2 GB de espacio en disco para almacenar las instantáneas.
Siga estos pasos para configurar el rol del servicio en la nube con un recurso local dedicado para las instantáneas.

1. Agregue un nuevo recurso local a su servicio en la nube editando el archivo de definición (.csdef) del servicio en la nube. En el ejemplo siguiente se define un recurso llamado `SnapshotStore` con un tamaño de 5 GB.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. Modifique el código de inicio del rol para agregar una variable de entorno que apunte al recurso local `SnapshotStore`. En roles de trabajo, el código se debe agregar al método `OnStart` del rol:
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   En roles web (ASP.NET), el código se debe agregar al método `Application_Start` de la aplicación web:
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. Actualice el archivo ApplicationInsights.config del rol para reemplazar la ubicación de la carpeta temporal utilizada por `SnapshotCollector`
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>Invalidación de la carpeta Instantáneas

Cuando se inicia Snapshot Collector, intenta encontrar una carpeta del disco que sea adecuada para ejecutar el proceso del cargador de instantáneas. La carpeta elegida se conoce como carpeta de instantáneas.

Snapshot Collector comprueba unas cuantas ubicaciones conocidas, asegurándose de que tiene permisos para copiar los archivos binarios del cargador de instantáneas. Se usaron las siguientes variables de entorno:
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

Si no se puede encontrar una carpeta adecuada, Snapshot Collector envía un error que dice _"Could not find a suitable shadow copy folder."_ (No se pudo encontrar una carpeta de instantáneas adecuada).

Si se produce un error en la copia, Snapshot Collector envía un error `ShadowCopyFailed`.

Si no se puede iniciar el cargador, Snapshot Collector enviará un error `UploaderCannotStartFromShadowCopy`. El cuerpo del mensaje normalmente incluye `System.UnauthorizedAccessException`. Este error se suele producir porque la aplicación se ejecuta en una cuenta con permisos reducidos. La cuenta tiene permiso para escribir en la carpeta de instantáneas, pero no tiene permiso para ejecutar código.

Puesto que estos errores suelen ocurrir durante el inicio, normalmente van seguidos de un error `ExceptionDuringConnect` que dice _"Uploader failed to start"_ ("Error al iniciar el cargador").

Para resolver estos errores, puede especificar la carpeta de copia de instantáneas manualmente a través de la opción de configuración `ShadowCopyFolder`. Por ejemplo, use ApplicationInsights.config:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

O bien, si usa appsettings.json con una aplicación .NET Core:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Usar la búsqueda de Application Insights para buscar excepciones con instantáneas

Cuando se crea una instantánea, la excepción iniciada se etiqueta con un identificador de instantánea. Ese identificador de instantánea se incluye como una propiedad personalizada cuando se notifica la telemetría de excepciones a Application Insights. Mediante la **búsqueda** de Application Insights, puede buscar toda la telemetría con la propiedad personalizada `ai.snapshot.id`.

1. Vaya al recurso de Application Insights en Azure Portal.
2. Haga clic en **Buscar**.
3. En el cuadro de texto Buscar, escriba `ai.snapshot.id` y presione Entrar.

![Buscar telemetría con un identificador de instantánea en el portal](./media/snapshot-debugger/search-snapshot-portal.png)

Si esta búsqueda no devuelve ningún resultado, no se ha informado de ninguna instantánea a Application Insights para la aplicación en el intervalo de tiempo seleccionado.

Para buscar un identificador de instantánea específico en los registros de usuario de carga, escriba ese identificador en el cuadro Buscar. Si no se encuentra la telemetría para una instantánea que sabe que se cargó, siga estos pasos:

1. Compruebe que está viendo el recurso de Application Insights correcto comprobando la clave de instrumentación.

2. Mediante la marca de tiempo del registro del usuario de carga, ajuste el filtro de intervalo de tiempo de la búsqueda para cubrir ese intervalo de tiempo.

Si sigue sin ver una excepción con ese identificador de instantánea, significa que la excepción de telemetría no se ha notificado a Application Insights. Esta situación puede ocurrir si se bloqueó la aplicación después de que tomó la instantánea, pero antes de notificar la telemetría de excepción. En este caso, compruebe los registros de App Service en `Diagnose and solve problems` para ver si hay reinicios inesperados o excepciones no controladas.

## <a name="edit-network-proxy-or-firewall-rules"></a>Edición de reglas de firewall o proxy de red

Si la aplicación se conecta a Internet a través de un proxy o un firewall, es posible que tenga que editar las reglas para permitir que la aplicación se comunique con el servicio de Snapshot Debugger. A continuación, se incluye [una lista de puertos y direcciones IP que usa Snapshot Debugger](../../azure-monitor/app/ip-addresses.md#snapshot-debugger).
