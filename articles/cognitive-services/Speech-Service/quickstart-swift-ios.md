---
title: 'Inicio rápido: Reconocimiento de voz en Swift (Servicios de voz)'
titleSuffix: Azure Cognitive Services
description: Aprenda a reconocer la voz en Swift para iOS mediante el SDK de Voz.
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: e9d17b0bdeb89fc03c0f089b84a89a5203c39566
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717421"
---
# <a name="quickstart-recognize-speech-in-swift-on-ios-using-the-speech-sdk"></a>Inicio rápido: Reconocimiento de voz en Swift para iOS mediante el SDK de Voz

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

En este artículo, aprenderá a crear una aplicación iOS en Swift mediante el SDK de Voz de Cognitive Services para transcribir a texto la voz grabada con un micrófono.

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, presentamos una lista de requisitos previos:

* Una [clave de suscripción](get-started.md) para el servicio Voz.
* Una máquina macOS con [Xcode 9.4.1](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12) o una versión posterior y [CocoaPods](https://cocoapods.org/) instalado.

## <a name="get-the-speech-sdk-for-ios"></a>Obtención del SDK de Voz para iOS

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

La versión actual del SDK de Speech de Cognitive Services es `1.6.0`. Tenga en cuenta que este tutorial no funcionará sin cambios en ninguna versión anterior del SDK.

El SDK de Voz de Cognitive Services para iOS se distribuye como un paquete de marcos.
Se puede usar en proyectos de Xcode como un administrador de dependencias [CocoaPod](https://cocoapods.org/), o se puede descargar desde https://aka.ms/csspeech/macosbinary y vincularse manualmente. En esta guía se usa CocoaPod.

## <a name="create-an-xcode-project"></a>Creación de un proyecto de Xcode

Inicie Xcode y haga clic en **Archivo** > **Nuevo** > **Proyecto** para iniciar un proyecto nuevo.
En el cuadro de diálogo de selección de plantilla, elija la plantilla "Aplicación de vista única (iOS)".

En los cuadros de diálogo que aparecen después, realice las selecciones siguientes:

1. Cuadro de diálogo Opciones del proyecto
    1. Escriba un nombre para la aplicación de inicio rápido, por ejemplo `helloworld`.
    1. Si ya tiene una cuenta de desarrollador de Apple, escriba un nombre y un identificador de organización adecuados. Con fines de prueba, puede elegir cualquier nombre, como `testorg`. Para firmar la aplicación, necesita un perfil de aprovisionamiento adecuado. Consulte el [sitio para desarrolladores de Apple](https://developer.apple.com/) para más información.
    1. Asegúrese de elegir Swift como lenguaje del proyecto.
    1. Desactive las casillas para usar guiones gráficos y para crear una aplicación basada en documentos. La interfaz de usuario sencilla para la aplicación de ejemplo se creará mediante programación.
    1. Desactive todas las casillas de las pruebas y los datos principales.
1. Selección del directorio del proyecto
    1. Elija un directorio en el que colocar el proyecto. Se crea un directorio `helloworld` en el directorio elegido que contiene todos los archivos del proyecto de Xcode.
    1. Deshabilite la creación de un repositorio de Git para este proyecto de ejemplo.
1. La aplicación también debe declarar el uso del micrófono en el archivo `Info.plist`. Haga clic en el archivo en la información general y agregue la clave "Privacidad: descripción de uso del micrófono", con un valor como "Microphone is needed for speech recognition" (Se necesita el micrófono para el reconocimiento de voz).
    ![Configuración de Info.plist](media/sdk/qs-swift-ios-info-plist.png)
1. Cierre el proyecto de Xcode. Más tarde, usará una instancia diferente de él después de configurar CocoaPods.

## <a name="add-the-sample-code"></a>Incorporación del código de ejemplo

1. Coloque un nuevo archivo de encabezado con el nombre `MicrosoftCognitiveServicesSpeech-Bridging-Header.h` en el directorio `helloworld` dentro del proyecto helloworld y pegue en él el siguiente código:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h#code)]
1. Agregue la ruta de acceso relativa `helloworld/MicrosoftCognitiveServicesSpeech-Bridging-Header.h` del encabezado puente a la configuración del proyecto de Swift para el destino helloworld en el campo *Objective-C Bridging Header* (Encabezado puente Objective-C) ![Propiedades de encabezado](media/sdk/qs-swift-ios-bridging-header.png).
1. Reemplace el contenido del archivo `AppDelegate.swift` generado automáticamente por lo siguiente:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/AppDelegate.swift#code)]
1. Reemplace el contenido del archivo `ViewController.swift` generado automáticamente por lo siguiente:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/helloworld/ViewController.swift#code)]
1. En `ViewController.swift`, reemplace la cadena `YourSubscriptionKey` por su clave de suscripción.
1. Reemplace la cadena `YourServiceRegion` por la [región](regions.md) asociada a sus suscripción (por ejemplo, `westus` para la suscripción de evaluación gratuita).

## <a name="install-the-sdk-as-a-cocoapod"></a>Instalación del SDK como CocoaPod

1. Instale el administrador de dependencias de CocoaPod como se describe en sus [instrucciones de instalación](https://guides.cocoapods.org/using/getting-started.html).
1. Vaya al directorio de la aplicación de ejemplo (`helloworld`). Coloque un archivo de texto con el nombre `Podfile` y el siguiente contenido en ese directorio:  
   [!code-swift[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/swift-ios/helloworld/Podfile)]
1. Vaya al directorio `helloworld` en un terminal y ejecute el comando `pod install`. Se genera un área de trabajo de Xcode `helloworld.xcworkspace` que contiene la aplicación de ejemplo y el SDK de Voz como dependencia. Este área de trabajo se usa en las secciones siguientes.

## <a name="build-and-run-the-sample"></a>Compilación y ejecución del ejemplo

1. Abra el área de trabajo `helloworld.xcworkspace` en Xcode.
1. Haga visible la salida de depuración (**Ver** > **Área de depuración** > **Activar consola**).
1. Elija el simulador de iOS o un dispositivo iOS conectado a la máquina de desarrollo como destino para la aplicación en la lista del menú **Producto** > **Destino**.
1. Para compilar y ejecutar el código de ejemplo en el simulador de iOS seleccione **Producto** > **Ejecutar** en el menú o haga clic en el botón **Reproducir**.
1. Después de hacer clic en el botón "Recognize" (Reconocer) de la aplicación y decir algunas palabras, verá el texto que ha dicho en la parte inferior de la pantalla.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Exploración de ejemplos en GitHub](https://aka.ms/csspeech/samples)
