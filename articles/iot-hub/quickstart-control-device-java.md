---
title: 'Inicio rápido: Control de un dispositivo desde Azure IoT Hub con Java'
description: En este inicio rápido, ejecuta dos aplicaciones de Java de muestra. Una aplicación es una aplicación back-end que puede controlar dispositivos conectados al centro de manera remota. La otra aplicación simula un dispositivo conectado al centro que se puede controlar de manera remota.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.devlang: java
ms.topic: quickstart
ms.custom: mvc, seo-java-august2019
ms.date: 06/21/2019
ms.openlocfilehash: 977bf07c8383bb1086e7878bd10f2519cc2f40ad
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68958634"
---
# <a name="quickstart-control-a-device-connected-to-an-iot-hub-java"></a>Inicio rápido: Control de un dispositivo conectado a un centro de IoT (Java)

[!INCLUDE [iot-hub-quickstarts-2-selector](../../includes/iot-hub-quickstarts-2-selector.md)]

IoT Hub es un servicio de Azure que le permite ingerir grandes volúmenes de datos de telemetría desde los dispositivos IoT en la nube y administrar dispositivos desde la nube. En este inicio rápido, usa un *método directo* para controlar un dispositivo simulado conectado a IoT Hub. Puede usar métodos directos para cambiar el comportamiento de un dispositivo conectado a IoT Hub de manera remota.

El inicio rápido usa dos aplicaciones Java escritas anteriormente:

* Una aplicación de dispositivo simulado que responde a métodos directos que se llaman desde una aplicación back-end. Para recibir las llamadas de método directo, esta aplicación se conecta a un punto de conexión específico del dispositivo en IoT Hub.

* Una aplicación back-end que llama a los métodos directos en el dispositivo simulado. Para llamar a un método directo en un dispositivo, esta aplicación se conecta a un punto de conexión de servicio en IoT Hub.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Las dos aplicaciones de ejemplo que se ejecutan en este inicio rápido se escriben con Java. Necesita Java SE 8 o una versión posterior en el equipo de desarrollo.

