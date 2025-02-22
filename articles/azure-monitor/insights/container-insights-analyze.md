---
title: Supervisar el rendimiento del clúster de AKS con Azure Monitor para contenedores | Microsoft Docs
description: En este artículo se describe cómo puede ver y analizar los datos de registro y rendimiento con Azure Monitor para contenedores.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/22/2019
ms.author: magoedte
ms.openlocfilehash: 154848c33960cb78b10c58e7a39ddec669d4fae0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872985"
---
# <a name="understand-aks-cluster-performance-with-azure-monitor-for-containers"></a>Comprender el rendimiento del clúster de AKS con Azure Monitor para contenedores
Con Azure Monitor para contenedores, puede usar los gráficos de rendimiento y el estado de mantenimiento para supervisar la carga de trabajo de los clústeres de Azure Kubernetes Service (AKS) desde dos perspectivas. Puede supervisar directamente desde un clúster de AKS, o desde todos los clústeres de AKS de una suscripción desde Azure Monitor. La visualización de Azure Container Instances también es posible cuando se supervisa un clúster de AKS concreto.

Este artículo le ayuda a comprender dos perspectivas y cómo Azure Monitor le ayuda a evaluar, investigar y resolver rápidamente los problemas detectados.

Para obtener información acerca de cómo habilitar Azure Monitor para contenedores, consulte el artículo sobre [incorporación de Azure Monitor para contenedores](container-insights-onboard.md).

Azure Monitor proporciona una vista de varios clústeres que muestra el estado de mantenimiento de todos los clústeres de AKS que ejecutan Linux y Windows Server 2019 supervisados implementados en los grupos de recursos de sus suscripciones. Muestra los clústeres de AKS detectados que la solución no supervisa. Puede comprender de forma inmediata el estado del clúster y desde aquí puede explorar en profundidad la página de rendimiento del controlador y del nodo, o explorar para ver los gráficos de rendimiento del clúster. En el caso de los clústeres de AKS que se detectaron e identificaron como no supervisados, puede habilitar la supervisión para ellos en cualquier momento. 

Estas son las principales diferencias al supervisar los contenedores de un clúster de Windows Server con Azure Monitor en comparación con un clúster de Linux:

