---
title: Uso de Caffe en Azure HDInsight Spark para el aprendizaje profundo distribuido
description: Uso de Caffe en Azure HDInsight Spark para el aprendizaje profundo distribuido
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/17/2017
ms.openlocfilehash: d0d68263485c5ab6e57a349317b1975862470cc2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64721520"
---
# <a name="use-caffe-on-azure-hdinsight-spark-for-distributed-deep-learning"></a>Uso de Caffe en Azure HDInsight Spark para el aprendizaje profundo distribuido


## <a name="introduction"></a>Introducción

El aprendizaje profundo afecta a muchos sectores empresariales, desde la atención sanitaria, al transporte o a la fabricación, entre otros. Las empresas empiezan a usar el aprendizaje profundo para solucionar problemas difíciles, como la [clasificación de imágenes](https://blogs.microsoft.com/next/2015/12/10/microsoft-researchers-win-imagenet-computer-vision-challenge/), el [reconocimiento de voz](https://googleresearch.blogspot.jp/2015/08/the-neural-networks-behind-google-voice.html), el reconocimiento de objetos y la traducción automática. 

Hay [muchos marcos populares](https://en.wikipedia.org/wiki/Comparison_of_deep_learning_software), entre los que se incluyen [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/research/product/cognitive-toolkit/), [Tensorflow](https://www.tensorflow.org/), [Apache MXNet](https://mxnet.apache.org/), Theano, etc. [Caffe](https://caffe.berkeleyvision.org/) es uno de los marcos de redes neuronales no simbólicos más conocidos y se usa ampliamente en muchas áreas, entre las que se incluye la visión de equipos. Además, [CaffeOnSpark](https://yahoohadoop.tumblr.com/post/139916563586/caffeonspark-open-sourced-for-distributed-deep) combina Caffe con Apache Spark, en cuyo caso el aprendizaje profundo se puede usar fácilmente en un clúster de Hadoop existente. Puede usar el aprendizaje profundo junto con las canalizaciones de Spark ETL para reducir la complejidad del sistema y la latencia para el aprendizaje de la solución completa.

[HDInsight](https://azure.microsoft.com/services/hdinsight/) es una oferta de Apache Hadoop en la nube que proporciona clústeres de análisis de código abierto optimizados para Apache Spark, Apache Hive, Apache Hadoop, Apache HBase, Apache Storm, Apache Kafka y ML Services. HDInsight está respaldado por un contrato de nivel de servicio del 99,9%. Cada una de estas tecnologías de macrodatos, así como las aplicaciones de fabricantes de software independientes, se pueden implementar fácilmente como clústeres administrados, con seguridad y supervisión para empresas.

En este artículo se demuestra cómo instalar [Caffe on Spark](https://github.com/yahoo/CaffeOnSpark) para un clúster de HDInsight. En este artículo también se utiliza la demostración de MNIST integrada para mostrar cómo se utiliza el aprendizaje profundo distribuido con HDInsight Spark en CPU.

Se requieren cuatro pasos para completar la tarea:

1. Instalar las dependencias necesarias en todos los nodos
2. Compilar Caffe en Spark para HDInsight en el nodo principal
3. Distribuir las bibliotecas requeridas a todos los nodos de trabajo
4. Componer un modelo de Caffe y ejecutarlo de forma distribuida.

Dado que HDInsight es una solución de PaaS, ofrece unas características de plataforma excelentes (por lo que es sencillo realizar algunas tareas). Una de estas características utilizada en esta entrada de blog se llama [Acción de scripts](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) y con ella puede ejecutar comandos de shell para personalizar nodos de clúster (nodo principal, nodo de trabajo o nodo perimetral).

## <a name="step-1--install-the-required-dependencies-on-all-the-nodes"></a>Paso 1:  Instalar las dependencias necesarias en todos los nodos

Para empezar, es preciso instalar las dependencias. Los sitios de Caffe y [CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn) ofrecen algunos recursos wiki útiles para instalar las dependencias para Spark en modo YARN. HDInsight también utiliza Spark en modo YARN. Sin embargo, es necesario agregar algunas dependencias más para la plataforma de HDInsight. Para ello, se usará la acción de script y se ejecutará en todos los nodos principales y nodos de trabajo. Esta acción de script tarda aproximadamente 20 minutos en realizarse, ya que dichas dependencias a su vez dependen de otros paquetes. Debe colocarla en alguna ubicación a la que pueda acceder el clúster de HDInsight, como una ubicación de GitHub o la cuenta de Blob Storage predeterminada.

    #!/bin/bash
    #Please be aware that installing the below will add additional 20 mins to cluster creation because of the dependencies
    #installing all dependencies, including the ones mentioned in https://caffe.berkeleyvision.org/install_apt.html, as well a few packages that are not included in HDInsight, such as gflags, glog, lmdb, numpy
    #It seems numpy will only needed during compilation time, but for safety purpose you install them on all the nodes

    sudo apt-get install -y libprotobuf-dev libleveldb-dev libsnappy-dev libopencv-dev libhdf5-serial-dev protobuf-compiler maven libatlas-base-dev libgflags-dev libgoogle-glog-dev liblmdb-dev build-essential  libboost-all-dev python-numpy python-scipy python-matplotlib ipython ipython-notebook python-pandas python-sympy python-nose

    #install protobuf
    wget https://github.com/google/protobuf/releases/download/v2.5.0/protobuf-2.5.0.tar.gz
    sudo tar xzvf protobuf-2.5.0.tar.gz -C /tmp/
    cd /tmp/protobuf-2.5.0/
    sudo ./configure
    sudo make
    sudo make check
    sudo make install
    sudo ldconfig
    echo "protobuf installation done"


La acción de script tiene dos pasos. En el primero se instalan todas las bibliotecas requeridas. Aquí se incluyen todas las bibliotecas necesarias tanto para compilar Caffe (por ejemplo, gflags, glog) como para ejecuta Caffe (por ejemplo, numpy). Se va a utilizar libatlas para optimizar la CPU, pero puede seguir el wiki de CaffeOnSpark acerca de cómo instalar otras bibliotecas de optimización, como MKL o CUDA (para GPU).

El segundo paso es descargar, compilar e instalar protobuf 2.5.0 para Caffe durante el runtime. Protobuf 2.5.0 [es obligatorio](https://github.com/yahoo/CaffeOnSpark/issues/87); sin embargo, esta versión no está disponible como paquete en Ubuntu 16, por lo que es preciso compilarlo del código fuente. En Internet también hay varios recursos que indican cómo compilarlo. Para más información, consulte [esta página](https://jugnu-life.blogspot.com/2013/09/install-protobuf-25-on-ubuntu.html).

Para empezar, puede ejecutar esta acción de script en el clúster para todos los nodos de trabajo y los nodos principales (para HDInsight 3.5). Puede ejecutar las acciones de script en un clúster existente, o puede usar acciones de script durante la creación del clúster. Para obtener más información acerca de las acciones de script, consulte la documentación [aquí](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux).

![Acciones de script para instalar dependencias](./media/apache-spark-deep-learning-caffe/Script-Action-1.png)


## <a name="step-2-build-caffe-on-apache-spark-for-hdinsight-on-the-head-node"></a>Paso 2: Compilar Caffe en Apache Spark para HDInsight en el nodo principal

El segundo paso es compilar Caffe en el nodo principal y, después, distribuir las bibliotecas compiladas a todos los nodos de trabajo. En este paso, debe utilizar [ssh en el nodo principal](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix). Después de eso, debe seguir el [proceso de compilación de CaffeOnSpark](https://github.com/yahoo/CaffeOnSpark/wiki/GetStarted_yarn). A continuación se muestra el script que puede usar para compilar CaffeOnSpark con unos pocos pasos adicionales. 

    #!/bin/bash
    git clone https://github.com/yahoo/CaffeOnSpark.git --recursive
    export CAFFE_ON_SPARK=$(pwd)/CaffeOnSpark

    pushd ${CAFFE_ON_SPARK}/caffe-public/
    cp Makefile.config.example Makefile.config
    echo "INCLUDE_DIRS += ${JAVA_HOME}/include" >> Makefile.config
    #Below configurations might need to be updated based on actual cases. For example, if you are using GPU, or using a different BLAS library, you may want to update those settings accordingly.
    echo "CPU_ONLY := 1" >> Makefile.config
    echo "BLAS := atlas" >> Makefile.config
    echo "INCLUDE_DIRS += /usr/include/hdf5/serial/" >> Makefile.config
    echo "LIBRARY_DIRS += /usr/lib/x86_64-linux-gnu/hdf5/serial/" >> Makefile.config
    popd

    #compile CaffeOnSpark
    pushd ${CAFFE_ON_SPARK}
    #always clean up the environment before building (especially when rebuiding), or there will be errors such as "failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2"
    make clean 
    #the build step usually takes 20~30 mins, since it has a lot maven dependencies
    make build 
    popd
    export LD_LIBRARY_PATH=${CAFFE_ON_SPARK}/caffe-public/distribute/lib:${CAFFE_ON_SPARK}/caffe-distri/distribute/lib

    hadoop fs -mkdir -p wasb:///projects/machine_learning/image_dataset

    ${CAFFE_ON_SPARK}/scripts/setup-mnist.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/mnist_*_lmdb wasb:///projects/machine_learning/image_dataset/

    ${CAFFE_ON_SPARK}/scripts/setup-cifar10.sh
    hadoop fs -put -f ${CAFFE_ON_SPARK}/data/cifar10_*_lmdb wasb:///projects/machine_learning/image_dataset/

    #put the already compiled CaffeOnSpark libraries to wasb storage, then read back to each node using script actions. This is because CaffeOnSpark requires all the nodes have the libarries
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-public/distribute/lib/
    hadoop fs -mkdir -p /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-distri/distribute/lib/* /CaffeOnSpark/caffe-distri/distribute/lib/
    hadoop fs -put CaffeOnSpark/caffe-public/distribute/lib/* /CaffeOnSpark/caffe-public/distribute/lib/

Es posible que tenga que realizar más operaciones de las que se indican en la documentación de CaffeOnSpark. Los cambios son:
- Cambie a solo la CPU solo y utilice libatlas para este fin concreto.
- Coloque los conjuntos de datos en Blob Storage, que es una ubicación compartida a la que pueden acceder todos los nodos de trabajo para su uso posterior.
- Coloque las bibliotecas de Caffe compiladas en Blob Storage. Más adelante copiará dichas bibliotecas a todos los nodos mediante acciones de script para que el tiempo de compilación sea el menor posible.


### <a name="troubleshooting-an-ant-buildexception-has-occurred-exec-returned-2"></a>Solución de problemas: An Ant BuildException has occurred: exec returned (se ha producido una excepción Ant BuildException: ejecución devuelta): 2

La primera vez que se intenta realizar la compilación de CaffeOnSpark, a veces puede aparecer el siguiente mensaje

    failed to execute goal org.apache.maven.plugins:maven-antrun-plugin:1.7:run (proto) on project caffe-distri: An Ant BuildException has occurred: exec returned: 2

Limpie el repositorio de código con "make clean" y, después, ejecute "make build" para resolver este problema, siempre que tenga las dependencias correctas.

### <a name="troubleshooting-maven-repository-connection-time-out"></a>Solución de problemas: se ha superado el tiempo de espera de la conexión con el repositorio de Maven

A veces Maven genera un error de tiempo de espera de la conexión similar al fragmento de código siguiente:

    Retry:
    [INFO] Downloading: https://repo.maven.apache.org/maven2/com/twitter/chill_2.11/0.8.0/chill_2.11-0.8.0.jar
    Feb 01, 2017 5:14:49 AM org.apache.maven.wagon.providers.http.httpclient.impl.execchain.RetryExec execute
    INFO: I/O exception (java.net.SocketException) caught when processing request to {s}->https://repo.maven.apache.org:443: Connection timed out (Read failed)

Debe intentarlo de nuevo después de unos minutos.


### <a name="troubleshooting-test-failure-for-caffe"></a>Solución de problemas: error de prueba de Caffe

Al hacer la comprobación final en CaffeOnSpark, es probable que vea un error en la prueba. Es probable que tenga que ver con la codificación UTF-8, pero no debería afectar al uso de Caffe.

    Run completed in 32 seconds, 78 milliseconds.
    Total number of tests run: 7
    Suites: completed 5, aborted 0
    Tests: succeeded 6, failed 1, canceled 0, ignored 0, pending 0
    *** 1 TEST FAILED ***

## <a name="step-3-distribute-the-required-libraries-to-all-the-worker-nodes"></a>Paso 3: Distribuir las bibliotecas requeridas a todos los nodos de trabajo

El paso siguiente consiste en distribuir las bibliotecas (básicamente, las bibliotecas de CaffeOnSpark/caffe-public/distribute/lib/ and CaffeOnSpark/caffe-distri/distribute/lib/) a todos los nodos. En el paso 2, dichas bibliotecas se colocaron en Blob Storage y, en este paso, se usan acciones de script para copiarlas en todos los nodos principales y los nodos de trabajo.

Para ello, ejecute una acción de script como se muestra en el fragmento de código siguiente:

    #!/bin/bash
    hadoop fs -get wasb:///CaffeOnSpark /home/changetoyourusername/

Asegúrese de que necesita apuntar a la ubicación correcta específica para el clúster.

Dado que en el paso 2 se colocaron en Blob Storage, al que pueden acceder todos los nodos, en este paso simplemente se copian en todos los nodos.

## <a name="step-4-compose-a-caffe-model-and-run-it-in-a-distributed-manner"></a>Paso 4: Componer un modelo de Caffe y ejecutarlo de forma distribuida.

Caffe se instala después de ejecutar los pasos anteriores. El siguiente paso es escribir un modelo de Caffe. 

Caffe usa una "arquitectura expresiva", donde para crear un modelo, solo es preciso definir un archivo de configuración, no hace falta escribir código (en la mayoría de los casos). Vamos a examinar dicha arquitectura. 

El modelo que se entrena es un modelo de ejemplo para el entrenamiento de MNIST. La base de datos MNIST de dígitos manuscritos tiene un conjunto de entrenamiento de 60 000 ejemplos y un conjunto de pruebas de 10 000 ejemplos. Es un subconjunto de un conjunto mayor disponible en NIST. Los dígitos tienen un tamaño normalizado y están centrados en una imagen de tamaño fijo. CaffeOnSpark tiene algunos scripts para descargar el conjunto de datos y convertirlos al formato correcto.

CaffeOnSpark proporciona algunos ejemplos de topologías de red para el entrenamiento de MNIST. Su diseño divide la arquitectura de red (la topología de la red) y la optimiza. En este caso, se requieren dos archivos: 

el archivo "Solver" (${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt) se usa para supervisar la optimización y generar actualizaciones de parámetros. Por ejemplo, define si se usa la CPU o la GPU, cuál es el momento, cuántas iteraciones hay, etc. También define qué topología de red neuronal debe utilizar el programa (que es el segundo archivo que se necesita). Para obtener más información acerca de Solver, consulte la [documentación de Caffe](https://caffe.berkeleyvision.org/tutorial/solver.html).

En este ejemplo, dado que se va a usar la CPU, en lugar de la GPU, es preciso cambiar la última línea a:

    # solver mode: CPU or GPU
    solver_mode: CPU

![Configuración de Caffe](./media/apache-spark-deep-learning-caffe/Caffe-1.png)

Puede cambiar otras líneas si fuera necesario.

El segundo archivo (${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt) define el aspecto de la red neuronal y el archivo de entrada y salida relevante. También es preciso actualizar el archivo para que refleje la ubicación de los datos de entrenamiento. Cambie la siguiente parte en lenet_memory_train_test.prototxt (es preciso apuntar a la ubicación correcta específica del clúster):

- cambie "file:/Users/mridul/bigml/demodl/mnist_train_lmdb" a "wasb:///projects/machine_learning/image_dataset/mnist_train_lmdb"
- cambie "file:/Users/mridul/bigml/demodl/mnist_test_lmdb/" a "wasb:///projects/machine_learning/image_dataset/mnist_test_lmdb"

![Configuración de Caffe](./media/apache-spark-deep-learning-caffe/Caffe-2.png)

Para obtener más información acerca de cómo definir la red, consulte la [documentación de Caffe en el conjunto de datos MNIST](https://caffe.berkeleyvision.org/gathered/examples/mnist.html).

Para los fines de este artículo, se usa este ejemplo de MNIST. Ejecute los siguientes comandos en el nodo principal:

    spark-submit --master yarn --deploy-mode cluster --num-executors 8 --files ${CAFFE_ON_SPARK}/data/lenet_memory_solver.prototxt,${CAFFE_ON_SPARK}/data/lenet_memory_train_test.prototxt --conf spark.driver.extraLibraryPath="${LD_LIBRARY_PATH}" --conf spark.executorEnv.LD_LIBRARY_PATH="${LD_LIBRARY_PATH}" --class com.yahoo.ml.caffe.CaffeOnSpark ${CAFFE_ON_SPARK}/caffe-grid/target/caffe-grid-0.1-SNAPSHOT-jar-with-dependencies.jar -train -features accuracy,loss -label label -conf lenet_memory_solver.prototxt -devices 1 -connection ethernet -model wasb:///mnist.model -output wasb:///mnist_features_result

El comando anterior distribuye los archivos necesarios (lenet_memory_solver.prototxt y lenet_memory_train_test.prototxt) en cada contenedor YARN. El comando también establece el valor PATH relevante de cada controlador/ejecutor de Spark en LD_LIBRARY_PATH. LD_LIBRARY_PATH se define en el fragmento de código anterior y apunta a la ubicación que tiene las bibliotecas de CaffeOnSpark. 

## <a name="monitoring-and-troubleshooting"></a>Supervisión y solución de problemas

Dado que se usa el modo de clúster YARN, en cuyo caso el controlador Spark se programará para un contenedor arbitrario (y un nodo de trabajo arbitrario) en la consola solo se debería ver algo como esto:

    17/02/01 23:22:16 INFO Client: Application report for application_1485916338528_0015 (state: RUNNING)

Si desea saber lo que ha ocurrido, lo habitual es que tenga que acceder al registro del controlador de Spark, que proporciona más información. En este caso, es preciso que vaya a la interfaz de usuario de YARN para buscar los registros de YARN relevantes. Para acceder a la interfaz de usuario de YARN, use esta dirección URL: 

    https://yourclustername.azurehdinsight.net/yarnui
   
![IU de YARN](./media/apache-spark-deep-learning-caffe/YARN-UI-1.png)

Puede echar un vistazo al número de recursos que se asignan a esta aplicación concreta. Puede hacer clic en el vínculo "Scheduler" (Programador) y vera que en la aplicación hay nueve contenedores en ejecución. Se pide a YARN que proporcione ocho ejecutores, mientras que el otro contenedor es para el proceso del controlador. 

![Programador de YARN](./media/apache-spark-deep-learning-caffe/YARN-Scheduler.png)

Si aparecen errores, se pueden comprobar los registros del controlador o los registros del contenedor. En el caso de los registros del controlador, puede haga clic en el identificador de la aplicación en la interfaz de usuario de YARN y, después, hacer clic en el botón "Logs" (Registros). Los registros de controlador se escriben en stderr.

![UI de YARN 2](./media/apache-spark-deep-learning-caffe/YARN-UI-2.png)

Por ejemplo, a continuación puede ver una parte del error de los registros de controlador, que indica que se asignan demasiados ejecutores.

    17/02/01 07:26:06 ERROR ApplicationMaster: User class threw exception: java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
    java.lang.IllegalStateException: Insufficient training data. Please adjust hyperparameters or increase dataset.
        at com.yahoo.ml.caffe.CaffeOnSpark.trainWithValidation(CaffeOnSpark.scala:261)
        at com.yahoo.ml.caffe.CaffeOnSpark$.main(CaffeOnSpark.scala:42)
        at com.yahoo.ml.caffe.CaffeOnSpark.main(CaffeOnSpark.scala)
        at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
        at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:62)
        at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)
        at java.lang.reflect.Method.invoke(Method.java:498)
        at org.apache.spark.deploy.yarn.ApplicationMaster$$anon$2.run(ApplicationMaster.scala:627)

A veces, el problema puede darse en los ejecutores, en lugar de en los controladores. En este caso, es preciso comprobar los registros del contenedor. Siempre puede acceder a los registros del contenedor y, después, al contenedor con el error. Por ejemplo, este error puede aparecer al ejecutar Caffe.

    17/02/01 07:12:05 WARN YarnAllocator: Container marked as failed: container_1485916338528_0008_05_000005 on host: 10.0.0.14. Exit status: 134. Diagnostics: Exception from container-launch.
    Container id: container_1485916338528_0008_05_000005
    Exit code: 134
    Exception message: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

    Stack trace: ExitCodeException exitCode=134: /bin/bash: line 1: 12230 Aborted                 (core dumped) LD_LIBRARY_PATH=/usr/hdp/current/hadoop-client/lib/native:/usr/hdp/current/hadoop-client/lib/native/Linux-amd64-64:/home/xiaoyzhu/CaffeOnSpark/caffe-public/distribute/lib:/home/xiaoyzhu/CaffeOnSpark/caffe-distri/distribute/lib /usr/lib/jvm/java-8-openjdk-amd64/bin/java -server -Xmx4608m '-Dhdp.version=' '-Detwlogger.component=sparkexecutor' '-DlogFilter.filename=SparkLogFilters.xml' '-DpatternGroup.filename=SparkPatternGroups.xml' '-Dlog4jspark.root.logger=INFO,console,RFA,ETW,Anonymizer' '-Dlog4jspark.log.dir=/var/log/sparkapp/${user.name}' '-Dlog4jspark.log.file=sparkexecutor.log' '-Dlog4j.configuration=file:/usr/hdp/current/spark2-client/conf/log4j.properties' '-Djavax.xml.parsers.SAXParserFactory=com.sun.org.apache.xerces.internal.jaxp.SAXParserFactoryImpl' -Djava.io.tmpdir=/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/tmp '-Dspark.driver.port=43942' '-Dspark.history.ui.port=18080' '-Dspark.ui.port=0' -Dspark.yarn.app.container.log.dir=/mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005 -XX:OnOutOfMemoryError='kill %p' org.apache.spark.executor.CoarseGrainedExecutorBackend --driver-url spark://CoarseGrainedScheduler@10.0.0.13:43942 --executor-id 4 --hostname 10.0.0.14 --cores 3 --app-id application_1485916338528_0008 --user-class-path file:/mnt/resource/hadoop/yarn/local/usercache/xiaoyzhu/appcache/application_1485916338528_0008/container_1485916338528_0008_05_000005/__app__.jar > /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stdout 2> /mnt/resource/hadoop/yarn/log/application_1485916338528_0008/container_1485916338528_0008_05_000005/stderr

        at org.apache.hadoop.util.Shell.runCommand(Shell.java:933)
        at org.apache.hadoop.util.Shell.run(Shell.java:844)
        at org.apache.hadoop.util.Shell$ShellCommandExecutor.execute(Shell.java:1123)
        at org.apache.hadoop.yarn.server.nodemanager.DefaultContainerExecutor.launchContainer(DefaultContainerExecutor.java:225)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:317)
        at org.apache.hadoop.yarn.server.nodemanager.containermanager.launcher.ContainerLaunch.call(ContainerLaunch.java:83)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1142)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:617)
        at java.lang.Thread.run(Thread.java:745)


    Container exited with a non-zero exit code 134

En ese caso, es preciso obtener el identificador del contenedor con error (en este caso es container_1485916338528_0008_05_000005). Luego, tiene que ejecutar 

    yarn logs -containerId container_1485916338528_0008_03_000005

desde el nodo principal. Después de comprobar el error del contenedor, se constata que se debe a que se ha usado el modo GPU (que debe usar el modo de CPU en su lugar) en lenet_memory_solver.prototxt.

    17/02/01 07:10:48 INFO LMDB: Batch size:100
    WARNING: Logging before InitGoogleLogging() is written to STDERR
    F0201 07:10:48.309725 11624 common.cpp:79] Cannot use GPU in CPU-only Caffe: check mode.


## <a name="getting-results"></a>Obtención de resultados

Dado que se asignan ocho ejecutores y que la topología de red es sencilla, el resultado no debería tardar más de 30 minutos en aparecer. En la línea de comandos, se puede ver que el modelo se ha colocado en wasb:///mnist.model y los resultados en una carpeta llamada wasb: ///mnist_features_result.

Para obtener los resultados, ejecute

    hadoop fs -cat hdfs:///mnist_features_result/*

y obtendrá un resultado como este:

    {"SampleID":"00009597","accuracy":[1.0],"loss":[0.028171852],"label":[2.0]}
    {"SampleID":"00009598","accuracy":[1.0],"loss":[0.028171852],"label":[6.0]}
    {"SampleID":"00009599","accuracy":[1.0],"loss":[0.028171852],"label":[1.0]}
    {"SampleID":"00009600","accuracy":[0.97],"loss":[0.0677709],"label":[5.0]}
    {"SampleID":"00009601","accuracy":[0.97],"loss":[0.0677709],"label":[0.0]}
    {"SampleID":"00009602","accuracy":[0.97],"loss":[0.0677709],"label":[1.0]}
    {"SampleID":"00009603","accuracy":[0.97],"loss":[0.0677709],"label":[2.0]}
    {"SampleID":"00009604","accuracy":[0.97],"loss":[0.0677709],"label":[3.0]}
    {"SampleID":"00009605","accuracy":[0.97],"loss":[0.0677709],"label":[4.0]}

SampleID representa el identificador del conjunto de datos de MNIST y "label" es el número que identifica el modelo.


## <a name="conclusion"></a>Conclusión

En esta documentación, se ha intentado instalar CaffeOnSpark mediante la ejecución de un ejemplo sencillo. HDInsight es una plataforma de proceso distribuido en la nube totalmente administrada y es el mejor lugar para ejecutar cargas de trabajo de análisis avanzado y aprendizaje automático en conjuntos de datos grandes, mientras que para el aprendizaje profundo distribuido puede usar Caffe en HDInsight Spark para realizar tareas de aprendizaje profundo.


## <a name="seealso"></a>Consulte también
* [Información general: Apache Spark en Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Escenarios
* [Apache Spark con Machine Learning: uso de Apache Spark en HDInsight para analizar la temperatura de edificios con los datos del sistema de acondicionamiento de aire](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark con Machine Learning: uso de Spark en HDInsight para predecir los resultados de la inspección de alimentos](apache-spark-machine-learning-mllib-ipython.md)

### <a name="manage-resources"></a>Administración de recursos
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)

