---
title: Orígenes de datos en Azure Monitor | Microsoft Docs
description: Describe los datos disponibles para supervisar el estado y rendimiento de los recursos de Azure y las aplicaciones que se ejecutan en ellos.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: bwren
ms.openlocfilehash: 673575d480b78c151e68963e4a935fc72e7e578b
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564756"
---
# <a name="sources-of-monitoring-data-for-azure-monitor"></a>Orígenes de datos de supervisión para Azure Monitor
Azure Monitor se basa en una [plataforma de datos de supervisión común](data-platform.md) que incluye [registros](data-platform-logs.md) y [métricas](data-platform-metrics.md). La recopilación de datos en esta plataforma permite que los datos de múltiples recursos se analicen juntos mediante un conjunto común de herramientas en Azure Monitor. Los datos de supervisión también pueden enviarse a otras ubicaciones para admitir determinados escenarios, y algunos recursos pueden realizar operaciones de escritura en otras ubicaciones para poder recopilarse en registros o métricas.

En este artículo se describen los diferentes orígenes de datos de datos de supervisión recopilados por Azure Monitor además de los datos de supervisión creados por los recursos de Azure. Se proporcionan vínculos a información detallada sobre la configuración necesaria para recopilar estos datos en diferentes ubicaciones.

## <a name="application-tiers"></a>Niveles de aplicación

Los orígenes de datos de supervisión de las aplicaciones de Azure se pueden organizar en niveles. Los niveles más altos son su propia aplicación y los niveles más bajos son componentes de la plataforma de Azure. El método de acceso a los datos de cada nivel es variable. Los niveles de aplicación se resumen en la tabla siguiente y los orígenes de datos de supervisión en cada nivel se presentan en las siguientes secciones. Consulte [Supervisión de ubicaciones de datos en Azure](data-locations.md) para obtener una descripción de cada ubicación de datos y cómo puede acceder a sus datos.


![Supervisión de niveles](../media/overview/overview.png)


### <a name="azure"></a>Azure
La siguiente tabla describe brevemente los niveles de aplicación específicos de Azure. Siga el vínculo para obtener más información sobre cada una de las secciones a continuación.

