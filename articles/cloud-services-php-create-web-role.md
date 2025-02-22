---
title: Creación de roles web y de trabajo de Azure para PHP
description: Una guía para crear roles web y de trabajo de PHP en un servicio en la nube de Azure y configurar el tiempo en ejecución de PHP.
services: ''
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 9f7ccda0-bd96-4f7b-a7af-fb279a9e975b
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.openlocfilehash: 83834104dd73e4381947903196ad35c3497b64a1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60337568"
---
# <a name="create-php-web-and-worker-roles"></a>Creación de roles web y de trabajo para PHP

## <a name="overview"></a>Información general

En esta guía se explica cómo crear roles de trabajo o web de PHP en un entorno de desarrollo de Windows, elegir una versión específica de PHP de versiones "integradas" disponibles, cambiar la configuración de PHP, habilitar extensiones y, por último, implementar en Azure. También se describe cómo configurar un rol web o de trabajo para usar un tiempo de ejecución de PHP (con las extensiones y la configuración personalizada) proporcionado por el usuario.

Azure proporciona tres modelos de proceso para ejecutar aplicaciones: Azure App Service, Azure Virtual Machines y Azure Cloud Services. Los tres modelos admiten PHP. Cloud Services, que incluye roles web y de trabajo, ofrece el modelo *Plataforma como servicio (PaaS)* . Dentro de un servicio en la nube, un rol web proporciona un servidor web de Internet Information Services (IIS) dedicado para hospedar aplicaciones web front-end. Un rol de trabajo puede ejecutar tareas asincrónicas, de ejecución prolongada o perpetuas, independientes de la entrada o la interacción del usuario.

Para más información sobre estas opciones, consulte [Cálculo de las opciones de hospedaje proporcionadas por Azure](cloud-services/cloud-services-choose-me.md).

## <a name="download-the-azure-sdk-for-php"></a>Descarga del SDK de Azure para PHP

El [SDK de Azure para PHP](php-download-sdk.md) tiene varios componentes. En este artículo se usarán dos de ellos: Azure PowerShell y emuladores de Azure. Estos dos componentes se pueden instalar a través del instalador de la plataforma web de Microsoft. Para obtener más información, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).

## <a name="create-a-cloud-services-project"></a>Creación de un proyecto de Cloud Services

El primer paso para crear un rol web o de trabajo de PHP es crear un proyecto del servicio de Azure. El primer paso para crear un rol web o de trabajo de PHP es crear un proyecto del servicio de Azure. Un proyecto del servicio de Azure sirve como un contenedor lógico para roles web y de trabajo y contiene los archivos de [definición del servicio (.csdef)] y [configuración del servicio (.cscfg)] del proyecto.

Para crear un proyecto nuevo del servicio de Azure, ejecute Azure PowerShell como administrador y, a continuación, ejecute el comando siguiente:

    PS C:\>New-AzureServiceProject myProject

Este comando creará un directorio nuevo (`myProject`) al que puede agregar roles web y de trabajo.

## <a name="add-php-web-or-worker-roles"></a>de roles web y de trabajo de PHP

Para agregar un rol web de PHP a un proyecto, ejecute el siguiente comando desde el directorio raíz del proyecto:

    PS C:\myProject> Add-AzurePHPWebRole roleName

Para un rol de trabajo, use este comando:

    PS C:\myProject> Add-AzurePHPWorkerRole roleName

> [!NOTE]
> El `roleName` es opcional. Si se omite, el nombre de rol se generará automáticamente. El primer rol web creado será `WebRole1`, el segundo será `WebRole2` y así sucesivamente. El primer rol de trabajo creado será `WorkerRole1`, el segundo será `WorkerRole2` y así sucesivamente.
>
>

## <a name="specify-the-built-in-php-version"></a>de la versión de PHP integrada

Al agregar un rol web o de trabajo de PHP a un proyecto, los archivos de configuración del proyecto se modifican para que PHP se instale en cada instancia de trabajo o web de la aplicación cuando se implementa. Para ver la versión de PHP que se instalará de forma predeterminada, ejecute el comando siguiente:

    PS C:\myProject> Get-AzureServiceProjectRoleRuntime

La salida del comando anterior tendrá un aspecto similar al que se muestra a continuación. En este ejemplo, la marca `IsDefault` se define en `true` para PHP 5.3.17, indicando que será la versión de PHP instalada de forma predeterminada.

```
Runtime Version     PackageUri                      IsDefault
------- -------     ----------                      ---------
Node 0.6.17         http://nodertncu.blob.core...   False
Node 0.6.20         http://nodertncu.blob.core...   True
Node 0.8.4          http://nodertncu.blob.core...   False
IISNode 0.1.21      http://nodertncu.blob.core...   True
Cache 1.8.0         http://nodertncu.blob.core...   True
PHP 5.3.17          http://nodertncu.blob.core...   True
PHP 5.4.0           http://nodertncu.blob.core...   False
```

Puede definir la versión del tiempo de ejecución de PHP con cualquier versión de PHP de la lista. Por ejemplo, para definir la versión de PHP (para un rol con nombre `roleName`) como 5.4.0, use el comando siguiente:

    PS C:\myProject> Set-AzureServiceProjectRole roleName php 5.4.0

