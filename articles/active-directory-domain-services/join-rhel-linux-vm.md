---
title: 'Azure Active Directory Domain Services: Unión de una VM de RHEL a un dominio administrado | Microsoft Docs'
description: Unión de una máquina virtual Red Hat Enterprise Linux a un dominio administrado de Azure AD
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b59bd7c7196ceb87da087967498eca6dda7c212b
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990598"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Unión de una máquina virtual de Red Hat Enterprise Linux 7 a un dominio administrado
Este artículo muestra cómo unir una máquina virtual de Red Hat Enterprise Linux (RHEL) 7 a un dominio administrado con Servicios de dominio de Azure AD.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:  
1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](tutorial-create-instance.md).
4. Asegúrese de que ha configurado las direcciones IP del dominio administrado como servidores DNS de la red virtual. Para más información, consulte [cómo actualizar la configuración de DNS para la red virtual de Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Complete los pasos necesarios para [sincronizar contraseñas para el dominio administrado de Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Aprovisionamiento de una máquina virtual de Red Hat Enterprise Linux
Aprovisione una máquina virtual RHEL 7 en Azure mediante cualquiera de los métodos siguientes:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI de Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * Implemente la máquina virtual en la **misma red virtual en la que ha habilitado Azure AD Domain Services**.
> * Elija una **subred diferente** de aquella en la que ha habilitado Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conexión remota a la máquina virtual de Linux recién aprovisionada
Se ha aprovisionado la máquina virtual de RHEL 7.2 en Azure. La siguiente tarea consiste en conectar de forma remota a la máquina virtual mediante la cuenta de administrador local creada al aprovisionar la máquina virtual.

Siga las instrucciones que aparecen en el artículo sobre [cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configuración del archivo hosts en la máquina virtual Linux
En el terminal SSH, edite el archivo /etc/hosts y actualice la dirección IP y el nombre de host de la máquina.

```console
sudo vi /etc/hosts
```

En el archivo hosts, escriba el siguiente valor:

```console
127.0.0.1 contoso-rhel.contoso.com contoso-rhel
```

En este caso, "contoso.com" es el nombre de dominio DNS del dominio administrado. "contoso-rhel" es el nombre de host de la máquina virtual RHEL que va a unir al dominio administrado.


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Instalación de los paquetes necesarios en la máquina virtual de Linux
A continuación, instale los paquetes necesarios para unirse a un dominio en la máquina virtual. En el terminal SSH, escriba el siguiente comando para instalar los paquetes necesarios:

```console
sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Unión de la máquina virtual de Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

1. Detecte el dominio administrado con Servicios de dominio de AAD. En el terminal SSH, escriba el siguiente comando:

    ```console
    sudo realm discover CONTOSO.COM
    ```

   > [!NOTE]
   > **Solución de problemas:** si la *detección de dominio kerberos* no puede encontrar el dominio administrado:
   >   * Asegúrese de que el dominio sea accesible desde la máquina virtual (pruebe con ping).
   >   * Compruebe que la máquina virtual se haya implementado realmente en la misma red virtual en la que el dominio administrado está disponible.
   >   * Compruebe si ha actualizado la configuración del servidor DNS para que la red virtual apunte a los controladores de dominio del dominio administrado.

2. Inicialice Kerberos. En el terminal SSH, escriba el siguiente comando:

    > [!TIP]
    > * Asegúrese de especificar un usuario que pertenezca al grupo "Administradores del controlador de dominio de AAD".
    > * Especifique el nombre de dominio en mayúsculas o kinit generará un error.

    ```console
    kinit bob@CONTOSO.COM
    ```

3. Una la máquina al dominio. En el terminal SSH, escriba el siguiente comando:

    > [!TIP]
    > Utilice la misma cuenta de usuario que ha especificado el paso anterior ("kinit").
    >
    > Si la máquina virtual no puede unirse al dominio, asegúrese de que el grupo de seguridad de red de la máquina virtual permita el tráfico Kerberos saliente en el puerto TCP + UDP 464 a la subred de la red virtual para el dominio administrado de Azure AD DS.

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'bob@CONTOSO.COM'
    ```

Debe obtener un mensaje (Máquina inscrita correctamente en el dominio kerberos) cuando la máquina está unida correctamente al dominio administrado.


## <a name="verify-domain-join"></a>Verificación de la unión a un dominio
Verifique si la máquina se ha unido correctamente al dominio administrado. Conéctese a la máquina virtual RHEL unida al dominio con otra conexión SSH. Utilice una cuenta de usuario del dominio y, a continuación, compruebe si la cuenta de usuario se ha resuelto correctamente.

1. En el terminal SSH, escriba el comando siguiente para conectarse a la máquina virtual RHEL unida al dominio con SSH. Use una cuenta de dominio que pertenezca al dominio administrado (por ejemplo, "bob@CONTOSO.COM" en este caso).
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-rhel.contoso.com
    ```

2. En el terminal SSH, escriba el comando siguiente para ver si el directorio principal se ha inicializado correctamente.
    
    ```console
    pwd
    ```

3. En el terminal SSH, escriba el comando siguiente para ver si los miembros del grupo se están resolviendo correctamente.
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>Solución de problemas de unión al dominio
Consulte el artículo [Solución de problemas de unión al dominio](join-windows-vm.md#troubleshoot-domain-join-issues) .

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](tutorial-create-instance.md)
* [Unión de una máquina virtual de Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md)
* [Cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Installing Kerberos (Instalación de Kerberos)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7: guía de integración de Windows)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
