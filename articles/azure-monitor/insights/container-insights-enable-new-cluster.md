---
title: Supervisión de un nuevo clúster de Azure Kubernetes Service (AKS) | Microsoft Docs
description: Obtenga información sobre cómo habilitar la supervisión de un nuevo clúster de Azure Kubernetes Service (AKS) con la suscripción de Azure Monitor para contenedores.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: d73ab2d5cca4f20f954a0b0e972111d3f395c3c8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65077536"
---
# <a name="enable-monitoring-of-a-new-azure-kubernetes-service-aks-cluster"></a>Habilitar la supervisión de un nuevo clúster de Azure Kubernetes Service (AKS)

En este artículo se describe cómo configurar Azure Monitor para contenedores para supervisar el clúster de Kubernetes administrado, hospedado en [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/), que está preparando para implementar en la suscripción.

Puede habilitar la supervisión de un clúster de AKS mediante uno de los métodos admitidos:

* Azure CLI
* Terraform

## <a name="enable-using-azure-cli"></a>Habilitación del uso de la CLI de Azure

Para habilitar la supervisión de un nuevo clúster de AKS creado con la CLI de Azure, siga el paso correspondiente del artículo de la guía de inicio rápido, en la sección [Creación de un clúster de AKS](../../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Si decide usar la CLI de Azure, primero debe instalar y usar la CLI localmente. Debe ejecutar la versión 2.0.59 de la CLI de Azure, o cualquier versión posterior. Para identificar la versión, ejecute `az --version`. Si necesita instalar o actualizar la CLI de Azure, consulte [Instalación de la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

## <a name="enable-using-terraform"></a>Habilitación con Terraform

Si va a [implementar un nuevo clúster de AKS mediante Terraform](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md), especifique los argumentos necesarios en el perfil [para crear un área de trabajo de Log Analytics](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_workspace.html) si no eligió especificar uno existente. 

>[!NOTE]
>Si decide usar Terraform, debe ejecutar el proveedor de Azure RM para Terraform versión 1.17.0 o superior.

Para agregar Azure Monitor para contenedores al área de trabajo, consulte [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) y complete el perfil mediante la inclusión de la [ **addon_profile** ](https://www.terraform.io/docs/providers/azurerm/r/kubernetes_cluster.html#addon_profile) y especifique **oms_agent**. 

Una vez que haya habilitado la supervisión y todas las tareas de configuración se hayan completado correctamente, puede supervisar el rendimiento de su clúster de cualquiera de estas dos maneras:

* Directamente en el clúster de AKS mediante la selección de **Health** (Mantenimiento) en el panel izquierdo.
* Si selecciona el icono de **Monitor Container insights** (Supervisar conclusiones de contenedores) en la página del clúster de AKS correspondiente al clúster seleccionado. En Azure Monitor, en el panel izquierdo, seleccione **Health** (Mantenimiento). 

  ![Opciones para la selección de Azure Monitor para contenedores en AKS](./media/container-insights-onboard/kubernetes-select-monitoring-01.png)

Después de habilitar la supervisión, pueden pasar unos 15 minutos hasta que pueda ver la métrica de estado del clúster. 

## <a name="verify-agent-and-solution-deployment"></a>Comprobar la implementación del agente y la solución
Con la versión del agente *06072018*, o cualquier versión posterior, puede comprobar que tanto el agente como la solución se han implementado correctamente. Con las versiones anteriores del agente, solo se puede comprobar la implementación del agente.

### <a name="agent-version-06072018-or-later"></a>Versión 06072018 del agente, o posterior
Ejecute el siguiente comando para comprobar que el agente se ha implementado correctamente. 

```
kubectl get ds omsagent --namespace=kube-system
```

Si la salida se parece a la siguiente, la implementación se ha realizado correctamente:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

Para comprobar la implementación de la solución, ejecute el siguiente comando:

```
kubectl get deployment omsagent-rs -n=kube-system
```

Si la salida se parece a la siguiente, la implementación se ha realizado correctamente:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>Versión del agente anterior a 06072018

Para comprobar que la versión del agente de Log Analytics anterior a *06072018* se ha implementado correctamente, ejecute el comando siguiente:  

```
kubectl get ds omsagent --namespace=kube-system
```

Si la salida se parece a la siguiente, la implementación se ha realizado correctamente:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Visualización de la configuración con la CLI
Use el comando `aks show` para obtener detalles como si la solución está habilitada o no, cuál es el valor del identificador de recursos del área de trabajo de Log Analytics y detalles de resumen acerca del clúster.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Transcurridos unos minutos, el comando se completa y devuelve información en formato JSON acerca de la solución.  Los resultados del comando deben mostrar el perfil de complemento de supervisión y son similares a la salida del ejemplo siguiente:

```
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>Pasos siguientes

* Si experimenta problemas al intentar incorporar la solución, consulte la [guía de solución de problemas](container-insights-troubleshoot.md).

* Con la supervisión habilitada para capturar métricas de estado para los pods y nodos del clúster de AKS, estas métricas de estado están disponibles en Azure Portal. Para obtener información sobre cómo usar Azure Monitor para contenedores, consulte el artículo sobre cómo [ver el estado de Azure Kubernetes Service](container-insights-analyze.md).
