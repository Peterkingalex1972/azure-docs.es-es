---
title: Planificación de la capacidad del clúster en Azure HDInsight
description: Se describe cómo especificar la capacidad y el rendimiento de un clúster de HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: bd2284211c2fdc5a346c6ffb113f89fe311a358c
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2019
ms.locfileid: "67786511"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Planeamiento de la capacidad de los clústeres de HDInsight

Antes de implementar un clúster de HDInsight, planee la capacidad del clúster deseada; para ello, determine el rendimiento y la escala necesarios. Este planeamiento ayuda a optimizar la facilidad de uso y los costos. Algunas decisiones sobre la capacidad del clúster no se pueden cambiar después de la implementación. Si cambian los parámetros de rendimiento, un clúster se puede desmantelar y volver a crear sin perder los datos almacenados.

Las preguntas claves para planear la capacidad son:

* ¿En qué región geográfica debe implementar el clúster?
* ¿Cuánto almacenamiento necesita?
* ¿Qué tipo de clúster debe implementar?
* ¿Qué tamaño y tipo de máquina virtual (VM) deben usar los nodos del clúster?
* ¿Cuántos nodos de trabajo debe tener el clúster?

## <a name="choose-an-azure-region"></a>Elección de una región de Azure

La región de Azure determina dónde se aprovisiona físicamente el clúster. Para minimizar la latencia de lecturas y escrituras, el clúster debe estar cerca de los datos.

