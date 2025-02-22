---
title: Problemas conocidos y soluciones
titleSuffix: Azure Machine Learning service
description: Obtenga una lista de los problemas conocidos, soluciones alternativas y soluciones para Azure Machine Learning Service.
services: machine-learning
author: j-martens
ms.author: jmartens
ms.reviewer: mldocs
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 08/09/2019
ms.custom: seodec18
ms.openlocfilehash: 74d345249e1cbaeb45a1a35d3c3d2f61a4c0b9cf
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69032966"
---
# <a name="known-issues-and-troubleshooting-azure-machine-learning-service"></a>Problemas conocidos y solución de problemas del servicio Azure Machine Learning

En este artículo obtendrá ayuda para buscar y corregir los errores que se producen al usar el servicio Azure Machine Learning.

## <a name="visual-interface-issues"></a>Problemas de la interfaz visual

Problemas de la interfaz visual para Machine Learning Service.

### <a name="long-compute-preparation-time"></a>Tiempo prolongado de preparación de un proceso

Crear un nuevo proceso o evocar un proceso saliente lleva tiempo, puede ser de minutos o incluso más. El equipo está trabajando para optimizarlo.


### <a name="cannot-run-an-experiment-only-contains-dataset"></a>No se puede ejecutar un experimento que solo contiene un conjunto de datos 

Puede querer ejecutar un experimento que solo contenga un conjunto de datos para visualizar el conjunto de datos. Sin embargo, en este momento no se permite ejecutar un experimento que solo contiene un conjunto de datos. Estamos corrigiendo este problema.
 
Antes de la corrección, puede conectar el conjunto de datos a cualquier módulo de transformación de datos (seleccione Select Columns in Dataset [Seleccionar columnas en el conjunto de datos], Edit Metadata [Editar metadatos], Split Data [Dividir datos], etc.) y ejecute el experimento. A continuación, puede visualizar el conjunto de datos. 

La imagen a continuación muestra cómo: ![visualize-data](./media/resource-known-issues/aml-visualize-data.png)

## <a name="sdk-installation-issues"></a>Problemas de instalación de SDK

**Mensaje de error: no se puede desinstalar 'PyYAML'**

SDK de Azure Machine Learning para Python: PyYAML es un proyecto instalado de Distutils. Por lo tanto, no se puede determinar con precisión qué archivos le pertenecen en caso de una desinstalación parcial. Para continuar con la instalación del SDK sin tener en cuenta este error, use:

```Python
pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML
```

**Mensaje de error: `ERROR: No matching distribution found for azureml-dataprep-native`**

