---
title: Generación de perfiles de Azure Cloud Services con Application Insights | Microsoft Docs
description: Habilitación de Application Insights Profiler para Azure Cloud Services.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 93392e379cbb03508fefc1877d5d50e04436b79c
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737231"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Generación de perfiles de Azure Cloud Services con Application Insights

También puede implementar Application Insights Profiler en estos servicios:
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicaciones de Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler se instala con la extensión de Azure Diagnostics. Solo tiene que configurar Azure Diagnostics para instalar Profiler y enviar perfiles al recurso de Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Habilitación de Profiler para Azure Cloud Services
1. Asegúrese de que está utilizando [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) o una versión posterior. Si usa la familia 4 del sistema operativo, deberá instalar .NET Framework 4.6.1 o una versión más reciente con una [tarea de inicio](https://docs.microsoft.com/en-us/azure/cloud-services/cloud-services-dotnet-install-dotnet). La familia 5 del sistema operativo incluye una versión compatible de .NET Framework de forma predeterminada. 

1. Agregue el [SDK de Application Insights a Azure Cloud Services](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

    **Se ha corregido el error en el generador de perfiles que se incluye con WAD para Cloud Services.** La versión más reciente de WAD (1.12.2.0) para Cloud Services funciona con todas las versiones recientes del SDK de App Insights. Los hosts de Cloud Services actualizarán WAD automáticamente, pero no es algo inmediato. Para forzar una actualización, puede volver a implementar su servicio o reiniciar el nodo.

1. Realice un seguimiento de las solicitudes con Application Insights:

    * Para los roles web de ASP.NET, Application Insights puede realizar un seguimiento de las solicitudes automáticamente.

    * Para los roles de trabajo, [agregue código para realizar el seguimiento de solicitudes](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json).

1. Configure la extensión de Azure Diagnostics para habilitar Profiler:

    a. Busque el archivo *diagnostics.wadcfgx* de [Azure Diagnostics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) correspondiente a su rol de aplicación, como se muestra aquí:  

      ![Ubicación del archivo de configuración de diagnósticos](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Si no encuentra el archivo, consulte [Set up diagnostics for Azure Cloud Services and Virtual Machines](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) (Configuración de diagnósticos para Azure Cloud Services y Virtual Machines).

    b. Agregue la siguiente sección `SinksConfig` como elemento secundario de `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > Si el archivo *diagnostics.wadcfgx* también contiene otro receptor de tipo ApplicationInsights, las tres claves de instrumentación siguientes tienen que coincidir:  
    > * La clave que usa la aplicación. 
    > * La clave que usa el receptor ApplicationInsights. 
    > * La clave que usa el receptor ApplicationInsightsProfiler. 
    >
    > Puede encontrar el valor real de la clave de instrumentación que usa el receptor `ApplicationInsights` en los archivos *ServiceConfiguration.\*.cscfg*. 
    > Después de la versión de Azure SDK para Visual Studio 15.5, solo es preciso que coincidan las claves de instrumentación que usan la aplicación y el receptor ApplicationInsightsProfiler.

1. Implemente el servicio con la nueva configuración de Diagnostics, y Application Insights Profiler se configurará para ejecutarse en el servicio.
 
## <a name="next-steps"></a>Pasos siguientes

* Genere tráfico para su aplicación (por ejemplo, inicie una [prueba de disponibilidad](monitor-web-app-availability.md)). A continuación, espere de 10 a 15 minutos para que se empiecen a enviar seguimientos a la instancia de Application Insights.
* Consulte [Seguimientos de Profiler](profiler-overview.md?toc=/azure/azure-monitor/toc.json) en Azure Portal.
* Para solucionar los problemas de Profiler, consulte [Solución de problemas de Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
