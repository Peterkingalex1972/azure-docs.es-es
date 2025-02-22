---
title: Arquitectura y conceptos clave
titleSuffix: Azure Machine Learning service
description: Obtenga información sobre la arquitectura, los términos, los conceptos y el flujo de trabajo que conforman Azure Machine Learning Service.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 07/12/2019
ms.custom: seodec18
ms.openlocfilehash: ea5e476680b07a6a7ba2b57e94f1f0b99cc10987
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990100"
---
# <a name="how-azure-machine-learning-service-works-architecture-and-concepts"></a>Cómo funciona Azure Machine Learning Service: Arquitectura y conceptos

Obtenga información sobre la arquitectura, los conceptos y el flujo de trabajo de Azure Machine Learning Service. En el siguiente diagrama se muestran los componentes principales del servicio y se ilustra el flujo de trabajo general al usar el servicio:

![Arquitectura y flujo de trabajo de Azure Machine Learning Service](./media/concept-azure-machine-learning-architecture/workflow.png)

## <a name="workflow"></a>Flujo de trabajo

Normalmente, el flujo de trabajo del modelo de Machine Learning sigue estos pasos:

1. **Entrenar**
    + Desarrolle scripts de entrenamiento de aprendizaje automático en **Python** o con la interfaz visual.
    + Se crea y configura un **destino de proceso**.
    + **Se envían los scripts** al destino de proceso configurado para ejecutarse en ese entorno. Durante el entrenamiento, los scripts pueden leer o escribir en el **almacén de datos**. Y los registros de ejecución se guardan como **ejecuciones** en el **área de trabajo**, agrupados en **experimentos**.

1. **Paquete**: después de encontrar una ejecución satisfactoria, se registra el modelo guardado en el **registro de modelos**.

1. **Validar** - **Consulte el experimento** para las métricas registradas en ejecuciones actuales y anteriores. Si las métricas no ofrecen el resultado deseado, se vuelve en bucle al paso 1 y se repiten los scripts.

1. **Implementar**: desarrolle un script de puntuación que usa el modelo e **implemente el modelo** como un **servicio web** en Azure, o en un **dispositivo IoT Edge**.

1. **Supervisión**: supervise el **desfase de datos** entre el conjunto de datos de entrenamiento y los datos de inferencia de un modelo implementado. Cuando sea necesario, vuelva al paso 1 para volver a entrenar el modelo con nuevos datos de entrenamiento.

## <a name="tools-for-azure-machine-learning"></a>Herramientas para Azure Machine Learning

Utilice estas herramientas para Azure Machine Learning:

+  Interactúe con el servicio en cualquier entorno de Python con el [SDK de Azure Machine Learning para Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
+ Automatice las actividades de aprendizaje automático con la [CLI de Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/reference-azure-machine-learning-cli).
+ Escribir código en Visual Studio Code con la [extensión VS Code para Azure Machine Learning](how-to-vscode-tools.md)
+ Use la [interfaz visual (versión preliminar) de Azure Machine Learning Service](ui-concept-visual-interface.md) para realizar los pasos del flujo de trabajo sin escribir código.

> [!NOTE]
> Aunque en este artículo se definen los términos y conceptos que usa Azure Machine Learning Service, no se definen los términos y conceptos de la plataforma Azure. Para obtener más información sobre la terminología de la plataforma Azure, consulte el [glosario de Microsoft Azure](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology).

## <a name="glossary"></a>Glosario

+ <a href="#workspaces">Área de trabajo</a>
+ <a href="#experiments">Experimentos</a>
+ <a href="#models">Modelos</a>
+ <a href="#run-configurations">Configuración de ejecución</a>
+ [Estimadores](#estimators)
+ <a href="#datasets-and-datastores">Conjunto de datos y almacenes de datos</a>
+ <a href="#compute-targets">Destinos de proceso</a>
+ <a href="#training-scripts">Script de entrenamiento</a>
+ <a href="#runs">Run</a>
+ <a href="#github-tracking-and-integration">Seguimiento de GIT</a>
+ <a href="#snapshots">Instantánea</a>
+ <a href="#activities">Actividad</a>
+ <a href="#images">Imagen</a>
+ <a href="#deployment">Implementación</a>
+ <a href="#web-service-deployments">Servicios web</a>
+ <a href="#iot-module-deployments">Módulos de IoT</a>
+ <a href="#ml-pipelines">Canalizaciones de Machine Learning</a>
+ <a href="#logging">Logging</a>

### <a name="workspaces"></a>Áreas de trabajo

[El área de trabajo](concept-workspace.md) es el recurso de nivel superior de Azure Machine Learning Service. Proporciona un lugar centralizado para trabajar con todos los artefactos que cree al usar Azure Machine Learning Service. Puede compartir un área de trabajo con otros usuarios. Para una descripción detallada de las áreas de trabajo, consulte [¿Qué es un área de trabajo de Azure Machine Learning?](concept-workspace.md)

### <a name="experiments"></a>Experimentos

Un experimento es una agrupación de varias ejecuciones de un script determinado. Siempre pertenece a un área de trabajo. Cuando envíe una ejecución, proporcione un nombre de experimento. La información de la ejecución se almacena en ese experimento. Si envía una ejecución y especifica un nombre de experimento que no existe, se crea automáticamente un experimento nuevo con ese nombre.

Para obtener un ejemplo del uso de un experimento, consulte [Tutorial: Entrenamiento del primer modelo](tutorial-1st-experiment-sdk-train.md).

### <a name="models"></a>Modelos

En su forma más simple, un modelo es un fragmento de código que toma una entrada y genera una salida. El hecho de crear un modelo de Machine Learning implica seleccionar un algoritmo, proporcionarle datos y ajustar los hiperparámetros. El entrenamiento es un proceso iterativo que genera un modelo entrenado, que encapsula lo que el modelo ha aprendido durante el proceso de aprendizaje.

Un modelo se genera mediante una ejecución en Azure Machine Learning. También puede usar un modelo entrenado fuera de Azure Machine Learning. Puede registrar el modelo en un área de trabajo de Azure Machine Learning Service.

Recuerde que Azure Machine Learning Service es independiente del marco de trabajo. Al crear un modelo, puede usar cualquier marco de aprendizaje automático popular, como Scikit-learn, XGBoost, PyTorch, TensorFlow y Chainer.

Para ver un ejemplo de cómo entrenar un modelo con Scikit-learn y un estimador, consulte [Tutorial: Entrenamiento de un modelo de clasificación de imágenes con Azure Machine Learning Service](tutorial-train-models-with-aml.md).

El **registro de modelo** realiza un seguimiento de todos los modelos del área de trabajo de Azure Machine Learning Service.

Los modelos se identifican por el nombre y la versión. Cada vez que registra un modelo con el mismo nombre que uno ya existente, el registro interpreta que se trata de una nueva versión. La versión se incrementa y el nuevo modelo se registra con el nombre en cuestión.

Una vez registrado el modelo, puede proporcionar etiquetas de metadatos adicionales y, después, usar esas etiquetas al buscar modelos.

> [!TIP]
> Un modelo registrado es un contenedor lógico para uno o varios archivos que componen el modelo. Por ejemplo, si tiene un modelo que se almacena en varios archivos, puede registrarlos como un único modelo en el área de trabajo de Azure Machine Learning. Después del registro, puede descargar o implementar el modelo registrado y recibir todos los archivos que se registraron.

No se puede eliminar un modelo registrado que esté usando una implementación activa.

Para obtener un ejemplo de registro de un modelo, consulte [Entrenamiento de un modelo de clasificación de imágenes con Azure Machine Learning](tutorial-train-models-with-aml.md).

### <a name="run-configurations"></a>Configuraciones de ejecución

Una configuración de ejecución es un conjunto de instrucciones que define cómo se debe ejecutar un script en un destino de proceso determinado. Esta configuración incluye un amplio conjunto de definiciones de comportamiento como, por ejemplo, si quiere usar un entorno de Python existente o un entorno de Conda creado a partir de la especificación.

Una configuración de ejecución puede conservarse en un archivo del directorio que contiene el script de entrenamiento o puede crearse como un objeto en la memoria y usarse para enviar una ejecución.

Para ver configuraciones de ejecución de ejemplo, consulte [Selección y uso de un destino de proceso para entrenar el modelo](how-to-set-up-training-targets.md).

### <a name="estimators"></a>Estimadores

Para facilitar el entrenamiento de modelos con marcos conocidos, la clase Estimator le permite construir fácilmente configuraciones de ejecución. Puede crear y usar un objeto [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) genérico para enviar scripts de entrenamiento que usen cualquier plataforma de aprendizaje que elija (como scikit-learn).

En las tareas de PyTorch, TensorFlow y Chainer, Azure Machine Learning también proporciona los estimadores [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py) y [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) para simplificar el uso de estos marcos.

Para más información, consulte los siguientes artículos.

* [Entrenamiento de modelos de aprendizaje automático con estimadores](how-to-train-ml-models.md).
* [Entrenamiento de modelos de aprendizaje profundo de PyTorch a escala con Azure Machine Learning](how-to-train-pytorch.md).
* [Entrenamiento y registro de modelos de TensorFlow a escala con Azure Machine Learning Service](how-to-train-tensorflow.md).
* [Entrenamiento y registro de modelos de Chainer a escala con Azure Machine Learning Service](how-to-train-chainer.md).

### <a name="datasets-and-datastores"></a>Conjuntos de datos y almacenes de datos

**Conjuntos de datos de Azure Machine Learning** (versión preliminar) hace que sea más sencillo acceder a los datos y trabajar con ellos. Los conjuntos de datos administran datos en varios escenarios, como el entrenamiento de modelos y la creación de canalizaciones. Con el SDK de Azure Machine Learning, puede tener acceso al almacenamiento subyacente, explorar y preparar datos, administrar el ciclo de vida de las definiciones de conjuntos de datos diferentes y comparar entre conjuntos de datos utilizados en entrenamiento y en producción.

Los conjuntos de datos proporcionan métodos para trabajar con datos en formatos populares, como el uso de `from_delimited_files()` o `to_pandas_dataframe()`.

Para obtener más información, consulte [Creación y registro de los conjuntos de datos de Azure Machine Learning](how-to-create-register-datasets.md).  Para ver más ejemplos del uso de los conjuntos de datos, consulte los [cuadernos de ejemplo](https://github.com/Azure/MachineLearningNotebooks/tree/master/work-with-data/datasets).

Un **almacén de datos** es una abstracción de almacenamiento en una cuenta de Azure Storage. El almacén de datos puede usar un contenedor de blobs de Azure o un recurso compartido de archivos de Azure como almacenamiento back-end. Cada área de trabajo tiene un almacén de datos predeterminado en el que puede registrar almacenes de datos adicionales. Use la API del SDK de Python o la CLI de Azure Machine Learning para almacenar y recuperar archivos desde el almacén de datos.

### <a name="compute-targets"></a>Destinos de proceso

Un [destino de proceso](concept-compute-target.md) le permite especificar el recurso de proceso en el que se ejecuta el script de entrenamiento o se hospeda la implementación del servicio web. Esta ubicación puede ser su equipo local o un recurso de proceso en la nube. Los destinos de proceso facilitan el cambio entorno de proceso sin cambiar el código.

Obtenga más información sobre los [destinos de proceso disponibles para el entrenamiento y la implementación](concept-compute-target.md).

### <a name="training-scripts"></a>Scripts de entrenamiento

Para entrenar un modelo, especifique el directorio que contiene el script de entrenamiento y los archivos asociados. También puede especificar un nombre del experimento que se usará para almacenar la información recopilada durante el entrenamiento. Durante dicho proceso de entrenamiento, se copia todo el directorio en el entorno de entrenamiento (destino de proceso) y se inicia el script que especifique la configuración de ejecución. También se almacena una instantánea del directorio en el experimento del área de trabajo.

Si quiere ver un ejemplo, consulte [Tutorial: Entrenamiento de un modelo de clasificación de imágenes con Azure Machine Learning Service](tutorial-train-models-with-aml.md).

### <a name="runs"></a>Ejecuciones

Una ejecución es un registro que contiene la información siguiente:

* Metadatos sobre la ejecución (marca de tiempo, duración, etc.).
* Métricas que el script registra.
* Archivos de salida que recopila automáticamente el experimento o que carga explícitamente usted mismo.
* Instantánea del directorio que contiene los scripts antes de la ejecución.

Una ejecución se produce cuando se envía un script para entrenar un modelo. Una ejecución puede tener cualquier número de ejecuciones secundarias. Por ejemplo, la ejecución de nivel superior puede tener dos ejecuciones secundarias, cada una de las cuales puede tener sus propias ejecuciones secundarias.

### <a name="github-tracking-and-integration"></a>Integración y seguimiento de GitHub

Cuando se inicia una ejecución de entrenamiento en la que el directorio de origen es un repositorio de GIT local, se almacena información sobre el repositorio en el historial de ejecución. Por ejemplo, el identificador de confirmación actual para el repositorio se registra como parte del historial. Esto funciona con ejecuciones enviadas mediante un estimador, una canalización de aprendizaje automático o una ejecución del script. También funciona para las ejecuciones enviadas desde el SDK o la CLI de Machine Learning.

### <a name="snapshots"></a>Instantáneas

Al enviar una ejecución, Azure Machine Learning comprime el directorio que contiene el script como un archivo zip y lo envía al destino de proceso. A continuación, el archivo .zip se extrae y el script se ejecuta. Azure Machine Learning también almacena el archivo .zip como una instantánea como parte del registro de ejecución. Cualquier persona con acceso al área de trabajo puede buscar un registro de ejecución y descargar la instantánea.

> [!NOTE]
> Para evitar que se incluyan archivos innecesarios en la instantánea, cree un archivo ignore (.gitignore o .amlignore). Ubique este archivo en el directorio de instantáneas y agréguele los nombres de archivo que se deben ignorar. El archivo .amlignore usa [la misma sintaxis y los mismos patrones que el archivo .gitignore](https://git-scm.com/docs/gitignore). Si ambos archivos existen, el archivo .amlignore tiene prioridad.

### <a name="activities"></a>Actividades

Una actividad representa una operación de larga ejecución. Las operaciones siguientes son ejemplos de actividades:

* Creación o eliminación de un destino de proceso
* Ejecución de un script en un destino de proceso

Las actividades pueden proporcionar notificaciones a través del SDK o la interfaz de usuario web para que pueda supervisar fácilmente el progreso de estas operaciones.

### <a name="images"></a>Imágenes

Las imágenes ofrecen una forma fiable de implementar un modelo, junto con todos los componentes necesarios para usar el mismo. Una imagen contiene los siguientes elementos:

* Un modelo.
* Una aplicación o un script de puntuación. Este script se usa para pasar la entrada del modelo y devolver el resultado del mismo.
* Las dependencias que necesita el modelo, el script de puntuación o la aplicación. Por ejemplo, puede incluir un archivo de entorno de Conda en el que se detallen las dependencias de paquetes de Python.

Azure Machine Learning puede crear dos tipos de imágenes:

* **Imagen de FPGA**: se usa al realizar una implementación en una matriz de puerta programable de campo en Azure.
* **Imagen de Docker**: se usa al realizar implementaciones en destinos de proceso que no sean FPGA. Por ejemplo, Azure Container Instances y Azure Kubernetes Service.

Azure Machine Learning Service proporciona una imagen base, que se usa de forma predeterminada. También puede proporcionar sus propias imágenes personalizadas.

### <a name="image-registry"></a>Registro de imágenes

Las imágenes se catalogan en el **registro de imágenes** en el área de trabajo. Puede proporcionar etiquetas de metadatos adicionales al crear la imagen, así podrá consultarlas más adelante para encontrar la imagen.

Para obtener un ejemplo de creación de una imagen, consulte [Implementación de un modelo de clasificación de imágenes en Azure Container Instances](tutorial-deploy-models-with-aml.md).

Para obtener un ejemplo de implementación de un modelo con una imagen personalizada, consulte [Cómo implementar un modelo con una imagen personalizada de Docker](how-to-deploy-custom-docker-image.md).

### <a name="deployment"></a>Implementación

Una implementación es una instancia del modelo en un servicio web que puede hospedarse en la nube o un módulo de IoT para las implementaciones de dispositivos integrados.

#### <a name="web-service-deployments"></a>Implementaciones del servicio web

Un servicio web implementado puede usar Azure Container Instances, Azure Kubernetes Service o FPGA. El servicio se crea a partir del modelo, el script y los archivos asociados. Estos están encapsulados en una imagen, que proporciona el entorno de tiempo de ejecución para el servicio web. La imagen tiene un punto de conexión HTTP de carga equilibrada que recibe solicitudes de puntuación que se envían al servicio web.

Azure le ayuda a supervisar la implementación del servicio web mediante la recopilación de telemetría de Application Insight o telemetría de modelos, si es que ha optado por habilitar esta característica. Solo usted puede obtener acceso a los datos de telemetría, que se almacenan en las instancias de su cuenta de almacenamiento y Application Insights.

Si ha habilitado el ajuste automático de escala, Azure ajustará automáticamente la escala de su implementación.

Para obtener un ejemplo de implementación de un modelo como servicio web, consulte [Implementación de un modelo de clasificación de imágenes en Azure Container Instances](tutorial-deploy-models-with-aml.md).

#### <a name="iot-module-deployments"></a>Implementaciones de módulo de IoT

Un módulo de IoT implementado es un contenedor de Docker que incluye el modelo y el script asociado o la aplicación y las dependencias adicionales. Estos módulos se implementan con Azure IoT Edge en dispositivos Edge.

Si ha habilitado la supervisión, Azure recopila datos de telemetría desde el modelo que está en el módulo de Azure IoT Edge. Solo usted puede obtener acceso a los datos de telemetría, que se almacenan en las instancias de su cuenta de almacenamiento.

Azure IoT Edge garantiza que el módulo se esté ejecutando y supervisa el dispositivo que lo hospeda.

### <a name="ml-pipelines"></a>Canalizaciones de Machine Learning

Las canalizaciones de aprendizaje automático se usan para crear y administrar flujos de trabajo que unen las fases de aprendizaje automático. Por ejemplo, una canalización podría incluir las fases de preparación de los datos, entrenamiento del modelo, implementación del modelo e inferencia y puntuación. Cada fase puede estar formada por varios pasos, cada uno de los cuales puede ejecutarse en modo desatendido en varios destinos de proceso. 

Los pasos de canalización se pueden reutilizar y se pueden ejecutar sin volver a ejecutar los pasos subsiguientes si la salida de ese paso no ha cambiado. Por ejemplo, puede volver a entrenar un modelo sin volver a ejecutar los costosos pasos de preparación de datos si los datos no han cambiado. Las canalizaciones también permiten a los científicos de datos colaborar mientras trabajan en áreas independientes de un flujo de trabajo de Machine Learning.

Para obtener más información sobre las canalizaciones de aprendizaje automático con este servicio, consulte el artículo [Canalizaciones y Azure Machine Learning](concept-ml-pipelines.md).

### <a name="logging"></a>Registro

Al desarrollar la solución, use el SDK de Python de Azure Machine Learning en el script de Python para registrar las métricas arbitrarias. Después de la ejecución, consulte las métricas para determinar si la ejecución ha generado el modelo que quiere implementar.

### <a name="next-steps"></a>Pasos siguientes

Para comenzar a usar Azure Machine Learning Service, consulte:

* [¿Qué es Azure Machine Learning Service?](overview-what-is-azure-ml.md)
* [Creación de un área de trabajo de Azure Machine Learning Service](how-to-manage-workspace.md)
* [Tutorial (parte 1): Entrenamiento de un modelo](tutorial-train-models-with-aml.md)
