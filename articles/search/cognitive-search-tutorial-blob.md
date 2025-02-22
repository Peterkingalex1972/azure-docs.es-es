---
title: 'Tutorial de REST: Creación de una canalización de enriquecimiento de inteligencia artificial mediante la búsqueda cognitiva: Azure Search'
description: Recorra un ejemplo de extracción de texto y de procesamiento de lenguaje natural sobre el contenido de blobs JSON mediante Postman y las API REST de Azure Search.
manager: pablocas
author: luiscabrer
services: search
ms.service: search
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: e647d3c66d339a60278fa7d0f078497157b3fff1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102808"
---
# <a name="tutorial-add-structure-to-unstructured-content-with-cognitive-search"></a>Tutorial: Adición de una estructura a "contenido no estructurado" con la búsqueda cognitiva

Si tiene contenido de imagen o texto no estructurado, la característica de [ búsqueda cognitiva](cognitive-search-concept-intro.md) de Azure Search puede ayudarle a extraer información y crear contenido útil para escenarios de búsqueda de texto completo o de minería de conocimiento. Aunque la búsqueda cognitiva puede procesar archivos de imagen (JPG, PNG y TIFF), este tutorial se centra en contenido basado en palabras y se aplica la detección de idioma y el análisis de texto para crear campos e información que puede aprovechar en consultas, aspectos y filtros.

> [!div class="checklist"]
> * Comience con documentos completos (texto no estructurado) como PDF, MD, DOCX y PPTX en Azure Blob Storage.
> * Cree una canalización que extraiga texto, detecte el idioma, reconozca entidades y detecte frases clave.
> * Defina un índice para almacenar la salida (contenido sin procesar, además de pares nombre-valor generados por canalización).
> * Ejecute la canalización para crear y cargar el índice.
> * Explore el contenido mediante la búsqueda de texto completo y una sintaxis de consulta enriquecida.

