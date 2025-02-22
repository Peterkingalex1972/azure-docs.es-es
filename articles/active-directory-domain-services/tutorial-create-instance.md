---
title: 'Tutorial: Creación de una instancia de Azure Active Directory Domain Services | Microsoft Docs'
description: En este tutorial aprenderá a crear y configurar una instancia de Azure Active Directory Domain Services mediante Azure Portal.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: 7fa2a5088e2eae039d43ecf0db080190f74cd772
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125214"
---
# <a name="tutorial-create-and-configure-an-azure-active-directory-domain-services-instance"></a>Tutorial: Creación y configuración de una instancia de Azure Active Directory Domain Services

Azure Active Directory Domain Services (Azure AD DS) proporciona servicios de dominio administrados como, por ejemplo, unión a un dominio, directiva de grupo, LDAP o autenticación Kerberos/NTLM, que son totalmente compatibles con Windows Server Active Directory. Estos servicios de dominio se usan sin necesidad de implementar, administrar ni aplicar revisiones a los controladores de dominio. Azure AD DS se integra con el inquilino de Azure AD existente. Esta integración permite a los usuarios iniciar sesión con sus credenciales corporativas y, además, le permite usar grupos y cuentas de usuario existentes para proteger el acceso a los recursos.

En este tutorial se muestra cómo crear y configurar una instancia de Azure AD DS mediante Azure Portal.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración de DNS y los parámetros de red virtual para un dominio administrado
> * Creación de una instancia de Azure AD DS
> * Incorporación de usuarios administrativos a la administración de dominios
> * Habilitación de la sincronización de hash de contraseñas

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará los siguientes recursos y privilegios:

* Una suscripción de Azure activa.
    * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
    * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Necesita privilegios de *administrador global* en el inquilino de Azure AD para habilitar Azure AD DS.
* Necesita privilegios de *colaborador* en la suscripción de Azure para crear los recursos de Azure AD DS necesarios.
* El inquilino de Azure AD debe estar [configurado para el autoservicio de restablecimiento de contraseña][configure-sspr].

> [!IMPORTANT]
> Después de crear un dominio administrado de Azure AD DS, no puede trasladar la instancia a otro grupo de recursos, red virtual, suscripción, etc. Tenga cuidado a la hora de seleccionar la suscripción, el grupo de recursos, la región y la red virtual más adecuados al implementar la instancia de Azure AD DS.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

En este tutorial creará y configurará la instancia de Azure AD DS mediante Azure Portal. Para empezar a trabajar, primero inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-an-instance-and-configure-basic-settings"></a>Creación de una instancia y configuración de las opciones básicas

Para iniciar el Asistente para **habilitar Azure AD Domain Services**, complete los pasos siguientes:

1. En la esquina superior izquierda de Azure Portal, seleccione **+ Crear un recurso**.
1. Escriba *Domain Services* en la barra de búsqueda y, a continuación, elija *Azure AD Domain Services* en las sugerencias de búsqueda.
1. En la página Azure AD Domain Services, seleccione **Crear**. Se inicia el Asistente para **habilitar Azure AD Domain Services**.

Cuando se crea una instancia de Azure AD DS, se especifica un nombre DNS. Hay algunas consideraciones que se deben tener en cuenta al elegir este nombre DNS:

* **Nombre de dominio integrado:** de forma predeterminada, se usa el nombre de dominio integrado del directorio (un sufijo *.onmicrosoft.com*). Si se quiere habilitar el acceso LDAP seguro al dominio administrado a través de Internet, no se puede crear un certificado digital para proteger la conexión con este dominio predeterminado. Microsoft es el propietario del dominio *.onmicrosoft.com*, por lo que una entidad de certificación no emitirá un certificado.
* **Nombres de dominio personalizados:** el enfoque más común consiste en especificar un nombre de dominio personalizado, normalmente uno que ya se posee y es enrutable. Cuando se usa un dominio personalizado enrutable, el tráfico puede fluir correctamente según sea necesario para admitir las aplicaciones.
* **Sufijos de dominio no enrutables:** por lo general, se recomienda evitar un sufijo de nombre de dominio no enrutable, como *contoso.local*. El sufijo *.local* no es enrutable y puede provocar problemas con la resolución de DNS.

