---
title: Eliminación de un almacén de Recovery Services en Azure
description: Se describe cómo eliminar un almacén de Recovery Services.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: dacurwin
ms.openlocfilehash: 9c63170b60a871182042acab8a35e505c603f260
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018889"
---
# <a name="delete-a-recovery-services-vault"></a>Eliminación de un almacén de Recovery Services

En este artículo se describe cómo eliminar un almacén de Recovery Services de [Azure Backup](backup-overview.md). Contiene instrucciones para quitar las dependencias y, a continuación, eliminar un almacén.


## <a name="before-you-start"></a>Antes de comenzar

No se puede eliminar un almacén de Recovery Services que tenga dependencias, como servidores protegidos o servidores de administración de copia de seguridad, asociadas al almacén.

- Tampoco se puede eliminar un almacén que contenga datos de copia de seguridad (es decir, incluso si se ha detenido la protección pero se han conservado los datos de copia de seguridad).

- Si elimina un almacén que contiene dependencias, se mostrará el siguiente error:

  ![Error de eliminación de almacén](./media/backup-azure-delete-vault/error.png)

- Si elimina un elemento protegido local (MARS, MABS o DPM en Azure) del portal que contiene dependencias, aparecerá un mensaje de advertencia:

  ![Error al eliminar el servidor protegido](./media/backup-azure-delete-vault/error-message.jpg)

  
Para eliminar correctamente el almacén, elija el escenario que coincida con la configuración y siga los pasos recomendados:

