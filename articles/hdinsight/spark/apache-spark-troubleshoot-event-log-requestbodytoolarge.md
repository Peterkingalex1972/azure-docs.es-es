---
title: NativeAzureFileSystem ... RequestBodyTooLarge aparece en el registro de la aplicación de streaming de Apache Spark en Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge aparece en el registro de la aplicación de streaming de Apache Spark en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 571e02e79714d2e713d4173936120e78e4b067de
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700200"
---
# <a name="scenario-nativeazurefilesystem--requestbodytoolarge-appears-in-log-for-apache-spark-streaming-app-in-azure-hdinsight"></a>Escenario: "NativeAzureFileSystem ... RequestBodyTooLarge" aparece en el registro de la aplicación de streaming de Apache Spark en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

El error `NativeAzureFileSystem ... RequestBodyTooLarge` aparece en el registro de controladores para una aplicación de streaming de Apache Spark.

## <a name="cause"></a>Causa

El archivo de registro de eventos de Spark probablemente alcanza el límite de longitud de archivos de WASB.

En Spark 2.3, cada aplicación de Spark genera un archivo de registro de eventos de Spark. El archivo del registro de eventos de Spark correspondiente a una aplicación de streaming de Spark sigue creciendo mientras la aplicación se está ejecutando. En la actualidad, un archivo en WASB tiene un límite de bloque de 50 000 y el tamaño de bloque predeterminado es de 4 MB. Por lo tanto, en la configuración predeterminada, el tamaño máximo del archivo es de 195 GB. Sin embargo, el almacenamiento de Azure ha aumentado el tamaño máximo de bloque a 100 MB, lo que establece el límite de un solo archivo a 4,75 TB. Para obtener más información, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

## <a name="resolution"></a>Resolución

Hay tres soluciones disponibles para este error:

* Aumente el tamaño del bloque hasta 100 MB. En la interfaz de usuario de Ambari, modifique la propiedad de configuración `fs.azure.write.request.size` de HDFS (o créela en la sección `Custom core-site`). Establezca la propiedad en un valor mayor, por ejemplo: 33554432. Guarde la configuración actualizada y reinicie los componentes afectados.

* Detenga y vuelva a enviar periódicamente el trabajo de streaming de Spark.

* Use HDFS para almacenar los registros de eventos de Spark. Si usa HDFS en el almacenamiento puede provocar la pérdida de datos de eventos de Spark durante el escalado de clústeres o las actualizaciones de Azure.

    1. Realice cambios en `spark.eventlog.dir` y `spark.history.fs.logDirectory` a través de la interfaz de usuario de Ambari:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Cree directorios en HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Reinicie todos los servicios afectados a través de la interfaz de usuario de Ambari.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener más información, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
