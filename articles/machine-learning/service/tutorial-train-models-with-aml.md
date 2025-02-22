---
title: 'Tutorial de clasificación de imágenes: Entrenamiento de modelos'
titleSuffix: Azure Machine Learning service
description: Aprenda a entrenar un modelo de clasificación de imágenes con scikit-learn en un cuaderno de Jupyter Notebook de Python con Azure Machine Learning Service. Este tutorial es la primera de una serie de dos partes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: sdgilley
ms.author: sgilley
ms.date: 08/20/2019
ms.custom: seodec18
ms.openlocfilehash: 90f745d3ef5fd4442a184a51d82cd61b12828e15
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2019
ms.locfileid: "70036196"
---
# <a name="tutorial-train-image-classification-models-with-mnist-data-and-scikit-learn-using-azure-machine-learning"></a>Tutorial: Entrenamiento de modelos de clasificación de imágenes con los datos MNIST y scikit-learn mediante Azure Machine Learning

En este tutorial, entrenará un modelo de aprendizaje automático en los recursos de proceso remotos. Usará el flujo de trabajo de entrenamiento e implementación para el servicio Azure Machine Learning (versión preliminar) en un cuaderno de Jupyter en Python.  A continuación, puede utilizar el cuaderno como plantilla para entrenar su propio modelo de Machine Learning con sus propios datos. Este tutorial es la **primera de dos partes**.  

