---
title: Migración de máquinas virtuales de Hyper-V locales a Azure con Azure Migrate Server Migration | Microsoft Docs
description: En este artículo se describe cómo migrar máquinas virtuales de Hyper-V a Azure con Azure Migrate Server Migration
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/09/2019
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 4a88e9dddd492d5c24698bcde8c3a1fd942eaf66
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854196"
---
# <a name="migrate-hyper-v-vms-to-azure"></a>Migración de máquinas virtuales de Hyper-V a Azure 

En este artículo se muestra cómo migrar máquinas virtuales de Hyper-V locales a Azure mediante la migración sin agente con la herramienta Azure Migrate Server Migration.

[Azure Migrate](migrate-services-overview.md) proporciona un centro de conectividad para realizar el seguimiento de la detección, evaluación y migración a Azure de las aplicaciones y cargas de trabajo locales, así como de máquinas virtuales en la nube privadas o públicas. El centro proporciona herramientas de Azure Migrate para la evaluación y la migración, así como ofertas de proveedores de software independientes (ISV).

Este tutorial es el tercero de una serie en la que se muestra cómo evaluar y migrar Hyper-V a Azure mediante Azure Migrate Server Assessment y Migration. En este tutorial, aprenderá a:


> [!div class="checklist"]
> * Preparar Azure y el entorno de Hyper-V local
> * Configurar el entorno de origen e implementar un dispositivo de replicación.
> * Configurar el entorno de destino.
> * Habilite la replicación.
> * Ejecutar una migración de prueba para asegurarse de que todo funciona de la forma esperada.
> * Ejecutar una migración completa a Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.


## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar este tutorial, debe:

