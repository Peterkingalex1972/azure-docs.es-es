---
title: 'Inicio rápido: Biblioteca cliente de Face para Python | Microsoft Docs'
description: Introducción a la biblioteca cliente de Face para Python...
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/26/2019
ms.author: pafarley
ms.openlocfilehash: 011345cca1ed1c763a628c94401320862182c9cc
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707353"
---
# <a name="quickstart-face-client-library-for-python"></a>Inicio rápido: Biblioteca cliente de Face para Python

Introducción a la biblioteca cliente de Face para Python. Siga estos pasos para instalar el paquete y probar el código de ejemplo para realizar tareas básicas. El servicio Face API le proporciona acceso a algoritmos avanzados para detectar y reconocer caras humanas en imágenes.

Use la biblioteca cliente de Face para Python para:

* Detectar caras en una imagen
* Búsqueda de caras similares
* Crear y entrenar un grupo de personas
* Identificar una cara
* Tomar una instantánea para la migración de datos

[Documentación de referencia](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-vision-face) | [Paquete (PiPy)](https://pypi.org/project/azure-cognitiveservices-vision-face/) | [Ejemplos](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=Face&sort=0)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/)
* [Python 3.x](https://www.python.org/)

## <a name="setting-up"></a>Instalación

### <a name="create-a-face-azure-resource"></a>Creación de un recurso de Azure para Face

Los servicios de Azure Cognitive Services se representan por medio de recursos de Azure a los que se suscribe. Cree un recurso para Face mediante [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) o la [CLI de Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) en la máquina local. También puede:

* Obtener una [clave de prueba](https://azure.microsoft.com/try/cognitive-services/#decision) válida durante siete días de forma gratuita Después de registrarse, estará disponible en el [sitio web de Azure](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Ver este recurso en [Azure Portal](https://portal.azure.com/)

Después de obtener una clave de la suscripción de evaluación o el recurso, [cree una variable de entorno](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para ella denominada `FACE_SUBSCRIPTION_KEY`.
 
### <a name="create-a-new-python-application"></a>Creación de una nueva aplicación de Python

Cree un nuevo script de Python, por ejemplo, &mdash;*quickstart-file.py*. Ábralo en el editor o el IDE que prefiera e importe las siguientes bibliotecas.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_imports)]

A continuación, cree variables para el punto de conexión y la clave de Azure del recurso. Es posible que deba cambiar la primera parte del punto de conexión (`westus`) para que coincida con la suscripción.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_subvars)]

> [!NOTE]
> Si ha creado la variable de entorno después de haber iniciado la aplicación, deberá cerrar y volver a abrir el editor, el IDE o el shell que lo ejecuta para acceder a la variable.

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Puede instalar la biblioteca cliente con lo siguiente:

```console
pip install --upgrade azure-cognitiveservices-Face
```

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases e interfaces controlan algunas de las características principales del SDK de Python para Face.

|NOMBRE|DESCRIPCIÓN|
|---|---|
|[FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python) | Esta clase representa la autorización para usar el servicio Face, se necesita para que Face funcione correctamente. Cree una instancia de ella con la información de suscripción y úsela para generar instancias de otras clases. |
|[FaceOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python)|Esta clase controla las tareas básicas de detección y reconocimiento que puede realizar con las caras humanas. |
|[DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python)|Esta clase representa todos los datos que se detectaron de una única cara en una imagen. Puede usarla para recuperar información detallada sobre la cara.|
|[FaceListOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.facelistoperations?view=azure-python)|Esta clase administra las construcciones **FaceList** almacenadas en la nube, que almacenan a su vez un conjunto ordenado de caras. |
|[PersonGroupPersonOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongrouppersonoperations?view=azure-python)| Esta clase administra las construcciones **Person** almacenadas en la nube, que almacenan a su vez un conjunto de caras que pertenecen a una sola persona.|
|[PersonGroupOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.persongroupoperations?view=azure-python)| Esta clase administra las construcciones **PersonGroup** almacenadas en la nube, que almacenan a su vez un conjunto de objetos **Person** ordenados. |
|[ShapshotOperations](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.snapshotoperations?view=azure-python)|Esta clase administra la funcionalidad de instantáneas; puede usarla para guardar temporalmente todos los datos de caras en la nube y migrar los datos a una nueva suscripción de Azure. |

## <a name="code-examples"></a>Ejemplos de código

En estos fragmentos de código se muestra cómo realizar las siguientes tareas con la biblioteca cliente de Face para Python:

