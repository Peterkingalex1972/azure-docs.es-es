---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: faa93b75bde3a14e48baa7d27a3eb6439a137e44
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482435"
---
1. Inicie Visual Studio 2019.

1. Asegúrese de que la carga de trabajo de **desarrollo de Plataforma universal de Windows** está disponible. Elija **Herramientas** > **Get Tools and Features (Obtener herramientas y características)** desde la barra de menús de Visual Studio para abrir el instalador de Visual Studio. Si esta carga de trabajo ya está habilitada, cierre el cuadro de diálogo.

    ![Captura de pantalla del instalador de Visual Studio, con la pestaña Workloads (Cargas de trabajo) resaltada](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    En caso contrario, active la casilla junto a **Desarrollo multiplataforma de .NET Core** y elija **Modificar** en la esquina inferior derecha del cuadro de diálogo. La instalación de la nueva característica tardará un momento.

1. Crear una aplicación Windows Universal de Visual C# vacía. En primer lugar, elija **Archivo** > **Nuevo** > **Proyecto** en el menú. En el cuadro de diálogo **Nuevo proyecto**, expanda **Instalado** > **Visual C#**  > **Windows Universal** en el panel izquierdo. Luego, seleccione **Aplicación vacía (Windows universal)** . Para el nombre del proyecto, escriba *helloworld*.

    ![Captura de pantalla del cuadro de diálogo Nuevo proyecto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. El SDK de Voz requiere que la aplicación se cree para Windows 10 Fall Creators Update o posterior. En la ventana emergente **New Universal Windows Platform Project** (Nuevo proyecto de la Plataforma universal de Windows), elija **Windows 10 Fall Creators Update (10.0, compilación 16299)** como la **versión mínima**. En el cuadro **Versión de destino**, seleccione esta versión o cualquier otra posterior y, a continuación, haga clic en **Aceptar**.

    ![Captura de pantalla de la ventana New Universal Windows Platform Project (Nuevo proyecto de la Plataforma universal de Windows)](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Si está ejecutando Windows de 64 bits, puede cambiar la plataforma de compilación `x64` mediante el menú desplegable en la barra de herramientas de Visual Studio. (Windows de 64 bits puede ejecutar aplicaciones de 32 bits, por lo que puede dejarlo establecido en `x86` si lo prefiere).

   ![Captura de pantalla de la barra de herramientas de Visual Studio, con x64 resaltado](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > El SDK de Voz solo admite los procesadores compatibles con Intel. Actualmente, ARM no se admite.

1. Instale y haga referencia al [paquete NuGet del SDK de Voz](https://aka.ms/csspeech/nuget). En el Explorador de soluciones, haga clic con el botón derecho en la solución y seleccione **Manage NuGet Packages for Solution** (Administrar paquetes de NuGet para la solución).

    ![Captura de pantalla del Explorador de soluciones, con la opción Administrar paquetes NuGet para la solución resaltada](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. En la esquina superior derecha, en el campo **Origen del paquete**, seleccione **nuget.org**. Busque el paquete `Microsoft.CognitiveServices.Speech` e instálelo en el proyecto **helloworld**.

    ![Captura de pantalla del cuadro de diálogo de administrar paquetes para la solución](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Instalación de paquete de NuGet")

1. Acepte la licencia que aparece para comenzar la instalación del paquete de NuGet.

    ![Captura de pantalla de cuadro de diálogo de aceptación de licencia](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Acepte la licencia")

1. La línea de salida siguiente aparece en la consola del Administrador de paquetes.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.5.0' to helloworld
   ```

1. Dado que la aplicación usa el micrófono para entrada de voz, agregue la funcionalidad de **micrófono** al proyecto. En el Explorador de soluciones, haga doble clic en **Package.appxmanifest** para editar el manifiesto de aplicación. Después, seleccione la pestaña **Capabilities** (Funcionalidades), active la casilla de verificación de la funcionalidad **Microphone** (Micrófono) y guarde los cambios.

   ![Captura de pantalla del manifiesto de aplicación de Visual Studio, con las opciones Capabilities (Funcionalidades) y Microphone (Micrófono) resaltadas](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
