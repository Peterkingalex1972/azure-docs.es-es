---
title: 'Azure Active Directory Domain Services: Unión de una máquina virtual Linux con CoreOS | Microsoft Docs'
description: Unión de una máquina virtual Linux con CoreOS a Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: c1f3d1ec7bb9e9f449cea3f9aa36ca8f80348c6e
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69612823"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>Unión de una máquina virtual Linux con CoreOS a un dominio administrado
En este artículo se muestra cómo unir una máquina virtual Linux con CoreOS de Azure a un dominio administrado de Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:
1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](tutorial-create-instance.md).
4. Asegúrese de que ha configurado las direcciones IP del dominio administrado como servidores DNS de la red virtual. Para más información, consulte [cómo actualizar la configuración de DNS para la red virtual de Azure](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)
5. Complete los pasos necesarios para [sincronizar contraseñas para el dominio administrado de Azure AD Domain Services](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds).


## <a name="provision-a-coreos-linux-virtual-machine"></a>Aprovisionamiento de una máquina virtual Linux con CoreOS
Aprovisione una máquina virtual Linux con CoreOS en Azure mediante cualquiera de los métodos siguientes:
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI de Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

En este artículo se utiliza la imagen de máquina virtual **Linux con CoreOS (estable)** en Azure.

> [!IMPORTANT]
> * Implemente la máquina virtual en la **misma red virtual en la que ha habilitado Azure AD Domain Services**.
> * Elija una **subred diferente** de aquella en la que ha habilitado Azure AD Domain Services.
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>Conexión remota a la máquina virtual de Linux recién aprovisionada
Se ha aprovisionado la máquina virtual con CoreOS en Azure. La siguiente tarea consiste en conectar de forma remota a la máquina virtual mediante la cuenta de administrador local creada al aprovisionar la máquina virtual.

Siga las instrucciones que aparecen en el artículo sobre [cómo iniciar sesión en una máquina virtual con Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Configuración del archivo hosts en la máquina virtual Linux
En el terminal SSH, edite el archivo /etc/hosts y actualice la dirección IP y el nombre de host de la máquina.

```console
sudo vi /etc/hosts
```

En el archivo hosts, escriba el siguiente valor:

```console
127.0.0.1 contoso-coreos.contoso.com contoso-coreos
```

En este caso, "contoso.com" es el nombre de dominio DNS del dominio administrado. "contoso-coreos" es el nombre de host de la máquina virtual con CoreOS que va a unir al dominio administrado.


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Configuración del servicio SSSD en la máquina virtual Linux
A continuación, actualice el archivo de configuración SSSD en ("/etc/sssd/sssd.conf") para que coincida con el ejemplo siguiente:

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = contoso.COM

[domain/contoso.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = contoso.COM
```

Reemplace "contoso.com" por el nombre de dominio DNS del dominio administrado. Asegúrese de que especificar el nombre de dominio en el archivo conf en mayúsculas.


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Unión de la máquina virtual de Linux al dominio administrado
Ahora que los paquetes necesarios están instalados en la máquina virtual de Linux, la siguiente tarea es unir la máquina virtual al dominio administrado.

```console
sudo adcli join -D contoso.COM -U bob@contoso.COM -K /etc/krb5.keytab -H contoso-coreos.contoso.com -N coreos
```


> [!NOTE]
> **Solución de problemas:** si *adcli* no encuentra el dominio administrado:
>   * Asegúrese de que el dominio sea accesible desde la máquina virtual (pruebe con ping).
>   * Compruebe que la máquina virtual se haya implementado realmente en la misma red virtual en la que el dominio administrado está disponible.
>   * Compruebe si ha actualizado la configuración del servidor DNS para que la red virtual apunte a los controladores de dominio del dominio administrado.

Inicie el servicio SSSD. En el terminal SSH, escriba el siguiente comando:
  
```console
sudo systemctl start sssd.service
```


## <a name="verify-domain-join"></a>Verificación de la unión a un dominio
Verifique si la máquina se ha unido correctamente al dominio administrado. Conéctese a la máquina virtual con CoreOS unida al dominio con otra conexión SSH. Utilice una cuenta de usuario del dominio y, a continuación, compruebe si la cuenta de usuario se ha resuelto correctamente.

1. En el terminal SSH, escriba el comando siguiente para conectarse a la máquina virtual con CoreOS unida al dominio con SSH. Use una cuenta de dominio que pertenezca al dominio administrado (por ejemplo, "bob@contoso.COM" en este caso).
    
    ```console
    ssh -l bob@contoso.COM contoso-coreos.contoso.com
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