* [Autenticar el cliente](#authenticate-the-client)
* [Detección de caras en una imagen](#detect-faces-in-an-image)
* [Búsqueda de caras similares](#find-similar-faces)
* [Creación y entrenamiento de un grupo de personas](#create-and-train-a-person-group)
* [Identificación de una cara](#identify-a-face)
* [Realización de una instantánea para la migración de datos](#take-a-snapshot-for-data-migration)

## <a name="authenticate-the-client"></a>Autenticar el cliente

> [!NOTE]
> En este inicio rápido se da por supuesto que ha [creado una variable de entorno](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) para la clave de Face denominada `FACE_SUBSCRIPTION_KEY`.

Cree una instancia de un cliente con la clave y el punto de conexión. Cree un objeto [CognitiveServicesCredentials](https://docs.microsoft.com/python/api/msrest/msrest.authentication.cognitiveservicescredentials?view=azure-python) con la clave y úselo con el punto de conexión para crear un objeto [FaceClient](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.faceclient?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_auth)]

## <a name="detect-faces-in-an-image"></a>Detectar caras en una imagen

El siguiente código detecta una cara en una imagen remota. Imprime el identificador de la cara detectada en la consola y también lo almacena en la memoria del programa. A continuación, detecta las caras en una imagen con varias personas e imprime también sus identificadores en la consola. Al cambiar los parámetros en el método [detect_with_url](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#detect-with-url-url--return-face-id-true--return-face-landmarks-false--return-face-attributes-none--recognition-model--recognition-01---return-recognition-model-false--detection-model--detection-01---custom-headers-none--raw-false----operation-config-), puede devolver información diferente con cada objeto [DetectedFace](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.models.detectedface?view=azure-python).

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detect)]

Consulte el código de ejemplo en [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py) para más escenarios de detección.

## <a name="find-similar-faces"></a>Búsqueda de caras similares

El siguiente código toma una sola cara detectada y busca coincidencias en un conjunto de otras caras. Cuando la encuentra, imprime las coordenadas del rectángulo de la cara coincidente en la consola. 

### <a name="find-matches"></a>Búsqueda de coincidencias

En primer lugar, ejecute el código de la sección anterior ([Detectar caras en una imagen](#detect-faces-in-an-image)) para guardar una referencia a una sola cara. A continuación, ejecute el siguiente código para obtener referencias a varias caras de una imagen de grupo.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_detectgroup)]

Agregue el siguiente bloque de código para buscar las instancias de la primera cara del grupo. Consulte el método [find_similar](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/azure.cognitiveservices.vision.face.operations.faceoperations?view=azure-python#find-similar-face-id--face-list-id-none--large-face-list-id-none--face-ids-none--max-num-of-candidates-returned-20--mode--matchperson---custom-headers-none--raw-false----operation-config-) para aprender a modificar este comportamiento.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar)]

### <a name="print-matches"></a>Impresión de las coincidencias

Use el siguiente código para imprimir los detalles coincidentes en la consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_findsimilar_print)]

## <a name="create-and-train-a-person-group"></a>Crear y entrenar un grupo de personas

En el siguiente código se crea un objeto **PersonGroup** con tres objetos **Person** distintos. Asocia cada **Person** con un conjunto de imágenes de ejemplo y entrena para reconocer a cada persona. 

### <a name="create-persongroup"></a>Creación de un objeto PersonGroup

Para seguir en este escenario, debe guardar las siguientes imágenes en el directorio raíz del proyecto: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

Este grupo de imágenes contiene tres conjuntos de imágenes de caras correspondientes a tres personas distintas. El código definirá tres objetos **Person** y los asociará con archivos de imagen que comienzan por `woman`, `man` y `child`.

Una vez configuradas las imágenes, defina una etiqueta en la parte superior del script para el objeto **PersonGroup** que va a crear.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroupvars)]

Después, agregue el siguiente código al final del script. Este código crea un objeto **PersongGroup** y tres objetos **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_create)]

### <a name="assign-faces-to-persons"></a>Asignación de caras a los objetos Person

El siguiente código ordenal las imágenes por su prefijo, y detecta caras y las asigna a cada objeto **Person**.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_assign)]

### <a name="train-persongroup"></a>Entrenamiento del objeto PersonGroup

Una vez asignadas las caras, debe entrenar el objeto **PersonGroup** para que identifique las características visuales asociadas con cada uno de sus objetos **Person**. El siguiente código llama al método **train** asincrónico, sondea el resultado e imprime el estado en la consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_persongroup_train)]

## <a name="identify-a-face"></a>Identificar una cara

