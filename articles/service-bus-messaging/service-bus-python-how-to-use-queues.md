---
title: Uso de colas de Service Bus de Azure con Python | Microsoft Docs
description: Aprenda a usar las colas de de Azure Service Bus desde Python.
services: service-bus-messaging
documentationcenter: python
author: axisc
manager: timlt
editor: spelluru
ms.assetid: b95ee5cd-3b31-459c-a7f3-cf8bcf77858b
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 04/10/2019
ms.author: aschhab
ms.openlocfilehash: fa3aedf138564fedafe555adfbaf6c56efc1813e
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68360846"
---
# <a name="how-to-use-service-bus-queues-with-python"></a>Uso de colas de Service Bus con Python

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

En este tutorial, obtendrá información sobre cómo crear aplicaciones de Python para enviar mensajes a una cola Service Bus y recibir mensajes de una cola de Service Bus. 

## <a name="prerequisites"></a>Requisitos previos
1. Una suscripción de Azure. Para completar este tutorial, deberá tener una cuenta de Azure. Puede activar sus [ventajas de suscriptor a MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) o registrarse para obtener una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
2. Siga los pasos del artículo [Uso de Azure Portal para crear una cola de Service Bus](service-bus-quickstart-portal.md).
    1. Lea la **introducción** rápida de las **colas** de Service Bus. 
    2. Cree un **espacio de nombres** de Service Bus. 
    3. Obtenga la **cadena de conexión**. 

        > [!NOTE]
        > Creará una **cola** en el espacio de nombres de Service Bus con Python en este tutorial. 
