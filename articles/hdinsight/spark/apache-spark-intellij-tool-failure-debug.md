---
title: 'Error de depuración de trabajos de Spark con Azure Toolkit for IntelliJ (versión preliminar) '
description: Instrucciones paso a paso para el uso de las herramientas de HDInsight del Kit de herramientas de Azure para IntelliJ para depurar aplicaciones de forma remota en clústeres de HDInsight mediante SSH
keywords: depurar remotamente intellij, depuración remota intellij, ssh, intellij, hdinsight, depurar intellij, depuración
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 07/12/2019
ms.openlocfilehash: aff9f0f70377ebc6e741618b22ff82bc06251521
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295910"
---
# <a name="failure-spark-job-debugging-with-azure-toolkit-for-intellij-preview"></a>Error de depuración de trabajos de Spark con Azure Toolkit for IntelliJ (versión preliminar)

En este artículo se proporcionan instrucciones paso a paso sobre cómo usar las herramientas de HDInsight de [Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij?view=azure-java-stable) para ejecutar aplicaciones de **depuración de errores de Spark**. 

## <a name="prerequisites"></a>Requisitos previos
* [Kit de desarrollo de Oracle Java](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). En este tutorial se usa la versión 8.0.202 de Java.
  
* IntelliJ IDEA. En este artículo se usa [IntelliJ IDEA Community 2019.1.3](https://www.jetbrains.com/idea/download/#section=windows).
  
* Azure Toolkit for IntelliJ. Consulte [Instalación de Azure Toolkit for IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation?view=azure-java-stable).

* Conéctese al clúster de HDInsight. Vea [Conexión al clúster de HDInsight](apache-spark-intellij-tool-plugin.md).

* Explorador de Azure Storage. Vea [Descargar el Explorador de Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

## <a name="create-a-project-with-debugging-template"></a>Creación de un proyecto con la plantilla de depuración 

Cree un proyecto de Spark 2.3.2 para continuar con la depuración de errores; use el archivo de ejemplo de depuración de tareas de este documento.

1. Abra IntelliJ IDEA. Abra la ventana **New Project** (Nuevo proyecto).

   a. Seleccione **Azure Spark/HDInsight** en el panel izquierdo. 

   b. Seleccione **Spark Project with Failure Task Debugging Samples (Preview) (Scala)** (Proyecto de Spark con ejemplos de depuración de tareas de error [versión preliminar] [Scala]) en la ventana principal.

     ![Creación de un proyecto de depuración](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-projectfor-failure-debug.png)

   c. Seleccione **Next** (Siguiente).     
 
2. En la ventana **New Project** (Nuevo proyecto), siga estos pasos:

   ![Selección del SDK de Spark](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-project.png)

   a. Escriba un nombre y una ubicación de proyecto.

   b. En la lista desplegable **Project SDK** (SDK del proyecto), seleccione **Java 1.8** para el clúster de **Spark 2.3.2**.

   c. En la lista desplegable **Spark Version** (Versión de Spark), seleccione **Spark 2.3.2(Scala 2.11.8)** .

   d. Seleccione **Finalizar**.

3. Seleccione **src** > **main** > **scala** para abrir el código en el proyecto. En este ejemplo se usa el script **AgeMean_Div()** .

## <a name="run-a-spark-scalajava-application-on-an-hdinsight-cluster"></a>Ejecución de una aplicación Scala o Java de Spark en un clúster de HDInsight Spark

Cree una aplicación Scala o Java de Spark, y después ejecútela en un clúster de Spark mediante estos pasos:

1. Haga clic en **Add Configuration** (Agregar configuración) para abrir la ventana **Run/Debug Configurations** (Ejecutar/depurar configuraciones).

   ![Editar configuraciones](./media/apache-spark-intellij-tool-failure-debug/hdinsight-add-new-configuration.png) 

2. En el cuadro de diálogo **Run/Debug Configurations** (Ejecutar/depurar configuraciones), seleccione el signo más ( **+** ). Después, seleccione la opción **Apache Spark on HDInsight** (Apache Spark en HDInsight).

   ![Adición de nueva configuración](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-01.png)

3. Cambie a la pestaña **Remotely Run in Cluster** (Ejecutar de forma remota en clúster). Escriba la información en los campos **Name** (Nombre), **Spark cluster** (Clúster de Spark) y **Main class name** (Nombre de clase principal). Nuestras herramientas admiten la depuración con **ejecutores**. El valor predeterminado de **numExectors** es 5, y se recomienda no establecerlo por encima de 3. Para reducir el tiempo de ejecución, puede agregar **spark.yarn.maxAppAttempts** a **job Configurations** y establecer el valor en 1. Haga clic en el botón **Aceptar** para guardar la configuración.

   ![Ejecutar configuraciones de depuración](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-new-configuraion-002.png)

4. La configuración se guarda ahora con el nombre especificado. Para ver los detalles de configuración, seleccione el nombre de configuración. Para realizar cambios, seleccione **Edit Configurations** (Editar configuraciones). 

5. Después de completar la configuración, puede ejecutar el proyecto en el clúster remoto.
   
   ![Botón de ejecución remota](./media/apache-spark-intellij-tool-failure-debug/hdinsight-local-run-configuration.png)

6. Puede comprobar el identificador de la aplicación en la ventana de salida.
   
   ![Botón de ejecución remota](./media/apache-spark-intellij-tool-failure-debug/hdinsight-remotely-run-result.png)   

## <a name="download-failed-job-profile"></a>Error en la descarga del perfil de trabajo

Si se produce un error al enviar el trabajo, puede descargar el perfil de trabajo con errores en el equipo local para depurarlo más.

1. Abra el **Explorador de Microsoft Azure Storage**, busque la cuenta de HDInsight del clúster del trabajo con errores, descargue los recursos de trabajo con errores desde la ubicación correspondiente: **\hdp\spark2-events\\.spark-failures\\\<Id. de la aplicación>** a una carpeta local. En la ventana **activities** (actividades) se mostrará el progreso de la descarga.

   ![Descarga del archivo con error](./media/apache-spark-intellij-tool-failure-debug/hdinsight-find-spark-file-001.png)

   ![Descarga del archivo con error](./media/apache-spark-intellij-tool-failure-debug/spark-on-cosmos-doenload-file-2.png)   

## <a name="configure-local-debugging-environment-and-debug-on-failure"></a>Configuración del entorno de depuración local y depuración en caso de error

1. Abra el proyecto original o cree uno y asócielo con el código fuente original. Actualmente solo se admite la versión Spark 2.3.2 para la depuración de errores.

2. En IntelliJ IDEA, cree un archivo de configuración **Spark Failure Debug** (Depuración de errores de Spark) y seleccione el archivo FTD de los recursos de trabajo con errores que ha descargado antes para el campo **Spark Job Failure Context location** (Ubicación del contexto de error del trabajo de Spark).
   
   ![Botón de ejecución remota](./media/apache-spark-intellij-tool-failure-debug/hdinsight-create-failure-configuration-01.png)

4. Haga clic en el botón de ejecución local en la barra de herramientas; el error se mostrará en la ventana de ejecución.
   
   ![Botón de ejecución remota](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuraion-01.png)

   ![Botón de ejecución remota](./media/apache-spark-intellij-tool-failure-debug/local-run-failure-configuration.png)

5. Establezca el punto de interrupción como se indica en el registro y, después, haga clic en el botón depuración local para realizar la depuración local como en los proyectos normales de Scala o Java en IntelliJ.

5. Después de la depuración, si el proyecto se completa de forma correcta, puede volver a enviar el trabajo con error al clúster de Spark en HDInsight.

## <a name="seealso"></a>Pasos siguientes
* [Información general: Depuración de aplicaciones de Apache Spark](apache-spark-intellij-tool-debug-remotely-through-ssh.md)

### <a name="demo"></a>Demostración
* Creación de proyecto de Scala (vídeo): [Crear aplicaciones Apache Spark en Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Depuración remota (vídeo): [Uso de Azure Toolkit for IntelliJ para depurar de forma remota aplicaciones de Apache Spark para un clúster de HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Escenarios
* [Apache Spark con BI: Análisis de datos interactivos con Spark en HDInsight con las herramientas de BI](apache-spark-use-bi-tools.md)
* [Apache Spark con Machine Learning: uso de Apache Spark en HDInsight para analizar la temperatura de edificios con los datos del sistema de acondicionamiento de aire](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)
* [Análisis de registros de un sitio web mediante Apache Spark en HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Creación y ejecución de aplicaciones
* [Crear una aplicación independiente con Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Ejecución de trabajos de forma remota en un clúster de Apache Spark mediante Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Herramientas y extensiones
* [Uso de Azure Toolkit for IntelliJ con el fin de crear aplicaciones Apache Spark para un clúster de HDInsight](apache-spark-intellij-tool-plugin.md)
* [Uso de Azure Toolkit for IntelliJ para depurar de forma remota aplicaciones de Apache Spark mediante VPN](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Uso de las herramientas de HDInsight para IntelliJ con Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Uso de las herramientas de HDInsight de Azure Toolkit for Eclipse con el fin de crear aplicaciones Apache Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Uso de cuadernos de Apache Zeppelin con un clúster Apache Spark en HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels para Jupyter Notebook en clústeres Azure Spark en Azure HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Uso de paquetes externos con cuadernos de Jupyter Notebook](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalación de un cuaderno de Jupyter Notebook en el equipo y conexión al clúster de Apache Spark en HDInsight de Azure](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)
