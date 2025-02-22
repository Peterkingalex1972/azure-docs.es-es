---
title: 'Multiclass Logistic Regression: referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo Multiclass Logistic Regression (Regresión logística multiclase) en Azure Machine Learning Service para crear un modelo de regresión logística que pueda usarse para predecir varios valores.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ac4310e851808d6e6d89d1a2b506975eea3b1d69
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029331"
---
# <a name="multiclass-logistic-regression-module"></a>Módulo Multiclass Logistic Regression

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Use este módulo para crear un modelo de regresión logística que pueda usarse para predecir varios valores.

La clasificación con la regresión logística es un método de aprendizaje supervisado y, por lo tanto, requiere un conjunto de datos con etiquetas. Puede entrenar el modelo proporcionando el modelo y el conjunto de datos etiquetado como entrada para un módulo, como [Train Model](./train-model.md) (Entrenar modelo). Después, el modelo entrenado puede usarse para predecir valores para los nuevos ejemplos de entrada.

Azure Machine Learning también proporciona un módulo [Two-Class Logistic Regression](./two-class-logistic-regression.md) (Regresión logística de dos clases), que es adecuado para la clasificación de variables binarias o dicotómicas.

## <a name="about-multiclass-logistic-regression"></a>Acerca de la regresión logística multiclase

La regresión logística es un método conocido en estadística que se usa para predecir la probabilidad de un resultado y es popular para las tareas de clasificación. El algoritmo predice la probabilidad de aparición de un evento ajustando los datos a una función logística. 

En la regresión logística multiclase, el clasificador puede usarse para predecir varios resultados.

## <a name="configure-a-multiclass-logistic-regression"></a>Configurar una regresión logística multiclase

1. Agregue el módulo **Multiclass Logistic Regression** al experimento.

2. Especifique cómo quiere que se entrene el modelo, estableciendo la opción **Create trainer mode** (Crear modo entrenador).

    + **Single Parameter** (Parámetro único): Use esta opción si sabe cómo quiere configurar el modelo y proporcione un conjunto específico de valores como argumentos.

    + **Parameter Range** (Intervalo de parámetros): Use esta opción si no está seguro de los mejores parámetros y quiere usar un barrido de parámetros.

3. **Optimization tolerance** (Tolerancia de optimización), especifique el valor de umbral de convergencia del optimizador. Si la mejora entre las iteraciones es menor que el umbral, el algoritmo se detiene y devuelve el modelo actual.

4. **L1 regularization weight** (Ponderación de regularización L1), **L2 regularization weight** (Ponderación de regularización L2): Escriba un valor que se usará para los parámetros de regularización L1 y L2. Se recomienda un valor distinto de cero para ambos.

    La regularización es un método para evitar el sobreajuste mediante la penalización de modelos con valores de coeficiente extremos. La regularización funciona agregando la penalización asociada a los valores de coeficiente al error de la hipótesis. Un modelo preciso con valores de coeficiente extremos se penalizaría más, y un modelo menos preciso con valores más conservadores se penalizaría menos.

     Las regularizaciones L1 y L2 tienen efectos y usos diferentes. L1 puede aplicarse a modelos dispersos, lo que resulta útil cuando se trabaja con datos muy dimensionales. En cambio, la regularización L2 es preferible para los datos que no están dispersos.  Este algoritmo es compatible con una combinación lineal de los valores de regularización L1 y L2: es decir, si `x = L1` y `y = L2`, `ax + by = c` define el intervalo lineal de los términos de regularización.

     Se han diseñado diferentes combinaciones lineales de los términos de L1 y L2 para los modelos de regresión logística: por ejemplo, [regularización elástica neta](https://wikipedia.org/wiki/Elastic_net_regularization).

6. **Número de iniciación aleatorio**: Escriba un valor entero para usar como valor de inicialización para el algoritmo si quiere que los resultados se puedan repetir entre ejecuciones. En caso contrario, como inicialización se usa un valor de reloj del sistema, lo que puede producir resultados ligeramente diferentes cada vez que ejecute el mismo experimento.

8. Conecte un conjunto de datos etiquetados y uno de los módulos de entrenamiento:

    + Si establece **Create trainer mode** (Crear modo entrenador) en **Single Parameter** (Parámetro único), use el módulo [Entrenar modelo](./train-model.md).

9. Ejecute el experimento.

## <a name="results"></a>Results

Después de completar el entrenamiento, puede ver un resumen de los parámetros del modelo, junto con las ponderaciones de característica aprendidas en el entrenamiento, haga clic con el botón derecho en la salida de [Train Model](./train-model.md) (Entrenar modelo) y seleccione **Visualizar**.


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 