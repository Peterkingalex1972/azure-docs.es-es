---
title: archivo de inclusión
description: archivo de inclusión
services: event-hubs
author: sethmanheim
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c163e3cce862640d43f8696dca4eeef29f2ae12a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912494"
---
En la tabla siguiente se enumeran las cuotas y los límites específicos de [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/). Para más información sobre los precios de Event Hubs, consulte los [precios de Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).

| Límite | Ámbito | Notas | Valor |
| --- | --- | --- | --- |
| Número de espacios de nombres de Event Hubs por suscripción |Subscription |- |100 |
| Número de centros de eventos por espacio de nombres |Espacio de nombres |Las solicitudes posteriores para la creación de un nuevo centro de eventos se rechazan. |10 |
| Número de particiones por centro de eventos |Entidad |- |32 |
| Tamaño máximo de evento de Event Hubs|Entidad |- |1 MB |
| Tamaño máximo del nombre de un centro de eventos |Entidad |- |50 caracteres |
| Número de destinatarios no de época por grupo de consumidores |Entidad |- |5 |
| Unidades de rendimiento máximo |Espacio de nombres |Si se supera el límite de unidades de procesamiento, los datos se limitan y se genera un excepción [ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception). Para solicitar un número mayor de unidades de procesamiento para el nivel Estándar rellene una [solicitud de soporte técnico](/azure/azure-supportability/how-to-create-azure-support-request). Las [unidades de rendimiento adicionales](../articles/event-hubs/event-hubs-auto-inflate.md) se encuentran disponibles en bloques de 20 y están sujetas a un compromiso de compra. |20 |
| Número de reglas de autorización por espacio de nombres |Espacio de nombres|Las solicitudes posteriores de creación de reglas de autorización se rechazan.|12 |
| Número de llamadas al método GetRuntimeInformation | Entidad | - | 50 por segundo | 
| Número de reglas de red virtual (VNet) y de configuración IP | Entidad | - | 128 | 

### <a name="event-hubs-basic-and-standard---quotas-and-limits"></a>Niveles Básico y Estándar de Event Hubs: límites y cuotas
| Límite | Ámbito | Notas | Básica | Estándar |
| --- | --- | --- | -- | --- |
| Número de grupos de consumidores por centro de eventos |Entidad | - |1 |20 |
| Número de conexiones de AMQP por espacio de nombres |Espacio de nombres |Las solicitudes posteriores de conexiones adicionales se rechazan y el código que llama recibe una excepción. |100 |5\.000|
| Período de retención máximo de datos de eventos |Entidad | - |1 día |1-7 días |
|Espacio de nombres habilitado para Apache Kafka|Espacio de nombres |Aplicaciones de flujos de espacios de nombres de Event Hubs que usan el protocolo Kafka. |Sin | Sí |
|Capture |Entidad | Cuando está habilitado, microlotes en el mismo flujo. |Sin |Sí |


### <a name="event-hubs-dedicated---quotas-and-limits"></a>Cuotas y límites de Event Hubs dedicado
La oferta de Event Hubs dedicado se factura aplicando una tarifa mensual fija con un uso mínimo de 4 horas. El nivel Dedicado ofrece todas las características del plan Estándar, pero con una capacidad de escalado de nivel empresarial para aquellos clientes que tienen cargas de trabajo muy exigentes. 

| Característica | límites |
| --- | ---|
| Ancho de banda |  20 CU |
| Espacios de nombres | 50 por CU |
| Event Hubs |  1000 por espacio de nombres |
| Eventos de entrada | Se incluye |
| Tamaño de los mensajes | 1 millón de bytes |
| Particiones | 2000 por CU |
| Grupos de consumidores | Sin límite por CU, 1000 por centro de eventos |
| Conexiones asincrónicas | 100 000 incluidos |
| Retención de mensajes | Hasta 7 días (próximamente se incorporará la retención de 90 días), 10 TB incluidos por CU |
| Capture | Se incluye |
