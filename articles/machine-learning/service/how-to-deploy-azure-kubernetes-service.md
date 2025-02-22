---
title: Implementación de modelos en Azure Kubernetes Service
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo implementar modelos de Azure Machine Learning Service como un servicio web con Azure Kubernetes Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 07/08/2019
ms.openlocfilehash: 490085da1e8f6b8e151168433836d59329887c6e
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69623958"
---
# <a name="deploy-a-model-to-an-azure-kubernetes-service-cluster"></a>Implementación de un modelo en un clúster de Azure Kubernetes Service

Aprenda a usar el servicio Azure Machine Learning para implementar un modelo como un servicio web en Azure Kubernetes Service (AKS). Azure Kubernetes Service se recomienda para implementaciones de producción a gran escala. Úselo si necesita una o varias de las siguientes funcionalidades:

- __Tiempo de respuesta rápido__.
- __Escalado automático__ del servicio implementado.
- Opciones de  __aceleración de hardware__, como GPU y matrices de puertas programables (FPGA).

> [!IMPORTANT]
> El escalado de clústeres no se proporciona a través del SDK de Azure Machine Learning. Para más información acerca del escalado de nodos en un clúster de Azure Kubernetes Service, consulte [Escalado del número de nodos en un clúster de Azure Kubernetes Service](../../aks/scale-cluster.md).

En Azure Kubernetes Service, la implementación se realiza en un clúster de AKS que está __conectado a su área de trabajo__. Hay dos formas de conectar un clúster de AKS a un área de trabajo:

* Cree el clúster de AKS mediante el SDK de Azure Machine Learning Service, la CLI de Machine Learning o Azure Portal. Este proceso conecta automáticamente el clúster al área de trabajo.
* Conecte el clúster de AKS existente a un área de trabajo de Azure Machine Learning Service. Un clúster se puede conectar mediante el SDK de Azure Machine Learning Service, la CLI de Machine Learning o Azure Portal.

> [!IMPORTANT]
> El proceso de creación o de conexión es una tarea que se realiza una sola vez. Una vez que un clúster de AKS está conectado al área de trabajo, puede usarlo para las implementaciones. Cuando deje de necesitar el clúster de AKS puede desasociarlo o eliminarlo. Una vez que lo haga, ya no podrá implementar realizar ninguna implementación en el clúster.

## <a name="prerequisites"></a>Requisitos previos

- Un área de trabajo de Azure Machine Learning. Para más información, consulte [Creación de un área de trabajo de Azure Machine Learning Service](how-to-manage-workspace.md).

- Un modelo de Machine Learning registrado en el área de trabajo. Si no tiene un modelo registrado, consulte el artículo en el que se explica [cómo y dónde se implementan los modelos](how-to-deploy-and-where.md).