> [!NOTE]
> Las versiones PHP disponibles pueden cambiar en el futuro.
>
>

## <a name="customize-the-built-in-php-runtime"></a>del tiempo de ejecución de PHP integrado

Tiene el control total de la configuración del tiempo de ejecución de PHO instalado al seguir los pasos anteriores, incluida la modificación de la configuración de `php.ini` y la habilitación de las extensiones.

Para personalizar el tiempo de ejecución de PHP integrado, siga estos pasos:

1. Agregue una carpeta nueva, con el  nombre `php`, al directorio `bin` del rol web. Si se trata de un rol de trabajo, agréguelo al directorio raíz del rol.
2. En la carpeta `php`, cree otra carpeta denominada `ext`. Coloque los archivos de extensión `.dll` (por ejemplo, `php_mongo.dll`) que desee habilitar en esta carpeta.
3. Agregue un archivo `php.ini` a la carpeta `php`. Habilite todas las extensiones personalizadas y defina todas las directivas de PHP en este archivo. Por ejemplo, si desea activar `display_errors` y habilitar la extensión `php_mongo.dll`, el contenido del archivo `php.ini` será el siguiente:

        display_errors=On
        extension=php_mongo.dll

> [!NOTE]
> Cualquier configuración que no defina explícitamente en el archivo `php.ini` que proporcione se definirá automáticamente con los valores predeterminados. Sin embargo, tenga en cuenta que puede agregar un archivo `php.ini` completo.
>
>

## <a name="use-your-own-php-runtime"></a>del tiempo de ejecución de PHP propio

En algunos casos, en lugar de seleccionar un tiempo de ejecución de PHP integrado y configurarlo como se ha descrito anteriormente, puede proporcionar el tiempo de ejecución de PHP propio. Por ejemplo, puede usar el mismo tiempo de ejecución de PHP en un rol web o de trabajo que use en el entorno de desarrollo. De esta forma, garantiza de forma más fácil que la aplicación no cambiará su comportamiento en el entorno de producción.

### <a name="configure-a-web-role-to-use-your-own-php-runtime"></a>Configuración de un rol web para usar un tiempo de ejecución de PHP propio

Para configurar un rol web para usar un tiempo de ejecución de PHP proporcionado por el usuario, siga estos pasos:

1. Cree un proyecto del servicio de Azure y agregue un rol web de PHP según se describe anteriormente en este tema.
2. Cree una carpeta `php` en la carpeta `bin` que se encuentra en el directorio raíz del rol web y, a continuación, agregue el tiempo de ejecución de PHP (todos los binarios, archivos de configuración, subcarpetas, etc.) a la carpeta `php`.
3. (OPCIONAL) Si el entorno en tiempo de ejecución de PHP usa los [Controladores de Microsoft para PHP en SQL Server][sqlsrv drivers], tendrá que configurar el rol web para instalar [SQL Server Native Client 2012][sql native client] cuando se aprovisione. Para ello, agregue el [instalador sqlncli.msi x64] a la carpeta `bin` en el directorio raíz del rol web. El script de inicio descrito en el siguiente paso ejecutará el instalador silenciosamente cuando se aprovisione el rol. Si el tiempo de ejecución de PHP no usa los controladores de Microsoft para PHP en SQL Server, puede eliminar la línea siguiente del script que se muestra en el paso siguiente:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina una tarea de inicio que configura [Internet Information Services (IIS)][iis.net] para que use el entorno en tiempo de ejecución de PHP a fin de que controle las solicitudes para las páginas `.php`. Para ello, abra el archivo `setup_web.cmd` (en el archivo `bin` del directorio raíz del rol web) en un editor de texto y reemplace su contenido por el script siguiente:

    ```cmd
    @ECHO ON
    cd "%~dp0"

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    SET PHP_FULL_PATH=%~dp0php\php-cgi.exe
    SET NEW_PATH=%PATH%;%RoleRoot%\base\x86

    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%',maxInstances='12',idleTimeout='60000',activityTimeout='3600',requestTimeout='60000',instanceMaxRequests='10000',protocol='NamedPipe',flushNamedPipe='False']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PATH',value='%NEW_PATH%']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /+"[fullPath='%PHP_FULL_PATH%'].environmentVariables.[name='PHP_FCGI_MAX_REQUESTS',value='10000']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/handlers /+"[name='PHP',path='*.php',verb='GET,HEAD,POST',modules='FastCgiModule',scriptProcessor='%PHP_FULL_PATH%',resourceType='Either',requireAccess='Script']" /commit:apphost
    %WINDIR%\system32\inetsrv\appcmd.exe set config -section:system.webServer/fastCgi /"[fullPath='%PHP_FULL_PATH%'].queueLength:50000"
    ```