1. [Revisar](migrate-architecture.md)la arquitectura de migración de Hyper-V.
2. [Complete el primer tutorial](tutorial-prepare-hyper-v.md) de esta serie para configurar Azure e Hyper-V para la migración. En el primer tutorial:
    - [Preparará Azure](tutorial-prepare-hyper-v.md#prepare-azure) para la migración.
    - [Preparará el entorno local](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-migration) para la migración.
3. Se recomienda intentar evaluar las máquinas virtuales de Hyper-V, mediante Azure Migrate Server Assessment, antes de migrarlas a Azure. Para ello, [complete el segundo tutorial](tutorial-assess-hyper-v.md) de esta serie. Aunque se recomienda probar una evaluación, no es necesario ejecutarla antes de migrar las máquinas virtuales.
4. Asegurarse de que la cuenta de Azure tiene asignado el rol Colaborador de máquina virtual, con el fin de que tenga permisos para:

    - Crear una máquina virtual en el grupo de recursos seleccionado.
    - Crear una máquina virtual en la red virtual seleccionada.
    - Escribir en un disco administrado de Azure.   
5. [Configure una red de Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Al realizar una migración a Azure, las máquinas virtuales de Azure creadas se unen a una red de Azure que se especifica al configurar la migración.


## <a name="add-the-azure-migrate-server-migration-tool"></a>Incorporación de la herramienta Azure Migrate Server Migration

Si no ha seguido el segundo tutorial para evaluar las máquinas virtuales de Hyper-V, debe [seguir estas instrucciones](how-to-add-tool-first-time.md) para configurar un proyecto de Azure Migrate y agregar la herramienta Azure Migrate Server Migration a dicho proyecto.

Si siguió el segundo tutorial y ya tiene un proyecto de Azure Migrate configurado, agregue la herramienta Azure Migrate Server Migration de la siguiente manera:

1. En el proyecto de Azure Migrate, haga clic en **Información general**. 
2. En **Detectar, evaluar y migrar servidores**, haga clic en **Evaluar y migrar servidores**.
3. En **Herramientas de migración** , seleccione **Click here to add a migration tool when you are ready to migrate** (Haga clic aquí para agregar una herramienta de migración cuando esté listo para migrar).

    ![Seleccionar una herramienta](./media/tutorial-migrate-hyper-v/select-migration-tool.png)

4. En la lista de herramientas, seleccione **Azure Migrate: Server Migration** > **Agregar herramienta**

    ![Herramienta Server Migration](./media/tutorial-migrate-hyper-v/server-migration-tool.png)


## <a name="set-up-the-azure-migrate-appliance"></a>Configurar el dispositivo de Azure Migrate

Azure Migrate Server Migration ejecuta un dispositivo ligero de máquina virtual de Hyper-V.

- Dicho dispositivo realiza la detección de la máquina virtual y envía metadatos y datos de rendimiento de estas a Azure Migrate Server Migration.
- La herramienta Azure Migrate Server Assessment también usa el mismo dispositivo.

Para configurar el dispositivo:
- Si ha seguido el segundo tutorial para evaluar máquinas virtuales de Hyper-V, en él configuró el dispositivo.
- Si no ha seguido dicho tutorial, debe configurar el dispositivo ahora. Para ello, puede: 

    - Descargar un disco duro virtual de Hyper-V comprimido desde Azure Portal.
    - Cree el dispositivo y compruebe que se puede conectar a Azure Migrate Server Assessment. 
    - Configurar el dispositivo por primera vez y registrarlo en el proyecto de Azure Migrate.

    Siga las instrucciones de [este artículo](how-to-set-up-appliance-hyper-v.md) para configurar el dispositivo.

## <a name="prepare-hyper-v-hosts"></a>Preparar los hosts de Hyper-V

1. En el proyecto de Azure Migrate > **Servidores**, en **Azure Migrate: Migración del servidor**, haga clic en **Detectar**.
2. En **Detectar máquinas** >  **¿Las máquinas están virtualizadas?** , seleccione **Sí, con Hyper-V**.
3. En **Región de destino**, seleccione la región de Azure a la que desea migrar las máquinas.
6. Seleccione **Confirme que la región de destino de la migración es nombreDeRegión**.
7. Haga clic en **Crear recursos**. Esto crea un almacén de Azure Site Recovery en segundo plano.
    - Si ya ha configurado la migración con Azure Migrate Server Migration, esta opción no aparecerá, ya que los recursos se configuraron anteriormente.
    - Después de hacer clic en este botón ya no se puede cambiar la región de destino de este proyecto.
    - Todas las migraciones posteriores se realizan a esta región.
    
8. En **Preparar los servidores host de Hyper-V**, descargue el proveedor de replicación de Hyper-V y el archivo de la clave de registro.
    - La clave de registro es necesaria para registrar el host de Hyper-V en Azure Migrate Server Migration.
    - La clave será válida durante cinco días a partir del momento en que se genera.

    ![Descargar el proveedor y la clave](./media/tutorial-migrate-hyper-v/download-provider-hyper-v.png)

4. Copie el archivo de instalación del proveedor y el archivo de la clave de registro en todos los hosts de Hyper-V (o nodos de clúster) que ejecuten las máquinas virtuales que desea replicar.
5. Ejecute el archivo de instalación del proveedor en cada host, como se describe en el procedimiento siguiente.
6. Después de instalar el proveedor en los hosts, en **Detectar máquinas**, haga clic en **Finalizar registro**.

    ![Finalizar el registro](./media/tutorial-migrate-hyper-v/finalize-registration.png)

Tras la finalización del registro, pueden pasar 15 minutos hasta que las máquinas virtuales detectadas aparecen en Azure Migrate Server Migration. A medida que se detectan las máquinas virtuales, aumenta el número de **Servidores detectados**.

![Servidores detectados](./media/tutorial-migrate-hyper-v/discovered-servers.png)

### <a name="register-hyper-v-hosts"></a>Registro de hosts de Hyper-V

Instale el archivo de instalación descargado (AzureSiteRecoveryProvider.exe) en todos los host de Hyper-V relevantes.

1. Ejecute el archivo de instalación del proveedor en cada host o nodo de clúster.
2. En el Asistente para la instalación del proveedor > **Microsoft Update**, decida usar Microsoft Update para comprobar las actualizaciones del proveedor.
3. En **Instalación**, acepte la ubicación predeterminada de instalación para el proveedor y el agente y haga clic en **Instalar**.
4. Después de la instalación, en el Asistente para registro > **Configuración del almacén**, seleccione **Examinar** y, en **Archivo de clave**, seleccione el archivo de clave del almacén que descargó.
5. En **Configuración de proxy** , especifique cómo se conecta a Internet el proveedor que se ejecuta en el host.
    - Si el dispositivo se encuentra detrás de un servidor proxy, debe especificar la configuración de proxy.
    - Especifique el nombre del proxy como **http://ip-address** o **http://FQDN** . No se admiten servidores proxy HTTPS.
   

6. Asegúrese de que el proveedor puede acceder a las [direcciones URL necesarias](migrate-support-matrix-hyper-v.md#migration-hyper-v-host-url-access).
7. En **Registro**, una vez que el host se haya registrado, haga clic en **Finalizar**.

## <a name="replicate-hyper-v-vms"></a>Replicación de máquinas virtuales de Hyper-V

Una vez finalizada la detección, puede comenzar la replicación de máquinas virtuales de Hyper-V en Azure.

1. En el proyecto de Azure Migrate > **Servidores**, **Azure Migrate: Server Migration**, haga clic en **Replicar**.
2. En **Replicar** > **Configuración de origen** >  **¿Las máquinas están virtualizadas?** , seleccione **Sí, con Hyper-V**. A continuación, haga clic en **Siguiente: Máquinas virtuales**.
3. En **Máquinas virtuales**, seleccione las máquinas que desea replicar.
    - Si ha ejecutado una evaluación para las máquinas virtuales, puede aplicar las recomendaciones de tamaño y tipo de disco (Premium/estándar) de máquina virtual que sugieren los resultados de dicha evaluación. Para ello, en **¿Quiere importar la configuración de migración de evaluación de Azure Migrate?** , seleccione la opción **Sí**.
    - Si no ha ejecutado una evaluación o no desea usar la configuración de evaluación, seleccione la opción **No**.
    - Si ha decidido usar la evaluación, seleccione el grupo de máquinas virtuales y el nombre de la evaluación.

        ![Seleccionar evaluación](./media/tutorial-migrate-hyper-v/select-assessment.png)

4. En **Máquinas virtuales**, busque las máquinas virtuales que necesite y compruebe todas las que desee migrar. Después, haga clic en **Siguiente: Configuración de destino**.

    ![Selección de las máquinas virtuales](./media/tutorial-migrate-hyper-v/select-vms.png)

5. En **Configuración de destino**, seleccione la región de destino a la que va a realizar la migración, la suscripción y el grupo de recursos en el que residirán las máquinas virtuales de Azure después de la migración.
7. En **Cuenta de almacenamiento de replicación**, seleccione la cuenta de almacenamiento de Azure en la que se almacenarán los datos replicados en Azure.
8. **Red virtual**, seleccione la red virtual o la subred de Azure a la que se unirán las máquinas virtuales de Azure después de la migración.
9. En **Ventaja híbrida de Azure**:

    - Seleccione **No** si no desea aplicar Ventaja híbrida de Azure. A continuación, haga clic en **Siguiente**.
    - Seleccione **Sí** si tiene equipos con Windows Server que están incluidos en suscripciones activas de Software Assurance o Windows Server y desea aplicar el beneficio a las máquinas que va a migrar. A continuación, haga clic en **Siguiente**.

    ![Configuración de destino](./media/tutorial-migrate-hyper-v/target-settings.png)

10. En **Proceso**, revise el nombre de la máquina virtual, su tamaño, el tipo de disco del sistema operativo y el conjunto de disponibilidad. Las máquinas virtuales deben cumplir los [requisitos de Azure](migrate-support-matrix-vmware.md#agentless-migration-vmware-vm-requirements).

    - **Tamaño de VM**: si usa las recomendaciones de la evaluación, el menú desplegable de tamaño de VM contendrá el tamaño recomendado. De lo contrario, Azure Migrate elige un tamaño en función de la coincidencia más cercana en la suscripción de Azure. También puede elegir un tamaño de manera manual en **Tamaño de la máquina virtual de Azure**. 
    - **Disco del sistema operativo**: especifique el disco del sistema operativo (arranque) de la máquina virtual. Este es el disco que tiene el cargador de arranque y el instalador del sistema operativo. 
    - **Conjunto de disponibilidad**: si la máquina virtual debe estar incluida en un conjunto de disponibilidad de Azure después de la migración, especifique el conjunto. El conjunto debe estar en el grupo de recursos de destino que especifique para la migración.

    ![Configuración del proceso de la máquina virtual](./media/tutorial-migrate-hyper-v/compute-settings.png)

11. En **Discos**, especifique si los discos de la máquina virtual se deben replicar en Azure y seleccione el tipo de disco (discos SSD o HDD estándar, o bien discos administrados Premium) en Azure. A continuación, haga clic en **Siguiente**.
    - Puede excluir discos de la replicación.
    - Si excluye discos, no estarán presentes en la máquina virtual de Azure después de la migración. 

    ![Discos](./media/tutorial-migrate-hyper-v/disks.png)

10. En **Review and start replication** (Revisar e iniciar la replicación), revise la configuración y haga clic en **Replicate** (Replicar) para iniciar la replicación inicial de los servidores.

> [!NOTE]
> La configuración de replicación se puede actualizar en cualquier momento antes de que esta comience; para ello, vaya a **Administrar** > **Replicación de máquinas**. Una vez iniciada la replicación, su configuración no se puede cambiar.

### <a name="provisioning-for-the-first-time"></a>Primer aprovisionamiento

Si se trata de la primera máquina virtual que va a replicar en el proyecto de Azure Migrate, Azure Migrate Server Migration aprovisiona automáticamente estos recursos en el mismo grupo de recursos que el proyecto.

- **Service Bus**: Azure Migrate Server Migration usa Service Bus para enviar mensajes de orquestación de replicación al dispositivo.
- **Cuenta de almacenamiento de puerta de enlace**: Server Migration usa la cuenta de almacenamiento de puerta de enlace para almacenar información del estado de las máquinas virtuales que se replican.
- **Cuenta de almacenamiento de registros**: El dispositivo con Azure Migrate carga los registros de replicación de las máquinas virtuales en una cuenta de almacenamiento de registros. Azure Migrate aplica la información de replicación a los discos administrados de réplica.
- **Almacén de claves**: El dispositivo con Azure Migrate usa el almacén de claves para administrar las cadenas de conexión de Service Bus y las claves de acceso de las cuentas de almacenamiento utilizadas en la replicación. Debe haber configurado los permisos que necesita el almacén de claves para acceder a la cuenta de almacenamiento cuando esté preparado. [Revise dichos permisos](tutorial-prepare-vmware.md#assign-role-assignment-permissions).   


## <a name="track-and-monitor"></a>Seguimiento y supervisión


- Al hacer clic en **Replicar**, comienza el trabajo de inicio de replicación. 
- Cuando el trabajo de inicio de replicación finaliza correctamente, las máquinas comienzan su replicación inicial en Azure.
- Cuando finaliza esta replicación inicial, comienza la replicación diferencial. Los cambios incrementales de los discos locales se replican periódicamente en Azure.

Puede realizar un seguimiento del estado del trabajo en las notificaciones del portal.

Para supervisar el estado de la replicación, haga clic en **Replicando servidores** en **Azure Migrate: Server Migration**.
![Supervisión de la replicación](./media/tutorial-migrate-hyper-v/replicating-servers.png)



## <a name="run-a-test-migration"></a>Ejecutar una migración de prueba


Cuando comienza la replicación diferencial, puede ejecutar una migración de prueba para las máquinas virtuales antes de ejecutar una migración completa a Azure. Le recomendamos encarecidamente que lo haga al menos una vez en cada máquina, antes de migrarla.

- La ejecución de una migración de prueba comprueba que la migración funcionará según lo previsto, sin afectar a las máquinas locales, que seguirán estando operativas y continuarán realizando la replicación. 
- La migración de prueba simula la migración mediante la creación de una máquina virtual de Azure mediante datos replicados (normalmente, con una migración a una red virtual que no es de producción en la suscripción a Azure).
- Puede usar la máquina virtual de Azure de prueba replicada para validar la migración, realizar pruebas de aplicaciones y resolver los problemas antes de la migración completa.

Realice una migración de prueba como se indica a continuación:


1. En **Objetivos de migración** > **Servidores** > **Azure Migrate: Server Migration**, haga clic en **Probar servidores migrados**.

     ![Probar servidores migrados](./media/tutorial-migrate-hyper-v/test-migrated-servers.png)

2. Haga clic con el botón derecho en la máquina virtual que va a probar y haga clic en **Migración de prueba**.

    ![Migración de prueba](./media/tutorial-migrate-hyper-v/test-migrate.png)

3. En **Migración de prueba**, seleccione la red virtual de Azure en la que se ubicará la máquina virtual de Azure después de la migración. Se recomienda usar una red virtual que no sea de producción.
4. Comienza el trabajo de **Migración de prueba**. Supervise el trabajo en las notificaciones del portal.
5. Una vez finalizada la migración, la máquina virtual de Azure migrada se puede ver en **Máquinas virtuales** en Azure Portal. El nombre de la máquina tiene el sufijo **-Test**.
6. Una vez finalizada la prueba, haga clic con el botón derecho en la máquina virtual de Azure, en **Replicación de máquinas**, y haga clic en **Limpiar la migración de prueba**.

    ![Limpiar la migración](./media/tutorial-migrate-hyper-v/clean-up.png)


## <a name="migrate-vms"></a>Migración de máquinas virtuales

Después de comprobar que la migración de prueba funciona según lo previsto, puede migrar las máquinas locales.

1. En el proyecto de Azure Migrate > **Servidores** > **Azure Migrate: Server Migration**, haga clic en **Replicando servidores**.

    ![Replicando servidores](./media/tutorial-migrate-hyper-v/replicate-servers.png)

2. En **Replicación de máquinas**, haga clic con el botón derecho en la máquina virtual > **Migrar**.
3. En **Migrar** >  **¿Quiere apagar las máquinas virtuales y realizar una migración planificada sin perder datos?** , seleccione **Sí** > **Aceptar**.
    - De forma predeterminada, Azure Migrate apaga la máquina virtual local y ejecuta una replicación a petición para sincronizar los cambios que se han producido en la máquina virtual desde la última replicación. De esta forma se garantiza que no se pierden datos.
    - Si no desea apagar la máquina virtual, seleccione **No**
4. Se inicia un trabajo de migración de la máquina virtual. Realice un seguimiento del trabajo en las notificaciones de Azure.
5. Una vez finalizado el trabajo, la máquina virtual puede ver y administrar desde la página **Máquinas virtuales**.

## <a name="complete-the-migration"></a>Completar la migración

1. Una vez finalizada la migración, haga clic con el botón derecho en la máquina virtual > **Detener migración**. Así se detiene la replicación en la máquina local y se limpia la información acerca del estado de replicación de la máquina virtual.
2. Instale el agente de [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) o [Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) de la máquina virtual de Azure en las máquinas migradas.
3. Realice los ajustes de la aplicación posteriores a la migración, como actualizar las cadenas de conexión de la base de datos y las configuraciones del servidor web.
4. Realice las pruebas finales de la aplicación y la aceptación de la migración en la aplicación migrada que ahora se ejecuta en Azure.
5. Pase el tráfico a la instancia de máquina virtual de Azure migrada.
6. Quite las máquinas virtuales locales del inventario de máquinas virtuales local.
7. Quite las máquinas virtuales locales de las copias de seguridad locales.
8. Actualice la documentación interna para mostrar la nueva ubicación y la dirección IP las máquinas virtuales de Azure. 

## <a name="post-migration-best-practices"></a>Procedimientos recomendados después de la migración

- Para aumentar la resistencia:
    - Proteja los datos mediante la copia de seguridad de máquinas virtuales de Azure mediante el servicio Azure Backup. [Más información](../backup/quick-backup-vm-portal.md).
    - Mantenga las cargas de trabajo en ejecución y disponibles continuamente mediante la replicación de máquinas virtuales de Azure en una región secundaria con Site Recovery. [Más información](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- Para aumentar la seguridad:
    - Bloquee y limite el acceso de tráfico de entrada con la [administración Just-In-Time de Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).
    - Restrinja el tráfico de red a los puntos de conexión de administración con [grupos de seguridad de red](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implemente [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para ayudar a proteger discos y datos frente al robo y acceso no autorizado.
    - Obtenga más información sobre la [protección de recursos IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/) y visite [Azure Security Center](https://azure.microsoft.com/services/security-center/).
- Para supervisión y administración:
-  Considere la posibilidad de implementar [Azure Cost Management](https://docs.microsoft.com/azure/cost-management/overview) para supervisar el gasto y el uso de recursos.


## <a name="next-steps"></a>Pasos siguientes

Investigue el [proceso de la migración en la nube](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/migrate) en el marco de Cloud Adoption Framework para Azure.
