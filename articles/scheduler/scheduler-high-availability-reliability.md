---
title: Alta disponibilidad y confiabilidad en Azure Scheduler
description: Obtenga más información sobre la alta disponibilidad y confiabilidad en Azure Scheduler
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 5ec78e60-a9b9-405a-91a8-f010f3872d50
ms.topic: article
ms.date: 08/16/2016
ms.openlocfilehash: 50ab6cfefe4a7df9d671e7fd1287aa16b803f260
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64702886"
---
# <a name="high-availability-and-reliability-for-azure-scheduler"></a>Alta disponibilidad y confiabilidad en Azure Scheduler

> [!IMPORTANT]
> [Azure Logic Apps](../logic-apps/logic-apps-overview.md) reemplaza a Azure Scheduler, que se va a retirar. Para programar trabajos, [pruebe Azure Logic Apps en su lugar](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Azure Scheduler ofrece tanto [alta disponibilidad](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) como confiabilidad para sus trabajos. Para más información, consulte el [Acuerdo de Nivel de Servicio de Scheduler](https://azure.microsoft.com/support/legal/sla/scheduler).

## <a name="high-availability"></a>Alta disponibilidad

Azure Scheduler tiene una [alta disponibilidad] y usa una implementación del servicio con redundancia geográfica y replicación geográfica regional de los trabajos.

### <a name="geo-redundant-service-deployment"></a>Implementación de servicio con redundancia geográfica

Azure Scheduler está disponible en Azure Portal en casi [todas las regiones geográficas admitidas por Azure actualmente](https://azure.microsoft.com/global-infrastructure/regions/#services). Por lo tanto, si un centro de datos de Azure en una región hospedada deja de estar disponible, podrá seguir usando Azure Scheduler porque las funcionalidades de conmutación por error del servicio hacen que Scheduler esté disponible en otro centro de datos.

### <a name="geo-regional-job-replication"></a>Replicación geográfica regional de trabajos

Los trabajos que tenga en Azure Scheduler se replican entre las regiones de Azure. Si se produce una interrupción en una región, Azure Scheduler conmuta por error y garantiza que el trabajo se ejecuta en otro centro de datos en la región geográfica emparejada.

Por ejemplo, si crea un trabajo en la zona Centro-sur de EE. UU., Azure Scheduler replica automáticamente ese trabajo en la zona Centro-norte de EE. UU. Si se produce un error en la región Centro-sur de EE. UU., Azure Scheduler ejecuta el trabajo en Centro-norte de EE. UU. 

![Replicación geográfica regional de trabajos](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image1.png)

Azure Scheduler también se asegura de que los datos permanezcan dentro de la misma región geográfica pero más amplia, en caso de que se produzca un error en Azure. Esto significa que no tiene que duplicar los trabajos si únicamente desea alta disponibilidad. Azure Scheduler proporciona automáticamente la alta disponibilidad para sus trabajos.

## <a name="reliability"></a>Confiabilidad

Azure Scheduler garantiza una alta disponibilidad propia y adopta un enfoque diferente para los trabajos creados por el usuario. Por ejemplo, supongamos que su trabajo invoca un punto de conexión HTTP que no está disponible. Aun así, Azure Scheduler intenta ejecutar el trabajo correctamente y proporciona formas alternativas para tratar los errores: 

* Configuración de directivas de reintentos.
* Configuración de puntos de conexión alternativos.

<a name="retry-policies"></a>

### <a name="retry-policies"></a>Directivas de reintentos

Azure Scheduler permite configurar directivas de reintentos. Si se produce un error en un trabajo, de manera predeterminada, Scheduler reintenta el trabajo otras cuatro veces, en intervalos de 30 segundos. Puede hacer que esta directiva de reintentos sea más intensiva (por ejemplo, 10 veces en intervalos de 30 segundos) o menos (dos veces al día).

Por ejemplo, supongamos que crea un trabajo semanal que llama a un punto de conexión HTTP. Si el punto de conexión HTTP deja de estar disponible durante algunas horas cuando se ejecuta el trabajo, quizás no quiera esperar otra semana para ejecutar de nuevo el trabajo, que es lo que sucedería porque la directiva de reintentos predeterminada no funcionará en este caso. Es posible que desee cambiar la directiva de reintentos estándar para que los reintentos se realicen, por ejemplo, cada tres horas, en lugar de cada 30 segundos. 

Para más información sobre cómo configurar una directiva de reintentos, consulte [retryPolicy](scheduler-concepts-terms.md#retrypolicy).

### <a name="alternate-endpoints"></a>Puntos de conexión alternativos

Si el trabajo de Azure Scheduler llama a un punto de conexión que no es accesible, incluso después de seguir la directiva de reintentos, Scheduler conmuta por error a un punto de conexión alternativo que pueda controlar estos errores. Si ha configurado este punto de conexión, Scheduler llama a ese punto de conexión, lo que hace que sus trabajos tengan alta disponibilidad en caso de error.

Por ejemplo, este diagrama muestra cómo Scheduler sigue la directiva de reintentos cuando se llama a un servicio web de Nueva York. Si los reintentos fracasan, Scheduler busca un punto de conexión alternativo. Si el punto de conexión existe, Scheduler comienza a enviar solicitudes al punto de conexión alternativo. Se aplica la misma directiva de reintentos a la acción original y a la acción alternativa.

![Comportamiento de Scheduler con directivas de reintentos y punto de conexión alternativo](./media/scheduler-high-availability-reliability/scheduler-high-availability-reliability-image2.png)

El tipo de acción alternativa puede diferir de la acción original. Por ejemplo, aunque la acción original llama a un punto de conexión HTTP, la acción alternativa podría registrar errores mediante el una cola de Storage, una cola de Service Bus o una acción de tema de Service Bus.

Para más información sobre cómo configurar un punto de conexión alternativo, consulte [errorAction](scheduler-concepts-terms.md#error-action).

## <a name="see-also"></a>Otras referencias

* [¿Qué es Azure Scheduler?](scheduler-intro.md)
* [Conceptos, terminología y jerarquía de entidades](scheduler-concepts-terms.md)
* [Creación de programaciones complejas y periodicidad avanzada](scheduler-advanced-complexity.md)
* [Límites, cuotas, valores predeterminados y códigos de error](scheduler-limits-defaults-errors.md)
