---
title: 'Inicio rápido: Ejecución de un script de R en un clúster de Machine Learning Services en Azure HDInsight con una consola de R'
description: En el inicio rápido, ejecutará un script de R en un clúster de Machine Learning Services en Azure HDInsight con una consola de R.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 682ee4f44dcdd2619668645fa7a8aa22cb645273
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67451516"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Inicio rápido: Ejecución de un script de R en un clúster de Machine Learning Services en Azure HDInsight con una consola de R

Machine Learning Services en Azure HDInsight permite que los scripts de R usen Apache Spark y Apache Hadoop MapReduce para ejecutar cálculos distribuidos. ML Services controla cómo se ejecutan las llamadas mediante el establecimiento del contexto de proceso. El nodo perimetral de un clúster proporciona un lugar conveniente para conectarse al clúster y ejecutar los scripts de R. Con un nodo perimetral, tiene la opción de ejecutar las funciones distribuidas paralelizadas de RevoScaleR en los diferentes núcleos del servidor de nodo perimetral. También puede ejecutarlas en los nodos del clúster utilizando los contextos de proceso de Apache Spark o Hadoop MapReduce de RevoScaleR.

En este inicio rápido, aprenderá a ejecutar un script de R con una consola de R que muestra el uso de Spark para cálculos de R distribuidos. Definirá un contexto de proceso para llevar a cabo cálculos localmente en un nodo perimetral y, nuevamente, distribuidos entre los nodos del clúster de HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de ML Services en HDInsight. Consulte el artículo sobre la [Creación de clústeres basados en Linux en HDInsight mediante Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) y seleccione **ML Services** como **Tipo de clúster**.

* Un cliente SSH. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Conexión a la consola de R

1. Conéctese al nodo perimetral de un clúster de HDInsight para ML Services mediante SSH. Modifique el comando siguiente: reemplace `CLUSTERNAME` por el nombre del clúster y, luego, escriba el comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. En la sesión SSH, use el siguiente comando para iniciar la consola de R:

    ```
    R
    ```

    Debería ver una salida con la versión de ML Server, además de otra información.


## <a name="use-a-compute-context"></a>Usar un contexto de proceso

1. Desde el símbolo de sistema de `>` , puede escribir el código de R. Use las opciones siguientes para cargar datos de ejemplo en el almacenamiento predeterminado de HDInsight:

    ```R
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Este paso puede tardar 10 minutos en completarse.

1. Cree información de datos y defina dos orígenes de datos. Escriba el código siguiente en la consola de R:

    ```R
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Ejecute una regresión lógica a través de los datos con el contexto de proceso **local**. Escriba el código siguiente en la consola de R:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Estos cálculos deberían tardar unos 7 minutos. Debería ver una salida que finalice con líneas similares a las del siguiente fragmento de código:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Ejecute la misma regresión lógica usando el contexto de **Spark**. El contexto de Spark distribuirá el procesamiento en todos los nodos de trabajo del clúster de HDInsight. Escriba el código siguiente en la consola de R:

    ```R
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Estos cálculos deberían tardar unos 5 minutos.

1. Para salir de la consola de R, use el siguiente comando:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Después de completar el inicio rápido, puede ser conveniente eliminar el clúster. Con HDInsight, los datos se almacenan en Azure Storage, por lo que puede eliminar un clúster de forma segura cuando no se esté usando. También se le cobrará por un clúster de HDInsight aunque no se esté usando. Como en muchas ocasiones los cargos por el clúster son mucho más elevados que los cargos por el almacenamiento, desde el punto de vista económico tiene sentido eliminar clústeres cuando no se estén usando.

Para eliminar un clúster, consulte [Eliminación de un clúster de HDInsight con el explorador, PowerShell o la CLI de Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha aprendido a ejecutar un script de R con una consola de R que muestra el uso de Spark para cálculos de R distribuidos.  Siga con el artículo siguiente para aprender las opciones que están disponibles para especificar si la ejecución se realiza o no en paralelo y, en caso afirmativo, cómo se lleva a cabo a través del nodo perimetral o del clúster de HDInsight.

> [!div class="nextstepaction"]
>[Opciones de contexto de proceso para de ML Services en HDInsight](./r-server-compute-contexts.md)