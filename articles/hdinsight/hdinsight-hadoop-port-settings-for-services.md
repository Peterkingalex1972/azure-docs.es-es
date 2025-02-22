---
title: Puertos utilizados por los servicios Hadoop en HDInsight (Azure)
description: Una lista de puertos utilizados por los servicios Hadoop que se ejecutan en HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: hrasheed
ms.openlocfilehash: 34ab49378f9237a42bed869a6f6d67249b5238f9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464692"
---
# <a name="ports-used-by-apache-hadoop-services-on-hdinsight"></a>Puertos utilizados por los servicios Apache Hadoop en HDInsight

En este documento se proporciona una lista de puertos que se usan con los servicios de Apache Hadoop que se ejecutan en clústeres de HDInsight basados en Linux. También se proporciona información sobre los puertos utilizados para conectarse al clúster mediante SSH.

## <a name="public-ports-vs-non-public-ports"></a>Puertos públicos frente a puertos no públicos

Los clústeres de HDInsight basados en Linux solo exponen tres puertos públicamente en Internet: 22, 23 y 443. Estos puertos se usan para el acceso seguro al clúster mediante SSH y a los servicios expuestos a través del protocolo HTTPS seguro.

Internamente, HDInsight se implementa mediante varias Azure Virtual Machines (los nodos del clúster) que se ejecutan en una instancia de Azure Virtual Network. Desde dentro de la red virtual, puede acceder a los puertos no expuestos a través de Internet. Por ejemplo, si se conecta a uno de los nodos principales con SSH, desde él puede acceder directamente a los servicios que se ejecutan en los nodos del clúster.

> [!IMPORTANT]  
> Si no especifica una instancia de Azure Virtual Network como una opción de configuración de HDInsight, automáticamente se crea una. Sin embargo, no puede unir otras máquinas (por ejemplo, otras Azure Virtual Machines o su equipo de desarrollo de cliente) a esta red virtual.

