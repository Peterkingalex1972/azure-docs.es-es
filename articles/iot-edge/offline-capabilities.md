---
title: 'Funcionamiento de dispositivos sin conexión: Azure IoT Edge | Microsoft Docs'
description: Descubra cómo los módulos y los dispositivos de IoT Edge pueden funcionar sin conexión a Internet durante largos períodos de tiempo y cómo IoT Edge puede permitir que dispositivos de IoT comunes funcionen también sin conexión.
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6d82b353f8b485b4441853b7ff8e70e7d69f4d6a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986981"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>Uso de funcionalidades sin conexión ampliadas en dispositivos, módulos y dispositivos secundarios IoT Edge

Azure IoT Edge permite realizar operaciones sin conexión ampliadas en dispositivos IoT Edge y también operaciones sin conexión en dispositivos secundarios que no son IoT Edge. Si un dispositivo de IoT Edge ha podido conectarse a IoT Hub aunque sea una sola vez, ese dispositivo y cualquier dispositivo secundario podrá seguir funcionando con una conexión a Internet intermitente o sin conexión. 


## <a name="how-it-works"></a>Cómo funciona

Cuando un dispositivo de IoT Edge está en modo sin conexión, el centro de IoT Edge asume tres roles. En primer lugar, almacena los mensajes que deberían enviarse y los guarda hasta que el dispositivo vuelva a tener conexión. En segundo lugar, actúa en nombre de IoT Hub para autenticar los módulos y los dispositivos secundarios, de forma que puedan seguir trabajando. En tercer lugar, permite la comunicación entre los dispositivos secundarios, que normalmente se realizaría mediante IoT Hub. 

En el ejemplo siguiente, se muestra cómo funciona un escenario de IoT Edge en modo sin conexión:

1. **Configuración de dispositivos**

   De forma automática, los dispositivos de IoT Edge tienen habilitadas funcionalidades sin conexión. Para ampliar esa funcionalidad a otros dispositivos de IoT, debe declarar una relación de tipo primario/secundario entre los dispositivos de IoT Hub. A continuación, se configuran los dispositivos secundarios para que confíen en su dispositivo principal asignado y se enrutan las comunicaciones de dispositivo a nube mediante el principal como puerta de enlace. 

2. **Sincronización con IoT Hub**

   Es necesario que, al menos una vez después de instalar el entorno de ejecución de IoT Edge, el dispositivo de IoT Edge tenga conexión para sincronizarse con IoT Hub. En esta sincronización, el dispositivo IoT Edge obtiene información detallada sobre los dispositivos secundarios asignados a él. El dispositivo IoT Edge también actualiza de forma segura su caché local para permitir las operaciones sin conexión y recupera la configuración del almacenamiento local de mensajes de telemetría. 

3. **Desconexión**

   Mientras el dispositivo de IoT Edge está desconectado de IoT Hub, los módulos implementados y los dispositivos secundarios IoT pueden operar indefinidamente. Mientras están sin conexión, los módulos y los dispositivos secundarios pueden iniciarse y reiniciarse autenticándose con el centro de IoT Edge. Los datos de telemetría que deben enviarse a IoT Hub se almacenan localmente. La comunicación entre los módulos o los dispositivos IoT secundarios se realiza mediante mensajes o métodos directos. 

4. **Nueva conexión y nueva sincronización con IoT Hub**

   Una vez que se restaura la conexión con IoT Hub, el dispositivo de IoT Edge vuelve a sincronizarse. Los mensajes almacenados localmente se entregan en el mismo orden en el que se almacenaron. Las diferencias entre las propiedades deseadas y notificadas de los módulos y los dispositivos se concilian. El dispositivo de IoT Edge actualiza los cambios en el grupo de dispositivos IoT secundarios que tiene asignados.

## <a name="restrictions-and-limits"></a>Restricciones y límites

