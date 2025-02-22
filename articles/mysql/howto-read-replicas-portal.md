---
title: Creación y administración de réplicas de lectura en Azure Database for MySQL
description: En este artículo se describe cómo configurar y administrar réplicas de lectura en Azure Database for MySQL mediante Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: b422718a1eaec483acdc2c8ab37442b9aea78aaa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65510780"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Procedimiento para crear y administrar réplicas de lectura en Azure Database for MySQL mediante Azure Portal

En este artículo, obtendrá información sobre cómo crear y administrar las réplicas de lectura en el servicio Azure Database for MySQL mediante Azure Portal.

> [!IMPORTANT]
> Puede crear una réplica de lectura en la misma región que el servidor maestro o en cualquier otra región de Azure que prefiera. La replicación entre regiones se encuentra actualmente en versión preliminar pública.

## <a name="prerequisites"></a>Requisitos previos

- Un [servidor de Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md) que se usará como servidor maestro.

> [!IMPORTANT]
> La característica de réplica de lectura solo está disponible para servidores de Azure Database for MySQL en los planes de tarifa De uso general u Optimizada para memoria. Asegúrese de que el servidor maestro está en uno de estos planes de tarifa.

## <a name="create-a-read-replica"></a>Creación de una réplica de lectura

Para crear un servidor de réplica de lectura, puede seguir estos siguientes pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

2. Seleccione el servidor de Azure Database for MySQL existente que desea utilizar como servidor maestro. Esta acción abre la página **Información general**.

3. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.

4. Seleccione **Agregar réplica**.

   ![Azure Database for MySQL: Replicación](./media/howto-read-replica-portal/add-replica.png)

5. Escriba un nombre para el servidor de réplica.

    ![Azure Database for MySQL: nombre de réplica](./media/howto-read-replica-portal/replica-name.png)

6. Seleccione la ubicación del servidor de réplica. Puede crear una réplica en cualquier región de Azure. La ubicación predeterminada es la misma que la del servidor maestro.

    ![Azure Database for MySQL: ubicación de la réplica](./media/howto-read-replica-portal/replica-location.png)

7. Seleccione **Aceptar** para confirmar la creación de la réplica.

> [!NOTE]
> Las réplicas de lectura se crean con la misma configuración de servidor que el servidor maestro. Una vez creado, se puede cambiar la configuración del servidor de réplica. Se recomienda mantener la configuración del servidor de réplica con valores iguales o mayores que el maestro para asegurarse de que la réplica trabajar al mismo nivel que el servidor maestro.

Una vez creado el servidor de réplica, puede verlo en la hoja **Replicación**.

   ![Azure Database for MySQL: Lista de réplicas](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Detención de la replicación en un servidor de réplica

> [!IMPORTANT]
> La detención la replicación en un servidor es irreversible. Una vez que detenida, la replicación entre un servidor maestro y una réplica no se puede deshacer. Después, el servidor de réplica se convierte en un servidor independiente que admite operaciones de lectura y escritura. Este servidor no puede volver a convertirse en una réplica.

Para detener la replicación entre un servidor maestro y un servidor de réplicas desde Azure Portal, siga estos pasos:

1. En Azure Portal, seleccione el servidor maestro de Azure Database for MySQL. 

2. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.

3. Seleccione el servidor de réplica para el que desea detener la replicación.

   ![Azure Database for MySQL: Seleccionar el servidor para detener la replicación](./media/howto-read-replica-portal/stop-replication-select.png)

4. Seleccione **Detener replicación**.

   ![Azure Database for MySQL: Detener replicación](./media/howto-read-replica-portal/stop-replication.png)

5. Para confirmar que desea detener la replicación, haga clic en **Aceptar**.

   ![Azure Database for MySQL: Confirmar la detención de la replicación](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Eliminación de un servidor de réplica

Para eliminar un servidor de réplica de lectura en Azure Portal, siga estos pasos:

1. En Azure Portal, seleccione el servidor maestro de Azure Database for MySQL.

2. Seleccione **Replicación** en el menú, en **CONFIGURACIÓN**.

3. Seleccione el servidor de réplica que desea eliminar.

   ![Azure Database for MySQL: Seleccionar el servidor para eliminar las réplicas](./media/howto-read-replica-portal/delete-replica-select.png)

4. Seleccione **Eliminar réplica**.

   ![Azure Database for MySQL: Eliminar réplica](./media/howto-read-replica-portal/delete-replica.png)

5. Escriba el nombre de la réplica y haga clic en **Eliminar** para confirmar la eliminación.  

   ![Azure Database for MySQL: Confirmar la eliminación de la réplica](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Eliminación de un servidor maestro

> [!IMPORTANT]
> Al eliminar un servidor maestro, se detiene la replicación en todos los servidores de réplica y se elimina el propio servidor maestro. Los servidores de réplica se convierten en servidores independientes que ahora admiten tanto lectura como escritura.

Para eliminar un servidor maestro en Azure Portal, siga estos pasos:

1. En Azure Portal, seleccione el servidor maestro de Azure Database for MySQL.

2. En la página **Información general**, seleccione **Eliminar**.

   ![Azure Database for MySQL: Eliminar servidor maestro](./media/howto-read-replica-portal/delete-master-overview.png)

3. Escriba el nombre del servidor maestro y haga clic en **Eliminar** para confirmar la eliminación.  

   ![Azure Database for MySQL: Eliminar servidor maestro](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Supervisión de la replicación

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de réplica de Azure Database for MySQL que desea supervisar.

2. En la sección **Supervisión** de la barra lateral, seleccione **Métricas**:

3. Seleccione **Intervalo de replicación en segundos** en la lista desplegable de métricas disponibles.

   ![Seleccionar el intervalo de replicación](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Seleccione el intervalo de tiempo que desea ver. En la imagen siguiente se ha seleccionado un intervalo de tiempo de 30 minutos.

   ![Seleccionar intervalo de tiempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Ver el intervalo de replicación para el intervalo de tiempo seleccionado. En la imagen siguiente se muestran los últimos 30 minutos.

   ![Seleccionar intervalo de tiempo](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre [las réplicas de lectura](concepts-read-replicas.md)