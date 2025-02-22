---
title: Guía de inicio rápido con Azure Application Insights | Microsoft Docs
description: Proporciona instrucciones para configurar rápidamente una aplicación web ASP.NET Core para la supervisión con Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: 931de532aa6e09b2cd00955df6ba1f05d7e4f42c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67428507"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Inicio de la supervisión de la aplicación web ASP.NET Core

Con Azure Application Insights puede supervisar fácilmente la disponibilidad, el rendimiento y el uso de su aplicación web. También puede identificar y diagnosticar errores en la aplicación rápidamente sin tener que esperar a que un usuario informe de ellos. 

Esta guía de inicio rápido le ayudará a agregar el SDK de Application Insights a una aplicación web ASP.NET Core existente. Para información sobre cómo configurar Application Insights sin Visual Studio, consulte este [artículo](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core).

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido:

- [Instale Visual Studio 2019](https://www.visualstudio.com/downloads/) con las cargas de trabajo siguientes:
  - ASP.NET y desarrollo web
  - Desarrollo de Azure
- [Instalación del SDK de .NET Core 2.0](https://www.microsoft.com/net/core)
- Necesitará una suscripción de Azure y una aplicación web .NET Core existente.

Si no tiene una aplicación web ASP.NET Core, puede usar nuestra guía detallada para [crear una aplicación ASP.NET Core y agregar Application Insights.](../../azure-monitor/app/asp-net-core.md)

Si no tiene una suscripción a Azure, cree una cuenta [gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en el [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Habilitación de Application Insights

Application Insights recopila datos de telemetría desde cualquier aplicación conectada a Internet, independientemente de si se está ejecutando localmente o en la nube. Siga estos pasos para empezar a ver los datos.

1. Seleccione **Crear un recurso** > **Herramientas de desarrollo** > **Application Insights**.

   > [!NOTE]
   >Si esta es la primera vez que crea un recurso de Application Insights, puede obtener más información visitando la documentación [Creación de recursos en Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource).

    Aparece un cuadro de configuración, use la tabla siguiente para rellenar los campos de entrada.

   | Configuración        |  Valor           | DESCRIPCIÓN  |
   | ------------- |:-------------|:-----|
   | **Nombre**      | Nombre único global | Nombre que identifica la aplicación que se está supervisando |
   | **Grupo de recursos**     | myResourceGroup      | Nombre para el nuevo grupo de recursos que hospedará los datos de Application Insights |
   | **Ubicación** | Este de EE. UU | Elija una ubicación cerca de usted o de donde se hospeda la aplicación |

2. Haga clic en **Create**(Crear).

## <a name="configure-app-insights-sdk"></a>Configuración del SDK de Application Insights

1. Abra el **proyecto** de la aplicación web ASP.NET Core en Visual Studio > haga clic con el botón derecho en el nombre de la aplicación en el **Explorador de soluciones** > seleccione **Agregar**  >  **Telemetría de Application Insights**.

    ![Incorporación de los datos de telemetría de Application Insights](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Haga clic en el botón **Comenzar**

3. Seleccione su cuenta y su suscripción > Seleccione el **recurso existente** que creó en Azure Portal > Haga clic en **Registrar**.

4. Seleccione **Depurar** > **Iniciar sin depurar** (Ctrl + F5) para iniciar la aplicación.

    ![Menú Introducción de Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Los datos tardan unos 3-5 minutos en empezar a aparecer en el portal. Si se trata de una aplicación de prueba de poco tráfico, tenga en cuenta que la mayoría de las métricas se capturan solo cuando hay solicitudes u operaciones activas.

## <a name="start-monitoring-in-the-azure-portal"></a>Inicio de la supervisión en Azure Portal

1. Vuelva a abrir la **Información general** de Application Insights en Azure Portal; para ello, seleccione **Inicio** y, bajo los recursos recientes, seleccione el recurso que creó anteriormente para ver los detalles acerca de la aplicación que se ejecuta actualmente.

   ![Menú Introducción de Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Haga clic en **Mapa de la aplicación** para ver un diseño visual de las relaciones de dependencia entre los componentes de la aplicación. Cada componente muestra KPI como la carga, el rendimiento, errores y alertas.

   ![Mapa de aplicación](./media/dotnetcore-quick-start/5appmap.png)

3. Haga clic en el icono **App Analytics** ![icono de Mapa de Aplicación](./media/dotnetcore-quick-start/006.png) **Ver en Analytics**. Se abrirá **Application Insights Analytics**, que proporciona un lenguaje de consulta avanzado para analizar todos los datos recopilados por Application Insights. En este caso, se genera una consulta que representa el número de solicitudes en un gráfico. Puede escribir sus propias consultas para analizar otros datos.

   ![Gráfico de Analytics con las solicitudes de usuario durante un período de tiempo](./media/dotnetcore-quick-start/6analytics.png)

4. Vuelva a la página **Información general** y examine los paneles de indicadores clave de rendimiento.  Este panel proporciona estadísticas sobre el estado de aplicación, incluido el número de solicitudes entrantes, la duración de las solicitudes y los errores que se producen. 

   ![Gráficos de Escala de tiempo con información general de Estado](./media/dotnetcore-quick-start/7kpidashboards.png)

5. A la izquierda, haga clic en **Métrica**. Utilice el Explorador de métricas para investigar el estado y la utilización del recurso. Puede hacer clic en **Agregar nuevo gráfico** para crear vistas personalizadas adicionales o seleccionar **Editar** para modificar los tipos de gráfico existentes, el alto, la paleta de colores, las agrupaciones o las métricas. Por ejemplo, puede hacer un gráfico que muestre el tiempo de carga de páginas promedio del explorador si selecciona "Tiempo de carga de páginas del explorador" en la lista desplegable de las métricas y "Promedio" en la agregación. Para más información acerca del Explorador de métricas de Azure, consulte [Introducción al Explorador de métricas de Azure](../../azure-monitor/platform/metrics-getting-started.md).

     ![Pestaña Métricas: Gráfico de tiempo promedio de carga de páginas del explorador](./media/dotnetcore-quick-start/8metrics.png)

## <a name="video"></a>Vídeo

- Vídeo externo detallado sobre cómo [configurar Application Insights con .NET Core y Visual Studio](https://www.youtube.com/watch?v=NoS9UhcR4gA&t) desde cero.
- Vídeo externo detallado sobre cómo [configurar Application Insights con .NET Core y Visual Studio Code](https://youtu.be/ygGt84GDync) desde cero.

## <a name="clean-up-resources"></a>Limpieza de recursos
Cuando haya realizado las pruebas, puede eliminar el grupo de recursos y todos los recursos relacionados. Para ello, siga estos pasos.

1. En el menú izquierdo de Azure Portal, haga clic en **Grupos de recursos** y en **myResourceGroup**.
2. En la página del grupo de recursos, haga clic en **Eliminar**, escriba **myResourceGroup** en el cuadro de texto y haga clic en **Eliminar**.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Búsqueda y diagnóstico de excepciones en tiempo de ejecución](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)
