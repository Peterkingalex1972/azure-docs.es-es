---
title: 'Creación de un contenedor de perfiles de FSLogix para un grupo host mediante Azure NetApp Files en Windows Virtual Desktop: Azure'
description: Procedimiento para crear un contenedor de perfiles de FSLogix mediante Azure NetApp Files en Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: helohr
ms.openlocfilehash: 05289b8e3aa0e083dd948a2d43f3af92418bc5d2
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051981"
---
# <a name="create-an-fslogix-profile-container-for-a-host-pool-using-azure-netapp-files"></a>Creación de un contenedor de perfiles de FSLogix para un grupo host mediante Azure NetApp Files

Se recomienda usar los contenedores de perfiles de FSLogix como solución para los perfiles de usuario para el [servicio de versión preliminar de Windows Virtual Desktop](overview.md). Los contenedores de perfiles de FSLogix almacenan un perfil de usuario completo en un único contenedor y están diseñados para usar un perfil itinerante en entornos informáticos remotos no persistentes, como Windows Virtual Desktop. Cuando inicia sesión, el contenedor se adjunta dinámicamente al entorno informático mediante un disco duro virtual (VHD) compatible de forma local y un disco duro virtual de Hyper-V (VHDX). Estas tecnologías avanzadas de controlador de filtro permiten que el perfil de usuario esté disponible inmediatamente y aparezca en el sistema exactamente igual que un perfil de usuario local. Para obtener más información sobre los contenedores de perfiles de FSLogix, consulte [Contenedores de perfiles de FSLogix y archivos de Azure](fslogix-containers-azure-files.md).