También se aplican las siguientes restricciones de nombre DNS:

* **Restricciones de prefijo de dominio:** no se puede crear un dominio administrado con un prefijo de más de 15 caracteres. El prefijo del nombre de dominio especificado (por ejemplo, *contoso* en el nombre de dominio *contoso.com*) debe contener 15 caracteres o menos.
* **Conflictos de nombres de red:** el nombre de dominio DNS del dominio administrado no puede existir ya en la red virtual. Busque, en concreto, los siguientes escenarios que provocarían un conflicto de nombres:
    * Ya tiene un dominio de Active Directory con el mismo nombre de dominio DNS en la red virtual de Azure.
    * La red virtual en la que planea habilitar el dominio administrado tiene una conexión VPN con la red local. En este escenario, asegúrese de que no tiene un dominio con el mismo nombre de dominio DNS de la red local.
    * Ya dispone de un servicio en la nube de Azure con ese nombre en la red virtual de Azure.

Complete los campos de la ventana *Datos básicos* de Azure Portal para crear una instancia de Azure AD DS:

1. Escriba un **nombre de dominio DNS** para el dominio administrado, teniendo en cuenta los puntos anteriores.
1. Seleccione la **suscripción** de Azure en la que desea crear el dominio administrado.
1. Seleccione el **grupo de recursos** al que debería pertenecer el dominio administrado. Elija **Crear nuevo** o seleccione un grupo de recursos existente.
1. Elija la **ubicación** de Azure en que se debe crear el dominio administrado.
1. Haga clic en **Aceptar** para pasar a la sección **Red**.

![Configuración de las opciones básicas de una instancia de Azure AD Domain Services](./media/tutorial-create-instance/basics-window.png)

## <a name="create-and-configure-the-virtual-network"></a>Creación y configuración de la red virtual

Para proporcionar conectividad, se necesitan una red virtual de Azure y una subred dedicada. Azure AD DS estará habilitado en esta subred de la red virtual. En este tutorial creará una red virtual, aunque podría elegir usar una existente. En ambos enfoques, debe crear una subred dedicada para que se use en Azure AD DS.

Entre las consideraciones para esta subred de red virtual dedicada se incluyen los siguientes aspectos:

* La subred debe tener al menos entre 3 y 5 direcciones IP disponibles en su intervalo de direcciones para admitir los recursos de Azure AD DS.
* No seleccione la subred de *Puerta de enlace* para implementar Azure AD DS. No se admite la implementación de Azure AD DS en una subred de *Puerta de enlace*.
* No implemente ninguna otra máquina virtual en la subred. Las aplicaciones y las máquinas virtuales suelen usar grupos de seguridad de red para proteger la conectividad. La ejecución de estas cargas de trabajo en una subred independiente permite aplicar esos grupos de seguridad de red sin interrumpir la conectividad con el dominio administrado.
* Después de habilitar Azure AD DS, el dominio administrado ya no se puede mover a otra red virtual.

Para más información sobre cómo planear y configurar la red virtual, consulte [Consideraciones de red de Azure AD Domain Services][network-considerations].

Complete los campos de la ventana *Red* de la siguiente manera:

1. En la ventana **Red**, elija **Seleccionar una red virtual**.
1. En este tutorial, elija **Crear nueva** red virtual para implementar en ella Azure AD DS.
1. Escriba un nombre para la red virtual, por ejemplo *myVnet*, y proporcione un intervalo de direcciones, por ejemplo *10.1.0.0/16*.
1. Cree una subred dedicada con un nombre claro, por ejemplo *DomainServices*. Indique un intervalo de direcciones, como *10.1.0.0/24*.

    ![Creación de una red virtual y una subred para su uso con Azure AD Domain Services](./media/tutorial-create-instance/create-vnet.png)

    Asegúrese de seleccionar un intervalo de direcciones que se encuentre dentro de su intervalo de direcciones IP privadas. Los intervalos de direcciones IP que no son de su propiedad y que se encuentran en el espacio de direcciones públicas provocan errores en Azure AD DS.

    > [!TIP]
    > En la página **Elegir red virtual** se muestran las redes virtuales existentes que pertenecen al grupo de recursos y a la ubicación de Azure que seleccionó anteriormente. Antes de implementar Azure AD DS, debe [crear una subred dedicada][create-dedicated-subnet].

