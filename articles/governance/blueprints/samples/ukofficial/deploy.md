---
title: 'Ejemplos: Planos técnicos de UK OFFICIAL y UK NHS (pasos de implementación)'
description: Pasos de implementación de los ejemplos de planos técnicos de UK OFFICIAL y UK NHS
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d720f220b8903d960e7bba2a75aa5a86bd0391ce
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2019
ms.locfileid: "68225993"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Implementación de los ejemplos de planos técnicos de UK OFFICIAL y UK NHS

Para implementar los ejemplos de UK OFFICIAL y UK NHS, debe seguir los siguientes pasos:

> [!div class="checklist"]
> - Crear un plano técnico a partir del ejemplo
> - Marcar la copia del ejemplo como **publicada**
> - Asignar la copia del plano técnico a una suscripción existente

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free) antes de empezar.

## <a name="create-blueprint-from-sample"></a>Creación de un plano técnico a partir del ejemplo

En primer lugar, implemente el ejemplo de plano técnico mediante la creación de un plano técnico en su entorno tomando el ejemplo como punto de partida.

1. Seleccione **Todos los servicios**, busque la opción **Directiva** en el panel izquierdo y selecciónela. En la página **Directiva**, seleccione **Planos técnicos**.

1. En la página **Introducción** de la izquierda, seleccione el botón **Crear** en _Crear un plano técnico_.

1. Busque el ejemplo de plano técnico de **UK OFFICIAL** o **UK NHS** en _Otros ejemplos_ y seleccione **Usar este ejemplo**.

1. Escriba los _Aspectos básicos_ del ejemplo de plano técnico:

   - **Nombre del plano técnico**: proporcione un nombre para su copia del ejemplo de plano técnico.
   - **Ubicación de definición**: use los puntos suspensivos y seleccione el grupo de administración donde guardar la copia del ejemplo.

1. Seleccione la pestaña _Artefactos_ en la parte superior de la página **Siguiente: Artefactos** en la parte inferior de la página.

1. Revise la lista de artefactos que componen el ejemplo de plano técnico. Muchos de los artefactos tienen parámetros que se definirán más tarde. Seleccione **Guardar borrador** cuando haya terminado de revisar el ejemplo de plano técnico.

## <a name="publish-the-sample-copy"></a>Publicación de la copia del ejemplo

La copia del ejemplo de plano técnico ahora se ha creado en el entorno. Se crea en el modo **Borrador** y debe **publicarse** antes de que se pueda asignar e implementar. La copia del ejemplo de plano técnico se puede personalizar para adecuarla a su entorno y necesidades, pero esa modificación puede apartarla del estándar establecido.

1. Seleccione **Todos los servicios**, busque la opción **Directiva** en el panel izquierdo y selecciónela. En la página **Directiva**, seleccione **Planos técnicos**.

1. En la parte izquierda, seleccione la página **Definiciones del plano técnico**. Use los filtros para buscar su copia del ejemplo de plano técnico y, a continuación, selecciónela.

1. Seleccione **Publicar plano técnico** en la parte superior de la página. En la nueva página de la derecha, especifique una **versión** para la copia del ejemplo de plano técnico. Esta propiedad es útil si realiza una modificación posteriormente. Escriba las **notas de cambios** como "Primera versión publicada del ejemplo de plano técnico de UK OFFICIAL o UK NHS". A continuación, seleccione **Publicar** en la parte inferior de la página.

## <a name="assign-the-sample-copy"></a>Asignación de la copia de ejemplo

Una vez que la copia del ejemplo de plano técnico se haya **publicado** correctamente, se podrá asignar a una suscripción dentro del grupo de administración donde se guardó. En este paso se proporcionan los parámetros para hacer que cada implementación de la copia del ejemplo de plano técnico sea única.

1. Seleccione **Todos los servicios**, busque la opción **Directiva** en el panel izquierdo y selecciónela. En la página **Directiva**, seleccione **Planos técnicos**.

1. En la parte izquierda, seleccione la página **Definiciones del plano técnico**. Use los filtros para buscar su copia del ejemplo de plano técnico y, a continuación, selecciónela.

1. Seleccione **Asignar plano técnico** en la parte superior de la página de definición del plano técnico.

