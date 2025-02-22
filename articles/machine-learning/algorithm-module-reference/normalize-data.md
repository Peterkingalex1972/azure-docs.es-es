---
title: 'Normalizar datos: referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo Normalizar datos en Azure Machine Learning Service para transformar un conjunto de datos a través de la *normalización*.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 504224ae586e18fc5bf9294b537e730da37a2423
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128566"
---
# <a name="normalize-data-module"></a>Módulo Normalize Data

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Use este módulo para transformar un conjunto de datos a través de la *normalización*.

La normalización es una técnica que se aplica a menudo como parte de la preparación de datos para el aprendizaje automático. El objetivo de la normalización es cambiar los valores de las columnas numéricas del conjunto de datos para usar una escala común, sin distorsionar las diferencias en los intervalos de valores ni perder información. La normalización también es necesaria para que algunos algoritmos modelen los datos correctamente.

Por ejemplo, suponga que el conjunto de datos de entrada contiene una columna con valores comprendidos entre 0 y 1, y otra columna con valores comprendidos entre 10 000 y 100 000. La enorme diferencia en el *escala* de los números podría generar problemas al intentar combinar los valores como características durante el modelado.

La *normalización* evita estos problemas mediante la creación de nuevos valores que mantienen la distribución general y las relaciones en los datos de origen, a la vez que se conservan los valores dentro de una escala que se aplica en todas las columnas numéricas que se usan en el modelo.

Este módulo ofrece varias opciones para transformar los datos numéricos:

- Puede cambiar todos los valores a la escala de 0 a 1, o transformar los valores para representarlos como percentiles en lugar de como valores absolutos.
- Puede aplicar la normalización a una sola columna o a varias columnas en el mismo conjunto de datos.
- Si tiene que repetir el experimento, o aplica los mismos pasos de normalización a otros datos, puede guardar los pasos como transformación de normalización y aplicarla a otros conjuntos de datos que tengan el mismo esquema.

> [!WARNING]
> Algunos algoritmos requieren que se normalicen los datos antes de entrenar un modelo. Otros algoritmos realizan su propio escalado o normalización de datos. Por lo tanto, al elegir un algoritmo de aprendizaje automático para usar en la creación de un modelo predictivo, asegúrese de revisar los requisitos de datos del algoritmo antes de aplicar la normalización a los datos de entrenamiento.

##  <a name="configure-normalize-data"></a>Configurar Normalize Data

Con este módulo, puede aplicar solo un método de normalización a la vez. Por lo tanto, se aplica el mismo método de normalización a todas las columnas que seleccione. Para usar los métodos de normalización diferentes, use una segunda instancia de **Normalize Data**.

1. Agregue el módulo **Normalize Data** al experimento. Puede encontrar el módulo de Azure Machine Learning en **Transformación de datos**, en la categoría **Scale and Reduce** (Escalar y reducir).

2. Conecte un conjunto de datos que contenga al menos una columna de todos los números.

3. Use el selector de columnas para elegir las columnas numéricas que quiere normalizar. Si no elige columnas individuales, de forma predeterminada se incluyen en la entrada **todas** las columnas de tipo numérico y se aplica el mismo proceso de normalización a todas las columnas seleccionadas. 

    Esto puede conducir a resultados extraños si incluye columnas numéricas que no deberían normalizarse. Compruebe siempre las columnas con cuidado.

    Si no se detectan columnas numéricas, compruebe los metadatos de columna para verificar que el tipo de datos de la columna sea un tipo numérico admitido.

    > [!TIP]
    > Para asegurarse de que las columnas de un tipo específico se proporcionen como entrada, pruebe a usar el módulo [Select Columns in Dataset](./select-columns-in-dataset.md) (Seleccionar columnas en conjunto de datos) antes de **Normalize Data**.

4. **Use 0 for constant columns when checked** (Usar 0 para columnas constantes cuando esté activada):  Seleccione esta opción cuando cualquier columna numérica contenga un único valor invariable. Esto garantiza que estas columnas no se usen en las operaciones de normalización.

5. De la lista desplegable **Transformation method** (Método de transformación), elija una sola función matemática que se aplicará a todas las columnas seleccionadas. 
  
    - **Zscore**: Convierte todos los valores en puntuación z.
    
      Los valores de la columna se transforman mediante la fórmula siguiente:  
  
      ![normalización con puntuaciones z](media/module/aml-normalization-z-score.png)
  
      La media y la desviación estándar se calculan por separado para cada columna. Se usa la desviación estándar poblacional.
  
    - **MinMax**: El normalizador mínimo-máximo cambia la escala linealmente de todas las características del intervalo [0,1].
    
      Para cambiar la escala del intervalo [0,1] se cambian los valores de cada característica para que el valor mínimo sea 0, y, luego, se divide por el nuevo valor máximo (que es la diferencia entre los valores máximos y mínimos originales).
      
      Los valores de la columna se transforman mediante la fórmula siguiente:  
  
      ![normalización con la función min-max](media/module/aml-normalization-minmax.png "AML_normalization-minmax")  
  
    - **Logistic**: Los valores de la columna se transforman mediante la fórmula siguiente:

      ![fórmula de normalización por función logística](media/module/aml-normalization-logistic.png "AML_normalization-logistic")  
  
    - **LogNormal**: Esta opción convierte todos los valores en una escala logarítmica normal.
  
      Los valores de la columna se transforman mediante la fórmula siguiente:
  
      ![fórmula de distribución logarítmica normal](media/module/aml-normalization-lognormal.png "AML_normalization-lognormal")
    
      Aquí μ y σ son los parámetros de la distribución, calculados empíricamente a partir de los datos como las estimaciones de verosimilitud máxima para cada columna por separado.  
  
    - **TanH**: Todos los valores se convierten en una tangente hiperbólica.
    
      Los valores de la columna se transforman mediante la fórmula siguiente:
    
      ![normalización con la función tanh](media/module/aml-normalization-tanh.png "AML_normalization-tanh")

6. Ejecute el experimento, o haga doble clic en el módulo **Normalize Data** y seleccione **Ejecutar seleccionados**. 

## <a name="results"></a>Results

El módulo **Normalize Data** genera dos salidas:

- Para ver los valores transformados, haga clic con el botón derecho en el módulo, seleccione **Transformed dataset** (Conjunto de datos transformado) y haga clic en **Visualizar**.

    De forma predeterminada, los valores se transforman en su lugar. Si desea comparar los valores transformados con los valores originales, use el módulo [Agregar columnas](./add-columns.md) para recombinar los conjuntos de datos y ver las columnas en paralelo.

- Para guardar la transformación para que pueda aplicar el mismo método de normalización para otro conjunto de datos similar, haga clic con el botón derecho en el módulo, seleccione **Transformation function** (Función de transformación) y haga clic en **Save as Transform** (Guardar como transformación).

    A continuación, puede cargar las transformaciones guardadas desde el grupo **Transformaciones** del panel de navegación izquierdo y aplicarlas a un conjunto de datos con el mismo esquema con [./Aplicar transformación](apply-transformation.md).  


## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 