1. Una vez creadas la red virtual y la subred, debe seleccionarse automáticamente la subred, por ejemplo *DomainServices*. También puede elegir otra subred existente que forme parte de la red virtual seleccionada:

    ![Elección de la subred dedicada dentro de la red virtual](./media/tutorial-create-instance/choose-subnet.png)

1. Seleccione **Aceptar** para confirmar la configuración de la red virtual.

## <a name="configure-an-administrative-group"></a>Configuración de un grupo administrativo

Para la administración del dominio de Azure AD DS se usa un grupo administrativo especial denominado *Administradores de DC de AAD*. A los miembros de este grupo se les conceden permisos administrativos en las máquinas virtuales que están unidas al domino administrado. En las máquinas virtuales unidas al dominio, este grupo se agrega al grupo de administradores locales. Además, los miembros de este grupo también pueden usar Escritorio remoto para conectarse de forma remota a las máquinas virtuales unidas al dominio.

En los dominios administrados con Azure AD DS, no tiene permisos de *Administrador de dominio* ni de *Administrador de empresa*. Estos permisos están reservados por el servicio y no están disponibles para los usuarios del inquilino. En su lugar, el grupo *Administradores de DC de AAD* permite realizar algunas operaciones con privilegios. Estas operaciones incluyen unir equipos al dominio, formar parte del grupo de administradores en las máquinas virtuales unidas al dominio y configurar una directiva de grupo.

El asistente crea automáticamente el grupo *Administradores de DC de AAD* en el directorio de Azure AD. Si tiene un grupo existente con este nombre en el directorio de Azure AD, el asistente lo selecciona. También puede optar por agregar usuarios adicionales a este grupo de *Administradores de DC de AAD* durante el proceso de implementación. Estos pasos se pueden completar más adelante.

1. Para agregar otros usuarios a este grupo de *Administradores de DC de AAD*, seleccione **Administrar pertenencia al grupo**.
1. Seleccione el botón **Agregar miembros** y, después, busque y seleccione usuarios en el directorio de Azure AD. Por ejemplo, busque su propia cuenta y agréguela al grupo *Administradores de DC de AAD*.

    ![Configuración de la pertenencia al grupo Administradores de DC de AAD](./media/tutorial-create-instance/admin-group.png)

1. Cuando finalice, seleccione **Aceptar**.

## <a name="configure-synchronization"></a>Configuración de la sincronización

Azure AD DS permite sincronizar *todos* los usuarios y grupos disponibles en Azure AD, o bien realizar una sincronización *con ámbito* solo de grupos específicos. Si decide sincronizar *todos* los usuarios y grupos, no tendrá la opción de realizar más adelante solo una sincronización con ámbito. Para más información sobre la sincronización con ámbito, consulte [Sincronización con ámbito de Azure AD Domain Services][scoped-sync].

1. Para este tutorial, elija la sincronización de **Todos** los usuarios y grupos. Esta es la opción predeterminada de sincronización.

    ![Realización de una sincronización completa de usuarios y grupos de Azure AD](./media/tutorial-create-instance/sync-all.png)

1. Seleccione **Aceptar**.

## <a name="deploy-your-managed-domain"></a>Implementación del dominio administrado

En la página **Resumen** del asistente, revise la configuración del dominio administrado. Puede volver a cualquier paso del asistente para realizar cambios.

1. Para crear el dominio administrado, seleccione **Aceptar**.
1. El proceso de aprovisionamiento del dominio administrado puede tardar hasta una hora. En el portal se muestra una notificación que señala el progreso de la implementación de Azure AD DS. Seleccione la notificación para ver el progreso detallado de la implementación.

    ![Notificación en Azure Portal de la implementación en curso](./media/tutorial-create-instance/deployment-in-progress.png)

