---
title: Tutorial sobre cómo copiar datos de discos duros virtuales en discos administrados con Azure Data Box | Microsoft Docs
description: Obtenga información sobre cómo copiar datos desde discos duros virtuales desde cargas de trabajo de máquinas virtuales locales en una instancia de Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
ms.openlocfilehash: 3284821e0ec65a76b29d5195315136639304e411
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/30/2019
ms.locfileid: "64925474"
---
# <a name="tutorial-use-data-box-to-import-data-as-managed-disks-in-azure"></a>Tutorial: Uso de Data Box para importar datos como discos administrados en Azure

En este tutorial se describe cómo usar Azure Data Box para migrar los VHD locales a discos administrados en Azure. Los VHD de las máquinas virtuales locales se copian en Data Box como blobs en páginas y se cargan en Azure como discos administrados. Estos discos administrados se pueden adjuntar a las máquinas virtuales de Azure.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Revisar los requisitos previos
> * Conexión a un dispositivo Data Box
> * Copia de datos a un dispositivo Data Box


## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que:

1. Ha completado el [Tutorial: Instalación de un dispositivo Azure Data Box](data-box-deploy-set-up.md).
2. Ha recibido su dispositivo Data Box y el estado del pedido en el portal se actualiza a **Delivered** (Entregado).
3. Está conectado a una red de alta velocidad. Es muy recomendable tener una conexión de 10 GbE como mínimo. Si no hay disponible una conexión de 10 GbE, use un vínculo de datos de 1 GbE, pero las velocidades de copia serán afectadas.
4. Revisó lo siguiente:

    - [Tamaños de discos administrados compatibles en los límites de tamaño de objeto de Azure](data-box-limits.md#azure-object-size-limits).
    - [Introducción a los discos administrados de Azure](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box"></a>Conexión a un dispositivo Data Box

En función de los grupos de recursos especificados, Data Box crea un recurso compartido para cada grupo de recursos asociado. Por ejemplo, si `mydbmdrg1` y `mydbmdrg2` se crearon al realizar el pedido, se crean los recursos compartidos siguientes:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

En cada uno de los recursos compartidos, se crean las tres carpetas siguientes, que corresponden a contenedores de la cuenta de almacenamiento.

- SSD Premium
- HDD estándar
- SSD estándar

En la tabla siguiente se muestran las rutas de acceso UNC a los recursos compartidos en Data Box.
 
|        Protocolo de la conexión           |             Ruta de acceso UNC al recurso compartido                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

Según si se usa SMB o NFS para conectarse a los recursos compartidos de Data Box, los pasos para conectarse son distintos.

> [!NOTE]
> La conexión a través de REST no es compatible con esta característica.

### <a name="connect-to-data-box-via-smb"></a>Conexión a Data Box a través de SMB

Si usa un equipo host Windows Server, realice los pasos siguientes para conectarse a su dispositivo Data Box.

1. El primer paso es autenticarse e iniciar sesión. Vaya a **Connect and copy** (Conectar y copiar). Haga clic en **Get credentials** (Obtener credenciales) para obtener las credenciales de acceso para los recursos compartidos asociados con el grupo de recursos. También puede obtener las credenciales de acceso desde los **detalles del dispositivo** en Azure Portal.

    > [!NOTE]
    > Las credenciales de todos los recursos compartidos para los discos administrados son idénticas.

    ![Obtención de las credenciales del recurso compartido 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. En el cuadro de diálogo Access share and copy data (Acceder al recurso compartido y copiar datos), copie los valores **Username** (Nombre de usuario) y **Password** (Contraseña) del recurso compartido. Haga clic en **OK**.
    
    ![Obtención de las credenciales del recurso compartido 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Para acceder a los recursos compartidos asociados con el recurso (*mydbmdrg1* en el ejemplo siguiente) desde el equipo host, abra una ventana de comandos. En el símbolo del sistema, escriba:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    Las rutas de acceso del recurso compartido de UNC de este ejemplo son las siguientes:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Cuando se le solicite, escriba la contraseña del recurso compartido. En el ejemplo siguiente se muestra la conexión a un recurso compartido con el comando anterior.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Presione Windows + R. En la ventana **Ejecutar**, escriba `\\<device IP address>\<ShareName>`. Haga clic en **Aceptar** para abrir el Explorador de archivos.
    
    ![Conexión al recurso compartido mediante el Explorador de archivos 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    Ahora debería ver las siguientes carpetas creadas previamente dentro de cada recurso compartido.
    
    ![Conexión al recurso compartido mediante el Explorador de archivos 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-via-nfs"></a>Conexión a Data Box a través de NFS

Si usa un equipo host Linux, realice los pasos siguientes para configurar un dispositivo Data Box para que pueda acceder a los clientes NFS.

1. Proporcione las direcciones IP de los clientes autorizados que pueden acceder al recurso compartido. En la interfaz de usuario web local, vaya a la página **Connect and copy** (Conectar y copiar). En **NFS settings** (Configuración de NFS), haga clic en **NFS client access** (Acceso de cliente NFS).

    ![Configuración del acceso de cliente NFS 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Proporcione la dirección IP del cliente NFS y haga clic en **Add** (Agregar). Para configurar el acceso para varios clientes NFS, repita este paso. Haga clic en **OK**.

    ![Configuración del acceso de cliente NFS 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Asegúrese de que el equipo host de Linux tiene instalada una [versión admitida](data-box-system-requirements.md) del cliente NFS. Use la versión específica para su distribución de Linux.

3. Una vez instalado el cliente NFS, use el siguiente comando para montar el recurso compartido NFS en el dispositivo Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    En el ejemplo siguiente se muestra cómo conectarse mediante NFS a un recurso compartido de Data Box. La dirección IP del dispositivo Data Box es `169.254.250.200`; el recurso compartido `mydbmdrg1_MDisk` se monta en la máquina virtual ubuntuVM y el punto de montaje es `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box"></a>Copia de datos a un dispositivo Data Box

Una vez que se conecte al servidor de datos, el paso siguiente es copiar los datos. El archivo VHD se copia en la cuenta de almacenamiento provisional como blob en páginas. A continuación, el blob en páginas se convierte en disco administrado y se mueve a un grupo de recursos.

Antes de empezar a copiar los datos, revise las consideraciones siguientes:

- Copie siempre los VHD en una de las carpetas creadas previamente. Si copia los VHD fuera de estas carpetas o en una carpeta que usted mismo haya creado, se cargarán en la cuenta de Azure Storage como blobs en páginas y no como discos administrados.
- Solo se pueden cargar VHD fijos para crear discos administrados. No se admiten archivos VHDX ni VHD dinámicos o de diferenciación.
- Solo puede tener un disco administrado con un nombre determinado en un grupo de recursos a través de todas las carpetas creadas previamente. Esto implica que los VHD cargados en las carpetas creadas previamente deben tener nombres únicos. Asegúrese de que el nombre indicado no coincida con un disco administrado ya existente en un grupo de recursos.
- Revise los límites de los discos administrados en [Límites de tamaño de objeto de Azure](data-box-limits.md#azure-object-size-limits).

En función de si usa SMB o NFS para conectarse, puede usar:

- [Copia de datos mediante SMB](data-box-deploy-copy-data.md#copy-data-to-data-box)
- [Copia de datos mediante NFS](data-box-deploy-copy-data-via-nfs.md#copy-data-to-data-box)

Espere a que finalicen los trabajos de copia. Asegúrese de que los trabajos de copia se hayan completado sin errores antes de avanzar al paso siguiente.

![No hay errores en la página **Connect and copy**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Si hay errores durante el proceso de copia, descargue los registros de la página **Conectar y copiar**.

- Si copió un archivo que no tiene 512 bytes alineados, el archivo no se carga como blob en páginas en la cuenta de almacenamiento provisional. Verá un error en los registros. Quite el archivo y copie uno que tenga 512 bytes alineados.

- Si copió un archivo VHDX (estos archivos no son compatibles) con un nombre largo, verá un error en los registros.

    ![Errores en los registros de la página **Conectar y copiar**](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Resuelva los errores antes de continuar al paso siguiente.

Para garantizar la integridad de los datos, la suma de comprobación se calcula a medida que los datos se copian. Una vez completada la copia, compruebe el espacio utilizado y el espacio disponible en el dispositivo.
    
![Comprobación del espacio libre y utilizado en el panel](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Una vez finalizado el trabajo de copia, puede ir a **preparación para el envío**.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha obtenido información acerca de varios temas relacionados con Azure Data Box, como:

> [!div class="checklist"]
> * Revisar los requisitos previos
> * Conexión a un dispositivo Data Box
> * Copia de datos a un dispositivo Data Box


En el siguiente tutorial aprenderá a enviar su dispositivo Data Box a Microsoft.

> [!div class="nextstepaction"]
> [Envío de un dispositivo Data Box a Microsoft](./data-box-deploy-picked-up.md)