Para completar este tutorial necesitará varios servicios, además de la [aplicación de escritorio Postman](https://www.getpostman.com/) u otra herramienta de pruebas web para realizar llamadas a la API REST. 

Si no tiene una suscripción a Azure, abra una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="download-files"></a>Descarga de archivos

1. Abra esta [carpeta de OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) y, en la esquina superior izquierda, haga clic en **Descargar** para copiar los archivos en el equipo. 

1. Haga clic con el botón derecho en el archivo ZIP y seleccione **Extraer todo**. Hay 14 archivos de varios tipos. Para este ejercicio, usará 7.

## <a name="1---create-services"></a>1: Creación de servicios

En este tutorial se usa Azure Search para los índices y las consultas, Cognitive Services para el enriquecimiento de la inteligencia artificial y Azure Blob Storage para proporcionar los datos. Si es posible, cree los tres servicios en la misma región y grupo de recursos por proximidad y capacidad de administración. En la práctica, la cuenta de Azure Storage puede estar en cualquier región.

### <a name="start-with-azure-storage"></a>Comienzo con Azure Storage

1. [Inicie sesión en Azure Portal](https://portal.azure.com/) y haga clic en **+ Crear un recurso**.

1. Busque *cuenta de almacenamiento* y seleccione la oferta de Cuenta de almacenamiento de Microsoft.

   ![Crear cuenta de almacenamiento](media/cognitive-search-tutorial-blob/storage-account.png "Create Storage account")

1. En la pestaña Datos básicos, se necesitan los siguientes elementos. Acepte los valores predeterminados para todo lo demás.

   + **Grupo de recursos**. Seleccione un grupo existente o cree uno nuevo, pero use el mismo grupo para todos los servicios, con el fin de que pueda administrarlos colectivamente.

   + **Nombre de cuenta de almacenamiento**. Si cree que puede tener varios recursos del mismo tipo, use el nombre para diferenciarlos por tipo y región, por ejemplo *blobstoragewestus*. 

   + **Ubicación**. Si es posible, elija la misma ubicación que se usa para Azure Search y Cognitive Services. Una ubicación única anula los cargos de ancho de banda.

   + **Tipo de cuenta**. Elija el valor predeterminado, *StorageV2 (uso general v2)* .

1. Haga clic en **Revisar y crear** para crear el servicio.

1. Una vez creado, haga clic en **Go to the resource** (Ir al recurso) para abrir la página de información general.

1. Haga clic en el servicio **Blobs**.

1. Haga clic en **+ Contenedor** para crear un contenedor y asígnele el nombre *cog-search-demo*.

1. Seleccione *cog-search-demo* y haga clic en **Cargar** para abrir la carpeta en la que guardó los archivos de descarga. Seleccione todos los archivos que no sean de imagen. Debería tener 7 archivos. Haga clic en **Aceptar** para empezar a cargar.

   ![Cargar archivos de ejemplo](media/cognitive-search-tutorial-blob/sample-files.png "Upload sample files")

1. Antes de salir de Azure Storage, obtenga una cadena de conexión para poder formular una conexión en Azure Search. 

   1. Vuelva a la página de información general de la cuenta de almacenamiento (hemos usado *blobstragewestus* como ejemplo). 
   
   1. En el panel de navegación izquierdo, seleccione **Claves de acceso** y copie una de las cadenas de conexión. 

   La cadena de conexión es una dirección URL parecida a la del ejemplo siguiente:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Guarde la cadena de conexión en el Bloc de notas. La necesitará más adelante, al configurar la conexión del origen de datos.

### <a name="cognitive-services"></a>Cognitive Services

El enriquecimiento de la inteligencia artificial en la búsqueda cognitiva está respaldado por Cognitive Services, lo que incluye Text Analytics y Computer Vision para el procesamiento de imágenes y del lenguaje natural. Si su objetivo era completar un prototipo o proyecto reales, en este momento aprovisionaría Cognitive Services (en la misma región que Azure Search) para poder conectarlo a las operaciones de indexación.

Sin embargo, para este ejercicio puede omitir el aprovisionamiento de recursos, porque Azure Search puede conectarse a Cognitive Services en segundo plano y darle veinte transacciones gratuitas por cada ejecución del indexador. Como en este tutorial se usan 7 transacciones, la asignación gratuita es suficiente. En el caso de proyectos mayores, planee aprovisionamiento de Cognitive Services en el nivel S0 de pago por uso. Para más información, consulte [Asociación de Cognitive Services](cognitive-search-attach-cognitive-services.md).

### <a name="azure-search"></a>Azure Search

El tercer componente es Azure Search, que se puede [crear en el portal](search-create-service-portal.md). Puede usar el nivel Gratis para completar este tutorial. 

Al igual que con Azure Blob Storage dedique un momento a recopilar la clave de acceso. Además, cuando empiece a estructurar las solicitudes, deberá proporcionar el punto de conexión y la clave de la API de administración que se usan para autenticar cada solicitud.

### <a name="get-an-admin-api-key-and-url-for-azure-search"></a>Obtención de una clave de API de administración y una dirección URL para Azure Search

1. [Inicie sesión en Azure Portal](https://portal.azure.com/) y, en la página **Información general** del servicio de búsqueda, obtenga el nombre del servicio de búsqueda. Para confirmar el nombre del servicio, revise la dirección URL del punto de conexión. Si la dirección URL del punto de conexión fuera `https://mydemo.search.windows.net`, el nombre del servicio sería `mydemo`.

2. En **Configuración** > **Claves**, obtenga una clave de administrador para tener derechos completos en el servicio. Se proporcionan dos claves de administrador intercambiables para lograr la continuidad empresarial, por si necesitara sustituir una de ellas. Puede usar la clave principal o secundaria en las solicitudes para agregar, modificar y eliminar objetos.

    Obtenga también la clave de consulta. Es una práctica recomendada emitir solicitudes de consulta con acceso de solo lectura.

![Obtención del nombre del servicio y las claves de consulta y administrador](media/search-get-started-nodejs/service-name-and-keys.png)

Todas las solicitudes enviadas al servicio necesitan una clave de API en el encabezado. Una clave válida genera la confianza, solicitud a solicitud, entre la aplicación que la envía y el servicio que se encarga de ella.

## <a name="2---set-up-postman"></a>2: Configuración de Postman

Inicie Postman y configure una solicitud HTTP. Si no está familiarizado con esta herramienta, consulte [Exploración de las API REST de Azure Search mediante Postman](search-get-started-postman.md).

Los métodos de solicitud usados en este tutorial son **POST**, **PUT** y **GET**. Usaremos los métodos siguientes para realizar cuatro llamadas API al servicio de búsqueda: crear un origen de datos, un conjunto de aptitudes, un índice y un indexador.

En Encabezados, establezca "Content-Type" en `application/json` y establezca `api-key` en la clave de API de administración de su servicio de Azure Search. Una vez que establezca los encabezados, puede usarlos para todas las solicitudes de este ejercicio.

  ![URL y encabezado de solicitud Postman](media/search-get-started-postman/postman-url.png "Postman request URL and header")

## <a name="3---create-the-pipeline"></a>3: Creación de la canalización

En Azure Search, el procesamiento de la inteligencia artificial se produce durante la indexación (o la ingesta de datos). En esta parte del tutorial se crean cuatro objetos: origen de datos, definición de índice, conjunto de aptitudes, indexador. 

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

Un [objeto de origen de datos](https://docs.microsoft.com/rest/api/searchservice/create-data-source) proporciona la cadena de conexión al contenedor de blobs que contiene los archivos.

1. Use **POST** y la dirección URL siguiente, reemplazando NOMBRE-DE-SU-SERVICIO por el nombre real del servicio.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2019-05-06
   ```

1. En el **cuerpo**de la solicitud, copie la siguiente definición de JSON y reemplace `connectionString` por la conexión real de la cuenta de almacenamiento. 

   No olvide editar también el nombre del contenedor. En un paso anterior sugerimos "cog-search-demo" como nombre del contenedor.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Envíe la solicitud. Debería ver un código de estado 201 que confirme que la operación se ha realizado correctamente. 

Si obtuvo un error 403 o 404, compruebe la construcción de la solicitud: `api-version=2019-05-06` debe estar en el punto de conexión, `api-key` debe estar en el encabezado después de `Content-Type` y su valor debe ser válido para un servicio de búsqueda. Puede que desee ejecutar el documento JSON a través de un validador JSON en línea para asegurarse de que la sintaxis es correcta. 

### <a name="step-2-create-a-skillset"></a>Paso 2: Creación de un conjunto de aptitudes

Un [objeto conjunto de aptitudes](https://docs.microsoft.com/rest/api/searchservice/create-skillset) es un conjunto de pasos de enriquecimiento aplicados al contenido. 

1. Use **PUT** y la dirección URL siguiente, reemplazando NOMBRE-DE-SU-SERVICIO por el nombre real del servicio.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-ss?api-version=2019-05-06
    ```

1. En el **cuerpo**de la solicitud, copie la siguiente definición de JSON. Este conjunto de aptitudes consta de las siguientes aptitudes integradas.

   | Habilidad                 | DESCRIPCIÓN    |
   |-----------------------|----------------|
   | [Reconocimiento de entidades](cognitive-search-skill-entity-recognition.md) | Extrae los nombres de las personas, organizaciones y ubicaciones del contenido del contenedor de blobs. |
   | [Detección de idioma](cognitive-search-skill-language-detection.md) | Detecta el idioma del contenido. |
   | [División de texto](cognitive-search-skill-textsplit.md)  | Divide el contenido de gran tamaño en fragmentos más pequeños antes de llamar a la aptitud de extracción de frases clave. La extracción de frases clave acepta entradas de 50 000 caracteres o menos. Algunos de los archivos de ejemplo deben dividirse para no superar este límite. |
   | [Extracción de frases clave](cognitive-search-skill-keyphrases.md) | Extrae las principales frases clave. |

   Cada aptitud se ejecuta en el contenido del documento. Durante el procesamiento, Azure Search extrae cada documento para leer el contenido de diferentes formatos de archivo. El texto encontrado procedente del archivo de origen se coloca en un campo ```content``` generado, uno para cada documento. Como tal, la entrada pasa a ser ```"/document/content"```.

   Para la extracción de las frases clave, al usar el separador de texto para dividir los archivos grandes en páginas, el contenido de la aptitud de la extracción de frases clave es ```"document/pages/*"``` (para cada página del documento) en lugar de ```"/document/content"```.

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    A continuación se muestra una representación gráfica del conjunto de aptitudes. 

    ![Comprender un conjunto de aptitudes](media/cognitive-search-tutorial-blob/skillset.png "Comprender un conjunto de aptitudes")

1. Envíe la solicitud. Postman debe devolver un código de estado 201 que confirme que la operación se ha realizado correctamente. 

> [!NOTE]
> Las salidas se pueden asignar a un índice, usar como entrada para una aptitud descendente, o ambas cosas como sucede con el código de idioma. En el índice, un código de idioma es útil para el filtrado. Como entrada, el código de idioma se usa en las aptitudes de análisis de texto para informar a las reglas lingüísticas de la separación de palabras. Para obtener más información sobre los conceptos básicos del conjunto de aptitudes, consulte el tema sobre la [definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>Paso 3: Creación de un índice

Un [índice](https://docs.microsoft.com/rest/api/searchservice/create-index) proporciona el esquema que se usa para crear la expresión física del contenido en índices invertidos y otras construcciones en Azure Search. El componente más grande de un índice es la colección de campos, donde el tipo de datos y los atributos determinan el contenido y los comportamientos en Azure Search.

1. Use **PUT** y la dirección URL siguiente, reemplazando NOMBRE-DE-SU-SERVICIO por el nombre real del servicio, para asignar un nombre al índice.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2019-05-06
   ```

1. En el **cuerpo**de la solicitud, copie la siguiente definición de JSON. El campo `content` almacena el propio documento. Los campos adicionales para `languageCode`, `keyPhrases` y `organizations` representan la nueva información (campos y valores) creada por el conjunto de aptitudes.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Envíe la solicitud. Postman debe devolver un código de estado 201 que confirme que la operación se ha realizado correctamente. 

### <a name="step-4-create-and-run-an-indexer"></a>Paso 4: Creación y ejecución de un indexador

Un [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer) rige la canalización. Los tres componentes que ha creado hasta ahora (origen de datos, conjunto de aptitudes e índice) son entradas para un indexador. La creación del indexador en Azure Search es el evento que pone toda la canalización en movimiento. 

1. Use **PUT** y la dirección URL siguiente, reemplazando NOMBRE-DE-SU-SERVICIO por el nombre real del servicio, para asignar un nombre al indexador.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
   ```

1. En el **cuerpo**de la solicitud, copie la siguiente definición de JSON. Observe los elementos de asignación de campos; estas asignaciones son importantes porque definen el flujo de datos. 

   `fieldMappings` se procesa antes que el conjunto de aptitudes y envía el contenido del origen de datos a los campos de destino de un índice. Usará asignaciones de campos para enviar contenido existente sin modificar al índice. Si los tipos y nombres de campo son los mismos en ambos extremos, no se requiere ninguna asignación.

   `outputFieldMappings` es para los campos creados por las aptitudes y, por tanto, se procesa después de que se haya ejecutado el conjunto de aptitudes. Las referencias a `sourceFieldNames` en `outputFieldMappings` no existen hasta que el descifrado de documentos o el enriquecimiento las crean. `targetFieldName` es un campo de un índice definido en el esquema del índice.

    ```json
    {
      "name":"cog-search-demo-idxr",    
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Envíe la solicitud. Postman debe devolver un código de estado 201 que confirme el éxito del procesamiento. 

   Este paso puede tardar varios minutos en completarse. Aunque el conjunto de datos es pequeño, las aptitudes analíticas realiza un uso intensivo de los recursos. 

> [!NOTE]
> La creación de un indexador invoca la canalización. Si hay problemas para conectar con los datos, las entradas y salidas de asignación o el orden de las operaciones, se muestran en esta fase. Para volver a ejecutar la canalización con los cambios de código o script, deberá quitar primero los objetos. Para más información, consulte [Restablecer y volver a ejecutar](#reset).

#### <a name="about-indexer-parameters"></a>Acerca de los parámetros de un indexador

El script establece ```"maxFailedItems"``` en -1, que indica al motor de indexación que ignore los errores durante la importación de datos. Esto es aceptable porque hay muy pocos documentos en el origen de datos de demostración. Para un origen de datos mayor, debería establecer un valor mayor que 0.

La instrucción ```"dataToExtract":"contentAndMetadata"``` indica al indexador que extraiga automáticamente el contenido de diferentes formatos de archivo, así como los metadatos relacionados con cada archivo. 

Una vez extraído el contenido, puede establecer ```imageAction``` para que se extraiga el texto de las imágenes que se encuentran en el origen de datos. La configuración de ```"imageAction":"generateNormalizedImages"```, en combinación con OCR Skill y Text Merge Skill, le dice al indexador que extraiga texto de las imágenes (por ejemplo, la palabra "stop" de una señal de Stop de tráfico) y lo inserte como parte del campo de contenido. Este comportamiento se aplica a las imágenes incrustadas en los documentos (piense en una imagen de un archivo PDF), así como a las imágenes que se encuentran en el origen de datos, como un archivo JPG.

## <a name="4---monitor-indexing"></a>4: Supervisión de la indexación

La indexación y el enriquecimiento comienzan en cuanto se envía la solicitud de creación de indexador. En función de las aptitudes cognitivas definidas, la indexación puede tardar un tiempo. Para averiguar si el indexador todavía se está ejecutando, envíe la solicitud siguiente para comprobar el estado del indexador.

1. Use **GET** y la dirección URL siguiente, reemplazando NOMBRE-DE-SU-SERVICIO por el nombre real del servicio, para asignar un nombre al indexador.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2019-05-06
   ```

1. Revise la respuesta para saber si el indexador se está ejecutando o para ver la información de los errores y advertencias.  

Si usa el nivel Gratis, se espera el siguiente mensaje: "Could not extract content or metadata from your document. Truncated extracted text to '32768' characters" (No se pudieron extraer el contenido ni los metadatos del documento. El texto extraído se ha truncado a los '32 768' caracteres). Este mensaje aparece porque la indexación de blobs en el nivel tiene un límite de [32 000 en la extracción de caracteres](search-limits-quotas-capacity.md#indexer-limits). En los niveles superiores no verá este mensaje para este conjunto de datos. 

> [!NOTE]
> Las advertencias son comunes en algunos escenarios y no siempre indican un problema. Por ejemplo, si un contenedor de blobs incluye archivos de imagen y la canalización no controla imágenes, recibirá una advertencia que indica que no se procesaron las imágenes.

## <a name="5---search"></a>5: Búsqueda

Ahora que ha creado nuevos campos e información, vamos a ejecutar algunas consultas para conocer el valor de la búsqueda cognitiva en lo que se refiere a un escenario de búsqueda típico.

Recuerde que comenzamos con el contenido del blob, donde todo el documento se empaqueta en un solo campo `content`. Puede buscar en este campo elementos que coincidan con sus consultas.

1. Use **GET** y la siguiente dirección URL, reemplazando NOMBRE-DE-SU-SERVICIO por el nombre real del servicio, para buscar instancias de un término o frase, y devolviendo el campo `content` y el número de documentos coincidentes.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?search=*&$count=true&$select=content?api-version=2019-05-06
   ```
   
   Los resultados de esta consulta devuelven el contenido del documento, que es el mismo resultado que obtendría si usara el indexador de blob sin la canalización de búsqueda cognitiva. En este campo se pueden realizar búsquedas, pero no se puede trabajar en él si se desean usar facetas, filtros o la función de autocompletar.

   ![Salida del campo de contenido](media/cognitive-search-tutorial-blob/content-output.png "Content field output")
   
1. Para la segunda consulta, devuelva algunos de los nuevos campos que ha creado la canalización (persons, organizations, locations, languageCode). Se omite keyPhrases para no extenderse en exceso, pero es preciso incluirlo si se desea ver esos valores.

   ```http
   https://mydemo.search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2019-05-06
   ```
   Los campos de la instrucción $select contienen información nueva creada a partir de las funcionalidades de procesamiento de lenguaje natural de Cognitive Services. Como cabría esperar, hay cierto ruido en los resultados y variación entre los documentos, pero en muchos casos, los modelos analíticos generan resultados precisos.

   En la imagen siguiente se muestran los resultados de la carta abierta que escribió Satya Nadella cuando aceptó el puesto de CEO en Microsoft.

   ![Salida de canalización](media/cognitive-search-tutorial-blob/pipeline-output.png "Pipeline output")

1. Para ver cómo puede aprovechar estos campos, agregue un parámetro facet para que se devuelva una agregación de los documentos coincidentes por ubicación.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2019-05-06
   ``` 

   En este ejemplo, para cada ubicación hay 2 o 3 coincidencias.

   ![Salida de facet](media/cognitive-search-tutorial-blob/facet-output.png "Facet output")
   

1. En este ejemplo final, aplique un filtro a la colección de organizaciones, lo que devolverá dos coincidencias para los criterios de filtro basados en NASDAQ.

   ```http
   cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2019-05-06
   ```

Estas consultas muestran algunas de las formas en que puede trabajar con la sintaxis de consulta y los filtros en los nuevos campos creados por la búsqueda cognitiva. Para más ejemplos de consultas, vea [Ejemplos de la API REST de búsqueda de documentos](https://docs.microsoft.com/rest/api/searchservice/search-documents#bkmk_examples), [Ejemplos de consulta de sintaxis simple](search-query-simple-examples.md) y [Ejemplos de consultas que usan la sintaxis de búsqueda de Lucene "completa"](search-query-lucene-examples.md).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Restablecer y volver a ejecutar

En las primeras etapas experimentales del desarrollo de canalizaciones, el enfoque más práctico para las iteraciones de diseño es eliminar los objetos de Azure Search y permitir que el código vuelva a generarlos. Los nombres de los recursos son únicos. La eliminación de un objeto permite volver a crearlo con el mismo nombre.

Para volver a indexar los documentos con las nuevas definiciones:

1. Elimine el indexador, el índice y el conjunto de aptitudes.
2. Modifique los objetos.
3. Vuelva a crearlos en el servicio para ejecutar la canalización. 

Puede usar el portal para eliminar índices, indexadores y conjuntos de aptitudes, o bien usar **DELETE** y especificar direcciones URL a cada objeto. El siguiente comando elimina un indexador.

```http
DELETE https://[YOUR-SERVICE-NAME]].search.windows.net/indexers/cog-search-demo-idxr?api-version=2019-05-06
```

Se devuelve el código de estado 204 si la eliminación se realiza correctamente.

A medida que el código evoluciona, puede refinar una estrategia de regeneración. Para más información, consulte [Volver a generar un índice](search-howto-reindex.md).

## <a name="takeaways"></a>Puntos clave

Este tutorial muestra los pasos básicos para la creación de una canalización de indexación enriquecida a través de la creación de componentes: un origen de datos, un conjunto de aptitudes, un índice y un indexador.

Las [aptitudes predefinidas](cognitive-search-predefined-skills.md) se presentan, junto con la definición del conjunto de aptitudes y los mecanismos de encadenamiento de aptitudes, a través de entradas y salidas. También ha aprendido que `outputFieldMappings` en la definición del indexador se necesita para enrutar los valores enriquecidos de la canalización a un índice que permita búsquedas en un servicio Azure Search.

Por último, ha aprendido cómo probar los resultados y restablecer el sistema para otras iteraciones. Ha aprendido que emitir consultas en el índice devuelve la salida creada por la canalización de indexación enriquecida. 

## <a name="clean-up-resources"></a>Limpieza de recursos

La manera más rápida de borrar el contenido después de un tutorial es eliminar el grupo de recursos que contiene el servicio Azure Search y el servicio Azure Blob. Si decide colocar ambos servicios en el mismo grupo, elimine el grupo de recursos para eliminar de manera permanente todo lo que contiene, incluyendo los servicios y cualquier contenido almacenado que haya creado para este tutorial. En el portal, el nombre del grupo de recursos está en la página Información general de cada servicio.

## <a name="next-steps"></a>Pasos siguientes

Personalice o extienda la canalización con aptitudes personalizadas. Al crear una aptitud personalizada y agregarla a un conjunto de aptitudes, puede incorporar texto o análisis de imágenes que escriba usted mismo. 

> [!div class="nextstepaction"]
> [Ejemplo: Creación de una aptitud de búsqueda cognitiva personalizada](cognitive-search-create-custom-skill-example.md)