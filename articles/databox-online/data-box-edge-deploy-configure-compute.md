---
title: Tutorial sobre cómo filtrar y analizar datos con el proceso en Azure Data Box Edge | Microsoft Docs
description: Aprenda a configurar el rol de proceso en Data Box Edge y a usarlo para transformar los datos antes de enviarlos a Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Data Box Edge so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 7b3d725eb05c811d3fdd44516c1bde9a8dfbaaac
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64924323"
---
# <a name="tutorial-transform-data-with-azure-data-box-edge"></a>Tutorial: Transformación de datos con Azure Data Box Edge

Este tutorial describe cómo configurar el rol de proceso en un dispositivo de Azure Data Box Edge. Una vez configurado el rol de proceso, Azure Data Box Edge puede transformar los datos antes de enviarlos a Azure.

Este procedimiento tarda aproximadamente entre 10 y 15 minutos en completarse.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar el proceso
> * Agregar recursos compartidos
> * Agregar un módulo de proceso
> * Comprobar la transformación y la transferencia de los datos

 
## <a name="prerequisites"></a>Requisitos previos

Antes de configurar un rol de proceso en un dispositivo de Data Box Edge, asegúrese de que:

- Ha activado el dispositivo físico de Data Box Edge tal como se describe en [Conexión, configuración y activación de Azure Data Box Edge](data-box-edge-deploy-connect-setup-activate.md).


## <a name="configure-compute"></a>Configurar el proceso

Para configurar el proceso en Data Box Edge, creará un recurso de IoT Hub.

1. En la instancia de Azure Portal del recurso de Data Box Edge, vaya a Información general. En el panel derecho, en el icono **Proceso**, seleccione **Comenzar**.

    ![Introducción al proceso](./media/data-box-edge-deploy-configure-compute/configure-compute-1.png)

2. En el icono **Configurar el proceso de Edge**, seleccione **Configurar proceso**.
3. En la hoja **Configurar el proceso de Edge**, escriba lo siguiente:

   
    |Campo  |Valor  |
    |---------|---------|
    |IoT Hub     | Elija entre **Nuevo** o **Existente**. <br> De forma predeterminada, se usa un nivel estándar (S1) para crear un recurso de IoT. Para usar un recurso de IoT de nivel gratuito, cree uno y, a continuación, seleccione el recurso existente. <br> En cualquier caso, el recurso de IoT Hub usa la misma suscripción y el mismo grupo de recursos que utiliza el recurso de Data Box Edge.     |
    |NOMBRE     |Escriba un nombre para el recurso de IoT Hub.         |

    ![Introducción al proceso](./media/data-box-edge-deploy-configure-compute/configure-compute-2.png)

4. Seleccione **Crear**. La creación del recurso de IoT Hub tarda unos minutos. Después de crear el recurso de IoT Hub, el icono **Configurar proceso** se actualiza para mostrar la configuración de proceso. Para confirmar que se ha configurado el rol de proceso perimetral, seleccione **Ver proceso** en el icono **Configurar proceso**.
    
    ![Introducción al proceso](./media/data-box-edge-deploy-configure-compute/configure-compute-3.png)

    Cuando el rol de proceso de Edge está configurado en el dispositivo de Edge, este crea dos dispositivos: uno IoT y el otro IoT Edge. Ambos se pueden ver en el recurso de IoT Hub. Un entorno de ejecución de IoT Edge también se ejecuta en este dispositivo de IoT Edge. En este momento, solo está disponible la plataforma Linux para el dispositivo IoT Edge.


## <a name="add-shares"></a>Agregar recursos compartidos

En este tutorial, se realizará una implementación sencilla con dos recursos compartidos: un recurso compartido perimetral y otro recurso compartido local perimetral.

