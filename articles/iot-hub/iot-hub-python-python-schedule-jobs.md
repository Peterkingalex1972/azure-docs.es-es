---
title: Programación de trabajos con Azure IoT Hub (Python) | Microsoft Docs
description: Cómo programar un trabajo de IoT Hub de Azure para invocar un método directo en varios dispositivos. El SDK de IoT de Azure para Python se usa para implementar las aplicaciones de dispositivo simulado y una aplicación de servicio para ejecutar el trabajo.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.devlang: python
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: robinsh
ms.openlocfilehash: bc93dcea74f0660a4f56aec237eaae5a4e3eeb9a
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69904522"
---
# <a name="schedule-and-broadcast-jobs-python"></a>Programación y difusión de trabajos (Python)

[!INCLUDE [iot-hub-selector-schedule-jobs](../../includes/iot-hub-selector-schedule-jobs.md)]

IoT Hub de Azure es un servicio completamente administrado que permite a una aplicación back-end crear y realizar un seguimiento de los trabajos que programan y actualizan millones de dispositivos.  Los trabajos pueden utilizarse para las siguientes acciones:

* Actualizar las propiedades deseadas
* Actualizar etiquetas
* Invocar métodos directos

Conceptualmente, un trabajo contiene una de estas acciones y realiza un seguimiento del progreso de ejecución en un conjunto de dispositivos, que define una consulta de dispositivo gemelo.  Por ejemplo, una aplicación back-end puede usar un trabajo para invocar un método de reinicio en 10 000 dispositivos, especificado por una consulta de dispositivos gemelos y programada en el futuro.  Esa aplicación puede después seguir el progreso cuando cada uno de estos dispositivos reciben y ejecutan el método de reinicio.

Más información sobre estas funcionalidades en estos artículos:

* Dispositivo gemelo y propiedades: [Introducción a los dispositivos gemelos](iot-hub-python-twin-getstarted.md) y [Tutorial: uso de las propiedades de dispositivos gemelos](tutorial-device-twins.md)

* Métodos directos: [Guía para desarrolladores de IoT Hub: métodos directos](iot-hub-devguide-direct-methods.md) y [Tutorial: Métodos directos](quickstart-control-device-python.md)

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

En este tutorial se muestra cómo realizar las siguientes acciones:

* Crear una aplicación de dispositivo simulado en Python con un método directo, que habilita **lockDoor**, que la solución de back-end puede llamar.

* Crear una aplicación de consola de Python que llame al método directo **lockDoor** en la aplicación de dispositivo simulado mediante un trabajo y que actualice las propiedades deseadas mediante un trabajo de dispositivo.

Al final de este tutorial, tiene dos aplicaciones de Python:

**simDevice.py**, que se conecta a IoT Hub con la identidad del dispositivo y recibe un método directo **lockDoor**.

**scheduleJobService.py**, que llama a un método directo en la aplicación de dispositivo simulado y actualiza las propiedades deseadas del dispositivo gemelo mediante un trabajo.

