---
title: 'Inicio rápido: Detección de caras en una imagen con la API REST de Azure y cURL'
titleSuffix: Azure Cognitive Services
description: En esta guía de inicio rápido, usará la API REST de Azure Face con cURL para detectar caras en una imagen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 07/03/2019
ms.author: pafarley
ms.openlocfilehash: 68cdd147977294954051735d70307305aa5dc0cb
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603328"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Inicio rápido: Detección de caras en una imagen mediante Face REST API y cURL

En esta guía de inicio rápido, usará la API REST de Azure Face con cURL para detectar caras humanas en una imagen.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. 

## <a name="prerequisites"></a>Requisitos previos

- Una clave de suscripción de Face API. Puede obtener una clave de la suscripción de evaluación gratuita en la página [Pruebe Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). O bien, siga las instrucciones de [Creación de una cuenta de Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) para suscribirse al servicio Face API y obtener la clave.

## <a name="write-the-command"></a>Escritura del comando
 
Utilizará un comando como el siguiente para llamar a la API Face y obtener los datos de los atributos de las caras de una imagen. En primer lugar, copie el código en un editor de texto; deberá realizar cambios en ciertas partes del comando antes de su ejecución.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Subscription key
Reemplace `<Subscription Key>` por una clave de suscripción de Face API válida.

### <a name="face-endpoint-url"></a>Dirección URL del punto de conexión de Face

La dirección URL `https://westcentralus.api.cognitive.microsoft.com/face/v1.0/detect` indica el punto de conexión de Azure Face para las consultas. Deberá cambiar la primera parte de esta dirección URL para que coincida con la región que corresponde a la clave de suscripción. Consulte la documentación sobre la [API Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) para obtener una lista de todos los puntos de conexión de la región.

### <a name="url-query-string"></a>Cadena de consulta de URL

La cadena de consulta de la dirección URL del punto de conexión de Face especifica qué atributos de cara recuperar. Es posible que desee cambiar esta cadena según su uso previsto.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Dirección URL del origen de la imagen
La dirección URL del origen indica la imagen que se usará como entrada. Puede cambiarla para que apunte a cualquier imagen que desee analizar.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Ejecute el comando

Una vez realizados los cambios, abra un símbolo del sistema y escriba el nuevo comando. Debería ver la información de la cara mostrada como datos JSON en la ventana de la consola. Por ejemplo:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
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
      }
    }
  }
]
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha escrito un comando de cURL que llama a Azure Face API para detectar caras en una imagen y devolver sus atributos. A continuación, explore la documentación de referencia de Face API para más información.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
