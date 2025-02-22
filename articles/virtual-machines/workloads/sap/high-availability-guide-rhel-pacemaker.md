---
title: Configuración de Pacemaker en Red Hat Enterprise Linux en Azure | Microsoft Docs
description: Configuración de Pacemaker en Red Hat Enterprise Linux en Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: mssedusch
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/17/2018
ms.author: sedusch
ms.openlocfilehash: 4e12ad64ef277396a101aab6d1bb8f3cc6079cf9
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099593"
---
# <a name="setting-up-pacemaker-on-red-hat-enterprise-linux-in-azure"></a>Configuración de Pacemaker en Red Hat Enterprise Linux en Azure

[planning-guide]:planning-guide.md
[deployment-guide]:deployment-guide.md
[dbms-guide]:dbms-guide.md
[sap-hana-ha]:sap-hana-high-availability.md
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351

[virtual-machines-linux-maintenance]:../../linux/maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot


Lea primero las notas y los documentos de SAP siguientes:

* Nota de SAP [1928533], que incluye:
  * La lista de tamaños de máquina virtual de Azure que se admiten para la implementación del software de SAP.
  * Información importante sobre capacidad para los tamaños de máquina virtual de Azure.
  * El software de SAP admitido y combinaciones de sistema operativo y base de datos.
  * La versión del kernel de SAP necesaria para Windows y Linux en Microsoft Azure.
