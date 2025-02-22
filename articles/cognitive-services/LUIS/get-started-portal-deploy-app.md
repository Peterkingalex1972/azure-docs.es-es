---
title: 'Inicio rápido: Implementación de una aplicación con el portal de LUIS'
titleSuffix: Azure Cognitive Services
description: Para obtener información sobre cómo implementar la aplicación de LUIS en el punto de conexión de la predicción una vez que la aplicación está lista para devolver predicciones de expresiones a una aplicación cliente, como un bot de chat. En este tutorial de inicio rápido se ofrece orientación sobre cómo implementar una aplicación mediante la creación de un recurso del punto de conexión de la predicción, la asignación de dicho recurso a la aplicación, el entrenamiento de la aplicación y la publicación de la aplicación.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 5c310c1943eaf23423be873c6172e27c621fe109
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564066"
---
# <a name="quickstart-deploy-an-app-in-the-luis-portal"></a>Inicio rápido: Implementación de una aplicación en el portal de LUIS

Una vez que la aplicación de LUIS está lista para devolver predicciones de expresiones a una aplicación cliente (por ejemplo, un bot de chat), es preciso implementar la aplicación en el punto de conexión de la predicción.

En esta guía de inicio rápido, aprenderá a implementar una aplicación. Cree un recurso de punto de conexión de predicción, asigne el recurso a la aplicación, entrene la aplicación y publíquela.

## <a name="prerequisites"></a>Requisitos previos