La distribución Python 3.7.4 de Anaconda tiene un error que interrumpe la instalación de azureml-sdk. Este problema se trata en esta [incidencia de GitHub](https://github.com/ContinuumIO/anaconda-issues/issues/11195) y se puede solucionar creando un nuevo entorno de Conda mediante este comando:
```bash
conda create -n <env-name> python=3.7.3
```
El cual crea un entorno de Conda mediante Python 3.7.3, que no tiene la incidencia de instalación en la versión 3.7.4.

## <a name="trouble-creating-azure-machine-learning-compute"></a>Problemas al crear la instancia de Proceso de Azure Machine Learning

Es posible que algunos usuarios que crearon su área de trabajo de Azure Machine Learning en Azure Portal antes de la versión disponible de forma general no puedan crear la instancia de Proceso de Azure Machine Learning en esa área de trabajo. Puede generar una solicitud de soporte técnico en el servicio o crear una nueva área de trabajo mediante el portal o el SDK para desbloquearse a sí mismo inmediatamente.

## <a name="image-building-failure"></a>Error de creación de imagen

Error de creación de imagen al implementar el servicio web. La solución alternativa es agregar "pynacl==1.2.1" como una dependencia pip al archivo Conda para la configuración de la imagen.

## <a name="deployment-failure"></a>Error de implementación

Si observa `['DaskOnBatch:context_managers.DaskOnBatch', 'setup.py']' died with <Signals.SIGKILL: 9>`, cambie la SKU de las máquinas virtuales usadas en la implementación por otra que tenga más memoria.

## <a name="fpgas"></a>FPGA

No podrá implementar modelos en FPGA hasta que haya solicitado y se haya aprobado para la cuota FPGA. Para solicitar acceso, rellene el formulario de solicitud de cuota: https://aka.ms/aml-real-time-ai

## <a name="automated-machine-learning"></a>Automated Machine Learning

El aprendizaje automático automatizado de TensorFlow no admite actualmente la versión 1.13 de TensorFlow. Instalar esta versión hará que las dependencias del paquete dejen de funcionar. Estamos trabajando para corregir este problema en una versión futura. 

### <a name="experiment-charts"></a>Gráficos de experimento

Los gráficos de clasificación binaria (precisión-retirada, ROC, curva de ganancia, etc.) que se muestran en las iteraciones de experimentos de ML automatizados no se representan correctamente en la interfaz de usuario desde el 12/04. Los trazados de los gráficos actualmente muestran resultados inversos, donde los modelos con mejor rendimiento se muestran con resultados inferiores. Se está investigando una resolución.

## <a name="databricks"></a>Databricks

Problemas de Databricks y Azure Machine Learning.

### <a name="failure-when-installing-packages"></a>Error al instalar paquetes

No es posible instalar el SDK de Azure Machine Learning en Azure Databricks cuando se instalan más paquetes. Algunos paquetes, como `psutil`, pueden provocar conflictos. Para evitar errores de instalación, inmovilice la versión de la biblioteca para instalar los paquetes. Este problema está relacionado con Databricks y no con el SDK de Azure Machine Learning Service. También puede experimentar este problema con otras bibliotecas. Ejemplo:

```python
psutil cryptography==1.5 pyopenssl==16.0.0 ipython==2.2.0
```

Como alternativa, puede usar scripts de init si sigue experimentando problemas de instalación con las bibliotecas de Python. Este enfoque no se admite oficialmente. Para más información, consulte [Cluster-scoped init scripts](https://docs.azuredatabricks.net/user-guide/clusters/init-scripts.html#cluster-scoped-init-scripts) (Script de init del ámbito de clúster).

### <a name="cancel-an-automated-machine-learning-run"></a>Cancelar una ejecución de aprendizaje automático automatizado

Al usar las funcionalidades de aprendizaje automático automatizado en Azure Databricks, para cancelar una ejecución e iniciar una nueva ejecución de un experimento, reinicie el clúster de Azure Databricks.

### <a name="10-iterations-for-automated-machine-learning"></a>> 10 iteraciones para aprendizaje automático automatizado

En la configuración del aprendizaje automático automatizado, si tiene más de 10 iteraciones, establezca `show_output` en `False` cuando envíe la ejecución.

### <a name="widget-for-the-azure-machine-learning-sdkautomated-machine-learning"></a>Widget para el SDK de Azure Machine Learning/aprendizaje automático automatizado

El widget del SDK de Azure Machine Learning no se admite en un cuaderno de Databricks porque los cuadernos no pueden analizar los widgets HTML. Para ver el widget en el portal, use este código de Python en la celda del cuaderno de Azure Databricks:

```
displayHTML("<a href={} target='_blank'>Azure Portal: {}</a>".format(local_run.get_portal_url(), local_run.id))
```

### <a name="import-error-no-module-named-pandascoreindexes"></a>Error de importación: No hay ningún módulo denominado “pandas.core.indexes”

Si ve este error al usar el aprendizaje automático automatizado:

1. Ejecute este comando para instalar dos paquetes en el clúster de Azure Databricks: 

   ```
   scikit-learn==0.19.1
   pandas==0.22.0
   ```

1. Desasocie y, luego, vuelva a conectar el clúster al cuaderno. 

Si estos pasos no resuelven el problema, pruebe a reiniciar el clúster.

### <a name="failtosendfeather"></a>FailToSendFeather

Si ve un error `FailToSendFeather` al leer datos en un clúster de Azure Databricks, consulte las soluciones siguientes:

* Actualice el paquete `azureml-sdk[automl_databricks]` a la versión más reciente.
* Agregue `azure-dataprep` versión 1.1.8 o superior.
* Agregue `pyarrow` versión 0.11 o superior.

## <a name="azure-portal"></a>Portal de Azure

Si ve directamente el área de trabajo desde un vínculo de recurso compartido desde el SDK o el portal, no podrá ver la página de información general normal con la información de suscripción en la extensión. Tampoco será capaz de cambiar a otra área de trabajo. Si necesita ver otra área de trabajo, la solución consiste en ir directamente a [Azure Portal](https://portal.azure.com) y buscar el nombre de la misma.

## <a name="diagnostic-logs"></a>Registros de diagnóstico

A veces puede resultar útil proporcionar información de diagnóstico al solicitar ayuda. Para ver algunos registros, visite [Azure Portal](https://portal.azure.com), vaya al área de trabajo y seleccione **Área de trabajo > Experimento > Ejecutar > Registros**.

> [!NOTE]
> Azure Machine Learning Service registra información de varios orígenes durante el entrenamiento, como AutoML o el contenedor de Docker que ejecuta el trabajo de entrenamiento. Muchos de estos registros no están documentados. Si encuentra problemas y se pone en contacto con el soporte técnico de Microsoft, es posible que puedan usar estos registros durante la resolución de problemas.

## <a name="activity-logs"></a>Registros de actividad

Algunas acciones dentro del área de trabajo de Azure Machine Learning no registran información en el __registro de actividad__. Por ejemplo, el inicio de una ejecución de entrenamiento o el registro de un modelo.

Algunas de estas acciones aparecen en el área __Actividades__ del área de trabajo, pero no indican quién inició la actividad.

## <a name="resource-quotas"></a>Cuotas de recursos

Obtenga información sobre la [cuotas de recursos](how-to-manage-quotas.md) que puede encontrar al trabajar con Azure Machine Learning.

## <a name="authentication-errors"></a>Errores de autenticación

Si realiza una operación de administración en un destino de proceso desde un trabajo remoto, recibirá uno de los siguientes errores:

```json
{"code":"Unauthorized","statusCode":401,"message":"Unauthorized","details":[{"code":"InvalidOrExpiredToken","message":"The request token was either invalid or expired. Please try again with a valid token."}]}
```

```json
{"error":{"code":"AuthenticationFailed","message":"Authentication failed."}}
```

Por ejemplo, si intenta crear o asociar un destino de proceso desde una canalización de aprendizaje automático que se envía para ejecución remota, recibirá un error.

## <a name="overloaded-azurefile-storage"></a>Almacenamiento AzureFile sobrecargado

Si recibe un error `Unable to upload project files to working directory in AzureFile because the storage is overloaded`, aplique las siguientes soluciones alternativas.

Si usa un recurso compartido de archivos para otras cargas de trabajo, como la transferencia de datos, se recomienda usar blobs para que el recurso compartido de archivos se pueda usar para el envío de ejecuciones. También puede dividir la carga de trabajo entre dos áreas de trabajo diferentes.
