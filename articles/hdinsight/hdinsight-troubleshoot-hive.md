---
title: Solución de problemas de Hive mediante Azure HDInsight
description: Obtenga respuestas a las preguntas comunes sobre cómo trabajar con Apache Hive y Azure HDInsight.
keywords: Azure HDInsight, Hive, preguntas más frecuentes, guía de solución de problemas, preguntas comunes
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: ca1e3e11ad5458e8e7f7072b7d3dd561853029fe
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69575699"
---
# <a name="troubleshoot-apache-hive-by-using-azure-hdinsight"></a>Solución de problemas de Apache Hive mediante Azure HDInsight

Obtenga información sobre las principales preguntas y sus soluciones al trabajar con cargas útiles de Apache Hive en Apache Ambari.

## <a name="how-do-i-export-a-hive-metastore-and-import-it-on-another-cluster"></a>¿Cómo se exporta una instancia de Hive Metastore y la importo en otro clúster?

### <a name="resolution-steps"></a>Pasos de la solución

1. Conéctese al clúster de HDInsight con un cliente Secure Shell (SSH). Para más información, consulte [Lecturas adicionales](#additional-reading-end).

2. Ejecute el siguiente comando en el clúster de HDInsight desde donde desea exportar Metastore:

    ```apache
    for d in `hive -e "show databases"`; do echo "create database $d; use $d;" >> alltables.sql ; for t in `hive --database $d -e "show tables"` ; do ddl=`hive --database $d -e "show create table $t"`; echo "$ddl ;" >> alltables.sql ; echo "$ddl" | grep -q "PARTITIONED\s*BY" && echo "MSCK REPAIR TABLE $t ;" >> alltables.sql ; done; done
    ```

   Este comando genera un archivo denominado allatables.sql.

3. Copie el archivo alltables.sql en el nuevo clúster de HDInsight y ejecute el siguiente comando:

    ```apache
    hive -f alltables.sql
    ```

El código de los pasos de resolución asume que las rutas de acceso de datos en el nuevo clúster son las mismos que las rutas de acceso de datos en el clúster antiguo. Si las rutas de acceso de datos son diferentes, puede editar manualmente el archivo `alltables.sql` generado para reflejar cualquier cambio.

### <a name="additional-reading"></a>Lecturas adicionales

- [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-locate-hive-logs-on-a-cluster"></a>¿Cómo se buscan registros de Hive en un clúster?

### <a name="resolution-steps"></a>Pasos de la solución

1. Conéctese al clúster de HDInsight con SSH. Para más información, consulte **Lecturas adicionales**.

2. Para ver los registros de cliente de Hive, use el comando siguiente:

   ```apache
   /tmp/<username>/hive.log
   ```

3. Para ver los registros de Hive Metastore, use el comando siguiente:

   ```apache
   /var/log/hive/hivemetastore.log
   ```

4. Para ver los registros de HiveServer, use el comando siguiente:

   ```apache
   /var/log/hive/hiveserver2.log
   ```

### <a name="additional-reading"></a>Lecturas adicionales

- [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-launch-the-hive-shell-with-specific-configurations-on-a-cluster"></a>¿Cómo se inicia el shell de Hive con configuraciones específicas en un clúster?

### <a name="resolution-steps"></a>Pasos de la solución

1. Especifique un par clave-valor de configuración cuando inicie el shell de Hive. Para más información, consulte [Lecturas adicionales](#additional-reading-end).

   ```apache
   hive -hiveconf a=b
   ```

2. Para enumerar todas las configuraciones efectivas en el shell de Hive, use el siguiente comando:

   ```apache
   hive> set;
   ```

   Por ejemplo, utilice el siguiente comando para iniciar el shell de Hive con el registro de depuración habilitado en la consola:

   ```apache
   hive -hiveconf hive.root.logger=ALL,console
   ```

### <a name="additional-reading"></a>Lecturas adicionales

- [Propiedades de configuración de Hive](https://cwiki.apache.org/confluence/display/Hive/Configuration+Properties)

## <a name="how-do-i-analyze-tez-dag-data-on-a-cluster-critical-path"></a>¿Cómo se analizan datos de Apache Tez DAG en una ruta crítica de clúster?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para analizar un gráfico acíclico dirigido (DAG) de Apache Tez en un grafo crítico para el clúster, conéctese al clúster de HDInsight con SSH. Para más información, consulte [Lecturas adicionales](#additional-reading-end).

2. En el símbolo del sistema, ejecute el siguiente comando:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar CriticalPath --saveResults --dagId <DagId> --eventFileName <DagData.zip> 
   ```

3. Para enumerar otros analizadores que pueden usarse para analizar DAG de Tez, utilice el comando siguiente:

   ```apache
   hadoop jar /usr/hdp/current/tez-client/tez-job-analyzer-*.jar
   ```

   Debe proporcionar un programa de ejemplo como el primer argumento.

   Los nombres de programa válidos incluyen:
    - **ContainerReuseAnalyzer**: imprima los detalles de la reutilización del contenedor en un DAG
    - **CriticalPath**: busque la ruta crítica de un DAG
    - **LocalityAnalyzer**: imprima los detalles de localidad en un DAG
    - **ShuffleTimeAnalyzer**: analice los detalles de tiempo de orden aleatorio en un DAG
    - **SkewAnalyzer**: analice los detalles de distorsión en un DAG
    - **SlowNodeAnalyzer**: imprima los detalles del nodo en un DAG
    - **SlowTaskIdentifier**: imprima los detalles de la tarea lenta en un DAG
    - **SlowestVertexAnalyzer**: imprima los detalles del vértice más lento en un DAG
    - **SpillAnalyzer**: imprima los detalles de desbordamiento en un DAG
    - **TaskConcurrencyAnalyzer**: imprima los detalles de la simultaneidad de tareas en un DAG
    - **VertexLevelCriticalPathAnalyzer**: busque la ruta crítica a nivel de vértice en un DAG

### <a name="additional-reading"></a>Lecturas adicionales

- [Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="how-do-i-download-tez-dag-data-from-a-cluster"></a>¿Cómo se descargan datos de Tez DAG desde un clúster?

#### <a name="resolution-steps"></a>Pasos de la solución

Hay dos maneras de recopilar los datos de DAG de Tez.

- Desde la línea de comandos:

    Conéctese al clúster de HDInsight con SSH. En el símbolo del sistema, ejecute el siguiente comando:

  ```apache
  hadoop jar /usr/hdp/current/tez-client/tez-history-parser-*.jar org.apache.tez.history.ATSImportTool -downloadDir . -dagId <DagId>
  ```

- Use la vista de Tez de Ambari:

  1. Vaya a Ambari.
  2. Vaya a la vista de Tez (en el icono de la esquina superior derecha).
  3. Seleccione el DAG que desea ver.
  4. Seleccione **Descargar datos**.

### <a name="additional-reading-end"></a>Lecturas adicionales

[Conexión a través de SSH con HDInsight (Hadoop)](hdinsight-hadoop-linux-use-ssh-unix.md)

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

- Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

- Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. Esta cuenta conecta a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

- Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
