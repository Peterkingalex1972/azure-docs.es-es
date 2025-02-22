---
title: Instalación de contenedores de Speech
titleSuffix: Azure Cognitive Services
description: Detalles de las opciones de configuración de un gráfico Helm de speech-to-text
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717234"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Speech-to-Text (gráfico secundario: charts/speechToText)

Para reemplazar al gráfico de nivel superior, agregue el prefijo `speechToText.` a todos los parámetros para que sean más específicos. Se reemplazará el parámetro correspondiente; por ejemplo, `speechToText.numberOfConcurrentRequest` reemplaza a `numberOfConcurrentRequest`.

|Parámetro|DESCRIPCIÓN|Valor predeterminado|
| -- | -- | -- |
| `enabled` | Si el servicio **speech-to-text** está habilitado. | `false` |
| `numberOfConcurrentRequest` | Número de solicitudes simultáneas del servicio **speech-to-text**. Este gráfico calcula automáticamente los recursos de CPU y memoria en función de este valor. | `2` |
| `optimizeForAudioFile`| Si el servicio necesita optimizar la entrada de audio a través de archivos de audio. Si `true`, este gráfico asignará más recursos de CPU al servicio. | `false` |
| `image.registry`| Registro de la imagen de Docker de **speech-to-text**. | `containerpreview.azurecr.io` |
| `image.repository` | Repositorio de la imagen de Docker de **speech-to-text**. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Etiqueta de la imagen de Docker de **speech-to-text**. | `latest` |
| `image.pullSecrets` | Secretos de la imagen para extraer la imagen de Docker de **speech-to-text**. | |
| `image.pullByHash`| Si la imagen de Docker se extrae mediante hash. Si `true`, `image.hash` es obligatorio. | `false` |
| `image.hash`| Hash de la imagen de Docker de **speech-to-text**. Solo se usa con `image.pullByHash: true`.  | |
| `image.args.eula` (obligatorio) | Indica que ha aceptado la licencia. El único valor válido es `accept`. | |
| `image.args.billing` (obligatorio) | El valor de URI del punto de conexión de facturación está disponible en Azure Portal, en la página de Información general de Información general del servicio de voz. | |
| `image.args.apikey` (obligatorio) | Se usa para realizar un seguimiento de la información de facturación. ||
| `service.type` | Tipo de servicio de Kubernetes del servicio **speech-to-text**. Consulte las [instrucciones de los tipos de servicio de Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) para obtener más información y comprobar la compatibilidad con los proveedores de nube. | `LoadBalancer` |
| `service.port`|  Puerto del servicio **speech-to-text**. | `80` |
| `service.autoScaler.enabled` | Si [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) está habilitado. Si `true`, `speech-to-text-autoscaler` se implementará en el clúster de Kubernetes. | `true` |
| `service.podDisruption.enabled` | Si [Pod Disruption Budget](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) está habilitado. Si `true`, `speech-to-text-poddisruptionbudget` se implementará en el clúster de Kubernetes. | `true` |