| Nivel | DESCRIPCIÓN | Método de recopilación |
|:---|:---|:---|
| [Inquilino de Azure](#azure-tenant) | datos sobre el funcionamiento de los servicios de Azure en el nivel del inquilino, como Azure Active Directory. | Vea los datos de AAD en el portal o configure la recopilación en o Azure Monitor mediante una configuración de diagnóstico de inquilino. |
| [Suscripción de Azure](#azure-subscription) | Datos relacionados con el estado y la administración de los servicios entre recursos en su suscripción de Azure, como Resource Manager y Service Health. | Ver en el portal o configurar la colección para Azure Monitor mediante perfil de registro. |
| [Recursos de Azure](#azure-resources) |  Datos sobre el funcionamiento y el rendimiento de cada recurso de Azure. | Métricas recopiladas automáticamente. Ver en el explorador de métricas.<br>Configure las opciones de diagnóstico para recopilar registros en Azure Monitor.<br>Hay perspectivas y soluciones de supervisión disponibles para realizar una supervisión más detallada de tipos de recursos específicos. |

### <a name="azure-other-cloud-or-on-premises"></a>Azure, otra nube o local 
La siguiente tabla describe brevemente los niveles de aplicación posibles en Azure, otra nube o local. Siga el vínculo para obtener más información sobre cada una de las secciones a continuación.

| Nivel | DESCRIPCIÓN | Método de recopilación |
|:---|:---|:---|
| [Sistema operativo (invitado)](#operating-system-guest) | Datos sobre el sistema operativo en los recursos de proceso. | Instalar el agente de Log Analytics para recopilar orígenes de datos de cliente en Azure Monitor y Dependency Agent para recopilar las dependencias que admitan Azure Monitor para VM.<br>Para Azure Virtual Machines, instale la extensión de Azure Diagnostics para recopilar registros y métricas en Azure Monitor. |
| [Código de aplicación](#application-code) | Datos sobre el rendimiento y la funcionalidad de la aplicación y el código reales, incluidos los seguimientos de rendimiento, registros de aplicaciones y telemetría de usuario. | Instrumentar el código para recopilar datos en Application Insights. |
| [Orígenes personalizados](#custom-sources) | Datos de servicios externos u otros componentes o dispositivos. | Recopilar datos de registro o métricas en Azure Monitor desde cualquier cliente REST. |

## <a name="azure-tenant"></a>Inquilino de Azure
Los datos de telemetría relacionados con el inquilino de Azure se recopilan desde los servicios de todos los inquilinos, como Azure Active Directory.

![Recopilación del inquilino de Azure](media/data-sources/tenant.png)

### <a name="azure-active-directory-audit-logs"></a>Registros de auditoría de Azure Active Directory
Los [informes de Azure Active Directory](../../active-directory/reports-monitoring/overview-reports.md), contienen el historial de actividad de inicio de sesión y la traza de auditoría de los cambios realizados en un inquilino determinado. 

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Configure registros de Azure AD para que se recopilen en Azure Monitor para analizarlos con otros datos de supervisión. | [Integración de registros de Azure AD con registros de Azure Monitor (versión preliminar)](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | Exporte registros de Azure AD en Azure Storage para realizar el procedimiento de archivado. | [Tutorial: Archivado de registros de Azure AD en una cuenta de Azure Storage (versión preliminar)](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| Centro de eventos | Transmita los registros de Azure AD a otras ubicaciones mediante Event Hubs. | [Tutorial: Streaming de los registros de Azure Active Directory a Azure Event Hubs (versión preliminar)](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md). |



## <a name="azure-subscription"></a>Suscripción de Azure
Telemetría relacionada con el estado y el funcionamiento de su suscripción de Azure.

![Suscripción de Azure](media/data-sources/azure-subscription.png)

### <a name="azure-activity-log"></a>Registro de actividad de Azure 
El [registro de actividad de Azure](activity-logs-overview.md) incluye registros de Service Health, además de registros sobre los cambios de configuración aplicados a los recursos en su suscripción de Azure. El registro de actividad está disponible para todos los recursos de Azure y representa su vista _externa_.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|
| Registro de actividades | El registro de actividad se recopila en su propio almacén de datos, que puede ver en el menú de Azure Monitor o utilizar para crear alertas de registro de actividad. | [Consulta del registro de actividad de Azure en Azure Portal](activity-log-view.md#azure-portal) |
| Registros de Azure Monitor | Configure los registros de Azure Monitor para que recopilen el registro de actividad para analizarlo con otros datos de supervisión. | [Recopilación y análisis de los registros de actividad de Azure en un área de trabajo de Log Analytics en Azure Monitor](activity-log-collect.md) |
| Azure Storage | Exporte el registro de actividad en Azure Storage para realizar el procedimiento de archivado. | [Archivo del registro de actividad](activity-log-export.md#archive-activity-log)  |
| Event Hubs | Transmita el registros de actividad a otras ubicaciones mediante Event Hubs. | [Transmisión del registro de actividad al centro de eventos](activity-log-export.md#stream-activity-log-to-event-hub). |

### <a name="azure-service-health"></a>Azure Service Health
[Azure Service Health](../../service-health/service-health-overview.md) proporciona información sobre el estado de los servicios de Azure de la suscripción de los que dependen la aplicación y los recursos.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registro de actividades<br>Registros de Azure Monitor | Los registros de Service Health se almacenan en el registro de actividad de Azure, por lo que puede verlos en Azure Portal o realizar otras actividades que permita el registro de actividad. | [Visualización de notificaciones de mantenimiento del servicio mediante Azure Portal](service-notifications.md) |


## <a name="azure-resources"></a>Recursos de Azure
Los registros de diagnóstico de nivel de recursos y las métricas proporcionan información sobre el funcionamiento _interno_ de recursos de Azure. Están disponibles para la mayoría de servicios de Azure, y las perspectivas y soluciones de administración proporcionan datos adicionales para determinados servicios.

![Colección de recursos de Azure](media/data-sources/azure-resources.png)


### <a name="platform-metrics"></a>Métricas de la plataforma 
La mayoría de los servicios de Azure generarán [métricas de plataforma](data-platform-metrics.md) que reflejan su rendimiento y funcionamiento directamente en la base de datos de métricas. Las [métricas específicas varían en función del tipo de recurso](metrics-supported.md). 

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Métricas de Azure Monitor | Las métricas de la plataforma se escribirán en la base de datos de métricas de Azure Monitor sin ninguna configuración. Acceda a las métricas de la plataforma del Explorador de métricas.  | [Introducción al Explorador de métricas de Azure](metrics-getting-started.md)<br>[Métricas compatibles con Azure Monitor](metrics-supported.md) |
| Registros de Azure Monitor | Copie las métricas de la plataforma en los registros para las tendencias y otros análisis con Log Analytics. | [Diagnósticos de Azure Diagnostics directos a Log Analytics](diagnostic-logs-stream-log-store.md) |
| Event Hubs | Transmita métricas a otras ubicaciones mediante Event Hubs. |[Flujo de datos de supervisión de Azure a un centro de eventos para que lo consuma una herramienta externa](stream-monitoring-data-event-hubs.md) |

### <a name="diagnostic-logs"></a>Registros de diagnóstico
Los [registros de Diagnostics](diagnostic-logs-overview.md) proporcionan perspectivas en la operación _interna_ de un recurso de Azure.  Los registros de Diagnostics no están habilitados de forma predeterminada. Debe habilitarlos y especificar un destino para cada recurso. 

Los requisitos de configuración y el contenido de los registros de Diagnostics varían según el tipo de recurso, y no todos los servicios crean registros de diagnóstico. Consulte [Servicios, esquemas y categorías compatibles para registros de Azure Diagnostics](diagnostic-logs-schema.md) para obtener información sobre los servicios y vínculos a los procedimientos de configuración detallados. Si el servicio no aparece en este artículo, el servicio no realiza aún operaciones de escritura en los registros de Diagnostics.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Envíe registros de diagnóstico a registros de Azure Monitor para el análisis con otros datos de registro recopilados. Algunos recursos pueden escribir directamente en Azure Monitor, mientras que otros escriben en una cuenta de almacenamiento antes de importarse a un área de trabajo de Log Analytics. | [Transmisión de registros de Azure Diagnostics a un área de trabajo de Log Analytics en Azure Monitor](diagnostic-logs-stream-log-store.md)<br>[Uso de Azure Portal para recopilar registros de Azure Storage](azure-storage-iis-table.md#use-the-azure-portal-to-collect-logs-from-azure-storage)  |
| Storage | Envíe registros de diagnóstico a Azure Storage para realizar procedimientos de archivado. | [Archivo de registros de Azure Diagnostics](archive-diagnostic-logs.md) |
| Event Hubs | Transmita los registros de diagnóstico a otras ubicaciones mediante Event Hubs. |[Transmisión de registros de Azure Diagnostics a un centro de eventos](diagnostic-logs-stream-event-hubs.md) |

## <a name="operating-system-guest"></a>Sistema operativo (invitado)
Los recursos de proceso en Azure, en otras nubes y en el entorno local tienen un sistema operativo invitado para supervisar. Con la instalación de uno o más agentes, puede recopilar datos de telemetría del invitado en Azure Monitor para analizarlos con las mismas herramientas de supervisión que los propios servicios de Azure.

![Colección de recursos de proceso de Azure](media/data-sources/compute-resources.png)

### <a name="azure-diagnostic-extension"></a>Extensión de diagnóstico de Azure
La habilitación de la extensión de Azure Diagnostics para Azure Virtual Machines le permite recopilar registros y métricas del sistema operativo invitado de recursos de proceso, incluidos los roles web y de trabajo de Azure Cloud Services (clásico), Virtual Machines, conjuntos de escalado de máquinas virtuales y Service Fabric.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Storage | Cuando habilite la extensión de Diagnostics, se escribirá en una cuenta de almacenamiento de forma predeterminada. | [Almacenamiento y visualización de los datos de diagnóstico en Azure Storage](diagnostics-extension-to-storage.md) |
| Métricas de Azure Monitor | Cuando configure la extensión de Diagnostics para recopilar los contadores de rendimiento, se escribirán en la base de datos de métricas de Azure Monitor. | [Envío de métricas de SO invitado al almacén de métricas de Azure Monitor con una plantilla de Resource Manager para una máquina virtual Windows](collect-custom-metrics-guestos-resource-manager-vm.md) |
| Registros de Application Insights | Recopile registros y contadores de rendimiento a partir del recurso de proceso que admite su aplicación para el análisis con otros datos de aplicación. | [Envío de datos de diagnóstico de Cloud Services, Virtual Machines o Service Fabric a Application Insights](diagnostics-extension-to-application-insights.md) |
| Event Hubs | Configure la extensión de Diagnostics para transmitir los datos a otras ubicaciones mediante Event Hubs.  | [Transmisión de datos de Azure Diagnostics en la ruta de acceso activa mediante Event Hubs](diagnostics-extension-stream-event-hubs.md) |

### <a name="log-analytics-agent"></a>Agente de Log Analytics 
Instale el agente de Log Analytics para la supervisión y administración completas de las máquinas virtuales de Windows y Linux. La máquina virtual se puede ejecutar en Azure, en otra nube o en el entorno local.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | El agente de Log Analytics se conecta a Azure Monitor directamente o a través de System Center Operations Manager y le permite recopilar datos desde orígenes de datos que configura o desde soluciones de supervisión que proporcionan perspectivas adicionales en aplicaciones que se ejecutan en la máquina virtual. | [Orígenes de datos de agente en Azure Monitor](agent-data-sources.md)<br>[Conexión de Operations Manager con Azure Monitor](om-agents.md) |


### <a name="azure-monitor-for-vms"></a>Azure Monitor para máquinas virtuales 
[Azure Monitor para VM](../insights/vminsights-overview.md) ofrece una experiencia de supervisión personalizada para máquinas virtuales que proporciona características más allá de la funcionalidad de Azure Monitor principal, incluido el estado del servicio y el estado de la máquina virtual. Requiere Dependency Agent en las máquinas virtuales de Windows y Linux que se integra con el agente de Log Analytics para recopilar datos detectados acerca de los procesos que se ejecutan en la máquina virtual y en las dependencias de procesos externos.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Almacena los datos sobre los procesos y dependencias en el agente. | [Uso de la asignación de Azure Monitor para VM (versión preliminar) para conocer los componentes de una aplicación](../insights/vminsights-maps.md) |
| Almacenamiento de máquinas virtuales | Azure Monitor para VM almacena información sobre el estado en una ubicación personalizada. Esto solo está disponible en Azure Monitor para VM en Azure Portal, además de en la [API REST de Azure Resource Health](/rest/api/resourcehealth/). | [Descripción del mantenimiento de las máquinas virtuales de Azure](../insights/vminsights-health.md)<br>[API REST de Azure Resource Health](https://docs.microsoft.com/rest/api/resourcehealth/) |



## <a name="application-code"></a>Código de aplicación
Se realiza la supervisión detallada de la aplicación en Azure Monitor con [Application Insights](https://docs.microsoft.com/azure/application-insights/), que recopila datos de aplicaciones que se ejecutan en una variedad de plataformas. La aplicación se puede ejecutar en Azure, en otra nube o en el entorno local.

![Recopilación de datos de aplicación](media/data-sources/applications.png)


### <a name="application-data"></a>Datos de aplicación
Cuando se habilita Application Insights para una aplicación mediante la instalación de un paquete de instrumentación, recopila métricas y registros relacionados con el rendimiento y el funcionamiento de la aplicación. Application Insights almacena los datos que recopila en la misma plataforma de datos de Azure Monitor que utilizan otros orígenes de datos. Incluye completas herramientas para analizar estos datos, pero también puede analizarlos con datos de otros orígenes mediante herramientas como el Explorador de métricas y Log Analytics.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Datos operativos sobre la aplicación, como vistas de página, las solicitudes de aplicación, excepciones y seguimientos. | [Análisis de datos de registro en Azure Monitor](../log-query/log-query-overview.md) |
|                    | Información de dependencia entre los componentes de aplicación para admitir la asignación de aplicaciones y la correlación de telemetría. | [Correlación de telemetría en Application Insights](../app/correlation.md) <br> [Mapa de aplicación](../app/app-map.md) |
|            | Resultados de las pruebas de disponibilidad que permiten probar la disponibilidad y capacidad de respuesta de la aplicación desde diferentes ubicaciones de la red Internet pública. | [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](../app/monitor-web-app-availability.md) |
| Métricas de Azure Monitor | Application Insights recopila las métricas que describen el rendimiento y el funcionamiento de la aplicación, además de las métricas personalizadas que define en la aplicación en la base de datos de métricas de Azure Monitor. | [Métricas agregadas previamente y basadas en registros en Application Insights](../app/pre-aggregated-metrics-log-metrics.md)<br>[API de Application Insights para eventos y métricas personalizados](../app/api-custom-events-metrics.md) |
| Azure Storage | Envíe datos de aplicación a Azure Storage para el procedimiento de archivado. | [Exportación de datos de telemetría desde Application Insights](../app/export-telemetry.md) |
|            | Los detalles de las pruebas de disponibilidad se almacenan en Azure Storage. Utilice Application Insights en Azure Portal para descargar los análisis locales. Los resultados de las pruebas de disponibilidad se almacenan en los registros de Azure Monitor. | [Supervisión de la disponibilidad y la capacidad de respuesta de cualquier sitio web](../app/monitor-web-app-availability.md) |
|            | Los datos de seguimiento del generador de perfiles se almacenan en Azure Storage. Utilice Application Insights en Azure Portal para descargar los análisis locales.  | [Generación de perfiles de aplicaciones de producción en Azure con Application Insights](../app/profiler-overview.md) 
|            | Los datos de instantáneas de depuración capturados para un subconjunto de excepciones se almacenan en Azure Storage. Utilice Application Insights en Azure Portal para descargar los análisis locales.  | [Funcionamiento de las instantáneas](../app/snapshot-debugger.md#how-snapshots-work) |

## <a name="monitoring-solutions-and-insights"></a>Soluciones de supervisión y perspectivas
Las [soluciones de supervisión](../insights/solutions.md) y [perspectivas](../insights/insights-overview.md) recopilan datos para proporcionar conclusiones sobre el funcionamiento de un servicio o aplicación determinados. Pueden abordar recursos en diferentes niveles de aplicación e incluso varios niveles.

### <a name="monitoring-solutions"></a>Soluciones de supervisión

| Destino | DESCRIPCIÓN | Referencia
|:---|:---|:---|
| Registros de Azure Monitor | Las soluciones de supervisión recopilan datos en registros de Azure Monitor, donde se pueden analizar mediante un lenguaje de consulta o [vistas](view-designer.md) que se suelen incluir en la solución. | [Detalles de la recopilación de datos para las soluciones de supervisión en Azure](../insights/solutions-inventory.md) |


### <a name="azure-monitor-for-containers"></a>Azure Monitor para contenedores
[Azure Monitor para contenedores](../insights/container-insights-overview.md) proporciona una experiencia de supervisión personalizada para [Azure Kubernetes Service (AKS)](/azure/aks/). Recopila los datos adicionales sobre estos recursos que se describen en la tabla siguiente.

| Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|
| Registros de Azure Monitor | Almacena datos de supervisión para AKS, como inventario, registros y eventos. Los datos de métrica también se almacenan en los registros para poder aprovechar su funcionalidad de análisis en el portal. | [Comprender el rendimiento del clúster de AKS con Azure Monitor para contenedores](../insights/container-insights-analyze.md) |
| Métricas de Azure Monitor | Los datos de métricas se almacenan en la base de datos de métricas para favorecer la visualización y las alertas. | [Visualización de métricas del contenedor en el Explorador de métricas](../insights/container-insights-analyze.md#view-container-metrics-in-metrics-explorer) |
| Azure Kubernetes Service | Para conseguir una experiencia casi en tiempo real, Azure Monitor para contenedores presenta los datos directamente desde Azure Kubernetes Service en Azure Portal. | [Vista de los registros de contenedor en tiempo real con Azure Monitor para contenedores (versión preliminar)](../insights/container-insights-live-logs.md) |

### <a name="azure-monitor-for-vms"></a>Azure Monitor para máquinas virtuales
[Azure Monitor para VM](../insights/vminsights-overview.md) proporciona una experiencia personalizada para la supervisión de máquinas virtuales. Se incluye una descripción de los datos recopilados por Azure Monitor para VM en la sección [Sistema operativo (invitado)](#operating-system-guest) anterior.

## <a name="custom-sources"></a>Orígenes personalizados
Además de los niveles estándar de una aplicación, puede que necesite supervisar otros recursos que tengan datos de telemetría y que no puedan recopilarse con los otros orígenes de datos. Para estos recursos, escriba estos datos en las métricas o los registros mediante la API de Azure Monitor.

![Colección personalizada](media/data-sources/custom.png)

| Destino | Método | DESCRIPCIÓN | Referencia |
|:---|:---|:---|:---|
| Registros de Azure Monitor | API de recopilador de datos | Recopile datos de registro desde cualquier cliente REST y almacénelos en el área de trabajo de Log Analytics. | [Envío de datos de registro a Azure Monitor con HTTP Data Collector API (versión preliminar pública)](data-collector-api.md) |
| Métricas de Azure Monitor | API de métricas personalizadas | Recopile datos de métricas desde cualquier cliente REST y almacénelos en la base de datos de métricas de Azure Monitor. | [Envío de métricas personalizadas de un recurso de Azure al almacén de métricas de Azure Monitor mediante la API REST](metrics-store-custom-rest-api.md) |


## <a name="other-services"></a>Otros servicios
Otros servicios de Azure escriben datos en la plataforma de datos de Azure Monitor. Esto le permite analizar los datos reunidos por estos servicios con los datos recopilados por Azure Monitor y aprovechar las mismas herramientas de visualización y análisis.

| Servicio | Destino | DESCRIPCIÓN | Referencia |
|:---|:---|:---|:---|
| [Azure Security Center](/azure/security-center/) | Registros de Azure Monitor | Azure Security Center almacena los datos de seguridad que recopila en el área de trabajo de Log Analytics, que permite que estos se analicen con otros datos de registro recopilados por Azure Monitor.  | [Recopilación de datos en Azure Security Center](../../security-center/security-center-enable-data-collection.md) |
| [Azure Sentinel](/azure/sentinel/) | Registros de Azure Monitor | Azure Sentinel almacena los datos que recopila a partir de diferentes orígenes de datos en el área de trabajo de Log Analytics, que permite que estos se analicen con otros datos de registro recopilados por Azure Monitor.  | [Conexión con orígenes de datos](/azure/sentinel/quickstart-onboard) |


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [tipos de datos de supervisión recopilados por Azure Monitor](data-platform.md) y cómo ver y analizar estos datos.
- Enumere las [distintas ubicaciones en las que los recursos de Azure almacenan datos](data-locations.md) y cómo acceder a ellos. 