1. Para instalar Python o el [paquete de Azure Service Bus para Python][Python Azure Service Bus package], consulte la [Guía de instalación de Python](../python-how-to-install.md). Consulte la documentación completa del SDK de Python de Service Bus [aquí](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="create-a-queue"></a>Creación de una cola
El objeto **ServiceBusService** le permite trabajar con colas. Agregue el siguiente código cerca de la parte superior de todo archivo Python en el que desee obtener acceso a Service Bus mediante programación:

```python
from azure.servicebus import ServiceBusClient
```

El siguiente código crea un objeto **ServiceBusService**. Reemplace `<CONNECTION STRING>` por la cadena de conexión de Service Bus.

```python
sb_client = ServiceBusClient.from_connection_string('<CONNECTION STRING>')
```

Los valores para el nombre de clave y el valor de la SAS pueden encontrarse en la información de conexión de [Azure Portal][Azure portal] o en el panel **Propiedades** de Visual Studio al seleccionar el espacio de nombres de Service Bus en el Explorador de servidores (como se muestra en la sección anterior).

```python
sb_client.create_queue("taskqueue")
```

El método `create_queue` también admite opciones adicionales, que le permiten invalidar la configuración predeterminada de las colas, como el período de vida de los mensajes (TTL) o el tamaño máximo de las colas. En el siguiente ejemplo se establece el tamaño máximo de las colas en 5 GB y el valor de TTL en 1 minuto:

```python
sb_client.create_queue("taskqueue", max_size_in_megabytes=5120,
                       default_message_time_to_live=datetime.timedelta(minutes=1))
```

Para más información, consulte la [documentación de Python para Azure Service Bus](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="send-messages-to-a-queue"></a>mensajes a una cola
Para enviar un mensaje a una cola de Service Bus, su aplicación llama al método `send` en el objeto `ServiceBusClient`.

En el ejemplo siguiente se muestra cómo enviar un mensaje de prueba a la cola `taskqueue` mediante `send_queue_message`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Send a test message to the queue
msg = Message(b'Test Message')
queue_client.send(msg)
```

El tamaño máximo de mensaje que admiten las colas de Service Bus es de 256 KB en el [nivel Estándar](service-bus-premium-messaging.md) y de 1 MB en el [nivel Premium](service-bus-premium-messaging.md). El encabezado, que incluye propiedades de la aplicación estándar y personalizadas, puede tener un tamaño máximo de 64 KB. No hay límite para el número de mensajes que contiene una cola, pero hay un tope para el tamaño total de los mensajes contenidos en una cola. El tamaño de la cola se define en el momento de la creación, con un límite de 5 GB. Para obtener más información sobre las cuotas, consulte [Cuotas de Service Bus][Service Bus quotas].

Para más información, consulte la [documentación de Python para Azure Service Bus](/python/api/overview/azure/servicebus?view=azure-python).

## <a name="receive-messages-from-a-queue"></a>mensajes de una cola
Los mensajes se reciben de una cola utilizando el método `get_receiver` del objeto `ServiceBusService`:

```python
from azure.servicebus import QueueClient, Message

# Create the QueueClient
queue_client = QueueClient.from_connection_string(
    "<CONNECTION STRING>", "<QUEUE NAME>")

# Receive the message from the queue
with queue_client.get_receiver() as queue_receiver:
    messages = queue_receiver.fetch_next(timeout=3)
    for message in messages:
        print(message)
        message.complete()
```

Para más información, consulte la [documentación de Python para Azure Service Bus](/python/api/overview/azure/servicebus?view=azure-python).


Los mensajes se eliminan de la cola a medida que se leen cuando el parámetro `peek_lock` está establecido en **False**. Puede leer y bloquear los mensajes sin eliminarlos de la cola si establece el parámetro opcional `peek_lock` en **True**.

El comportamiento por el que los mensajes se eliminan tras leerlos como parte del proceso de recepción es el modelo más sencillo y el que mejor funciona en aquellas situaciones en las que una aplicación puede tolerar que no se procese un mensaje en caso de error. Para entenderlo mejor, pongamos una situación en la que un consumidor emite la solicitud de recepción que se bloquea antes de procesarla. Como Service Bus habrá marcado el mensaje como consumido, cuando la aplicación se reinicie y empiece a consumir mensajes de nuevo, habrá perdido el mensaje que se consumió antes del bloqueo.

Si el parámetro `peek_lock` está establecido en **True**, el proceso de recepción se convierte en una operación en dos fases que permite admitir aplicaciones que no toleran la pérdida de mensajes. Cuando Service Bus recibe una solicitud, busca el siguiente mensaje que se va a consumir, lo bloquea para impedir que otros consumidores lo reciban y, a continuación, lo devuelve a la aplicación. Una vez que la aplicación termina de procesar el mensaje (o lo almacena de forma confiable para su futuro procesamiento), completa la segunda fase del proceso de recepción llamando al método **delete** en el objeto **Message**. El método **delete** marcará el mensaje como consumido y lo eliminará de la cola.

```python
msg.delete()
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Actuación ante errores de la aplicación y mensajes que no se pueden leer
Service Bus proporciona una funcionalidad que le ayuda a superar sin problemas los errores de la aplicación o las dificultades para procesar un mensaje. Si por cualquier motivo una aplicación de recepción es incapaz de procesar el mensaje, entonces puede llamar al método **unlock** del objeto **Message**. Esto hará que Service Bus desbloquee el mensaje de la cola y esté disponible para que pueda volver a recibirse, ya sea por la misma aplicación que lo consume o por otra.

También hay un tiempo de espera asociado con un mensaje bloqueado en la cola y, si la aplicación no puede procesar el mensaje antes de que finalice el tiempo de espera del bloqueo (por ejemplo, si la aplicación sufre un error), entonces Service Bus desbloquea el mensaje automáticamente y hace que esté disponible para que pueda volver a recibirse.

En caso de que la aplicación sufra un error después de procesar el mensaje y antes de llamar al método **delete**, entonces el mensaje se volverá a entregar a la aplicación cuando esta se reinicie. Habitualmente se denomina **al menos un procesamiento**, es decir, cada mensaje se procesará al menos una vez, aunque en determinadas situaciones podría volver a entregarse el mismo mensaje. Si el escenario no puede tolerar el procesamiento duplicado, entonces los desarrolladores de la aplicación deberían agregar lógica adicional a su aplicación para solucionar la entrega de mensajes duplicados. A menudo, esto se consigue usando la propiedad **MessageId** del mensaje, que permanecerá constante en todos los intentos de entrega.

> [!NOTE]
> Puede administrar los recursos de Service Bus con el [Explorador de Service Bus](https://github.com/paolosalvatori/ServiceBusExplorer/). El Explorador de Service Bus permite a los usuarios conectarse a un espacio de nombres de Service Bus y administrar las entidades de mensajería de una forma sencilla. La herramienta dispone de características avanzadas, como la funcionalidad de importación y exportación o la capacidad de probar el tema, las colas, las suscripciones, los servicios de retransmisión, los centros de notificaciones y los centros de eventos. 

## <a name="next-steps"></a>Pasos siguientes
Ahora que conoce los fundamentos de las colas de Service Bus, consulte estos vínculos para obtener más información.

* [Colas, temas y suscripciones][Queues, topics, and subscriptions]

[Azure portal]: https://portal.azure.com
[Python Azure Service Bus package]: https://pypi.python.org/pypi/azure-servicebus  
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[Service Bus quotas]: service-bus-quotas.md

