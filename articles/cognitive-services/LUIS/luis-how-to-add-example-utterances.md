---
title: 'Adición de expresiones de ejemplo: LUIS'
titleSuffix: Azure Cognitive Services
description: Las expresiones de ejemplo son ejemplos de texto de preguntas de los usuarios o de comandos. Para entrenar el servicio Language Understanding (LUIS), debe agregar expresiones de ejemplo a una intención.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 1e170b86f573112cc5bc8dddd6f080921ef29d2d
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932950"
---
# <a name="add-an-entity-to-example-utterances"></a>Incorporación de una entidad a expresiones de ejemplo 

Las expresiones de ejemplo son ejemplos de texto de preguntas de los usuarios o de comandos. Para entrenar el servicio Language Understanding (LUIS), debe agregar [expresiones de ejemplo](luis-concept-utterance.md) a una [intención](luis-concept-intent.md).

En general, primero se agrega una expresión de ejemplo a una intención y luego se crean expresiones de etiquetas y entidades en la página **Intenciones**. Si prefiere crear primero las entidades, consulte [Agregar entidades](luis-how-to-add-entities.md).

## <a name="marking-entities-in-example-utterances"></a>Marcado de entidades en expresiones de ejemplo

Cuando se selecciona texto en expresión de ejemplo para marcarlo para una entidad, aparece un menú emergente en contexto. Use este menú para crear o seleccionar una entidad. 

En la expresión de ejemplo no se pueden etiquetar determinados tipos de entidad, como las precompiladas y las entidades de expresión regular, ya que se etiquetan automáticamente. 

## <a name="add-a-simple-entity"></a>Incorporación de una entidad sencilla

En el siguiente procedimiento se crea y se etiqueta una entidad personalizada dentro de la siguiente expresión de la página **Intenciones**:

```text
Are there any SQL server jobs?
```

1. Seleccione a `SQL server` en la expresión para etiquetarlo como entidad sencilla. En el cuadro desplegable de la entidad que aparece, puede seleccionar una entidad existente o agregar una nueva entidad. Para agregar una nueva entidad, escriba el nombre en el cuadro de texto `Job` y, luego, seleccione **Crear nueva entidad**.

    ![Captura de pantalla de la especificación del nombre de la entidad](./media/luis-how-to-add-example-utterances/create-simple-entity.png)

    > [!NOTE]
    > Al seleccionar palabras para etiquetarlas como entidades:
    > * Para una sola palabra, simplemente selecciónela. 
    > * Para un conjunto de dos o más palabras, seleccione la primera palabra y luego la palabra final.

1. En el cuadro de diálogo emergente **What type of entity do you want to create?** (¿Qué tipo de entidad desea crear?), verifique el nombre de entidad, y seleccione el tipo de entidad **Simple** (Sencilla) y **Done** (Listo).

    Habitualmente se usa una [lista de frases](luis-concept-feature.md) para mejorar la señal de las entidades sencillas.

## <a name="add-a-list-entity"></a>Incorporación de una entidad de lista

Las entidades de la lista representan un conjunto de coincidencias de texto exacto de palabras relacionadas en el sistema. 

Para la lista de departamento de una empresa, puede tener valores normalizados: `Accounting` y `Human Resources`. Cada nombre normalizado tiene sinónimos. Para un departamento, estos sinónimos pueden incluir cualquier acrónimo, número o jerga de departamento. No es necesario conocer todos los valores cuando se crea la entidad. Puede agregar más después de revisar las expresiones reales del usuario con los sinónimos.

1. En una declaración de ejemplo de la página **Intenciones**, seleccione la palabra o frase que desea de la nueva lista. Cuando aparezca la lista desplegable de entidades, escriba el nombre de la nueva entidad de la lista en el cuadro de texto superior y, luego, seleccione **Crear nueva entidad**.   

