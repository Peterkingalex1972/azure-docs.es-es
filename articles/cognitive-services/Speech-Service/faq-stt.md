---
title: Preguntas más frecuentes sobre el servicio Speech to Text en Azure
titleSuffix: Azure Cognitive Services
description: Obtenga respuestas a las preguntas más habituales sobre el servicio Speech to Text.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: panosper
ms.openlocfilehash: a437927f6ad43f4c3a90433faa5b9fa6f964aa5c
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839232"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Preguntas más frecuentes sobre el servicio Speech to Text

Si no encuentra respuestas a sus preguntas en estas P+F, [consulte otras opciones de soporte técnico](support.md).

## <a name="general"></a>General

**P: ¿Qué diferencia hay entre un modelo de línea base y un modelo personalizado de conversión de voz en texto?**

**R.** : Un modelo de línea base se ha entrenado con datos propiedad de Microsoft y ya está implementado en la nube.  Puede usar un modelo personalizado con el fin de adaptar un modelo para que se ajuste mejor a un entorno concreto que tenga ruido ambiental o lenguaje específicos. Fábricas, coches o calles ruidosas requerirán un modelo acústico adaptado. Temas como la biología, la física, la radiología, los nombres de productos y los acrónimos personalizados requerirán un modelo de lenguaje adaptado.

**P: ¿Por dónde empiezo si quiero usar un modelo de línea base?**

**R.** : Primero, obtenga una [clave suscripción](get-started.md). Si quiere realizar llamadas REST a los modelos de línea de base implementados previamente, vea las [API REST](rest-apis.md). Si quiere usar WebSockets, [descargue el SDK](speech-sdk.md).

**P: ¿Siempre es necesario crear un modelo de voz personalizado?**

**R.** :  No. Si en la aplicación se usa un lenguaje cotidiano genérico, no es necesario personalizar un modelo. Si la aplicación se usa en un entorno con poco o ningún ruido de fondo, tampoco es necesario personalizar un modelo.

Puede implementar modelos de línea de base y personalizados en el portal, y después ejecutar pruebas de precisión en ellos. Puede usar esta característica para medir la precisión de un modelo de línea de base con respecto a uno personalizado.

**P: ¿Cómo sabré que el procesamiento del conjunto de datos o modelo se ha completado?**

**R.** : Actualmente, el estado del modelo o del conjunto de datos en la tabla es la única manera de saberlo. Cuando se complete el procesamiento, el estado será **Succeeded** (Correcto).

**P: ¿Puedo crear más de un modelo?**

**R.** : No hay límite en cuanto al número de modelos que puede tener en la colección.

**P: Me he dado cuenta de que me he equivocado. ¿Cómo cancelo la importación de datos o la creación del modelo que está en curso?**

**R.** : Actualmente no se puede revertir un proceso de adaptación acústica o de lenguaje. Puede eliminar los modelos y los datos importados cuando estén en un estado terminal.

**P: ¿Qué diferencia existe entre el modelo de búsqueda y dictado y el modelo de conversación?**

**R.** : Puede elegir entre varios modelos de línea base en el servicio Voz. El modelo Conversational es adecuado para el reconocimiento de la voz hablada en un estilo conversacional. Este modelo es ideal para la transcripción de llamadas de teléfono. El modelo Search and Dictation es ideal para aplicaciones desencadenada por la voz. El modelo Universal es un modelo nuevo con el objetivo de abordar ambos escenarios. El modelo Universal tiene actualmente un nivel de calidad igual o superior al del modelo de conversación en la mayoría de las configuraciones regionales.

**P: ¿Puedo actualizar el modelo existente (apilamiento del modelo)?**

**R.** : Un modelo existente no se puede actualizar. Como solución alternativa, puede combinar el conjunto de datos anterior con el nuevo y readaptarlo.

El conjunto de datos antiguo y el nuevo se deben combinar en un único archivo ZIP (para datos acústicos) o en un archivo .txt (para datos de lenguaje). Cuando termine la adaptación, se debe volver a implementar el nuevo modelo actualizado para obtener un punto de conexión nuevo.

**P: Cuando hay disponible una versión nueva de una línea base, ¿la implementación se actualiza de forma automática?**

**R.** : Las implementaciones NO se actualizan de forma automática.

Si ha adaptado e implementado un modelo con la línea de base V1.0, esa implementación permanecerá tal cual. Los clientes pueden retirar el modelo implementado, volver a adaptar con la versión más reciente de la línea de base y volver a implementar.

**P: ¿Qué sucede si necesito una mayor simultaneidad para el modelo implementado de la que se ofrece en el portal?**

**R.** : Se puede escalar verticalmente el modelo en incrementos de 20 solicitudes simultáneas.

Póngase en contacto con el [servicio de soporte técnico de Speech](mailto:speechsupport@microsoft.com?subject=Request%20for%20higher%20concurrency%20for%20Speech-to-text) si requiere una escala mayor.

**P: ¿Puedo descargar mi modelo y ejecutarlo localmente?**

**R.** : Los modelos no se pueden descargar ni ejecutar localmente.

**P: ¿Se registran mis solicitudes?**

**R.** : Al crear una implementación, tiene una opción para desactivar el seguimiento. Después, no se registrará ningún sonido ni ninguna transcripción. De lo contrario, las solicitudes se registran normalmente en Azure, en almacenamiento seguro.

**P: ¿Están limitadas mis solicitudes?**

**R.** : La API REST limita las solicitudes a 25 cada 5 segundos. Encontrará los detalles en nuestras páginas de [Speech to Text](speech-to-text.md).

**P: ¿Cómo se cobra el audio de canal doble?**

