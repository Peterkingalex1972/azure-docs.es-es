---
title: Registro de instancias de contenedor con registros de Azure Monitor
description: Aprenda a enviar registros desde instancias de contenedores de Azure a registros de Azure Monitor.
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: overview
ms.date: 07/09/2019
ms.author: danlep
ms.openlocfilehash: 4099bc0b15f02faade02f47aeb00fb7c4b4a3332
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325880"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Registro de instancias de contenedor con registros de Azure Monitor

Las áreas de trabajo de Log Analytics proporcionan una ubicación centralizada para almacenar y consultar datos de registro no solo de los recursos de Azure, sino también de los recursos locales y de los recursos de otras nubes. Azure Container Instances incluye compatibilidad integrada para el envío de datos a los registros de Azure Monitor.

Para enviar datos de instancias de contenedores a los registros de Azure Monitor, debe especificar una clave y un identificador de área de trabajo de Log Analytics al crear un grupo de contenedores. En las secciones siguientes se describe tanto la creación de un grupo de contenedores con el registro habilitado como la consulta de registros.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Requisitos previos

Para habilitar el registro en las instancias de los contenedores, necesita lo siguiente:

* [Área de trabajo de Log Analytics](../azure-monitor/learn/quick-create-workspace.md)
* [CLI de Azure](/cli/azure/install-azure-cli) (o [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Obtención de las credenciales de Log Analytics

Azure Container Instances necesita permiso para enviar datos a su área de trabajo de Log Analytics. Para conceder este permiso y habilitar el registro, es preciso proporcionar el identificador del área de trabajo de Log Analytics y una de sus claves (la principal o la secundaria) al crear el grupo de contenedores.

Para obtener el identificador y la clave principal del área de trabajo de Log Analytics:

1. Vaya al área de trabajo de Log Analytics en Azure Portal
1. En **Configuración**, seleccione **Configuración avanzada**.
1. Seleccione **Orígenes conectados** > **Servidores de Windows** (o **Servidores de Linux**, ya que tanto el identificador como las claves son las mismas para ambos)
1. Anote el valor de:
   * **ID. DEL ÁREA DE TRABAJO**
   * **CLAVE PRINCIPAL**

## <a name="create-container-group"></a>Creación de un grupo de contenedores

Ahora que tiene el identificador y la clave principal del área de trabajo de Log Analytics, ya puede crear un grupo de contenedores con el registro habilitado.

En los ejemplos siguientes se muestran dos maneras de crear un grupo de contenedores con un solo contenedor [fluentd][fluentd]: CLI de Azure y la CLI de Azure con una plantilla de YAML. El contenedor Fluentd genera varias líneas de salida en su configuración predeterminada. Dado que esta salida se envía a su área de trabajo de Log Analytics, sirve para mostrar la visualización y consulta de registros.

### <a name="deploy-with-azure-cli"></a>Implementación con la CLI de Azure

Para implementar con la CLI de Azure, especifique los parámetros `--log-analytics-workspace` y `--log-analytics-workspace-key` en el comando [az container create][az-container-create]. Antes de ejecutar el siguiente comando, reemplace los dos valores de área de trabajo por los valores obtenidos en el paso anterior (y actualice el nombre del grupo de recursos).

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implementación con YAML

Utilice este método si prefiere implementar grupos de contenedores con YAML. El siguiente fragmento de código YAML define un grupo de contenedores con un solo contenedor. Copie el código YAML en un nuevo archivo y sustituya `LOG_ANALYTICS_WORKSPACE_ID` y `LOG_ANALYTICS_WORKSPACE_KEY` por los valores que obtuvo en el paso anterior. Guarde el archivo con el nombre **deploy-aci.yaml**.

```yaml
apiVersion: 2018-10-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

A continuación, ejecute el siguiente comando para implementar el grupo de contenedores. Reemplace `myResourceGroup` por un grupo de recursos de su suscripción (o bien cree antes un grupo de recursos denominado "myResourceGroup"):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Debería recibir una respuesta de Azure con detalles de implementación poco después de emitir el comando.

## <a name="view-logs-in-azure-monitor-logs"></a>Visualización de registros de Azure Monitor

Una vez que haya implementado el grupo de contenedores, las primeras entradas de registro pueden tardar varios minutos (hasta 10) en aparecer en Azure Portal. Para ver los registros del grupo de contenedores:

1. Vaya al área de trabajo de Log Analytics en Azure Portal
1. En **General**, seleccione **Registros**.  
1. Escriba la consulta siguiente: `search *`
1. Seleccione **Ejecutar**.

Debería ver que la consulta `search *` muestra varios resultados. Si no ve ninguno, espere unos minutos y seleccione el botón **Ejecutar** para volver a ejecutar la consulta. De forma predeterminada, las entradas del registro aparecen en formato de **tabla**. Luego puede expandir una fila para ver el contenido de una entrada de registro individual.

![Resultados de Búsqueda de registros en Azure Portal][log-search-01]

## <a name="query-container-logs"></a>Consulta de registros de contenedores

Los registros de Azure Monitor incluyen un amplio [lenguaje de consulta][query_lang] para poder extraer información de miles de líneas de la salida del registro.

El agente de registro de Azure Container Instances envía las entradas a la tabla `ContainerInstanceLog_CL` de su área de trabajo de Log Analytics. La estructura básica de una consulta consiste en la tabla de origen (`ContainerInstanceLog_CL`) seguida de una serie de operadores separados por el carácter de barra vertical (`|`). Puede encadenar varios operadores para refinar los resultados y realizar funciones avanzadas.

Para ver los resultados de una consulta de ejemplo, pegue la siguiente consulta en el cuadro de texto de consulta (en "Mostrar convertidor de lenguaje heredado") y seleccione el botón **EJECUTAR** para ejecutar la consulta. Esta consulta muestra todas las entradas de registro cuyo campo "Message" contiene la palabra "warn":

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

También se admiten consultas más complejas. Por ejemplo, esta consulta muestra solo las entradas de registro del grupo de contenedores "mycontainergroup001" generadas en la última hora:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Pasos siguientes

### <a name="azure-monitor-logs"></a>Registros de Azure Monitor

Para más información acerca de cómo realizar consultas en registros y configurar alertas en registros de Azure Monitor, consulte:

* [Descripción de las búsquedas de registros en los registros de Azure Monitor](../log-analytics/log-analytics-log-search.md)
* [Alertas unificadas en Azure Monitor](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>Supervisión de la CPU y la memoria de los contenedores

Para obtener información acerca de la supervisión de los recursos de CPU y de memoria de un instancia de contenedor, consulte:

* [Supervisión de los recursos de los contenedores en Azure Container Instances](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create