---
title: Ampliación de Azure IoT Central con análisis personalizados | Microsoft Docs
description: Como desarrollador de soluciones, configure una aplicación de IoT Central para realizar análisis y visualizaciones personalizados. Esta solución utiliza Azure Databricks.
author: dominicbetts
ms.author: dobett
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: philmea
ms.openlocfilehash: e039e2b8d9c183b5bfee1bee47e4addc4c873bf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66743446"
---
# <a name="extend-azure-iot-central-with-custom-analytics"></a>Ampliación de Azure IoT Central con análisis personalizados

Esta guía paso a paso le muestra, como desarrollador de soluciones, cómo ampliar la aplicación IoT Central con análisis y visualizaciones personalizados. En el ejemplo se usa un área de trabajo de [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) para analizar el flujo de datos de telemetría de IoT Central y generar visualizaciones como [diagramas de cajas](https://wikipedia.org/wiki/Box_plot).

Esta guía paso a paso le muestra cómo ampliar IoT Central todavía más con las [herramientas de análisis integradas](howto-create-analytics.md).

En esta guía paso a paso, aprenderá a:

* Hacer streaming de datos de telemetría desde una aplicación de IoT Central mediante la *exportación de datos continua*.
* Cree un entorno de Azure Databricks para analizar y representar los datos de telemetría del dispositivo.

## <a name="prerequisites"></a>Requisitos previos

Recuerde que para completar los pasos de esta guía paso a paso, necesita una suscripción activa a Azure.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

### <a name="iot-central-application"></a>Aplicación de IoT Central

Cree una aplicación de IoT Central desde la página [Azure IoT Central: Mis aplicaciones](https://aka.ms/iotcentral) con la configuración siguiente:

| Configuración | Valor |
| ------- | ----- |
| Plan de pago | Pay-As-You-Go |
| Plantilla de la aplicación | Ejemplo Contoso |
| Nombre de la aplicación | Acepte el valor predeterminado o elija su propio nombre. |
| URL | Acepte el valor predeterminado o elija un prefijo de dirección URL único. |
| Directorio | El inquilino de Azure Active Directory |
| Suscripción de Azure | Su suscripción de Azure |
| Region | Este de EE. UU |

Los ejemplos y capturas de pantalla de este artículo utilizan la región **Este de EE. UU.** . Elija una ubicación cercana a usted y asegúrese de que crea todos los recursos en la misma región.

### <a name="resource-group"></a>Grupos de recursos

Use [Azure Portal para crear un grupo de recursos](https://portal.azure.com/#create/Microsoft.ResourceGroup) llamado **IoTCentralAnalysis** que contenga los otros recursos que cree. Cree los recursos de Azure en la misma ubicación que la aplicación de IoT Central.

### <a name="event-hubs-namespace"></a>Espacio de nombres de Event Hubs

Use [Azure Portal para crear un espacio de nombres de Event Hubs](https://portal.azure.com/#create/Microsoft.EventHub) con la siguiente configuración:

| Configuración | Valor |
| ------- | ----- |
| NOMBRE    | Elija el nombre del espacio de nombres |
| Plan de tarifa | Básica |
| Subscription | Su suscripción |
| Grupos de recursos | IoTCentralAnalysis |
| Ubicación | Este de EE. UU |
| Unidades de procesamiento | 1 |

### <a name="azure-databricks-workspace"></a>Área de trabajo de Azure Databricks

Use [Azure Portal para crear un servicio de Azure Databricks](https://portal.azure.com/#create/Microsoft.Databricks) con la siguiente configuración:

| Configuración | Valor |
| ------- | ----- |
| Nombre del área de trabajo    | Elija el nombre del área de trabajo. |
| Subscription | Su suscripción |
| Grupos de recursos | IoTCentralAnalysis |
| Ubicación | Este de EE. UU |
| Nivel de precios | Estándar |

Cuando haya creado los recursos necesarios, el grupo de recursos **IoTCentralAnalysis** se parecerá al de la captura de pantalla siguiente:

![Grupo de recursos IoTCentralAnalysis](media/howto-create-custom-analytics/resource-group.png)

## <a name="create-an-event-hub"></a>Creación de un centro de eventos

Puede configurar una aplicación de IoT Central para la exportación continua de datos de telemetría a un centro de eventos. En esta sección, creará un centro de eventos para recibir datos de telemetría de la aplicación de IoT Central. El centro de eventos entrega los datos de telemetría al trabajo de Stream Analytics para su procesamiento.

1. En Azure Portal, vaya al espacio de nombres de Event Hubs y seleccione **+ Centro de eventos**.
1. Asigne al centro de eventos el nombre **centralexport** y seleccione **Crear**.
1. En la lista de centros de eventos del espacio de nombres, seleccione **centralexport**. A continuación, elija **Directivas de acceso compartido**.
1. Seleccione **+Agregar**. Cree una directiva denominada **Listen** con la notificación **Listen**.
1. Cuando la directiva esté preparada, selecciónela en la lista y, a continuación, copie el valor de **Cadena de conexión: clave principal**.
1. Tome nota de esta cadena de conexión, la usará más adelante cuando configure su cuaderno de Databricks para leer desde el centro de eventos.

El espacio de nombres de Event Hubs se parece a la captura de pantalla siguiente:

![Espacio de nombres de Event Hubs](media/howto-create-custom-analytics/event-hubs-namespace.png)

## <a name="configure-export-in-iot-central"></a>Configuración de la exportación en IoT Central

Vaya a la [aplicación de IoT Central](https://aka.ms/iotcentral) que creó a partir de la plantilla de Contoso. En esta sección va a configurar la aplicación para que haga streaming de los datos de telemetría desde sus dispositivos simulados al centro de eventos. Para configurar la exportación:

1. Vaya a la página **Exportación de datos continua**, seleccione **+ Nuevo** y, a continuación, **Azure Event Hubs**.
1. Utilice los siguientes valores para configurar la exportación y, luego, seleccione **Guardar**:

    | Configuración | Valor |
    | ------- | ----- |
    | Display Name (Nombre para mostrar) | Exportar a Event Hubs |
    | habilitado | Por |
    | Espacio de nombres de Event Hubs | El nombre de su espacio de nombres de Event Hubs |
    | Centro de eventos | centralexport |
    | Medidas | Por |
    | Dispositivos | Off |
    | Plantillas de dispositivo | Off |

![Configuración de la exportación de datos continua](media/howto-create-custom-analytics/cde-configuration.png)

Espere hasta que el estado de la exportación sea **En ejecución** antes de continuar.

## <a name="configure-databricks-workspace"></a>Configuración del área de trabajo de Databricks

En Azure Portal, vaya al servicio Azure Databricks y seleccione **Launch Workspace** (Iniciar área de trabajo). Se abre una nueva pestaña en el explorador y se inicia sesión en el área de trabajo.

### <a name="create-a-cluster"></a>Creación de un clúster

En la página **Azure Databricks**, en la lista de tareas comunes, seleccione **Nuevo clúster**.

Use la información de la tabla siguiente para crear el clúster:

| Configuración | Valor |
| ------- | ----- |
| Cluster Name | centralanalysis |
| Modo de clúster | Estándar |
| Versión de Databricks Runtime | 5.3 (Scala 2.11, Spark 2.4.0) |
| versión de Python | 3 |
| Habilitar escalado automático | Sin |
| Terminar después de estos minutos de inactividad | 30 |
| Tipo de trabajo | Standard_DS3_v2 |
| Trabajos | 1 |
| Tipo de controlador | Igual que el trabajo |

La creación de un clúster puede tardar varios minutos, espere a que se complete antes de continuar.

### <a name="install-libraries"></a>Instalar bibliotecas

En la página **Clústeres**, espere a que el estado del clúster sea **En ejecución**.

En los pasos siguientes se indica cómo importar la biblioteca que necesita el ejemplo en el clúster:

1. En la página **Clústeres**, espere hasta que el estado del clúster interactivo **centralanalysis** sea **En ejecución**.

1. Seleccione el clúster y, después, elija la pestaña **Bibliotecas**.

1. En la pestaña **Bibliotecas**, elija **Instalar nueva**.

1. En la página **Install Library** (Instalar biblioteca), elija **Maven** como origen de la biblioteca.

1. En el cuadro de texto **Coordenadas**, escriba el siguiente valor: `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10`

1. Elija **Instalar** para instalar la biblioteca en el clúster.

1. El estado de la biblioteca es ahora **Instalada**:

    ![Biblioteca instalada](media/howto-create-custom-analytics/cluster-libraries.png)

### <a name="import-a-databricks-notebook"></a>Importación de un cuaderno de Databricks

Use los pasos siguientes para importar un cuaderno de Databricks que contiene el código de Python para analizar y visualizar los datos de telemetría de IoT Central:

1. Vaya al panel **Área de trabajo** del entorno de Databricks. Seleccione la lista desplegable situada junto a su nombre de cuenta y, a continuación, elija **Importar**.

1. Elija la opción para importar desde una dirección URL y escriba la siguiente dirección: [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Para importar el cuaderno, elija **Importar**.

1. Seleccione el **área de trabajo** para ver el cuaderno importado:

    ![Cuaderno importado](media/howto-create-custom-analytics/import-notebook.png)

1. Edite el código en la primera celda de Python para agregar la cadena de conexión de Event Hubs que guardó anteriormente:

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>Ejecución de análisis

Para ejecutar el análisis, debe asociar el cuaderno al clúster:

1. Seleccione **Desasociado** y, a continuación, seleccione el clúster **centralanalysis**.
1. Si el clúster no se está ejecutando, inícielo.
1. Para iniciar el cuaderno, seleccione el botón Ejecutar.

Puede ver un error en la última celda. Si es así, compruebe que las celdas anteriores se están ejecutando, espere un minuto para que algunos datos se escriban en el almacenamiento y, a continuación, ejecute de nuevo la última celda.

### <a name="view-smoothed-data"></a>Visualización de datos suavizados

En el cuaderno, desplácese hacia abajo hasta la celda 14 para ver un trazado con la humedad media acumulada por tipo de dispositivo. Este trazado se actualiza continuamente a medida que llegan los datos de telemetría por streaming:

![Trazado de datos de telemetría suavizados](media/howto-create-custom-analytics/telemetry-plot.png)

Puede cambiar el tamaño del gráfico en el cuaderno.

### <a name="view-box-plots"></a>Visualización de diagramas de cajas

En el cuaderno, desplácese hacia abajo hasta la celda 20 para ver los [diagramas de cajas](https://en.wikipedia.org/wiki/Box_plot). Los diagramas de cajas se basan en datos estáticos. Por ello, para actualizarlos debe volver a ejecutar la celda:

![Diagramas de cajas](media/howto-create-custom-analytics/box-plots.png)

Puede cambiar el tamaño de los trazados en el cuaderno.

## <a name="tidy-up"></a>Limpieza

Para hacer limpieza después de este artículo de procedimientos y evitar costos innecesarios, elimine el grupo de recursos **IoTCentralAnalysis** de Azure Portal.

Puede eliminar la aplicación de IoT Central desde la página **Administración** de la aplicación.

## <a name="next-steps"></a>Pasos siguientes

En esta guía paso a paso, ha aprendido lo siguiente:

* Hacer streaming de datos de telemetría desde una aplicación de IoT Central mediante la *exportación de datos continua*.
* Cree un entorno de Azure Databricks para analizar y representar datos de telemetría.

Ahora que sabe cómo crear un análisis personalizado, el siguiente paso sugerido es aprender a [Visualizar y analizar los datos de Azure IoT Central en un panel de Power BI](howto-connect-powerbi.md).