- La [extensión de la CLI de Azure para Machine Learning Service](reference-azure-machine-learning-cli.md), el [SDK de Python para Azure Machine Learning](https://aka.ms/aml-sdk) o la [extensión de Visual Studio Code para Azure Machine Learning](how-to-vscode-tools.md).

- En los fragmentos de código de __Python__ de este artículo se supone que se han establecido las siguientes variables:

    * `ws`: establézcalo en su área de trabajo.
    * `model`: establézcalo en el modelo registrado.
    * `inference_config`: establézcalo en la configuración de inferencia del modelo.

    Para más información acerca de cómo establecer estas variables, consulte el artículo en el que se explica [cómo y dónde se implementan los modelos](how-to-deploy-and-where.md).

- En los fragmentos de código de la __CLI__ de este artículo se supone que ha creado un documento `inferenceconfig.json`. Para más información acerca cómo crear este documento, consulte el artículo en el que se explica [cómo y dónde se implementan los modelos](how-to-deploy-and-where.md).

## <a name="create-a-new-aks-cluster"></a>Creación de un clúster de AKS

**Tiempo estimado**: aproximadamente 20 minutos.

Crear o asociar un clúster de AKS es un proceso único en el área de trabajo. Puede volver a usar este clúster con diferentes implementaciones. Si elimina el clúster o el grupo de recursos que lo contiene, tendrá que crear un nuevo clúster la próxima vez que tenga que realizar una implementación. Puede tener varios clústeres de AKS asociados al área de trabajo.

> [!TIP]
> Si quiere proteger el clúster de AKS mediante una instancia de Azure Virtual Network, primero debe crear la red virtual. Para más información, consulte [Protección de los trabajos de experimentación e inferencia con Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

Si desea crear un clúster de AKS para __desarrollo__,  __validación__y __pruebas__, en lugar de producción, en __cluster purpose__ puede especificar __dev test__.

> [!WARNING]
> Si establece `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, el clúster que se crea no es adecuado para un tráfico de nivel de producción y puede aumentar los tiempos de inferencia. Los clústeres de desarrollo y pruebas tampoco garantizan la tolerancia a errores. Se recomiendan al menos dos CPU virtuales para los clústeres de desarrollo y pruebas.

En los siguientes ejemplos se muestra cómo crear un clúster de AKS mediante el SDK y la CLI:

**Uso del SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget

# Use the default configuration (you can also provide parameters to customize this).
# For example, to create a dev/test cluster, use:
# prov_config = AksCompute.provisioning_configuration(cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
prov_config = AksCompute.provisioning_configuration()

aks_name = 'myaks'
# Create the cluster
aks_target = ComputeTarget.create(workspace = ws,
                                    name = aks_name,
                                    provisioning_configuration = prov_config)

# Wait for the create process to complete
aks_target.wait_for_completion(show_output = True)
```

> [!IMPORTANT]
> Para [`provisioning_configuration()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py), si elige valores personalizados para `agent_count` y `vm_size`, y `cluster_purpose` no es `DEV_TEST`, deberá asegurarse de que `agent_count` multiplicado por `vm_size` sea mayor o igual que 12 CPU virtuales. Por ejemplo, si usa un valor de `vm_size` de "Standard_D3_v2", que tiene cuatro CPU virtuales, debe elegir un valor de `agent_count` de 3 o mayor.
>
> El SDK de Azure Machine Learning no admite escalar un clúster de AKS. Para escalar los nodos en el clúster, use la interfaz de usuario para el clúster de AKS en Azure Portal. Solo puede cambiar el número de nodos, no el tamaño de máquina virtual del clúster.

Para más información acerca de las clases, los métodos y los parámetros que se usan en este ejemplo, consulte los siguientes documentos de referencia:

* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.provisioning_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [ComputeTarget.create](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#create-workspace--name--provisioning-configuration-)
* [ComputeTarget.wait_for_completion](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#wait-for-completion-show-output-false-)

**Uso de la CLI**

```azurecli
az ml computetarget create aks -n myaks
```

Para más información, consulte la referencia de [az ml computetarget create ask](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-aks).

## <a name="attach-an-existing-aks-cluster"></a>Asociación de un clúster de AKS ya existente

**Tiempo estimado:** Aproximadamente 5 minutos.

Si ya tiene un clúster de AKS en su suscripción a Azure y es de la versión 1.12.## puede usarlo para implementar la imagen.

> [!TIP]
> El clúster de AKS existente puede estar en la misma región de Azure que su área de trabajo de Azure Machine Learning Service.
>
> Si quiere proteger el clúster de AKS mediante una instancia de Azure Virtual Network, primero debe crear la red virtual. Para más información, consulte [Protección de los trabajos de experimentación e inferencia con Azure Virtual Network](how-to-enable-virtual-network.md#aksvnet).

> [!WARNING]
> Cuando se asocia un clúster de AKS a un área de trabajo, puede definir cómo utilizará el clúster estableciendo el parámetro `cluster_purpose`.
>
> Si no establece el parámetro `cluster_purpose`, o establece `cluster_purpose = AksCompute.ClusterPurpose.FAST_PROD`, el clúster deberá tener al menos 12 CPU virtuales disponibles.
>
> Si establece `cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST`, el clúster no necesitará tener 12 CPU virtuales. Se recomiendan al menos dos CPU virtuales para desarrollo y pruebas. No obstante, un clúster que está configurado para desarrollo y pruebas no es adecuado para un tráfico de nivel de producción y puede aumentar los tiempos de inferencia. Los clústeres de desarrollo y pruebas tampoco garantizan la tolerancia a errores.

Para más información acerca de cómo crear un clúster de AKS mediante la CLI de Azure o Azure Portal, consulte los artículos siguientes:

* [Creación de un clúster de AKS (CLI)](https://docs.microsoft.com/cli/azure/aks?toc=%2Fazure%2Faks%2FTOC.json&bc=%2Fazure%2Fbread%2Ftoc.json&view=azure-cli-latest#az-aks-create)
* [Creación de un clúster de AKS: Portal](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough-portal?view=azure-cli-latest)

En los ejemplos siguientes se muestra cómo asociar un clúster de AKS 1.12.## existente a un área de trabajo:

**Uso del SDK**

```python
from azureml.core.compute import AksCompute, ComputeTarget
# Set the resource group that contains the AKS cluster and the cluster name
resource_group = 'myresourcegroup'
cluster_name = 'myexistingcluster'

# Attach the cluster to your workgroup. If the cluster has less than 12 virtual CPUs, use the following instead:
# attach_config = AksCompute.attach_configuration(resource_group = resource_group,
#                                         cluster_name = cluster_name,
#                                         cluster_purpose = AksCompute.ClusterPurpose.DEV_TEST)
attach_config = AksCompute.attach_configuration(resource_group = resource_group,
                                         cluster_name = cluster_name)
aks_target = ComputeTarget.attach(ws, 'myaks', attach_config)
```

Para más información acerca de las clases, los métodos y los parámetros que se usan en este ejemplo, consulte los siguientes documentos de referencia:

* [AksCompute.attach_configuration()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.akscompute?view=azure-ml-py#attach-configuration-resource-group-none--cluster-name-none--resource-id-none--cluster-purpose-none-)
* [AksCompute.ClusterPurpose](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute.clusterpurpose?view=azure-ml-py)
* [AksCompute.attach](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computetarget?view=azure-ml-py#attach-workspace--name--attach-configuration-)

**Uso de la CLI**

Para asociar un clúster existente mediante la CLI, es preciso obtener el identificador de recurso del clúster existente. Para obtener este valor, use el comando siguiente. Reemplace `myexistingcluster` por el nombre del clúster de AKS. Reemplace `myresourcegroup` por el grupo de recursos que contiene el clúster:

```azurecli
az aks show -n myexistingcluster -g myresourcegroup --query id
```

Este comando devuelve un valor similar al siguiente texto:

```text
/subscriptions/{GUID}/resourcegroups/{myresourcegroup}/providers/Microsoft.ContainerService/managedClusters/{myexistingcluster}
```

Para conectar el clúster existente a un área de trabajo, use el siguiente comando. Reemplace `aksresourceid` por el valor devuelto por el comando anterior. Reemplace `myresourcegroup` por el grupo de recursos que contiene el área de trabajo. Reemplace `myworkspace` por el nombre del área de trabajo.

```azurecli
az ml computetarget attach aks -n myaks -i aksresourceid -g myresourcegroup -w myworkspace
```

Para más información, consulte la referencia de [az ml computetarget attach aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks).

## <a name="deploy-to-aks"></a>Implementación en AKS

Para implementar un modelo en Azure Kubernetes Service, cree una __configuración de implementación__ que describa los recursos de proceso necesarios. Por ejemplo, el número de núcleos y la memoria. También necesita una __configuración de inferencia__, que describe el entorno necesario para hospedar el modelo y el servicio web. Para más información sobre cómo crear la configuración de inferencia, consulte [Cómo y dónde implementar modelos](how-to-deploy-and-where.md).

### <a name="using-the-sdk"></a>Uso del SDK

```python
from azureml.core.webservice import AksWebservice, Webservice
from azureml.core.model import Model

aks_target = AksCompute(ws,"myaks")
# If deploying to a cluster configured for dev/test, ensure that it was created with enough
# cores and memory to handle this deployment configuration. Note that memory is also used by
# things such as dependencies and AML components.
deployment_config = AksWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "myservice", [model], inference_config, deployment_config, aks_target)
service.wait_for_deployment(show_output = True)
print(service.state)
print(service.get_logs())
```

Para más información acerca de las clases, los métodos y los parámetros que se usan en este ejemplo, consulte los siguientes documentos de referencia:

* [AksCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.aks.akscompute?view=azure-ml-py)
* [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py)
* [Model.deploy](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config--deployment-config-none--deployment-target-none-)
* [Webservice.wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>Uso de la CLI

Para realizar una implementación con la CLI, use el siguiente comando. Reemplace `myaks` por el nombre del destino de proceso de AKS. Reemplace `mymodel:1` por el nombre y la versión del modelo registrado. Reemplace `myservice` por el nombre que quiere asignar a este servicio:

```azurecli-interactive
az ml model deploy -ct myaks -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

Para obtener más información, consulte la referencia [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy). 

### <a name="using-vs-code"></a>Uso de Visual Studio Code

Para obtener información acerca del uso de Visual Studio Code, consulte cómo se [realiza la implementación en AKS a través de la extensión de Visual Studio Code](how-to-vscode-tools.md#deploy-and-manage-models).

> [!IMPORTANT] 
> La implementación a través de Visual Studio Code requiere que el clúster de AKS se cree o se adjunte al área de trabajo de antemano.

## <a name="web-service-authentication"></a>Autenticación de servicio web

Cuando se implementa en Azure Kubernetes Service, la autenticación __basada en claves__ se habilita de manera predeterminada. También puede habilitar la autenticación de __tokens__. La autenticación de tokens requiere que los clientes utilicen una cuenta de Azure Active Directory para solicitar un token de autenticación, que se usa para hacer solicitudes al servicio implementado.

Para __deshabilitar__ la autenticación, establezca el parámetro `auth_enabled=False` al crear la configuración de implementación. En el siguiente ejemplo se deshabilita la autenticación mediante el SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, auth_enabled=False)
```

Para obtener información sobre la autenticación desde una aplicación cliente, consulte [Consumir un modelo de Azure Machine Learning que está implementado como un servicio web](how-to-consume-web-service.md).

### <a name="authentication-with-keys"></a>Autenticación con claves

Si la autenticación con claves está habilitada, puede usar el método `get_keys` para recuperar una clave de autenticación primaria y secundaria:

```python
primary, secondary = service.get_keys()
print(primary)
```

> [!IMPORTANT]
> Si necesita regenerar una clave, use [`service.regen_key`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice(class)?view=azure-ml-py).

### <a name="authentication-with-tokens"></a>Autenticación con tokens

Para habilitar la autenticación por tokens, establezca el parámetro `token_auth_enabled=True` cuando cree o actualice una implementación. En el ejemplo siguiente se habilita la autenticación por tokens con el SDK:

```python
deployment_config = AksWebservice.deploy_configuration(cpu_cores=1, memory_gb=1, token_auth_enabled=True)
```

Si la autenticación por tokens está habilitada, puede usar el método `get_token` para recuperar un token JWT y la hora de expiración de los tokens:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Tendrá que solicitar un nuevo token después de la hora del token `refresh_by`.
>
> Microsoft recomienda crear el área de trabajo de Azure Machine Learning en la misma región que el clúster de Azure Kubernetes Service. Para autenticarse con un token, el servicio web hará una llamada a la región en la que se crea el área de trabajo de Azure Machine Learning. Si la región del área de trabajo no está disponible, no se podrá capturar un token para el servicio web, incluso si el clúster está en una región distinta del área de trabajo. Esto da como resultado que el servicio Autenticación de Azure AD no esté disponible hasta que la región del área de trabajo vuelva a estarlo. Además, cuanto mayor sea la distancia entre la región del clúster y la región del área de trabajo, más tiempo tardará la captura de un token.

## <a name="update-the-web-service"></a>Actualizar el servicio web

[!INCLUDE [aml-update-web-service](../../../includes/machine-learning-update-web-service.md)]

## <a name="next-steps"></a>Pasos siguientes

* [Protección de experimentos e inferencias en una red virtual](how-to-enable-virtual-network.md)
* [Cómo implementar un modelo con una imagen personalizada de Docker](how-to-deploy-custom-docker-image.md)
* [Solución de problemas de implementación](how-to-troubleshoot-deployment.md)
* [Protección de los servicios web de Azure Machine Learning con SSL](how-to-secure-web-service.md)
* [Consumir un modelo de ML que está implementado como un servicio web](how-to-consume-web-service.md)
* [Supervisión de los modelos de Azure Machine Learning con Application Insights](how-to-enable-app-insights.md)
* [Recopilar datos de modelos en producción](how-to-enable-data-collection.md)