- La métrica RSS de memoria no está disponible para los contenedores y nodos de Windows.
- La información sobre la capacidad de almacenamiento del disco no está disponible para los nodos de Windows.
- La compatibilidad con registros dinámicos está disponible, con la excepción de los registros de contenedores de Windows.
- Solo se supervisan los entornos de pods, no los entornos de Docker.
- Con la versión preliminar, se admite un máximo de 30 contenedores de Windows Server. Esta limitación no se aplica a los contenedores de Linux. 

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Azure Portal](https://portal.azure.com). 

## <a name="multi-cluster-view-from-azure-monitor"></a>Vista de varios clústeres desde Azure Monitor

Para ver el estado de mantenimiento de todos los clústeres de AKS implementados, seleccione **Monitor** en el panel izquierdo de Azure Portal. En la sección **Insights**  (Conclusiones), seleccione **Contenedores**. 

![Ejemplo del panel de varios clústeres de Azure Monitor](./media/container-insights-analyze/azmon-containers-multiview.png)

En la pestaña **Clústeres supervisados**, aprenderá lo siguiente:

- Cuántos clústeres se encuentran en un estado crítico o incorrecto, frente a cuántos están en buen estado o no notifican ninguno (es decir, tienen un estado desconocido).
- Si todas las implementaciones de [Azure Kubernetes Engine (motor de AKS)](https://github.com/Azure/aks-engine) están en buen estado.
- Cuántos nodos y pods de usuario y de sistema se implementan por clúster.
- Cuánto espacio en disco hay disponible y si existe algún problema de capacidad.

Los estados de mantenimiento incluidos son: 

* **Correcto**: no se detectó ningún problema para la VM y funciona adecuadamente. 
* **Crítico**: se detectan uno o varios problemas críticos que deben solucionarse para restaurar el estado de funcionamiento normal según lo previsto.
* **Advertencia**: se detectan uno o varios problemas que deben solucionarse o el estado puede volverse crítico.
* **Desconocido**: si el servicio no pudo establecer ninguna conexión con el nodo o el pod, el estado se cambia a un estado desconocido.
* **No se encuentra**: se eliminó el área de trabajo, el grupo de recursos o la suscripción que contiene el área de trabajo para esta solución.
* **No autorizado**: el usuario no tiene los permisos necesarios para leer los datos del área de trabajo.
* **Error**: al intentar leer los datos del área de trabajo.
* **Configuración errónea**: Azure Monitor para contenedores no se configuró correctamente en el área de trabajo especificada.
* **No hay datos**: no se han notificado los datos en el área de trabajo durante los últimos 30 minutos.

El estado de mantenimiento calcula el estado general del clúster como *el peor* de los tres estados con una excepción. Si alguno de los tres estados es desconocido, el estado general del clúster se muestra como **Unknown** (Desconocido). 

En la tabla siguiente se proporciona un desglose del cálculo que controla los estados de mantenimiento para un clúster supervisado en la vista de varios clústeres.

| |Status |Disponibilidad |  
|-------|-------|-----------------|  
|**Pod de usuario**| | |  
| |Healthy |100% |  
| |Advertencia |90 - 99 % |  
| |Crítico |<90 % |  
| |Desconocido |Si no se notifica en los últimos 30 minutos |  
|**Pod del sistema**| | |  
| |Healthy |100% |
| |Advertencia |N/D |
| |Crítico |<100 % |
| |Desconocido |Si no se notifica en los últimos 30 minutos |
|**Node** | | |
| |Healthy |>85 % |
| |Advertencia |60 - 84 % |
| |Crítico |<60 % |
| |Desconocido |Si no se notifica en los últimos 30 minutos |

Desde la lista de clústeres puede explorar en profundidad la página **Clúster** si selecciona el nombre del clúster. A continuación, vaya a la página de rendimiento **Nodos** seleccionando la acumulación de nodos de la columna **Nodos** de ese clúster específico. También puede explorar en profundidad la página de rendimiento **Controladores** seleccionando la acumulación de la columna **Pods de usuario** o **Pods del sistema**.  

## <a name="view-performance-directly-from-an-aks-cluster"></a>Vista del rendimiento directamente desde un clúster de AKS

El acceso a Azure Monitor para contenedores está disponible directamente desde un clúster de AKS mediante la selección de la opción **Conclusiones** del panel izquierdo. La información sobre el clúster de AKS está organizada en cuatro perspectivas:

- Clúster
- Nodos 
- Controladores 
- Contenedores

Se abre la página predeterminada cuando selecciona **Conclusiones** > **Clúster**. Los cuatro gráficos de rendimiento de línea muestran las métricas de rendimiento clave del clúster. 

![Gráficos de rendimiento de ejemplo de la pestaña Clúster](./media/container-insights-analyze/containers-cluster-perfview.png)

Los gráficos de rendimiento muestran cuatro métricas de rendimiento:

- **Uso de la CPU del nodo&nbsp;%** : Una perspectiva agregada del uso de la CPU para todo el clúster. Para filtrar los resultados según el intervalo de tiempo, seleccione **Promedio**, **Mínimo**, **50.º**, **90.º**, **95.º** o **Máximo** en el selector de percentiles sobre el gráfico. Los filtros se pueden usar de forma individual o combinada. 
- **Node memory utilization&nbsp;%** (Utilización de memoria del nodo): Una perspectiva agregada del uso de la memoria para todo el clúster. Para filtrar los resultados según el intervalo de tiempo, seleccione **Promedio**, **Mínimo**, **50.º**, **90.º**, **95.º** o **Máximo** en el selector de percentiles situado encima del gráfico. Los filtros se pueden usar de forma individual o combinada. 
- **Node count** (Número de nodos): Número y estado de los nodos de Kubernetes. Los estados de los nodos de clúster que se representan son Total, Ready (Listo) y Not Ready (No está listo). Se pueden filtrar de forma individual o combinada en el selector situado encima del gráfico. 
- **Número de pods activos**: Número y estado de los pods de Kubernetes. Los estados de los pods se representan como Total, Pending (Pendiente), Running (En ejecución), Unknown (Desconocido), Succeeded (Correcto) o Failed (Con errores). Se pueden filtrar de forma individual o combinada en el selector situado encima del gráfico. 

Use las teclas de dirección izquierda y derecha para desplazarse por los puntos de datos del gráfico. Use las teclas de dirección arriba y abajo para desplazarse por las líneas de percentil. Seleccione el icono de anclaje en la esquina superior derecha de uno de los gráficos para anclarlo en el último panel de Azure que haya visto. En el panel, puede cambiar el tamaño y la posición del gráfico. Al seleccionar el gráfico en el panel, se le redirige a Azure Monitor para contenedores y se cargan el ámbito y la vista correctos.

Azure Monitor para contenedores también es compatible con el [Explorador de métricas](../platform/metrics-getting-started.md) de Azure Monitor, donde puede crear sus propios gráficos de diagrama, poner en correlación e investigar tendencias y realizar el anclaje en paneles. Desde el explorador de métricas también se pueden usar los criterios que establezca para visualizar las métricas como la base de una [regla de alertas basada en métricas](../platform/alerts-metric.md). 

## <a name="view-container-metrics-in-metrics-explorer"></a>Visualización de métricas de contenedor en el Explorador de métricas

En el Explorador de métricas se pueden ver el nodo agregado y las métricas de utilización de pods de Azure Monitor para contenedores. En la tabla siguiente se resumen los detalles que le ayudarán a aprender a usar los gráficos de métricas para visualizar las métricas de contenedor.

|Espacio de nombres | Métrica |
|----------|--------|
| insights.container/nodes | |
| | cpuUsageMillicores |
| | cpuUsagePercentage |
| | memoryRssBytes |
| | memoryRssPercentage |
| | memoryWorkingSetBytes |
| | memoryWorkingSetPercentage |
| | nodesCount |
| insights.container/pods | |
| | PodCount |

Puede [dividir](../platform/metrics-charts.md#apply-splitting-to-a-chart) una métrica para verla por dimensión y visualizar la comparación de unos segmentos con otros. Para un nodo, puede segmentar el gráfico según la dimensión *host*. Para un pod, puede segmentar el gráfico según las dimensiones siguientes:

* Controller
* Espacio de nombres de Kubernetes
* Nodo
* Fase

## <a name="analyze-nodes-controllers-and-container-health"></a>Análisis de nodos, controladores y mantenimiento de contenedores

Cuando cambie a las pestañas **Nodos**, **Controladores** y **Contenedores**, se muestra automáticamente un panel de propiedades en el lado derecho de la página. Este muestra las propiedades del elemento seleccionado, incluidas las etiquetas que haya definido para organizar los objetos de Kubernetes. Cuando se selecciona un nodo de Linux, la sección **Local Disk Capacity** (Capacidad de disco local) también muestra el espacio en disco disponible y el porcentaje usado en todos los discos presentes en el nodo. Seleccione el vínculo **>>** en el panel para verlo u ocultarlo.

![Ejemplo de paneles de propiedades de las perspectivas de Kubernetes](./media/container-insights-analyze/perspectives-preview-pane-01.png)

A medida que expande los objetos en la jerarquía, el panel de propiedades se actualiza según el objeto seleccionado. En el panel, también puede ver los eventos de Kubernetes con búsquedas de registros predefinidas si selecciona el vínculo **Ver registros de eventos de Kubernetes** en la parte superior del panel. Para obtener más información acerca de cómo ver los datos de registro de Kubernetes, consulte [Búsquedas de registros para analizar datos](container-insights-log-search.md). Mientras examina los recursos del clúster, puede ver registros de contenedor y eventos en tiempo real. Para obtener más información acerca de esta característica y la configuración necesaria para conceder acceso y controlarlo, consulte [Visualización de registros en tiempo real con Azure Monitor para contenedores](container-insights-live-logs.md). 

Use la opción **+ Agregar filtro** en la parte superior de la página para filtrar los resultados de la vista por **Servicio**, **Nodo**, **Espacio de nombres** o **Grupo de nodos**. Después de seleccionar el ámbito de filtro, seleccione uno de los valores que se muestran en el campo **Seleccionar valores**. Una vez se configura el filtro, se aplica globalmente mientras se visualiza cualquier perspectiva del clúster de AKS. La fórmula solo admite el signo igual. Puede agregar filtros adicionales que complementen el primero para restringir aún más los resultados. Por ejemplo, si especifica un filtro por **Nodo**, solo puede seleccionar **Servicio** o **Espacio de nombres** como segundo filtro.

![Ejemplo de cómo utilizar el filtro para restringir los resultados](./media/container-insights-analyze/add-filter-option-01.png)

Si especifica un filtro en una pestaña, seguirá aplicándose cuando seleccione otra. Se elimina después de seleccionar el símbolo **x** situado junto al filtro especificado. 

Al cambiar a la pestaña **Nodos**, la jerarquía de filas sigue el modelo de objetos de Kubernetes que inicia con un nodo del clúster. Expanda el nodo para ver uno o varios de los pods que se ejecutan en el nodo. Si hay más de un contenedor agrupado en un pod, se muestran como la última fila de la jerarquía. También puede ver cuántas cargas de trabajo no relacionadas con pods se ejecutan en el host si el host tiene presión de memoria o procesador.

![Ejemplo de la jerarquía de nodos de Kubernetes en la vista de rendimiento](./media/container-insights-analyze/containers-nodes-view.png)

En la lista, los contenedores de Windows Server que usan el sistema operativo Windows Server 2019 se muestran después de todos los nodos con Linux. Al expandir un nodo virtual de Windows Server, se pueden ver uno o varios pods y contenedores que se ejecutan en el nodo. Una vez seleccionado un nodo, el panel Propiedades muestra la información de versión. La información del agente se excluye porque los nodos de Windows Server no tienen un agente instalado. 

![Jerarquía de nodos de ejemplo que muestra los nodos de Windows Server](./media/container-insights-analyze/nodes-view-windows.png) 

Los nodos virtuales de Azure Container Instances que ejecutan el sistema operativo Linux se muestran tras el último nodo del clúster de AKS en la lista. Al expandir un nodo virtual de Container Instances, puede ver uno o varios pods y contenedores de Container Instances que se ejecutan en el nodo. No se recopilan ni notifican métricas para los nodos, solo para los pods.

![Ejemplo de jerarquía de nodos con Azure Container Instances enumerados](./media/container-insights-analyze/nodes-view-aci.png)

En un nodo expandido, puede explorar en profundidad desde el pod o contenedor que se ejecuta en el nodo hasta el controlador para ver los datos de rendimiento filtrados por ese controlador. Seleccione el valor de la columna **Controladores** del nodo específico.
 
![Ejemplo de exploración en profundidad del nodo al controlador en la vista de rendimiento](./media/container-insights-analyze/drill-down-node-controller.png)

Seleccione los controladores o los contenedores en la parte superior de la página para revisar el estado y el uso de recursos de dichos objetos. Para revisar el uso de memoria, en la lista desplegable **Métrica**, seleccione **RSS de memoria** o **Espacio de trabajo de memoria**. **RSS de memoria** solo se admite en Kubernetes versión 1.8, y en las versiones posteriores. En caso contrario, verá los valores de **Min&nbsp;%** (Porcentaje mínimo) como *NaN&nbsp;%* (Porcentaje de NaN), que es un valor de tipo de datos numérico que representa un valor no definido o no representable.

El **espacio de trabajo de memoria** muestra la memoria residente y la memoria virtual (caché) incluida, y es el total de lo que está usando la aplicación. **RSS de memoria** muestra solo la memoria principal, que es la memoria residente. Esta métrica muestra la capacidad real de la memoria disponible.

![Vista de rendimiento de los nodos del contenedor](./media/container-insights-analyze/containers-node-metric-dropdown.png)

De forma predeterminada, los datos de rendimiento se basan en las últimas seis horas, pero puede cambiar el período con la opción **Intervalo de tiempo** de la parte superior izquierda. También puede filtrar los resultados del intervalo de tiempo si selecciona **Mínimo**, **Promedio**, **50.º**, **90.º**, **95.º** y **Máximo** en el selector de percentiles. 

![Selección de percentil para el filtrado de datos](./media/container-insights-analyze/containers-metric-percentile-filter.png)

Cuando mueve el puntero sobre el gráfico de barras en la columna **Tendencia**, cada barra muestra el uso de CPU o de memoria, según la métrica seleccionada, dentro de un período de muestreo de 15 minutos. Después de seleccionar el gráfico de tendencias con el teclado, use las teclas Alt + Re Pág o Alt + Av Pág para recorrer cada barra por separado. Obtendrá los mismos detalles que si moviera el puntero sobre la barra.

![Ejemplo de gráfico de barras de tendencias por el que se pasa el puntero](./media/container-insights-analyze/containers-metric-trend-bar-01.png) 

En el ejemplo siguiente, para el primer nodo de la lista, *aks-nodepool1-* , el valor de **Contenedores** es 9. Este valor es un resumen del número total de contenedores implementados.

![Ejemplo de acumulación de contenedores por nodo](./media/container-insights-analyze/containers-nodes-containerstotal.png)

Esta información puede ayudarle a identificar rápidamente si tiene el equilibrio adecuado de contenedores entre los nodos del clúster. 

La información que se presenta al ver la pestaña **Nodos** se describe en la siguiente tabla.

| Columna | DESCRIPCIÓN | 
|--------|-------------|
| NOMBRE | El nombre del host. |
| Status | Vista de Kubernetes del estado del nodo. |
| %&nbsp;mínimo, %&nbsp;promedio, 50&nbsp;%, 90&nbsp;%, 95&nbsp;%, %&nbsp;máximo  | Porcentaje medio de nodos basado en el percentil durante la duración seleccionada. |
| Mínimo, Promedio, 50.º, 90.º, 95.º, Máximo | Valor real de promedio de nodos basado en el percentil para la duración seleccionada. El valor promedio se mide a partir del límite de CPU o memoria establecido para un pod. En el caso de los pods y los contenedores, es el valor promedio indicado por el host. |
| Contenedores | Número de contenedores. |
| Tiempo de actividad | Representa la hora desde un nodo iniciado o que se reinició. |
| Controller | Solo para los contenedores y pods. Muestra el controlador en el que reside. No todos los pods están en un controlador, así que algunos pueden mostrar **N/D**. | 
| Tendencia de %&nbsp;mínimo, %&nbsp;promedio, 50&nbsp;%, 90&nbsp;%, 95&nbsp;%, %&nbsp;máximo | Tendencia de gráfico de barras que presenta el porcentaje de métricas de percentil promedio del controlador. |

En el selector, elija **Controllers** (Controladores).

![Selección de la vista de controladores](./media/container-insights-analyze/containers-controllers-tab.png)

Aquí puede ver el estado de rendimiento de los controladores y a los controladores del nodo virtual de Container Instances o los pods de nodo virtual no conectados a un controlador.

![\<Vista de rendimiento de los controladores <nombre>](./media/container-insights-analyze/containers-controllers-view.png)

La jerarquía de filas comienza con un controlador. Al expandir un controlador, se pueden ver uno o varios pods. Expanda un pod, en la última fila se muestra el contenedor agrupado al pod. Desde un controlador expandido, puede explorar en profundidad el nodo en que se ejecuta para ver los datos de rendimiento filtrados por ese nodo. Los pods de Container Instances no conectados a un controlador se enumeran los últimos en la lista.

![Ejemplo de jerarquía de controladores con pods de Azure Container Instances enumerados](./media/container-insights-analyze/controllers-view-aci.png)

Seleccione el valor de la columna **Nodo** del nodo específico.

![Ejemplo de exploración en profundidad del nodo al controlador en la vista de rendimiento](./media/container-insights-analyze/drill-down-controller-node.png)

La información que se muestra al ver los controladores se describe en la siguiente tabla.

| Columna | DESCRIPCIÓN | 
|--------|-------------|
| NOMBRE | El nombre del controlador.|
| Status | Estado de la acumulación de los contenedores cuando han terminado de ejecutarse con un estado, como *OK (Correcto)* , *Terminated (Finalizado)* , *Failed (Error)* *Stopped (Detenido)* o *Paused (En pausa)* . Si el contenedor se está ejecutando, pero el estado no se mostró correctamente o el agente no lo seleccionó, y no ha respondido durante más de 30 minutos, el estado es *Unknown* (Desconocido). En la tabla siguiente se proporcionan detalles adicionales del icono de estado.|
| %&nbsp;mínimo, %&nbsp;promedio, 50&nbsp;%, 90&nbsp;%, 95&nbsp;%, %&nbsp;máximo| Promedio resumen del porcentaje medio de cada entidad para la métrica y el percentil seleccionados. |
| Mínimo, Promedio, 50.º, 90.º, 95.º, Máximo  | Resumen del rendimiento medio de memoria o CPU de millares de núcleos del contenedor para el percentil seleccionado. El valor medio se mide a partir del límite de CPU o memoria establecido para un pod. |
| Contenedores | Número total de contenedores para el controlador o pod. |
| Reinicios | Resumen del número de reinicios de los contenedores. |
| Tiempo de actividad | Representa el tiempo desde que se inició un contenedor. |
| Nodo | Solo para los contenedores y pods. Muestra el controlador en el que reside. | 
| Tendencia de %&nbsp;mínimo, %&nbsp;promedio, 50&nbsp;%, 90&nbsp;%, 95&nbsp;%, %&nbsp;máximo | Tendencia de gráfico de barras que representa las métricas de percentil promedio del controlador. |

Los iconos del campo de estado indican el estado en línea de los contenedores.
 
| Icono | Status | 
|--------|-------------|
| ![Icono de estado de ejecución listo](./media/container-insights-analyze/containers-ready-icon.png) | En ejecución (Listo)|
| ![Icono de estado de espera o en pausa](./media/container-insights-analyze/containers-waiting-icon.png) | En pausa o en espera|
| ![Icono de último estado de ejecución notificado](./media/container-insights-analyze/containers-grey-icon.png) | Se notificó por última vez como en ejecución, pero no ha respondido en más de 30 minutos|
| ![Icono de estado correcto](./media/container-insights-analyze/containers-green-icon.png) | Se detuvo correctamente o no se pudo detener|

El icono de estado muestra un número en función de lo que proporciona el pod. Muestra los dos peores estados y, al pasar el ratón sobre el estado, muestra un estado de resumen de todos los pods del contenedor. Si no hay un estado listo, el valor de estado muestra **(0)** .

En el selector, elija **Containers** (Contenedores).

![Selección de la vista de contenedores](./media/container-insights-analyze/containers-containers-tab.png)

Aquí puede ver el estado del rendimiento de los contenedores de Azure Container Instances y Azure Kubernetes. 

![\<Name> Vista de rendimiento de los contenedores](./media/container-insights-analyze/containers-containers-view.png)

En un contenedor, puede explorar en profundidad un pod o un nodo para ver los datos de rendimiento filtrados por ese objeto. Seleccione el valor de la columna **Pod** o de **Nodo** del contenedor específico.

![Ejemplo de exploración en profundidad del nodo a los contenedores en la vista de rendimiento](./media/container-insights-analyze/drill-down-controller-node.png)

La información que se muestra al ver los contenedores se describe en la siguiente tabla.

| Columna | DESCRIPCIÓN | 
|--------|-------------|
| NOMBRE | El nombre del controlador.|
| Status | Estado de los contenedores, si lo hay. En la tabla siguiente se proporcionan detalles adicionales del icono de estado.|
| %&nbsp;mínimo, %&nbsp;promedio, 50&nbsp;%, 90&nbsp;%, 95&nbsp;%, %&nbsp;máximo | El resumen del porcentaje medio de cada entidad para la métrica y el percentil seleccionados. |
| Mínimo, Promedio, 50.º, 90.º, 95.º, Máximo | Resumen del rendimiento medio de memoria o CPU de millares de núcleos del contenedor para el percentil seleccionado. El valor medio se mide a partir del límite de CPU o memoria establecido para un pod. |
| Pod | Contenedor en el que reside el pod.| 
| Nodo |  Nodo donde reside el contenedor. | 
| Reinicios | Representa el tiempo desde que se inició un contenedor. |
| Tiempo de actividad | Representa el tiempo desde que se inicia o reinicia un contenedor. |
| Tendencia de %&nbsp;mínimo, %&nbsp;promedio, 50&nbsp;%, 90&nbsp;%, 95&nbsp;%, %&nbsp;máximo | Tendencia de gráfico de barras que representa el porcentaje de métricas de percentil promedio del contenedor. |

Los iconos en el campo de estado indican los estados en línea de los pods, tal como se describen en la tabla siguiente.
 
| Icono | Status |  
|--------|-------------|  
| ![Icono de estado de ejecución listo](./media/container-insights-analyze/containers-ready-icon.png) | En ejecución (Listo)|  
| ![Icono de estado de espera o en pausa](./media/container-insights-analyze/containers-waiting-icon.png) | En pausa o en espera|  
| ![Icono de último estado de ejecución notificado](./media/container-insights-analyze/containers-grey-icon.png) | Última notificación: en ejecución, pero no ha respondido en más de 30 minutos|  
| ![Icono de estado finalizado](./media/container-insights-analyze/containers-terminated-icon.png) | Se detuvo correctamente o no se pudo detener|  
| ![Icono de estado de error](./media/container-insights-analyze/containers-failed-icon.png) | Estado de error |  

## <a name="workbooks"></a>Workbooks

Los libros combinan texto, [consultas de registros](../log-query/query-language.md), [métricas ](../platform/data-platform-metrics.md)y parámetros para crear informes interactivos avanzados. Otros miembros del equipo con acceso a los mismos recursos de Azure pueden editar los libros.

Azure Monitor para contenedores incluye cuatro libros introductorios:

- **Capacidad de disco**: Presenta gráficos interactivos del uso de cada uno de los discos que se presenta en el nodo dentro de un contenedor por las siguientes perspectivas:

    - Porcentaje de uso de disco (para todos los discos).
    - Espacio libre en disco (para todos los discos).
    - Una cuadrícula que muestra el disco de cada nodo, su porcentaje de espacio usado, la tendencia del porcentaje de espacio usado, el espacio libre en el disco (GiB) y la tendencia de espacio libre en el disco (GiB). Cuando se selecciona una fila de la tabla, a continuación se muestran debajo de dicha fila el porcentaje de espacio usado y el espacio libre en el disco (GiB). 

- **E/S de disco**: Presenta gráficos interactivos del uso de cada uno de los discos que se presenta en el nodo dentro de un contenedor por las siguientes perspectivas:

    - Resumen de E/S de disco en todos los discos por bytes de lectura por segundo, bytes de escritura por segundo y tendencias de lectura y escritura en bytes por segundo.
    - Ocho gráficos de rendimiento que muestran indicadores clave de rendimiento que le ayudarán a medir e identificar los cuellos de botella de E/S de disco.

- **Kubelet**: incluye dos cuadrículas que muestran las estadísticas operativas del nodo principal:

    - La información general sobre la cuadrícula de nodos resume el número total de operaciones, errores y operaciones correctas en porcentaje, junto con la tendencia de cada nodo.
    - La información general por tipo de operación resume para cada operación el número total de operaciones, de errores y de operaciones correctas en porcentaje, y la tendencia.

- **Network** (Red): Presenta gráficos interactivos de uso de red para cada adaptador de red de nodos y una cuadrícula que presenta los indicadores clave de rendimiento para ayudar a medir el rendimiento de los adaptadores de red.

Para acceder a estos libros, seleccione cada uno de ellos en la lista desplegable **Ver libros**.

![Lista desplegable Ver libros](./media/container-insights-analyze/view-workbooks-dropdown-list.png)

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Creación de alertas de rendimiento con Azure Monitor para contenedores](container-insights-alerts.md) para aprender a crear alertas si se produce un uso elevado de la CPU y la memoria, con el fin de admitir sus DevOps o sus procesos y procedimientos operativos.
- En los [ejemplos de consultas de registro](container-insights-log-search.md#search-logs-to-analyze-data) encontrará consultas predefinidas y ejemplos que puede evaluar o personalizar para las alertas, visualización o análisis de los clústeres.
