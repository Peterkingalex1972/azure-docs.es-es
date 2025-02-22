---
title: 'Regresión del árbol de decisión potenciado: referencia para los módulos'
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre cómo usar el módulo de regresión del árbol de decisión potenciado en Azure Machine Learning Service para crear un conjunto de árboles de regresión mediante la potenciación.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67e54f10074ee566ce974dbd27485904bfe0a653
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65411544"
---
# <a name="boosted-decision-tree-regression-module"></a>Módulo de regresión del árbol de decisión potenciado

En este artículo se describe un módulo de la interfaz visual (versión preliminar) de Azure Machine Learning Service.

Utilice este módulo para crear un conjunto de árboles de regresión mediante la potenciación. *Potenciación* significa que cada árbol depende de árboles anteriores. El algoritmo aprende ajustando el valor residual de los árboles que le preceden. Por lo tanto, la potenciación de un conjunto de árboles de decisión tiende a mejorar la precisión a pesar de correr el riesgo de tener menor cobertura.  
  
Este método de regresión es un método de aprendizaje supervisado y, por lo tanto, requiere un *conjunto de datos con etiquetas*. La columna con la etiqueta debe contener valores numéricos.  

> [!NOTE]
> Utilice este módulo solo con conjuntos de datos en los que se usen variables numéricas.  

Cuando haya definido el modelo, entrénelo usando el [modelo de entrenamiento](./train-model.md).

> [!TIP]
> ¿Quiere más información sobre los árboles que se han creado? Después de entrenar el modelo, haga clic con el botón derecho en el resultado del módulo [Modelo de entrenamiento](./train-model.md) y seleccione **Visualizar** para ver el árbol que se ha creado en cada iteración. Puede explorar en profundidad las divisiones de cada árbol y ver las reglas de cada nodo.  
  
## <a name="more-about-boosted-regression-trees"></a>Más información sobre los árboles de regresión potenciados  

La potenciación es uno de los distintos métodos clásicos para crear modelos de conjuntos, junto con la agregación, los bosques aleatorios, etc.  En Azure Machine Learning, los árboles de decisión potenciados utilizan una implementación eficaz del algoritmo de potenciación del gradiente MART. La potenciación del gradiente es una técnica de aprendizaje automático para problemas de regresión. Genera cada árbol de regresión de forma gradual, usando una función de pérdida predefinida para medir el error en cada paso y corregirlo en el siguiente. Por lo tanto, el modelo de predicción es en realidad un conjunto de modelos de predicción más débiles.  
  
En los problemas de regresión, la potenciación genera una serie de árboles de forma gradual y después selecciona el árbol óptimo mediante una función arbitraria de pérdida diferenciable.  
  
Para más información, vea estos artículos:  
  