El siguiente código toma una imagen con varias caras y busca la identidad de cada persona de esta imagen. Compara cada cara detectada con un objeto **PersonGroup**, una base de datos con distintos objetos **Person** con distintas caras asociadas. 

> [!IMPORTANT]
> Para ejecutar este ejemplo, primero debe ejecutar el código de [Creación y entrenamiento de un grupo de personas](#create-and-train-a-person-group).

### <a name="get-a-test-image"></a>Obtención de una imagen de prueba

El siguiente código busca en la raíz del proyecto una imagen _test-image-person-group.jpg_ y detecta las caras de la imagen. Puede encontrar esta imagen con las que se usan para la administración de **PersonGroup**: https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/Face/images.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify_testimage)]

### <a name="identify-faces"></a>Identificación de caras

El método **identify** toma una matriz de caras detectadas y las compara con un objeto **PersonGroup**. Si puede hacer coincidir una cara detectada con un objeto **Person**, guarda el resultado. Este código imprime los resultados detallados de las coincidencias en la consola.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_identify)]

## <a name="take-a-snapshot-for-data-migration"></a>Tomar una instantánea para la migración de datos

La característica de instantáneas permite trasladar los datos de la cara guardados, como un objeto **PersonGroup** entrenado, a otra suscripción de Face de Azure Cognitive Services. Quizás quiera usar esta característica si, por ejemplo, ha creado un objeto **PersonGroup** mediante una suscripción de evaluación gratuita y ahora desea migrarlo a una de pago. Consulte el artículo de [Migración de los datos de las caras](../Face-API-How-to-Topics/how-to-migrate-face-data.md) para información general de la característica de instantáneas.

En este ejemplo migrará el objeto **PersonGroup** que ha creado en [Creación y entrenamiento de un grupo de personas](#create-and-train-a-person-group). Puede completar esa sección primero o usar sus propias construcciones de Face.

### <a name="set-up-target-subscription"></a>Configuración de la suscripción de destino

En primer lugar, debe tener una segunda suscripción de Azure con un recurso de Face; puede crearlo si sigue los pasos descritos en la sección [Instalación](#setting-up). 

A continuación, cree las siguientes variables cerca de la parte superior del script. También necesitará crear nuevas variables de entorno para el identificador de suscripción de la cuenta de Azure, así como la clave y el identificador de suscripción de la cuenta nueva (de destino). 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshotvars)]

### <a name="authenticate-target-client"></a>Autenticación del cliente de destino

Más adelante en el script, guarde el objeto de cliente actual como cliente de origen y autentique un nuevo objeto de cliente para la suscripción de destino. 

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_auth)]

### <a name="use-a-snapshot"></a>Uso de una instantánea

El resto de las operaciones de instantánea tienen lugar dentro de una función asincrónica. 

1. El primer paso es **tomar** la instantánea, que guarda los datos de la cara de la suscripción original en una ubicación temporal en la nube. Este método devuelve un identificador que se utiliza para consultar el estado de la operación.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_take)]

1. Después, consulte el identificador hasta que se haya completado la operación.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait)]

    Este código usa la función `wait_for_operation`, que debe definir por separado:

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_waitforop)]

1. Vuelva a la función asincrónica. Use la operación **apply** para escribir los datos de la cara en la suscripción de destino. Este método también devuelve un identificador.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_apply)]

1. De nuevo, use la función `wait_for_operation` para consultar el identificador hasta que se haya completado la operación.

    [!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_snapshot_wait2)]

Una vez que haya completado estos pasos, podrá acceder a las construcciones de datos faciales desde la nueva suscripción (de destino).

## <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute la aplicación con el comando `python` en el archivo de inicio rápido.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.

* [Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#clean-up-resources)
* [CLI de Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli#clean-up-resources)

Si ha creado un objeto **PersonGroup** en este inicio rápido y desea eliminarlo, ejecute el siguiente código en el script:

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletegroup)]

Si migró los datos mediante la característica de instantánea, también deberá eliminar el objeto **PersonGroup** guardado en la suscripción de destino.

[!code-python[](~/cognitive-services-quickstart-code/python/Face/FaceQuickstart.py?name=snippet_deletetargetgroup)]

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido ha aprendido a usar la biblioteca de Face para Java para realizar tareas básicas. A continuación, consulte la documentación de referencia para más información sobre la biblioteca.

> [!div class="nextstepaction"]
> [Referencia de Face API (Python)](https://docs.microsoft.com/python/api/azure-cognitiveservices-vision-face/?view=azure-python)

* [¿Qué es Face API?](../overview.md)
* El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/Face/FaceQuickstart.py).