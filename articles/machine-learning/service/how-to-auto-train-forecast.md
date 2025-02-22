---
title: Entrenamiento automático de un modelo de previsión de series temporales
titleSuffix: Azure Machine Learning service
description: Aprenda a usar el servicio Azure Machine Learning para entrenar un modelo de regresión de previsión de series temporales mediante aprendizaje automático automatizado.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 793474495f3ab3ef06a17b48d15c2f91d0677365
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848161"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Entrenamiento automático de un modelo de previsión de series temporales

En este artículo aprenderá a entrenar un modelo de regresión de previsión de series temporales con aprendizaje automático automatizado en el servicio Azure Machine Learning. La configuración de un modelo de previsión es similar a la configuración de un modelo de regresión estándar mediante aprendizaje automático automatizado, pero existen algunas opciones de configuración y pasos previos de procesamiento para trabajar con los datos de series temporales. En los siguientes ejemplos se indica cómo:

* Preparar los datos para el modelado de series temporales
* Configurar parámetros específicos de las serie temporales en un objeto [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig)
* Ejecutar predicciones con los datos de serie temporal

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

Puede usar aprendizaje automático automatizado para combinar las técnicas y enfoques y obtener una previsión recomendada y de alta calidad de series temporales. Un experimento automatizado de series temporales se trata como un problema de regresión multivariante. Los valores de series temporales anteriores se "dinamizan" para convertirse en dimensiones adicionales para el regresor junto con otros indicadores.

Este enfoque, a diferencia de los métodos clásicos de series temporales, tiene una ventaja de incorporar de forma natural varias variables contextuales y su relación entre sí durante el entrenamiento. En aplicaciones de previsión del mundo real, varios factores pueden influir en un pronóstico. Por ejemplo, al prever ventas, las interacciones de las tendencias históricas, la tasa de cambio y el precio son motores conjuntos del resultado de ventas. Una ventaja adicional es que todas las innovaciones recientes hechas en los modelos de regresión se aplican inmediatamente a la previsión.

