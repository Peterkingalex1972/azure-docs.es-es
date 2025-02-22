---
title: 'Tutorial: Entrenamiento del primer modelo de Machine Learning'
titleSuffix: Azure Machine Learning service
description: En este tutorial aprenderá los patrones de diseño básico en Azure Machine Learning Service y entrenará un modelo de scikit-learn simple basado en el conjunto de datos sobre diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 07/20/2019
ms.openlocfilehash: cee5801826c78bdee51ba5afb14d6776a1191702
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051635"
---
# <a name="tutorial-train-your-first-ml-model"></a>Tutorial: Entrenamiento del primer modelo de Machine Learning

Este tutorial es la **segunda parte de dos**. En el tutorial anterior, [creó un área de trabajo y eligió un entorno de desarrollo](tutorial-1st-experiment-sdk-setup.md). En este tutorial aprenderá los patrones de diseño básico en Azure Machine Learning Service y entrenará un modelo de scikit-learn simple basado en el conjunto de datos sobre diabetes. Después de completar este tutorial, tendrá el conocimiento práctico del SDK que le permitirá escalar verticalmente al desarrollo de experimentos y flujos de trabajo más complejos.

En este tutorial, ha aprendido las tareas siguientes:

> [!div class="checklist"]
> * Conexión con el área de trabajo y creación de un experimento
> * Carga de datos y entrenamiento de modelos scikit-learn
> * Visualización de los resultados del entrenamiento en el portal
> * Recuperación del mejor modelo

## <a name="prerequisites"></a>Requisitos previos

El único requisito previo es ejecutar la parte primera de este tutorial, [Configuración del entorno y el área de trabajo](tutorial-1st-experiment-sdk-setup.md).

En esta parte del tutorial, ejecutará el código `tutorials/tutorial-1st-experiment-sdk-train.ipynb` en el cuaderno de Jupyter Notebook de ejemplo que se abrió al final de la parte uno. Este artículo le guía a través del mismo código que se encuentra en el cuaderno.

## <a name="connect-workspace-and-create-experiment"></a>Conexión con el área de trabajo y creación de un experimento

Importe la clase `Workspace` y cargue la información de suscripción del archivo `config.json` mediante la función `from_config().`. Esto busca el archivo JSON en el directorio actual de forma predeterminada, pero también puede especificar un parámetro de ruta de acceso para que apunte al archivo utilizando `from_config(path="your/file/path")`. En un servidor de cuadernos en la nube, el archivo se encuentra automáticamente en el directorio raíz.

Si el código siguiente solicita autenticación adicional, simplemente pegue el vínculo en un explorador y escriba el token de autenticación.

> [!TIP]
> Si no está familiarizado con los cuadernos de Jupyter Notebook, ejecute el código haciendo clic en una celda de código y presionando **Mayús + Entrar** para ejecutar las celdas de una en una. También puede ejecutar todo el cuaderno a la vez, para ello haga clic en **Celda** en la barra de menús superior y, a continuación, haga clic en **Ejecutar todo**.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Ahora cree un experimento en el área de trabajo. Un experimento es otro recurso básico de la nube que representa una colección de pruebas (ejecuciones individuales del modelo). En este tutorial, usará el experimento para crear ejecuciones y realizar un seguimiento del entrenamiento del modelo en Azure Portal. Los parámetros incluyen la referencia del área de trabajo y un nombre de cadena para el experimento.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Carga de datos y preparación para el entrenamiento

En este tutorial, usará el conjunto de datos sobre diabetes, que es un conjunto de datos prenormalizado incluido en scikit-learn. Este conjunto de datos usa características como edad, sexo y IMC para predecir la progresión de la diabetes. Cargue los datos de la función estática `load_diabetes()` y divídalos en conjuntos de entrenamiento y de prueba mediante `train_test_split()`. Esta función separa los datos para que el modelo tenga datos no detectados que se usarán para las pruebas después del entrenamiento.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Entrenamiento de un modelo

El entrenamiento de un modelo scikit-learn simple se puede realizar fácilmente de forma local para el aprendizaje a pequeña escala, pero para el entrenamiento de muchas iteraciones con docenas de permutaciones de características y configuraciones de hiperparámetros diferentes, es fácil perderle la pista a los modelos que ha entrenado y a la forma en la que lo ha hecho. El modelo de diseño siguiente muestra cómo aprovechar el SDK para realizar fácilmente un seguimiento del entrenamiento en la nube.

Cree un script que entrene modelos Ridge en un bucle mediante diferentes valores alfa de hiperparámetro.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

En el código anterior se consigue lo siguiente:

