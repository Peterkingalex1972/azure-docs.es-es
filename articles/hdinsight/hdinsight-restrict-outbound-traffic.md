---
title: Configuración de la restricción del tráfico de red saliente para clústeres de Azure HDInsight
description: Obtenga información sobre cómo configurar la restricción del tráfico de red saliente para clústeres de Azure HDInsight.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 63e23275a68ddde9385bb252dcb872d02c5cea08
ms.sourcegitcommit: 9dc7517db9c5817a3acd52d789547f2e3efff848
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68405977"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Configuración del tráfico de red saliente para clústeres de Azure HDInsight mediante el uso de un firewall (Versión preliminar)

Este artículo proporciona los pasos para proteger el tráfico saliente del clúster de HDInsight con Azure Firewall. En los pasos siguientes se supone que va a configurar Azure Firewall para un clúster existente. Si va a implementar un nuevo clúster detrás de un firewall, cree primero el clúster de HDInsight y una subred y, a continuación, siga los pasos descritos en esta guía.

## <a name="background"></a>Fondo

Los clústeres de Azure HDInsight se implementan normalmente en su propia red virtual. El clúster tiene dependencias de servicios fuera de dicha red virtual que requieren que el acceso a la red funcione correctamente.

Hay varias dependencias que requieren tráfico de entrada. El tráfico entrante de administración no se puede enviar a través de un dispositivo de firewall. Las direcciones de origen de este tráfico son conocidas y se publican [aquí](hdinsight-management-ip-addresses.md). También puede crear reglas de grupo de seguridad de red (NSG) con esa información para proteger el tráfico entrante a los clústeres.

Las dependencias del tráfico de salida de HDInsight aislado se definen casi por completo con nombres de dominio completos, que no tienen direcciones estáticas tras ellos. La falta de direcciones estáticas significa que no se pueden usar grupos de seguridad de red (NSG) para bloquear el tráfico saliente de un clúster. Las direcciones cambian con tal frecuencia que no se pueden configurar reglas en función de la resolución de nombres actual ni usarlas para configurar reglas de grupo de seguridad de red.