* Obtener una [suscripción de Azure](https://azure.microsoft.com/free).
* Completar el [inicio rápido del portal anterior](get-started-portal-build-app.md) o [descargar e importar la aplicación](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/in-portal/build-portal-app.json).

## <a name="create-the-endpoint-resource"></a>Creación del recurso del punto de conexión

Cree el recurso del punto de conexión de predicción en Azure Portal. Dicho recurso solo se debe usar para las consultas de predicción de punto de conexión. No se debe usar para la creación de los cambios en la aplicación.

1. Inicie sesión en el [Azure Portal](https://ms.portal.azure.com/).

1. Seleccione el símbolo **+** verde en el panel superior izquierdo. Busque `Cognitive Services` en Marketplace y selecciónelo.

1. Configure la suscripción con los valores siguientes:

   |Configuración|Valor|Propósito|
   |--|--|--|
   |NOMBRE|`my-cognitive-service-resource`|El nombre del recurso de Azure. Este nombre será necesario al asignar el recurso a la aplicación en el portal de LUIS.|
   |Subscription|Su suscripción|Seleccione una de las suscripciones asociadas a su cuenta.|
   |Location|**Oeste de EE. UU.**|La región de Azure de este recurso.|
   |Plan de tarifa|**S0**|El plan de tarifa predeterminado de este recurso.|
   |Resource group|`my-cognitive-service-resource-group`|Cree un grupo de recursos para todos los recursos de Cognitive Services. Cuando haya terminado con los recursos, puede eliminar el grupo de recursos para limpiar la suscripción. |
   | | | |

   ![Elección de la API de Azure](./media/get-started-portal-deploy-app/create-cognitive-services-resource.png)

1. Seleccione **Crear** para crear el recurso de Azure.

   En la siguiente sección, aprenderá a conectar este recurso nuevo a una aplicación de LUIS en el portal de LUIS.

## <a name="assign-the-resource-key-to-the-luis-app-in-the-luis-portal"></a>Asignar la clave de recurso a la aplicación de LUIS en el portal de LUIS

Cada vez que crea un recurso para LUIS, será preciso que lo asigne a la aplicación de LUIS. Una vez que lo asigne, no tendrá que volver a realizar este paso, a menos que cree otro recurso. Puede crear un recurso para expandir las regiones de la aplicación o para admitir un mayor número de consultas de predicción.

1. Inicie sesión en el [portal de LUIS](https://www.luis.ai) y elija la aplicación **myEnglishApp** en la lista de aplicaciones.

1. Seleccione **Manage** (Administrar) en el menú superior derecho y, después, seleccione **Keys and endpoints** (Claves y puntos de conexión).

1. Para agregar LUIS, seleccione **Asignar recurso +** .

   [![Asignar un recurso a la aplicación](./media/get-started-portal-deploy-app/assign-resource-button.png)](./media/get-started-portal-deploy-app/assign-resource-button.png#lightbox)

1. Seleccione el inquilino, la suscripción y el nombre del recurso. Seleccione **Asignar recurso**.

   ![Asignación de un recurso a la aplicación](./media/get-started-portal-deploy-app/assign-resource.png)

1. Busque la nueva fila en la tabla y copie la dirección URL de punto de conexión. Se construye correctamente para realizar una solicitud `HTTP GET` al punto de conexión de LUIS API para una predicción.

## <a name="train-and-publish-the-app"></a>Entrenamiento y publicación de la aplicación

Entrene la aplicación cuando esté listo para probarla. Publique la aplicación cuando desee que la versión entrenada actual esté disponible para las aplicaciones cliente desde el punto de conexión de predicción de consulta en tiempo de ejecución.

1. Si la aplicación no está entrenada, seleccione **Train** (Entrenar) en el menú de la parte superior derecha.

1. Seleccione **Publicar** (Publicar) en el menú superior. Acepte la configuración de entorno predeterminada y seleccione **Publicar**.

1. Cuando aparezca la barra de notificación verde en la parte superior de la ventana del explorador, seleccione **Refer to the list of endpoints** (Consultar la lista de puntos de conexión).

   ![Barra de notificación de que la aplicación se ha publicado correctamente en el explorador](./media/get-started-portal-deploy-app/successfully-published-notification.png)

1. En la página **Keys and Endpoint settings** (Configuración de claves y puntos de conexión), busque la lista de los recursos asignados y de las direcciones URL de los puntos de conexión correspondientes que se encuentra en la parte inferior.

1. Seleccione la dirección URL del punto de conexión asociada con el nombre del recurso nuevo. Esta acción abre un explorador web con una dirección URL correcta para realizar una solicitud `GET` al punto de conexión de predicción en tiempo de ejecución.

1. `q=`, al final de la dirección URL, es la abreviatura de **query** y es donde la expresión del usuario se anexa a la solicitud GET. Después de `q=`, escriba la misma expresión del usuario que se utilizó al final del inicio rápido anterior:

    ```Is there a form named hrf-234098```

    El explorador muestra la respuesta, que es el mismo JSON que la aplicación cliente recibirá:

    ```JSON
    {
    "query": "Is there a form named hrf-234098",
    "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9768753
    },
    "intents": [
        {
        "intent": "FindForm",
        "score": 0.9768753
        },
        {
        "intent": "None",
        "score": 0.0216071066
        }
    ],
    "entities": [
        {
        "entity": "hrf-234098",
        "type": "Human Resources Form Number",
        "startIndex": 22,
        "endIndex": 31
        }
      ]
    }
    ```

    Esta respuesta le proporciona más información que el panel de prueba predeterminado del tutorial anterior. Para ver el mismo nivel de información en el panel de prueba, debe publicar la aplicación. Una vez publicada la aplicación, seleccione **Comparar con el publicado** en el panel de prueba. Use **Show JSON view** (Mostrar vista de JSON) en el panel de prueba publicado para ver el mismos JSON que en el paso anterior. De esta forma, puede comparar la aplicación en la que trabaja actualmente con una aplicación publicada en el punto de conexión.

    [![Comparar la versión que se está editando con la versión publicada de la aplicación](./media/get-started-portal-deploy-app/compare-test-pane.png)](./media/get-started-portal-deploy-app/compare-test-pane.png#lightbox)

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con este inicio rápido, seleccione **Mis aplicaciones** en el menú de navegación superior. Seleccione la casilla de la aplicación en la lista y seleccione **Delete** (Eliminar) en la barra de herramientas de contexto que está encima de la lista.

[![Eliminar una aplicación de la lista de mis aplicaciones](./media/get-started-portal-build-app/delete-app.png)](./media/get-started-portal-build-app/delete-app.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Identificación de intenciones y entidades habituales](luis-tutorial-prebuilt-intents-entities.md)