1. Para cada valor de hiperparámetro alfa en la matriz `alphas`, se crea una nueva ejecución dentro del experimento. El valor alfa se registra para diferenciar entre cada ejecución.
1. En cada ejecución, se crea una instancia de un modelo Ridge, se entrena y se usa para ejecutar las predicciones. El error cuadrático medio se calcula para los valores reales frente a los previstos y, a continuación, se registra en la ejecución. En este momento, la ejecución tiene metadatos adjuntos para el valor alfa y la precisión del error cuadrático medio (RMSE).
1. Después, el modelo de cada ejecución se serializa y se carga en la ejecución. Esto le permite descargar el archivo de modelo desde la ejecución en el portal.
1. Al final de cada iteración, la ejecución se completa llamando a `run.complete()`.

Una vez completado el entrenamiento, llame a la variable `experiment` para capturar un vínculo al experimento en el portal.

```python
experiment
```

<table style="width:100%"><tr><th>NOMBRE</th><th>Área de trabajo</th><th>Página del informe</th><th>Página de documentos</th></tr><tr><td>diabetes-experiment</td><td>Nombre-área-de-trabajo</td><td>Vínculo a Azure Portal</td><td>Vínculo a la documentación</td></tr></table>

## <a name="view-training-results-in-portal"></a>Visualización de los resultados del entrenamiento en el portal

Si sigue el **vínculo a Azure Portal**, llegará a la página principal del experimento. Aquí verá todas las ejecuciones individuales del experimento. Cualquier valor de registro personalizado (`alpha_value` y `rmse`, en este caso) se convierten en campos para cada ejecución, y también están disponibles para los gráficos y los iconos en la parte superior de la página del experimento. Para agregar una métrica registrada a un gráfico o un icono, mantenga el mouse sobre ella, haga clic en el botón Editar y busque la métrica de registro personalizado.

Al entrenar modelos a escala sobre cientos y miles de ejecuciones independientes, esta página facilita la consulta de todos los modelos que haya entrenado, la forma específica en la que se entrenó cada uno y cómo han cambiado las métricas únicas a lo largo del tiempo.

![Página principal del experimento en el portal](./media/tutorial-quickstart/experiment-main.png)

Hacer clic en un vínculo de número de ejecución en la columna `RUN NUMBER`, le lleva hasta la página de cada ejecución individual. En la pestaña predeterminada **Detalles** se muestra información más detallada sobre cada ejecución. Vaya a la pestaña **Salidas** y verá el archivo `.pkl` del modelo que se cargó en la ejecución durante cada iteración de entrenamiento. Aquí puede descargar el archivo de modelo, en lugar de tener que volver a entrenarlo manualmente.

![Página de detalles de ejecución en el portal](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Obtención del mejor modelo

Además de poder descargar los archivos de modelo del experimento en el portal, también puede descargarlos mediante programación. El siguiente código recorre en iteración cada ejecución del experimento y obtiene acceso a las métricas de ejecución registradas y a los detalles de la ejecución (que contiene el run_id). Esto realiza un seguimiento de la mejor ejecución, en este caso, la ejecución con el error cuadrático medio más bajo.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Use el mejor identificador de ejecución para capturar la ejecución individual mediante el constructor `Run` junto con el objeto de experimento. A continuación, llame a `get_file_names()` para ver todos los archivos disponibles para su descarga desde esta ejecución. En este caso, solo cargó un archivo para cada ejecución durante el entrenamiento.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Llame a `download()` en el objeto de ejecución, especificando el nombre del archivo de modelo que se va a descargar. De forma predeterminada, esta función se descarga en el directorio actual.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Limpieza de recursos

No complete esta sección si planea ejecutar otros tutoriales de Azure Machine Learning Service.

### <a name="stop-the-notebook-vm"></a>Detención de la máquina virtual de Notebook

Si usó un servidor de cuadernos en la nube, detenga la máquina virtual cuando no la esté usando a fin de reducir el costo.

1. En el área de trabajo, seleccione **Máquinas virtuales de Notebook**.

   ![Detener el servidor de máquina virtual](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. En la lista, seleccione la máquina virtual.

1. Seleccione **Detener**.

1. Cuando esté listo para volver a usar el servidor, seleccione **Iniciar**.

### <a name="delete-everything"></a>Eliminar todo el contenido

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

También puede mantener el grupo de recursos pero eliminar una sola área de trabajo. Muestre las propiedades del área de trabajo y seleccione **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha realizado las tareas siguientes:

> [!div class="checklist"]
> * Ha conectado con el área de trabajo y creado un experimento
> * Ha cargado datos y entrenado modelos scikit-learn
> * Ha visualizado los resultados del entrenamiento en el portal y recuperado los modelos

[Implemente el modelo](tutorial-deploy-models-with-aml.md) con Azure Machine Learning.
Aprenda a desarrollar experimentos de [aprendizaje automático automatizados](tutorial-auto-train-models.md).