---
title: Supervisión y administración de Azure HDInsight mediante la interfaz de usuario web de Ambari
description: Aprenda a usar Ambari para supervisar y administrar clústeres de HDInsight basado en Linux. Con este documento aprende a usar la interfaz de usuario web de Ambari incluida con clústeres de HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: hrasheed
ms.openlocfilehash: d0641a1c058db59acd5e9a64b10bb57b334f82bd
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442066"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Administración de clústeres de HDInsight con la interfaz de usuario web de Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica la administración y la supervisión de un clúster de Apache Hadoop al brindar una API de REST y una interfaz de usuario web fácil de usar. Ambari se incluye en los clústeres de HDInsight y, además, se usa para supervisar el clúster y realizar cambios en la configuración.

Con este documento aprende a usar la interfaz de usuario web de Ambari con un clúster de HDInsight.

## <a id="whatis"></a>¿Qué es Apache Ambari?

[Apache Ambari](https://ambari.apache.org) simplifica la administración de Hadoop al proporcionar una interfaz de usuario de web fácil de usar. Puede usar Ambari para administrar y supervisar los clústeres de Hadoop. Los desarrolladores pueden integrar estas funcionalidades en sus aplicaciones mediante el uso de las [API de REST de Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Conectividad

La interfaz de usuario web de Ambari está disponible en el clúster de HDInsight en `https://CLUSTERNAME.azurehdinsight.net`, donde `CLUSTERNAME` es el nombre del clúster.

> [!IMPORTANT]  
> La conexión a Ambari en HDInsight requiere HTTPS. Cuando se le solicite autenticación, use el nombre de la cuenta de administrador y la contraseña que proporcionó cuando se creó el clúster.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

A pesar de que es posible tener acceso directamente a través de Internet a Ambari en su clúster, algunos vínculos de la interfaz de usuario web de Ambari (como JobTracker) no estarán expuestos en Internet. Para acceder a estos servicios, debe crear un túnel SSH. Para más información, consulte el documento [Uso de un túnel SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interfaz de usuario web de Ambari

> [!WARNING]  
> No todas las características de la interfaz de usuario web de Ambari son compatibles con HDInsight. Para más información, consulte la sección sobre las [operaciones no admitidas](#unsupported-operations) de este documento.

Cuando se conecte a la interfaz de usuario web de Ambari, se le pedirá que se autentique en la página. Use el nombre de usuario y la contraseña de administrador del clúster (valor predeterminado, Admin) que utilizó durante la creación del clúster.

Cuando se abra la página, observe la barra que se encuentra en la parte superior. Esta barra contiene la siguiente información y controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

|item |DESCRIPCIÓN |
|---|---|
|Logotipo de Ambari|Abre el panel, que puede usarse para supervisar el clúster.|
|Cluster name # ops|Muestra el número de operaciones de Ambari en curso. Seleccione el nombre del clúster o **# ops** para ver una lista de las operaciones en segundo plano.|
|# alerts|Muestra alertas de advertencia o alertas críticas del clúster, si las hay.|
|panel|Muestra el panel.|
|Services|Información y ajustes de configuración de los servicios en el clúster.|
|Hosts|Información y ajustes de configuración de los nodos del clúster.|
|Alertas|Un registro de información, advertencias y alertas críticas.|
|Administración|Servicios/pila de software que están instalados en el clúster, información de la cuenta de servicio y seguridad Kerberos.|
|Botón Admin|Administración de Ambari, configuración del usuario y cierre de sesión.|

## <a name="monitoring"></a>Supervisión

### <a name="alerts"></a>Alertas

La lista siguiente contiene los estados de alerta comunes usados por Ambari:

* **OK (CORRECTO)**
* **Warning (ADVERTENCIA)**
* **CRITICAL (CRÍTICA)**
* **UNKNOWN (DESCONOCIDO)**

Las alertas, con la excepción de **OK**, hacen que la entrada **# alerts** en la parte superior de la página muestre el número de alertas. Seleccione esta entrada para ver las alertas y sus estados.

Las alertas se organizan en varios grupos predeterminados, que se pueden ver desde la página **Alertas** .

![página de alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Para administrar los grupos, use el menú **Actions** (Acciones) y seleccione **Manage Alert Groups** (Administración de grupos de alertas).

![administrar cuadro de diálogo de grupos de alertas](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

También puede administrar métodos de alerta y crear notificaciones de alerta a partir del menú **Actions** (Acciones) seleccionando __Manage Alert Notifications__ (Administrar notificaciones de alerta). Se muestran las notificaciones actuales. También puede crear notificaciones desde aquí. Se pueden enviar notificaciones a través de **CORREO ELECTRÓNICO** o **SNMP** cuando se producen combinaciones de alerta/gravedad específicas. Por ejemplo, puede enviar un mensaje de correo electrónico cuando alguna de las alertas del grupo **YARN Default** (Predeterminado de YARN) está definida en **Critical** (Crítica).

![cuadro de diálogo Crear alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por último, al seleccionar __Manage Alert Settings__ (Administrar configuración de alerta) en el menú __Actions__ (Acciones) podrá probar el número de veces que debe aparecer una alerta antes de enviar una notificación. Esta opción puede usarse para evitar notificaciones de errores transitorios.

### <a name="cluster"></a>Clúster

La pestaña **Metrics** (Métricas) del panel contiene una serie de widgets que facilitan la supervisión del estado del clúster de un solo vistazo. Varios widgets, como **CPU Usage**(Uso de CPU), proporcionan información adicional al hacer clic en ellos.

![panel con métricas](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La pestaña **Heatmaps** (Mapas térmicos) muestran las métricas como mapas térmicos coloreados, desde el verde hasta el rojo.

![panel con mapas térmicos](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para más información sobre los nodos del clúster, seleccione **Hosts**. A continuación, seleccione el nodo específico que le interesa.

![detalles del host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Services

La barra lateral **Services** (Servicios) del panel proporciona información rápida del estado de los servicios que se ejecutan en el clúster. Se usan varios iconos para indicar el estado o las acciones que se deben realizar. Por ejemplo, si es necesario reciclar un servicio, se muestra un símbolo de reciclaje amarillo.

![barra lateral de servicios](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> Los servicios mostrados difieren entre las versiones y los tipos de clúster de HDInsight. Los servicios mostrados aquí pueden ser diferentes de los servicios que se muestran para el clúster.

Al seleccionar un servicio se muestra información más detallada sobre el mismo.

![información de resumen de servicio](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Vínculos rápidos

Algunos servicios muestran un vínculo **Quick Links** (Vínculos rápidos) en la parte superior de la página. Este vínculo puede usarse para tener acceso a interfaces de usuario web específicas del servicio, como:

* **Job History** : el historial de trabajos de MapReduce.
* **Resource Manager** : la interfaz de usuario del administrador de recursos de YARN.
* **NameNode** : la interfaz de usuario de NameNode del Sistema de archivos distribuido de Hadoop (HDFS).
* **Oozie Web UI** : interfaz de usuario de Oozie.

Seleccione cualquiera de estos vínculos para abrir una pestaña nueva del explorador, donde aparecerá la página seleccionada.

> [!NOTE]  
> Al seleccionar la entrada **Vínculos rápidos** para un servicio, puede aparecer un error de "servidor no encontrado". Si se produce este error, debe usar un túnel SSH al emplear la entrada **Vínculos rápidos** para este servicio. Para más información, consulte [Uso de la tunelización SSH con HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="management"></a>Administración

### <a name="ambari-users-groups-and-permissions"></a>Usuarios, grupos y permisos de Ambari

Se puede trabajar con usuarios, grupos y permisos cuando se usa un clúster de HDInsight [unido a un dominio](./domain-joined/hdinsight-security-overview.md). Para más información sobre el uso de la interfaz de usuario de administración de Ambari en un clúster unidos a un dominio, consulte [Manage domain-joined HDInsight clusters](./domain-joined/hdinsight-security-overview.md) (Administración de clústeres de HDInsight unidos a dominio).

> [!WARNING]  
> No cambie la contraseña del guardián Ambari (hdinsightwatchdog) en el clúster de HDInsight basado en Linux. El cambio de la contraseña impide usar acciones de script o realizar operaciones de escalado con el clúster.

### <a name="hosts"></a>Hosts

La página **Hosts** muestra todos los hosts existentes en el clúster. Siga estos pasos para administrar los hosts.

![página de hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> No se debe agregar, retirar o volver a programar un host con los clústeres de HDInsight.

1. Seleccione los hosts que desee administrar.

2. Use el menú **Actions** para seleccionar la acción que desea realizar:

    |item |DESCRIPCIÓN |
    |---|---|
    |Iniciar todos los componentes|Inicia todos los componentes en el host.|
    |Detener todos los componentes|Detiene todos los componentes en el host.|
    |Reiniciar todos los componentes|Detiene e inicia todos los componentes en el host.|
    |Activar el modo de mantenimiento|Suprime las alertas del host. Este modo se debería habilitar si va a realizar acciones que generan alertas. Por ejemplo, detener e iniciar un servicio.|
    |Desactivar el modo de mantenimiento|Devuelve el host al modo de alertas normal.|
    |Stop|Detiene DataNode o NodeManagers en el host.|
    |Start|Inicia DataNode o NodeManagers en el host.|
    |Reinicio|Detiene e inicia DataNode o NodeManagers en el host.|
    |Dar de baja|Quita un host del clúster. **No use esta acción en clústeres de HDInsight.**|
    |Dar de alta|Agrega un host anteriormente retirado al clúster. **No use esta acción en clústeres de HDInsight.**|

### <a id="service"></a>Servicios

En la página **Dashboard** (Panel) o **Services** (Servicios), use el botón **Actions** (Acciones) que se encuentra en la parte inferior de la lista de servicios para detener e iniciar todos los servicios.

![Service Actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> Aunque **Add Service** (Agregar servicio) aparece en este menú, no debe usarse para agregar servicios al clúster de HDInsight. Se deben agregar nuevos servicios deben mediante una acción de script durante el aprovisionamiento del clúster. Para obtener más información sobre el uso de las acciones de script, consulte [Personalización de clústeres de HDInsight mediante la acción de script](hdinsight-hadoop-customize-cluster-linux.md).

A pesar de que el botón **Actions** puede reiniciar todos los servicios, con frecuencia se desea iniciar, detener o reiniciar un servicio específico. Use los siguientes pasos para realizar acciones sobre un servicio individual:

1. En la página **Dashboard** (Panel) o **Services** (Servicios), seleccione un servicio.

2. En la parte superior de la pestaña **Summary** (Resumen), use el botón **Service Actions** (Acciones de servicio) y seleccione la acción que se realizará. Con esto se reinicia el servicio en todos los nodos.

    ![acción de servicio](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar algunos servicios mientras el clúster está en ejecución puede generar alertas. Para evitarlo, puede usar el botón **Service Actions** (Acciones de servicio) para habilitar el **modo de mantenimiento** del servicio antes del reinicio.

3. Una vez que se selecciona una acción, la entrada **# op** que aparece en la parte superior de la página aumenta para mostrar que se está produciendo una operación en segundo plano. La lista de operaciones en segundo plano aparecerá si está configurada de ese modo.

   > [!NOTE]  
   > Si habilitó el **modo de mantenimiento** para el servicio, recuerde deshabilitarlo con el botón **Service Actions** (Acciones de servicio) una vez finalizada la operación.

Para configurar un servicio, use los siguientes pasos:

1. En la página **Dashboard** (Panel) o **Services** (Servicios), seleccione un servicio.

2. Seleccione la pestaña **Configs** (Configuraciones). Aparecerá la configuración actual. También aparecerá una lista de las configuraciones anteriores.

    ![configuraciones](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Use los campos que aparecen para modificar la configuración y, a continuación, seleccione **Save**(Guardar). O bien, seleccione una configuración anterior y, a continuación, seleccione **Make current** (Convertir en actual) para volver a la configuración anterior.

## <a name="ambari-views"></a>Vistas de Ambari

Las vistas de Ambari permiten a los desarrolladores conectar elementos de interfaz de usuario a la interfaz de usuario web de Ambari mediante el [marco de vistas de Apache Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight proporciona las siguientes vistas con los tipos de clúster de Hadoop:

* Vista de Hive: la vista de Hive permite ejecutar consultas de Hive directamente desde el explorador web. Puede guardar consultas, ver resultados, guardar resultados en el almacenamiento del clúster o descargar los resultados en el sistema local. Para más información sobre el uso de vistas de Hive, consulte [Uso de vistas de Apache Hive con HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Vista de Tez: la vista de Tez permite comprender mejor y optimizar los trabajos. Puede ver información sobre cómo se ejecutan los trabajos de Tez y qué recursos se usan.

## <a name="unsupported-operations"></a>Operaciones no admitidas

Las operaciones de Ambari siguientes no son compatibles con HDInsight:

* __Migración del servicio Recopilador de métricas__. Cuando se ve información en el servicio Recopilador de métricas, una de las acciones disponibles en el menú Acciones del servicio es la __migración del recopilador de métricas__. Esto no es compatible con HDInsight.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar la [API de REST de Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) con HDInsight.