Para unir equipos adicionales a la red virtual, debe crear primero la red virtual y luego especificarla al crear el clúster de HDInsight. Para más información, consulte [Planeamiento de una red virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

## <a name="public-ports"></a>Puertos públicos

Todos los nodos de un clúster de HDInsight se encuentran en una instancia de Azure Virtual Network y no son accesibles directamente desde Internet. Una puerta de enlace pública proporciona acceso desde Internet a los puertos siguientes, que son comunes a todos los tipos de clúster de HDInsight.

| Servicio | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- |
| sshd |22 |SSH |Conecta los clientes a sshd en el nodo primario principal. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |22 |SSH |Conecta los clientes a sshd en el nodo perimetral. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| sshd |23 |SSH |Conecta los clientes a sshd en el nodo primario secundario. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
| Ambari |443 |HTTPS |Interfaz de usuario web de Ambari. Consulte [Administración de clústeres de HDInsight con la interfaz de usuario web de Apache Ambari](hdinsight-hadoop-manage-ambari.md) |
| Ambari |443 |HTTPS |API de REST de Ambari. Consulte [Administración de clústeres de HDInsight con la API REST de Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) |
| WebHCat |443 |HTTPS |API de REST de HCatalog. Consulte [Uso de MapReduce con Curl](hadoop/apache-hadoop-use-mapreduce-curl.md) |
| HiveServer2 |443 |ODBC |Conecta a Hive mediante ODBC. Consulte [Conexión de Excel en HDInsight con el controlador ODBC de Microsoft](hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md). |
| HiveServer2 |443 |JDBC |Conecta a Apache Hive mediante JDBC. Consulte [Conexión a Apache Hive en HDInsight de Azure con el controlador JDBC de Hive](hadoop/apache-hadoop-connect-hive-jdbc-driver.md). |

Las siguientes opciones están disponibles para determinados tipos de clúster:

| Servicio | Port | Protocolo | Tipo de clúster | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Stargate |443 |HTTPS |HBase |API de REST de HBase. Consulte [Introducción a Apache HBase](hbase/apache-hbase-tutorial-get-started-linux.md). |
| Livy |443 |HTTPS |Spark |API de REST de Spark. Consulte [Envío de trabajos remotos de Apache Spark mediante Apache Livy](spark/apache-spark-livy-rest-interface.md). |
| Servidor Thrift de Spark |443 |HTTPS |Spark |El servidor Thrift de Spark que se usa para enviar consultas de Hive. Consulte [Beeline con Apache Hive en HDInsight](hadoop/apache-hadoop-use-hive-beeline.md). |
| Storm |443 |HTTPS |Storm |La interfaz de usuario web de Storm. Consulte [Implementación y administración de topologías de Apache Storm en HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md). |

### <a name="authentication"></a>Authentication

Todos los servicios expuestos públicamente en Internet se deben autenticar:

| Port | Credenciales |
| --- | --- |
| 22 o 23 |Las credenciales de usuario de SSH especificadas durante la creación del clúster. |
| 443 |El nombre de inicio de sesión (predeterminado: admin) y la contraseña que se establecieron durante la creación del clúster. |

## <a name="non-public-ports"></a>Puertos no públicos

> [!NOTE]  
> Algunos servicios solo están disponibles en determinados tipos de clúster. Por ejemplo, HBase solo está disponible en los tipos de clúster de HBase.

> [!IMPORTANT]  
> Algunos servicios solo se ejecutan en un nodo principal cada vez. Si intenta conectarse al servicio en el nodo principal primario y recibe un error, vuelva a intentarlo con el nodo principal secundario.

### <a name="ambari"></a>Ambari

| Servicio | Nodos | Port | Ruta de acceso URL | Protocolo | 
| --- | --- | --- | --- | --- |
| Interfaz de usuario web de Ambari | Nodos principales | 8080 | / | HTTP |
| API de REST de Ambari | Nodos principales | 8080 | /api/v1 | HTTP |

Ejemplos:

* API de REST de Ambari: `curl -u admin "http://10.0.0.11:8080/api/v1/clusters"`

### <a name="hdfs-ports"></a>Puertos HDFS

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Interfaz de usuario web de NameNode |Nodos principales |30070 |HTTPS |Interfaz de usuario web para ver el estado |
| Servicio de metadatos de NameNode |Nodos principales |8020 |IPC |Metadatos del sistema de archivos |
| DataNode |Todos los nodos de trabajo |30075 |HTTPS |Interfaz de usuario web para ver el estado, los registros, etc. |
| DataNode |Todos los nodos de trabajo |30010 |&nbsp; |Transferencia de datos |
| DataNode |Todos los nodos de trabajo |30020 |IPC |Operaciones de metadatos |
| NameNode secundario |Nodos principales |50090 |HTTP |Punto de control para metadatos de NameNode |

### <a name="yarn-ports"></a>Puertos de YARN

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Interfaz de usuario web de Resource Manager |Nodos principales |8088 |HTTP |Interfaz de usuario web para Resource Manager |
| Interfaz de usuario web de Resource Manager |Nodos principales |8090 |HTTPS |Interfaz de usuario web para Resource Manager |
| Interfaz de administración de Resource Manager |Nodos principales |8141 |IPC |Para envíos de aplicaciones (Hive, servidor de Hive, Pig, etc.) |
| Programador de Resource Manager |Nodos principales |8030 |HTTP |Interfaz administrativa |
| Interfaz de aplicación de Resource Manager |Nodos principales |8050 |HTTP |Dirección de la interfaz del administrador de aplicaciones |
| NodeManager |Todos los nodos de trabajo |30050 |&nbsp; |La dirección del administrador de contenedores |
| Interfaz de usuario web de NodeManager |Todos los nodos de trabajo |30060 |HTTP |Interfaz de Resource Manager |
| Dirección de escala de tiempo |Nodos principales |10200 |RPC |El servicio RPC del servicio de escala de tiempo. |
| Interfaz de usuario web de escala de tiempo |Nodos principales |8188 |HTTP |La interfaz de usuario web del servicio de escala de tiempo |

### <a name="hive-ports"></a>Puertos de Hive

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| HiveServer2 |Nodos principales |10001 |Thrift |Servicio para conectarse a Hive (Thrift/JDBC) |
| Tienda de metadatos Hive |Nodos principales |9083 |Thrift |Servicio para conectarse a metadatos de Hive (Thrift/JDBC) |

### <a name="webhcat-ports"></a>Puertos de WebHCat

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Servidor de WebHCat |Nodos principales |30111 |HTTP |Web API encima de HCatalog y otros servicios de Hadoop |

### <a name="mapreduce-ports"></a>Puertos de MapReduce

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Historial de trabajos |Nodos principales |19888 |HTTP |Interfaz de usuario web del historial de trabajos de MapReduce |
| Historial de trabajos |Nodos principales |10020 |&nbsp; |Servidor de historial de trabajos de MapReduce |
| ShuffleHandler |&nbsp; |13562 |&nbsp; |Transfiere las salidas de mapa intermedias a los reductores solicitantes |

### <a name="oozie"></a>Oozie

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Servidor de Oozie |Nodos principales |11000 |HTTP |Dirección URL del servicio de Oozie |
| Servidor de Oozie |Nodos principales |11001 |HTTP |Puerto de administración de Oozie |

### <a name="ambari-metrics"></a>Métricas de Ambari

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Escala de tiempo (historial de aplicaciones) |Nodos principales |6188 |HTTP |La interfaz de usuario web del servicio de escala de tiempo |
| Escala de tiempo (historial de aplicaciones) |Nodos principales |30200 |RPC |La interfaz de usuario web del servicio de escala de tiempo |

### <a name="hbase-ports"></a>Puertos de HBase

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| HMaster |Nodos principales |16000 |&nbsp; |&nbsp; |
| Interfaz de usuario web de información de HMaster |Nodos principales |16010 |HTTP |El puerto de la interfaz de usuario web de HBase Master |
| Servidor de región |Todos los nodos de trabajo |16020 |&nbsp; |&nbsp; |
| &nbsp; |&nbsp; |2181 |&nbsp; |El puerto que los clientes utilizan para conectarse a ZooKeeper |

### <a name="kafka-ports"></a>Puertos Kafka

| Servicio | Nodos | Port | Protocolo | DESCRIPCIÓN |
| --- | --- | --- | --- | --- |
| Agente |Nodos de trabajo |9092 |[Protocolo de conexión de Kafka](https://kafka.apache.org/protocol.html) |Se utiliza para la comunicación del cliente |
| &nbsp; |Nodos Zookeeper |2181 |&nbsp; |El puerto que los clientes utilizan para conectarse a ZooKeeper |

### <a name="spark-ports"></a>Puertos de Spark

| Servicio | Nodos | Port | Protocolo | Ruta de acceso URL | DESCRIPCIÓN |
| --- | --- | --- | --- | --- | --- |
| Servidores Thrift de Spark |Nodos principales |10002 |Thrift | &nbsp; | Servicio para conectarse a Spark SQL (Thrift/JDBC) |
| Servidor Livy | Nodos principales | 8998 | HTTP | &nbsp; | Servicio para ejecutar instrucciones, trabajos y aplicaciones |
| Jupyter Notebook | Nodos principales | 8001 | HTTP | &nbsp; | Sitio web de Jupyter Notebook |

Ejemplos:

* Livy: `curl -u admin -G "http://10.0.0.11:8998/"`. En este ejemplo, `10.0.0.11` es la dirección IP del nodo principal que hospeda el servicio Livy.
