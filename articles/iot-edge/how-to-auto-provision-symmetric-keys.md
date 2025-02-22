---
title: 'Aprovisionamiento automático de dispositivos con DPS mediante la atestación de clave simétrica: Azure IoT Edge | Microsoft Docs'
description: Uso de atestación de clave simétrica para probar el aprovisionamiento automático de dispositivos para Azure IoT Edge con Device Provisioning Service
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: mrohera
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3c21c0bdce6f6a5cd3c8f634bf400600b30a8ead
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414596"
---
# <a name="create-and-provision-an-iot-edge-device-using-symmetric-key-attestation"></a>Creación y aprovisionamiento de un dispositivo IoT Edge mediante la atestación de clave simétrica

Los dispositivos Azure IoT Edge pueden aprovisionarse automáticamente con [Device Provisioning Service](../iot-dps/index.yml), igual que los dispositivos que no están habilitados para Edge. Si no está familiarizado con el proceso de aprovisionamiento automático, revise los [conceptos sobre aprovisionamiento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar.

En este artículo se muestra cómo crear una inscripción individual de Device Provisioning Service mediante la atestación de clave simétrica en un dispositivo IoT Edge con los pasos siguientes:

* Cree una instancia de IoT Hub Device Provisioning Service (DPS).
* Cree una inscripción individual para el dispositivo.
* Instale el entorno de ejecución de IoT Edge y conéctese a IoT Hub.

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo con una instancia del servicio Device Provisioning. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad. La atestación de dispositivos mediante un [TPM](../iot-dps/concepts-tpm-attestation.md) es más segura y se debe usar cuando los requisitos de seguridad son más estrictos.

## <a name="prerequisites"></a>Requisitos previos

* Una instancia de IoT Hub activa
* Un dispositivo físico o virtual

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configuración de IoT Hub Device Provisioning Service

Cree una nueva instancia de IoT Hub Device Provisioning Service en Azure y vincúlela a IoT Hub. Puede seguir las instrucciones de [Configuración de IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.

## <a name="choose-a-unique-registration-id-for-the-device"></a>Elección de un identificador de registro único para el dispositivo

Se debe definir un identificador de registro único para identificar cada dispositivo. Puede usar la dirección MAC, el número de serie o cualquier otra información única del dispositivo.

En este ejemplo se usa una combinación de una dirección MAC y un número de serie que forman la siguiente cadena de un identificador de registro.

```
sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6
```

Cree un identificador de registro único para el dispositivo. Solo se pueden usar caracteres alfanuméricos en minúsculas y guiones ("-").

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Use el identificador de registro del dispositivo para crear una inscripción individual en DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-monitor.md).

> [!TIP]
> Las inscripciones de grupo también son posibles cuando se usa la atestación de clave simétrica e implican las mismas decisiones que las inscripciones individuales.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. En **Mecanismo**, seleccione **Clave simétrica**.

   1. Active la casilla **Generar claves automáticamente**.

   1. Proporcione el **Id. de registro** que ha creado para el dispositivo.

   1. Si lo desea, proporcione un **Id. de dispositivo IoT Hub** para el dispositivo. Puede usar identificadores de dispositivo para dirigirse a un dispositivo individual para la implementación del módulo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

   1. Acepte el valor predeterminado de la directiva de asignación de Device Provisioning Service para **la forma de asignar dispositivos a los centros**, o bien elija otro valor que sea específico de esta inscripción.

   1. Elija la instancia de **IoT Hub** vinculada a la que quiere conectar el dispositivo. Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   1. Elija **cómo quiere que se controlen los datos del dispositivo durante el reaprovisionamiento** cuando los dispositivos soliciten el aprovisionamiento después de la primera vez.

   1. Agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Asegúrese de que **Habilitar entrada** está establecido en **Habilitar**.

   1. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación. Asegúrese de copiar el valor **Clave principal** de la inscripción para usarlo al crear la clave del dispositivo.

## <a name="derive-a-device-key"></a>Derivación de una clave de dispositivo

En el dispositivo se usa la clave de dispositivo derivada con el identificador de registro único para realizar la atestación de clave simétrica con la inscripción durante el aprovisionamiento. Para generar la clave del dispositivo, use la clave que ha copiado de la inscripción de DPS para calcular un valor [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) del identificador de registro único del dispositivo y convierta el resultado a formato Base64.

No incluya la clave principal o secundaria de la inscripción en el código del dispositivo.

### <a name="linux-workstations"></a>Estaciones de trabajo de Linux

Si utiliza una estación de trabajo de Linux, puede usar openssl para generar la clave de dispositivo derivada tal y como se muestra en el ejemplo siguiente.

Reemplace el valor de **KEY** por el de la **clave principal** que ha apuntado anteriormente.

Reemplace el valor de **REG_ID** por el identificador de registro del dispositivo.

```bash
KEY=8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw==
REG_ID=sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6

keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
```

```bash
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

### <a name="windows-based-workstations"></a>Estaciones de trabajo basadas en Windows

Si utiliza una estación de trabajo basada en Windows, puede usar PowerShell para generar las claves de dispositivo derivadas tal y como se muestra en el ejemplo siguiente.

Reemplace el valor de **KEY** por el de la **clave principal** que ha apuntado anteriormente.

Reemplace el valor de **REG_ID** por el identificador de registro del dispositivo.

```powershell
$KEY='8isrFI1sGsIlvvFSSFRiMfCNzv21fjbE/+ah/lSh3lF8e2YG1Te7w1KpZhJFFXJrqYKi9yegxkqIChbqOS9Egw=='
$REG_ID='sn-007-888-abc-mac-a1-b2-c3-d4-e5-f6'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Sus componentes se ejecutan en contenedores y permiten implementar contenedores adicionales en el dispositivo para que pueda ejecutar código en Edge.

Al aprovisionar el dispositivo necesitará la información siguiente:

* El valor **Ámbito de id.** del DPS
* El **Id. de registro** del dispositivo que ha creado
* La clave de dispositivo derivada del dispositivo para la atestación de clave simétrica

### <a name="linux-device"></a>Dispositivo Linux

Siga las instrucciones de la arquitectura del dispositivo. Asegúrese de configurar el entorno de ejecución de IoT Edge para el aprovisionamiento automático, no manual.

[Instalación del entorno de ejecución de Azure IoT Edge en Linux](how-to-install-iot-edge-linux.md)

La sección del archivo de configuración para el aprovisionamiento de clave simétrica tiene este aspecto:

```yaml
# DPS symmetric key provisioning configuration
provisioning:
   source: "dps"
   global_endpoint: "https://global.azure-devices-provisioning.net"
   scope_id: "{scope_id}"
   attestation:
      method: "symmetric_key"
      registration_id: "{registration_id}"
      symmetric_key: "{symmetric_key}"
```

Reemplace los valores de marcador de posición para `{scope_id}`, `{registration_id}` y `{symmetric_key}` con los datos que ha recopilado antes.

### <a name="windows-device"></a>Dispositivo Windows

Siga las instrucciones para instalar el entorno de ejecución de Azure IoT Edge en el dispositivo para el que ha generado una clave de dispositivo derivada. Asegúrese de configurar el entorno de ejecución de IoT Edge para el aprovisionamiento automático, no manual.

[Instalación y aprovisionamiento automático de IoT Edge en Windows](how-to-install-iot-edge-windows.md#option-2-install-and-automatically-provision)

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo. Use los siguientes comandos en el dispositivo para comprobar que el entorno de ejecución está instalado e iniciado correctamente.

### <a name="linux-device"></a>Dispositivo Linux

Compruebe el estado del servicio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine los registros del servicio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

### <a name="windows-device"></a>Dispositivo Windows

Compruebe el estado del servicio IoT Edge.

```powershell
Get-Service iotedge
```

Examine los registros del servicio.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

Enumere los módulos en ejecución.

```powershell
iotedge list
```

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer la id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-monitor.md) o la [CLI de Azure](how-to-deploy-monitor-cli.md).
