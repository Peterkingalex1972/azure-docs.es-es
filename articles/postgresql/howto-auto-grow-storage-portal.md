---
title: 'Aumento automático del almacenamiento con Azure Portal en Azure Database for PostgreSQL: servidor único'
description: 'En este artículo se describe cómo habilitar el aumento automático de almacenamiento con Azure Portal en Azure Database for PostegreSQL: servidor único'
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/29/2019
ms.openlocfilehash: 9f88fe3e8a30dd80c5331bd6c8ea7aec401c6fb9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676764"
---
# <a name="auto-grow-storage-using-the-azure-portal-in-azure-database-for-postgresql---single-server"></a>Aumento automático del almacenamiento con Azure Portal en Azure Database for PostgreSQL: servidor único
En este artículo se describe cómo configurar el almacenamiento en el servidor Azure Database for PostgreSQL para que aumente sin que ello afecte a la carga de trabajo.

Cuando un servidor alcanza el límite de almacenamiento asignado, se marca como de solo lectura. Sin embargo, si habilita el aumento automático de almacenamiento en el servidor, acomodará los datos que se vayan acumulando. Para servidores con menos de 100 GB de almacenamiento aprovisionado, el tamaño del almacenamiento aprovisionado se incrementa en 5 GB tan pronto como el almacenamiento disponible se encuentre por debajo de 1 GB o el 10 % del almacenamiento aprovisionado. En cuanto a servidores con más de 100 GB de almacenamiento aprovisionado, el tamaño del almacenamiento aprovisionado se incrementa en un 5 % cuando el espacio de almacenamiento disponible es inferior al 5 % del tamaño de almacenamiento aprovisionado. Se aplican los límites máximos de almacenamiento según lo especificado [aquí](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#storage).

## <a name="prerequisites"></a>Requisitos previos
Para completar esta guía, necesita:
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md).

## <a name="enable-storage-auto-grow"></a>Habilitación del aumento automático de almacenamiento 

Siga estos pasos para establecer el aumento automático de almacenamiento en el servidor de PostgreSQL:

1. En [Azure Portal](https://portal.azure.com/), seleccione el servidor de Azure Database for PostgreSQL.

2. En la página del servidor de PostgreSQL, en **Configuración**, haga clic en **Plan de tarifa** para abrir la página de planes de tarifa.

3. En la sección de **aumento automático**, seleccione **Sí** para habilitarlo para el almacenamiento.

    ![Azure Database for PostgreSQL: Settings_Pricing_tier: aumento automático](./media/howto-auto-grow-storage-portal/3-auto-grow.png)

4. Haga clic en **Aceptar** para guardar los cambios.

5. Se enviara una notificación de confirmación de la habilitación del aumento automático.

    ![Azure Database for PostgreSQL: aumento automático correcto](./media/howto-auto-grow-storage-portal/5-auto-grow-successful.png)

## <a name="next-steps"></a>Pasos siguientes

Aprenda sobre la [creación de alertas de métricas](howto-alert-on-metric.md).