+ [https://wikipedia.org/wiki/Gradient_boosting#Gradient_tree_boosting](https://wikipedia.org/wiki/Gradient_boosting)

    Este artículo de la Wikipedia sobre potenciación del gradiente proporciona información general sobre los árboles potenciados. 
  
-  [https://research.microsoft.com/apps/pubs/default.aspx?id=132652](https://research.microsoft.com/apps/pubs/default.aspx?id=132652)  

    Microsoft Research: de RankNet a LambdaRank y a LambdaMART (información general) Por J. C. Burges.

El método de potenciación de gradientes también se puede usar para clasificar problemas reduciéndolos a la regresión con una función de pérdida adecuada. Para más información sobre la implementación de árboles potenciados para tareas de clasificación, vea [Árbol de decisión potenciado de segunda clase](./two-class-boosted-decision-tree.md).  

## <a name="how-to-configure-boosted-decision-tree-regression"></a>Cómo configurar la regresión del árbol de decisión potenciado

1.  Agregue el módulo **Boosted  Decision Tree** (Árbol de decisión potenciado) al experimento. Puede encontrar este módulo en **Machine Learning**, **Initialize** (Inicializar), en la categoría **Regression** (Regresión). 
  
2.  Para especificar cómo quiere que se entrene el modelo, establezca la opción **Create trainer mode** (Crear modo entrenador).  
  
    -   **Parámetro único**: seleccione esta opción si sabe cómo quiere configurar el modelo y proporcione un conjunto específico de valores como argumentos.  
   
  
3. **Número máximo de hojas por árbol**: indique el número máximo de nodos terminales (hojas) que se pueden crear en un árbol.  

    Al aumentar este valor, podría aumentar el tamaño del árbol y obtener una mayor precisión, asumiendo un el riesgo de sobreajuste y de mayor tiempo de entrenamiento.  

4. **Número mínimo de muestras por nodo hoja**: indique el número mínimo de casos necesarios para crear un nodo terminal (hoja) en un árbol.

    Al aumentar este valor, aumenta el umbral para crear reglas nuevas. Por ejemplo, con el valor predeterminado de 1, incluso un solo caso puede provocar que se cree una regla nueva. Si aumenta el valor a 5, los datos de entrenamiento tienen que contener, como mínimo, cinco casos que cumplan las mismas condiciones.

5. **Velocidad de aprendizaje**: escriba un número entre 0 y 1 que defina el tamaño de paso durante el aprendizaje. La velocidad de aprendizaje determina la rapidez o lentitud con la que el aprendiz converge en la solución óptima. Si el tamaño del paso es demasiado grande, puede pasar por alto la solución óptima. Si el tamaño del paso es demasiado pequeño, el entrenamiento tarda más tiempo a converger en la mejor solución.

6. **Número de árboles construidos**: indica el número total de árboles de decisión que se va a crear en el conjunto. Al crear más árboles de decisión, puede obtener una mejor cobertura, pero el tiempo de entrenamiento aumenta.

    Este valor también controla el número de árboles que se muestran al visualizar el modelo entrenado. Si desea ver o imprimir un árbol único, puede establecer el valor en 1. Sin embargo, solo se genera un árbol (el árbol con el conjunto inicial de parámetros) y no se llevan a cabo más iteraciones.

7. **Número de iniciación aleatorio**: introduzca un número entero no negativo opcional para que se use como valor de inicialización aleatorio. Al especificar un valor, se garantiza la reproducibilidad durante las ejecuciones que tienen los mismos datos y parámetros.

    De forma predeterminada, el valor de inicialización aleatorio se establece en 0, lo que significa que el valor de inicialización inicial se obtiene a partir del reloj del sistema.
  
8. **Permitir niveles de categorías desconocidos**: seleccione esta opción para crear un grupo de valores desconocidos en los conjuntos de entrenamiento y validación. Si desactiva esta opción, el modelo puede aceptar únicamente los valores que se encuentran en los datos de entrenamiento. El modelo podría ser menos preciso con valores conocidos, pero puede proporcionar mejores predicciones para los valores nuevos (desconocidos).

9. Agregue un conjunto de datos de entrenamiento y uno de los módulos de entrenamiento:

    - Si establece la opción **Create trainer mode** (Crear modo entrenador) en **Single Parameter** (Parámetro único), use el módulo [Modelo de entrenamiento](train-model.md).  
  
    

10. Ejecute el experimento.  
  
### <a name="results"></a>Results

Una vez completado el entrenamiento:

+ Para ver el árbol que se ha creado en cada iteración, haga clic con el botón derecho en el resultado del módulo [Train Model](train-model.md) (Modelo de entrenamiento) y seleccione **Visualize** (Visualizar).
  
     Haga clic en cada árbol para explorar en profundidad las divisiones y ver las reglas de cada nodo.  

+ Para usar el modelo para la puntuación, conéctelo a [Score Model](./score-model.md) (Modelo de puntuación) para predecir los valores de ejemplos de nuevas entradas.

+ Para guardar una instantánea del modelo entrenado, haga clic con el botón derecho en el resultado **Trained model** (Modelo entrenado) del módulo de entrenamiento y seleccione **Save As** (Guardar como). La copia del modelo entrenado que guarde no se actualiza en las ejecuciones sucesivas del experimento.

## <a name="next-steps"></a>Pasos siguientes

Consulte el [conjunto de módulos disponibles](module-reference.md) para Azure Machine Learning Service. 