---
title: Tutorial sobre el uso de la configuración dinámica de Azure App Configuration en una aplicación de ASP.NET Core | Microsoft Docs
description: En este tutorial aprenderá a actualizar dinámicamente los datos de configuración de aplicaciones de ASP.NET Core
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 02/24/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: 9eccb4ca505dac312dd22123a3585863c67f3ad7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359861"
---
# <a name="tutorial-use-dynamic-configuration-in-an-aspnet-core-app"></a>Tutorial: Uso de la configuración dinámica en una aplicación de ASP.NET Core

ASP.NET Core dispone de un sistema de configuración conectable que puede leer datos de configuración de diversos orígenes. Es capaz de controlar los cambios sobre la marcha sin hacer que una aplicación se reinicie. ASP.NET Core admite el enlace de valores de configuración a clases .NET fuertemente tipadas. Estas clases las inserta en el código mediante los diversos patrones `IOptions<T>`. Uno de estos patrones, en concreto `IOptionsSnapshot<T>`, recarga automáticamente la configuración de la aplicación cuando cambian los datos subyacentes. Puede insertar `IOptionsSnapshot<T>` en controladores de la aplicación para acceder a la configuración más reciente almacenada en Azure App Configuration.

También puede configurar la biblioteca cliente de ASP.NET Core de App Configuration para que actualice dinámicamente un conjunto de ajustes de configuración mediante un middleware. Mientras la aplicación web siga recibiendo solicitudes, los ajustes de configuración continuarán actualizándose con el almacén de configuración.

Con el fin de mantener la configuración actualizada y evitar demasiadas llamadas al almacén de configuración, se usa una caché para cada configuración. Hasta que haya expirado el valor almacenado en caché de una configuración, la operación no actualiza el valor, incluso cuando el valor cambia en el almacén de configuración. El tiempo de expiración predeterminado para cada solicitud es de 30 segundos, pero puede reemplazarse si es necesario.

Este tutorial le muestra cómo puede implementar las actualizaciones de configuración dinámica en el código. Se basa en la aplicación web que se introdujo en los inicios rápidos. Antes de continuar, finalice primero el tutorial [Creación de una aplicación ASP.NET Core con Azure App Configuration](./quickstart-aspnet-core-app.md).

