---
title: Restauración de datos de Azure en un equipo Windows Server o Windows
description: Obtenga información sobre cómo restaurar los datos almacenados en Azure en un equipo Windows Server o Windows.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 9/7/2018
ms.author: dacurwin
ms.openlocfilehash: c8e0ddb788d5e155bfaa511b1012cc8d8e62aab5
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689107"
---
# <a name="restore-files-to-windows-by-using-the-azure-resource-manager-deployment-model"></a>Restauración de archivos en Windows con el modelo de implementación de Azure Resource Manager

En este artículo se explica cómo restaurar datos a partir de un almacén de copia de seguridad. Para restaurar datos, utilice al Asistente de recuperación de datos del agente de Microsoft Azure Recovery Services (MARS). Puede:

* La restauración de datos en la misma máquina desde la cual se realizaron las copias de seguridad.
* Restaurar datos en una máquina alternativa.

Use la característica de restauración instantánea para montar una instantánea de punto de recuperación grabable como un volumen de recuperación. Después, puede explorar el volumen de recuperación y copiar archivos de recuperación en un equipo local, por tanto, restaurando los archivos de forma selectiva.

> [!NOTE]
> Se necesita la [actualización de enero de 2017 de Azure Backup](https://support.microsoft.com/en-us/help/3216528?preview) si desea utilizar la restauración instantánea para restaurar datos. También se deben proteger los datos de copia de seguridad en los almacenes en configuraciones regionales que aparecen en el artículo de soporte técnico. Consulte la [actualización de Azure Backup de enero de 2017](https://support.microsoft.com/en-us/help/3216528?preview) para obtener la lista más reciente de configuraciones regionales que admitan la restauración instantánea.
>

Use almacenes de Restauración instantánea con Recovery Services en Azure Portal. Si almacenó datos en los almacenes de Backup, se convirtieron en almacenes de Recovery Services. Si desea usar la restauración instantánea, descargue la actualización de MARS y siga los procedimientos que mencionan la restauración instantánea.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="use-instant-restore-to-recover-data-to-the-same-machine"></a>Uso de la restauración instantánea para recuperar datos en la misma máquina

Si ha eliminado accidentalmente un archivo y desea restaurarlo en la misma máquina (desde la que se realizó la copia de seguridad), los pasos siguientes le ayudarán a recuperar esos datos.

1. Abra el complemento **Microsoft Azure Backup**. Si no conoce la ubicación donde se instaló el complemento, busque el equipo o servidor para **Microsoft Azure Backup**.

    La aplicación de escritorio debe aparecer en los resultados de búsqueda.

2. Seleccione **Recuperar datos** para iniciar el asistente.

    ![Captura de pantalla de Azure Backup, con la opción Recuperar datos resaltada](./media/backup-azure-restore-windows-server/recover.png)

3. En la página **Introducción**, para restaurar los datos en el mismo servidor o equipo, seleccione **Este servidor (`<server name>`)**  > **Siguiente**.

    ![Captura de pantalla de la página Introducción del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/samemachine_gettingstarted_instantrestore.png)

4. En la página **Seleccionar modo de recuperación**, seleccione **Archivos y carpetas individuales** > **Siguiente**.

    ![Captura de pantalla de la página Seleccionar modo de recuperación del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/samemachine_selectrecoverymode_instantrestore.png)
   > [!IMPORTANT]
   > La opción de restauración Archivos y carpetas individuales requiere .NET Framework 4.5.2 o posterior. Si no ve la opción **Archivos y carpetas individuales**, debe actualizar .NET Framework a la versión 4.5.2 o posterior e intentarlo de nuevo.

   > [!TIP]
   > La opción **Archivos y carpetas individuales** permite el acceso rápido a los datos de punto de recuperación. Es adecuada para recuperar archivos individuales, con tamaños que no superen los 80 GB en total, y ofrece velocidades de transferencia o copia de hasta 6 MBps durante la recuperación. La opción **Volumen** recupera todos los datos de los que se ha realizado una copia de seguridad en un volumen especificado. Esta opción ofrece mayores velocidades de transferencia (hasta 60 MBps), lo que resulta ideal para la recuperación de volúmenes completos o de datos de gran tamaño.

5. En la página **Seleccionar volumen y fecha**, seleccione el volumen que contiene los archivos y carpetas que desea restaurar.

    En el calendario, seleccione un punto de recuperación. Las fechas en **negrita** indican la disponibilidad de al menos un punto de recuperación. Si están disponibles varios puntos de recuperación dentro de una única fecha, elija el punto de recuperación específico desde el menú desplegable **Hora**.

    ![Captura de pantalla de la página Seleccionar volumen y fecha del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/samemachine_selectvolumedate_instantrestore.png)

6. Después de elegir el punto de recuperación para realizar la restauración, seleccione **Montar**.

    Azure Backup monta el punto de recuperación local y lo usa como volumen de recuperación.

7. En la página **Examinar y recuperar archivos**, seleccione **Examinar** para abrir el Explorador de Windows y buscar los archivos y carpetas que desea.

    ![Captura de pantalla de la página Examinar y recuperar archivos del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/samemachine_browserecover_instantrestore.png)


8. En el Explorador de Windows, copie los archivos y carpetas que desea restaurar y péguelos en cualquier ubicación local en el servidor o equipo. Puede abrir o transmitir los archivos directamente desde el volumen de recuperación y comprobar que está recuperando las versiones correctas.

    ![Captura de pantalla del Explorador de Windows, con la opción Copiar resaltada](./media/backup-azure-restore-windows-server/samemachine_copy_instantrestore.png)


9. Cuando haya terminado, en la página **Examinar y recuperar archivos**, seleccione **Desmontar**. Seleccione **Sí** para confirmar que desea desmontar el volumen.

    ![Captura de pantalla de la página Examinar y recuperar archivos del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/samemachine_unmount_instantrestore.png)

    > [!Important]
    > Si no selecciona **Desmontar**, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. Sin embargo, el tiempo de montaje se puede extender hasta veinticuatro horas en el caso de una copia de archivos en curso. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >


## <a name="use-instant-restore-to-restore-data-to-an-alternate-machine"></a>Uso de restauración instantánea para restaurar datos en una máquina alternativa
Si ha perdido todo el servidor, todavía puede recuperar los datos de Azure Backup en una máquina diferente. Los pasos siguientes muestran el flujo de trabajo.


Estos pasos incluyen la siguiente terminología:

* *Máquina de origen* : es la máquina original desde la que se realizó la copia de seguridad y que no está disponible actualmente.
* *Máquina de destino* : es la máquina en la que se recuperan los datos.
* *Almacén de ejemplo*: el almacén de Recovery Services en el que se registran la máquina de origen y la máquina de destino. <br/>

> [!NOTE]
> Las copias de seguridad no se pueden restaurar en una máquina de destino en la que se ejecuta una versión anterior del sistema operativo. Por ejemplo, una copia de seguridad perteneciente a un equipo con Windows 7 se puede restaurar en un equipo con Windows 7 o posterior. Una copia de seguridad realizada en un equipo con Windows 8 no se puede restaurar en un equipo con Windows 7.
>
>

1. Abra el complemento **Microsoft Azure Backup** en la máquina de destino.

2. Asegúrese de que tanto la máquina de destino como la máquina de origen están registradas en el mismo almacén de Recovery Services.

3. Seleccione **Recuperar datos** para abrir el **Asistente de recuperación de datos**.

    ![Captura de pantalla de Azure Backup, con la opción Recuperar datos resaltada](./media/backup-azure-restore-windows-server/recover.png)

4. En la página **Introducción**, seleccione **Otro servidor**.

    ![Captura de pantalla de la página Introducción del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/alternatemachine_gettingstarted_instantrestore.png)

5. Proporcione el archivo de credenciales de almacén que se corresponde con el almacén de ejemplo y seleccione **Siguiente**.

    Si el archivo de credenciales de almacén no es válido o ha expirado, descargue un nuevo archivo de credenciales de almacén desde el almacén de ejemplo en Azure Portal. Una vez proporcionada una credencial de almacén válida, aparecerá el nombre del almacén de Backup correspondiente.


6. En la página **Seleccionar servidor de copia de seguridad**, seleccione la máquina de origen en la lista de máquinas mostradas y proporcione la frase de contraseña. Luego, seleccione **Siguiente**.

    ![Captura de pantalla de la página Seleccionar servidor de copia de seguridad del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/alternatemachine_selectmachine_instantrestore.png)

7. En la página **Seleccionar modo de recuperación**, seleccione **Archivos y carpetas individuales** > **Siguiente**.

    ![Captura de pantalla de la página Seleccionar modo de recuperación del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/alternatemachine_selectrecoverymode_instantrestore.png)

8. En la página **Seleccionar volumen y fecha**, seleccione el volumen que contiene los archivos y carpetas que desea restaurar.

    En el calendario, seleccione un punto de recuperación. Las fechas en **negrita** indican la disponibilidad de al menos un punto de recuperación. Si están disponibles varios puntos de recuperación dentro de una única fecha, elija el punto de recuperación específico desde el menú desplegable **Hora**.

    ![Captura de pantalla de la página Seleccionar volumen y fecha del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/alternatemachine_selectvolumedate_instantrestore.png)

9. Seleccione **Montar** para montar localmente el punto de recuperación como un volumen de recuperación en la máquina de destino.

10. En la página **Examinar y recuperar archivos**, seleccione **Examinar** para abrir el Explorador de Windows y buscar los archivos y carpetas que desea.

    ![Captura de pantalla de la página Examinar y recuperar archivos del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/alternatemachine_browserecover_instantrestore.png)

11. En el Explorador de Windows, copie los archivos y carpetas desde el volumen de recuperación y péguelos en la ubicación de la máquina de destino. Puede abrir o transmitir los archivos directamente desde el volumen de recuperación y comprobar que se recuperan las versiones correctas.

    ![Captura de pantalla del Explorador de Windows, con la opción Copiar resaltada](./media/backup-azure-restore-windows-server/alternatemachine_copy_instantrestore.png)

12. Cuando haya terminado, en la página **Examinar y recuperar archivos**, seleccione **Desmontar**. Seleccione **Sí** para confirmar que desea desmontar el volumen.

    ![Captura de pantalla de la página Examinar y recuperar archivos del Asistente de recuperación de datos](./media/backup-azure-restore-windows-server/alternatemachine_unmount_instantrestore.png)

    > [!Important]
    > Si no selecciona **Desmontar**, el volumen de recuperación permanecerá montado durante seis horas desde el momento en el que se montó. Sin embargo, el tiempo de montaje se puede extender hasta veinticuatro horas en el caso de una copia de archivos en curso. No se ejecutará ninguna operación de copia de seguridad mientras el volumen esté montado. Cualquier operación de copia de seguridad programada para ejecutarse durante el tiempo en el que el volumen está montado, se ejecutará después de desmontar el volumen de recuperación.
    >

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha recuperado los archivos y las carpetas, puede [administrar las copias de seguridad](backup-azure-manage-windows-server.md).