Puede crear contenedores de perfiles de FSLogix mediante [Azure NetApp Files](https://azure.microsoft.com/services/netapp/), un servicio de plataforma nativa de Azure fácil de usar que ayuda a los clientes a aprovisionar de forma rápida y confiable volúmenes SMB de nivel empresarial para sus entornos de Windows Virtual Desktop. Para obtener más información acerca de Azure NetApp Files, consulte [¿Qué es Azure NetApp Files?](../azure-netapp-files/azure-netapp-files-introduction.md)

En esta guía se le mostrará cómo configurar una cuenta de Azure NetApp Files y crear contenedores de perfiles de FSLogix en Windows Virtual Desktop.

En este artículo se supone que ya tiene [grupos host](create-host-pools-azure-marketplace.md) configurados y agrupados en uno o varios inquilinos en el entorno de Windows Virtual Desktop. Para obtener información sobre cómo configurar los inquilinos, consulte [Creación de un inquilino en Windows Virtual Desktop](tenant-setup-azure-active-directory.md) y la [entrada de blog en Tech Community](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

Las instrucciones de esta guía son específicas para los usuarios de Windows Virtual Desktop. Si busca instrucciones más generales sobre cómo configurar Azure NetApp Files y crear contenedores de perfiles de FSLogix fuera de Windows Virtual Desktop, consulte la guía de inicio rápido [Configuración de Azure NetApp Files y creación de un volumen de NFS](../azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes.md).

>[!NOTE]
>En este artículo no se tratan los procedimientos recomendados para proteger el acceso al recurso compartido de Azure NetApp Files.

## <a name="prerequisites"></a>Requisitos previos

Antes de crear un contenedor de perfiles de FSLogix para un grupo host, debe hacer lo siguiente:

- Configurar Windows Virtual Desktop.
- Aprovisionar un grupo host de Windows Virtual Desktop.
- [Habilitar la suscripción a Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register).

## <a name="set-up-your-azure-netapp-files-account"></a>Configuración de la cuenta a Azure NetApp Files

Para empezar, necesita configurar una cuenta a Azure NetApp Files.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com). Asegúrese de que su cuenta tiene permisos de colaborador o administrador.

2. Seleccione el **icono de Azure Cloud Shell** a la derecha de la barra de búsqueda para abrir Azure Cloud Shell.

3. Tras abrir Azure Cloud Shell, seleccione **PowerShell**.

4. Si esta es la primera vez que usa Azure Cloud Shell, cree una cuenta de almacenamiento en la misma suscripción en la que usa Azure NetApp Files y Windows Virtual Desktop.

   ![La ventana de la cuenta de almacenamiento con un botón para crear almacenamiento situado en la parte inferior de la ventana resaltado en rojo.](media/create-storage-button.png)

5. Una vez que cargue Azure Cloud Shell, ejecute los dos cmdlets siguientes.

   ```powershell
   az account set --subscription <subscriptionID>
   ```

   ```powershell
   az provider register --namespace Microsoft.NetApp --wait
   ```

6. En el lado izquierdo de la ventana, seleccione **Todos los servicios**. Escriba **Azure NetApp Files** en el cuadro de búsqueda que aparece en la parte superior del menú.

   ![Captura de pantalla de un usuario escribiendo "Azure NetApp Files" en el cuadro de búsqueda Todos los servicios. Los resultados de la búsqueda muestran el recurso Azure NetApp Files.](media/azure-netapp-files-search-box.png)


7. Seleccione **Azure NetApp Files** en los resultados de la búsqueda y, a continuación, seleccione **Crear**.

8. Seleccione el botón **Agregar**.
9. Cuando se abra la hoja **Nueva cuenta de NetApp**, escriba los valores siguientes:

    - En **Nombre**, escriba el nombre de la cuenta de NetApp.
    - En **Suscripción**, seleccione en el menú desplegable la suscripción de la cuenta de almacenamiento que configuró en el paso 4.
    - Para **Grupo de recursos**, seleccione un grupo de recursos existente en el menú desplegable o seleccione la opción **Crear nuevo** para crear uno.
    - En **Ubicación**, seleccione la región de su cuenta de NetApp en el menú desplegable. Esta región debe ser la misma que usan las máquinas virtuales de su host de sesión.

   >[!NOTE]
   >Actualmente, Azure NetApp Files no admite el montaje de un volumen entre regiones.

10. Cuando finalice, seleccione **Crear** para crear la cuenta de NetApp.

## <a name="create-a-capacity-pool"></a>Creación de un grupo de capacidad

A continuación, cree un nuevo grupo de capacidad: 

1. Vaya al menú de Azure NetApp Files y seleccione la nueva cuenta.
2. En el menú de la cuenta, seleccione **Grupos de capacidad** en Servicio de almacenamiento.
3. Seleccione **Agregar grupo**.
4. Cuando se abra la hoja **Nuevo grupo de capacidad**, escriba los valores siguientes:

    - En **Nombre**, escriba un nombre para el nuevo grupo de capacidad.
    - En **Nivel de servicio**, seleccione el valor deseado en el menú desplegable. Se recomienda **Premium** en la mayoría de los entornos.
       >[!NOTE]
       >La configuración Premium proporciona el rendimiento mínimo disponible para un nivel de servicio Premium, que es de 256 MBps. Es posible que necesite ajustar este rendimiento para un entorno de producción. El rendimiento final se basa en la relación descrita en [Límites de rendimiento](../azure-netapp-files/azure-netapp-files-service-levels.md).
    - En **Tamaño (TiB)** , escriba el tamaño del grupo de capacidad que mejor se adapte a sus necesidades. El tamaño mínimo es 4 TiB.

5. Cuando haya finalizado, seleccione **Aceptar**.

## <a name="join-an-active-directory-connection"></a>Unión a una conexión de Active Directory

Después, debe unirse a una conexión de Active Directory.

1. Seleccione **Conexiones de Active Directory** en el menú del lado izquierdo de la página y, a continuación, seleccione el botón **Unirse** para abrir la página **Unirse a Active Directory**.

   ![Captura de pantalla del menú Unirse a conexiones de Active Directory.](media/active-directory-connections-menu.png)

2. Escriba los siguientes valores en la página **Unirse a Active Directory** para unirse a una conexión:

    - En **DNS principal**, escriba la dirección IP del servidor DNS de su entorno que puede resolver el nombre de dominio.
    - En **Dominio**, escriba el nombre de dominio completo (FQDN).
    - En **SMB Server (Computer Account) Prefix** (Prefijo de servidor SMB [cuenta de equipo]), escriba la cadena que quiere anexar al nombre de la cuenta de equipo.
    - En **Nombre de usuario**, escriba el nombre de la cuenta con permisos para realizar la unión a un dominio.
    - En **Contraseña**, escriba la contraseña de la cuenta.

  >[!NOTE]
  >Se recomienda confirmar que la cuenta de equipo que creó en [Unión a una conexión de Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection) ha aparecido en el controlador de dominio en la sección **Equipos** o en la **unidad organizativa correspondiente de su empresa**.

## <a name="create-a-new-volume"></a>Creación de un nuevo volumen

A continuación, tendrá que crear un nuevo volumen.

1. Seleccione **Volúmenes** y luego **Agregar volumen**.

2. Cuando se abra la hoja **Crear un volumen**, escriba los valores siguientes:

    - En **Nombre del volumen**, escriba un nombre para el nuevo volumen.
    - En **Grupo de capacidad**, seleccione el grupo de capacidad que acaba de crear en el menú desplegable.
    - En **Cuota (GiB)** , escriba el tamaño de volumen adecuado para su entorno.
    - En **Red virtual**, seleccione una red virtual existente que tenga conectividad con el controlador de dominio en el menú desplegable.
    - En **Subred**, seleccione **Crear nueva**. Tenga en cuenta que esta subred se delegará a Azure NetApp Files.

3.  Seleccione **Siguiente: Protocolo \>\>** para abrir la pestaña Protocolo y configurar los parámetros de acceso al volumen.

## <a name="configure-volume-access-parameters"></a>Configuración de parámetros de acceso al volumen

Después de crear el volumen, configure los parámetros de acceso al volumen.

1.  Seleccione **SMB** como tipo de protocolo.
2.  En Configuración, en el menú desplegable **Active Directory**, seleccione el mismo directorio que conectó originalmente en [Unión a una conexión de Active Directory](create-fslogix-profile-container.md#join-an-active-directory-connection). Tenga en cuenta que existe un límite de una instancia de Active Directory por suscripción.
3.  En el cuadro de texto **Nombre del recurso compartido**, escriba el nombre del recurso compartido que usa el grupo host de sesión y sus usuarios.

4.  En la parte inferior de la página, seleccione **Revisar y crear**. Se abrirá la página validación. Una vez que el volumen se haya validado correctamente, seleccione **Crear**.

5.  En este momento, el nuevo volumen comenzará a implementarse. Una vez completada la implementación, puede usar el recurso compartido de Azure NetApp Files.

6.  Para ver la ruta de montaje, seleccione **Ir al recurso** y búsquelo en la pestaña Información general.

    ![Captura de pantalla de la Información general con una flecha roja que apunta a la ruta de montaje.](media/overview-mount-path.png)

## <a name="configure-fslogix-on-session-host-virtual-machines-vms"></a>Configuración de FSLogix en máquinas virtuales de host de sesión (VM)

Esta sección está basada en [Creación de un contenedor de perfiles para un grupo host mediante un recurso compartido de archivos](create-host-pools-user-profile.md).

1. [Descargue el archivo .ZIP del agente de FSLogix](https://go.microsoft.com/fwlink/?linkid=2084562&clcid=0x409) mientras sigue siendo remoto en la máquina virtual del host de sesión.

2. Descomprima el archivo descargado.

3. En el archivo, vaya a **x64** > **Releases** y ejecute **FSLogixAppsSetup.exe**. Se abrirá el menú de instalación.

4.  Si tiene una clave de producto, escríbala en el cuadro de texto Clave de producto.

5. Active la casilla situada junto a **Acepto los términos y las condiciones de la licencia**.

6. Seleccione **Instalar**.

7. Vaya a **C:\\Archivos de programa\\FSLogix\\Apps** para confirmar que el agente esté instalado.

8. Desde el menú Inicio, ejecute **RegEdit** como administrador.

9. Vaya a **Equipo\\HKEY_LOCAL_MACHINE\\software\\FSLogix**.

10. Cree una clave denominada **Profiles**.

11.  Cree un valor denominado **Enabled** con un tipo **REG_DWORD** establecido en **1**.

12. Cree un valor denominado **VHDLocations** con tipo **Cadena múltiple** y establezca su valor como el URI del recurso compartido de Azure NetApp Files.

## <a name="assign-users-to-session-host"></a>Asignación de usuarios al host de sesión

1. Abra **PowerShell ISE** como administrador e inicie sesión en Windows Virtual Desktop.

2. Ejecute los siguientes cmdlets:

   ```powershell
   Import-Module Microsoft.RdInfra.RdPowershell
   # (Optional) Install-Module Microsoft.RdInfra.RdPowershell
   $brokerurl = "https://rdbroker.wvd.microsoft.com"
   Add-RdsAccount -DeploymentUrl $brokerurl
   ```

3. Cuando se le pidan las credenciales, escriba las credenciales del usuario con el rol Creador de inquilinos o Colaborador de RDS/Propietario de RDS en el inquilino de Windows Virtual Desktop.

4. Ejecute los siguientes cmdlets para asignar un usuario a un grupo de Escritorio remoto:

   ```powershell
   $tenant = "<your-wvd-tenant>"
   $pool1 = "<wvd-pool>"
   $appgroup = "Desktop Application Group"
   $user1 = "<user-principal>"
   Add-RdsAppGroupUser $tenant $pool1 $appgroup $user1
   ```

## <a name="make-sure-users-can-access-the-azure-netapp-file-share"></a>Asegúrese de que los usuarios puedan tener acceso al recurso compartido de Azure NetApp Files.

1. Abra el explorador de Internet y vaya a <https://rdweb.wvd.microsoft.com/webclient/index.html>.

2. Inicie sesión con las credenciales de un usuario asignado al grupo de Escritorio remoto.

3. Una vez que haya establecido la sesión de usuario, inicie sesión en Azure Portal con una cuenta administrativa.

4. Abra **Azure NetApp Files**, seleccione su cuenta de Azure NetApp Files y, a continuación, seleccione **Volúmenes**. Cuando se abra el menú Volúmenes, seleccione el volumen correspondiente.

   ![Una captura de pantalla de la cuenta de NetApp que configuró anteriormente en Azure Portal con el botón Volúmenes seleccionado.](media/netapp-account.png)

5. Vaya a la pestaña **Información general** y confirme que el contenedor de perfiles de FSLogix está usando espacio.

6. Conéctese directamente a cualquiera de las máquinas virtuales parte del grupo de hosts mediante Escritorio remoto y abra el **Explorador de archivos**. A continuación, vaya a **Ruta de acceso de montaje** (en el ejemplo siguiente, la ruta de acceso de montaje es \\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

   Dentro de esta carpeta, debe haber un VHD de perfil como el del ejemplo siguiente.

   ![Captura de pantalla del contenido de la carpeta en la ruta de montaje. Dentro se encuentra un solo archivo VHD denominado "Profile_ssbb".](media/mount-path-folder.png)

## <a name="next-steps"></a>Pasos siguientes

Puede usar contenedores de perfiles de FSLogix para configurar un recurso compartido de perfil de usuario. Para obtener información sobre cómo crear recursos compartidos de perfil de usuario con sus nuevos contenedores, consulte [Creación de un contenedor de perfiles para un grupo host mediante un recurso compartido de archivos](create-host-pools-user-profile.md).