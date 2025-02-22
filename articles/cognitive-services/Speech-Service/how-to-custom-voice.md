---
title: ¿Qué es la voz personalizada? - Servicio de Voz
titleSuffix: Azure Cognitive Services
description: Custom Voice es un conjunto de herramientas en línea que permiten crear una voz única y reconocible para su marca. Todo lo que se necesita para empezar son unos cuantos archivos de audio y las transcripciones asociadas. Siga los vínculos que se incluyen a continuación para empezar a crear una experiencia personalizada de conversión de voz a texto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 10d76bc1dd52f04cceb9f0952a755c55d90c6896
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562789"
---
# <a name="get-started-with-custom-voice"></a>Introducción a voz personalizada

Custom Voice es un conjunto de herramientas en línea que permiten crear una voz única y reconocible para su marca. Todo lo que se necesita para empezar son unos cuantos archivos de audio y las transcripciones asociadas. Siga los vínculos que se incluyen a continuación para empezar a crear una experiencia personalizada de conversión de texto a voz.

## <a name="whats-in-custom-voice"></a>¿Qué incluye Custom Voice?

Antes de comenzar con Custom Voice, necesitará una cuenta de Azure y una suscripción de Servicios de voz. Cuando haya creado una cuenta, podrá preparar los datos, entrenar y probar los modelos, evaluar la calidad de la voz y, finalmente, implementar el modelo de voz personalizado.

En el diagrama siguiente se indican los pasos para crear un modelo de voz personalizado mediante el portal de Custom Voice. Siga los vínculos para obtener más información.

![Diagrama de arquitectura de Custom Voice](media/custom-voice/custom-voice-diagram.png)

1.  [Suscríbase y cree un proyecto](#set-up-your-azure-account): cree una cuenta de Azure y una suscripción de Servicios de voz. Esta suscripción unificada proporciona acceso a la conversión de voz a texto, la conversión de texto a voz, la traducción de voz y el portal de Custom Voice. A continuación, mediante la suscripción a Servicios de voz, cree su primer proyecto de Custom Voice.

2.  [Cargar datos](how-to-custom-voice-create-voice.md#upload-your-datasets): carga de datos (audio y texto) mediante el portal de Custom Voice o una API de voz personalizada. Desde el portal, puede investigar y evaluar las puntuaciones de pronunciación y las relaciones de señal a ruido. Para más información, consulte el artículo sobre la [preparación de datos para Custom Voice](how-to-custom-voice-prepare-data.md).

3.  [Entrenar el modelo](how-to-custom-voice-create-voice.md#build-your-custom-voice-model): utilice los datos para crear un modelo de voz de conversión de texto a voz personalizada. Puede entrenar un modelo en diferentes idiomas. Después del entrenamiento, pruebe el modelo y, si le satisface el resultado, ya puede implementar el modelo.

4.  [Implementar el modelo](how-to-custom-voice-create-voice.md#create-and-use-a-custom-voice-endpoint): cree un punto de conexión personalizado para el modelo de voz de conversión de texto a voz y utilícelo para la síntesis de voz en sus productos, herramientas y aplicaciones.

## <a name="set-up-your-azure-account"></a>Configuración de la cuenta de Azure

Para poder usar el portal de Custom Speech y crear un modelo personalizado, se necesita una suscripción de Servicios de voz. Siga estas instrucciones para crear una suscripción a Servicios de voz en Azure. Si no tiene una cuenta de Azure, puede registrarse para obtener una nueva.  

Después de crear una cuenta de Azure y la suscripción de Servicios de voz, deberá iniciar sesión en el portal de Custom Voice y conectarse a su suscripción.

1. Obtenga la clave de la suscripción de Servicios de voz en Azure Portal.
2. Inicie sesión en el [portal de Custom Voice](https://aka.ms/custom-voice).
3. Seleccione la suscripción y cree un proyecto de voz.
4. Si desea cambiar a otra suscripción de voz, utilice el icono de engranaje situado en el panel de navegación superior.

> [!NOTE]
> El servicio Custom Voice no admite la clave de evaluación gratuita de 30 días. Debe tener una clave F0 o S0 creada en Azure para poder usar el servicio.

## <a name="how-to-create-a-project"></a>Creación de un proyecto

El contenido como datos, modelos, pruebas y puntos finales se organizan en **proyectos** en el portal de Custom Voice. Cada proyecto es específico de un idioma o país y del género de la voz que desea crear. Por ejemplo, puede crear un proyecto de voz femenina para los bots de chat del centro de llamadas que utilizan el inglés de Estados Unidos (en-US).

Para crear su primer proyecto, seleccione la pestaña **Text-to-Speech/Custom Voice** (Conversión de texto a voz/Conversión de voz personalizada) y, después, haga clic en **New Project** (Nuevo proyecto). Siga las instrucciones del asistente para crear el proyecto. Después de crear el proyecto, verá cuatro pestañas: **Data** (Datos), **Training** (Entrenamiento), **Testing** (Pruebas) y **Deployment** (Implementación). Use los vínculos incluidos en [Pasos siguientes](#next-steps) para aprender a usar cada pestaña.

## <a name="next-steps"></a>Pasos siguientes

- [Preparación de datos de voz personalizado](how-to-custom-voice.md)
- [Creación de una voz personalizada](how-to-custom-voice-create-voice.md)
- [Guía: Grabación de muestras de voz](record-custom-voice-samples.md)
