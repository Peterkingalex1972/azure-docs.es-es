---
title: 'Revisión de expresiones de usuario: LUIS'
titleSuffix: Azure Cognitive Services
description: Con el aprendizaje activo, puede revisar las expresiones de punto de conexión para intenciones y entidades correctas. LUIS elige las expresiones de punto de conexión de las cuales no está seguro.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: a6b89b315c4cdb1438fc8256cfc01793b3c0f920
ms.sourcegitcommit: 08d3a5827065d04a2dc62371e605d4d89cf6564f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68619750"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Conceptos para habilitar el aprendizaje activo mediante la revisión de expresiones de punto de conexión
El aprendizaje activo es una de las tres estrategias para mejorar la precisión de la predicción y la más fácil de implementar. Con el aprendizaje activo, puede revisar las expresiones de punto de conexión para intenciones y entidades correctas. LUIS elige las expresiones de punto de conexión de las cuales no está seguro.

## <a name="what-is-active-learning"></a>¿Qué es el aprendizaje activo?
El aprendizaje activo es un proceso de dos pasos. En primer lugar, LUIS selecciona las expresiones que recibe en el punto de conexión de la aplicación que necesitan validación. El segundo paso lo realiza el propietario de la aplicación o el colaborador para validar las expresiones seleccionadas para [revisión](luis-how-to-review-endpoint-utterances.md), incluida la intención correcta y todas las entidades dentro de la intención. Después de revisar las expresiones, vuelva a entrenar y publicar la aplicación. 

## <a name="which-utterances-are-on-the-review-list"></a>Expresiones incluidas en la lista de revisión
En LUIS se agregan expresiones a la lista de revisión cuando la primera intención que se desencadena tiene una puntuación baja, o bien cuando las puntuaciones de las dos primeras intenciones son demasiado próximas. 

## <a name="single-pool-for-utterances-per-app"></a>Grupo único de expresiones por aplicación
La lista **Review endpoint utterances** (Revisión de las expresiones de punto de conexión) no cambia según la versión. Existe un único grupo de expresiones para revisar, independientemente de la versión que esté editando activamente o de la versión de la aplicación que se haya publicado en el punto de conexión. 

## <a name="where-are-the-utterances-from"></a>De dónde provienen las expresiones
Las expresiones de punto de conexión se obtienen de las consultas del usuario final en el punto de conexión HTTP de la aplicación. Si la aplicación no se ha publicado o todavía no ha recibido visitas, no hay ninguna expresión para revisar. Si las visitas del punto de conexión se reciben para una intención o entidad específica, no habrá expresiones para revisar que las contengan. 

## <a name="schedule-review-periodically"></a>Programar la revisión periódicamente
No es necesario revisar las expresiones sugeridas todos los días, pero la revisión debe formar parte del mantenimiento periódico de LUIS. 

## <a name="delete-review-items-programmatically"></a>Eliminar los elementos de revisión mediante programación
Use la API para **[eliminar expresiones sin etiquetar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Realice una copia de seguridad de estas expresiones antes de eliminarlas mediante la **[exportación de los archivos de registro](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [revisar](luis-how-to-review-endpoint-utterances.md) las expresiones de punto de conexión
