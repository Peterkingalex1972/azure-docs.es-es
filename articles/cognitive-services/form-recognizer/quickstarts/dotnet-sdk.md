---
title: 'Inicio rápido: Biblioteca cliente de Form Recognizer para .NET | Microsoft Docs'
description: Introducción a la biblioteca cliente de Form Recognizer para .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 07/12/2019
ms.author: pafarley
ms.openlocfilehash: f6cb364c231c2415bf49a24e6e9406a14640b892
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600120"
---
# <a name="quickstart-form-recognizer-client-library-for-net"></a>Inicio rápido: Biblioteca cliente de Form Recognizer para .NET

Introducción a la biblioteca cliente de Form Recognizer para .NET. Azure Form Recognizer es un servicio cognitivo que utiliza tecnología de aprendizaje automático para identificar y extraer pares clave-valor, así como datos de tablas de los documentos de formularios. A continuación, genera datos estructurados que incluyen las relaciones del fichero original. Siga estos pasos para instalar el paquete SDK y probar el código de ejemplo para realizar tareas básicas.

Utilice la biblioteca cliente de Form Recognizer para .NET para:

* Entrenar un modelo personalizado de Form Recognizer
* Analizar formularios con un modelo personalizado
* Obtener una lista de modelos personalizados

[Documentación de referencia](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/formrecognizer?view=azure-dotnet-preview) | [Código fuente de la biblioteca](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.FormRecognizer) | [Paquete (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.FormRecognizer/)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/).
* La versión actual de [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Un blob de Azure Storage que contenga un conjunto de datos de entrenamiento. Consulte [Build a training data set for a custom model](../build-training-data-set.md) (Creación de un conjunto de datos de aprendizaje para un modelo personalizado) para ver sugerencias y opciones para reunir los datos de entrenamiento. 

## <a name="setting-up"></a>Instalación

### <a name="create-a-form-recognizer-azure-resource"></a>Creación de un recurso de Azure de Form Recognizer

[!INCLUDE [create resource](../includes/create-resource.md)]

Después de obtener una clave de la suscripción de evaluación o el recurso, [cree una variable de entorno](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) para ella denominada `FORM_RECOGNIZER_KEY`.

### <a name="create-a-new-c-application"></a>Creación de una aplicación de C#

En una ventana de consola (por ejemplo, cmd, PowerShell o Bash), use el comando `dotnet new` para crear una nueva aplicación de consola con el nombre `formrecognizer-quickstart`. Este comando crea un sencillo proyecto "Hola mundo" de C# con un solo archivo de origen: _Program.cs_. 

```console
dotnet new console -n formrecognizer-quickstart
```

Cambie el directorio a la carpeta de aplicaciones recién creada. A continuación, compile la aplicación con:

```console
dotnet build
```

La salida de la compilación no debe contener advertencias ni errores. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

En el directorio del proyecto, abra el archivo _Program.cs_ en el editor o IDE que prefiera. Agregue las instrucciones siguientes `using` :

```csharp
using Microsoft.Azure.CognitiveServices.FormRecognizer;
using Microsoft.Azure.CognitiveServices.FormRecognizer.Models;

using System;
using System.IO;
using System.Threading.Tasks;
```

Después, agregue el código siguiente en el método **Main** de la aplicación. Definirá esta tarea asincrónica más adelante.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_main)]

### <a name="install-the-client-library"></a>Instalación de la biblioteca cliente

Dentro del directorio de aplicaciones, instale la biblioteca cliente de Form Recognizer para .NET con el siguiente comando:

```console
dotnet add package Microsoft.Azure.CognitiveServices.FormRecognizer --version 0.8.0-preview
```

Si usa el IDE de Visual Studio, la biblioteca cliente estará disponible como un paquete de NuGet descargable.

## <a name="object-model"></a>Modelo de objetos

Las siguientes clases controlan la funcionalidad principal del SDK de Form Recognizer.

|NOMBRE|DESCRIPCIÓN|
|---|---|
|[FormRecognizerClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.formrecognizerclient?view=azure-dotnet-preview)|Esta clase es necesaria para todas las funcionalidades de Form Recognizer. Cree una instancia de ella con la información de suscripción y úsela para generar instancias de otras clases.|
|[TrainRequest](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainrequest?view=azure-dotnet-preview)| Utilice esta clase para entrenar un modelo personalizado de Form Recognizer con sus propios datos de entrada de entrenamiento. |
|[TrainResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.trainresult?view=azure-dotnet-preview)| Esta clase proporciona los resultados de una operación de entrenamiento de modelo personalizado, incluido el identificador de modelo, que puede usar para analizar formularios. |
|[AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)| Esta clase proporciona los resultados de una operación de análisis de modelo personalizado. Incluye una lista de instancias de **ExtractedPage**. |
|[ExtractedPage](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.extractedpage?view=azure-dotnet-preview)| Esta clase representa todos los datos extraídos de un único documento de formulario.|