Puede descargar Java para varias plataformas desde [Oracle](https://aka.ms/azure-jdks).

Puede verificar la versión actual de Java en el equipo de desarrollo con el comando siguiente:

```cmd/sh
java -version
```

Para compilar las muestras, deberá instalar Maven 3. Puede descargar Maven para varias plataformas desde [Apache Maven](https://maven.apache.org/download.cgi).

Puede verificar la versión actual de Maven en el equipo de desarrollo con el comando siguiente:

```cmd/sh
mvn --version
```

Ejecute el siguiente comando para agregar la extensión IoT de Microsoft Azure para la CLI de Azure a la instancia de Cloud Shell. La extensión IOT agrega comandos específicos de IoT Hub, IoT Edge e IoT Device Provisioning Service (DPS) a la CLI de Azure.

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Si aún no lo ha hecho, descargue el proyecto de Java de muestra desde https://github.com/Azure-Samples/azure-iot-samples-java/archive/master.zip y extraiga el archivo ZIP.

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Si ha completado la anterior [Guía de inicio rápido: Envío de telemetría desde un dispositivo a un centro de IoT](quickstart-send-telemetry-java.md) puede omitir este paso.

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-device"></a>Registrar un dispositivo

Si ha completado la anterior [Guía de inicio rápido: Envío de telemetría desde un dispositivo a un centro de IoT](quickstart-send-telemetry-java.md) puede omitir este paso.

Debe registrar un dispositivo con IoT Hub antes de poder conectarlo. En esta guía de inicio rápido, usará Azure Cloud Shell para registrar un dispositivo simulado.

1. Ejecute los siguientes comandos en Azure Cloud Shell para crear la identidad del dispositivo.

   **YourIoTHubName**: reemplace este marcador de posición por el nombre elegido para el centro de IoT.

   **MyJavaDevice**: nombre del dispositivo que se va a registrar. Use **MyJavaDevice** tal como se muestra. Si elige otro nombre para el dispositivo, puede que tenga que usarlo en todo el artículo y actualizar el nombre del dispositivo en las aplicaciones de ejemplo antes de ejecutarlas.

    ```azurecli-interactive
    az iot hub device-identity create \
      --hub-name YourIoTHubName --device-id MyJavaDevice
    ```

2. Ejecute los siguientes comandos en Azure Cloud Shell para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

   **YourIoTHubName**: reemplace este marcador de posición por el nombre elegido para el centro de IoT.

    ```azurecli-interactive
    az iot hub device-identity show-connection-string \
      --hub-name YourIoTHubName \
      --device-id MyJavaDevice \
      --output table
    ```

    Anote la cadena de conexión del dispositivo, que se parecerá a esta:

   `HostName={YourIoTHubName}.azure-devices.net;DeviceId=MyNodeDevice;SharedAccessKey={YourSharedAccessKey}`

    Usará este valor más adelante en este inicio rápido.

## <a name="retrieve-the-service-connection-string"></a>Recuperación de la cadena de conexión de servicio

También necesita una _cadena de conexión de servicio_ para permitir que la aplicación back-end se conecte a IoT Hub y recupere los mensajes. El comando siguiente recupera la cadena de conexión del servicio de su instancia de IoT Hub:

**YourIoTHubName**: reemplace este marcador de posición por el nombre elegido para el centro de IoT.

```azurecli-interactive
az iot hub show-connection-string --name YourIoTHubName --policy-name service --output table
```

Anote la cadena de conexión del servicio, que se parecerá a esta:

`HostName={YourIoTHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}`

Usará este valor más adelante en este inicio rápido. La cadena de conexión de servicio es diferente de la cadena de conexión de dispositivo.

## <a name="listen-for-direct-method-calls"></a>Escuchas para llamadas de método directo

La aplicación del dispositivo simulado se conecta a un punto de conexión específico del dispositivo en IoT Hub, envía los datos de telemetría simulados y escucha llamadas de método directo desde el centro. En este inicio rápido, la llamada de método directo desde el centro indica al dispositivo que debe cambiar el intervalo en el que envía los datos de telemetría. El dispositivo simulado envía una confirmación al centro después de que ejecute el método directo.

1. En una ventana de terminal local, vaya a la carpeta raíz del proyecto de Java de ejemplo. A continuación, vaya a la carpeta **iot-hub\Quickstarts\simulated-device-2**.

2. Abra el archivo **src/main/java/com/microsoft/docs/iothub/samples/SimulatedDevice.java** en el editor de texto de su elección.

    Reemplace el valor de la variable `connString` por la cadena de conexión de dispositivo que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **SimulatedDevice.java**.

3. En la ventana de terminal local, ejecute los comandos siguientes para instalar las bibliotecas necesarias y compile la aplicación de dispositivo simulado:

    ```cmd/sh
    mvn clean package
    ```

4. En la ventana de terminal local, ejecute los comandos siguientes para ejecutar la aplicación de dispositivo simulado:

    ```cmd/sh
    java -jar target/simulated-device-2-1.0.0-with-deps.jar
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación de dispositivo simulado envía datos de telemetría a IoT Hub:

    ![Ejecutar el dispositivo simulado](./media/quickstart-control-device-java/SimulatedDevice-1.png)

## <a name="call-the-direct-method"></a>Llamar al método directo

La aplicación back-end se conecta a un punto de conexión de servicio en IoT Hub. La aplicación realiza llamadas de método directo a un dispositivo con IoT Hub y realiza escuchas para confirmaciones. Normalmente, una aplicación back-end de IoT Hub se ejecuta en la nube.

1. En otra ventana de terminal local, vaya a la carpeta raíz del proyecto de Java de ejemplo. A continuación, vaya a la carpeta **iot-hub\Quickstarts\back-end-application**.

2. Abra el archivo **src/main/java/com/microsoft/docs/iothub/samples/BackEndApplication.java** en el editor de texto que prefiera.

    Reemplace el valor de la variable `iotHubConnectionString` por la cadena de conexión de servicio que anotó anteriormente. A continuación, guarde los cambios realizados en el archivo **BackEndApplication.java**.

3. En la ventana de terminal local, ejecute los comandos siguientes para instalar las bibliotecas necesarias y compile la aplicación back-end:

    ```cmd/sh
    mvn clean package
    ```

4. En la ventana de terminal local, ejecute los comandos siguientes para ejecutar la aplicación back-end:

    ```cmd/sh
    java -jar target/back-end-application-1.0.0-with-deps.jar
    ```

    La siguiente captura de pantalla muestra la salida en la que la aplicación realiza una llamada de método directo en el dispositivo y recibe una confirmación:

    ![Ejecutar la aplicación back-end](./media/quickstart-control-device-java/BackEndApplication.png)

    Después de ejecutar la aplicación back-end, verá un mensaje en la ventana de consola que ejecuta el dispositivo simulado y cambiará la velocidad a la que envía mensajes:

    ![Cambio en el cliente simulado](./media/quickstart-control-device-java/SimulatedDevice-2.png)

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-hub-quickstarts-clean-up-resources](../../includes/iot-hub-quickstarts-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha llamado a un método directo en un dispositivo desde una aplicación de back-end y ha respondido a la llamada de método directo en una aplicación de dispositivo simulado.

Para obtener información sobre cómo redirigir mensajes del dispositivo a la nube a diferentes destinos en la nube, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Tutorial: Enrutar datos de telemetría a distintos puntos de conexión para procesamiento](tutorial-routing.md)