> [!NOTE]
> El **SDK de IoT de Azure para Python** no admite directamente la funcionalidad de **trabajos**. En su lugar, este tutorial ofrece una solución alternativa con temporizadores y subprocesos asincrónicos. Para obtener más actualizaciones, consulte la lista de características del **SDK del cliente de servicio** en la página [Azure IoT SDK for Python](https://github.com/Azure/azure-iot-sdk-python) (SDK de IoT de Azure para Python).
>

[!INCLUDE [iot-hub-include-python-sdk-note](../../includes/iot-hub-include-python-sdk-note.md)]

A continuación se indican las instrucciones de instalación de los requisitos previos.

[!INCLUDE [iot-hub-include-python-installation-notes](../../includes/iot-hub-include-python-installation-notes.md)]

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

## <a name="register-a-new-device-in-the-iot-hub"></a>Registro de un nuevo dispositivo en el centro de IoT

[!INCLUDE [iot-hub-include-create-device](../../includes/iot-hub-include-create-device.md)]

## <a name="create-a-simulated-device-app"></a>Creación de una aplicación de dispositivo simulado

En esta sección, creará una aplicación de consola de Python que responda a un método directo al que la nube llama, que desencadena un método **lockDoor** simulado.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iot-device-client**:

    ```cmd/sh
    pip install azure-iothub-device-client
    ```

   > [!NOTE]
   > Los paquetes PIP para azure-iothub-service-client y azure-iothub-device-client actualmente solo están disponibles para el sistema operativo Windows. Para Linux o Mac OS, consulte las secciones específicas de Mac OS y Linux en la publicación [Preparar el entorno de desarrollo para Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md).
   >

2. Con un editor de texto, cree un nuevo archivo **simDevice.py** en el directorio de trabajo.

3. Agregue las siguientes instrucciones y variables `import` al principio del archivo **simDevice.py**. Reemplace `deviceConnectionString` con la cadena de conexión del dispositivo que creó anteriormente:

    ```python
    import time
    import sys

    import iothub_client
    from iothub_client import IoTHubClient, IoTHubClientError, IoTHubTransportProvider, IoTHubClientResult
    from iothub_client import IoTHubError, DeviceMethodReturnValue

    METHOD_CONTEXT = 0
    TWIN_CONTEXT = 0
    WAIT_COUNT = 10

    PROTOCOL = IoTHubTransportProvider.MQTT
    CONNECTION_STRING = "{deviceConnectionString}"
    ```

4. Agregue la siguiente devolución de llamada de función para controlar el método **lockDoor**:

    ```python
    def device_method_callback(method_name, payload, user_context):
        if method_name == "lockDoor":
            print ( "Locking Door!" )

            device_method_return_value = DeviceMethodReturnValue()
            device_method_return_value.response = "{ \"Response\": \"lockDoor called successfully\" }"
            device_method_return_value.status = 200
            return device_method_return_value
    ```

5. Agregue otra devolución de llamada de función para controlar las actualizaciones de los dispositivos gemelos:

    ```python
    def device_twin_callback(update_state, payload, user_context):
        print ( "")
        print ( "Twin callback called with:")
        print ( "payload: %s" % payload )
    ```

6. Agregue el código siguiente para registrar el controlador del método **lockDoor**. Incluya también la rutina `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            client = IoTHubClient(CONNECTION_STRING, PROTOCOL)
            client.set_device_method_callback(device_method_callback, METHOD_CONTEXT)
            client.set_device_twin_callback(device_twin_callback, TWIN_CONTEXT)

            print ( "Direct method initialized." )
            print ( "Device twin callback initialized." )
            print ( "IoTHubClient waiting for commands, press Ctrl-C to exit" )

            while True:
                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(10)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "Unexpected error %s from IoTHub" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "IoTHubClient sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub Python jobs sample..." )
        print ( "    Protocol %s" % PROTOCOL )
        print ( "    Connection string=%s" % CONNECTION_STRING )

        iothub_jobs_sample_run()
    ```

7. Guarde y cierre el archivo **simDevice.py**.

> [!NOTE]
> Por simplificar, este tutorial no implementa ninguna directiva de reintentos. En el código de producción, deberá implementar directivas de reintentos (por ejemplo, retroceso exponencial), tal y como se sugiere en el artículo [Control de errores transitorios](/azure/architecture/best-practices/transient-faults).
>

## <a name="get-the-iot-hub-connection-string"></a>Obtención de la cadena de conexión de IoT Hub

En este artículo, creará un servicio back-end que invoca un método directo en un dispositivo y actualiza el dispositivo gemelo. El servicio necesita el permiso **Conexión del servicio** para realizar una llamada al método directo de un dispositivo. El servicio también necesita los permisos **Lectura del registro** y **Escribir en el registro** para leer y escribir en el registro de identidad. No hay ninguna directiva de acceso compartido predeterminada que contenga solo estos permisos, por lo que tendrá que crearla.

Para crear una directiva de acceso compartido que conceda los permisos **Conexión del servicio**, **Lectura del registro** y **Escribir en el registro** y obtener una cadena de conexión para esta directiva, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), abra IoT Hub. La manera más fácil de acceder a IoT Hub es seleccionar **Grupos de recursos**, seleccionar el grupo de recursos donde se encuentra IoT Hub y después seleccionarlo en la lista de recursos.