## <a name="code-examples"></a>Ejemplos de código

<!--
    Include code snippets and short descriptions for each task you list in the the bulleted list. Briefly explain each operation, but include enough clarity to explain complex or otherwise tricky operations.

    Include links to the service's reference content when introducing a class for the first time
-->

Estos fragmentos de código muestran cómo realizar las siguientes tareas con la biblioteca cliente de Form Recognizer para .NET:

* [Autenticar el cliente](#authenticate-the-client)
* [Entrenar un modelo personalizado de Form Recognizer](#train-a-custom-model)
* [Analizar formularios con un modelo personalizado](#analyze-forms-with-a-custom-model)
* [Obtener una lista de modelos personalizados](#get-a-list-of-custom-models)

### <a name="define-variables"></a>Definición de variables

Antes de definir métodos, agregue las siguientes definiciones de variable a la parte superior de la clase **Program**. También tendrá que rellenar manualmente algunas de las variables. 

* Puede encontrar el valor del punto de conexión de servicio en la sección **Información general** de Azure Portal. 
* Para recuperar la dirección URL de SAS para los datos de entrenamiento, abra el Explorador de Microsoft Azure Storage, haga clic con el botón derecho en el contenedor y seleccione **Obtener firma de acceso compartido**. Asegúrese de que los permisos de **lectura** y **enumeración** están marcados y haga clic en **Create** (Crear). A continuación, copie el valor en la sección **URL**. Debe tener el formato `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_variables)]

### <a name="authenticate-the-client"></a>Autenticar el cliente

Debajo del método `Main`, defina la tarea a la que se hace referencia en `Main`. Aquí, autenticará el objeto de cliente mediante las variables de suscripción que definió anteriormente. Más adelante, definirá los demás métodos.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_maintask)]

### <a name="train-a-custom-model"></a>Entrenamiento de un modelo personalizado

El método siguiente usa el objeto de cliente de Form Recognizer para entrenar un nuevo modelo de reconocimiento en los documentos almacenados en el contenedor de blobs de Azure. Utiliza un método auxiliar para mostrar información sobre el nuevo modelo entrenado (representado por un objeto [ModelResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelresult?view=azure-dotnet-preview)) y devuelve el identificador del modelo.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_train)]

El siguiente método auxiliar muestra información sobre un modelo de Form Recognizer.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displaymodel)]

### <a name="analyze-forms-with-a-custom-model"></a>Analizar formularios con un modelo personalizado

Este método usa el cliente de Form Recognizer y un identificador de modelo para analizar un documento de formulario PDF y extraer los datos de clave-valor. Utiliza un método auxiliar para mostrar los resultados (representados por un objeto [AnalyzeResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.analyzeresult?view=azure-dotnet-preview)).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_analyzepdf)]

El siguiente método auxiliar muestra información sobre una operación de análisis.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_displayanalyze)]

### <a name="get-a-list-of-custom-models"></a>Obtener una lista de modelos personalizados

Puede devolver una lista de todos los modelos entrenados que pertenecen a su cuenta y recuperar información sobre cuándo se crearon. La lista de modelos está representada por un objeto [ModelsResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.formrecognizer.models.modelsresult?view=azure-dotnet-preview).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/Program.cs?name=snippet_getmodellist)]

## <a name="run-the-application"></a>Ejecución de la aplicación

Ejecute la aplicación llamando al comando `dotnet run` desde el directorio de aplicaciones.

```console
dotnet run
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere limpiar y eliminar una suscripción a Cognitive Services, puede eliminar el recurso o grupo de recursos. Al eliminar el grupo de recursos, también se elimina cualquier otro recurso que esté asociado a él.

* [Portal](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [CLI de Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Pasos siguientes

En este inicio rápido, ha usado la biblioteca cliente de .NET de Form Recognizer para entrenar un modelo personalizado y analizar formularios. A continuación, obtenga sugerencias para crear un mejor conjunto de datos de entrenamiento y generar modelos más precisos.

> [!div class="nextstepaction"]
>[Creación de un conjunto de datos de aprendizaje](../build-training-data-set.md)

* [¿Qué es Form Recognizer?](../overview.md)
* El código fuente de este ejemplo está disponible en [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/FormRecognizer).
