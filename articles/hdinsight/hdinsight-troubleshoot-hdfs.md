---
title: Solución de problemas de HDFS en Azure HDInsight
description: Obtenga respuestas a las preguntas comunes sobre cómo trabajar con HDFS y Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/14/2019
ms.custom: seodec18
ms.openlocfilehash: 1b244b3d402d7896ade2fa2e84881a0079ef6bba
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515638"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Solución de problemas de HDFS de Apache Hadoop con Azure HDInsight

Obtenga información sobre los principales problemas y sus soluciones al trabajar con cargas útiles del Sistema de archivos distribuido de Hadoop (HDFS) en Apache Ambari.

## <a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>¿Cómo se accede a la instancia de HDFS local desde un clúster?

### <a name="issue"></a>Problema

Acceda a la HDFS local desde la línea de comandos y el código de aplicación, en lugar de mediante Azure Blob Storage o Azure Data Lake Storage desde dentro del clúster de HDInsight.   

### <a name="resolution-steps"></a>Pasos de la solución

1. En el símbolo del sistema, use `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` literalmente, como en el siguiente comando:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Desde el código fuente, utilice el identificador URI `hdfs://mycluster/` literalmente, como se muestra en la siguiente aplicación de ejemplo:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Ejecute el archivo .jar compilado (por ejemplo, un archivo denominado `java-unit-tests-1.0.jar`) en el clúster de HDInsight con el siguiente comando:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. Esta cuenta conecta a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).