En este tutorial se entrena una regresión logística simple mediante el conjunto de datos [MNIST](http://yann.lecun.com/exdb/mnist/) y [scikit-learn](https://scikit-learn.org) con el servicio Azure Machine Learning. MNIST es un conjunto de datos popular que consta de 70 000 imágenes en escala de grises. Cada imagen es un dígito escrito a mano de 28×28 píxeles, que representa un número de 0 a 9. El objetivo es crear un clasificador multiclase para identificar el dígito que representa una imagen determinada.

Aprenda a realizar las siguientes acciones:

> [!div class="checklist"]
> * Configurar su entorno de desarrollo
> * Acceder a los datos y examinarlos
> * Entrene un modelo de regresión logística simple en un clúster remoto.
> * Revisar los resultados del entrenamiento y registrar el mejor modelo

En la [segunda parte de este tutorial](tutorial-deploy-models-with-aml.md), aprenderá a seleccionar un modelo e implementarlo.

Si no tiene una suscripción a Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning Service](https://aka.ms/AMLFree).

>[!NOTE]
> El código de este artículo se ha probado con el SDK de Azure Machine Learning, versión 1.0.41.

## <a name="prerequisites"></a>Requisitos previos

* Complete el [Tutorial: Comience a crear su primer experimento de ML ](tutorial-1st-experiment-sdk-setup.md) para:
    * Crear un área de trabajo
    * Crear un servidor de cuadernos en la nube
    * Inicio del panel de información de Jupyter Notebook

* Después de iniciar el panel de información de Jupyter Notebook, abra el cuaderno **tutorials/img-classification-part1-training.ipynb**.

El tutorial y el archivo **utils.py** que lo acompaña también está disponible en [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/tutorials) si desea usarlo en su propio [entorno local](how-to-configure-environment.md#local).  Asegúrese de que ha instalado `matplotlib` y `scikit-learn` en su entorno.


## <a name="start"></a>Configuración de su entorno de desarrollo

Toda la configuración para el trabajo de desarrollo puede realizarse en un cuaderno de Python. La configuración incluye las siguientes acciones:

* Importar paquetes de Python
* Conectarse a un área de trabajo para que el equipo local puede comunicarse con los recursos remotos
* Crear un experimento para realizar un seguimiento de todas las ejecuciones
* Generar un destino de proceso remoto que se usará para el entrenamiento

### <a name="import-packages"></a>Importación de paquetes

Importe los paquetes de Python que necesite en esta sesión. También visualice la versión del SDK de Azure Machine Learning:

```python
%matplotlib inline
import numpy as np
import matplotlib.pyplot as plt

import azureml.core
from azureml.core import Workspace

# check core SDK version number
print("Azure ML SDK Version: ", azureml.core.VERSION)
```

### <a name="connect-to-a-workspace"></a>Conexión a un área de trabajo

Cree un objeto de área de trabajo desde el área de trabajo existente. `Workspace.from_config()` lee el archivo **config.json** y carga los detalles en un objeto denominado `ws`:

```python
# load workspace configuration from the config.json file in the current folder.
ws = Workspace.from_config()
print(ws.name, ws.location, ws.resource_group, sep='\t')
```

### <a name="create-an-experiment"></a>Creación de un experimento

Cree un experimento para realizar un seguimiento de las ejecuciones en el área de trabajo. Un área de trabajo puede tener varios experimentos:

```python
from azureml.core import Experiment
experiment_name = 'sklearn-mnist'

exp = Experiment(workspace=ws, name=experiment_name)
```

### <a name="create-or-attach-an-existing-compute-target"></a>Creación o asociación de un destino de proceso existente

Al usar Proceso de Azure Machine Learning, un servicio administrado, los científicos de datos pueden entrenar modelos de aprendizaje automático en clústeres de máquinas virtuales de Azure, entre las que se incluyen las que tienen compatibilidad con GPU. En este tutorial, va a crear una instancia de Proceso de Azure Machine Learning como entorno de aprendizaje. El código siguiente crea los clústeres de proceso automáticamente si no existen aún en el área de trabajo.

 **La creación del destino de proceso tarda aproximadamente 5 minutos.** Si el recurso de proceso ya está en el área de trabajo, el código lo usa y omite el proceso de creación.

```python
from azureml.core.compute import AmlCompute
from azureml.core.compute import ComputeTarget
import os

# choose a name for your cluster
compute_name = os.environ.get("AML_COMPUTE_CLUSTER_NAME", "cpucluster")
compute_min_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MIN_NODES", 0)
compute_max_nodes = os.environ.get("AML_COMPUTE_CLUSTER_MAX_NODES", 4)

# This example uses CPU VM. For using GPU VM, set SKU to STANDARD_NC6
vm_size = os.environ.get("AML_COMPUTE_CLUSTER_SKU", "STANDARD_D2_V2")


if compute_name in ws.compute_targets:
    compute_target = ws.compute_targets[compute_name]
    if compute_target and type(compute_target) is AmlCompute:
        print('found compute target. just use it. ' + compute_name)
else:
    print('creating a new compute target...')
    provisioning_config = AmlCompute.provisioning_configuration(vm_size=vm_size,
                                                                min_nodes=compute_min_nodes,
                                                                max_nodes=compute_max_nodes)

    # create the cluster
    compute_target = ComputeTarget.create(
        ws, compute_name, provisioning_config)

    # can poll for a minimum number of nodes and for a specific timeout.
    # if no min node count is provided it will use the scale settings for the cluster
    compute_target.wait_for_completion(
        show_output=True, min_node_count=None, timeout_in_minutes=20)

    # For a more detailed view of current AmlCompute status, use get_status()
    print(compute_target.get_status().serialize())
```

Ahora tiene los paquetes y los recursos de proceso necesarios para entrenar un modelo en la nube.

## <a name="explore-data"></a>Exploración de los datos

Antes de entrenar un modelo, deberá comprender los datos que usa para entrenarlo. También deberá copiar los datos en la nube. A continuación, se pueden acceder a ellos a través del entorno de aprendizaje en la nube. En esta sección, aprenderá a realizar las siguientes acciones:

* Descargar el conjunto de datos de MNIST
* Mostrar algunas imágenes de ejemplo
* Cargar datos en la nube

### <a name="download-the-mnist-dataset"></a>Descargar el conjunto de datos de MNIST

Descargue el conjunto de datos de MNIST y guarde los archivos en un directorio `data` localmente. Se descargan las imágenes y etiquetas para el entrenamiento y las pruebas:

```python
import urllib.request
import os

data_folder = os.path.join(os.getcwd(), 'data')
os.makedirs(data_folder, exist_ok=True)

urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'train-labels.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-images.gz'))
urllib.request.urlretrieve('http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz',
                           filename=os.path.join(data_folder, 'test-labels.gz'))
```

Verá una salida similar a esto: ```('./data/test-labels.gz', <http.client.HTTPMessage at 0x7f40864c77b8>)```

### <a name="display-some-sample-images"></a>Mostrar algunas imágenes de ejemplo

Cargue los archivos comprimidos en matrices `numpy`. A continuación, use `matplotlib` para trazar 30 imágenes aleatorias del conjunto de datos con sus etiquetas sobre ellas. Tenga en cuenta que este paso requiere una función `load_data` que se incluye en un archivo `util.py`. Este archivo se incluye en la carpeta de ejemplo. Asegúrese de que se coloca en la misma carpeta que este cuaderno. La función `load_data` sencillamente analiza los archivos comprimidos en matrices de numpy:

```python
# make sure utils.py is in the same directory as this code
from utils import load_data

# note we also shrink the intensity values (X) from 0-255 to 0-1. This helps the model converge faster.
X_train = load_data(os.path.join(
    data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(
    data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(
    data_folder, 'test-labels.gz'), True).reshape(-1)

# now let's show some randomly chosen images from the traininng set.
count = 0
sample_size = 30
plt.figure(figsize=(16, 6))
for i in np.random.permutation(X_train.shape[0])[:sample_size]:
    count = count + 1
    plt.subplot(1, sample_size, count)
    plt.axhline('')
    plt.axvline('')
    plt.text(x=10, y=-10, s=y_train[i], fontsize=18)
    plt.imshow(X_train[i].reshape(28, 28), cmap=plt.cm.Greys)
plt.show()
```

Una muestra aleatoria de imágenes muestra:

![Muestra aleatoria de imágenes](./media/tutorial-train-models-with-aml/digits.png)

Ahora tiene una idea del aspecto de estas imágenes y el resultado de predicción esperado.

### <a name="upload-data-to-the-cloud"></a>Cargar datos en la nube

Descargó y usó los datos de entrenamiento en el equipo en el que se ejecuta el cuaderno.  En la siguiente sección, entrenará un modelo en el proceso de Azure Machine Learning remoto.  El recurso de proceso remoto también necesitará acceso a los datos. Para proporcionar acceso, cargue los datos en un almacén de datos centralizado asociado al área de trabajo. Este almacén de datos proporciona un acceso rápido a los datos al usar destinos de proceso remotos en la nube, como en el centro de datos de Azure.

Cargue los archivos de MNIST en un directorio denominado `mnist` en la raíz del almacén de datos. Consulte [Datos de acceso desde almacenes de datos](how-to-access-data.md) para más información.

```python
ds = ws.get_default_datastore()
print(ds.datastore_type, ds.account_name, ds.container_name)

ds.upload(src_dir=data_folder, target_path='mnist',
          overwrite=True, show_progress=True)
```

Ahora tiene todo lo que necesita para empezar a entrenar un modelo.

## <a name="train-on-a-remote-cluster"></a>Entrenamiento en un clúster remoto

Para esta tarea, envíe el trabajo al clúster de entrenamiento remoto que configuró anteriormente.  Para enviar un trabajo, deberá:
* Creación de directorios
* Crear un script de entrenamiento
* Crear un objeto de estimador
* Enviar el archivo

### <a name="create-a-directory"></a>Creación de directorios

Cree un directorio para proporcionar el código necesario desde el equipo al recurso remoto.

```python
import os
script_folder = os.path.join(os.getcwd(), "sklearn-mnist")
os.makedirs(script_folder, exist_ok=True)
```

### <a name="create-a-training-script"></a>Crear un script de entrenamiento

Para enviar el trabajo al clúster, primero cree un script de entrenamiento. Ejecute el siguiente código para crear el script de entrenamiento denominado `train.py` en el directorio que acaba de crear.

```python
%%writefile $script_folder/train.py

import argparse
import os
import numpy as np

from sklearn.linear_model import LogisticRegression
from sklearn.externals import joblib

from azureml.core import Run
from utils import load_data

# let user feed in 2 parameters, the location of the data files (from datastore), and the regularization rate of the logistic regression model
parser = argparse.ArgumentParser()
parser.add_argument('--data-folder', type=str, dest='data_folder', help='data folder mounting point')
parser.add_argument('--regularization', type=float, dest='reg', default=0.01, help='regularization rate')
args = parser.parse_args()

data_folder = args.data_folder
print('Data folder:', data_folder)

# load train and test set into numpy arrays
# note we scale the pixel intensity values to 0-1 (by dividing it with 255.0) so the model can converge faster.
X_train = load_data(os.path.join(data_folder, 'train-images.gz'), False) / 255.0
X_test = load_data(os.path.join(data_folder, 'test-images.gz'), False) / 255.0
y_train = load_data(os.path.join(data_folder, 'train-labels.gz'), True).reshape(-1)
y_test = load_data(os.path.join(data_folder, 'test-labels.gz'), True).reshape(-1)
print(X_train.shape, y_train.shape, X_test.shape, y_test.shape, sep = '\n')

# get hold of the current run
run = Run.get_context()

print('Train a logistic regression model with regularization rate of', args.reg)
clf = LogisticRegression(C=1.0/args.reg, solver="liblinear", multi_class="auto", random_state=42)
clf.fit(X_train, y_train)

print('Predict the test set')
y_hat = clf.predict(X_test)

# calculate accuracy on the prediction
acc = np.average(y_hat == y_test)
print('Accuracy is', acc)

run.log('regularization rate', np.float(args.reg))
run.log('accuracy', np.float(acc))

os.makedirs('outputs', exist_ok=True)
# note file saved in the outputs folder is automatically uploaded into experiment record
joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')
```

Tenga en cuenta cómo el script obtiene los datos y guarda los modelos:

+ El script de entrenamiento lee un argumento para encontrar el directorio que contiene los datos. Al enviar el trabajo más adelante, apunta al almacén de datos para este argumento: ```parser.add_argument('--data-folder', type=str, dest='data_folder', help='data directory mounting point')```

+ El script de entrenamiento guarda el modelo en un directorio denominado **outputs**. Todo lo que se escriba en este directorio se cargará automáticamente en el área de trabajo. Accederá al modelo desde este directorio más adelante en el tutorial. `joblib.dump(value=clf, filename='outputs/sklearn_mnist_model.pkl')`

+ El script de entrenamiento requiere el archivo `utils.py` para cargar el conjunto de datos correctamente. El código siguiente copia `utils.py` en `script_folder` para que se pueda acceder al archivo junto con el script de entrenamiento en el recurso remoto.

  ```python
  import shutil
  shutil.copy('utils.py', script_folder)
  ```

### <a name="create-an-estimator"></a>Crear un estimador

Se usa un objeto [estimador de SKLearn](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.sklearn.sklearn?view=azure-ml-py) para enviar la ejecución. Para crear el estimador, ejecute el código siguiente para definir estos elementos:

* El nombre del objeto estimador es `est`.
* El directorio que contiene los scripts. Todos los archivos de este directorio se cargan en los nodos del clúster para su ejecución.
* El destino de proceso. En este caso, se usará el clúster de proceso de Azure Machine Learning que creó.
* El nombre del script de entrenamiento es **train.py**.
* Los parámetros necesarios del script de entrenamiento.

En este tutorial, este destino es AmlCompute. Todos los archivos de la carpeta del script se cargan en los nodos del clúster para su ejecución. La carpeta de datos (**data_folder**) se establece para usar el almacén de datos, `ds.path('mnist').as_mount()`:

```python
from azureml.train.sklearn import SKLearn

script_params = {
    '--data-folder': ds.path('mnist').as_mount(),
    '--regularization': 0.5
}

est = SKLearn(source_directory=script_folder,
              script_params=script_params,
              compute_target=compute_target,
              entry_script='train.py')
```

### <a name="submit-the-job-to-the-cluster"></a>Envío del trabajo al clúster

Para ejecutar el experimento, envíe el objeto estimador:

```python
run = exp.submit(config=est)
run
```

Puesto que la llamada es asincrónica, devuelve un estado **Preparando** o **En ejecución** en cuanto se inicia el trabajo.

## <a name="monitor-a-remote-run"></a>Supervisión de una ejecución remota

En total, la primera ejecución tarda **aproximadamente 10 minutos**. Pero para las ejecuciones posteriores, siempre que no cambien las dependencias del script, se reutiliza la misma imagen. Por tanto, el tiempo de inicio del contenedor es mucho más rápido.

¿Qué sucede mientras espera?:

- **Creación de imágenes**: se crea una imagen de Docker que coincide con el entorno de Python especificado por el estimador. La imagen se carga en el área de trabajo. La creación y carga de la imagen tarda **aproximadamente 5 minutos**.

  Esta fase se produce una vez para cada entorno de Python, puesto que el contenedor se almacena en caché para las ejecuciones posteriores. Durante la creación de la imagen, los registros se transmiten al historial de ejecución. Puede supervisar el progreso de la creación de la imagen con estos registros.

- **Escalado**: si el clúster remoto requiere más nodos para la ejecución que los que hay disponibles actualmente, se agregan nodos adicionales de manera automática. El escalado suele tardar **aproximadamente 5 minutos.**

- **Running**: En esta fase, los archivos y scripts necesarios se envían al destino de proceso. A continuación, se montan o copian almacenes de datos. Y, luego, se ejecuta **entry_script**. Mientras se ejecuta el trabajo, **stdout** y el directorio **./logs** se transmiten al historial de ejecución. Puede supervisar el progreso de la ejecución con estos registros.

- **Posprocesamiento**: el directorio **./outputs** de la ejecución se copia en el historial de ejecución del área de trabajo para que pueda acceder a estos resultados.

Puede comprobar el progreso de un trabajo en ejecución de varias maneras. En este tutorial se usa un widget de Jupyter, así como un método `wait_for_completion`.

### <a name="jupyter-widget"></a>Widget de Jupyter

Vea el progreso de la ejecución con un [widget de Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Al igual que el envío de ejecución, el widget es asincrónico y proporciona las actualizaciones directas cada 10 a 15 segundos hasta que se completa el trabajo:

```python
from azureml.widgets import RunDetails
RunDetails(run).show()
```

El widget tendrá un aspecto similar al siguiente al final del entrenamiento:

![Widget del cuaderno](./media/tutorial-train-models-with-aml/widget.png)

Si necesita cancelar una ejecución, puede seguir [estas instrucciones](https://aka.ms/aml-docs-cancel-run).

### <a name="get-log-results-upon-completion"></a>Obtener los resultados de registros tras la finalización

El entrenamiento y la supervisión del modelo se producen en segundo plano. Espere a que el modelo haya completado el entrenamiento para poder ejecutar más código. Use `wait_for_completion` para mostrar cuando se haya completado el entrenamiento del modelo:

```python
run.wait_for_completion(show_output=False)  # specify True for a verbose log
```

### <a name="display-run-results"></a>Mostrar los resultados de la ejecución

Ahora tiene un modelo entrenado en un clúster remoto. Recupere la precisión del modelo:

```python
print(run.get_metrics())
```

La salida muestra que el modelo remoto tiene una precisión de 0,9204:

`{'regularization rate': 0.8, 'accuracy': 0.9204}`

En el siguiente tutorial explorará este modelo con más detalle.

## <a name="register-model"></a>Registro del modelo

En el último paso del entrenamiento, el script escribió el archivo `outputs/sklearn_mnist_model.pkl` en un directorio denominado `outputs` en la VM del clúster donde se ejecuta el trabajo. `outputs` es un directorio especial, ya que todo el contenido de este directorio se carga automáticamente en el área de trabajo. Este contenido aparece en el registro de ejecución en el experimento en el área de trabajo. Por lo tanto, el archivo del modelo ahora también está disponible en el área de trabajo.

Puede ver los archivos asociados a esa ejecución:

```python
print(run.get_file_names())
```

Registre el modelo en el área de trabajo para que usted u otros colaboradores puedan consultar, examinar e implementar este modelo más adelante:

```python
# register model
model = run.register_model(model_name='sklearn_mnist',
                           model_path='outputs/sklearn_mnist_model.pkl')
print(model.name, model.id, model.version, sep='\t')
```

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

También puede eliminar solo el clúster de proceso de Azure Machine Learning. Sin embargo, el escalado automático está activado y el mínimo del clúster es cero. Por tanto, este recurso concreto no acumula cargos de proceso adicionales cuando no se usa:

```python
# optionally, delete the Azure Machine Learning Compute cluster
compute_target.delete()
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial del servicio Azure Machine Learning, ha usado Python para las siguientes tareas:

> [!div class="checklist"]
> * Configurar su entorno de desarrollo
> * Acceder a los datos y examinarlos
> * Entrenamiento de varios modelos en un clúster remoto mediante la popular biblioteca de aprendizaje automático scikit-learn
> * Revisar los detalles del entrenamiento y registrar el mejor modelo

Ya podrá implementar este modelo registrado con las instrucciones de la siguiente parte de la serie de tutoriales:

> [!div class="nextstepaction"]
> [Tutorial 2: Implementación de modelos](tutorial-deploy-models-with-aml.md)
