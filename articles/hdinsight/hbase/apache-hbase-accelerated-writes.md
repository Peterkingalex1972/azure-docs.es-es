---
title: Escrituras aceleradas de Azure HDInsight para Apache HBase
description: Se proporciona una introducción de la característica Escrituras aceleradas de Azure HDInsight, que usa discos administrados premium para mejorar el rendimiento del registro de escritura previa de Apache HBase.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 8b24c7517402aa6f29c95c0cd0f58bb1d51e1082
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876467"
---
# <a name="azure-hdinsight-accelerated-writes-for-apache-hbase"></a>Escrituras aceleradas de Azure HDInsight para Apache HBase

En este artículo se proporciona una base sobre la característica **Escrituras aceleradas** para Apache HBase en Azure HDInsight y cómo puede usarse de forma eficaz para mejorar el rendimiento de escritura. **Escrituras aceleradas** usa [discos administrados SSD premium de Azure](../../virtual-machines/linux/disks-types.md#premium-ssd) para mejorar el rendimiento del registro de escritura previa (WAL) de Apache HBase. Para obtener más información acerca de Apache HBase, consulte [Qué es Apache HBase en HDInsight](apache-hbase-overview.md).

## <a name="overview-of-hbase-architecture"></a>Introducción a la arquitectura de HBase

En HBase, una **fila** consta de una o varias **columnas** y se identifica mediante una **clave de fila**. Varias filas constituyen una **tabla**. Las columnas contienen **celdas**, que son versiones con marca de tiempo del valor de esa columna. Las columnas se agrupan en **familias de columnas**, y todas las columnas de una familia de columnas se almacenan juntas en archivos de almacenamiento denominados **HFiles**.

Las **regiones** en HBase se usan para equilibrar la carga de procesamiento de datos. En primer lugar, HBase almacena las filas de una tabla en una única región. Las filas se distribuyen entre varias regiones a medida que aumenta la cantidad de datos en la tabla. Los **servidores regionales** pueden controlar las solicitudes de varias regiones.

## <a name="write-ahead-log-for-apache-hbase"></a>Registro de escritura previa para Apache HBase

HBase primero escribe las actualizaciones de datos en un tipo de registro de confirmación denominado registro de escritura previa (WAL). Una vez que la actualización se ha almacenado en el WAL, se escribe en la memoria **MemStore**. Cuando los datos en la memoria alcanzan su capacidad máxima, se escriben en el disco como un **HFile**.

Si un **RegionServer** se bloquea o deja de estar disponible antes de que se vacíe la MemStore, se puede usar el registro de escritura previa para reproducir las actualizaciones. Sin el WAL, si un **RegionServer** se bloquea antes de que las actualizaciones se vacíen en un **HFile**, se pierden todas esas actualizaciones.

## <a name="accelerated-writes-feature-in-azure-hdinsight-for-apache-hbase"></a>Característica Escrituras aceleradas de Azure HDInsight para Apache HBase

La característica Escrituras aceleradas soluciona el problema de mayores latencias de escritura debidas al uso de registros de escritura previa que se encuentran en el almacenamiento en la nube.  La característica Escrituras aceleradas para clústeres de HDInsight Apache HBase adjunta discos SSD administrados a cada RegionServer (nodo de trabajo). En consecuencia, los registros de escritura previa se escriben en el sistema de archivos Hadoop (HDFS) montado en estos discos administrados premium en lugar de escribirse en el almacenamiento en la nube.  Los discos administrados Premium usan discos de estado sólido (SSD) y ofrecen un rendimiento de E/S excelente con tolerancia a errores.  A diferencia de los discos no administrados, si una unidad de almacenamiento se bloquea, eso no afectará a otras unidades de almacenamiento del mismo conjunto de disponibilidad.  Como resultado, los discos administrados proporcionan una baja latencia de escritura y una mejor resistencia para las aplicaciones. Para obtener más información sobre los discos administrados por Azure, consulte [Introducción a los discos administrados de Azure](../../virtual-machines/windows/managed-disks-overview.md). 

## <a name="how-to-enable-accelerated-writes-for-hbase-in-hdinsight"></a>Cómo habilitar Escrituras aceleradas para HBase en HDInsight

Para crear un nuevo clúster de HBase con la característica Escrituras aceleradas, siga los pasos de [Configuración de clústeres en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md) hasta llegar al **Paso 3, almacenamiento**. En **Configuración de Metastore**, haga clic en la casilla junto a **Habilitar Escrituras aceleradas (versión preliminar)** . A continuación, continúe con los pasos restantes para crear un clúster.

![Habilitar la opción de escrituras aceleradas para Apache HBase de HDInsight](./media/apache-hbase-accelerated-writes/accelerated-writes-cluster-creation.png)

## <a name="other-considerations"></a>Otras consideraciones

Para conservar la durabilidad de los datos, cree un clúster con un mínimo de tres nodos de trabajo. Una vez creado, el clúster no se puede reducir verticalmente a menos de tres nodos de trabajo.

Vacíe o deshabilite las tablas de HBase antes de eliminar el clúster, de modo que no pierda los datos del registro de escritura previa.

```
flush 'mytable'
```

```
disable 'mytable'
```

## <a name="next-steps"></a>Pasos siguientes

* Documentación oficial de Apache HBase sobre la [característica Registro de escritura previa](https://hbase.apache.org/book.html#wal)
* Para actualizar el clúster de Apache HBase de HDInsight para usar Escrituras aceleradas, consulte [Migrar un clúster de Apache HBase a una nueva versión](apache-hbase-migrate-new-version.md).