1. Para agregar un recurso compartido perimetral al dispositivo, siga estos pasos:

    1. En el recurso de Data Box Edge, vaya a **Proceso perimetral > Comenzar**.
    2. En el icono **Add share(s)** (Agregar recursos compartidos), seleccione **Agregar**.
    3. En la hoja **Agregar recurso compartido**, proporcione el nombre del recurso compartido y seleccione el tipo de recurso compartido.
    4. Para montar el recurso compartido perimetral, active la casilla **Usar el recurso compartido con el proceso perimetral**.
    5. Seleccione la **cuenta de almacenamiento**, el **servicio de almacenamiento**, un usuario existente y, luego, **Crear**.

        ![Agregar un recurso compartido perimetral](./media/data-box-edge-deploy-configure-compute/add-edge-share-1.png) 

    Si ha creado un recurso compartido NFS local, use la siguiente opción de comando de sincronización remota (rsync) para copiar archivos en él:

    `rsync <source file path> < destination file path>`

    Para más información sobre el comando rsync, consulte la [documentación sobre rsync](https://www.computerhope.com/unix/rsync.htm).

    Se crea el recurso compartido perimetral y recibirá una notificación para indicar que la creación se realizó correctamente. Puede que la lista de recursos compartidos se actualice, pero debe esperar a que la creación del recurso compartido se complete.

2. Para agregar un recurso compartido local perimetral al dispositivo perimetral, repita todos los pasos anteriores y active la casilla **Configurar como recurso compartido local de Edge**. Los datos en el recurso compartido local permanecen en el dispositivo.

    ![Agregar un recurso compartido local perimetral](./media/data-box-edge-deploy-configure-compute/add-edge-share-2.png)

  
3. Seleccione **Add share(s)** (Agregar recursos compartidos) para ver la lista actualizada de recursos compartidos.

    ![Lista de recursos compartidos actualizada](./media/data-box-edge-deploy-configure-compute/add-edge-share-3.png) 
 

## <a name="add-a-module"></a>Agregar un módulo

Puede agregar un módulo personalizado o uno creado previamente. No hay módulos personalizados en este dispositivo perimetral. Para información sobre cómo crear un módulo personalizado, vaya a [Desarrollo de un módulo C# para un dispositivo de Data Box Edge](data-box-edge-create-iot-edge-module.md).

En esta sección, agregará un módulo personalizado al dispositivo IoT Edge que creó en [Desarrollo de un módulo C# para Data Box Edge](data-box-edge-create-iot-edge-module.md). Este módulo personalizado toma los archivos de un recurso compartido local perimetral del dispositivo perimetral y los mueve a un recurso compartido perimetral (nube) del dispositivo. A continuación, el recurso compartido en la nube inserta los archivos en la cuenta de Azure Storage asociada con este.

1. Vaya a **Proceso perimetral > Comenzar**. En el icono **Agregar módulos**, seleccione el tipo de escenario como **simple**. Seleccione **Agregar**.
2. En la hoja **Configurar y agregar módulo**, escriba los valores siguientes:

    
    |Campo  |Valor  |
    |---------|---------|
    |NOMBRE     | Un nombre único para el módulo. Este módulo es un contenedor de Docker que puede implementar en un dispositivo de IoT Edge asociado con Data Box Edge.        |
    |URI de imagen     | El URI de la imagen de contenedor correspondiente del módulo.        |
    |Credenciales necesarias     | Si está activada, se usa el nombre de usuario y la contraseña para recuperar los módulos con una dirección URL coincidente.        |
    |Recurso compartido de entrada     | Seleccione un recurso compartido de entrada. En este caso, el recurso compartido local perimetral es el recurso compartido de entrada. El módulo que se usa aquí mueve los archivos desde el recurso compartido local perimetral hasta un recurso compartido perimetral donde se cargan en la nube.        |
    |Recurso compartido de salida     | Seleccione un recurso compartido de salida. En este caso, el recurso compartido perimetral es el recurso compartido de salida.        |
    |Tipo de desencadenador     | Seleccione **Archivo** o **Programación**. Un desencadenador de archivo se activa cada vez que se produce un evento de archivo, por ejemplo, cuando se escribe un archivo en el recurso compartido de entrada. Un desencadenador programado se activa según una programación definida por el usuario.         |
    |Nombre del desencadenador     | Un nombre exclusivo para el concentrador.         |
    |Variables de entorno| Información opcional que le ayudará a definir el entorno en que se ejecutará su módulo.   |

    ![Agregar y configurar el módulo](./media/data-box-edge-deploy-configure-compute/add-module-1.png)

3. Seleccione **Agregar**. Se agrega el módulo. El icono **Agregar módulo** se actualiza para indicar que el módulo se ha implementado. 

    ![Módulo implementado](./media/data-box-edge-deploy-configure-compute/add-module-2.png)

### <a name="verify-data-transform-and-transfer"></a>Comprobar la transformación y la transferencia de los datos

El último paso es asegurarse de que el módulo está conectado y funciona según lo previsto. El estado del entorno de ejecución del módulo debe ser "en ejecución" para el dispositivo IoT Edge del recurso de IoT Hub.

Para comprobar que el módulo se está ejecutando, haga lo siguiente:

1. Seleccione el icono **Agregar módulo**. Esta acción le lleva a la hoja **Módulos**. En la lista de módulos, identifique el módulo que ha implementado. El estado del entorno de ejecución del módulo que se agrega es *En ejecución*.

    ![Comprobar transformación de datos](./media/data-box-edge-deploy-configure-compute/verify-data-1.png)
 
1.  En el Explorador de archivos, conéctese a los recursos compartidos local perimetral y perimetral que creó anteriormente.

    ![Comprobar transformación de datos](./media/data-box-edge-deploy-configure-compute/verify-data-2.png) 
 
1.  Agregue datos al recurso compartido local.

    ![Comprobar transformación de datos](./media/data-box-edge-deploy-configure-compute/verify-data-3.png) 
 
    Los datos se mueven al recurso compartido en la nube.

    ![Comprobar transformación de datos](./media/data-box-edge-deploy-configure-compute/verify-data-4.png)  

    A continuación, los datos se trasladan del recurso compartido en la nube a la cuenta de almacenamiento. Para ver los datos vaya al Explorador de Storage.

    ![Comprobar transformación de datos](./media/data-box-edge-deploy-configure-compute/verify-data-5.png) 
 
Ha completado el proceso de validación.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configurar el proceso
> * Agregar recursos compartidos
> * Agregar un módulo de proceso
> * Comprobar la transformación y la transferencia de los datos

Para aprender a administrar el dispositivo de Data Box Edge, consulte:

> [!div class="nextstepaction"]
> [Uso de la interfaz de usuario web local para administrar una instancia de Data Box Edge](data-box-edge-manage-access-power-connectivity-mode.md)
