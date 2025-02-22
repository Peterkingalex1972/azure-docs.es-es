---
title: Uso de cuadernos de Zeppelin con un clúster Apache Spark en Azure HDInsight
description: Instrucciones paso a paso sobre cómo usar cuadernos de Zeppelin con clústeres Apache Spark en Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: 219cdeea228ae3e334213a0f0654f904592cb09e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448740"
---
# <a name="use-apache-zeppelin-notebooks-with-apache-spark-cluster-on-azure-hdinsight"></a>Uso de cuadernos de Apache Zeppelin con un clúster Apache Spark en Azure HDInsight

Los clústeres Spark de HDInsight contienen cuadernos de [Apache Zeppelin](https://zeppelin.apache.org/) que se pueden utilizar para ejecutar trabajos de [Apache Spark](https://spark.apache.org/). En este artículo, aprenderá a usar Zeppelin Notebook en un clúster de HDInsight.

**Requisitos previos:**

* Una suscripción de Azure. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).
* El esquema de URI para el almacenamiento principal de clústeres. Sería `wasb://` para Azure Blob Storage, `abfs://` para Azure Data Lake Storage Gen2 o `adl://` para Azure Data Lake Storage Gen1. Si la transferencia segura está habilitada para Blob Storage o Data Lake Storage Gen2, el URI sería `wasbs://` o `abfss://`, respectivamente.  Consulte también, [Solicitar la transferencia segura en Azure Storage](../../storage/common/storage-require-secure-transfer.md) para obtener más información.

## <a name="launch-an-apache-zeppelin-notebook"></a>Inicio de un cuaderno de Apache Zeppelin

1. Desde la **Introducción** el clúster de Spark, seleccione el **cuaderno de Zeppelin** en los **paneles de clúster**. Escriba las credenciales de administrador del clúster.  

   > [!NOTE]  
   > También puede comunicarse con su equipo portátil ligero Zeppelin en el clúster si abre la siguiente dirección URL en el explorador. Reemplace **CLUSTERNAME** por el nombre del clúster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Cree un nuevo notebook. En el panel del encabezado, vaya a **Notebook** > **Create new note** (Cuaderno > Crear nueva nota).

    ![Creación de un nuevo cuaderno de Zeppeling](./media/apache-spark-zeppelin-notebook/hdinsight-create-zeppelin-notebook.png "Creación de un nuevo cuaderno de Zeppeling")

    Especifique un nombre para el cuaderno y haga clic en **Create Note** (Crear nota).

3. Por otro lado, asegúrese de que en el encabezado del cuaderno aparece el estado conectado. Esto estado se indica mediante un punto verde que se encuentra en la esquina superior derecha.

    ![Estado del cuaderno de Zeppeling](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-connected.png "Estado del cuaderno de Zeppeling")

4. Cargue los datos de ejemplo en una tabla temporal. Cuando cree un clúster de Spark en HDInsight, el archivo de datos de ejemplo `hvac.csv` se copia en la cuenta de almacenamiento asociada en `\HdiSamples\SensorSampleData\hvac`.

    En el párrafo vacío que se crea de manera predeterminada en el nuevo cuaderno, pegue el siguiente fragmento.

    ```scala
    %livy2.spark
    //The above magic instructs Zeppelin to use the Livy Scala interpreter

    // Create an RDD using the default Spark context, sc
    val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    // Define a schema
    case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
   
    // Map the values in the .csv file to the schema
    val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
        s => Hvac(s(0), 
                s(1),
                s(2).toInt,
                s(3).toInt,
                s(6)
        )
    ).toDF()

    // Register as a temporary table called "hvac"
    hvac.registerTempTable("hvac")
    ```

    Presione **MAYÚS+ENTRAR** o haga clic en el botón **Reproducir** del párrafo para ejecutar el fragmento de código. El estado en la esquina derecha del párrafo debería avanzar de READY (Listo), PENDING (Pendiente) o RUNNING (En ejecución) a FINISHED (Finalizado). El resultado se muestra en la parte inferior del mismo párrafo. La captura de pantalla es similar a la siguiente:

    ![Creación de una tabla temporal a partir de datos sin procesar](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-load-data.png "Creación de una tabla temporal a partir de datos sin procesar")

    También puede proporcionar un título para cada párrafo. Desde la esquina derecha del párrafo, seleccione el ícono **Settings** (Configuración) con forma de rueda dentada y seleccione **Show title** (Mostrar título).  

    > [!NOTE]  
    > El intérprete %spark2 no se admite en los cuadernos de Zeppelin en todas las versiones de HDInsight y el intérprete %sh no se admite en HDInsight 4.0 y versiones posteriores.

