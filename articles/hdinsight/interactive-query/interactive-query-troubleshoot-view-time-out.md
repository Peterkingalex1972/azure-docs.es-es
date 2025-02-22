---
title: Se agota el tiempo de espera de la vista de Apache Hive al capturar el resultado de una consulta en Azure HDInsight
description: Se agota el tiempo de espera de la vista de Apache Hive al capturar el resultado de una consulta en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: e5b16dc9163172b1c080a397990451cde685d25f
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947899"
---
# <a name="scenario-apache-hive-view-times-out-when-fetching-a-query-result-in-azure-hdinsight"></a>Escenario: Se agota el tiempo de espera de la vista de Apache Hive al capturar el resultado de una consulta en Azure HDInsight

En este artículo se describen los pasos para la solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Interactive Query en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Al ejecutar determinadas consultas desde la vista de Apache Hive, puede aparecer el siguiente error:

```
result fetch timed out
java.util.concurrent.TimeoutException: deadline passed
```

## <a name="cause"></a>Causa

Es posible que el valor de tiempo de espera predeterminado de la vista de Hive no sea adecuado para la consulta que se está ejecutando. El período de tiempo especificado es demasiado corto para que la vista de Hive capture el resultado de la consulta.

## <a name="resolution"></a>Resolución

Aumente los tiempos de espera de la vista de Apache Ambari Hive estableciendo las siguientes propiedades en `/etc/ambari-server/conf/ambari.properties`.

```
views.ambari.request.read.timeout.millis=300000
views.request.read.timeout.millis=300000
views.ambari.hive<HIVE_VIEW_INSTANCE_NAME>.result.fetch.timeout=300000
```

El valor de `HIVE_VIEW_INSTANCE_NAME` está disponible al final de la dirección URL de la vista de Hive.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
