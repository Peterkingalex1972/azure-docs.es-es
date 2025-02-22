---
title: 'Uso de .NET con MapReduce de Hadoop en HDInsight basado en Linux: Azure'
description: Aprenda a usar aplicaciones de .NET para streaming de MapReduce en HDInsight basado en Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.openlocfilehash: a1d1488840ca2b17c83f380af4fa24105bb36202
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "64729483"
---
# <a name="migrate-net-solutions-for-windows-based-hdinsight-to-linux-based-hdinsight"></a>Migración de soluciones .NET para HDInsight basado en Windows a HDInsight basado en Linux

Los clústeres de HDInsight basados en Linux usan [Mono (https://mono-project.com)](https://mono-project.com) para ejecutar aplicaciones .NET. Mono le permite usar componentes .NET como aplicaciones de MapReduce con HDInsight basado en Linux. En este documento, aprende a migrar soluciones .NET creadas para clústeres de HDInsight basados en Windows para que funcionen con Mono en HDInsight basados en Linux.

## <a name="mono-compatibility-with-net"></a>Compatibilidad de Mono con .NET

La versión 4.2.1 de Mono está incluida en la versión 3.6 de HDInsight. Para obtener más información sobre la versión de Mono incluida en HDInsight, consulte el artículo relativo a las [versiones de componentes de HDInsight](hdinsight-component-versioning.md).

Para más información sobre la compatibilidad entre Mono y .NET, vea el documento sobre [compatibilidad con Mono (https://www.mono-project.com/docs/about-mono/compatibility/)](https://www.mono-project.com/docs/about-mono/compatibility/).

> [!IMPORTANT]  
> El marco de trabajo de SCP.NET es compatible con Mono. Para obtener más información sobre cómo usar SCP.NET con Mono, consulte [Uso de Visual Studio para el desarrollo de topologías de C# para Apache Storm en HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="automated-portability-analysis"></a>Análisis de portabilidad automatizado

El [Analizador de portabilidad de .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer) puede usarse para generar un informe de las incompatibilidades entre la aplicación y Mono. Siga estos pasos con el fin de configurar el analizador para comprobar su aplicación en relación con la portabilidad a Mono:

1. Instale el [Analizador de portabilidad de .NET](https://marketplace.visualstudio.com/items?itemName=ConnieYau.NETPortabilityAnalyzer). Durante la instalación, seleccione la versión de Visual Studio que se usará.

2. En Visual Studio 2015, seleccione __Analyze__ > __Portability Analyzer Settings__ (Analizar > Configuración del analizador de portabilidad) y asegúrese de que esté marcado __4.5__ en la sección __Mono__.

    ![4.5 marcado en la sección de Mono para la configuración del analizador](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-settings.png)

    Haga clic en __OK__ (Aceptar) para guardar la configuración.

3. Seleccione __Analyze__ > __Analyze Assembly Portability__ (Analizar > Analizar portabilidad de ensamblado). Seleccione el ensamblado que contiene su solución y, después, seleccione __Open__ (Abrir) para iniciar el análisis.

4. Una vez completado el análisis, seleccione __Analyze__ > __View analysis reports__ (Analizar > Ver informes de análisis). En __Portability Analysis Results__ (Resultados de análisis de portabilidad), seleccione __Open report__ (Abrir informe) para abrir un informe.

    ![Cuadro de diálogo de resultados del analizador de portabilidad](./media/hdinsight-hadoop-migrate-dotnet-to-linux/portability-analyzer-results.png)

> [!IMPORTANT]  
> El analizador no puede detectar todos los problemas de su solución. Por ejemplo, la ruta del archivo `c:\temp\file.txt` se considera correcta si Mono se ejecuta en Windows. Sin embargo, la misma ruta de acceso no es válida en una plataforma Linux.

## <a name="manual-portability-analysis"></a>Análisis de portabilidad manual

Realice una auditoría manual del código con la información del documento sobre [portabilidad de la aplicación (https://www.mono-project.com/docs/getting-started/application-portability/)](https://www.mono-project.com/docs/getting-started/application-portability/).

## <a name="modify-and-build"></a>Modificación y compilación

Puede continuar usando Visual Studio para compilar sus soluciones .NET para HDInsight. Sin embargo, debe asegurarse de que el proyecto esté configurado para usar .NET Framework 4.5.

## <a name="deploy-and-test"></a>Implementación y prueba

Una vez que haya modificado la solución mediante las recomendaciones del Analizador de portabilidad de .NET o de un análisis manual, debe probarla con HDInsight. Probar la solución en un clúster de HDInsight basado en Linux puede revelar problemas sutiles que deben corregirse. Se recomienda que habilite el registro adicional en la aplicación durante la prueba.

Para obtener más información sobre el acceso a los registros, consulte los documentos siguientes:

* [Acceso a registros de aplicación de YARN de Apache Hadoop en HDInsight basado en Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)

## <a name="next-steps"></a>Pasos siguientes

* [Uso de MapReduce en C# en HDInsight](hadoop/apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Uso de funciones definidas por el usuario de C# con Apache Hive y Apache Pig](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Desarrollo de topologías de C# para Apache Storm en HDInsight](storm/apache-storm-develop-csharp-visual-studio-topology.md)