1. Proporcione los valores de parámetro para la asignación de plano técnico:

   - Aspectos básicos

     - **Suscripciones**: seleccione una o varias de las suscripciones que están en el grupo de administración donde guardó la copia del ejemplo de plano técnico. Si selecciona más de una suscripción, se creará una asignación para cada una mediante los parámetros especificados.
     - **Nombre de asignación**: el nombre se rellena de antemano de forma automática en función del nombre del plano técnico.
       Cámbielo si fuera necesario o déjelo tal cual.
     - **Ubicación**: seleccione una región para la identidad administrada en la que se va a crear. Azure Blueprint usa esta identidad administrada para implementar todos los artefactos del plano técnico asignado. Para más información, consulte [Identidades administradas para recursos de Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Versión de definición de Blueprint**: Elija una versión **publicada** de la copia del ejemplo de plano técnico.

   - Asignación de bloqueo

     Seleccione el valor de bloqueo de plano técnico para el entorno. Para más información, consulte [Bloqueo de recursos en planos técnicos](../../concepts/resource-locking.md).

   - Identidad administrada

     Deje la opción predeterminada de identidad administrada _asignada por el sistema_.

   - Parámetros de artefacto

     Los parámetros definidos en esta sección se aplican al artefacto en el que se define. Estos parámetros son [parámetros dinámicos](../../concepts/parameters.md#dynamic-parameters), ya que se definen durante la asignación del plano técnico. Para obtener una lista completa de los parámetros de los artefactos y sus descripciones, consulte la [tabla de parámetros de los artefactos](#artifact-parameters-table).

1. Una vez que se hayan especificado todos los parámetros, seleccione **Asignar** en la parte inferior de la página. Se crea la asignación del plano técnico y comienza la implementación del artefacto. La implementación tarda aproximadamente una hora. Para comprobar el estado de implementación, abra la asignación del plano técnico.

> [!WARNING]
> El servicio Azure Blueprints y los ejemplos de plano técnico incorporados son **gratuitos**. El precio de los recursos de Azure se [calcula por producto](https://azure.microsoft.com/pricing/). Use la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) para estimar el costo de la ejecución de los recursos implementados en este ejemplo de plano técnico.

## <a name="artifact-parameters-table"></a>Tabla de parámetros de los artefactos

En la tabla siguiente se proporciona una lista de los parámetros del artefacto de plano técnico:

Nombre del artefacto|Tipo de artefacto|Nombre de parámetro|DESCRIPCIÓN|
|-|-|-|-|
|Iniciativa de plano técnico de UK OFFICIAL o UK NHS|Asignación de directiva |Tipos de recursos para auditar los registros de diagnóstico (Directiva: Iniciativa de plano técnico de UK OFFICIAL o UK NHS) |Lista de tipos de recursos para auditar si la opción de registro de diagnóstico no está habilitada.  Para obtener los valores admitidos, consulte [Servicios, esquemas y categorías admitidos en los registros de diagnóstico de Azure](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|\[Versión preliminar\]: Implementar el agente de Log Analytics en máquinas virtuales Linux |Asignación de directiva |Opcional: Lista de imágenes de VM que han admitido el sistema operativo Linux que se agregarán al ámbito (directiva: \[Versión preliminar\]: Implementar el agente de Log Analytics en VM de Linux) |(Opcional) El valor predeterminado es _none_. Para obtener más información, consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Versión preliminar\]: Implementar el agente de Log Analytics en máquinas virtuales Windows |Asignación de directiva |Opcional: Lista de imágenes de VM que han admitido el sistema operativo Windows que se agregarán al ámbito (directiva: \[Versión preliminar\]: Implementar el agente de Log Analytics en máquinas virtuales Windows) |(Opcional) El valor predeterminado es _none_. Para obtener más información, consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha revisado los pasos para implementar los ejemplos de los planos técnicos UK OFFICIAL y UK NHS, consulte los siguientes artículos para obtener información sobre la asignación de controles:

> [!div class="nextstepaction"]
> [Planos técnicos de UK OFFICIAL y UK NHS: introducción](./index.md)
> [Planos técnicos de UK OFFICIAL y UK NHS: asignación de controles](./control-mapping.md)

Artículos adicionales sobre planos técnicos y cómo utilizarlos:

- Más información sobre el [ciclo de vida del plano técnico](../../concepts/lifecycle.md)
- Descubra cómo utilizar [parámetros estáticos y dinámicos](../../concepts/parameters.md).
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](../../concepts/sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](../../concepts/resource-locking.md).
- Aprenda a [actualizar las asignaciones existentes](../../how-to/update-existing-assignments.md).