2. En el panel de la izquierda del centro de IoT, seleccione **Directivas de acceso compartido**.

3. En el menú superior situado encima de la lista de directivas, seleccione **Agregar**.

4. En el panel **Agregar una directiva de acceso compartida**, escriba un nombre descriptivo para la directiva, por ejemplo *serviceAndRegistryReadWrite*. En **Permisos**, seleccione **Conexión del servicio** y **Escribir en el registro** (**Lectura del registro** se selecciona automáticamente al seleccionar **Escribir en el registro**). Seleccione **Crear**.

    ![Cómo agregar una nueva directiva de acceso compartido](./media/iot-hub-python-python-schedule-jobs/add-policy.png)

5. En el panel **Directivas de acceso compartido**, seleccione la nueva directiva en la lista de directivas.

6. En **Claves de acceso compartido**, seleccione el icono de **Cadena de conexión -- clave principal** y guarde el valor.

    ![Recuperación de la cadena de conexión](./media/iot-hub-python-python-schedule-jobs/get-connection-string.png)

Para obtener más información sobre las directivas de acceso compartido y los permisos de IoT Hub, consulte [Permisos y control del acceso](./iot-hub-devguide-security.md#access-control-and-permissions).

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-device-twins-properties"></a>Programación de trabajos para llamar un método directo y actualización de las propiedades de un dispositivo gemelo

En esta sección, creará una aplicación de consola de Python que inicia un **lockDook** remoto en un dispositivo con un método directo y actualiza las propiedades del dispositivo gemelo.

1. En el símbolo del sistema, ejecute el siguiente comando para instalar el paquete **azure-iot-service-client**:

    ```cmd/sh
    pip install azure-iothub-service-client
    ```

   > [!NOTE]
   > Los paquetes PIP para azure-iothub-service-client y azure-iothub-device-client actualmente solo están disponibles para el sistema operativo Windows. Para Linux o Mac OS, consulte las secciones específicas de Mac OS y Linux en la publicación [Preparar el entorno de desarrollo para Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md).
   >

2. Con un editor de texto, cree un nuevo archivo **scheduleJobService.py** en el directorio de trabajo.

3. Agregue las siguientes instrucciones y variables `import` al principio del archivo **scheduleJobService.py**. Reemplace el marcador de posición `{IoTHubConnectionString}` por la cadena de conexión de IoT Hub que copió anteriormente en [Obtención de la cadena de conexión de IoT Hub](#get-the-iot-hub-connection-string). Reemplace el marcador de posición `{deviceId}` por el id. de dispositivo que registró en [Registro de un nuevo dispositivo en el centro de IoT](#register-a-new-device-in-the-iot-hub):

    ```python
    import sys
    import time
    import threading
    import uuid

    import iothub_service_client
    from iothub_service_client import IoTHubRegistryManager, IoTHubRegistryManagerAuthMethod
    from iothub_service_client import IoTHubDeviceTwin, IoTHubDeviceMethod, IoTHubError

    CONNECTION_STRING = "{IoTHubConnectionString}"
    DEVICE_ID = "{deviceId}"

    METHOD_NAME = "lockDoor"
    METHOD_PAYLOAD = "{\"lockTime\":\"10m\"}"
    UPDATE_JSON = "{\"properties\":{\"desired\":{\"building\":43,\"floor\":3}}}"
    TIMEOUT = 60
    WAIT_COUNT = 5
    ```

4. Agregue la siguiente función que se usa para consultar los dispositivos:

    ```python
    def query_condition(device_id):
        iothub_registry_manager = IoTHubRegistryManager(CONNECTION_STRING)

        number_of_devices = 10
        dev_list = iothub_registry_manager.get_device_list(number_of_devices)

        for device in range(0, number_of_devices):
            if dev_list[device].deviceId == device_id:
                return 1

        print ( "Device not found" )
        return 0
    ```

5. Agregue los métodos siguientes para ejecutar los trabajos que llaman al método directo y al dispositivo gemelo:

    ```python
    def device_method_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job: " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_device_method = IoTHubDeviceMethod(CONNECTION_STRING)

            response = iothub_device_method.invoke(device_id, METHOD_NAME, METHOD_PAYLOAD, TIMEOUT)

            print ( "" )
            print ( "Direct method " + METHOD_NAME + " called." )

    def device_twin_job(job_id, device_id, wait_time, execution_time):
        print ( "" )
        print ( "Scheduling job " + str(job_id) )
        time.sleep(wait_time)

        if query_condition(device_id):
            iothub_twin_method = IoTHubDeviceTwin(CONNECTION_STRING)

            twin_info = iothub_twin_method.update_twin(DEVICE_ID, UPDATE_JSON)

            print ( "" )
            print ( "Device twin updated." )
    ```

6. Agregue el código siguiente para programar los trabajos y actualizar sus estados. Incluya también la rutina `main`:

    ```python
    def iothub_jobs_sample_run():
        try:
            method_thr_id = uuid.uuid4()
            method_thr = threading.Thread(target=device_method_job, args=(method_thr_id, DEVICE_ID, 20, TIMEOUT), kwargs={})
            method_thr.start()

            print ( "" )
            print ( "Direct method called with Job Id: " + str(method_thr_id) )

            twin_thr_id = uuid.uuid4()
            twin_thr = threading.Thread(target=device_twin_job, args=(twin_thr_id, DEVICE_ID, 10, TIMEOUT), kwargs={})
            twin_thr.start()

            print ( "" )
            print ( "Device twin called with Job Id: " + str(twin_thr_id) )

            while True:
                print ( "" )

                if method_thr.is_alive():
                    print ( "...job " + str(method_thr_id) + " still running." )
                else:
                    print ( "...job " + str(method_thr_id) + " complete." )

                if twin_thr.is_alive():
                    print ( "...job " + str(twin_thr_id) + " still running." )
                else:
                    print ( "...job " + str(twin_thr_id) + " complete." )

                print ( "Job status posted, press Ctrl-C to exit" )

                status_counter = 0
                while status_counter <= WAIT_COUNT:
                    time.sleep(1)
                    status_counter += 1

        except IoTHubError as iothub_error:
            print ( "" )
            print ( "Unexpected error {0}" % iothub_error )
            return
        except KeyboardInterrupt:
            print ( "" )
            print ( "IoTHubService sample stopped" )

    if __name__ == '__main__':
        print ( "Starting the IoT Hub jobs Python sample..." )
        print ( "    Connection string = {0}".format(CONNECTION_STRING) )
        print ( "    Device ID         = {0}".format(DEVICE_ID) )

        iothub_jobs_sample_run()
    ```

7. Guarde y cierre el archivo **scheduleJobService.py**.

## <a name="run-the-applications"></a>Ejecución de las aplicaciones

Ahora está preparado para ejecutar las aplicaciones.

1. En el símbolo del sistema, en el directorio de trabajo, ejecute el siguiente comando para iniciar la escucha del método directo de reinicio:

    ```cmd/sh
    python simDevice.py
    ```

2. En otro símbolo del sistema, en el directorio de trabajo, ejecute el siguiente comando para desencadenar los trabajos a fin de bloquear la puerta y actualizar el dispositivo gemelo:
  
    ```cmd/sh
    python scheduleJobService.py
    ```

3. Verá las respuestas del dispositivo al método directo y la actualización de los dispositivos gemelos en la consola.

    ![Ejemplo de trabajo de IoT Hub 1: salida del dispositivo](./media/iot-hub-python-python-schedule-jobs/sample1-deviceoutput.png)

    ![Ejemplo de trabajo de IoT Hub 2: salida del dispositivo](./media/iot-hub-python-python-schedule-jobs/sample2-deviceoutput.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha utilizado un trabajo para programar un método directo para un dispositivo y la actualización de las propiedades del dispositivo gemelo.

Para continuar con la introducción de IoT Hub y los patrones de administración de dispositivos como remotos a través de la actualización de firmware de aire, vea [Realización de una actualización de firmware](tutorial-firmware-update.md).