También puede [configurar](#config) hasta qué punto en el futuro debe extenderse la previsión (el horizonte de previsión), así como los retrasos y mucho más. El aprendizaje automático automatizado aprende un modelo único, pero a menudo internamente bifurcado para todos los elementos en el conjunto de datos y horizontes de predicción. Por tanto, hay más datos disponibles para calcular los parámetros del modelo, y se hace posible la generalización hasta series no antes vistas.

Las características que se extraen de los datos de entrenamiento desempeñan un papel fundamental. Y el aprendizaje automático automatizada lleva a cabo los pasos previos al procesamiento estándares y genera características adicionales de series temporales para capturar los efectos de temporada y maximizar la precisión de predicción.

## <a name="prerequisites"></a>Requisitos previos

* Un área de trabajo de Azure Machine Learning. Para crear el área de trabajo, consulte [Create an Azure Machine Learning service workspace](how-to-manage-workspace.md) (Creación de un área de trabajo del servicio Azure Machine Learning).
* En este artículo se presupone una familiarización básica con la configuración de una experimento de aprendizaje de automático automatizado. Siga el [tutorial](tutorial-auto-train-models.md) o los [procedimientos](how-to-configure-auto-train.md) para ver los patrones de diseño del experimento de aprendizaje automático automatizado.

## <a name="preparing-data"></a>Preparación de los datos

La diferencia más importante entre el tipo de tarea de regresión de previsión y el de regresión en el aprendizaje automático automatizado es que se incluye una característica en los datos que representa una serie de tiempo válida. Una serie de tiempo normal tiene una frecuencia coherente y bien definida, y un valor en cada punto de un intervalo de tiempo continuo. Tenga en cuenta la siguiente instantánea de un archivo `sample.csv`.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Este conjunto de datos es un ejemplo sencillo de los datos de ventas diarios de una empresa que tiene dos almacenes, A y B. Asimismo, hay una característica para `week_of_year` que permitirá que el modelo detecte la estacionalidad semanalmente. El campo `day_datetime` representa una serie temporal limpia con frecuencia diaria y `sales_quantity` es la columna de destino para ejecutar predicciones. Lea los datos en una trama de datos de Pandas y use la función `to_datetime` para asegurarse de que la serie temporal sea de tipo `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

En este caso los datos ya están ordenados de manera ascendente por el campo de hora `day_datetime`. Sin embargo, al configurar un experimento, asegúrese de que la columna de tiempo deseada esté ordenada de manera ascendente para compilar una serie de tiempo válida. Supongamos que los datos contienen 1000 registros; realice una división determinista en los datos para crear conjuntos de datos de entrenamiento y prueba. A continuación, separe el campo de destino `sales_quantity` para crear los conjuntos de datos de entrenamiento y prueba.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Al entrenar un modelo para la previsión de valores futuros, asegúrese de que todas las características del entrenamiento se pueden usar al ejecutar predicciones para su horizonte previsto. Por ejemplo, al crear una previsión de demanda, incluir una característica para el precio de cotización actual podría aumentar la precisión del entrenamiento de manera exponencial. Sin embargo, si quiere una previsión con un horizonte lejano, no es posible predecir con precisión valores de cotización futuros correspondientes a momentos futuros en la serie temporal y la precisión del modelo podría verse afectada.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configuración y ejecución del experimento

Para las tareas de previsión, el aprendizaje automático automatizado sigue unos pasos de cálculo y procesamiento previo específicos de los datos de la serie temporal. Se ejecutarán los siguientes pasos de procesamiento previos:

* Detectar la frecuencia de muestreo de la serie temporal (cada hora, diariamente, semanalmente, etc.) y crear nuevos registros para los momentos ausentes para que la serie sea continua.
* Imputar los valores que faltan en las columnas de destino (copia de los valores nulos hacia adelante) y de característica (con la mediana de los valores de la columna).
* Crear características basadas en los detalles para habilitar los efectos fijos en las diferentes series.
* Crear características basadas en el tiempo para ayudar a aprender los patrones estacionales.
* Codificar las variables categóricas en cantidades numéricas.

El objeto `AutoMLConfig` define la configuración y los datos necesarios para una tarea de aprendizaje automático automatizado. Al igual que en un problema de regresión, debe definir los parámetros de entrenamiento estándar como tipo de tarea, número de iteraciones, datos de entrenamiento y número de validaciones cruzadas. Para las tareas de previsión existen parámetros adicionales que se deben establecer y que afectan al experimento. En la siguiente tabla se explica cada parámetro y su uso.

| Parámetro | DESCRIPCIÓN | Obligatorio |
|-------|-------|-------|
|`time_column_name`|Se utiliza para especificar la columna de fecha y hora en los datos de entrada utilizada que se usa para compilar la serie temporal y se deduce su frecuencia.|✓|
|`grain_column_names`|Nombres que definen los grupos de series individuales en los datos de entrada. Si no se define el nivel de detalle, el conjunto de datos se presupone una serie temporal.||
|`max_horizon`|Define el horizonte de previsión máximo deseado en unidades de frecuencia de la serie temporal. Las unidades se basan en el intervalo de tiempo de los datos de entrenamiento, p. ej., semanales, mensuales, que debe predecir el pronosticador.|✓|
|`target_lags`|*n* períodos para diferir los valores de destino antes del entrenamiento del modelo.||
|`target_rolling_window_size`|*n* períodos históricos que se utilizarán para generar valores previstos, < = tamaño del conjunto de entrenamiento. Si se omite, *n* es el tamaño total del conjunto de entrenamiento.||

Cree la configuración de la serie temporal como objeto de diccionario. Establezca `time_column_name` en el campo `day_datetime` en el conjunto de datos. Defina el parámetro `grain_column_names` para asegurarse de que se crean **dos grupos de series temporales diferentes** para los datos; uno para el almacén A y otro para el B. Por último, establezca `max_horizon` en 50 para predecir el conjunto de prueba completo. Establezca un intervalo de previsión de 10 períodos con `target_rolling_window_size` y difiera los valores de destino 2 períodos con el parámetro `target_lags`.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> Los pasos previos al procesamiento del aprendizaje automático (normalización de características, control de los datos que faltan, conversión de valores de texto a numéricos, etc.) se convierten en parte del modelo subyacente. Cuando se usa el modelo para las predicciones, se aplican automáticamente a los datos de entrada los mismos pasos previos al procesamiento que se aplican durante el entrenamiento.

Ahora cree un objeto `AutoMLConfig` estándar y especifique el tipo de tarea `forecasting`; a continuación, envíe el experimento. Una vez finalizado el modelo, recupere la iteración con la mejor ejecución.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Para el procedimiento de validación cruzada (VC), los datos de serie temporal pueden infringir las suposiciones estadísticas básicas de la estrategia de validación cruzada de K iteraciones canónica, por lo que el aprendizaje automático automatizado implementa un procedimiento de validación de origen gradual para crear subconjuntos de validación cruzada para los datos de serie temporal. Para usar este procedimiento, especifique el parámetro `n_cross_validations` en el objeto `AutoMLConfig`. Puede omitir la validación y usar sus propios conjuntos validación con los parámetros `X_valid` y `y_valid`.

### <a name="view-feature-engineering-summary"></a>Visualización del resumen de ingeniería de las características

Puede ver detalles del proceso de ingeniería de las características para los tipos de tarea de serie temporal con aprendizaje automático automatizado. El siguiente código muestra cada característica sin formato con los siguientes atributos:

* Nombre de la característica sin formato
* Número de características diseñadas formadas a partir de esta característica sin formato
* Tipo detectado
* Si se quitó la característica
* Lista de las transformaciones de la característica sin formato

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsión con el mejor modelo

Use la mejor iteración del modelo para la previsión de valores para el conjunto de datos de prueba.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

Como alternativa, puede usar la función `forecast()` en lugar de `predict()`, lo que permitirá especificar cuándo deben comenzar las predicciones. En el siguiente ejemplo, primero se reemplazan todos los valores de `y_pred` con `NaN`. El origen de la previsión estará al final de los datos de entrenamiento en este caso, como suele ser el caso cuando se usa `predict()`. Sin embargo, si reemplazó solo la segunda mitad de `y_pred` con `NaN`, la función dejó intactos los valores numéricos de la primera, pero realizó la previsión de los valores de `NaN` de la segunda mitad. La función devuelve tanto los valores previstos como las características alineadas.

También puede usar el parámetro `forecast_destination` de la función `forecast()` para la previsión de valores hasta una fecha especificada.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcule el error cuadrático medio (ECM)entre los valores de `y_test` reales y los previstos en `y_pred`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Ahora que se ha determinado la precisión del modelo global, el siguiente paso más realista es usar este para prever valores futuros desconocidos. Solo tiene que proporcionar un conjunto de datos en el mismo formato que el de prueba (`X_test`) pero con fechas y horas futuras y el conjunto de predicción resultante son los valores previstos para cada paso de la serie temporal. Supongamos que los últimos registros de la serie temporal en el conjunto de datos fueron del 31/12/2018. Para prever la demanda para el día siguiente (o para los períodos que necesite previsión, <= `max_horizon`), cree un único registro de serie de tiempo para cada almacén para el 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Repita los pasos necesarios para cargar estos datos futuros a una trama de datos y ejecute `best_run.predict(X_test)` para predecir los valores futuros.

> [!NOTE]
> No se pueden predecir valores para períodos que superen el valor de `max_horizon`. El modelo debe volver a entrenarse con un horizonte mayor para predecir valores futuros más allá del actual.

## <a name="next-steps"></a>Pasos siguientes

* Siga el [tutorial](tutorial-auto-train-models.md) para aprender a crear experimentos con aprendizaje automático automatizado.
* Consulte la documentación de referencia del [SDK de Azure Machine Learning para Python](https://aka.ms/aml-sdk).