HDInsight está disponible en muchas regiones de Azure. Para buscar la región más cercana, vea la entrada *HDInsight* de *Análisis* en [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

## <a name="choose-storage-location-and-size"></a>Elección del tamaño y la ubicación de almacenamiento

### <a name="location-of-default-storage"></a>Ubicación del almacenamiento predeterminado

El almacenamiento predeterminado, ya sea una cuenta de Azure Storage o Azure Data Lake Storage, debe estar en la misma ubicación que el clúster. Azure Storage está disponible en todas las ubicaciones. Data Lake Storage Gen1 está disponible en algunas regiones: vea la disponibilidad actual de Data Lake Storage en *Storage* en [Productos disponibles por región](https://azure.microsoft.com/regions/services/).

### <a name="location-of-existing-data"></a>Ubicación de los datos existentes

Si ya tiene una cuenta de almacenamiento o un recurso de Data Lake Storage que contiene los datos y desea usar dicho almacenamiento como el predeterminado para el clúster, entonces debe implementar el clúster en esa misma ubicación.

### <a name="storage-size"></a>Tamaño de almacenamiento

Una vez que un clúster de HDInsight ya está implementado, puede adjuntar cuentas adicionales de Azure Storage o acceder a recursos de Data Lake Storage. Todas las cuentas de almacenamiento deben residir en la misma ubicación que el clúster. Un almacén de Data Lake Storage puede estar en una ubicación distinta, aunque esto puede ocasionar algo de latencia de lectura y escritura de datos.

Azure Storage tiene algunos [límites de capacidad](../azure-subscription-service-limits.md#storage-limits), mientras que Data Lake Storage Gen1 tiene una capacidad prácticamente ilimitada.

Un clúster puede acceder a una combinación de distintas cuentas de almacenamiento. Estos son ejemplos típicos:

* Cuando es probable que la cantidad de datos exceda la capacidad de almacenamiento de un único contenedor de Blob Storage.
* Cuando la velocidad de acceso al contenedor de blobs puede superar el umbral de limitación.
* Cuando desee que los datos que ya están cargados en un contenedor de blobs estén disponibles en el clúster.
* Si desea aislar diferentes partes del almacenamiento por motivos de seguridad o para simplificar la administración.

En un clúster de cuarenta y ocho nodos, se recomiendan entre cuatro y ocho cuentas de almacenamiento. Aunque puede que ya haya suficiente espacio de almacenamiento total, cada cuenta de almacenamiento proporciona ancho de banda de red adicional para los nodos de ejecución. Si tiene varias cuentas de almacenamiento, utilice un nombre aleatorio para cada cuenta de almacenamiento, sin prefijo. El propósito de la nomenclatura aleatoria es reducir la posibilidad de que se produzcan cuellos de botella de almacenamiento (limitación) o errores comunes en todas las cuentas. Para mejorar el rendimiento, utilice un único contenedor por cada cuenta de almacenamiento.

## <a name="choose-a-cluster-type"></a>Elección de un tipo de clúster

El tipo de clúster determina la carga de trabajo configurada para que el clúster de HDInsight la ejecute, como [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/) o [Apache Spark](https://spark.apache.org/). Para obtener una descripción detallada de los tipos de clúster disponibles, consulte [Introducción a Azure HDInsight](hadoop/apache-hadoop-introduction.md#cluster-types-in-hdinsight). Cada tipo de clúster tiene una topología de implementación específica que incluye los requisitos del tamaño y el número de nodos.

## <a name="choose-the-vm-size-and-type"></a>Elección del tipo y el tamaño de la máquina virtual

Cada tipo de clúster tiene un conjunto de tipos de nodos, y cada uno de ellos incluye opciones específicas para el tamaño y el tipo de máquina virtual.

Para determinar el tamaño de clúster óptimo de la aplicación, puede realizar pruebas comparativas de la capacidad del clúster y aumentar el tamaño como se indica. Por ejemplo, puede usar una carga de trabajo simulada o una *consulta de valor controlado*. Con una carga de trabajo simulada, ejecute las cargas de trabajo esperadas en clústeres de diferente tamaño, aumentando gradualmente el tamaño hasta alcanzar el rendimiento deseado. Una consulta de valor controlado puede insertarse periódicamente entre las demás consultas de producción para mostrar si el clúster tiene suficientes recursos o no.

El tamaño y el tipo de máquina virtual los determinan la capacidad de procesamiento de la CPU, el tamaño de RAM y la latencia de red:

* CPU: el tamaño de la máquina virtual determina el número de núcleos. Mientras más núcleos haya, mayor será el grado de cómputo en paralelo que cada nodo puede conseguir. Además, algunos tipos de VM tiene núcleos más rápidos.

* RAM: el tamaño de la máquina virtual también determina la cantidad de RAM disponible en la máquina virtual. Para las cargas de trabajo que almacenan los datos en memoria para el procesamiento, en lugar de leer desde el disco, asegúrese de que los nodos de trabajo tienen suficiente memoria para almacenar los datos.

* Red: para la mayoría de los tipos de clúster, los datos procesados por el clúster no están en el disco local, sino en un servicio de almacenamiento externo como Data Lake Storage o Azure Storage. Tenga en cuenta el ancho de banda de red y el rendimiento entre la máquina virtual del nodo y el servicio de almacenamiento. El ancho de banda de red disponible en una máquina virtual suele aumentar con tamaños más grandes. Para obtener información detallada, vea la [información general sobre los tamaños de las máquinas virtuales](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Elección de la escala del clúster

La cantidad de nodos de una máquina virtual determina la escala de un clúster. Para todos los tipos de clúster, hay tipos de nodos que tienen una escala específica y otros que admiten el escalado horizontal. Por ejemplo, un clúster puede requerir exactamente tres nodos de [Apache ZooKeeper](https://zookeeper.apache.org/) o dos nodos principales. Los nodos de trabajo que procesan datos de forma distribuida pueden beneficiarse del escalado horizontal mediante la adición de nodos de trabajo adicionales.

Dependiendo del tipo de clúster, aumentar el número de nodos de trabajo agrega capacidad de cálculo adicional (por ejemplo, más núcleos), pero también puede agregar la cantidad total de memoria necesaria para que todo el clúster admita el almacenamiento en memoria de los datos procesados. Al igual que sucede con la elección del tamaño y el tipo de máquina virtual, la selección de la escala correcta del clúster suele realizarse de forma empírica con la utilización de cargas de trabajo simuladas o consultas de valores controlados.

Puede escalar horizontalmente el clúster para satisfacer los picos de demandas de carga y, después, volver a reducir verticalmente cuando dichos nodos adicionales ya no sean necesarios. La [característica de escalabilidad automática](hdinsight-autoscale-clusters.md) le permite escalar automáticamente el clúster en función de las métricas y los intervalos predeterminados. Para obtener más información sobre cómo escalar los clústeres de forma manual, consulte [Escalabilidad de clústeres de HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Ciclo de vida del clúster

Se le cobra en función de la duración del clúster. Si solo necesita que el clúster funcione en momentos específicos, puede [crear clústeres a petición mediante Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). También puede crear scripts de PowerShell que aprovisionen y eliminen el clúster y después programar dichos scripts con [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Cuando se elimina un clúster, su instancia de Hive Metastore predeterminada también se elimina. Para conservar Metastore para volver a crear el siguiente clúster, use un repositorio de metadatos externo, como Azure Database u [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Aislamiento de errores de trabajo del clúster

A veces, los errores pueden producirse debido a la ejecución en paralelo de varios componentes de asignación y reducción en un clúster de varios nodos. Para facilitar el aislamiento del problema, intente realizar pruebas distribuidas mediante la ejecución de varios trabajos simultáneos en un clúster de un solo nodo y luego expanda este enfoque para ejecutar varios trabajos simultáneamente en clústeres que contienen más de un nodo. Para crear un clúster de HDInsight de un solo nodo en Azure, use la opción *avanzada*.

También puede instalar un entorno de desarrollo de un solo nodo en el equipo local y probar ahí la solución. Hortonworks proporciona un entorno de desarrollo local de un solo nodo para soluciones basadas en Hadoop que resulta útil para el desarrollo inicial, la prueba de concepto y las pruebas. Para más información, vea [Hortonworks Sandbox](https://hortonworks.com/products/hortonworks-sandbox/).

Para identificar el problema en un clúster local de un solo nodo, puede volver a ejecutar los trabajos con errores y ajustar los datos de entrada, o bien usar conjuntos de datos más pequeños. La forma de ejecutar tales trabajos depende de la plataforma y del tipo de aplicación.

## <a name="quotas"></a>Cuotas

Después de determinar el tamaño, la escala y el tipo de la máquina virtual del clúster de destino, compruebe los límites de capacidad de cuota actual de la suscripción. Cuando alcance un límite de cuota, puede que no sea capaz de implementar clústeres nuevos o de escalar horizontalmente los clústeres con la adición de más nodos de trabajo. El límite de cuota única es la cuota de núcleos de CPU que existe en el nivel de región para cada suscripción. Por ejemplo, la suscripción puede tener el límite de 30 núcleos en la región Este de EE. UU. Si fuera necesario solicitar un aumento de la cuota, siga estos pasos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
1. Seleccione **Ayuda y soporte técnico** de la parte inferior izquierda de la página.
1. Seleccione **Nueva solicitud de soporte técnico**.
1. En la página **Nueva solicitud de soporte técnico**, en la pestaña **Fundamentos**, seleccione las opciones siguientes:
   - **Tipo de problema**: **Límites de servicio y suscripción (cuotas)**
   - **Suscripción**: la suscripción que desea modificar.
   - **Tipo de cuota**: **HDInsight**
    
     ![Creación de una solicitud de soporte técnico para aumentar la cuota de núcleos de HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Seleccione **Siguiente: Soluciones >>** .
1. En la página **Detalles**, escriba una descripción del problema, seleccione su gravedad y método de contacto preferido, y complete los demás campos obligatorios.
1. Seleccione **Siguiente: Revisar y crear >>** .
1. En la pestaña **Revisar y crear**, seleccione **Crear**.

> [!NOTE]  
> Si necesita aumentar la cuota de núcleos de HDInsight en una región privada, [envíe una solicitud de lista de permitidos](https://aka.ms/canaryintwhitelist).

Puede [ponerse en contacto con el servicio de soporte técnico para solicitar un aumento de la cuota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Sin embargo, hay algunos límites de cuota fijos; por ejemplo, una única suscripción de Azure puede tener como máximo diez mil núcleos. Para obtener información detallada sobre estos límites, vea [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de clústeres en HDInsight con Hadoop, Spark, Kafka, etc.](hdinsight-hadoop-provision-linux-clusters.md): Aprenda a instalar y configurar clústeres en HDInsight con Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services o Storm.
* [Supervisión del rendimiento del clúster](hdinsight-key-scenarios-to-monitor.md): obtenga información sobre los escenarios claves para supervisar el clúster de HDInsight que podría afectar a la capacidad del clúster.