5. Ahora puede ejecutar instrucciones de Spark SQL en la tabla `hvac`. Pegue la siguiente consulta en un nuevo párrafo. La consulta recupera el identificador del edificio y la diferencia entre la temperatura objetivo y la real para cada edificio en una fecha determinada. Presione **MAYÚS + ENTRAR**.

    ```sql
    %sql
    select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 
    ```  

    La instrucción **%sql** del principio le indica al cuaderno que utilice el intérprete de Livy Scala.

6. Seleccione el icono del **gráfico de barras** para cambiar la visualización.  La **configuración**, que aparece después de haber seleccionado el **gráfico de barras**, le permite elegir las **claves** y los **valores**.  En la captura de pantalla siguiente se muestra el resultado.

    ![Ejecución de una instrucción Spark SQL mediante el cuaderno](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-1.png "Ejecución de una instrucción Spark SQL mediante el cuaderno")

7. También puede ejecutar instrucciones Spark SQL usando variables en la consulta. El siguiente fragmento de código muestra cómo definir una variable `Temp` en la consulta con los valores posibles con los que quiere hacer la consulta. Cuando ejecuta la consulta por primera vez, se rellena una lista desplegable automáticamente con los valores especificados para la variable.

    ```sql
    %sql  
    select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}"
    ```

    Pegue este fragmento de código en un nuevo párrafo y presione **MAYÚS + ENTRAR**. A continuación, seleccione **65** en la lista desplegable **Temp**. 

8. Seleccione el icono del **gráfico de barras** para cambiar la visualización.  Seguidamente, seleccione **configuración** y realice los siguientes cambios:

   * **Grupos:**  Agregue **targettemp**.  
   * **Valores:** 1. Quite la **fecha**.  2. Agregue **temp_diff**.  3.  Cambie el agregador de **SUM** a **AVG**.  

     En la captura de pantalla siguiente se muestra el resultado.

     ![Ejecución de una instrucción Spark SQL mediante el cuaderno](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-spark-query-2.png "Ejecución de una instrucción Spark SQL mediante el cuaderno")