Las funcionalidades sin conexión ampliadas que se describen en este artículo están disponibles en [IoT Edge 1.0.7 o versiones posteriores](https://github.com/Azure/azure-iotedge/releases). Las versiones anteriores tienen un subconjunto de características sin conexión. Los dispositivos de IoT Edge existentes que no tienen funcionalidades sin conexión ampliadas no se pueden actualizar cambiando la versión del entorno de ejecución, sino que deben volver a configurarse con una nueva identidad de dispositivo de IoT Edge para obtener estas características. 

La compatibilidad con las funcionalidades sin conexión ampliadas está disponible en todas las regiones en las que lo está IoT Hub, **excepto** en Este de EE. UU.

Solo los dispositivos que no son IoT Edge pueden agregarse como dispositivos secundarios. 

Los dispositivos de IoT Edge y sus dispositivos secundarios asignados pueden funcionar indefinidamente sin conexión tras realizar una vez la sincronización inicial. Sin embargo, el almacenamiento de mensajes depende de la configuración del período de vida (TTL) y del espacio en disco disponible para almacenar los mensajes. 

## <a name="set-up-parent-and-child-devices"></a>Configuración de dispositivos principales y secundarios

Para que un dispositivo IoT Edge amplíe sus funcionalidades sin conexión ampliadas a los dispositivos IoT secundarios, es necesario realizar dos pasos. En primer lugar, declare las relaciones principal-secundario en Azure Portal. En segundo lugar, cree una relación de confianza entre el dispositivo principal y todos los dispositivos secundarios y, luego, configure las comunicaciones de dispositivo a nube para pasar por el principal como una puerta de enlace. 

### <a name="assign-child-devices"></a>Asignación de dispositivos secundarios

Los dispositivos secundarios pueden ser cualquier dispositivo que no sea IoT Edge que esté registrado en la misma instancia de IoT Hub. Los dispositivos principales pueden tener varios dispositivos secundarios, pero un dispositivo secundario solo puede tener un dispositivo principal. Existen tres opciones para configurar dispositivos secundarios en un dispositivo perimetral: mediante Azure Portal, con la CLI de Azure o por medio del SDK de servicio de IoT Hub. 

En las secciones siguientes se proporcionan ejemplos de cómo puede declarar la relación principal-secundario en IoT Hub para dispositivos IoT existentes. Si va a crear identidades de dispositivo para los dispositivos secundarios, consulte [Autenticación de un dispositivo de bajada en Azure IoT Hub](how-to-authenticate-downstream-device.md) para más información.

#### <a name="option-1-iot-hub-portal"></a>Opción 1: Portal de IoT Hub

Puede declarar la relación principal-secundario cuando se crea un dispositivo. O bien, en el caso de dispositivos existentes, puede declarar la relación en la página de detalles de cualquier dispositivo IoT Edge principal o secundario. 

   ![Administración de dispositivos secundarios desde la página de detalles del dispositivo de IoT Edge](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>Opción 2: Uso de la herramienta de línea de comandos `az`

Mediante la [interfaz de línea de comandos de Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) con la [extensión de IoT](https://github.com/azure/azure-iot-cli-extension) (v0.7.0 o posterior), puede administrar relaciones principal-secundario con los subcomandos [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest). En el ejemplo siguiente se usa una consulta para asignar todos los dispositivos que no son IoT Edge del centro como dispositivos secundarios de un dispositivo IoT Edge. 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

Puede modificar la [consulta](../iot-hub/iot-hub-devguide-query-language.md) para seleccionar un subconjunto diferente de dispositivos. El comando puede tardar varios segundos si especifica un conjunto grande de dispositivos.

#### <a name="option-3-use-iot-hub-service-sdk"></a>Opción 3: Uso del SDK de servicio de IoT Hub 

Por último, puede administrar las relaciones principal-secundario mediante programación con el SDK de servicio de IoT Hub para C#, Java o Node.js. Este es un [ejemplo de asignación de un dispositivo secundario](https://aka.ms/set-child-iot-device-c-sharp) mediante el SDK de C#.

### <a name="set-up-the-parent-device-as-a-gateway"></a>Configuración del dispositivo principal como puerta de enlace

Se puede considerar una relación principal-secundario como una puerta de enlace transparente, donde el dispositivo secundario tiene su propia identidad en IoT Hub, pero se comunica a través de la nube por medio de su dispositivo principal. Para una comunicación segura, el dispositivo secundario debe ser capaz de comprobar que el dispositivo principal proviene de un origen de confianza. En caso contrario, terceros podrían configurar dispositivos malintencionados para suplantar a los principales e interceptar las comunicaciones. 

En los siguientes artículos se describe de forma detallada una manera de crear esta relación de confianza: 
* [Configuración de un dispositivo IoT Edge para que actúe como puerta de enlace transparente](how-to-create-transparent-gateway.md)
* [Conexión de un dispositivo de bajada (secundario) a una puerta de enlace de Azure IoT Edge](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>Especificación de los servidores DNS 

Para mejorar la solidez, se recomienda especificar las direcciones del servidor DNS usadas en su entorno. Para [configurar el servidor DNS en el artículo de solución de problemas](troubleshoot.md#resolution-7), verá dos opciones.

## <a name="optional-offline-settings"></a>Ajustes opcionales del modo sin conexión

Si los dispositivos se desconectan, el dispositivo principal IoT Edge almacena todos los mensajes de dispositivo a nube hasta que se restablece la conexión. El módulo del centro de IoT Edge administra el almacenamiento y el reenvío de los mensajes sin conexión. En el caso de dispositivos que pueden desconectarse durante largos períodos, configure dos valores del centro de IoT Edge para optimizar el rendimiento. 

En primer lugar, aumente el valor del período de vida para que el centro de IoT Edge mantenga los mensajes el tiempo suficiente para que el dispositivo se vuelva a conectar. A continuación, agregue espacio en disco adicional para el almacenamiento de mensajes. 

### <a name="time-to-live"></a>Período de vida

La configuración del período de vida es la cantidad de tiempo (en segundos) que puede esperar un mensaje para entregarse antes de que expire. El valor predeterminado es 7200 segundos (dos horas). El valor máximo solo está limitado por el valor máximo de una variable de entero, que equivale aproximadamente a dos mil millones. 

Esta configuración es una propiedad deseada del centro de IoT Edge, que se almacena en el módulo gemelo. Puede configurarla en Azure Portal o directamente en el manifiesto de implementación. 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="host-storage-for-system-modules"></a>Almacenamiento de host para módulos del sistema

Los mensajes y la información de estado de los módulos se almacenan de manera predeterminada en el sistema de archivos de contenedor local del centro de IoT Edge. Para mejorar la confiabilidad, especialmente cuando se trabaja sin conexión, también puede dedicar almacenamiento en el dispositivo host de IoT Edge.

Para configurar el almacenamiento en el sistema host, cree variables de entorno para el centro de IoT Edge y el agente de IoT Edge que apunten a una carpeta de almacenamiento en el contenedor. Después, tendrá que utilizar las opciones de creación para enlazar esa carpeta de almacenamiento con una carpeta del equipo host. 

Puede configurar las variables de entorno y las opciones de creación del módulo del centro de IoT Edge en Azure Portal, en la sección **Configurar las opciones avanzadas del entorno en tiempo de ejecución de Edge**. 

1. Tanto para el centro de IoT Edge como para el agente de IOT Edge, agregue una variable de entorno denominada **storageFolder** que apunte a un directorio en el módulo.
1. Tanto para el centro de IoT Edge como para el agente de IoT Edge, agregue enlaces para conectar un directorio local de la máquina host a un directorio del módulo. Por ejemplo: 

   ![Agregar opciones de creación y variables de entorno para el almacenamiento local](./media/offline-capabilities/offline-storage.png)

También puede configurar el almacenamiento local directamente en el manifiesto de implementación. Por ejemplo: 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Reemplace `<HostStoragePath>` y `<ModuleStoragePath>` por las rutas de almacenamiento de su host y de su módulo; ambos valores deben ser una ruta de acceso absoluta. 

Por ejemplo, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que el directorio **/etc/iotedge/storage** en el sistema host se asigna al directorio **/iotedge/storage/** en el contenedor. O en otro ejemplo para sistemas Windows, `"Binds":["C:\\temp:C:\\contemp"]` significa que el directorio **C:\\temp** en el sistema host se asigna al directorio **C:\\contemp** en el contenedor. 

En los dispositivos Linux, asegúrese de que el perfil de usuario del centro de IoT Edge, UID 1000, tenga permisos de lectura, escritura y ejecución en el directorio del sistema host. Estos permisos son necesarios para que el centro de IoT Edge pueda almacenar mensajes en el directorio y recuperarlos más adelante. (El agente de IoT Edge funciona como raíz, por lo que no necesita permisos adicionales). Hay varias maneras de administrar los permisos de directorio en los sistemas Linux; entre otros, usar `chown` para cambiar el propietario del directorio y, luego, `chmod` para cambiar los permisos. Por ejemplo:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Puede encontrar más detalles sobre las opciones de creación en la [documentación de Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo configurar una puerta de enlace transparente para las conexiones de dispositivos principales y secundarios: 

* [Configuración de un dispositivo IoT Edge para que actúe como puerta de enlace transparente](how-to-create-transparent-gateway.md)
* [Autenticación de un dispositivo de bajada en Azure IoT Hub](how-to-authenticate-downstream-device.md)
* [Conexión de un dispositivo de bajada a una puerta de enlace Azure IoT Edge](how-to-connect-downstream-device.md)
