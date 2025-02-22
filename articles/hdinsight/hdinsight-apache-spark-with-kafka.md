---
title: Streaming de Apache Spark con Apache Kafka en Azure HDInsight
description: Aprenda a usar Apache Spark para trasmitir datos dentro y fuera de Apache Kafka mediante DStreams. En este ejemplo, se transmiten datos con Jupyter Notebook de Spark en HDInsight.
keywords: ejemplo de kafka, kafka zookeeper, spark streaming kafka, ejemplo de spark streaming kafka
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: e0c39ae5f5c23ae0715ef1eee38b6dd34704538a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64690963"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Ejemplo de streaming de Apache Spark (DStream) con Apache Kafka en HDInsight

Aprenda a usar [Apache Spark](https://spark.apache.org/) para transmitir datos dentro y fuera de [Apache Kafka](https://kafka.apache.org/) en HDInsight mediante [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). En este ejemplo se usa una instancia de [Jupyter Notebook](https://jupyter.org/) que se ejecuta en el clúster de Spark.

> [!NOTE]  
> Los pasos que se describen en este documento crean un grupo de recursos de Azure que contiene un clúster Spark de HDInsight y un clúster Kafka de HDInsight. Estos dos clústeres se encuentran en una instancia de Azure Virtual Network, lo que permite al clúster Spark comunicarse directamente con el clúster Kafka.
>
> Cuando haya terminado los pasos indicados en este documento, no olvide eliminar los clústeres para evitar gastos innecesarios.

> [!IMPORTANT]  
> En este ejemplo se utiliza DStreams, que es una tecnología de streaming de Spark anterior. Para ver un ejemplo en el que se utilizan las características de streaming de Spark más recientes, consulte el documento [Flujo estructurado de Apache Spark con Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md).

## <a name="create-the-clusters"></a>Creación de los clústeres

Apache Kafka en HDInsight no proporciona acceso a los agentes de Kafka a través de Internet. Cualquier comunicación con Kafka debe realizarse en la misma red virtual de Azure que utilizan los nodos del clúster Kafka. En este ejemplo, los clústeres Kafka y Spark se encuentran en una red virtual de Azure. En el diagrama siguiente, se muestra cómo fluye la comunicación entre los clústeres:

![Diagrama de clústeres Spark y Kafka en una red virtual de Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]  
> Aunque Kafka tiene limitadas la comunicación en la red virtual, se puede acceder a otros servicios del clúster, como SSH y Ambari, a través de Internet. Para más información sobre los puertos públicos disponibles en HDInsight, consulte [Puertos e identificadores URI usados en HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Aunque puede crear manualmente la red virtual de Azure y los clústeres Kafka y Spark, resulta más sencillo utilizar una plantilla de Azure Resource Manager. Siga los pasos que se indican a continuación para implementar una red virtual de Azure y los clústeres Kafka y Spark en la suscripción de Azure.

1. Utilice el siguiente botón para iniciar sesión en Azure y abrir la plantilla en Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    La plantilla de Azure Resource Manager se encuentra en **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

    > [!WARNING]  
    > Para garantizar la disponibilidad de Kafka en HDInsight, el clúster debe contener al menos tres nodos de trabajo. Esta plantilla crea un clúster de Kafka que contiene tres nodos de trabajo.

    Esta plantilla crea un clúster de HDInsight 3.6 para Kafka y Spark.

2. Utilice los datos siguientes para rellenar las entradas de la sección **Implementación personalizada**:
   
    ![Implementación personalizada de HDInsight](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Grupo de recursos**: cree un grupo o seleccione uno existente. Este grupo contiene el clúster de HDInsight.

    * **Ubicación**: seleccione una ubicación geográfica próxima a usted.

    * **Base Cluster Name** (Nombre base del clúster): este valor se utiliza como nombre base en los clústeres Spark y Kafka. Por ejemplo, si especifica **hdistreaming**, creará un clúster Spark denominado __spark-hdistreaming__ y un clúster Kafka denominado **kafka-hdistreaming**.

    * **Cluster Login User Name** (Nombre de usuario de inicio de sesión del clúster): nombre de usuario del administrador de los clústeres Spark y Kafka.

    * **Contraseña de inicio de sesión del clúster**: contraseña de usuario del administrador de los clústeres Spark y Kafka.

    * **Nombre de usuario SSH**: usuario de SSH que se va a crear para los clústeres Spark y Kafka.

    * **Contraseña SSH**: contraseña del usuario de SSH para los clústeres Spark y Kafka.

3. Consulte los **Términos y condiciones** y seleccione **Acepto los términos y condiciones indicados anteriormente**.

4. Por último, seleccione **Adquirir**. Se tarda aproximadamente 20 minutos en crear los clústeres.

Cuando se crean los recursos, aparece una página de resumen.

![Resumen del grupo de recursos de la red virtual y los clústeres](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]  
> Observe que los nombres de los clústeres de HDInsight son **spark-BASENAME** y **kafka-BASENAME**, donde BASENAME es el nombre que indicó en la plantilla. Estos nombres se utilizarán más adelante al establecer la conexión con los clústeres.

## <a name="use-the-notebooks"></a>Usar los blocs de notas

El código para el ejemplo descrito en este documento está disponible en [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Para completar este ejemplo, siga los pasos que se indican en `README.md`.

## <a name="delete-the-cluster"></a>Eliminación del clúster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Como el procedimiento descrito en este documento crea los dos clústeres en el mismo grupo de recursos de Azure, puede eliminar el grupo de recursos de Azure Portal. Al eliminar el grupo, se eliminan también todos los recursos creados con el procedimiento descrito en este documento, la red virtual de Azure Virtual Network y la cuenta de almacenamiento que utilizan los clústeres.

## <a name="next-steps"></a>Pasos siguientes

En este ejemplo ha aprendido a usar Spark para leer y escribir en Kafka. Utilice los vínculos siguientes para conocer otras formas de trabajar con Kafka:

* [Introducción a Apache Kafka en HDInsight](kafka/apache-kafka-get-started.md)
* [Uso de MirrorMaker para crear una réplica de Apache Kafka en HDInsight](kafka/apache-kafka-mirroring.md)
* [Uso de Apache Storm con Apache Kafka en HDInsight](hdinsight-apache-storm-with-kafka.md)