1. En el cuadro de diálogo emergente **What type of entity do you want to create?** (¿Qué tipo de entidad desea crear?), asígnele un nombre a la entidad y seleccione **List** (Lista) como tipo. Agregue sinónimos de este elemento de lista y seleccione **Done** (Listo). 

    ![Captura de pantalla de la especificación de sinónimos de la entidad de lista](./media/luis-how-to-add-example-utterances/hr-create-list-2.png)

    Puede agregar más elementos de lista o más sinónimos de los elementos al etiquetar otras expresiones o al editar la entidad en **Entities** (Entidades) en el panel de navegación izquierdo. La [edición](luis-how-to-add-entities.md#add-list-entities) de las entidades proporciona la opción de escribir elementos adicionales con los sinónimos correspondientes o de importar una lista. 

## <a name="add-a-composite-entity"></a>Incorporación de una entidad compuesta

Las entidades compuestas se crean a partir de **entidades** existentes para formar una entidad primaria. 

Con la expresión `Does John Smith work in Seattle?`, una expresión compuesta puede devolver información de la entidad del nombre del empleado `John Smith` y la ubicación `Seattle` en una entidad compuesta. Las entidades secundarias ya deben existir en la aplicación y se marcan en la expresión de ejemplo antes de crear la entidad compuesta.

1. Para encapsular las entidades secundarias en una entidad compuesta, seleccione la **primera** entidad etiquetada (más a la izquierda) en la expresión de la entidad compuesta. Aparece una lista de desplegable que muestra las opciones de esta selección.

1. Seleccione **Wrap in composite entity** (Encapsular la entidad compuesta) en la lista desplegable. 

1. Seleccione la última palabra de la entidad compuesta (en la derecha). Observe que una línea verde sigue a la entidad compuesta. Este es el indicador visual de una entidad compuesta y debe estar en todas las palabras de esta desde la entidad secundaria del extremo izquierdo hasta la entidad secundaria del extremo derecho.

1. Escriba el nombre de la entidad compuesta en la lista desplegable.

    Cuando se encapsulan las entidades correctamente, aparece una línea verde debajo de toda la frase.

1. Valide los detalles de la entidad compuesta en el cuadro de diálogo emergente **What type of entity do you want to create?** (¿Qué tipo de entidad desea crear?) y seleccione **Done** (Listo).

    ![Captura de pantalla del elemento emergente de los detalles de la entidad](./media/luis-how-to-add-example-utterances/hr-create-composite-3.png)

1. La entidad compuesta aparece con las entidades individuales resaltadas en azul y la entidad compuesta subrayada en verde. 

    ![Captura de pantalla de la página de detalles de Intents (Intenciones) con la entidad compuesta](./media/luis-how-to-add-example-utterances/hr-create-composite-4.png)

## <a name="add-entitys-role-to-utterance"></a>Adición del rol de la entidad a la expresión

Un rol es un subtipo con nombre de una entidad, determinado por el contexto de la expresión. Puede marcar una entidad dentro de una expresión como entidad o seleccionar un rol dentro de esa entidad. Cualquier entidad puede tener roles, incluidas las entidades personalizadas con aprendizaje automático (entidades simples y compuestas) o sin aprendizaje automático (entidades pecompiladas, entidades de expresión regular o entidades de lista). 

Aprenda [cómo marcar una expresión con roles de entidad](tutorial-entity-roles.md) en un práctico tutorial. 

## <a name="entity-status-predictions"></a>Predicciones de estado de entidad

Cuando indica una nueva expresión en el portal de LUIS, la expresión puede tener errores de predicción de entidad. El error de predicción es una diferencia entre cómo una entidad se etiqueta en comparación con cómo LUIS ha predicho la entidad. 

Esta diferencia se representa visualmente en el portal de LUIS con un subrayado rojo en la expresión. El subrayado rojo puede aparecer entre corchetes de la entidad o fuera de los corchetes. 

![Captura de pantalla de la discrepancia de predicción del estado de entidad](./media/luis-how-to-add-example-utterances/entity-prediction-error.png)

Seleccione las palabras que se hayan subrayado en rojo en la expresión. 

El cuadro de la entidad muestra el **Estado de entidad** con un signo de exclamación rojo si hay una discrepancia de predicción. Para ver el Estado de entidad con información sobre la diferencia entre las entidades etiquetadas y previstas, seleccione **Estado de entidad** y seleccione el elemento a la derecha.

![Captura de pantalla de selección de estado de la entidad](./media/luis-how-to-add-example-utterances/entity-prediction-error-correction.png)

La línea roja puede aparecer en cualquiera de los momentos siguientes:

   * Cuando se declara una expresión, pero antes de que se etiquete la entidad
   * Cuando se aplica la etiqueta de la entidad
   * Cuando se quita la etiqueta de la entidad
   * Cuando se predice más de una etiqueta de entidad para ese texto 

Las siguientes soluciones ayudan a resolver la discrepancia de predicción de la entidad:

|Entidad|Indicador visual|Predicción|Solución|
|--|--|--|--|
|Expresión declarada, la entidad no está etiquetada aún.|subrayado rojo|La predicción es correcta.|Etiquete la entidad con el valor predicho.|
|Texto sin etiquetar|subrayado rojo|Predicción incorrecta|Las expresiones actuales con esta entidad incorrecta tienen que revisarse en todas las intenciones. Las expresiones actuales han enseñado mal a LUIS que este texto es la entidad predicha.
|Texto etiquetado correctamente|resaltado azul de la entidad, subrayado rojo|Predicción incorrecta|Proporcione más expresiones con la entidad etiquetada correctamente en una variedad de lugares y usos. Las expresiones actuales no son suficientes para enseñar a LUIS que se trata de la entidad o aparecen entidades similares en el mismo contexto. La entidad similar debe combinarse en una única entidad de modo que LUIS no se confunda. Otra solución es agregar una lista de frases para aumentar la significación de las palabras. |
|Texto etiquetado incorrectamente|resaltado azul de la entidad, subrayado rojo|Predicción correcta| Proporcione más expresiones con la entidad etiquetada correctamente en una variedad de lugares y usos. 

## <a name="other-actions"></a>Otras acciones

Puede realizar acciones en las expresiones de ejemplo como grupo selecto o como elemento individual. Los grupos de expresiones de ejemplo selectos cambian el menú contextual sobre la lista. Los elementos individuales pueden usar tanto el menú contextual sobre la lista como los puntos suspensivos contextuales al final de cada fila de expresión. 

### <a name="remove-entity-labels-from-utterances"></a>Eliminación de etiquetas de entidad de las expresiones

Puede quitar las etiquetas de entidad de aprendizaje automático de una expresión en la página Intenciones. Si la entidad no procede del aprendizaje automático, no se puede quitar de una expresión. Si tiene que quitar una entidad que no es de aprendizaje automático de la expresión, debe eliminar la entidad de toda la aplicación. 

Para quitar una etiqueta de entidad de una expresión de aprendizaje automático, seleccione la entidad en la expresión. A continuación, seleccione **Quitar etiqueta** en el cuadro desplegable de la entidad que aparece.

### <a name="add-a-prebuilt-entity-label"></a>Incorporación de una etiqueta de entidad precompilada

Al agregar las entidades precompiladas a la aplicación de LUIS, no necesitará etiquetar expresiones con estas entidades. Para obtener más información sobre entidades precompiladas y cómo agregarlas, consulte la sección [Add entities](luis-how-to-add-entities.md#add-a-prebuilt-entity-to-your-app) (Agregar entidades).

### <a name="add-a-regular-expression-entity-label"></a>Incorporación de una etiqueta de la entidad de expresiones regulares

Al agregar las entidades de expresiones regulares a la aplicación de LUIS, no necesitará etiquetar expresiones con estas entidades. Para obtener más información sobre entidades de expresiones regulares y cómo agregarlas, consulte la sección [Add entities](luis-how-to-add-entities.md#add-regular-expression-entities-for-highly-structured-concepts) (Agregar entidades).


### <a name="create-a-pattern-from-an-utterance"></a>Creación de un patrón a partir de una expresión

Consulte [Add pattern from existing utterance on intent or entity page](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page) (Adición de un patrón a partir de una de expresión existente en la página de intención o entidad).


### <a name="add-a-patternany-entity"></a>Incorporación de una entidad pattern.any

Si agrega las entidades pattern.any a su aplicación de LUIS, no puede etiquetar expresiones con estas entidades. Solo son válidas en los patrones. Para obtener más información sobre las entidades pattern-any y cómo agregarlas, consulte la sección [Agregar entidades](luis-how-to-add-entities.md#add-patternany-entities-to-capture-free-form-entities).

## <a name="train-your-app-after-changing-model-with-utterances"></a>Entrenamiento de la aplicación después de cambiar el modelo con expresiones

Después de agregar, editar o quitar expresiones, [entrene](luis-how-to-train.md) y [publique](luis-how-to-publish-app.md) la aplicación para que los cambios se apliquen a las consultas de punto de conexión. 

## <a name="next-steps"></a>Pasos siguientes

Después de etiquetar expresiones en sus **intenciones**, puede crear una [entidad compuesta](luis-how-to-add-entities.md).
