---
title: Supervisar el rendimiento de Azure App Services | Microsoft Docs
description: Supervisión del rendimiento de aplicaciones de Azure App Services. Carga y tiempo de respuesta de gráfico, información de dependencia y establecer alertas en el rendimiento.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: mbullwin
ms.openlocfilehash: 4f296aae6c147b0d5209276dbd008a1207837cfd
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "67875199"
---
# <a name="monitor-azure-app-service-performance"></a>Supervisar el rendimiento de Azure App Service

Ahora, habilitar la supervisión en las aplicaciones web basadas en .NET y .NET Core que se ejecutan en [Azure App Service](https://docs.microsoft.com/azure/app-service/) es más fácil que nunca. Mientras que antes era necesario instalar manualmente una extensión de sitio, ahora el agente o la extensión más reciente están integrados en la imagen de App Service de manera predeterminada. En este artículo le guiaremos a través de la habilitación de la supervisión de Application Insights y proporcionaremos instrucciones preliminares para automatizar el proceso para implementaciones a gran escala.

> [!NOTE]
> Agregar manualmente una extensión de sitio de Application Insights a través de **Herramientas de desarrollo** > **Extensiones** está en desuso. Este método de instalación de extensiones dependía de actualizaciones manuales para cada nueva versión. La versión estable más reciente de la extensión ahora viene [preinstalada](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) como parte de la imagen de App Service. Los archivos se encuentran en `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` y se actualizan automáticamente con cada versión estable. Si sigue las instrucciones basadas en agentes para habilitar la supervisión a continuación, se quitará automáticamente la extensión en desuso.

## <a name="enable-application-insights"></a>Habilitación de Application Insights

Hay dos maneras de habilitar la supervisión de aplicaciones para las aplicaciones hospedadas de Azure App Service:

* **Supervisión de aplicaciones basadas en agentes** (ApplicationInsightsAgent).  
    * Este método es el más fácil de habilitar y no requiere ninguna configuración avanzada. A menudo se conoce como supervisión de "entorno en tiempo de ejecución". Para Azure App Services se recomienda la habilitación de este nivel de supervisión como mínimo y, a continuación, en función del escenario específico, puede evaluar si es necesaria una supervisión más avanzada a través de instrumentación manual.

* **Instrumentación manual de la aplicación mediante código** mediante la instalación del SDK de Application Insights.

    * Este enfoque es mucho más personalizable, pero requiere la [incorporación de una dependencia en los paquetes NuGet del SDK de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net). Este método también implica que el usuario tiene que administrar las actualizaciones a la versión más reciente de los paquetes.

    * Si necesita realizar llamadas de API personalizadas para supervisar eventos o dependencias no capturados de manera predeterminada con la supervisión basada en agentes, deberá usar este método. Consulte el [artículo API de Application Insights para eventos y métricas personalizados](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) para obtener más información.

> [!NOTE]
> Si se detecta tanto la supervisión basada en agentes como la instrumentación manual basada en SDK, solo se respetará la configuración de la instrumentación manual. Esto es para evitar datos duplicados del contenido enviado. Para obtener más información sobre este tema, consulte la [sección Solución de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting) a continuación.

## <a name="enable-agent-based-monitoring-for-net-applications"></a>Habilitación de la supervisión basada en agentes para aplicaciones .NET

> [!NOTE]
> No se admite la combinación de APPINSIGHTS_JAVASCRIPT_ENABLED y urlCompression. Para más información, consulte la explicación de la [sección Solución de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).


1. **Seleccione Application Insights** en el panel de control de Azure para el servicio de aplicaciones.

    ![En Configuración, elija Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Elija crear un nuevo recurso a menos que ya haya configurado un recurso de Application Insights para esta aplicación. 

     > [!NOTE]
     > Al hacer clic en **Aceptar** para crear el nuevo recurso, se le pedirá **Aplicar la configuración de supervisión**. Con la selección de **Continuar** se vinculará el nuevo recurso de Application Insights a su servicio de aplicaciones. Al hacerlo, también se **desencadenará un reinicio del servicio de aplicaciones**. 

     ![Instrumentación de la aplicación web](./media/azure-web-apps/create-resource-01.png)

2. Después de especificar qué recurso se debe usar, puede elegir cómo quiere que Application Insights recopile los datos de cada plataforma para la aplicación. La supervisión de aplicaciones ASP.NET está activada de manera predeterminada con dos niveles diferentes de la colección.

    ![Opciones de elección para cada plataforma](./media/azure-web-apps/choose-options-new.png)

   * El nivel de recopilación **Básico** de .NET ofrece funcionalidades esenciales de APM de instancia única.

   * El nivel de recopilación **Recomendado** de .NET:
       * Agrega las tendencias de uso de CPU, memoria y E/S.
       * Correlaciona los microservicios entre los límites de solicitud y dependencia.
       * Recopila las tendencias de uso y habilita la correlación entre los resultados de disponibilidad y las transacciones.
       * Recopila las excepciones no controladas por el proceso de host.
       * Mejora la precisión de las métricas de APM con carga, cuando se usa el muestreo.

3. Para configurar valores como el muestreo, que anteriormente se podía controlar mediante el archivo applicationinsights.config, ahora puede interactuar con esos mismos valores a través de la configuración de la aplicación con un prefijo correspondiente. 

    * Por ejemplo, para cambiar el porcentaje de muestreo inicial, puede crear una configuración de la aplicación de `MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` y un valor de `100`.

    * Para obtener la lista de valores de configuración de procesador de telemetría de muestreo adaptable compatibles, puede consultar el [código](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/master/src/ServerTelemetryChannel/AdaptiveSamplingTelemetryProcessor.cs) y la [documentación asociada](https://docs.microsoft.com/azure/azure-monitor/app/sampling).

## <a name="enable-agent-based-monitoring-for-net-core-applications"></a>Habilitación de la supervisión basada en agentes para aplicaciones .NET Core

Se admiten las siguientes versiones de .NET Core: ASP.NET Core 2.0, ASP.NET Core 2.1 y ASP.NET Core 2.2.

Actualmente, la orientación del marco de trabajo completo de .NET Core, la implementación independiente y ASP.NET Core 3.0 **no se admiten** con la supervisión basada en agentes o extensiones. (La [instrumentación manual](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core) a través de código funcionará en todos los escenarios anteriores).

1. **Seleccione Application Insights** en el panel de control de Azure para el servicio de aplicaciones.

    ![En Configuración, elija Application Insights](./media/azure-web-apps/settings-app-insights-01.png)

   * Elija crear un nuevo recurso a menos que ya haya configurado un recurso de Application Insights para esta aplicación. 

     > [!NOTE]
     > Al hacer clic en **Aceptar** para crear el nuevo recurso, se le pedirá **Aplicar la configuración de supervisión**. Con la selección de **Continuar** se vinculará el nuevo recurso de Application Insights a su servicio de aplicaciones. Al hacerlo, también se **desencadenará un reinicio del servicio de aplicaciones**. 

     ![Instrumentación de la aplicación web](./media/azure-web-apps/create-resource-01.png)

2. Después de especificar qué recurso se debe usar, puede elegir cómo quiere que Application Insights recopile los datos de cada plataforma para la aplicación. .NET Core ofrece la **recopilación recomendada** o **deshabilitada** para .NET Core 2.0, 2.1 y 2.2.

    ![Opciones de elección para cada plataforma](./media/azure-web-apps/choose-options-new-net-core.png)

## <a name="enable-client-side-monitoring-for-net-applications"></a>Habilitación de la supervisión del lado cliente para aplicaciones .NET

La supervisión de cliente está habilitada en ASP.NET. Para habilitar la supervisión de cliente:

* Seleccione **Configuración** >** **Configuración de la aplicación** **
   * En la configuración de la aplicación, agregue un nuevo **nombre de la configuración de la aplicación** y **valor**:

     Nombre: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `true`

   * **Guarde** la configuración y **reinicie** la aplicación.

![Captura de pantalla de la interfaz de usuario de la configuración de la aplicación](./media/azure-web-apps/appinsights-javascript-enabled.png)

Para deshabilitar la supervisión de cliente, quite el par clave-valor asociado de la configuración de la aplicación o establezca el valor en false.

## <a name="enable-client-side-monitoring-for-net-core-applications"></a>Habilitación de la supervisión del lado cliente para aplicaciones .NET Core

La supervisión de cliente está **habilitada de manera predeterminada** para las aplicaciones de .NET Core con la **recopilación recomendada**, independientemente de si está presente el valor "APPINSIGHTS_JAVASCRIPT_ENABLED" de la aplicación.

Si por algún motivo quiere deshabilitar la supervisión de cliente:

* Seleccione **Configuración** > **Configuración de la aplicación**
   * En la configuración de la aplicación, agregue un nuevo **nombre de la configuración de la aplicación** y **valor**:

     Nombre: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     Valor: `false`

   * **Guarde** la configuración y **reinicie** la aplicación.

![Captura de pantalla de la interfaz de usuario de la configuración de la aplicación](./media/azure-web-apps/appinsights-javascript-disabled.png)

## <a name="automate-monitoring"></a>Automatización de la supervisión

Con el fin de habilitar la recopilación de datos de telemetría con Application Insights, la configuración de la aplicación deberá configurarse:

   ![Configuración de la aplicación de App Service con la configuración de Application Insights disponible](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>Definiciones de los valores de configuración de la aplicación

|Nombre del valor de configuración de la aplicación |  Definición | Valor |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | Extensión principal; controla la supervisión en el entorno en tiempo de ejecución. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  Solo en el modo predeterminado; se habilitan las características esenciales para garantizar un rendimiento óptimo. | `default` o `recommended`. |
|InstrumentationEngine_EXTENSION_VERSION | Controla si se activa el motor `InstrumentationEngine` de reescritura binaria. Esta configuración tiene implicaciones de rendimiento y afecta a la hora de inicio o al arranque en frío. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | Controla si el texto de la tabla SQL y Azure se captura junto con las llamadas de dependencia. Advertencia de rendimiento: esta configuración requiere `InstrumentationEngine`. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Configuración de la aplicación de App Service con Azure Resource Manager

La configuración de la aplicación de App Service se puede administrar y configurar con [plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). Este método puede usarse al implementar nuevos recursos de App Service con la automatización de Azure Resource Manager o para modificar la configuración de los recursos existentes.

La estructura básica de JSON de la configuración de la aplicación para App Service se muestra a continuación:

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

Para obtener un ejemplo de una plantilla de Azure Resource Manager con la configuración de la aplicación establecida para Application Insights, esta [plantilla](https://github.com/Andrew-MSFT/BasicImageGallery) puede resultar útil, sobre todo en la sección que empieza a partir de la [línea 238](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238).

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Automatización de la creación de un recurso de Application Insights y vínculo a la instancia de App Service recién creada

Para crear una plantilla de Azure Resource Manager con toda la configuración predeterminada de Application Insights establecida, comience el proceso como si fuese a crear una nueva aplicación web con Application Insights habilitado.

Seleccione **Opciones de automatización**.

   ![Menú de creación de aplicación web de App Service](./media/azure-web-apps/create-web-app.png)

Esta opción genera la plantilla de Azure Resource Manager más reciente con toda la configuración necesaria establecida.

  ![Plantilla de aplicación web de App Service](./media/azure-web-apps/arm-template.png)

A continuación, se muestra un ejemplo; reemplace todas las instancias de `AppMonitoredSite` por el nombre del sitio:

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

> [!NOTE]
> La plantilla generará la configuración de la aplicación en modo "predeterminado". Este modo está optimizado para rendimiento, aunque puede modificar la plantilla para activar las características que prefiera.

### <a name="enabling-through-powershell"></a>Habilitación a través de PowerShell

Para habilitar la supervisión de la aplicación a través de PowerShell, solo se debe cambiar la configuración de la aplicación subyacente. A continuación, se muestra un ejemplo que habilita la supervisión de la aplicación para un sitio web denominado "AppMonitoredSite" en el grupo de recursos "AppMonitoredRG" y configura los datos para que se envíen a la clave de instrumentación "012345678-abcd-ef01-2345-6789abcd".

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} #preserve non Application Insights Application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # enable the ApplicationInsightsAgent
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>Actualización del agente o extensión de supervisión

### <a name="upgrading-from-versions-289-and-up"></a>Actualización de las versiones 2.8.9 y posteriores

La actualización de la versión 2.8.9 se realiza automáticamente, sin acciones adicionales. Los nuevos bits de supervisión se proporcionan en segundo plano para el servicio de aplicación de destino y se recogen en el reinicio de la aplicación.

Para comprobar qué versión de la extensión se ejecuta, visite `http://yoursitename.scm.azurewebsites.net/ApplicationInsights`

![Captura de pantalla de la ruta de dirección URL http://yoursitename.scm.azurewebsites.net/ApplicationInsights](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>Actualización de las versiones 1.0.0 a 2.6.5

A partir de la versión 2.8.9 se usa la extensión de sitio preinstalada. Si tiene una versión anterior, puede actualizarla de dos maneras:

* [Actualización mediante la habilitación a través del portal](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights). (Incluso si tiene la extensión Application Insights para Azure App Service instalada, la interfaz de usuario solo muestra el botón **Habilitar**. En segundo plano, la anterior extensión de sitio privada se quitará).

* [Actualización a través de PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell):

    1. Establezca la configuración de la aplicación para habilitar la extensión de sitio preinstalada ApplicationInsightsAgent. Consulte [Habilitación a través de PowerShell](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enabling-through-powershell).
    2. Quite manualmente la extensión de sitio privada denominada extensión de Application Insights para Azure App Service.

Si se realiza la actualización desde una versión anterior a la 2.5.1, compruebe que los archivos DLL de ApplicationInsigths se quitan de la carpeta de la ubicación de la aplicación. [Vea los pasos para la solución de problemas](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#troubleshooting).

## <a name="troubleshooting"></a>solución de problemas

A continuación, se muestra nuestra guía paso a paso de solución de problemas para la supervisión basada en extensiones o agentes para aplicaciones basadas en .NET y .NET Core que se ejecutan en Azure App Services.

> [!NOTE]
> Las aplicaciones de Java y Node.js solo se admiten en Azure App Services a través de la instrumentación manual basada en SDK y, por tanto, los pasos siguientes no se aplican a estos escenarios.

1. Compruebe que la aplicación se supervisa a través de `ApplicationInsightsAgent`.
    * Compruebe que la configuración de la aplicación `ApplicationInsightsAgent_EXTENSION_VERSION` se establece en un valor de "~2".
2. Asegúrese de que la aplicación cumple los requisitos que se deben supervisar.
    * Vaya a `https://yoursitename.scm.azurewebsites.net/ApplicationInsights`.

    ![Captura de pantalla de la página de resultados de https://yoursitename.scm.azurewebsites/applicationinsights](./media/azure-web-apps/app-insights-sdk-status.png)

    * Confirme que el `Application Insights Extension Status` es `Pre-Installed Site Extension, version 2.8.12.1527, is running.`.
        * Si no se está ejecutando, siga las [instrucciones para habilitar la supervisión de Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/azure-web-apps#enable-application-insights).

    * Confirme que el origen de estado existe y tiene el siguiente aspecto: `Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`.
        * Si no está presente un valor similar, significa que la aplicación no se admite o no se está ejecutando actualmente. Para asegurarse de que la aplicación se está ejecutando, intente visitar manualmente los puntos de conexión de la aplicación o la dirección URL de la aplicación, lo que permitirá que la información del entorno en tiempo de ejecución esté disponible.

    * Confirme que `IKeyExists` es `true`.
        * Si es false, agregue `APPINSIGHTS_INSTRUMENTATIONKEY con el GUID de iKey a la configuración de la aplicación.

    * Confirme que no hay ninguna entrada para `AppAlreadyInstrumented`, `AppContainsDiagnosticSourceAssembly` y `AppContainsAspNetTelemetryCorrelationAssembly`.
        * Si existe alguna de estas entradas, quite los siguientes paquetes de la aplicación: `Microsoft.ApplicationInsights`, `System.Diagnostics.DiagnosticSource` y `Microsoft.AspNet.TelemetryCorrelation`.

La tabla siguiente proporciona una explicación más detallada de lo que significan estos valores, sus causas subyacente y las correcciones recomendadas:

|Valor del problema|Explicación|Solución
|---- |----|---|
| `AppAlreadyInstrumented:true` | Este valor indica que la extensión ha detectado que algún aspecto del SDK ya está presente en la aplicación y se interrumpirá. Puede deberse a una referencia a `System.Diagnostics.DiagnosticSource`, `Microsoft.AspNet.TelemetryCorrelation` o `Microsoft.ApplicationInsights`.  | Quite las referencias. Algunas de estas referencias se agregan de manera predeterminada en determinadas plantillas de Visual Studio y las versiones anteriores de Visual Studio pueden agregar referencias a `Microsoft.ApplicationInsights`.
|`AppAlreadyInstrumented:true` | Si la aplicación tiene como destino .NET Core 2.1 o 2.2 y hace referencia al metapaquete [Microsoft.AspNetCore.All](https://www.nuget.org/packages/Microsoft.AspNetCore.All), se incorporará a Application Insights y la extensión se interrumpirá. | Se [recomienda](https://github.com/aspnet/Announcements/issues/287) que los clientes con .NET Core 2.1 o 2.2 usen el metapaquete Microsoft.AspNetCore.App en su lugar.|
|`AppAlreadyInstrumented:true` | Este valor también puede deberse a la presencia de los archivos DLL anteriores en la carpeta de la aplicación de una implementación anterior. | Limpie la carpeta de la aplicación para asegurarse de que se han quitado estos archivos DLL.|
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | Este valor indica que la extensión detectó referencias a `Microsoft.AspNet.TelemetryCorrelation` en la aplicación y se interrumpirá. | Quite la referencia.
|`AppContainsDiagnosticSourceAssembly**:true`|Este valor indica que la extensión detectó referencias a `System.Diagnostics.DiagnosticSource` en la aplicación y se interrumpirá.| Quite la referencia.
|`IKeyExists:false`|Este valor indica que la clave de instrumentación no está presente en la AppSetting, `APPINSIGHTS_INSTRUMENTATIONKEY`. Causas posibles: Es posible que los valores se hayan eliminado por accidente, que haya olvidado establecer los valores en el script de automatización, etc. | Asegúrese de que la configuración está presente en la configuración de la aplicación de App Service.

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>No se admite APPINSIGHTS_JAVASCRIPT_ENABLED ni urlCompression

Si usa APPINSIGHTS_JAVASCRIPT_ENABLED=true en casos donde el contenido está codificado, podría obtener errores como los siguientes: 

- Error de reescritura de dirección URL 500
- Error de módulo de reescritura de dirección URL 500.53 con el mensaje Las reglas de reescritura saliente no se pueden aplicar cuando el contenido de la respuesta HTTP está codificado ("gzip"). 

Esto se debe a que la configuración de la aplicación APPINSIGHTS_JAVASCRIPT_ENABLED está establecida en true y la codificación de contenido está presente al mismo tiempo. Este escenario aún no se admite. La solución consiste en quitar APPINSIGHTS_JAVASCRIPT_ENABLED de la configuración de la aplicación. Esto significa que si la instrumentación de JavaScript del lado cliente o explorador sigue siendo necesaria, se necesitan referencias del SDK manuales para las páginas web. Siga las [instrucciones](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup) para la instrumentación manual con el SDK de JavaScript.

Para obtener la información más reciente sobre la extensión o el agente de Application Insights, consulte las [notas de la versión](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md).

## <a name="next-steps"></a>Pasos siguientes
* [Ejecute el generador de perfiles en la aplicación activa](../app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample): supervisar Azure Functions con Application Insights
* [Diagnósticos de Microsoft Azure](../platform/diagnostics-extension-to-application-insights.md) para enviar este tipo de información a Application Insights.
* [Supervise las métricas del estado del servicio](../platform/data-platform.md) para asegurarse de que el servicio está disponible y responde adecuadamente.
* [Reciba notificaciones de alerta](../platform/alerts-overview.md) cada vez que se produzcan eventos de operaciones o las métricas traspasen un umbral.
* Use [aplicaciones y páginas web de Application Insights para JavaScript](javascript.md) para obtener la telemetría del cliente de los exploradores que visitan una página web.
* [Configure pruebas web de disponibilidad](monitor-web-app-availability.md) para recibir una alerta si el sitio deja de estar activo.