**R.** : Si envía cada canal por separado (cada canal en su propio archivo), se le cobrará según la duración de cada archivo. Si envía un solo archivo con cada canal multiplexado juntos, se le cobrará por la duración del archivo individual.

> [!IMPORTANT]
> Si tiene más dudas sobre la privacidad que le impidan usar el servicio Voz personalizado, póngase en contacto con uno de los canales de soporte técnico.

## <a name="importing-data"></a>Importación de datos

**P: ¿Cuál es el límite de tamaño de un conjunto de datos, y por qué existe?**

**R.** : El límite actual de un conjunto de datos es de 2 GB. El límite se debe a la restricción del tamaño de un archivo para la carga HTTP. 

**P: ¿Puedo comprimir mis archivos de texto para cargar un archivo de texto mayor?** 

**R.** :  No. Actualmente solo se permiten los archivos de texto no comprimidos.

**P: El informe de datos indica que ha habido expresiones erróneas. ¿Cuál es el problema?**

**R.** : No es un problema que no se pueda cargar el 100 % de las expresiones de un archivo. Si la gran mayoría de las expresiones de un conjunto de datos acústicos o de lenguaje (por ejemplo, más del 95 %) se importan correctamente, el conjunto de datos se podrá usar. Pero se recomienda comprender la causa del error de las expresiones y corregir los problemas. Los problemas más comunes, como los errores de formato, son fáciles de corregir. 

## <a name="creating-an-acoustic-model"></a>Creación de un modelo acústico

**P: ¿Cuántos datos acústicos necesito?**

**R.** : Se recomienda empezar con 30 minutos a una hora de datos acústicos.

**P: ¿Qué datos debo recopilar?**

**R.** : Recopile datos lo más cercanos posibles al escenario de aplicación y caso de uso. La colección de datos debe coincidir con la aplicación y los usuarios de destino en términos de dispositivo o dispositivos, entornos y tipos de hablante. En general, debe recopilar datos de un intervalo lo más amplio posible de hablantes. 

**P: ¿Cómo debo recopilar los datos acústicos?**

**R.** : Puede crear una aplicación de recopilación de datos autónoma o usar software de grabación de audio comercial. También puede crear una versión de la aplicación que registre los datos de audio y después los use. 

**P: ¿Debo transcribir los datos de adaptación yo mismo?**

**R.** : Sí. Puede transcribirlos usted mismo o utilizar un servicio de transcripción profesional. Algunos usuarios prefieren usar transcriptores profesionales, mientras que otros usan la colaboración abierta distribuida o realizan las transcripciones ellos mismos.

## <a name="accuracy-testing"></a>Pruebas de precisión

**P: ¿Puedo realizar una prueba sin conexión de mi modelo acústico personalizado con un modelo de lenguaje personalizado?**

**R.** : Sí, basta con seleccionar el modelo de lenguaje personalizado en el menú desplegable al configurar la prueba sin conexión.

**P: ¿Puedo realizar una prueba sin conexión de mi modelo de lenguaje personalizado con un modelo acústico personalizado?**

**R.** : Sí, basta con seleccionar el modelo acústico personalizado en el menú desplegable al configurar la prueba sin conexión.

**P: ¿Qué es Word Error Rate (WER) y cómo se calcula?**

**R.** : WER es la métrica de evaluación para el reconocimiento de voz. WER se cuenta como el número total de errores, lo que incluye las inserciones, eliminaciones y sustituciones, dividido por el número total de palabras en la transcripción de referencia. Para obtener más información, vea [word error rate](https://en.wikipedia.org/wiki/Word_error_rate).

**P: ¿Cómo determino si los resultados de una prueba de precisión son correctos?**

**R.** : Los resultados muestran una comparación entre el modelo de línea base y el personalizado. Debe aspirar a superar el modelo de línea de base para que la personalización sea útil.

**P: ¿Cómo puedo determinar el valor WER de un modelo base para ver si se produjo una mejora?** 

**R.** : Los resultados de la prueba sin conexión muestran la precisión de línea base del modelo personalizado y la mejora sobre la línea base.

## <a name="creating-a-language-model"></a>Creación de un modelo de lenguaje

**P: ¿Cuántos datos de texto tengo que cargar?**

**R.** : Depende de la diferencia que exista entre el vocabulario y las frases que se usan en la aplicación y los modelos de lenguaje iniciales. Para todas las palabras nuevas, es útil proporcionar el máximo de ejemplos posible de utilización de estas palabras. Para las frases comunes que se usan en la aplicación, también es útil incluir frases en los datos de lenguaje, ya que indican al sistema que escuche también estos términos. Es habitual que haya al menos 100, y normalmente varios cientos de expresiones en el conjunto de datos de lenguaje o más. Además, si se espera que algunos tipos de consultas sean más habituales que otros, puede insertar varias copias de las consultas comunes en el conjunto de datos.

**P: ¿Puedo simplemente cargar una lista de palabras?**

**R.** : La carga de una lista de palabras las agregará al vocabulario, pero no enseñará al sistema cómo se usan normalmente. Al proporcionar expresiones completas o parciales (oraciones o frases que es probable que digan los usuarios), el modelo de lenguaje puede aprender las palabras nuevas y cómo se usan. El modelo de lenguaje personalizado es bueno no solo para agregar palabras nuevas al sistema, sino también para ajustar la probabilidad de palabras conocidas para la aplicación. Al proporcionar expresiones completas se ayuda al sistema a aprender mejor. 

## <a name="next-steps"></a>Pasos siguientes

* [Solución de problemas](troubleshooting.md)
* [Notas de la versión](releasenotes.md)
