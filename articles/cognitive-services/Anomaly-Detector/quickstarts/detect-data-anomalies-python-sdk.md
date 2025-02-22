---
title: 'Inicio rápido: Detección de anomalías en los datos mediante la biblioteca cliente de Anomaly Detector para Python'
titleSuffix: Azure Cognitive Services
description: Use la API Anomaly Detector para detectar anomalías en la serie de datos como un lote o en la transmisión de datos.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 08/01/2019
ms.author: aahi
ms.openlocfilehash: 59a4d79cc68c57faf54bde3d42370fb17a317325
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725556"
---
# <a name="quickstart-anomaly-detector-client-library-for-python"></a>Inicio rápido: Biblioteca cliente de Anomaly Detector para Python

Comience a usar la biblioteca cliente de Anomaly Detector para .NET. Siga estos pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas. El servicio de Anomaly Detector le permite detectar anomalías en los datos de serie temporal mediante el uso automático de los mejores modelos, independientemente del sector, el escenario o el volumen de datos.

Use la biblioteca cliente de Anomaly Detector para Python para las siguientes acciones:

* Detectar anomalías en el conjunto de datos de serie temporal como una solicitud por lotes
* Detectar el estado de anomalía del punto de datos más reciente en la serie temporal

[Documentación de referencia de la biblioteca](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector?view=azure-python) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-anomalydetector) | [Paquete (PyPi)](https://pypi.org/project/azure-cognitiveservices-anomalydetector/) | [Ejemplos](https://github.com/Azure-Samples/anomalydetector)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)
* La [biblioteca de análisis de datos de Pandas](https://pandas.pydata.org/)
 
## <a name="setting-up"></a>Instalación

### <a name="create-an-anomaly-detector-resource"></a>Creación de un recurso de Anomaly Detector

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

Después de obtener una clave de la suscripción de evaluación o el recurso, [cree una variable de entorno](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para ella denominada `ANOMALY_DETECTOR_KEY`.

### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

 Cree una aplicación de Python en el IDE o editor que prefiera. A continuación, importe las bibliotecas siguientes.

[!code-python[import declarations](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=imports)]

Cree variables para la clave como una variable de entorno, la ruta de acceso a un archivo de datos de serie temporal y la ubicación de Azure de su suscripción. Por ejemplo, `westus2`. 

[!code-python[Vars for the key, path location and data path](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=initVars)]

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Después de instalar Python, puede instalar la biblioteca cliente con:

```console
pip install --upgrade azure-cognitiveservices-anomalydetector
```

## <a name="object-model"></a>Modelo de objetos

El cliente de Anomaly Detector es un objeto [AnomalyDetectorClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python) que se autentica en Azure mediante la clave. El cliente proporciona dos métodos de detección de anomalías: en un conjunto de datos completo mediante [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) y en el punto de datos más reciente mediante [Last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-). 

Los datos de serie temporal se envían como una serie de objetos [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point(class)?view=azure-python) en un objeto [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request(class)?view=azure-python). El objeto `Request` contiene propiedades para describir los datos ([Granularidad](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python), por ejemplo), así como los parámetros para la detección de anomalías. 

La respuesta de Anomaly Detector es un objeto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) o [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python), según el método usado. 

## <a name="code-examples"></a>Ejemplos de código 

Estos fragmentos de código muestran cómo realizar las siguientes acciones con la biblioteca cliente de Anomaly Detector para .NET:

* [Autenticar el cliente](#authenticate-the-client)
* [Cargar un conjunto de datos de serie temporal desde un archivo](#load-time-series-data-from-a-file)
* [Detectar anomalías en todo el conjunto de datos](#detect-anomalies-in-the-entire-data-set) 
* [Detectar el estado de anomalía del punto de datos más reciente](#detect-the-anomaly-status-of-the-latest-data-point)

## <a name="authenticate-the-client"></a>Autenticar el cliente

Agregue la variable de ubicación de Azure al punto de conexión y autentique el cliente con la clave.

[!code-python[Client authentication](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=client)]

## <a name="load-time-series-data-from-a-file"></a>Cargar datos de serie temporal desde un archivo

Descargue los datos de ejemplo de este inicio rápido desde [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/example-data/request-data.csv):
1. En el explorador, haga clic con el botón derecho en **Raw** (Sin formato).
2. Haga clic en **Save link as** (Guardar vínculo como).
3. Guarde el archivo como .csv en el directorio de aplicaciones.

Estos datos de serie temporal tienen el formato de un archivo .csv y se enviarán a Anomaly Detector API.

Cargue el archivo de datos con el método `read_csv()` de la biblioteca de Pandas y cree una variable de lista vacía para almacenar la serie de datos. Itere el archivo y anexe los datos como un objeto [Point](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.point%28class%29?view=azure-python). Este objeto contendrá la marca de tiempo y el valor numérico de las filas del archivo de datos .csv. 

[!code-python[Load the data file](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=loadDataFile)]

Cree un objeto [Request](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.request%28class%29?view=azure-python) con la serie temporal y la [granularidad](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.granularity?view=azure-python) (o periodicidad) de sus puntos de datos. Por ejemplo, `Granularity.daily`.

[!code-python[Create the request object](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=request)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Detectar anomalías en todo el conjunto de datos 

Llame a la API para detectar anomalías en el conjunto de datos de serie temporal mediante el método [entire_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#entire-detect-body--custom-headers-none--raw-false----operation-config-) del cliente. Almacene el objeto [EntireDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.entiredetectresponse?view=azure-python) devuelto. Itere la lista `is_anomaly` de la respuesta e imprima el índice de los valores `true`. Estos valores se corresponden con el índice de los puntos de datos anómalos, si se detecta alguno.

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=detectAnomaliesBatch)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Detectar el estado de anomalía del punto de datos más reciente

Llame a Anomaly Detector API para determinar si el punto de datos más reciente es una anomalía mediante el método [last_detect()](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.anomalydetectorclient?view=azure-python#last-detect-body--custom-headers-none--raw-false----operation-config-) del cliente y almacene el objeto [LastDetectResponse](https://docs.microsoft.com/python/api/azure-cognitiveservices-anomalydetector/azure.cognitiveservices.anomalydetector.models.lastdetectresponse?view=azure-python) devuelto. El valor `is_anomaly` de la respuesta es un valor booleano que especifica el estado de anomalía de ese punto.  

[!code-python[Batch anomaly detection sample](~/samples-anomaly-detector/quickstarts/sdk/python-sdk-sample.py?name=latestPointDetection)]

## <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute la aplicación en el IDE o en la línea de comandos con el comando `python` y el nombre del archivo.
 
## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a dicho grupo.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI de Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
>[Streaming de detección de anomalías con Azure Databricks](../tutorials/anomaly-detection-streaming-databricks.md)

* ¿Qué es [Anomaly Detector API?](../overview.md)
* [Procedimientos recomendados](../concepts/anomaly-detection-best-practices.md) cuando se usa Anomaly Detector API.
* El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs).