* La nota de SAP [2015553] enumera los requisitos previos para las implementaciones de software de SAP admitidas por SAP en Azure.
* La nota de SAP [2002167] recomienda la configuración del sistema operativo para Red Hat Enterprise Linux
* La nota de SAP [2009879] contiene las instrucciones de SAP HANA para Red Hat Enterprise Linux
* La nota de SAP [2178632] contiene información detallada sobre todas las métricas de supervisión notificadas para SAP en Azure.
* La nota de SAP [2191498] incluye la versión de SAP Host Agent necesaria para Linux en Azure.
* La nota de SAP [2243692] incluye información acerca de las licencias de SAP en Linux en Azure.
* La nota de SAP [1999351] contiene más soluciones de problemas de la extensión de supervisión mejorada de Azure para SAP.
* La [WIKI de la comunidad SAP](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) contiene todas las notas de SAP que se necesitan para Linux.
* [Planeación e implementación de Azure Virtual Machines para SAP en Linux][planning-guide]
* [Implementación de Azure Virtual Machines para SAP en Linux (este artículo)][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP en Linux][dbms-guide]
* [Replicación del sistema de SAP HANA en un clúster de Pacemaker](https://access.redhat.com/articles/3004101)
* Documentación general de RHEL
  * [Introducción al complemento de alta disponibilidad](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)
  * [Administración del complemento de alta disponibilidad](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)
  * [Referencia del complemento de alta disponibilidad](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
* Documentación de RHEL específica para Azure:
  * [Directivas de compatibilidad para clústeres de alta disponibilidad RHEL: instancias de Microsoft Azure Virtual Machines como miembros del clúster](https://access.redhat.com/articles/3131341)
  * [Instalación y configuración de un clúster de alta disponibilidad de Red Hat Enterprise Linux 7.4 (y versiones posteriores) en Microsoft Azure](https://access.redhat.com/articles/3252491)
  * [Configuración de SAP S/4HANA ASCS/ERS con el servidor 2 de puesta en cola independiente (ENSA2) en Pacemaker en RHEL 7.6](https://access.redhat.com/articles/3974941)

## <a name="cluster-installation"></a>Instalación del clúster

![Información general de Pacemaker en RHEL](./media/high-availability-guide-rhel-pacemaker/pacemaker-rhel.png)

Los elementos siguientes tienen el prefijo **[A]** : aplicable a todos los nodos, **[1]** : aplicable solo al nodo 1 o **[2]** : aplicable solo al nodo 2.

1. **[A]** Registro

   Registre las máquinas virtuales y asócielas a un grupo que contenga repositorios para RHEL 7.

   <pre><code>sudo subscription-manager register
   # List the available pools
   sudo subscription-manager list --available --matches '*SAP*'
   sudo subscription-manager attach --pool=&lt;pool id&gt;
   </code></pre>

   Tenga en cuenta que si adjunta un grupo a una imagen de RHEL de pago por uso de Azure Marketplace, se le facturará el doble por el uso de RHEL, una vez por la imagen de pago por uso y otra, por el derecho de RHEL en el grupo que adjunta. Para mitigar esto, Azure ahora proporciona imágenes de RHEL de BYOS. Puede encontrar más información disponible [aquí](https://aka.ms/rhel-byos).

1. **[A]** Habilitación de RHEL para los repositorios SAP

   Para instalar los paquetes necesarios, habilite los siguientes repositorios.

   <pre><code>sudo subscription-manager repos --disable "*"
   sudo subscription-manager repos --enable=rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-sap-for-rhel-7-server-rpms
   sudo subscription-manager repos --enable=rhel-ha-for-rhel-7-server-eus-rpms
   </code></pre>

1. **[A]** Instalación del complemento de alta disponibilidad de RHEL

   <pre><code>sudo yum install -y pcs pacemaker fence-agents-azure-arm nmap-ncat
   </code></pre>

   > [!IMPORTANT]
   > Se recomiendan las siguientes versiones del agente de delimitación de Azure (o posterior) para que los clientes puedan beneficiarse de un tiempo de conmutación por error más rápido, si se produce un error en la detención de un recurso o los nodos del clúster no pueden comunicarse entre sí:  
   > RHEL 7.6: fence-agents-4.2.1-11.el7_6.8  
   > RHEL 7.5: fence-agents-4.0.11-86.el7_5.8  
   > RHEL 7.4: fence-agents-4.0.11-66.el7_4.12  
   > Para más información, consulte [La máquina virtual de Azure que se ejecuta como un miembro del clúster de alta disponibilidad de RHEL tarda mucho tiempo en delimitarse, ocurre un error en la delimitación o se agota el tiempo de espera antes de que la máquina virtual se apague](https://access.redhat.com/solutions/3408711).

   Compruebe la versión del agente de delimitación de Azure. Si es necesario, actualícelo a una versión igual o posterior a la indicada anteriormente.

   <pre><code># Check the version of the Azure Fence Agent
    sudo yum info fence-agents-azure-arm
   </code></pre>

   > [!IMPORTANT]
   > Si necesita actualizar el agente de barrera de Azure y, si usa un rol personalizado, asegúrese de actualizar el rol personalizado para incluir la acción **powerOff**. Para más información, consulte [Creación de un rol personalizado para el agente de barrera](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker#1-create-a-custom-role-for-the-fence-agent).  

1. **[A]** Configure la resolución nombres de host

   Puede usar un servidor DNS o modificar /etc/hosts en todos los nodos. En este ejemplo se muestra cómo utilizar el archivo /etc/hosts.
   Reemplace la dirección IP y el nombre de host en los siguientes comandos. La ventaja de usar /etc/hosts es que el clúster es independiente de DNS, lo que también podría representar un único punto de error.

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   Inserte las siguientes líneas en /etc/hosts. Cambie la dirección IP y el nombre de host para que coincida con su entorno

   <pre><code># IP address of the first cluster node
   <b>10.0.0.6 prod-cl1-0</b>
   # IP address of the second cluster node
   <b>10.0.0.7 prod-cl1-1</b>
   </code></pre>

1. **[A]** Haga que la contraseña de hacluster coincida

   <pre><code>sudo passwd hacluster
   </code></pre>

1. **[A]** Adición de reglas de firewall para Pacemaker

   Agregue las siguientes reglas de firewall para todas las comunicaciones entre los nodos del clúster.

   <pre><code>sudo firewall-cmd --add-service=high-availability --permanent
   sudo firewall-cmd --add-service=high-availability
   </code></pre>

1. **[A]** Habilitación de los servicios básicos del clúster

   Ejecute los comandos siguientes para habilitar el servicio de Pacemaker e inícielo.

   <pre><code>sudo systemctl start pcsd.service
   sudo systemctl enable pcsd.service
   </code></pre>

1. **[1]** Creación del clúster de Pacemaker

   Ejecute los comandos siguientes para autenticar los nodos y crear el clúster. Establezca el token en 30000 para permitir el mantenimiento con conservación de memoria. Para más información, consulte [este artículo para Linux][virtual-machines-linux-maintenance].

   <pre><code>sudo pcs cluster auth <b>prod-cl1-0</b> <b>prod-cl1-1</b> -u hacluster
   sudo pcs cluster setup --name <b>nw1-azr</b> <b>prod-cl1-0</b> <b>prod-cl1-1</b> --token 30000
   sudo pcs cluster start --all

   # Run the following command until the status of both nodes is online
   sudo pcs status

   # Cluster name: nw1-azr
   # WARNING: no stonith devices and stonith-enabled is not false
   # Stack: corosync
   # Current DC: <b>prod-cl1-1</b> (version 1.1.18-11.el7_5.3-2b07d5c5a9) - partition with quorum
   # Last updated: Fri Aug 17 09:18:24 2018
   # Last change: Fri Aug 17 09:17:46 2018 by hacluster via crmd on <b>prod-cl1-1</b>
   #
   # 2 nodes configured
   # 0 resources configured
   #
   # Online: [ <b>prod-cl1-0</b> <b>prod-cl1-1</b> ]
   #
   # No resources
   #
   #
   # Daemon Status:
   #   corosync: active/disabled
   #   pacemaker: active/disabled
   #   pcsd: active/enabled
   </code></pre>

1. **[A]** Establecimiento de votos esperados

   <pre><code>sudo pcs quorum expected-votes 2
   </code></pre>

## <a name="create-stonith-device"></a>Creación de un dispositivo STONITH

El dispositivo STONITH usa una entidad de servicio para la autorización de Microsoft Azure. Siga estos pasos para crear una entidad de servicio.

1. Vaya a <https://portal.azure.com>.
1. Abra la hoja Azure Active Directory  
   Vaya a Propiedades y anote el identificador del directorio. Se trata del **id. de inquilino**.
1. Haga clic en Registros de aplicaciones
1. Haga clic en Nuevo registro
1. Escriba un nombre y seleccione "Solo las cuentas de este directorio organizativo" 
2. Seleccione el tipo de aplicación "Web", escriba una dirección URL de inicio de sesión (por ejemplo, http:\//localhost) y haga clic en Agregar  
   La dirección URL de inicio de sesión no se usa y puede ser cualquier dirección URL válida
1. Seleccione Certificados y secretos, y luego haga clic en Nuevo secreto de cliente
1. Escriba una descripción para la nueva clave, seleccione "Nunca expira" y haga clic en Agregar
1. Anote el valor. Se utiliza como **contraseña** para la entidad de servicio
1. Seleccione Información general. Anote el identificador de la aplicación. Se utiliza como nombre de usuario (**Id. de inicio de sesión** en los pasos siguientes) de la entidad de servicio

### <a name="1-create-a-custom-role-for-the-fence-agent"></a>**[1]**  Creación de un rol personalizado para el agente de barrera

La entidad de servicio no tiene permiso para tener acceso a los recursos de Azure de forma predeterminada. Debe concedérselos para iniciar y detener (apagar) todas las máquinas virtuales del clúster. Si no ha creado aún el rol personalizado, puede crearlo mediante [PowerShell](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell) o la [CLI de Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-cli).

Utilice el siguiente contenido para el archivo de entrada. Debe adaptar el contenido a sus suscripciones; esto es, reemplace c276fc76-9cd4-44c9-99a7-4fd71546436e y e91d47c4-76f3-4271-a796-21b4ecfe3624 por los identificadores de su suscripción. Si solo tiene una suscripción, quite la segunda entrada en AssignableScopes.

```json
{
  "Name": "Linux Fence Agent Role",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows to power-off and start virtual machines",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/powerOff/action",
    "Microsoft.Compute/virtualMachines/start/action"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

### <a name="a-assign-the-custom-role-to-the-service-principal"></a>**[A]** Asignación del rol personalizado a la entidad de servicio

Asigne el rol personalizado "Rol del agente de barrera de Linux" que se creó en el último capítulo a la entidad de servicio. Deje de utilizar el rol de propietario.

1. Vaya a https://portal.azure.com.
1. Abra la hoja Todos los recursos
1. Seleccione la máquina virtual del primer nodo de clúster.
1. Haga clic en Control de acceso (IAM)
1. Haga clic en Agregar asignación de roles.
1. Seleccione el rol "Rol de agente de barrera de Linux".
1. Escriba el nombre de la aplicación que creó anteriormente
1. Haga clic en Guardar

Repita los pasos anteriores para el segundo nodo de clúster.

### <a name="1-create-the-stonith-devices"></a>**[1]** Cree los dispositivos STONITH

Después de editar los permisos para las máquinas virtuales, puede configurar los dispositivos STONITH en el clúster.

<pre><code>
sudo pcs property set stonith-timeout=900
</code></pre>

Para configurar al agente de delimitación, use el comando siguiente.

> [!NOTE]
> La opción "pcmk_host_map" SOLO es necesaria en el comando si los nombres de host RHEL y los nombres de nodo de Azure NO son idénticos. Consulte la sección en negrita en el comando.

<pre><code>sudo pcs stonith create rsc_st_azure fence_azure_arm login="<b>login ID</b>" passwd="<b>password</b>" resourceGroup="<b>resource group</b>" tenantId="<b>tenant ID</b>" subscriptionId="<b>subscription id</b>" <b>pcmk_host_map="prod-cl1-0:10.0.0.6;prod-cl1-1:10.0.0.7"</b> power_timeout=240 pcmk_reboot_timeout=900</code></pre>

### <a name="1-enable-the-use-of-a-stonith-device"></a>**[1]** Habilite el uso de un dispositivo STONITH

<pre><code>sudo pcs property set stonith-enabled=true
</code></pre>

## <a name="next-steps"></a>Pasos siguientes

* [Planeamiento e implementación de Azure Virtual Machines para SAP][planning-guide]
* [Implementación de Azure Virtual Machines para SAP][deployment-guide]
* [Implementación de DBMS de Azure Virtual Machines para SAP][dbms-guide]
* Para más información sobre cómo establecer la alta disponibilidad y planear la recuperación ante desastres de SAP HANA en Azure Virtual Machines, consulte [Alta disponibilidad de SAP HANA en las máquinas virtuales de Azure][sap-hana-ha].