1. Seleccione el grupo de recursos, como *myResourceGroup*, y, a continuación, elija la instancia de Azure AD DS en la lista de recursos de Azure, por ejemplo *contoso.com*. La pestaña **Información general** muestra que el dominio administrado se está *Implementando*. No se puede configurar el dominio administrado hasta que está totalmente aprovisionado.

    ![Estado de Domain Services durante el estado de aprovisionamiento](./media/tutorial-create-instance/provisioning-in-progress.png)

1. Cuando el dominio administrado está aprovisionado por completo, la pestaña **Información general** muestra el estado del dominio como *En ejecución*.

    ![Estado de Domain Services una vez aprovisionado correctamente](./media/tutorial-create-instance/successfully-provisioned.png)

Durante el proceso de aprovisionamiento, Azure AD DS crea dos aplicaciones empresariales, *Domain Controller Services* y *AzureActiveDirectoryDomainControllerServices* en su directorio. Estas aplicaciones empresariales se necesitan para dar servicio al dominio administrado. Es imprescindible que estas aplicaciones no se eliminen en ningún momento.

## <a name="update-dns-settings-for-the-azure-virtual-network"></a>Actualización de la configuración DNS para Azure Virtual Network

Ahora que Azure AD DS se ha implementado correctamente, es el momento de configurar la red virtual para permitir que otras máquinas virtuales y aplicaciones conectadas usen el dominio administrado. Para proporcionar esta conectividad, actualice la configuración del servidor DNS de la red virtual de modo que apunte a las dos direcciones IP donde se ha implementado Azure AD DS.

1. En la pestaña **Información general** del dominio administrado se muestran algunos de los **Pasos de configuración necesarios**. El primer paso de configuración es la actualización de la configuración de servidores DNS para la red virtual. Cuando la configuración de DNS esté correctamente establecida, ya no se mostrará este paso.

    Las direcciones que aparecen son los controladores de dominio que se usan en la red virtual. En este ejemplo, las direcciones son *10.1.0.4* y *10.1.0.5*. Más adelante puede encontrar estas direcciones IP en la pestaña **Propiedades**.

    ![Configuración de los valores de DNS para la red virtual con las direcciones IP de Azure AD Domain Services](./media/tutorial-create-instance/configure-dns.png)

1. Seleccione el botón **Configurar** para actualizar la configuración del servidor DNS de la red virtual. Los valores de DNS se configuran automáticamente para la red virtual.

> [!TIP]
> Si ha seleccionado una red virtual existente en los pasos anteriores, las máquinas virtuales conectadas a la red solo obtendrán la nueva configuración de DNS después de un reinicio. Puede reiniciar las máquinas virtuales mediante Azure Portal, Azure PowerShell o la CLI de Azure.

## <a name="enable-user-accounts-for-azure-ad-ds"></a>Habilitación de cuentas de usuario para Azure AD DS

Para autenticar a los usuarios en el dominio administrado, Azure AD DS necesita los hash de las contraseñas en un formato adecuado para la autenticación de NT LAN Manager (NTLM) y Kerberos. A menos que habilite Azure AD DS para el inquilino, Azure AD no genera ni almacena los hash de las contraseñas en el formato necesario para la autenticación NTLM o Kerberos. Por motivos de seguridad, Azure AD tampoco almacena las credenciales de contraseñas en forma de texto sin cifrar. Por consiguiente, Azure AD no tiene forma de generar automáticamente estos hash de las contraseñas de NTLM o Kerberos basándose en las credenciales existentes de los usuarios.

