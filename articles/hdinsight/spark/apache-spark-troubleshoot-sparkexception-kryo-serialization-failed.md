---
title: No se pueden descargar conjuntos de datos grandes con JDBC/ODBC y el marco de software Apache Thrift en Azure HDInsight
description: No se pueden descargar conjuntos de datos grandes con JDBC/ODBC y el marco de software Apache Thrift en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 4283de9d9046cc63ee10731c05b70850648ff981
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668668"
---
# <a name="scenario-unable-to-download-large-data-sets-using-jdbcodbc-and-apache-thrift-software-framework-in-azure-hdinsight"></a>Escenario: No se pueden descargar conjuntos de datos grandes con JDBC/ODBC y el marco de software Apache Thrift en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Al intentar descargar conjuntos de datos grandes mediante JDBC/ODBC y el marco de software Apache Thrift en Azure HDInsight, recibe un mensaje de error similar al siguiente:

```
org.apache.spark.SparkException: Kryo serialization failed:
Buffer overflow. Available: 0, required: 36518. To avoid this, increase spark.kryoserializer.buffer.max value.
```

## <a name="cause"></a>Causa

Esta excepción se debe a que el proceso de serialización está intentando usar más espacio en búfer del permitido. En Spark 2.0.0, la clase `org.apache.spark.serializer.KryoSerializer` se usa para serializar objetos cuando se tiene acceso a los datos mediante el marco de software Apache Thrift. Con los datos que se envían a través de la red o que se almacenan en caché en formato serializado, se usa una clase diferente.

## <a name="resolution"></a>Resolución

Aumente el valor de búfer `Kryoserializer`. Agregue una clave denominada `spark.kryoserializer.buffer.max` y establézcala en `2048` en la configuración de spark2 en `Custom spark2-thrift-sparkconf`.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport): la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para más información, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