5. Agregue los archivos de la aplicación al directorio raíz del rol web. Será el directorio raíz del servidor web.
6. Publique la aplicación como se describe en la sección [Publicación de la aplicación](#publish-your-application) más adelante.

> [!NOTE]
> El script `download.ps1` (en la carpeta `bin` del directorio raíz del rol web) se puede eliminar después de completar los pasos descritos anteriormente para usar el tiempo de ejecución de PHP propio.
>
>

### <a name="configure-a-worker-role-to-use-your-own-php-runtime"></a>Configuración de un rol de trabajo para usar un tiempo de ejecución de PHP propio

Para configurar un rol de trabajo para usar un tiempo de ejecución de PHP propio, siga estos pasos:

1. Cree un proyecto del servicio de Azure y agregue un rol de trabajo de PHP según se describe anteriormente en este tema.
2. Cree una carpeta `php` en el directorio raíz del rol de trabajo y, a continuación, agregue el tiempo de ejecución de PHP (todos los binarios, archivos de configuración, subcarpetas, etc.) a la carpeta `php`.
3. .(OPCIONAL) Si el entorno en tiempo de ejecución de PHP usa los [Controladores de Microsoft para PHP en SQL Server][sqlsrv drivers], tendrá que configurar el rol de trabajo para instalar [SQL Server Native Client 2012][sql native client] cuando se aprovisione. Para ello, agregue el [instalador sqlncli.msi x64] al directorio raíz del rol de trabajo. El script de inicio descrito en el siguiente paso ejecutará el instalador silenciosamente cuando se aprovisione el rol. Si el tiempo de ejecución de PHP no usa los controladores de Microsoft para PHP en SQL Server, puede eliminar la línea siguiente del script que se muestra en el paso siguiente:

        msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES
4. Defina una tarea de inicio que agregue el archivo ejecutable `php.exe` a la variable de entorno PATH del rol de trabajo cuando se aprovisiona el rol. Para ello, abra el archivo `setup_worker.cmd` (en el directorio raíz del rol de trabajo) en un editor de texto y reemplace su contenido por el siguiente script:

    ```cmd
    @echo on

    cd "%~dp0"

    echo Granting permissions for Network Service to the web root directory...
    icacls ..\ /grant "Network Service":(OI)(CI)W
    if %ERRORLEVEL% neq 0 goto error
    echo OK

    if "%EMULATED%"=="true" exit /b 0

    msiexec /i sqlncli.msi /qn IACCEPTSQLNCLILICENSETERMS=YES

    setx Path "%PATH%;%~dp0php" /M

    if %ERRORLEVEL% neq 0 goto error

    echo SUCCESS
    exit /b 0

    :error

    echo FAILED
    exit /b -1
    ```
5. Agregue los archivos de la aplicación al directorio raíz del rol de trabajo.
6. Publique la aplicación como se describe en la sección [Publicación de la aplicación](#publish-your-application) más adelante.

## <a name="run-your-application-in-the-compute-and-storage-emulators"></a>Ejecución de la aplicación en los emuladores de proceso y de almacenamiento

Los emuladores de Azure ofrecen un entorno local en el que puede probar la aplicación de Azure antes de implementarla en la nube. Existen algunas diferencias entre los emuladores y el entorno de Azure. Para comprender esto mejor, consulte [Uso del emulador de almacenamiento de Azure para desarrollo y pruebas](storage/common/storage-use-emulator.md).

Tenga en cuenta que debe tener instalado PHP localmente para usar el emulador de proceso. El emulador de proceso usará la instalación de PHP local para ejecutar la aplicación.

Para ejecutar el proyecto en los emuladores, ejecute el comando siguiente desde el directorio raíz del proyecto:

    PS C:\MyProject> Start-AzureEmulator

Verá una salida similar a esto:

    Creating local package...
    Starting Emulator...
    Role is running at http://127.0.0.1:81
    Started

Puede ver la aplicación en ejecución en el emulador; para ello, abra un explorador web y vaya a la dirección local que aparece en la salida (`http://127.0.0.1:81` en la salida del ejemplo anterior).

Para detener los emuladores, ejecute este comando:

    PS C:\MyProject> Stop-AzureEmulator

## <a name="publish-your-application"></a>Publicación de la aplicación

Para publicar la aplicación, primero tiene que importar la configuración de la publicación con el cmdlet [Import-AzurePublishSettingsFile](https://docs.microsoft.com/powershell/module/servicemanagement/azure/import-azurepublishsettingsfile) . Después, puede publicar la aplicación con el cmdlet [Publish-AzureServiceProject](https://docs.microsoft.com/powershell/module/servicemanagement/azure/publish-azureserviceproject) . Para obtener información sobre cómo iniciar sesión, consulte [Cómo instalar y configurar Azure PowerShell](/powershell/azure/overview).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte el [Centro para desarrolladores de PHP](https://azure.microsoft.com/develop/php/).

[install ps and emulators]: https://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[definición del servicio (.csdef)]: https://msdn.microsoft.com/library/windowsazure/ee758711.aspx
[configuración del servicio (.cscfg)]: https://msdn.microsoft.com/library/windowsazure/ee758710.aspx
[iis.net]: https://www.iis.net/
[sql native client]: https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation
[sqlsrv drivers]: https://php.net/sqlsrv
[instalador sqlncli.msi x64]: https://go.microsoft.com/fwlink/?LinkID=239648
