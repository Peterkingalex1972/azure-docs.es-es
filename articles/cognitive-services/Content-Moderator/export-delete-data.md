---
title: 'Exportación o eliminación de datos de usuario: Content Moderator'
titleSuffix: Azure Cognitive Services
description: Obtenga información acerca de cómo exportar o eliminar datos en Content Moderator.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: pafarley
ms.openlocfilehash: b18bc2907c00ed1424db440f09c914faff6a9863
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68565568"
---
# <a name="export-or-delete-user-data-in-content-moderator"></a>Exportación o eliminación de datos de usuario en Content Moderator

Content Moderator recopila los datos del usuario para prestar el servicio, pero los clientes tienen control total sobre la visualización, la exportación y la eliminación de sus datos mediante la [herramienta de revisión](https://contentmoderator.cognitive.microsoft.com/) y las [API de moderación y revisión](https://docs.microsoft.com/azure/cognitive-services/content-moderator/api-reference).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Para más información acerca de cómo exportar y eliminar datos del usuario en Content Moderator, consulte la siguiente tabla.

| Datos | Operación de exportación | Elimina una operación |
| ---- | ---------------- | ---------------- |
| Información de cuenta (claves de suscripción) | N/D | Eliminación mediante Azure Portal (suscripciones de Azure). O utilice el botón **Eliminar equipo** en la página de configuración de equipos de la [interfaz de usuario de revisión](https://contentmoderator.cognitive.microsoft.com/). |
| Imágenes para coincidencia personalizada | Llame a las [API de obtención de identificadores de imagen](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f676). Las imágenes se almacenan en un formato de algoritmo hash propietario unidireccional, y no hay manera de extraer las imágenes reales. | Llame a las [API de eliminación de todas las imágenes](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f686). O bien, elimine el recurso de Content Moderator con Azure Portal. |
| Condiciones para la coincidencia personalizada | Llame a las [API para obtener todos los términos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67e). | Llame a las [API de eliminación de todas los términos](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67d). O bien, elimine el recurso de Content Moderator con Azure Portal. |
| Etiquetas | N/D | Utilice el icono **Eliminar** disponible para cada etiqueta en la página de configuración de etiquetas de la interfaz de usuario de revisión. O utilice el botón **Eliminar equipo** en la página de configuración de equipos de la [interfaz de usuario de revisión](https://contentmoderator.cognitive.microsoft.com/). |
| Revisiones | Llame a la [API para obtener revisiones](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) | Use el botón **Eliminar equipo** en la página de configuración de equipos de la [interfaz de usuario de revisión](https://contentmoderator.cognitive.microsoft.com/).
| Usuarios | N/D | Utilice el icono **Eliminar** disponible para cada usuario en la página de configuración de equipos de la [interfaz de usuario de revisión](https://contentmoderator.cognitive.microsoft.com/). O utilice el botón **Eliminar equipo** en la página de configuración de equipos de la [interfaz de usuario de revisión](https://contentmoderator.cognitive.microsoft.com/). |

