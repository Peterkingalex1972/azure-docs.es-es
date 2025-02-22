---
title: 'Azure Active Directory Domain Services: Unión de una VM de Ubuntu a un dominio administrado | Microsoft Docs'
description: Unión de una máquina virtual Linux Ubuntu a Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c782629d422eb8846b209fed7ab6b5a5c015de25
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612284"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Unión de una máquina virtual Ubuntu de Azure a un dominio administrado
Este artículo muestra cómo unir una máquina virtual Linux Ubuntu a un dominio administrado de Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:  
1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](tutorial-create-instance.md).
4. Asegúrese de que ha configurado las direcciones IP del dominio administrado como servidores DNS de la red virtual. Para más información, consulte [cómo actualizar la configuración de DNS para la red virtual de Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Complete los pasos necesarios para [sincronizar contraseñas para el dominio administrado de Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Aprovisionamiento de una máquina virtual Linux Ubuntu
Aprovisione una máquina virtual Linux Ubuntu en Azure mediante cualquiera de los métodos siguientes:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI de Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implemente la máquina virtual en la **misma red virtual en la que ha habilitado Azure AD Domain Services**.
> * Elija una **subred diferente** de aquella en la que ha habilitado Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Conexión remota a la máquina virtual Linux Ubuntu
Se ha aprovisionado la máquina virtual Ubuntu en Azure. La siguiente tarea consiste en conectar de forma remota a la máquina virtual mediante la cuenta de administrador local creada al aprovisionar la máquina virtual.

Siga las instrucciones que aparecen en el artículo sobre [cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configuración del archivo hosts en la máquina virtual Linux
En el terminal SSH, edite el archivo /etc/hosts y actualice la dirección IP y el nombre de host de la máquina.

```console
sudo vi /etc/hosts
```

En el archivo hosts, escriba el siguiente valor:

```console
127.0.0.1 contoso-ubuntu.contoso.com contoso-ubuntu
```

En este caso, "contoso.com" es el nombre de dominio DNS del dominio administrado. "contoso-ubuntu" es el nombre de host de la máquina virtual Ubuntu que va a unir al dominio administrado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalación de los paquetes necesarios en la máquina virtual de Linux
A continuación, instale los paquetes necesarios para unirse a un dominio en la máquina virtual. Lleve a cabo los siguiente pasos:

1.  En el terminal SSH, escriba el siguiente comando para descargar las listas de paquetes de los repositorios. Este comando actualiza las listas de paquetes para obtener información sobre las versiones más recientes de los paquetes y sus dependencias.

    ```console
    sudo apt-get update
    ```

2. Escriba el siguiente comando para instalar los paquetes necesarios.

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Durante la instalación de Kerberos, verá una pantalla de color rosa. La instalación del paquete "krb5-user" pide el nombre de dominio Kerberos (con todas las letras mayúsculas). La instalación escribe las secciones [realm] y [domain_realm] en /etc/krb5.conf.

    > [!TIP]
    > Si el nombre del dominio administrado es contoso.com, escriba contoso.COM como dominio kerberos. Recuerde que el nombre del dominio kerberos debe especificarse en mayúsculas.


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Configuración de las opciones de NTP (Protocolo de hora de red) en la máquina virtual Linux
La fecha y hora de la máquina virtual Ubuntu deben sincronizar con el dominio administrado. Agregue el nombre de host NTP del dominio administrado en el archivo /etc/ntp.conf.

```console
sudo vi /etc/ntp.conf
```

En el archivo ntp.conf, escriba el siguiente valor y guarde el archivo:

```console
server contoso.com
```

En este caso, "contoso.com" es el nombre de dominio DNS del dominio administrado.

Sincronice ahora la fecha y hora de la máquina virtual Ubuntu con el servidor NTP y, a continuación, inicie el servicio NTP:

```console
sudo systemctl stop ntp
sudo ntpdate contoso.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Unión de la máquina virtual de Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

1. Detecte el dominio administrado con Servicios de dominio de AAD. En el terminal SSH, escriba el siguiente comando:

    ```console
    sudo realm discover contoso.COM
    ```

   > [!NOTE]
   > **Solución de problemas:** si la *detección de dominio kerberos* no puede encontrar el dominio administrado:
   >   * Asegúrese de que el dominio sea accesible desde la máquina virtual (pruebe con ping).
   >   * Compruebe que la máquina virtual se haya implementado realmente en la misma red virtual en la que el dominio administrado está disponible.
   >   * Compruebe si ha actualizado la configuración del servidor DNS para que la red virtual apunte a los controladores de dominio del dominio administrado.

2. Inicialice Kerberos. En el terminal SSH, escriba el siguiente comando:

    > [!TIP]
    > * Asegúrese de especificar un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD". Si es necesario, [agregue una cuenta de usuario a un grupo en Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).
    > * Especifique el nombre de dominio en mayúsculas o kinit generará un error.
    >

    ```console
    kinit bob@contoso.COM
    ```

3. Una la máquina al dominio. En el terminal SSH, escriba el siguiente comando:

    > [!TIP]
    > Utilice la misma cuenta de usuario que ha especificado el paso anterior ("kinit").
    >
    > Si la máquina virtual no puede unirse al dominio, asegúrese de que el grupo de seguridad de red de la máquina virtual permita el tráfico Kerberos saliente en el puerto TCP + UDP 464 a la subred de la red virtual para el dominio administrado de Azure AD DS.

    ```console
    sudo realm join --verbose contoso.COM -U 'bob@contoso.COM' --install=/
    ```

Debe obtener un mensaje (Máquina inscrita correctamente en el dominio kerberos) cuando la máquina está unida correctamente al dominio administrado.


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>Actualización de la configuración de SSSD y reinicio del servicio
1. En el terminal SSH, escriba el siguiente comando. Abra el archivo sssd.conf y realice el siguiente cambio
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. Convierta en comentario la línea **use_fully_qualified_names = True** y guarde el archivo.
    
    ```console
    # use_fully_qualified_names = True
    ```

3. Reinicie el servicio SSSD.
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>Configuración de la creación automática del directorio principal
Para habilitar la creación automática del directorio principal después de iniciar sesión los usuarios, escriba los siguientes comandos en el terminal PuTTY:

```console
sudo vi /etc/pam.d/common-session
```

Agregue la siguiente línea en este archivo debajo de la línea "session optional pam_sss.so" y guárdelo:

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>Verificación de la unión a un dominio
Verifique si la máquina se ha unido correctamente al dominio administrado. Conéctese a la máquina virtual Ubuntu unida al dominio con otra conexión SSH. Utilice una cuenta de usuario del dominio y, a continuación, compruebe si la cuenta de usuario se ha resuelto correctamente.

1. En el terminal SSH, escriba el comando siguiente para conectarse a la máquina virtual Ubuntu unida al dominio con SSH. Use una cuenta de dominio que pertenezca al dominio administrado (por ejemplo, "bob@contoso.COM" en este caso).
    
    ```console
    ssh -l bob@contoso.COM contoso-ubuntu.contoso.com
    ```

2. En el terminal SSH, escriba el comando siguiente para ver si el directorio principal se ha inicializado correctamente.
    
    ```console
    pwd
    ```

3. En el terminal SSH, escriba el comando siguiente para ver si los miembros del grupo se están resolviendo correctamente.
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>Conceda privilegios de sudo al grupo "Administradores de controlador de dominio de AAD"
Puede conceder privilegios administrativos a los miembros del grupo "Administradores de controlador de dominio de AAD" en la máquina virtual Ubuntu. El archivo sudo se encuentra en /etc/sudoers. Los miembros de los grupos de AD que agregó en sudoers pueden realizar sudo.

1. En el terminal SSH, asegúrese de que ha iniciado sesión con privilegios de superusuario. Puede usar la cuenta de administrador local que especificó al crear la máquina virtual. Ejecute el comando siguiente:
    
    ```console
    sudo vi /etc/sudoers
    ```

2. Agregue la siguiente entrada al archivo /etc/sudoers y guárdelo:
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. Ahora puede iniciar sesión como miembro del grupo "Administradores del controlador de dominio de AAD" y debe tener privilegios administrativos en la máquina virtual.


## <a name="troubleshooting-domain-join"></a>Solución de problemas de unión al dominio
Consulte el artículo [Solución de problemas de unión al dominio](join-windows-vm.md#troubleshoot-domain-join-issues) .


## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](tutorial-create-instance.md)
* [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
