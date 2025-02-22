---
title: 'Inicio rápido: Detección de caras en una imagen con la API REST de Azure y Java'
titleSuffix: Azure Cognitive Services
description: En este tutorial, usará la API REST de Azure Face con Java para detectar caras en una imagen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: d014785a0e866301e228458fe3742b899bd1f192
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606969"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-java"></a>Inicio rápido: Detección de caras en una imagen mediante la API REST y Java

En este tutorial, usará la API REST de Azure Face con Java para detectar caras humanas en una imagen.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

## <a name="prerequisites"></a>Requisitos previos

- Una clave de suscripción de Face API. Puede obtener una clave de la suscripción de evaluación gratuita en la página [Pruebe Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). O bien, siga las instrucciones de [Creación de una cuenta de Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para suscribirse al servicio Face API y obtener la clave.
- Cualquier entorno de desarrollo integrado de Java de su elección.

## <a name="create-the-java-project"></a>Creación del proyecto de Java

1. Cree una nueva aplicación de Java de la línea de comandos en el IDE y agregue una clase **Main** con un método **main**.
1. Importe las bibliotecas siguientes en el proyecto de Java. Si usa Maven, se proporcionan las coordenadas de Maven para cada biblioteca.
   - [Cliente HTTP de Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpclient:4.5.6)
   - [Núcleo HTTP de Apache](https://hc.apache.org/downloads.cgi) (org.apache.httpcomponents:httpcore:4.4.10)
   - [Biblioteca JSON](https://github.com/stleary/JSON-java) (org.json:json:20180130)
   - [Registro de Apache Commons](https://commons.apache.org/proper/commons-logging/download_logging.cgi) (commons-loggins: commons-loggins:1.1.2)

## <a name="add-face-detection-code"></a>Adición del código de detección de caras

Abra la clase main del proyecto. Aquí, agregará el código necesario para cargar imágenes y detectar caras.

### <a name="import-packages"></a>Importación de paquetes

Agregue las siguientes instrucciones `import` en la parte superior del archivo.

```java
// This sample uses Apache HttpComponents:
// http://hc.apache.org/httpcomponents-core-ga/httpcore/apidocs/
// https://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/

import java.net.URI;
import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpPost;
import org.apache.http.entity.StringEntity;
import org.apache.http.client.utils.URIBuilder;
import org.apache.http.impl.client.HttpClientBuilder;
import org.apache.http.util.EntityUtils;
import org.json.JSONArray;
import org.json.JSONObject;
```

### <a name="add-essential-fields"></a>Adición de los campos esenciales

Reemplace la clase **Main** por el código siguiente. Estos datos especifican cómo conectarse al servicio Face y dónde obtener los datos de entrada. Deberá actualizar el campo `subscriptionKey` con el valor de la clave de suscripción y es posible que deba cambiar la cadena `uriBase` para que contenga el identificador de la región correcta (consulte la [documentación de Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obtener una lista de los puntos de conexión de todas las regiones). También es posible que desee establecer el valor de `imageWithFaces` en una ruta de acceso que apunte a un archivo de imagen diferente.

El campo `faceAttributes` es simplemente una lista de determinados tipos de atributos. Especificará qué información recuperar sobre las caras detectadas.

```Java
public class Main {
    // Replace <Subscription Key> with your valid subscription key.
    private static final String subscriptionKey = "<Subscription Key>";

    // NOTE: You must use the same region in your REST call as you used to
    // obtain your subscription keys. For example, if you obtained your
    // subscription keys from westus, replace "westcentralus" in the URL
    // below with "westus".
    //
    // Free trial subscription keys are generated in the "westus" region. If you
    // use a free trial subscription key, you shouldn't need to change this region.
    private static final String uriBase =
        "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect";

    private static final String imageWithFaces =
        "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}";

    private static final String faceAttributes =
        "age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

### <a name="call-the-face-detection-rest-api"></a>Llamada a la API REST de detección de caras

Agregue el método **main** con el código siguiente. Construye una llamada REST a Face API para detectar información de caras en la imagen remota (la cadena `faceAttributes` especifica qué atributos de cara recuperar). A continuación, escribe los datos de salida en una cadena JSON.

```Java
    public static void main(String[] args) {
        HttpClient httpclient = HttpClientBuilder.create().build();

        try
        {
            URIBuilder builder = new URIBuilder(uriBase);

            // Request parameters. All of them are optional.
            builder.setParameter("returnFaceId", "true");
            builder.setParameter("returnFaceLandmarks", "false");
            builder.setParameter("returnFaceAttributes", faceAttributes);

            // Prepare the URI for the REST API call.
            URI uri = builder.build();
            HttpPost request = new HttpPost(uri);

            // Request headers.
            request.setHeader("Content-Type", "application/json");
            request.setHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

            // Request body.
            StringEntity reqEntity = new StringEntity(imageWithFaces);
            request.setEntity(reqEntity);

            // Execute the REST API call and get the response entity.
            HttpResponse response = httpclient.execute(request);
            HttpEntity entity = response.getEntity();
```

### <a name="parse-the-json-response"></a>Procese la respuesta JSON.

Agregue el siguiente bloque directamente debajo del código anterior, que convierte los datos JSON devueltos en un formato más fácil de leer antes de imprimirlo en la consola. Por último, cierre el bloque try-catch, el método **main** y la clase **Main**.

```Java
            if (entity != null)
            {
                // Format and display the JSON response.
                System.out.println("REST Response:\n");

                String jsonString = EntityUtils.toString(entity).trim();
                if (jsonString.charAt(0) == '[') {
                    JSONArray jsonArray = new JSONArray(jsonString);
                    System.out.println(jsonArray.toString(2));
                }
                else if (jsonString.charAt(0) == '{') {
                    JSONObject jsonObject = new JSONObject(jsonString);
                    System.out.println(jsonObject.toString(2));
                } else {
                    System.out.println(jsonString);
                }
            }
        }
        catch (Exception e)
        {
            // Display error message.
            System.out.println(e.getMessage());
        }
    }
}
```

## <a name="run-the-app"></a>Ejecución de la aplicación

Compile el código y ejecútelo. Una respuesta correcta mostrará datos de las caras en formato JSON fácilmente legible en la ventana de consola. Por ejemplo:

```json
[{
  "faceRectangle": {
    "top": 131,
    "left": 177,
    "width": 162,
    "height": 162
  },
  "faceAttributes": {
    "makeup": {
      "eyeMakeup": true,
      "lipMakeup": true
    },
    "facialHair": {
      "sideburns": 0,
      "beard": 0,
      "moustache": 0
    },
    "gender": "female",
    "accessories": [],
    "blur": {
      "blurLevel": "low",
      "value": 0.06
    },
    "headPose": {
      "roll": 0.1,
      "pitch": 0,
      "yaw": -32.9
    },
    "smile": 0,
    "glasses": "NoGlasses",
    "hair": {
      "bald": 0,
      "invisible": false,
      "hairColor": [
        {
          "color": "brown",
          "confidence": 1
        },
        {
          "color": "black",
          "confidence": 0.87
        },
        {
          "color": "other",
          "confidence": 0.51
        },
        {
          "color": "blond",
          "confidence": 0.08
        },
        {
          "color": "red",
          "confidence": 0.08
        },
        {
          "color": "gray",
          "confidence": 0.02
        }
      ]
    },
    "emotion": {
      "contempt": 0,
      "surprise": 0.005,
      "happiness": 0,
      "neutral": 0.986,
      "sadness": 0.009,
      "disgust": 0,
      "anger": 0,
      "fear": 0
    },
    "exposure": {
      "value": 0.67,
      "exposureLevel": "goodExposure"
    },
    "occlusion": {
      "eyeOccluded": false,
      "mouthOccluded": false,
      "foreheadOccluded": false
    },
    "noise": {
      "noiseLevel": "low",
      "value": 0
    },
    "age": 22.9
  },
  "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f"
}]
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado una sencilla aplicación de consola de Java que usa llamadas REST a Azure Face API para detectar caras en una imagen y devolver sus atributos. A continuación, obtenga información sobre cómo hacer más cosas con esta funcionalidad en una aplicación Android.

> [!div class="nextstepaction"]
> [Tutorial: Creación de una aplicación Android para detectar y enmarcar caras](../Tutorials/FaceAPIinJavaForAndroidTutorial.md)
