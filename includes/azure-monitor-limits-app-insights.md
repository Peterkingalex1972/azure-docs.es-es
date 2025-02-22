---
title: archivo de inclusión
description: archivo de inclusión
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 02/07/2019
ms.author: robb
ms.custom: include file
ms.openlocfilehash: fb57d8322cfd3f72862dc8edd1d2e231338a66de
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305333"
---
Hay algunos límites en el número de métricas y eventos por aplicación; es decir, por clave de instrumentación. Los límites dependen del [plan de precios](https://azure.microsoft.com/pricing/details/application-insights/) que elija.

| Recurso | Límite predeterminado | Nota:
| --- | --- | --- |
| Total de datos por día | 100 GB | Se pueden reducir los datos si se establece un límite. Si necesita más datos, puede aumentar el límite en el portal, hasta 1000 GB. Para capacidades mayores de 1000 GB, envíe un correo electrónico a AIDataCap@microsoft.com.
| Limitaciones | 32 000 eventos por segundo | El límite se mide por minuto.
| Retención de datos | 90 días | Este recurso es para [Search](../articles/azure-monitor/app/diagnostic-search.md), [Analytics](../articles/azure-monitor/app/analytics.md) y el [Explorador de métricas](../articles/azure-monitor/app/metrics-explorer.md).
| Retención de resultados detallados para la [prueba de disponibilidad de varios pasos](../articles/azure-monitor/app/availability-multistep.md) | 90 días | Este recurso proporciona resultados detallados de cada paso.
| Tamaño máximo del evento | 64 000 |
| Longitud de nombres de propiedades y métricas | 150 | Consulte [esquemas de tipos](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longitud de cadena del valor de propiedad | 8192 | Consulte [esquemas de tipos](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Longitud del mensaje de seguimiento y excepción | 32 768  | Consulte [esquemas de tipos](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Número de [pruebas de disponibilidad](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicación | 100 |
| Retención de datos del [generador de perfiles](../articles/azure-monitor/app/profiler.md) | 5 días |
| Datos enviados por día del [generador de perfiles](../articles/azure-monitor/app/profiler.md) | 10 GB |

Para más información, consulte [Administración de precios y cuotas para Application Insights](../articles/azure-monitor/app/pricing.md).