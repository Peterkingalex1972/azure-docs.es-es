---
title: 'Solución de problemas cuando no hay datos: Application Insights para .NET'
description: ¿No ve los datos en Azure Application Insights? Pruebe aquí.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/23/2018
ms.author: mbullwin
ms.openlocfilehash: 2966f90dcb381e439c00a6540ef9a01bd24f8743
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561188"
---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Solución de problemas cuando no hay datos: Application Insights para .NET
## <a name="some-of-my-telemetry-is-missing"></a>Falta parte de mi telemetría
*En Application Insights, solo veo una fracción de los eventos generados por mi aplicación.*

* Si observa la misma fracción constantemente, es probable que se deba al [muestreo](../../azure-monitor/app/sampling.md)adaptable. Para confirmarlo, abra Búsqueda (desde la hoja de información general) y examine una instancia de una solicitud u otro evento. En la parte inferior de la sección Propiedades, haga clic en "..." para obtener todos los detalles de la propiedad. Si Recuento de solicitudes > 1, hay un muestreo en curso.
* De lo contrario, es posible que se esté aproximando a un [límite de velocidad de datos](../../azure-monitor/app/pricing.md#limits-summary) para su plan de precios. Estos límites se aplican por minuto.

*Experimento una pérdida de datos aleatoria.*

* Compruebe si está experimentando pérdida de datos en el [canal de telemetría](telemetry-channels.md#does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost).

* Busque problemas conocidos en el [repositorio de Github](https://github.com/Microsoft/ApplicationInsights-dotnet/issues) del canal de telemetría.

*Experimento pérdida de datos en la aplicación de consola o en la aplicación web cuando la aplicación está a punto de detenerse.*

* El canal del SDK mantiene los datos de telemetría en el búfer y los envía en lotes. Si se está cerrando la aplicación, es posible que deba llamar explícitamente a [Flush()](api-custom-events-metrics.md#flushing-data). El comportamiento de `Flush()` depende del [canal](telemetry-channels.md#built-in-telemetry-channels) real utilizado.

## <a name="no-data-from-my-server"></a>No hay datos de mi servidor
*He instalado mi aplicación en el servidor web y ahora no veo ninguna telemetría procedente de ella. Funcionaba correctamente en mi equipo de desarrollo.*

* Probablemente sea un problema de firewall. [Configure excepciones de firewall para que Application Insights envíe datos](../../azure-monitor/app/ip-addresses.md).
* Es posible que falten algunos requisitos previos en el servidor IIS: Extensibilidad de .NET 4.5 y ASP.NET 4.5.

*He [instalado el monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md) en el servidor web para supervisar las aplicaciones existentes. No se ve ningún resultado.*

* Consulte [Solución de problemas del Monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md#troubleshoot).

## <a name="q01"></a>No hay ninguna opción "Agregar Application Insights" en Visual Studio
*Cuando hago clic con el botón derecho en un proyecto existente en el Explorador de soluciones, no veo ninguna opción de Application Insights.*

* No todos los tipos de proyectos .NET son compatibles con las herramientas. Se admiten los proyectos WCF y Web. Para otros tipos de proyecto como aplicaciones de escritorio o de servicio, puede [agregar manualmente un SDK de Application Insights al proyecto](../../azure-monitor/app/windows-desktop.md).
* Asegúrese de que tiene [Visual Studio 2013 Update 3 o posterior](https://docs.microsoft.com/visualstudio/releasenotes/vs2013-update3-rtm-vs). Viene con Developer Analytics Tools preinstalado, que ofrece el SDK de Application Insights.
* Seleccione **Herramientas**, **Extensiones y actualizaciones** y compruebe que **Developer Analytics Tools** está instalado y habilitado. Si es así, haga clic en **Actualizaciones** para ver si hay alguna disponible.
* Abra el cuadro de diálogo Nuevo proyecto y elija Aplicación web ASP.NET. Si ve la opción Application Insights aquí, es que las herramientas están instaladas. Si no es así, intente desinstalar y volver a instalar Developer Analytics Tools.

## <a name="q02"></a>Error al agregar Application Insights
*Cuando intento agregar Application Insights a un proyecto existente, veo un mensaje de error.*

Causas probables:

* se produce un error de comunicación con el portal de Application Insights,
* hay algún problema con su cuenta de Azure,
* Solo tiene [acceso de lectura a la suscripción o el grupo en que ha tratado de crear el recurso nuevo](../../azure-monitor/app/resources-roles-access-control.md).

Solución:

* Compruebe que ha proporcionado las credenciales de inicio de sesión para la cuenta de Azure correcta.
* En el explorador, compruebe que tiene acceso al [portal de Azure](https://portal.azure.com). Abra Configuración y compruebe si hay alguna restricción.
* [Agregar Application Insights a un proyecto existente](../../azure-monitor/app/asp-net.md): en el Explorador de soluciones, haga clic con el botón derecho en el proyecto y elija “Agregar Application Insights”.

## <a name="emptykey"></a>Aparece el mensaje de error "La clave de instrumentación no puede estar vacía".
Parece que algo salió mal durante la instalación de Application Insights o puede ser un adaptador del registro.

En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y elija **Application Insights > Configurar Application Insights**. Aparecerá un cuadro de diálogo que le invita a iniciar sesión en Azure y a crear un recurso de Application Insights, o a volver a utilizar uno existente.

## <a name="NuGetBuild"></a> "Faltan paquetes NuGet" en el servidor de compilación
*Todo se compila correctamente al depurar en el equipo de desarrollo pero aparece un error de NuGet en el servidor de compilación.*

Consulte [Restauración de paquetes NuGet](https://docs.nuget.org/Consume/Package-Restore) y [Restauración automática de paquetes](https://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Falta el comando de menú para abrir Application Insights desde Visual Studio
*Al hacer clic con el botón derecho en el Explorador de soluciones del proyecto, no se ve ningún comando de Application Insights o no se ve un comando Abrir Application Insights.*

Causas probables:

* Si se ha creado el recurso de Application Insights manualmente o si el proyecto es de un tipo que no es compatible con las herramientas de Application Insights.
* Developer Analytics Tools está deshabilitado en Visual Studio.
* Visual Studio es anterior a la actualización 3 de 2013.

Solución:

* Compruebe que la versión de Visual Studio sea la actualización 3 de 2013 o posterior.
* Seleccione **Herramientas**, **Extensiones y actualizaciones** y compruebe que **Developer Analytics Tools** está instalado y habilitado. Si es así, haga clic en **Actualizaciones** para ver si hay alguna disponible.
* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto. Si ve el comando **Application Insights > Configurar Application Insights**, úselo para conectar el proyecto al recurso en el servicio de Application Insights.

De lo contrario, el tipo de proyecto no es directamente compatible con Developer Analytics Tools. Para ver la telemetría, inicie sesión en el [Portal de Azure](https://portal.azure.com), elija Application Insights en la barra de navegación de la izquierda y seleccione la aplicación.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>'Acceso denegado' al abrir Application Insights desde Visual Studio
*El comando de menú 'Abrir Application Insights' lleva al Portal de Azure pero aparece un error de "acceso denegado".*

El inicio de sesión de Microsoft que utilizó por última vez en el explorador predeterminado no tiene acceso al [recurso que se creó cuando se agregó Application Insights a esta aplicación](../../azure-monitor/app/asp-net.md). Hay dos causas probables:

* Tiene más de una cuenta de Microsoft: ¿quizás tenga una cuenta profesional y una cuenta personal de Microsoft? El inicio de sesión que se utilizó por última vez en el explorador predeterminado era para una cuenta distinta de la que tiene acceso para [agregar Application Insights al proyecto](../../azure-monitor/app/asp-net.md).
  * Solución: haga clic en su nombre en la parte superior derecha de la ventana del explorador y cierre la sesión. A continuación, inicie sesión con la cuenta que tiene acceso. En la barra de navegación izquierda, haga clic en Application Insights y seleccione la aplicación.
* Otro usuario ha agregado Application Insights al proyecto y se ha olvidado de proporcionarle [acceso al grupo de recursos](../../azure-monitor/app/resources-roles-access-control.md) en el que se creó.
  * Solución: si este usuario utiliza una cuenta de organización, puede agregarle al equipo, o bien, puede concederle acceso individual al grupo de recursos.

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'No se encuentra ningún activo' al abrir Application Insights desde Visual Studio
*El comando de menú 'Abrir Application Insights' lleva al Portal de Azure pero aparece un error de "no se encuentra ningún activo".*

Causas probables:

* Se ha eliminado el recurso de Application Insights para su aplicación o
* la clave de instrumentación se ha establecido o modificado en ApplicationInsights.config editándola directamente, sin actualizar el archivo de proyecto.

La clave de instrumentación en ApplicationInsights.config controla dónde se envía la telemetría. Una línea en el archivo de proyecto controla qué recurso se abre al utilizar el comando de Visual Studio.

Solución:

* En el Explorador de soluciones, haga clic con el botón derecho en el proyecto y elija Application Insights, Configurar Application Insights. En el cuadro de diálogo, puede elegir enviar la telemetría a un recurso existente o crear uno nuevo. O:
* Abra el recurso directamente. Inicie sesión en el [Portal de Azure](https://portal.azure.com), elija Application Insights en la barra de navegación de la izquierda y seleccione la aplicación.

## <a name="where-do-i-find-my-telemetry"></a>¿Dónde se encuentra la telemetría?
*He iniciado sesión en [Microsoft Azure Portal](https://portal.azure.com) y estoy mirando el panel de inicio de Azure. ¿Dónde se encuentran los datos de Application Insights?*

* En la barra de navegación izquierda, haga clic en Application Insights y luego en el nombre de la aplicación. Si no tiene ahí ningún proyecto, es necesario [agregar o configurar Application Insights en el proyecto web](../../azure-monitor/app/asp-net.md).  
  Ahí verá algunos gráficos de resumen. Puede hacer clic en ellos para ver su contenido con más detalle.
* En Visual Studio, al depurar la aplicación, haga clic en el botón de Application Insights.

## <a name="q03"></a> No hay datos de servidor (o ningún tipo de datos)
*Se ejecuta la aplicación y se abre el servicio de Application Insights en Microsoft Azure pero todos los gráficos muestran 'Obtenga información sobre cómo recopilar...' o 'No está configurado.'* O bien, *solo vista de página y datos de usuario, pero no hay datos de servidor.*

* Ejecute la aplicación en modo de depuración en Visual Studio (F5). Utilice la aplicación para generar telemetría. Compruebe que puede ver los eventos registrados en la ventana de resultados de Visual Studio.  
  ![](./media/asp-net-troubleshoot-no-data/output-window.png)
* En el portal de Application Insights, abra [Diagnostic Search](../../azure-monitor/app/diagnostic-search.md)(Búsqueda de diagnóstico). Los datos suelen aparecer aquí en primer lugar.
* Haga clic en el botón Actualizar. La hoja se actualiza periódicamente, pero también puede hacerlo manualmente. El intervalo de actualización es mayor para intervalos de tiempo mayores.
* Compruebe las claves de instrumentación coincidan. En la hoja principal de la aplicación en el portal de Application Insights, en la lista desplegable de **Essentials**, vea la **Clave de instrumentación**. A continuación, en el proyecto de Visual Studio, abra ApplicationInsights.config y busque `<instrumentationkey>`. Compruebe que las dos claves sean iguales. Si no es así:  
  * En el portal, haga clic en Application Insights y busque el recurso de aplicación con la clave correcta, o
  * En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho en el proyecto y elija Application Insights, Configurar. Restablezca la aplicación para enviar la telemetría al recurso adecuado.
  * Si no puede encontrar las claves coincidentes, compruebe que utiliza las mismas credenciales de inicio de sesión en Visual Studio que en el portal.
* En el [panel de inicio de Microsoft Azure](https://portal.azure.com), observe el mapa de Estado del servicio. Si hay algunas indicaciones de alerta, espere hasta que hayan vuelto a su estado correcto y después cierre y vuelva a abrir el cuadro de la aplicación de Application Insights.
* Compruebe también [nuestro blog de estado](https://blogs.msdn.microsoft.com/servicemap-status/).
* ¿Ha escrito algún código para el [SDK del lado servidor](../../azure-monitor/app/api-custom-events-metrics.md) que pueda haber cambiado la clave de instrumentación en instancias `TelemetryClient` o en `TelemetryContext`? ¿O ha escrito una [configuración de filtro o muestreo](../../azure-monitor/app/api-filtering-sampling.md) que pueda estar filtrando demasiado?
* Si editó ApplicationInsights.config, compruebe minuciosamente la configuración de [TelemetryInitializers y TelemetryProcessors](../../azure-monitor/app/api-filtering-sampling.md). Un parámetro o tipo con un nombre incorrecto puede provocar que el SDK no envíe datos.

## <a name="q04"></a>No hay datos en Vistas de página, Explorador o Uso
*Se ven datos en los gráficos de Tiempo de respuesta del servidor y Solicitudes de servidor pero no hay datos en Tiempo de carga de la vista de página, o en las hojas Explorador o Uso.*

Los datos proceden de los scripts de las páginas web. 

* Si agrega Application Insights a un proyecto web existente, [tendrá que agregar los scripts manualmente](../../azure-monitor/app/javascript.md).
* Asegúrese de que Internet Explorer no muestra el sitio en modo de compatibilidad.
* Use la característica de depuración del explorador (F12 en algunos exploradores y, después, elija Red) para comprobar que los datos se envían a `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>No hay datos de excepción o dependencia
Consulte [telemetría de dependencias](../../azure-monitor/app/asp-net-dependencies.md) y [telemetría de excepciones](asp-net-exceptions.md).

## <a name="no-performance-data"></a>Sin datos de rendimiento
Los datos de rendimiento (CPU, velocidad de E/S, etc.) están disponibles para [servicios web de Java](../../azure-monitor/app/java-collectd.md), [aplicaciones de escritorio de Windows](../../azure-monitor/app/windows-desktop.md), [servicios y aplicaciones web IIS si instala el Monitor de estado](../../azure-monitor/app/monitor-performance-live-website-now.md) y [Azure Cloud Services](../../azure-monitor/app/app-insights-overview.md). Los encontrará en la sección Configuración > Servidores.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>No hay datos (de servidor) desde que se publicó la aplicación en el servidor
* Compruebe que realmente copió todas las DLL de Microsoft.ApplicationInsights en el servidor, junto con Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll.
* En el firewall, es posible que tenga que [abrir algunos puertos TCP](../../azure-monitor/app/ip-addresses.md).
* Si tiene que usar un servidor proxy para enviar fuera de la red corporativa, establezca el valor [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) en el archivo Web.config.
* Windows Server 2008: asegúrese de que ha instalado las siguientes actualizaciones: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523) y [KB2600217](https://support.microsoft.com/kb/2600217).

## <a name="i-used-to-see-data-but-it-has-stopped"></a>Solía ver datos, pero ya no sucede esto.
* Compruebe el [blog de estado](https://blogs.msdn.com/b/applicationinsights-status/).
* ¿Ha alcanzado su cuota mensual de puntos de datos? Abra Configuración/Cuotas y Precios para averiguarlo. Si es así, puede actualizar el plan o pagar para obtener capacidad adicional. Consulte el [esquema de precios](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="i-dont-see-all-the-data-im-expecting"></a>No veo todos los datos que esperaba
Si la aplicación envía una gran cantidad de datos y usa el SDK de Application Insights para ASP.NET versión 2.0.0-beta3 o posterior, la característica de [muestreo adaptativo](../../azure-monitor/app/sampling.md) puede operar y enviar solamente un porcentaje de los datos de telemetría.

Se puede deshabilitar, pero no se recomienda. El muestreo está diseñado para que la telemetría relacionada se transmita correctamente, con fines de diagnóstico.

## <a name="client-ip-address-is-0000"></a>La dirección IP del cliente es 0.0.0.0

El 5 de febrero de 2018, anunciamos que habíamos eliminado el registro de la dirección IP del cliente. Esto no afecta a la ubicación geográfica.

> [!NOTE]
> Si necesita los primeros tres octetos de la dirección IP, puede usar un [inicializador de telemetría](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling#add-properties-itelemetryinitializer) para agregar un atributo personalizado.
> Esto no afecta a los datos recopilados antes del 5 de febrero de 2018.

## <a name="wrong-geographical-data-in-user-telemetry"></a>Datos geográficos incorrectos en la telemetría de usuario
La ciudad, región y dimensiones del país proceden de las direcciones IP y no siempre son precisas. Estas direcciones IP se procesan en primer lugar para la ubicación y, a continuación, se cambian a 0.0.0.0 para almacenarse.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Excepción "Método no encontrado" en la ejecución en Azure Cloud Services
¿Ha realizado la compilación para .NET 4.6? 4.6 no se admite automáticamente en los roles de Azure Cloud Service. [Instale 4.6 en cada rol](../../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de ejecutar la aplicación.

## <a name="troubleshooting-logs"></a>Registros de la solución de problemas

Siga estas instrucciones para capturar registros de solución de problemas para su marco.

### <a name="net-framework"></a>.NET Framework

1. Instale el paquete [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) de NuGet. La versión que instale debe coincidir con la versión de `Microsoft.ApplicationInsighs` instalada actualmente.

2. Modifique el archivo applicationinsights.config para incluir lo siguiente:

    ```xml
    <TelemetryModules>
      <Add Type="Microsoft.ApplicationInsights.Extensibility.HostingStartup.FileDiagnosticsTelemetryModule, Microsoft.AspNet.ApplicationInsights.HostingStartup">
        <Severity>Verbose</Severity>
        <LogFileName>mylog.txt</LogFileName>
        <LogFilePath>C:\\SDKLOGS</LogFilePath>
      </Add>
    </TelemetryModules>
    ```
    La aplicación debe tener permisos de escritura en la ubicación configurada.

3. Reinicie el proceso para que el SDK adopte esta nueva configuración.

4. Revierta estos cambios cuando haya terminado.

### <a name="net-core"></a>.NET Core

1. Instale el paquete [Microsoft.AspNet.ApplicationInsights.HostingStartup](https://www.nuget.org/packages/Microsoft.AspNet.ApplicationInsights.HostingStartup) de NuGet. La versión que instale debe coincidir con la versión de `Microsoft.ApplicationInsights` instalada actualmente.

La versión más reciente de Microsoft.ApplicationInsights.AspNetCore es 2.7.1 y hace referencia a Microsoft.ApplicationInsights versión 2.10. Por lo tanto, se debería instalar la versión de Microsoft.AspNet.ApplicationInsights.HostingStartup 2.10.0

2. Modifique el método `ConfigureServices` en su clase `Startup.cs`:

    ```csharp
    services.AddSingleton<ITelemetryModule, FileDiagnosticsTelemetryModule>();
    services.ConfigureTelemetryModule<FileDiagnosticsTelemetryModule>( (module, options) => {
        module.LogFilePath = "C:\\SDKLOGS";
        module.LogFileName = "mylog.txt";
        module.Severity = "Verbose";
    } );
    ```
    La aplicación debe tener permisos de escritura en la ubicación configurada.

3. Reinicie el proceso para que el SDK adopte esta nueva configuración.

4. Revierta estos cambios cuando haya terminado.


## <a name="PerfView"></a> Recopilación de registros con PerfView
[PerfView](https://github.com/Microsoft/perfview) es una herramienta gratuita de análisis de rendimiento y diagnóstico que ayuda a aislar la CPU, la memoria y otros problemas mediante la recopilación y la visualización de información de diagnóstico de varios orígenes.

El SDK de Application Insights mantiene registros de solución de problemas automática de EventSource que se pueden capturar con PerfView.

Para recopilar registros, descargue PerfView y ejecute este comando:
```cmd
PerfView.exe collect -MaxCollectSec:300 -NoGui /onlyProviders=*Microsoft-ApplicationInsights-Core,*Microsoft-ApplicationInsights-Data,*Microsoft-ApplicationInsights-WindowsServer-TelemetryChannel,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Dependency,*Microsoft-ApplicationInsights-Extensibility-AppMapCorrelation-Web,*Microsoft-ApplicationInsights-Extensibility-DependencyCollector,*Microsoft-ApplicationInsights-Extensibility-HostingStartup,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector,*Microsoft-ApplicationInsights-Extensibility-PerformanceCollector-QuickPulse,*Microsoft-ApplicationInsights-Extensibility-Web,*Microsoft-ApplicationInsights-Extensibility-WindowsServer,*Microsoft-ApplicationInsights-WindowsServer-Core,*Microsoft-ApplicationInsights-Extensibility-EventSourceListener,*Microsoft-ApplicationInsights-AspNetCore
```

Puede modificar estos parámetros según sea necesario:
- **MaxCollectSec**. Establezca este parámetro para evitar que PerfView se ejecute indefinidamente y afecte al rendimiento del servidor.
- **OnlyProviders**. Establezca este parámetro para recopilar únicamente los registros del SDK. Puede personalizar esta lista en función de sus investigaciones específicas. 
- **NoGui**. Establezca este parámetro para recopilar los registros sin la GUI.


Para obtener más información,
- [Registro de seguimientos de rendimiento con PerfView](https://github.com/dotnet/roslyn/wiki/Recording-performance-traces-with-PerfView).
- [Orígenes de eventos de Application Insights](https://github.com/microsoft/ApplicationInsights-Home/tree/master/Samples/ETW)

## <a name="still-not-working"></a>Sigue sin funcionar...
* [Foro de Application Insights](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)