9. Reinicie el intérprete de Livy para salir de la aplicación. Para ello, abra la configuración del intérprete haciendo clic en el nombre del usuario conectado que encontrará en la esquina superior derecha y después en **Interpreter** (Intérprete).  

    ![Inicio del intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Salida de Hive")

10. Desplácese hasta **livy** y seleccione la opción para **reiniciar**.  Seleccione **Aceptar** cuando se le solicite.

    ![Reinicio del intérprete de Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reinicio del intérprete de Zeppeling")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Uso de paquetes externos con el cuaderno
Puede configurar el cuaderno de Zeppelin Notebook en un clúster Apache Spark de HDInsight si quiere usar paquetes externos que haya aportado la comunidad y que no estén incluidos en el clúster. Puede buscar el [repositorio de Maven](https://search.maven.org/) para obtener una lista completa de los paquetes que están disponibles. También puede obtener una lista de paquetes disponibles de otras fuentes. Por ejemplo, dispone de la lista completa de los paquetes externos aportados por la comunidad en [Spark Packages](https://spark-packages.org/)(Paquetes Spark).

En este artículo, aprenderá a utilizar el paquete [spark csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) con el cuaderno de Jupyter Notebook.

1. Abra la configuración del intérprete. Desde la esquina superior derecha, seleccione el nombre de usuario registrado y, a continuación, seleccione **Interpreter** (Intérprete).

    ![Inicio del intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Salida de Hive")

2. Desplácese hasta **livy** y seleccione la opción de **editar**.

    ![Cambio de la configuración del intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Cambio de la configuración del intérprete")

3. Agregue una nueva clave denominada `livy.spark.jars.packages` y establezca su valor con el formato `group:id:version`. Por ejemplo, si desea usar el paquete [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar), debe establecer el valor de la clave en `com.databricks:spark-csv_2.10:1.4.0`.

    ![Cambio de la configuración del intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Cambio de la configuración del intérprete")

    Haga clic en **Save** (Guardar) y reinicie el intérprete de Livy.

4. Si desea saber cómo acceder al valor de la clave especificada anteriormente, siga estos pasos.
   
    a. Busque el paquete en el repositorio de Maven. En este artículo, hemos utilizado [spark-csv](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. En el repositorio, recopile los valores de **GroupId**, **ArtifactId** y **Version**.
   
    ![Uso de paquetes externos con cuadernos de Jupyter](./media/apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Uso de paquetes externos con cuadernos de Jupyter")
   
    c. Concatene los tres valores separados por dos puntos ( **:** ).
   
        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>¿Dónde se guardan los cuadernos de Zeppelin Notebook?
Los cuadernos de Zeppelin Notebook se guardan en los nodos principales del clúster. Por tanto, si se elimina el clúster, también se eliminarán los cuadernos. Si desea guardar los cuadernos para utilizarlos más adelante en otros clústeres, debe exportarlos cuando haya terminado de ejecutar los trabajos. Para exportar un cuaderno, haga clic en el icono **Export** (Exportar), tal y como se muestra en la imagen siguiente.

![Descarga del cuaderno](./media/apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Descarga del cuaderno")

De este modo, el cuaderno se guarda como un archivo JSON en la ubicación de descarga.

## <a name="livy-session-management"></a>Administración de sesiones de Livy
Cuando ejecute el primer párrafo de código del cuaderno de Zeppelin Notebook, se creará una nueva sesión de Livy en el clúster Spark de HDInsight. Esta sesión se compartirá con todos los cuadernos de Zeppelin Notebook que cree en el futuro. Si, por alguna razón, termina la sesión de Livy (por ejemplo, se reinicia el clúster), no podrá ejecutar trabajos desde el cuaderno de Zeppelin Notebook.

En este caso, debe seguir los pasos que se indican a continuación para poder ejecutar trabajos desde un cuaderno de Zeppelin Notebook.  

1. Reinicie el intérprete de Livy desde el cuaderno de Zeppelin Notebook. Para ello, abra la configuración del intérprete haciendo clic en el nombre del usuario conectado que encontrará en la esquina superior derecha y después en **Interpreter** (Intérprete).

    ![Inicio del intérprete](./media/apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Salida de Hive")

2. Desplácese hasta **livy** y seleccione la opción para **reiniciar**.

    ![Reinicio del intérprete de Livy](./media/apache-spark-zeppelin-notebook/hdinsight-zeppelin-restart-interpreter.png "Reinicio del intérprete de Zeppeling")

3. Ejecute una celda de código desde el cuaderno de Zeppelin Notebook existente. Esto creará una nueva sesión de Livy en el clúster de HDInsight.

## <a name="seealso"></a>Consulte también
* [Información general: Apache Spark en Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Apache Spark con BI: Análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: uso de Apache Spark en HDInsight para analizar la temperatura de edificios con los datos del sistema de acondicionamiento de aire](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análisis de registros de un sitio web mediante Apache Spark en HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](apache-spark-create-standalone-application.md)
* [Ejecución de trabajos de forma remota en un clúster de Apache Spark mediante Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para crear y enviar aplicaciones de Apache Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Use HDInsight Tools Plugin for IntelliJ IDEA to debug Apache Spark applications remotely](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md) (Uso del complemento de herramientas de HDInsight para IntelliJ IDEA para depurar aplicaciones de Apache Spark de forma remota)
* [Kernels disponible para Jupyter Notebook en clústeres Apache Spark para HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[azure-create-storageaccount]:../../storage/common/storage-create-storage-account.md 
