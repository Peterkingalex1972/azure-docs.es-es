---
title: Conexión del código del dispositivo de ejemplo de la versión preliminar de IoT Plug and Play a IoT Hub | Microsoft Docs
description: Compile y ejecute el código de dispositivo de ejemplo de IoT Plug and Play que se conecta a un centro de IoT. Use la herramienta Azure IoT Explorer para ver la información enviada por el dispositivo al centro.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 08/02/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: b233fcecfe80d1ce4464d2d02fdddb188f9265a7
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69881584"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-to-iot-hub"></a>Inicio rápido: Conexión de una aplicación de dispositivo de ejemplo de la versión preliminar de IoT Plug and Play a IoT Hub

En este inicio rápido se muestra cómo crear una aplicación de dispositivo IoT Plug and Play de ejemplo, conectarla a IoT Hub y usar la herramienta Azure IoT Explorer para ver la información que envía al centro. La aplicación de ejemplo está escrita en C y se incluye en el SDK de dispositivo IoT de Azure para C. Los desarrolladores de soluciones puede usar la herramienta Azure IoT Explorer para conocer las funcionalidades de cualquier dispositivo IoT Plug and Play sin necesidad de ver nada de código de dispositivo.

## <a name="prerequisites"></a>Requisitos previos

Para completar este inicio rápido, es preciso instalar el siguiente software en el equipo local:

* [Visual Studio (Community, Professional o Enterprise)](https://visualstudio.microsoft.com/downloads/): asegúrese de incluir el componente **Administrador de paquetes NuGet** y la carga de trabajo **Desarrollo para el escritorio con C++** al instalar Visual Studio.
* [Git](https://git-scm.com/download/).
* [CMake](https://cmake.org/download/).

### <a name="install-the-azure-iot-explorer"></a>Instalación de Azure IoT Explorer

Descargue e instale la herramienta Azure IoT Explorer desde la página de la[versión más reciente](https://github.com/Azure/azure-iot-explorer/releases).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-an-iot-hub"></a>Preparación de un centro de IoT

También necesitará un centro de IoT de Azure en la suscripción de Azure para completar este inicio rápido. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Agregue la extensión Microsoft Azure IoT para la CLI de Azure:

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

Ejecute el siguiente comando la nueva identidad del dispositivo en su centro de IoT. Reemplace los marcadores de posición **YourIoTHubName** y **YourDevice** por los nombres reales. Si aún no tiene un centro de IoT, siga [estas instrucciones para crearlo](../iot-hub/iot-hub-create-using-cli.md):

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id [YourDevice]
```

Ejecute los siguientes comandos para obtener la _cadena de conexión del dispositivo_ que acaba de registrar:

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id [YourDevice] --output table
```

Ejecute los comandos siguientes para obtener la _cadena de conexión de su centro de IoT_:

```azurecli-interactive
az iot hub show-connection-string --hub-name [YourIoTHubName] --output table
```

## <a name="prepare-the-development-environment"></a>Preparación del entorno de desarrollo

### <a name="get-azure-iot-device-sdk-for-c"></a>Obtención del SDK de dispositivo IoT de Azure para C

En este inicio rápido se prepara un entorno de desarrollo que se puede usar para clonar y compilar el SDK de dispositivo IoT de Azure para C.

Abra el símbolo del sistema. Ejecute el siguiente comando para clonar el repositorio de GitHub del [SDK para C de Azure IoT](https://github.com/Azure/azure-iot-sdk-c):

```cmd/sh
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

Esta operación puede tardar varios minutos en completarse.

## <a name="build-the-code"></a>Compilación del código

La aplicación que se compila simula un dispositivo que se conecta a un centro de IoT. La aplicación envía datos de telemetría y propiedades, y recibe comandos.

1. Cree un subdirectorio `cmake` en la carpeta raíz del SDK del dispositivo y vaya a esa carpeta:

    ```cmd\sh
    cd <root folder>\azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. Ejecute los siguientes comandos para compilar el SDK del dispositivo y el código auxiliar generado:

    ```cmd\sh
    cmake ..
    cmake --build . -- /m /p:Configuration=Release
    ```

    > [!NOTE]
    > Si cmake no encuentra el compilador de C++, aparecerán errores de compilación al ejecutar el comando anterior. En ese caso, pruebe a ejecutar el comando en el [símbolo del sistema de Visual Studio](https://docs.microsoft.com/dotnet/framework/tools/developer-command-prompt-for-vs).

## <a name="run-the-device-sample"></a>Ejecución del ejemplo de dispositivo

Para ejecute la aplicación, use la cadena de conexión del dispositivo al centro de IoT como parámetro.

```cmd\sh
cd digitaltwin_client\samples\digitaltwin_sample_device\Release
copy ..\EnvironmentalSensor.interface.json .
digitaltwin_sample_device.exe "[IoT Hub device connection string]"
```

La aplicación del dispositivo comienza a enviar datos a IoT Hub.

## <a name="use-the-azure-iot-explorer-to-validate-the-code"></a>Uso de Azure IoT Explorer para validar el código

1. Abra Azure IoT Explorer y verá la página **App configurations** (Configuraciones de aplicaciones).

1. Escriba la cadena de conexión de IoT Hub y haga clic en **Conectar**.

1. Después de conectarse, verá la página de información general del dispositivo.

1. Para agregar el repositorio de la empresa, seleccione **Configuración**, después, **+ New** (+ Nuevo) y, después, **On the connected device** (En el dispositivo conectado).

1. En la página de información general del dispositivo, busque la identidad del dispositivo que creó anteriormente y selecciónela para ver más detalles.

1. Expanda la interfaz con el identificador **urn:YOUR_COMPANY_NAME_HERE:EnvironmentalSensor:1** para ver las primitivas de IoT Plug and Play (propiedades, comandos y telemetría).

1. Seleccione la página **Telemetry** (Telemetría) para ver los datos de telemetría que envía el dispositivo.

1. Seleccione la página **Properties(non-writable)** [Propiedades (no se permite la escritura)] para ver las propiedades que no permiten la escritura notificadas por el dispositivo.

1. Seleccione la página **Properties(writable)** [Propiedades (se permite la escritura)] para ver las propiedades que permiten la escritura que se pueden actualizar.

1. Expanda el **nombre** de la propiedad, actualícelo con un nuevo nombre y seleccione **update writable property** (actualizar propiedad editable). 

1. Para ver si el nuevo nombre se muestra en la columna **Reported Property** (Propiedad notificada), haga clic en el botón **Refresh** (Actualizar) de la parte superior de la página.

1. Seleccione la página **Command** (Comando) para ver todos los comandos que admite el dispositivo.

1. Expanda el comando **blink** y establezca un nuevo intervalo de tiempo de intermitencia. Seleccione **Send Command** (Enviar comando) para llamar al comando en el dispositivo.

1. Vaya al dispositivo simulado para comprobar que el comando se ejecutó como se esperaba.

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a conectar un dispositivo IoT Plug and Play a un centro de IoT. Para más información sobre cómo crear una solución que interactúe con dispositivos IoT Plug and Play, consulte:

> [!div class="nextstepaction"]
> [Procedimiento: Conexión e interacción con un dispositivo de la versión preliminar de IoT Plug and Play](howto-develop-solution.md)
