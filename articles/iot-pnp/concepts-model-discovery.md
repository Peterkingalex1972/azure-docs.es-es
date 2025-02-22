---
title: Implementación de la detección de modelo de IoT Plug and Play (versión preliminar) | Microsoft Docs
description: Los desarrolladores de soluciones deben aprender a implementar la detección de modelo de IoT Plug and Play en las soluciones.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/17/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: e4ab1d45e27762ef05ab7ec74c98ab0b0b934cbf
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879232"
---
# <a name="implement-iot-plug-and-play-preview-model-discovery-in-an-iot-solution"></a>Implementación de la detección de modelo de IoT Plug and Play (versión preliminar) en una solución de IoT

En este artículo se describe la forma en que los desarrolladores de soluciones, pueden implementar la detección de modelo de IoT Plug and Play (versión preliminar) en una solución de IoT.  La detección de modelo de IoT Plug and Play es tanto la forma en que los dispositivos IoT Plug and Play identifican sus interfaces y modelos de interfaces de funcionalidad compatibles como la forma en que una solución de IoT recupera esos modelos de funcionalidad e interfaces.

Hay dos amplias categorías de soluciones de IoT: soluciones creadas específicamente que funcionan con un conjunto conocido de dispositivos Plug and Play de IoT y soluciones basadas en modelos que funcionan con cualquier dispositivo IoT Plug and Play.

En este artículo se describe cómo implementar la detección de modelo en ambos tipos de soluciones.

## <a name="model-discovery"></a>Detección de modelo

La primera vez que un dispositivo IoT Plug and Play se conecta a un centro de IoT, envía un mensaje de telemetría con información del modelo. Este mensaje incluye los identificadores de las interfaces que el dispositivo implementa. Para que la solución funcione con el dispositivo, debe resolver esos identificadores y recuperar las definiciones de cada interfaz.

Estos son los pasos que da un dispositivo IoT Plug and Play cuando usa el servicio Device Provisioning (DPS) para conectarse a un centro:

1. Cuando el dispositivo está activado, se conecta al punto de conexión global del DPS y se autentica mediante uno de los métodos permitidos.
1. Luego, el DPS autentica el dispositivo y busca la regla que le indica a qué centro de IoT se debe asignar el dispositivo. Después, el DPS registra el dispositivo en ese centro.
1. El DPS devuelve una cadena de conexión del centro de IoT al dispositivo.
1. A continuación, el dispositivo envía un mensaje de telemetría de detección a su centro de IoT. Este mensaje contiene los identificadores de las interfaces que el dispositivo implementa.
1. El dispositivo IoT Plug and Play ya está listo para trabajar con una solución que usa su centro de IoT.

Si el dispositivo se conecta directamente a su centro de IoT, lo hace mediante una cadena de conexión que se ha insertado en el código del dispositivo. Luego, el dispositivo envía un mensaje de telemetría de detección a su centro de IoT.

Vea la interfaz [ModelInformation](concepts-common-interfaces.md) para más información sobre el mensaje de telemetría con información del modelo.

### <a name="purpose-built-iot-solutions"></a>Soluciones de IoT creadas específicamente

Las soluciones de IoT creadas específicamente funcionan con un conjunto conocido de interfaces y modelos de funcionalidad del dispositivo IoT Plug and Play.

Tendrá las interfaces y el modelo de funcionalidad de los dispositivos que se van a conectarán a la solución con anterioridad. Siga estos pasos para preparar la solución:

1. Almacene los archivos JSON de la interfaz en Azure en una ubicación donde la solución pueda leerlos.
1. Escriba la lógica de la solución de IoT en función de la interfaz y los modelos de funcionalidad de IoT Plug and Play esperados.
1. Suscríbase a las notificaciones del centro de IoT que usa la solución.

Cuando reciba la notificación de que se ha conectado un dispositivo, siga estos pasos:

1. Lea el mensaje de telemetría de detección para recuperar los identificadores del modelo de funcionalidad y las interfaces que ha implementado el dispositivo.
1. Compare el identificador del modelo de funcionalidad con los de los modelos de capacidad almacenados con anterioridad.
1. Así sabe qué tipo de dispositivo se ha conectado. Use la lógica que ha escrito anteriormente para que los usuarios puedan interactuar con el dispositivo de forma adecuada.

### <a name="model-driven-solutions"></a>Soluciones basadas en modelos

Las soluciones de IoT basadas en modelo puede funcionar con cualquier dispositivo IoT Plug and Play. La creación de una solución de IoT basada en modelos es más compleja, pero la ventaja es que la solución funcionará con cualquier dispositivo que agregue en el futuro.

Para crear una solución de IoT basada en modelo es preciso crear una lógica con las primitivas de la interfaz de IoT Plug and Play: telemetría, propiedades y comandos. La lógica de la solución de IoT representa un dispositivo mediante la combinación de las funcionalidades de varios comandos, propiedades y datos de telemetría.

La solución también se debe suscribir a las notificaciones del centro de IoT que use.

Cuando la solución reciba una notificación de que se ha conectado un dispositivo, siga estos pasos:

1. Lea el mensaje de telemetría de detección para recuperar los identificadores del modelo de funcionalidad y las interfaces que ha implementado el dispositivo.
1. Para cada identificador, lea todo el archivo JSON para buscar las funcionalidades del dispositivo.
1. Compruebe si cada interfaz está presente en las memorias caché que ha creado para almacenar los archivos JSON que la solución ha recuperado con anterioridad.
1. Luego, compruebe si hay alguna interfaz con ese identificador en el repositorio de modelos global. Para más información, consulte [Repositorio de modelos global](howto-manage-models.md).
1. Si la interfaz no se encuentra en el repositorio de modelos global, pruebe a buscarla en los repositorios de modelos privados que conozca en la solución. Para acceder a un repositorio de modelos privado. Para más información, consulte [Repositorio de modelos privado](howto-manage-models.md).
1. Si no encuentra todas las interfaces en el repositorio de modelos global o en un repositorio de modelos privado, puede comprobar si el dispositivo puede proporcionar la definición de la interfaz. Un dispositivo puede implementar la interfaz [ModelDefinition](concepts-common-interfaces.md) estándar para publicar información sobre cómo recuperar archivos de interfaz con un comando.
1. Si ha encontrado archivos JSON para todas las interfaces que ha implementado el dispositivo, puede enumerar las funcionalidades del dispositivo. Use la lógica que escribió anteriormente para que los usuarios puedan interactuar con el dispositivo.
1. En cualquier momento puede llamar a la API de gemelos digitales para recuperar el identificador del modelo de funcionalidad y los identificadores de interfaz del dispositivo.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha obtenido información sobre la detección de modelos en una solución de IoT, consulte [Plataforma Azure IoT](overview-iot-plug-and-play.md) para aprovechar otras funcionalidades de la solución.