Para realizar los pasos de este tutorial, puede usar cualquier editor de código. [Visual Studio Code](https://code.visualstudio.com/) es una excelente opción que está disponible en las plataformas Windows, macOS y Linux.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar la aplicación para actualizar su configuración en respuesta a los cambios en un almacén de App Configuration
> * Insertar la configuración más reciente en los controladores de la aplicación

## <a name="prerequisites"></a>Requisitos previos

Para realizar este tutorial, instale el [SDK de .NET Core](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Recarga de datos de App Configuration

1. Abra *Program.cs* y actualice el método `CreateWebHostBuilder` para agregar el método `config.AddAzureAppConfiguration()`.

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();

                config.AddAzureAppConfiguration(options =>
                {
                    options.Connect(settings["ConnectionStrings:AppConfig"])
                           .ConfigureRefresh(refresh =>
                           {
                               refresh.Register("TestApp:Settings:BackgroundColor")
                                      .Register("TestApp:Settings:FontColor")
                                      .Register("TestApp:Settings:Message")
                           });
                }
            })
            .UseStartup<Startup>();
    ```

    El método `ConfigureRefresh` se utiliza para especificar la configuración usada para actualizar los datos de configuración con el almacén de configuración de la aplicación cuando se desencadena una operación de actualización. Para desencadenar realmente una operación de actualización, es necesario configurar un middleware de actualización para que la aplicación actualice los datos de configuración cuando se produzca cualquier cambio.

2. Agregue un archivo *Settings.cs* que defina e implemente una nueva clase `Settings`.

    ```csharp
    namespace TestAppConfig
    {
        public class Settings
        {
            public string BackgroundColor { get; set; }
            public long FontSize { get; set; }
            public string FontColor { get; set; }
            public string Message { get; set; }
        }
    }
    ```

3. Abra *Startup.cs* y actualice el método `ConfigureServices` para enlazar los datos de configuración a la clase `Settings`.

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.Configure<Settings>(Configuration.GetSection("TestApp:Settings"));

        services.Configure<CookiePolicyOptions>(options =>
        {
            options.CheckConsentNeeded = context => true;
            options.MinimumSameSitePolicy = SameSiteMode.None;
        });

        services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
    }
    ```

4. Actualice el método `Configure` para agregar un middleware que permita actualizar los valores de la configuración mientras que la aplicación web de ASP.NET Core sigue recibiendo solicitudes.

    ```csharp
    public void Configure(IApplicationBuilder app, IHostingEnvironment env)
    {
        app.UseAzureAppConfiguration();
        app.UseMvc();
    }
    ```
    
    El middleware utiliza la configuración de actualización especificada en el método `AddAzureAppConfiguration` de `Program.cs` para desencadenar una actualización para cada solicitud recibida por la aplicación web de ASP.NET Core. Para cada solicitud, se desencadena una operación de actualización y la biblioteca cliente comprueba si el valor en caché de los ajustes de configuración registrados ha expirado. Para los valores en caché que han expirado, los valores de la configuración se actualizan con el almacén de configuración de aplicaciones, y los valores restantes permanecen sin cambios.
    
    > [!NOTE]
    > La hora de expiración de la caché predeterminada para un valor de configuración es de 30 segundos, pero puede reemplazarse por una llamada al método `SetCacheExpiration` en el inicializador de opciones que se pasa como argumento al método `ConfigureRefresh`.

## <a name="use-the-latest-configuration-data"></a>Uso de los datos de configuración más recientes

1. Abra *HomeController.cs* en el directorio de controladores y agregue una referencia al paquete `Microsoft.Extensions.Options`.

    ```csharp
    using Microsoft.Extensions.Options;
    ```

2. Actualice la clase `HomeController` para recibir `Settings` mediante la inserción de dependencias y asegúrese de usar sus valores.

    ```csharp
    public class HomeController : Controller
    {
        private readonly Settings _settings;
        public HomeController(IOptionsSnapshot<Settings> settings)
        {
            _settings = settings.Value;
        }

        public IActionResult Index()
        {
            ViewData["BackgroundColor"] = _settings.BackgroundColor;
            ViewData["FontSize"] = _settings.FontSize;
            ViewData["FontColor"] = _settings.FontColor;
            ViewData["Message"] = _settings.Message;

            return View();
        }
    }
    ```

3. Abra el archivo *Index.cshtml* en el directorio Views > Home, y sustituya su contenido por el siguiente script:

    ```html
    <!DOCTYPE html>
    <html lang="en">
    <style>
        body {
            background-color: @ViewData["BackgroundColor"]
        }
        h1 {
            color: @ViewData["FontColor"];
            font-size: @ViewData["FontSize"];
        }
    </style>
    <head>
        <title>Index View</title>
    </head>
    <body>
        <h1>@ViewData["Message"]</h1>
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Compilación y ejecución de la aplicación en un entorno local

1. Para compilar la aplicación mediante la CLI de .NET Core, ejecute el siguiente comando en el shell de comandos:

        dotnet build

2. Una vez que la compilación se haya realizado correctamente, ejecute el siguiente comando para ejecutar la aplicación web localmente:

        dotnet run

3. Inicie una ventana del explorador y vaya a `http://localhost:5000`, que es la dirección URL predeterminada de la aplicación web hospedada localmente.

    ![Inicio de la aplicación del artículo de inicio rápido en un entorno local](./media/quickstarts/aspnet-core-app-launch-local-before.png)

4. Inicie sesión en el [Azure Portal](https://portal.azure.com). Seleccione **Todos los recursos**y seleccione la instancia de almacén de App Configuration que creó en el inicio rápido.

5. Seleccione **Explorador de configuración** y actualice los valores de las claves siguientes:

    | Clave | Valor |
    |---|---|
    | TestApp:Settings:BackgroundColor | green |
    | TestApp:Settings:FontColor | lightGray |
    | TestApp:Settings:Message | Datos de Azure App Configuration: ahora con actualizaciones directas |

6. Actualice la página del explorador para ver los nuevos valores de configuración. Puede ser necesario actualizar más de una vez la página del explorador para que los cambios se reflejen.

    ![Inicio rápido de actualización de la aplicación en el entorno local](./media/quickstarts/aspnet-core-app-launch-local-after.png)
    
    > [!NOTE]
    > Como los valores de configuración se almacenan en caché con un tiempo de expiración predeterminado de 30 segundos, los cambios realizados en la configuración en el almacén de configuración de la aplicación solo se reflejarán en la aplicación web cuando la caché haya expirado.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha agregado una identidad de servicio administrada de Azure para optimizar el acceso a App Configuration y mejorar la administración de credenciales de su aplicación. Para más información sobre App Configuration, continúe con los ejemplos de la CLI de Azure.

> [!div class="nextstepaction"]
> [Ejemplos de CLI](./cli-samples.md)
