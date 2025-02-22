---
title: 'Generación de recomendaciones mediante Apache Mahout y HDInsight (SSH): Azure'
description: Aprenda a usar la biblioteca de aprendizaje automático de Apache Mahout para generar recomendaciones de películas con HDInsight (Hadoop).
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: d566b57ae12520b9eee26334a67d2e10c05f8040
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64709083"
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-linux-based-apache-hadoop-in-hdinsight-ssh"></a>Generación de recomendaciones de películas mediante Apache Mahout con Apache Hadoop en HDInsight (SSH) basado en Linux

[!INCLUDE [mahout-selector](../../../includes/hdinsight-selector-mahout.md)]

Aprenda a usar la biblioteca de aprendizaje automático de [Apache Mahout](https://mahout.apache.org) con HDInsight de Azure para generar recomendaciones de películas con HDInsight.

Mahout es una biblioteca de [aprendizaje automático](https://en.wikipedia.org/wiki/Machine_learning) para Apache Hadoop. Mahout contiene algoritmos para el procesamiento de datos, como filtrado, clasificación y agrupación en clústeres. En este artículo, se usa un motor de recomendaciones para generar recomendaciones de películas que se basan en películas que sus amigos han visto.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Hadoop en HDInsight. Consulte [Introducción a HDInsight en Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Un cliente SSH. Para más información, consulte [Conexión a través de SSH con HDInsight (Apache Hadoop)](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="apache-mahout-versioning"></a>Control de versiones de Apache Mahout

Para obtener más información sobre la versión de Mahout en HDInsight, vea [Versiones de HDInsight y componentes de Apache Hadoop](../hdinsight-component-versioning.md).

## <a name="recommendations"></a>Descripción de recomendaciones

Una de las funciones que proporciona Mahout es un motor de recomendaciones. Este motor acepta datos en formato de `userID`, `itemId` y `prefValue` (la preferencia por el elemento). Mahout puede realizar entonces análisis de ocurrencias conjuntas para determinar: *los usuarios que tienen predilección por un elemento también la tienen por estos otros elementos*. Mahout determinará los usuarios con preferencias de elementos similares, lo que se puede usar para realizar recomendaciones.

El siguiente flujo de trabajo es un ejemplo simplificado que usa datos de películas:

* **Concurrencia**: a José, Alicia y Roberto les gusta *La Guerra de las galaxias*, *El imperio contraataca* y *El retorno del Jedi*. Mahout determina que a los usuarios que les gusta alguna de estas películas también les gustan las otras dos.

* **Concurrencia**: a Roberto y Alicia también les gusta *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*. Mahout determina que los usuarios a los que les gustan las tres películas anteriores también les gustan estas tres.

* **Recomendación de similitud**: como a José le gustan las tres primeras películas, Mahout examina películas que a otros usuarios con preferencias similares les han gustado, pero que José no ha visto (gustado/valorado). En este caso, Mahout recomendaría *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*.

### <a name="understanding-the-data"></a>Descripción de los datos

Para su comodidad, [GroupLens Research](https://grouplens.org/datasets/movielens/) proporciona calificaciones de películas en un formato compatible con Mahout. Estos datos están disponibles en el almacenamiento predeterminado del clúster en `/HdiSamples/HdiSamples/MahoutMovieData`.

Hay dos archivos, `moviedb.txt` y `user-ratings.txt`. El archivo `user-ratings.txt` se utiliza durante el análisis. `moviedb.txt` se utiliza para proporcionar información de texto descriptivo al ver los resultados.

Los datos del archivo user-ratings.txt tienen una estructura de `userID`, `movieID`, `userRating` y `timestamp`, que indica qué valoración dio cada usuario a una película. A continuación se muestra un ejemplo de los datos:

    196    242    3    881250949
    186    302    3    891717742
    22     377    1    878887116
    244    51     2    880606923
    166    346    1    886397596

## <a name="run-the-analysis"></a>Ejecutar el análisis

Desde una conexión SSH al clúster, use el siguiente comando para ejecutar el trabajo de recomendación:

```bash
mahout recommenditembased -s SIMILARITY_COOCCURRENCE -i /HdiSamples/HdiSamples/MahoutMovieData/user-ratings.txt -o /example/data/mahoutout --tempDir /temp/mahouttemp
```

> [!NOTE]  
> El trabajo puede tardar varios minutos en completarse y puede ejecutar varios trabajos de MapReduce.

## <a name="view-the-output"></a>Visualización de la salida

1. Cuando finalice el trabajo, use el siguiente comando para ver la salida generada.

    ```bash
    hdfs dfs -text /example/data/mahoutout/part-r-00000
    ```

    La salida tiene el siguiente aspecto:

        1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
        2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
        3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
        4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

    La primera columna es `userID`. Los valores contenidos en '[' y ']' son `movieId`:`recommendationScore`.

2. Puede usar el resultado, junto con el archivo moviedb.txt, para ofrecer más información sobre recomendaciones. En primer lugar, copie los archivos de manera local con los siguientes comandos:

    ```bash
    hdfs dfs -get /example/data/mahoutout/part-r-00000 recommendations.txt
    hdfs dfs -get /HdiSamples/HdiSamples/MahoutMovieData/* .
    ```

    Este comando copia los datos de salida a un archivo llamado **recommendations.txt** en el directorio actual, junto con los archivos de datos de la película.

3. Use el siguiente comando para crear un script de Python que busca nombres de película para los datos en la salida de recomendaciones:

    ```bash
    nano show_recommendations.py
    ```

    Cuando se abra el editor, use el siguiente texto como contenido del archivo:

   ```python
   #!/usr/bin/env python

   import sys

   if len(sys.argv) != 5:
        print "Arguments: userId userDataFilename movieFilename recommendationFilename"
        sys.exit(1)

   userId, userDataFilename, movieFilename, recommendationFilename = sys.argv[1:]

   print "Reading Movies Descriptions"
   movieFile = open(movieFilename)
   movieById = {}
   for line in movieFile:
       tokens = line.split("|")
       movieById[tokens[0]] = tokens[1:]
   movieFile.close()

   print "Reading Rated Movies"
   userDataFile = open(userDataFilename)
   ratedMovieIds = []
   for line in userDataFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           ratedMovieIds.append((tokens[1],tokens[2]))
   userDataFile.close()

   print "Reading Recommendations"
   recommendationFile = open(recommendationFilename)
   recommendations = []
   for line in recommendationFile:
       tokens = line.split("\t")
       if tokens[0] == userId:
           movieIdAndScores = tokens[1].strip("[]\n").split(",")
           recommendations = [ movieIdAndScore.split(":") for movieIdAndScore in movieIdAndScores ]
           break
   recommendationFile.close()

   print "Rated Movies"
   print "------------------------"
   for movieId, rating in ratedMovieIds:
       print "%s, rating=%s" % (movieById[movieId][0], rating)
   print "------------------------"

   print "Recommended Movies"
   print "------------------------"
   for movieId, score in recommendations:
       print "%s, score=%s" % (movieById[movieId][0], score)
   print "------------------------"
   ```

    Presione **Ctrl-X**, **Y** y, finalmente, **Entrar** para guardar los datos.

4. Ejecute el script de Python. El siguiente comando da por hecho que está en el directorio donde se descargaron todos los archivos:

    ```bash
    python show_recommendations.py 4 user-ratings.txt moviedb.txt recommendations.txt
    ```

    Este comando busca las recomendaciones generadas para el usuario con el identificador 4.

   * El archivo **user-ratings.txt** se usa para recuperar películas han recibido valoraciones.

   * El archivo **moviedb.txt** se usa para recuperar los nombres de las películas.

   * El archivo **recommendations.txt** se usa para recuperar las recomendaciones de películas para este usuario.

     La salida de este comando será similar al siguiente texto:

       Seven Years in Tibet (1997), score=5.0   Indiana Jones and the Last Crusade (1989), score=5.0   Jaws (1975), score=5.0   Sense and Sensibility (1995), score=5.0   Independence Day (ID4) (1996), score=5.0   My Best Friend's Wedding (1997), score=5.0   Jerry Maguire (1996), score=5.0   Scream 2 (1997), score=5.0   Time to Kill, A (1996), score=5.0

## <a name="delete-temporary-data"></a>Eliminar datos temporales

Los trabajos de Mahout no eliminan los datos temporales creados durante el procesamiento del trabajo. El parámetro `--tempDir` se especifica en el trabajo de ejemplo para aislar los archivos temporales en una ruta de acceso específica de forma que sea fácil eliminarlos. Para quitar los archivos temporales, use el siguiente comando:

```bash
hdfs dfs -rm -f -r /temp/mahouttemp
```

> [!WARNING]  
> Si desea volver a ejecutar el comando, también debe eliminar el directorio de salida. Use lo siguiente para eliminar este directorio:
>
> `hdfs dfs -rm -f -r /example/data/mahoutout`


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Apache Hive con HDInsight](hdinsight-use-hive.md)
* [Apache Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)