La solución para proteger las direcciones de salida es el uso de un dispositivo de firewall que pueda controlar el tráfico saliente en función de los nombres de dominio. Azure Firewall puede restringir el tráfico saliente HTTP y HTTPS en función del nombre de dominio completo del destino o [etiquetas de FQDN](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configuración de Azure Firewall con HDInsight

A continuación, se proporciona un resumen de los pasos para bloquear la salida del clúster de HDInsight existente con Azure Firewall:
1. Cree un firewall.
1. Agregue reglas de aplicación al firewall.
1. Agregue reglas de red al firewall.
1. Cree una tabla de enrutamiento.

### <a name="create-a-new-firewall-for-your-cluster"></a>Creación de un nuevo firewall para el clúster

1. Cree una subred llamada **AzureFirewallSubnet** en la red virtual donde exista el clúster. 
1. Cree un nuevo firewall llamado **Test-FW01** mediante los pasos descritos en [Tutorial: Implementación y configuración de Azure Firewall mediante Azure Portal](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configuración del firewall con reglas de aplicación

Cree una colección de reglas de aplicación que permita al clúster enviar y recibir comunicaciones importantes.

Seleccione el nuevo firewall **Test-FW01** en Azure Portal. Haga clic en **Reglas** en **Configuración** > **Colección de reglas de aplicación** > **Agregar colección de reglas de aplicación**.

![Título: Agregar colección de reglas de aplicación](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

En la pantalla **Agregar colección de reglas de aplicación**, realice los pasos siguientes:

1. Escriba un **Nombre** y **Prioridad**, haga clic en **Permitir** desde el menú desplegable **Acción** y escriba las siguientes reglas en la **Sección Etiquetas de FQDN**:

   | **Nombre** | **Dirección de origen** | **Etiqueta de FQDN** | **Notas** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight y Windows Update | Necesario para los servicios de HDI |

1. Agregue las siguientes reglas en la **Sección FQDN de destino**:

   | **Nombre** | **Dirección de origen** | **Protocolo:Puerto** | **FQDN de destino** | **Notas** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Permite la actividad de inicio de sesión de Windows |
   | Rule_3 | * | https:443,http:80 | <nombre_de_la_cuenta_de_almacenamiento.blob.core.windows.net> | Si el clúster está respaldado por WASB, agregue una regla para WASB. Para usar SOLO conexiones https, asegúrese de que la opción ["se requiere transferencia segura"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está habilitada en la cuenta de almacenamiento. |

1. Haga clic en **Agregar**.

   ![Título: Escribir los detalles de la colección de reglas de aplicación](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configuración del firewall con reglas de red

Cree las reglas de red para configurar correctamente el clúster de HDInsight.

1. Seleccione el nuevo firewall **Test-FW01** en Azure Portal.
1. Haga clic en **Reglas** en **Configuración** > **Colección de reglas de red** > **Agregar colección de reglas de red**.
1. En la pantalla **Agregar colección de reglas de red**, escriba un **Nombre** y **Prioridad** y haga clic en **Permitir** desde el menú desplegable **Acción**.
1. Cree las siguientes reglas en la sección **Direcciones IP**:

   | **Nombre** | **Protocolo** | **Dirección de origen** | **Dirección de destino** | **Puerto de destino** | **Notas** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Servicio de hora |
   | Rule_2 | Cualquiera | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Si usa Enterprise Security Package (ESP), agregue una regla de red en la sección Direcciones IP que permita la comunicación con AAD-DS para los clústeres de ESP. Puede encontrar las direcciones IP de los controladores de dominio en la sección de AAD-DS en el portal. | 
   | Rule_3 | TCP | * | Dirección IP de la cuenta de Data Lake Storage | `*` | Si usa Azure Data Lake Storage, puede agregar una regla de red en la sección Direcciones IP para solucionar una incidencia de SNI con ADLS Gen1 y Gen2. Esta opción enrutará el tráfico al firewall, lo que podría ocasionar mayores costos para grandes cargas de datos pero se registrará el tráfico y será auditable en los registros del firewall. Determine la dirección IP de la cuenta de Data Lake Storage. Puede usar un comando de Powershell, como `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")`, para resolver el nombre de dominio completo en una dirección IP.|
   | Rule_4 | TCP | * | * | `12000` | (Opcional) Si usa Log Analytics, cree una regla de red en la sección Direcciones IP para habilitar la comunicación con el área de trabajo de Log Analytics. |

1. Cree las siguientes reglas en la sección **Etiquetas de servicio**:

   | **Nombre** | **Protocolo** | **Dirección de origen** | **Etiquetas de servicio** | **Puerto de destino** | **Notas** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | SQL | `1433` | Configure una regla de red en la sección Etiquetas de servicio para SQL que le permitirá registrar y auditar el tráfico SQL, a menos que haya configurado puntos de conexión de servicio para SQL Server en la subred de HDInsight, los cuales omiten el firewall. |

1. Haga clic en **Agregar** para finalizar la creación de la colección de reglas de red.

   ![Título: Escribir los detalles de la colección de reglas de aplicación](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Creación y configuración de una tabla de rutas

Cree una tabla de rutas con las siguientes entradas:

1. Seis direcciones de [esta lista de direcciones IP de administración que requiere HDInsight](../hdinsight/hdinsight-management-ip-addresses.md) con un próximo salto de tipo **Internet**:
    1. Cuatro direcciones IP para todos los clústeres en todas las regiones
    1. Dos direcciones IP que son específicas de la región donde se crea el clúster
1. Una ruta de aplicación virtual para la dirección IP 0.0.0.0/0 con el próximo salto establecido en la dirección IP privada de la instancia de Azure Firewall.

Por ejemplo, para configurar la tabla de rutas para un clúster creado en la región de Estados Unidos "Centro de EE. UU.", use los pasos siguientes:

1. Inicie sesión en el Portal de Azure.
1. Seleccione el firewall de Azure **Test-FW01**. Copie la **Dirección IP privada** que aparece en la página **Información general**. En este ejemplo usaremos la **dirección de ejemplo 10.1.1.4**.
1. Cree una nueva tabla de rutas.
1. Haga clic en **Rutas** en **Configuración**.
1. Haga clic en **Agregar** para crear rutas para las direcciones IP de la tabla siguiente.

| Nombre de ruta | Prefijo de dirección | Tipo de próximo salto | Siguiente dirección de salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | N/D |
| 23.99.5.239 | 23.99.5.239/32 | Internet | N/D |
| 168.61.48.131 | 168.61.48.131/32 | Internet | N/D |
| 138.91.141.162 | 138.91.141.162/32 | Internet | N/D |
| 13.67.223.215 | 13.67.223.215/32 | Internet | N/D |
| 40.86.83.253 | 40.86.83.253/32 | Internet | N/D |
| 0.0.0.0 | 0.0.0.0/0 | Aplicación virtual | 10.1.1.4 |

Complete la configuración de la tabla de rutas:

1. Asigne la tabla de rutas que ha creado a la subred de HDInsight; para ello, haga clic en **Subredes** en **Configuración** y, a continuación, en **Asociar**.
1. En la pantalla **Asociar subred**, seleccione la red virtual en la que se creó el clúster en y la **Subred de HDInsight** que usó para el clúster de HDInsight.
1. Haga clic en **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Nodo perimetral o tráfico de aplicación personalizado

Los pasos anteriores permitirán que el clúster funcione sin incidencias. Deberá configurar las dependencias para dar cabida a las aplicaciones personalizadas que se ejecutan en los nodos perimetrales, si procede.

Las dependencias de la aplicación se identificar y agregar a Azure Firewall o a la tabla de rutas.

Se deben crear rutas para el tráfico de la aplicación para evitar incidencias de enrutamiento asimétrico.

Si las aplicaciones tienen otras dependencias, deben agregarse a Azure Firewall. Crear reglas de aplicación para permitir el tráfico HTTP/HTTPS y reglas de red para todo lo demás.

## <a name="logging"></a>Registro

Azure Firewall puede enviar registros a algunos sistemas de almacenamiento diferentes. Para obtener instrucciones sobre la configuración del registro para el firewall, siga los pasos de [Tutorial: Supervisión de métricas y registros de Azure Firewall](../firewall/tutorial-diagnostics.md).

Una vez haya completado la configuración del registro, si está registrando datos en Log Analytics puede ver el tráfico bloqueado con una consulta como la siguiente:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

La integración de Azure Firewall con los registros de Azure Monitor resulta útil la primera vez que se pone una aplicación en funcionamiento, cuando aún no se conocen todas las dependencias de la aplicación. Puede obtener más información acerca de los registros de Azure Monitor en [Análisis de datos de registro en Azure Monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="access-to-the-cluster"></a>Acceso al clúster
Después de configurar correctamente el firewall, puede usar el punto de conexión interno (`https://<clustername>-int.azurehdinsight.net`) para acceder a Ambari desde la red virtual. Para usar el punto de conexión público (`https://<clustername>.azurehdinsight.net`) o el punto de conexión de SSH (`<clustername>-ssh.azurehdinsight.net`), asegúrese de que dispone de las rutas adecuadas en la tabla de rutas y la configuración de las reglas del grupo de seguridad de red para evitar la incidencia de enrutamiento asimétrico que se explica [aquí](https://docs.microsoft.com/azure/firewall/integrate-lb).

## <a name="configure-another-network-virtual-appliance"></a>Configuración de otra aplicación virtual de red

>[!Important]
> La siguiente información **solo** es necesaria si desea configurar una aplicación virtual de red (NVA) distinta a Azure Firewall.

Las instrucciones anteriores le ayudan a configurar Azure Firewall para restringir el tráfico saliente desde el clúster de HDInsight. Azure Firewall está configurado automáticamente para permitir el tráfico para muchos de los escenarios comunes más importantes. Si desea usar otra aplicación virtual de red, deberá configurar manualmente algunas características adicionales. Tenga en cuenta lo siguiente al configurar la aplicación virtual de red:

* Los servicios compatibles con puntos de conexión de servicio deben configurarse con puntos de conexión de servicio.
* Las dependencias de dirección IP son para tráfico que no sea HTTP/HTTPS (tráfico TCP y UDP).
* Los puntos de conexión HTTP/HTTPS de FQDN se pueden colocar en el dispositivo NVA.
* Los puntos de conexión HTTP/HTTPS de carácter comodín son dependencias que pueden variar en función de varios calificadores.
* Asigne la tabla de rutas que creó a la subred de HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Dependencias compatibles con los puntos de conexión de servicio

| **Punto de conexión** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dependencias de dirección IP

| **Punto de conexión** | **Detalles** |
|---|---|
| \*:123 | Comprobación de reloj NTP. El tráfico se comprueba en varios puntos de conexión en el puerto 123. |
| Las direcciones IP se publican [aquí](hdinsight-management-ip-addresses.md) | Estas son el servicio HDInsight |
| Direcciones IP privadas de AAD-DS para clústeres de ESP |
| \*:16800 para la activación de Windows de KMS |
| \*12000 para Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Dependencias HTTP/HTTPS de FQDN

>[!Important]
> La lista siguiente proporciona solo algunos de los FQDN más importantes. Puede obtener la lista completa de FQDN para la configuración de la aplicación virtual de red [en este archivo](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Punto de conexión**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Pasos siguientes

* [Arquitectura de red virtual de Azure HDInsight](hdinsight-virtual-network-architecture.md)
