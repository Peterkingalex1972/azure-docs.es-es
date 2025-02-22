---
title: 'Claves de suscripción: LUIS'
titleSuffix: Azure Cognitive Services
description: No es necesario crear claves de suscripción para usar las primeras 1000 consultas de punto de conexión. Si recibe un error de que se ha quedado _sin cuota_ como un error HTTP 403 o 429, debe crear una clave y asignarla a la aplicación.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: 1f8b84722c881cee1fe196e5a614b58cf3c19031
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932856"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Uso de claves de suscripción con la aplicación de LUIS

La primera vez que use Language Understanding (LUIS), no es necesario crear claves de suscripción. Se le proporcionarán 1000 consultas de punto de conexión para empezar. 

Para pruebas y prototipos solo, use el plan gratuito (F0). Para los sistemas de producción, use un plan de [pago](https://aka.ms/luis-price-tier). No use la [clave de creación](luis-concept-keys.md#authoring-key) para las consultas de punto de conexión en producción.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Creación del recurso del entorno de ejecución de punto de conexión de predicción en Azure Portal

Cree el [recurso del punto de conexión de predicción](get-started-portal-deploy-app.md#create-the-endpoint-resource) en Azure Portal. Dicho recurso solo se debe usar para las consultas de predicción de punto de conexión. No se debe usar para la creación de los cambios en la aplicación.

Puede crear un recurso de Language Understanding o de Cognitive Services. Si va a crear un recurso de Language Understanding, se recomienda posponer el tipo de recurso al nombre del recurso. 

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>

### <a name="using-resource-from-luis-portal"></a>Uso de un recurso del portal de LUIS

Si usa el recurso del portal de LUIS, no necesita conocer la clave y la ubicación. En su lugar, debe conocer el inquilino del recurso, la suscripción y el nombre del recurso.

Una vez que [asigne](#assign-resource-key-to-luis-app-in-luis-portal) el recurso a la aplicación de LUIS en el portal de LUIS, la clave y la ubicación se proporcionan como parte de la dirección URL del punto de conexión de predicción de consulta en la página **Configuración de claves y puntos de conexión** de la sección Administrar.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Uso de un recurso de API REST o del SDK

Si usa el recurso de API REST o del SDK, necesita conocer la clave y la ubicación. Esta información se proporciona como parte de la dirección URL del punto de conexión de predicción de consulta en la página **Configuración de claves y puntos de conexión** de la sección Administrar, así como en Azure Portal, en las páginas Información general y Claves del recurso.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Asignación de la clave de recurso a la aplicación de LUIS en el portal de LUIS

Cada vez que crea un recurso para LUIS, será preciso que lo [asigne a la aplicación de LUIS](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Una vez que lo asigne, no tendrá que volver a realizar este paso, a menos que cree otro recurso. Puede crear un recurso para expandir las regiones de la aplicación o para admitir un mayor número de consultas de predicción.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Anulación de la asignación de un recurso
Al anular la asignación de la clave de punto de conexión, no se elimina de Azure. Solo se desvincula de LUIS. 

Cuando una clave de punto de conexión está sin asignar o no está asignada a la aplicación, cualquier solicitud a la dirección URL de punto de conexión devuelve un error: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Incluir todas las puntuaciones de intención pronosticada
La casilla de verificación **Include all predicted intent scores** (Incluir todas las puntuaciones de intención pronosticada) permite que la respuesta a la consulta del punto de conexión incluya la puntuación de predicción para cada intención. 

Esta opción permite que el bot de chat o la aplicación que llama a LUIS tome decisiones mediante programación basadas en las puntuaciones de las intenciones devueltas. Por lo general, las dos primeras intenciones son las más interesantes. Si la mejor puntuación es la intención None, el bot de chat puede hacer una pregunta de seguimiento que elegirá definitivamente entre la intención None y la otra intención con una puntuación alta. 

Las intenciones y sus puntuaciones también se incluyen en los registros de punto de conexión. También puede [exportar](luis-how-to-start-new-app.md#export-app) esos registros y analizar los resultados. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>Habilitar el corrector ortográfico de Bing 
En **Endpoint url settings** (Configuración de dirección URL de punto de conexión), el conmutador **Bing spell checker** (Corrector ortográfico de Bing) permite a LUIS corregir las palabras mal escritas antes de realizar la predicción. Cree una **[clave de Bing Spell Check](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Agregue el parámetro de cadena de consulta **spellCheck=true** y **bing-spell-check-subscription-key={SU_CLAVE_DE_BING}** . Reemplace `{YOUR_BING_KEY_HERE}` con su clave del corrector ortográfico de Bing.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Regiones de publicación

Obtenga más información sobre las [regiones](luis-reference-regions.md) de publicación, incluida la publicación en [Europa](luis-reference-regions.md#publishing-to-europe) y [Australia](luis-reference-regions.md#publishing-to-australia). Las regiones de publicación son diferentes de las regiones de creación. Cree una aplicación en la región de creación correspondiente a la región de publicación que quiera para el punto de conexión de consulta.

## <a name="assign-resource-without-luis-portal"></a>Asignación del recurso sin el portal de LUIS

Para fines de automatización, como una canalización de CI/CD, puede automatizar la asignación de un recurso de LUIS para una aplicación de LUIS. Para ello, tiene que realizar los siguientes pasos:

1. Obtenga un token de Azure Resource Manager en este [sitio web](https://resources.azure.com/api/token?plaintext=true). Este token expira, por lo que debe usarlo de inmediato. La solicitud devuelve un token de Azure Resource Manager.

    ![Solicitud de un token de Azure Resource Manager y recepción del token de Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Use el token para solicitar los recursos de LUIS en distintas suscripciones desde la [API Get LUIS azure accounts](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), su cuenta de usuario tiene acceso. 

    Esta API POST requiere la siguiente configuración:

    |Encabezado|Valor|
    |--|--|
    |`Authorization`|El valor de `Authorization` es `Bearer {token}`. Tenga en cuenta que el valor del token debe ir precedido de la palabra `Bearer` y un espacio.| 
    |`Ocp-Apim-Subscription-Key`|Su [clave de creación](luis-how-to-account-settings.md).|

    Esta API devuelve una matriz de objetos JSON con las suscripciones de LUIS, incluidos el identificador de suscripción, el grupo de recursos y el nombre del recurso, que se devuelve como nombre de cuenta. Busque el elemento de la matriz que es el recurso de LUIS que se va a asignar a la aplicación de LUIS. 

1. Asigne el token al recurso de LUIS con la API [Assign a LUIS azure accounts to an application](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515). 

    Esta API POST requiere la siguiente configuración:

    |type|Configuración|Valor|
    |--|--|--|
    |Encabezado|`Authorization`|El valor de `Authorization` es `Bearer {token}`. Tenga en cuenta que el valor del token debe ir precedido de la palabra `Bearer` y un espacio.|
    |Encabezado|`Ocp-Apim-Subscription-Key`|Su [clave de creación](luis-how-to-account-settings.md).|
    |Encabezado|`Content-type`|`application/json`|
    |QueryString|`appid`|El id. de la aplicación LUIS. 
    |Body||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "resourcegroup-2",<br>"AccountName": "luis-uswest-S0-2"}|

    Cuando esta API finaliza correctamente, devuelve un estado 201: creado. 

## <a name="change-pricing-tier"></a>Cambiar el plan de tarifa

1.  En [Azure](https://portal.azure.com), busque la suscripción de LUIS. Seleccione la suscripción de LUIS.
    ![Buscar la suscripción de LUIS](./media/luis-usage-tiers/find.png)
1.  Seleccione **Plan de tarifa** para ver los planes de tarifa disponibles. 
    ![Ver los planes de tarifa](./media/luis-usage-tiers/subscription.png)
1.  Seleccione el plan de tarifa y después **Seleccionar** para guardar el cambio. 
    ![Cambiar el plan de pago de LUIS](./media/luis-usage-tiers/plans.png)
1.  Una vez completado el cambio de tarifa, el plan de tarifa nuevo se comprueba en una ventana emergente. 
    ![Comprobar el plan de pago de LUIS](./media/luis-usage-tiers/updated.png)
1. No olvide [asignar esta clave de punto de conexión](#assign-endpoint-key) en la página **Publicar** y usarla en todas las consultas de punto de conexión. 

## <a name="fix-http-status-code-403-and-429"></a>Corrección de los códigos de estado HTTP 403 y 429

Recibirá los códigos de estado HTTP 403 y 429 cuando supere las transacciones por segundo o las transacciones al mes del plan de tarifa.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Cuando reciba un código de estado de error HTTP 403

Al usar las 1000 consultas de punto de conexión gratuitas o al superar la cuota mensual de transacciones de su plan de tarifa, recibirá un código de estado de error HTTP 403. 

Para corregir este error, tendrá que [cambiar su plan de tarifa](luis-how-to-azure-subscription.md#change-pricing-tier) a un nivel superior o [crear un nuevo recurso](get-started-portal-deploy-app.md#create-the-endpoint-resource) y [asignarlo a la aplicación](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Las soluciones para este error incluyen:

* En [Azure Portal](https://portal.azure.com), en su recurso Language Understanding, en **Administración de recursos -> Plan de tarifa**, cambie su plan de tarifa a un nivel superior de TPS. No es necesario hacer nada en el portal de Language Understanding si el recurso ya está asignado a la aplicación Language Understanding.
*  Si el uso supera el plan de tarifa más alto, agregue más recursos de Language Understanding con un equilibrador de carga delante. El [contenedor de Language Understanding](luis-container-howto.md) con Kubernetes o Docker Compose puede ayudarle en esto.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Cuando reciba un código de estado de error HTTP 429

Este código de estado se devuelve cuando las transacciones por segundo superan su plan de tarifa.  

Entre las soluciones, se incluyen las siguientes:

* También puede [aumentar el plan de tarifa](#change-pricing-tier), si no está en el nivel más alto.
* Si el uso supera el plan de tarifa más alto, agregue más recursos de Language Understanding con un equilibrador de carga delante. El [contenedor de Language Understanding](luis-container-howto.md) con Kubernetes o Docker Compose puede ayudarle en esto.
* También puede programar las solicitudes de la aplicación cliente con una [directiva de reintentos](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) que implemente al recibir este código de estado. 

## <a name="viewing-summary-usage"></a>Ver uso de resumen
En Azure puede ver la información de uso de LUIS. En la página **Información general** se muestra información de resumen reciente incluidos los errores y las llamadas. Si realiza una solicitud de punto de conexión de LUIS, consulte inmediatamente la **página Información general**, y deje que pasen hasta cinco minutos hasta que se muestre el uso.

![Ver uso de resumen](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Personalización de los gráficos de uso
Las métricas proporcionan una vista más detallada de los datos.

![Métricas predeterminadas](./media/luis-usage-tiers/metrics-default.png)

Puede configurar los gráficos de métricas para el período de tiempo y el tipo de métrica. 

![Métricas personalizadas](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Alerta de umbral de transacciones totales
Si le gustaría saber cuándo se alcanza un umbral de transacciones determinado (por ejemplo 10.000) puede crear una alerta. 

![Alertas predeterminadas](./media/luis-usage-tiers/alert-default.png)

Agregue una alerta de métrica para la métrica **Llamadas totales** para un período de tiempo concreto. Agregue las direcciones de correo electrónico de todos los usuarios que deben recibir la alerta. Agregue webhooks para todos los sistemas que deben recibir la alerta. También puede ejecutar una aplicación lógica cuando se desencadene la alerta. 

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo usar las [versiones](luis-how-to-manage-versions.md) para administrar los cambios en la aplicación de LUIS.
