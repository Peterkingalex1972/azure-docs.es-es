---
title: Instrucciones detalladas de Monitor de estado de Azure v2 | Microsoft Docs
description: Instrucciones detalladas para empezar con el Monitor de estado v2. Supervise el rendimiento de los sitios web sin volver a implementarlos. Funciona con las aplicaciones web de ASP.NET hospedadas de forma local, en máquinas virtuales o en Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 02f4fa45cbfa619825478520961b6411459973e4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326275"
---
# <a name="status-monitor-v2-detailed-instructions"></a>Monitor de estado v2: Instrucciones detalladas

En este artículo se describe cómo incorporarse a la Galería de PowerShell y descargar el módulo ApplicationMonitor.
Se incluyen los parámetros más comunes que necesitará para empezar.
También proporcionamos instrucciones de descarga manual en caso de que no tenga acceso a Internet.

## <a name="get-an-instrumentation-key"></a>Obtención de una clave de instrumentación

Para empezar, necesita una clave de instrumentación. Para obtener más información, vea [Creación de recursos en Application Insights](create-new-resource.md#copy-the-instrumentation-key).

## <a name="run-powershell-as-admin-with-an-elevated-execution-policy"></a>Ejecución de PowerShell como administrador con una directiva de ejecución con privilegios elevados

### <a name="run-as-admin"></a>Ejecutar como administrador

PowerShell necesita permisos de nivel de administrador para hacer cambios en el equipo.
### <a name="execution-policy"></a>Directiva de ejecución
- Description: De forma predeterminada, la ejecución de scripts de PowerShell está deshabilitada. Le recomendamos que permita los scripts de RemoteSigned solo para el ámbito actual.
- Referencia: [Información sobre las directivas de ejecución](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/about/about_execution_policies?view=powershell-6) y [Set-ExecutionPolicy](
https://docs.microsoft.com/powershell/module/microsoft.powershell.security/set-executionpolicy?view=powershell-6
).
- Comando: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process`.
- Parámetro opcional:
    - `-Force`. Se omite el mensaje de confirmación.

**Ejemplos de errores**

```
Install-Module : The 'Install-Module' command was found in the module 'PowerShellGet', but the module could not be
loaded. For more information, run 'Import-Module PowerShellGet'.
    
Import-Module : File C:\Program Files\WindowsPowerShell\Modules\PackageManagement\1.3.1\PackageManagement.psm1 cannot
be loaded because running scripts is disabled on this system. For more information, see about_Execution_Policies at
https:/go.microsoft.com/fwlink/?LinkID=135170.
```


## <a name="prerequisites-for-powershell"></a>Requisitos previos para PowerShell

Ejecute el comando `$PSVersionTable` para auditar la instancia de PowerShell.
Este comando genera el siguiente resultado:


```
Name                           Value
----                           -----
PSVersion                      5.1.17763.316
PSEdition                      Desktop
PSCompatibleVersions           {1.0, 2.0, 3.0, 4.0...}
BuildVersion                   10.0.17763.316
CLRVersion                     4.0.30319.42000
WSManStackVersion              3.0
PSRemotingProtocolVersion      2.3
SerializationVersion           1.1.0.1
```

Estas instrucciones se han escrito y probado en un equipo que ejecuta Windows 10 y las versiones mencionadas anteriormente.

## <a name="prerequisites-for-powershell-gallery"></a>Requisitos previos de la Galería de PowerShell

Con estos pasos, se preparará el servidor para descargar los módulos de la Galería de PowerShell.

> [!NOTE] 
> La Galería de PowerShell es compatible con Windows 10, Windows Server 2016 y PowerShell 6.
> Para obtener información sobre las versiones anteriores, consulte [Instalación de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).


1. Ejecute PowerShell como administrador con una directiva de ejecución con privilegios elevados.
2. Instale el proveedor de paquetes NuGet.
    - Description: Necesita este proveedor para interactuar con repositorios basados en NuGet, como la Galería de PowerShell.
    - Referencia: [Install-PackageProvider](https://docs.microsoft.com/powershell/module/packagemanagement/install-packageprovider?view=powershell-6).
    - Comando: `Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201`.
    - Parámetros opcionales:
        - `-Proxy`. Especifica un servidor proxy para la solicitud.
        - `-Force`. Se omite el mensaje de confirmación.
    
    Recibirá este mensaje si NuGet no se ha configurado:
        
        NuGet provider is required to continue
        PowerShellGet requires NuGet provider version '2.8.5.201' or newer to interact with NuGet-based repositories. The NuGet
         provider must be available in 'C:\Program Files\PackageManagement\ProviderAssemblies' or
        'C:\Users\t\AppData\Local\PackageManagement\ProviderAssemblies'. You can also install the NuGet provider by running
        'Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force'. Do you want PowerShellGet to install and import
         the NuGet provider now?
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    
3. Configure la Galería de PowerShell como un repositorio de confianza.
    - Description: De forma predeterminada, la Galería de PowerShell no es un repositorio de confianza.
    - Referencia: [Set-PSRepository](https://docs.microsoft.com/powershell/module/powershellget/set-psrepository?view=powershell-6).
    - Comando: `Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted`.
    - Parámetro opcional:
        - `-Proxy`. Especifica un servidor proxy para la solicitud.

    Recibirá este mensaje si la Galería de PowerShell no es de confianza:

        Untrusted repository
        You are installing the modules from an untrusted repository. If you trust this repository, change its
        InstallationPolicy value by running the Set-PSRepository cmdlet. Are you sure you want to install the modules from
        'PSGallery'?
        [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is "N"):

    Puede confirmar este cambio y auditar todos los PSRepositories si ejecuta el comando `Get-PSRepository`.

4. Instale la versión más reciente de PowerShellGet.
    - Description: Este módulo contiene las herramientas que se usan para obtener otros módulos de la Galería de PowerShell. La versión 1.0.0.1 se distribuye con Windows 10 y Windows Server. Se requiere la versión 1.6.0 o posterior. Para determinar qué versión está instalada, ejecute el comando `Get-Command -Module PowerShellGet`.
    - Referencia: [Instalación de PowerShellGet](https://docs.microsoft.com/powershell/gallery/installing-psget).
    - Comando: `Install-Module -Name PowerShellGet`.
    - Parámetros opcionales:
        - `-Proxy`. Especifica un servidor proxy para la solicitud.
        - `-Force`. Omite la advertencia de que ya está instalado e instala la versión más reciente.

    Recibirá este error si no usa la versión más reciente de PowerShellGet:
    
        Install-Module : A parameter cannot be found that matches parameter name 'AllowPrerelease'.
        At line:1 char:20
        Install-Module abc -AllowPrerelease
                           ~~~~~~~~~~~~~~~~
            CategoryInfo          : InvalidArgument: (:) [Install-Module], ParameterBindingException
            FullyQualifiedErrorId : NamedParameterNotFound,Install-Module
    
5. Reinicie PowerShell. No puede cargar la nueva versión en la sesión actual. Las nuevas sesiones de PowerShell cargarán la versión más reciente de PowerShellGet.

## <a name="download-and-install-the-module-via-powershell-gallery"></a>Descarga e instalación del módulo a través de la Galería de PowerShell

Con estos pasos, descargará el módulo Az.ApplicationMonitor desde la Galería de PowerShell.

1. Asegúrese de que se cumplen todos los requisitos previos de la Galería de PowerShell.
2. Ejecute PowerShell como administrador con una directiva de ejecución con privilegios elevados.
3. Instale el módulo Az.ApplicationMonitor.
    - Referencia: [Install-Module](https://docs.microsoft.com/powershell/module/powershellget/install-module?view=powershell-6).
    - Comando: `Install-Module -Name Az.ApplicationMonitor`.
    - Parámetros opcionales:
        - `-Proxy`. Especifica un servidor proxy para la solicitud.
        - `-AllowPrerelease`. Permite la instalación de las versiones alfa y beta.
        - `-AcceptLicense`. Omite el mensaje para aceptar la licencia.
        - `-Force`. Omite la advertencia de repositorio de confianza.

## <a name="download-and-install-the-module-manually-offline-option"></a>Descarga e instalación manual del módulo (opción sin conexión)

Si por algún motivo no puede conectarse al módulo de PowerShell, puede descargar e instalar el módulo Az.ApplicationMonitor de forma manual.

### <a name="manually-download-the-latest-nupkg-file"></a>Descarga manual del archivo nupkg más reciente

1. Vaya a https://www.powershellgallery.com/packages/Az.ApplicationMonitor.
2. Seleccione la versión más reciente del archivo en la tabla **Historial de versiones**.
3. En **Opciones de instalación**, seleccione **Descarga manual**.

### <a name="option-1-install-into-a-powershell-modules-directory"></a>Opción 1: Instalación en un directorio de módulos de PowerShell
Instale el módulo de PowerShell que ha descargado manualmente en un directorio de PowerShell para que las sesiones de PowerShell lo puedan detectar.
Para obtener más información, consulte [Instalación de un módulo de PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).


#### <a name="unzip-nupkg-as-a-zip-file-by-using-expand-archive-v1010"></a>Descompresión de nupkg como un archivo ZIP mediante Expand-Archive (v1.0.1.0)

- Description: La versión base de Microsoft.PowerShell.Archive (v1.0.1.0) no puede descomprimir los archivos nupkg. Cambie el nombre del archivo con la extensión .zip.
- Referencia: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.3.0-alpha.nupkg"
    $pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
    $pathToNupkg | rename-item -newname $pathToZip
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
    ```

#### <a name="unzip-nupkg-by-using-expand-archive-v1100"></a>Descompresión de nupkg mediante Expand-Archive (v1.1.0.0)

- Description: Use una versión actual de Expand-Archive para descomprimir los archivos nupkg sin cambiar la extensión.
- Referencia: [Expand-Archive](https://docs.microsoft.com/powershell/module/microsoft.powershell.archive/expand-archive?view=powershell-6) y [Microsoft.PowerShell.Archive](https://www.powershellgallery.com/packages/Microsoft.PowerShell.Archive/1.1.0.0).
- Comando:

    ```
    $pathToNupkg = "C:\az.applicationmonitor.0.2.1-alpha.nupkg"
    $pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\az.applicationmonitor"
    Expand-Archive -LiteralPath $pathToNupkg -DestinationPath $pathInstalledModule
    ```

### <a name="option-2-unzip-and-import-nupkg-manually"></a>Opción 2: Descompresión e importación manual de nupkg
Instale el módulo de PowerShell que ha descargado manualmente en un directorio de PowerShell para que las sesiones de PowerShell lo puedan detectar.
Para obtener más información, consulte [Instalación de un módulo de PowerShell](https://docs.microsoft.com/powershell/developer/module/installing-a-powershell-module).

Si va a instalar el módulo en cualquier otro directorio, importe manualmente el módulo mediante [Import-Module](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/import-module?view=powershell-6).

> [!IMPORTANT] 
> Los archivos DLL se instalarán mediante las rutas de acceso relativas.
> Almacene el contenido del paquete en el directorio del entorno de ejecución previsto y confirme que los permisos de acceso permiten la lectura pero no la escritura.

1. Cambie la extensión por ".zip" y extraiga el contenido del paquete en el directorio de instalación que quiera.
2. Busque la ruta de acceso del archivo Az.ApplicationMonitor.psd1.
3. Ejecute PowerShell como administrador con una directiva de ejecución con privilegios elevados.
4. Cargue el módulo con el comando `Import-Module Az.ApplicationMonitor.psd1`.
    

## <a name="route-traffic-through-a-proxy"></a>Enrutamiento del tráfico a través de un proxy

Al supervisar un equipo en su intranet privada, deberá enrutar el tráfico HTTP a través de un proxy.

Los comandos de PowerShell para descargar e instalar Az.ApplicationMonitor desde la Galería de PowerShell admiten un parámetro `-Proxy`.
Consulte las instrucciones anteriores cuando escriba los scripts de instalación.

El SDK de Application Insights tendrá que enviar la telemetría de la aplicación a Microsoft. Le recomendamos que configure las opciones de configuración de proxy de la aplicación en el archivo web.config. Para obtener más información, consulte [Preguntas más frecuentes sobre Application Insights: Paso a través de proxy](https://docs.microsoft.com/azure/azure-monitor/app/troubleshoot-faq#proxy-passthrough).


## <a name="enable-monitoring"></a>Habilitar supervisión

Use el comando `Enable-ApplicationInsightsMonitoring` para habilitar la supervisión.

Consulte la [referencia de la API](status-monitor-v2-api-enable-monitoring.md) para obtener una descripción detallada de cómo usar este cmdlet.



## <a name="next-steps"></a>Pasos siguientes

 Vea la telemetría:

- [Explore las métricas](../../azure-monitor/app/metrics-explorer.md) para supervisar el rendimiento y el uso.
- [Busque en los eventos y los registros](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas.
- [Use Analytics](../../azure-monitor/app/analytics.md) para consultas más avanzadas.
- [Cree paneles](../../azure-monitor/app/overview-dashboard.md).

 Agregue más telemetría:

- [Cree pruebas web](monitor-web-app-availability.md) para asegurarse de que el sitio permanece activo.
- [Agregue telemetría de cliente web](../../azure-monitor/app/javascript.md) para ver las excepciones de código de la página web y para habilitar las llamadas de seguimiento.
- [Agregue el SDK de Application Insights al código](../../azure-monitor/app/asp-net.md) para que pueda insertar llamadas de seguimiento y registro.

Hacer más con el Monitor de estado v2:

- Use nuestra guía para [solucionar problemas](status-monitor-v2-troubleshoot.md) del Monitor de estado v2.
