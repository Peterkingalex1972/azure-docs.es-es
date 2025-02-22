---
title: Métricas de Azure Relay en Azure Monitor (versión preliminar) | Microsoft Docs
description: Use Azure Monitor para supervisar Azure Relay.
services: service-bus-relay
documentationcenter: .NET
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/28/2018
ms.author: spelluru
ms.openlocfilehash: bd62624406adb006fdcd7d59f72db3fb5e1848a0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60421815"
---
# <a name="azure-relay-metrics-in-azure-monitor-preview"></a>Métricas de Azure Relay en Azure Monitor (versión preliminar)
Las métricas de Azure Relay le permiten conocer el estado de los recursos de su suscripción de Azure. Con un amplio conjunto de datos de métricas, puede evaluar el estado general de los recursos de Relay, no solo en el nivel de espacio de nombres, sino también en el nivel de entidad. Estas estadísticas pueden ser importantes, ya que le ayudan a supervisar el estado de Azure Relay. Las métricas también pueden ayudarle a solucionar problemas de causa principal sin necesidad de ponerse en contacto con el soporte técnico de Azure.

Azure Monitor proporciona interfaces de usuario unificadas para la supervisión de distintos servicios de Azure. Para obtener más información, vea [Supervisión de Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md) y el ejemplo [Retrieve Azure Monitor metrics with .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) (Recuperación de métricas de Azure Monitor con .NET) en GitHub.

> [!IMPORTANT]
> Este artículo solo se aplica a la característica Conexiones híbridas de Azure Relay, no a WCF Relay. 

## <a name="access-metrics"></a>Acceso a la métrica

Azure Monitor proporciona varias maneras de tener acceso a las métricas. Puede acceder a las métricas desde [Azure Portal](https://portal.azure.com), o usar las API de Azure Monitor (REST y .NET) y soluciones de análisis como Operation Management Suite y Event Hubs. Para más información, vea [Datos de supervisión recopilados por Azure Monitor](../azure-monitor/platform/data-platform.md).

De forma predeterminada, las métricas están habilitadas y puede acceder a datos de los últimos 30 días. Si es necesario conservar los datos durante un periodo mayor, se pueden archivar en una cuenta de Azure Storage. Esto se configura en la [configuración de diagnóstico](../azure-monitor/platform/diagnostic-logs-overview.md#diagnostic-settings) de Azure Monitor.

## <a name="access-metrics-in-the-portal"></a>Acceso a métricas del portal

Una vez transcurrido un tiempo, las métricas se pueden supervisar en [Azure Portal](https://portal.azure.com). En el ejemplo siguiente se muestra cómo ver las solicitudes correctas y las solicitudes entrantes en el nivel de cuenta:

![][1]

También puede tener acceso a las métricas directamente a través del espacio de nombres. Para ello, seleccione el espacio de nombres y haga clic en **Métrica (vista previa)** . 

Para ver métricas que admitan las dimensiones, debe filtrar por el valor de la dimensión que desee.

## <a name="billing"></a>Facturación

Actualmente, el uso de métricas en Azure Monitor es gratuito, ya que se trata de una versión preliminar. Sin embargo, si usa otras soluciones que ingieren datos de métricas, puede que se le facturen dichas soluciones. Por ejemplo, se le facturará por Azure Storage si archiva datos de métricas en una cuenta de Azure Storage. También se le facturará por los registros de Azure Monitor si transmite datos de métricas a registros de Azure Monitor para realizar análisis avanzados.

Las siguientes métricas ofrecen una visión general del estado de su servicio. 

> [!NOTE]
> Se están dejando de usar algunas métricas a medida que se mueven a otro nombre. Esto podría requerir que actualice las referencias. Las métricas que se marquen con la palabra clave "en desuso" no se admitirán en el futuro.

Los valores de las métricas se envían a Azure Monitor cada minuto. La granularidad de tiempo define el intervalo de tiempo en el que se presentan los valores de las métricas. El intervalo de tiempo compatible para todas las métricas de Azure Relay es 1 minuto.

## <a name="connection-metrics"></a>Métricas de conexión

| Nombre de métrica | DESCRIPCIÓN |
| ------------------- | ----------------- |
| ListenerConnections-Success (versión preliminar) | Número de conexiones correctas del agente de escucha a Azure Relay durante un determinado periodo. <br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|ListenerConnections-ClientError (versión preliminar)|Número de errores de cliente en las conexiones del agente de escucha durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|ListenerConnections-ServerError (versión preliminar)|Número de errores del servidor en las conexiones del agente de escucha durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|SenderConnections-Success (versión preliminar)|Número de conexiones correctas del remitente durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|SenderConnections-ClientError (versión preliminar)|Número de errores de cliente en las conexiones del remitente durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|SenderConnections-ServerError (versión preliminar)|Número de errores del servidor en las conexiones del remitente durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|ListenerConnections-TotalRequests (versión preliminar)|Número total de errores en las conexiones del agente de escucha durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|SenderConnections-TotalRequests (versión preliminar)|Solicitudes de conexión realizadas por los remitentes durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|ActiveConnections (versión preliminar)|Número de conexiones activas durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|ActiveListeners (versión preliminar)|Número de agentes de escucha activos durante un determinado periodo.<br/><br/> Unidad: Recuento <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|ListenerDisconnects (versión preliminar)|Número de agentes de escucha desconectados durante un determinado periodo.<br/><br/> Unidad: Bytes <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|
|SenderDisconnects (versión preliminar)|Número de remitentes desconectados durante un determinado periodo.<br/><br/> Unidad: Bytes <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|

## <a name="memory-usage-metrics"></a>Métricas de uso de memoria

| Nombre de métrica | DESCRIPCIÓN |
| ------------------- | ----------------- |
|BytesTransferred (versión preliminar)|Número de bytes transferidos durante un determinado periodo.<br/><br/> Unidad: Bytes <br/> Tipo de agregación: Total <br/> Dimensión: EntityName|

## <a name="metrics-dimensions"></a>Dimensiones de métricas

Azure Relay admite las siguientes dimensiones para las métricas de Azure Monitor. Agregar dimensiones a las métricas es opcional. Si no agrega dimensiones, las métricas se especifican en el nivel de espacio de nombres. 

|Nombre de dimensión|DESCRIPCIÓN|
| ------------------- | ----------------- |
|EntityName| Azure Relay admite entidades de mensajería en el espacio de nombres.|

## <a name="next-steps"></a>Pasos siguientes

Vea [Información general sobre la supervisión en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png