Escenario | Pasos para quitar las dependencias y eliminar el almacén |
-- | --
Tengo archivos y carpetas locales protegidos mediante la copia de seguridad del agente de Azure Backup (MARS) en Azure. | Siga los pasos descritos en Eliminación de datos de copia de seguridad y elementos de copia de seguridad: [Para el agente de Mars](#delete-backup-items-from-mars-management-console).
Tengo máquinas locales protegidas con MABS (Microsoft Azure Backup Server) o DPM en Azure (System Center Data Protection Manager). | Siga los pasos que se indican en Eliminación de datos de copia de seguridad y elementos de copia de seguridad: [Para el agente de MABS](#delete-backup-items-from-mabs-management-console).
Tengo elementos protegidos en la nube (por ejemplo, máquina virtual laaS, recurso compartido de archivos de Azure, etc.).  | Siga los pasos que se describen en Eliminación de datos de copia de seguridad y elementos de copia de seguridad: [Para elementos protegidos en la nube](#delete-protected-items-in-cloud).
Tengo elementos protegidos tanto en el entorno local como en la nube. | Siga los pasos que se indican en Eliminación de datos de copia de seguridad y elementos de copia de seguridad, en la secuencia siguiente: <br> - [Para elementos protegidos en la nube](#delete-protected-items-in-cloud)<br> - [Para el agente de MARS](#delete-backup-items-from-mars-management-console) <br> - [Para el agente de MABS](#delete-backup-items-from-mabs-management-console)
No tengo elementos protegidos en el entorno local o en la nube; sin embargo, sigo obteniendo el error de eliminación del almacén. | Siga los pasos que se describen en [Eliminación del almacén de Recovery Services mediante el cliente de Azure Resource Manager](#delete-the-recovery-services-vault-using-azure-resource-manager-client).


## <a name="delete-protected-items-in-cloud"></a>Eliminación de elementos protegidos en la nube

Antes de continuar, lea **[esta](#before-you-start)** sección para comprender el proceso de eliminación de dependencias y almacenes.

Para detener la protección y eliminar los datos de copia de seguridad, realice lo siguiente:

1. En el portal > **Almacén de Recovery Services** > **Elementos de copia de seguridad** elija los elementos protegidos en la nube (por ejemplo AzureVirtual Machine, Azure Storage [Azure Files], SQL en máquina virtual de Azure, etc.).

    ![Seleccionar el tipo de copia de seguridad](./media/backup-azure-delete-vault/azure-storage-selected.png)

2. Haga clic con el botón derecho en el elemento de copia de seguridad. En función de si el elemento de copia de seguridad está protegido o no, aparecerá el menú **Detener copia de seguridad** o **Eliminar datos de copia de seguridad**.

    - En **Detener copia de seguridad**, seleccione **Eliminar datos de copia de seguridad** en el menú desplegable. Escriba el **nombre** del elemento de copia de seguridad (distingue mayúsculas de minúsculas), seleccione un **motivo**, escriba **Comentarios** y haga clic en **Detener copia de seguridad**.

        ![Seleccionar el tipo de copia de seguridad](./media/backup-azure-delete-vault/stop-backup-item.png)

    - En **Eliminar datos de copia de seguridad**, escriba el nombre del elemento de copia de seguridad (distingue mayúsculas de minúsculas), seleccione un **motivo**, escriba **Comentarios** y haga clic en **Eliminar**. 

         ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/stop-backup-blade-delete-backup-data.png)

5. Compruebe la **notificación** ![eliminación de datos de copia de seguridad](./media/backup-azure-delete-vault/messages.png). Una vez finalizada la operación, el servicio muestra el mensaje: **Deteniendo copia de seguridad y eliminando datos de copia de seguridad para "*elemento de copia de seguridad*"** . **La operación se completó correctamente**.
6. Haga clic en **Actualizar** en el menú **Elementos de copia de seguridad**, para comprobar si se ha quitado el elemento de copia de seguridad.

      ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/empty-items-list.png)

## <a name="delete-protected-items-on-premises"></a>Eliminación de elementos protegidos locales

Antes de continuar, lea **[esta](#before-you-start)** sección para comprender el proceso de eliminación de dependencias y almacenes.

1. En el menú del panel del almacén, haga clic en **Infraestructura de copia de seguridad**.
2. Según el escenario local, elija la opción siguiente:

      - En **Agente de copia de seguridad de Azure**, elija **Servidores protegidos** > **Agente de copia de seguridad de Azure** y seleccione el servidor que desea eliminar. 

        ![Seleccionar el almacén para abrir su panel](./media/backup-azure-delete-vault/identify-protected-servers.png)

      - En **Azure Backup Server**/**DPM**, elija  **Servidores de administración de copias de seguridad**. Seleccione el servidor que quiere eliminar. 


          ![Seleccionar el almacén para abrir su panel.](./media/backup-azure-delete-vault/delete-backup-management-servers.png)

3. Aparece la hoja **Eliminar** con el mensaje de advertencia.

     ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/delete-protected-server.png)

     Revise el mensaje de advertencia y las instrucciones proporcionadas en la casilla de consentimiento.
    
    > [!NOTE]
    >- Si el servidor protegido está sincronizado con el servicio de Azure y existen elementos de copia de seguridad, la casilla de consentimiento mostrará el número de elementos de copia de seguridad dependientes y el vínculo para ver esos elementos.
    >- Si el servidor protegido no está sincronizado con el servicio de Azure y existen elementos de copia de seguridad, la casilla de consentimiento mostrará el número de elementos de copia de seguridad.
    >- Si los elementos de copia de seguridad no existen, la casilla de consentimiento pedirá la eliminación.

4. Seleccione la casilla consentimiento y haga clic en **Eliminar**.




5. Compruebe la **notificación** ![eliminación de datos de copia de seguridad](./media/backup-azure-delete-vault/messages.png). Una vez finalizada la operación, el servicio muestra el mensaje: **Deteniendo copia de seguridad y eliminando datos de copia de seguridad para "*elemento de copia de seguridad*"** . **La operación se completó correctamente**.
6. Haga clic en **Actualizar** en el menú **Elementos de copia de seguridad**, para comprobar si se ha quitado el elemento de copia de seguridad.

Ahora puede continuar para eliminar los elementos de copia de seguridad de la consola de administración:
    
   - [Elementos protegidos con MARS](#delete-backup-items-from-mars-management-console)
    - [Elementos protegidos con MABS](#delete-backup-items-from-mabs-management-console)


### <a name="delete-backup-items-from-mars-management-console"></a>Eliminación de elementos de copia de seguridad desde la consola de administración de MARS

Eliminación de elementos de copia de seguridad desde la consola de administración de MARS

- Inicie la consola de administración de MARS, vaya al panel **Acciones** y elija **Programar copia de seguridad**.
- En el asistente **Modificar o detener una copia de seguridad programada**, elija la opción **Dejar de utilizar esta programación de copias de seguridad y eliminar todas las copias de seguridad almacenadas** y haga clic en **Siguiente**.

    ![Modificación o detención de una copia de seguridad programada](./media/backup-azure-delete-vault/modify-schedule-backup.png)

- En el asistente **Detener copia de seguridad programada**, haga clic en **Finalizar**.

    ![Detención de una copia de seguridad programada](./media/backup-azure-delete-vault/stop-schedule-backup.png)
- Se le solicita que escriba un PIN de seguridad. Para generar el PIN, realice los pasos siguientes:
  - Inicie sesión en el Portal de Azure.
  - Vaya al **almacén de Recovery Services** > **Configuración** > **Propiedades**.
  - En **PIN de seguridad**, haga clic en **Generar**. Copie este código PIN. (Solo es válido durante cinco minutos).
- En la consola de administración (aplicación cliente), pegue el código PIN y haga clic en **Aceptar**.

  ![PIN de seguridad](./media/backup-azure-delete-vault/security-pin.png)

- En el asistente **Modificar progreso de la copia de seguridad**, verá que los *datos de copia de seguridad eliminados se conservarán durante catorce días. Tras ese período, se eliminarán permanentemente los datos de copia de seguridad.*  

    ![Eliminación de la infraestructura de copia de seguridad](./media/backup-azure-delete-vault/deleted-backup-data.png)

Ahora que ha eliminado los elementos de copia de seguridad del entorno local, complete los pasos siguientes desde el portal.

### <a name="delete-backup-items-from-mabs-management-console"></a>Eliminación de elementos de copia de seguridad desde la consola de administración de MABS

Eliminación de elementos de copia de seguridad desde la consola de administración de MABS

**Método 1** Para detener la protección y eliminar los datos de copia de seguridad, realice los pasos siguientes:

1.  En la Consola de administrador DPM, haga clic en **Protección** en la barra de navegación.
2.  En el panel de información, seleccione el miembro del grupo de protección que quiere quitar. Haga clic con el botón derecho para elegir la opción **Stop Protection of Group Members** (Detener la protección de miembros del grupo).
3.  Desde el cuadro de diálogo **Detener protección**, seleccione la casilla **Eliminar datos protegidos** > **Eliminar almacenamiento en línea** y, a continuación, haga clic en **Detener protección**.

    ![Eliminación de almacenamiento en línea](./media/backup-azure-delete-vault/delete-storage-online.png)

Ahora se cambia el estado de miembro protegido a **Réplica inactiva disponible**.

4. Haga clic en el grupo de protección inactiva y seleccione **Quitar protección inactiva**.

    ![Quitar protección inactiva](./media/backup-azure-delete-vault/remove-inactive-protection.png)

5. Desde la ventana **Eliminar protección inactiva**, seleccione **Eliminar almacenamiento en línea** y haga clic en **Aceptar**.

    ![Quitar las réplicas en el disco y en línea](./media/backup-azure-delete-vault/remove-replica-on-disk-and-online.png)

**Método 2** Inicie la consola de **administración de MABS**. En la sección **Seleccionar método de protección de datos**, anule la selección de la opción **Deseo protección en línea**.

  ![seleccionar método de protección de datos](./media/backup-azure-delete-vault/data-protection-method.png)

Ahora que ha eliminado los elementos de copia de seguridad del entorno local, complete los pasos siguientes desde el portal.


## <a name="delete-the-recovery-services-vault"></a>Eliminación del almacén de Recovery Services

1. Cuando se hayan quitado todas las dependencias, desplácese al panel **Essentials** en el menú del almacén.
2. Compruebe que no haya **elementos de copia de seguridad**, **servidores de administración de copias de seguridad** ni **elementos replicados** en la lista. Si los elementos siguen apareciendo en el almacén, consulte la sección [Antes de empezar](#before-you-start).

3. Cuando no haya ningún otro elemento en el almacén, haga clic en **Eliminar** en el panel del almacén.

    ![Eliminar datos de copia de seguridad](./media/backup-azure-delete-vault/vault-ready-to-delete.png)

4. Para verificar que desea eliminar el almacén, haga clic en **Sí**. Se elimina el almacén y el portal vuelve al menú **Nuevo servicio** .

## <a name="delete-the-recovery-services-vault-using-azure-resource-manager-client"></a>Eliminación del almacén de Recovery Services mediante el cliente de Azure Resource Manager

Esta opción para eliminar el almacén de Recovery Services solo se recomienda cuando se quitan todas las dependencias y sigue apareciendo el *error de eliminación del almacén*.

- En el panel **Información esencial** del menú del almacén, compruebe que no aparecen **Elementos de copia de seguridad**, **Servidores de administración de copias de seguridad** ni **Elementos replicados**. Si hay elementos de copia de seguridad, consulte la sección [Antes de empezar](#before-you-start).
- Vuelva a intentar [eliminar el almacén desde el portal](#delete-the-recovery-services-vault).
- Si se quitan todas las dependencias y sigue apareciendo el *error de eliminación del almacén*, use la herramienta ARMClient para realizar los pasos indicados a continuación.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Instale Chocolatey desde [aquí](https://chocolatey.org/) y ejecute el comando siguiente para instalar ARMClient:

   `choco install armclient --source=https://chocolatey.org/api/v2/`
2. Inicie sesión en su cuenta de Azure y ejecute este comando:

    `ARMClient.exe login [environment name]`

3. En Azure Portal, recopile el id. de suscripción y el nombre del grupo de recursos del almacén que quiere eliminar.

Para obtener más información sobre el comando de ARMClient, consulte este [documento](https://github.com/projectkudu/ARMClient/blob/master/README.md).

### <a name="use-azure-resource-manager-client-to-delete-recovery-services-vault"></a>Uso del cliente de Azure Resource Manager para eliminar el almacén de Recovery Services

1. Ejecute el comando siguiente con el id. de suscripción, el nombre del grupo de recursos y el nombre de almacén. Al ejecutar el comando, se elimina el almacén, si no tiene ninguna dependencia.

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>?api-version=2015-03-15
   ```
2. Si el almacén no está vacío, aparece el error "No se puede eliminar el almacén, ya que contiene recursos existentes". Para quitar un contenedor o elementos protegidos de un almacén, haga lo siguiente:

   ```azurepowershell
   ARMClient.exe delete /subscriptions/<subscriptionID>/resourceGroups/<resourcegroupname>/providers/Microsoft.RecoveryServices/vaults/<recovery services vault name>/registeredIdentities/<container name>?api-version=2016-06-01
   ```

3. En Azure Portal, compruebe que se ha eliminado el almacén.


## <a name="next-steps"></a>Pasos siguientes

[Más información](backup-azure-recovery-services-vault-overview.md) sobre los almacenes de Recovery Services.<br/>
[Más información](backup-azure-manage-windows-server.md) sobre la supervisión y administración de almacenes de Recovery Services.