> [!NOTE]
> Una vez configurados correctamente, los hash de las contraseñas que se pueden usar se almacenan en el dominio administrado de Azure AD DS. Si elimina el dominio administrado de Azure AD DS, también se eliminarán los hash de las contraseñas que haya almacenados en ese momento. La información de credenciales sincronizada en Azure AD no se puede volver a usar si posteriormente crea un dominio administrado de Azure AD DS: debe volver a configurar la sincronización de los hash de contraseña para almacenarlos de nuevo. Los usuarios o las máquinas virtuales anteriormente unidos a un dominio no podrán autenticarse de inmediato, ya que Azure AD debe generar y almacenar los hash de contraseña en el nuevo dominio administrado de Azure AD DS. Para más información, consulte [Proceso de sincronización de hash de contraseña para Azure AD DS y Azure AD Connect][password-hash-sync-process].

Los pasos necesarios para generar y almacenar estos hash de contraseña son diferentes según se trate de cuentas de usuario solo de nube creadas en Azure AD o de las cuentas de usuarios que se sincronizan desde el directorio local mediante Azure AD Connect. Una cuenta de usuario solo de nube es una cuenta creada en su directorio de Azure AD mediante Azure Portal, o bien mediante cmdlets de PowerShell de Azure AD. Estas cuentas de usuario no se sincronizan desde un directorio local. En este tutorial, vamos a trabajar con una cuenta de usuario básica solo de nube. Para más información sobre los pasos adicionales necesarios para usar Azure AD Connect, consulte [Sincronización de los hash de contraseña para las cuentas de usuario sincronizadas desde la instancia local de AD con el dominio administrado][on-prem-sync].

> [!TIP]
> Si el inquilino de Azure AD tiene una combinación entre usuarios solo de nube y usuarios de la instancia local de AD, debe realizar ambos procedimientos.

En el caso de las cuentas de usuario solo de nube, los usuarios deben cambiar sus contraseñas para poder usar Azure AD DS. Este proceso de cambio de contraseña hace que los valores hash de contraseña para la autenticación Kerberos y NTLM se generen y almacenen en Azure AD. Puede hacer expirar las contraseñas de todos los usuarios del inquilino que tienen que usar Azure AD DS, lo que fuerza un cambio de contraseña en el siguiente inicio de sesión, o bien indicarles que cambien manualmente sus contraseñas. En este tutorial vamos a cambiar manualmente una contraseña de usuario.

Para que un usuario pueda restablecer su contraseña, el inquilino de Azure AD debe estar [configurado para el autoservicio de restablecimiento de contraseña][configure-sspr].

Para cambiar la contraseña de un usuario solo de nube, el usuario debe completar los pasos siguientes:

1. Vaya a la página Panel de acceso de Azure AD en [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. En la esquina superior derecha, seleccione su nombre y, a continuación, elija **Perfil** en el menú desplegable.

    ![Seleccionar perfil](./media/tutorial-create-instance/select-profile.png)

1. En la página **Perfil**, seleccione **Cambiar contraseña**.
1. En la página **Cambiar contraseña**, escriba la contraseña existente (anterior) y luego escriba y confirme una nueva contraseña.
1. Seleccione **Submit** (Enviar).

Tras el cambio, la nueva contraseña tarda unos minutos en poder usarse en Azure AD DS. Después de unos veinte minutos, podrá usar la nueva contraseña para iniciar sesión en los equipos unidos al dominio administrado.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración de DNS y los parámetros de red virtual para un dominio administrado
> * Creación de una instancia de Azure AD DS
> * Incorporación de usuarios administrativos a la administración de dominios
> * Habilitación de cuentas de usuario para Azure AD DS y generación de hash de contraseña

Para ver este dominio administrado en acción, cree una máquina virtual y únala al dominio.

> [!div class="nextstepaction"]
> [Unión de una máquina virtual de Windows Server a un dominio administrado](join-windows-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[network-considerations]: network-considerations.md
[create-dedicated-subnet]: ../virtual-network/virtual-network-manage-subnet.md#add-a-subnet
[scoped-sync]: scoped-synchronization.md
[on-prem-sync]: tutorial-configure-password-hash-sync.md
[configure-sspr]: ../active-directory/authentication/quickstart-sspr.md
[password-hash-sync-process]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md#password-hash-sync-process-for-azure-ad-domain-services
