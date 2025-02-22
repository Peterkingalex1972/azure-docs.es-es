---
title: Exportación de telemetría desde Application Insights | Microsoft Docs
description: Exporte datos de diagnóstico y uso al almacenamiento en Microsoft Azure y descárguelos desde allí.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mbullwin
ms.openlocfilehash: 3238abcbcbc4d776e3736b13d5b32149c642649c
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68516942"
---
# <a name="export-telemetry-from-application-insights"></a>Exportación de telemetría desde Application Insights
¿Desea mantener la telemetría durante más tiempo que el período de retención estándar? ¿O quiere procesarla de algún modo especializado? La exportación continua es lo más conveniente para ello. Los eventos que se ven en el portal de Application Insights pueden exportarse a almacenamiento en Microsoft Azure en formato JSON. Desde allí puede descargar los datos y escribir el código necesario para procesarlos.  

Antes de configurar la exportación continua, hay algunas alternativas que conviene tener en cuenta:

* El botón Exportar de la parte superior de una pestaña de búsqueda o métricas permite transferir tablas y gráficos a una hoja de cálculo de Excel.

* [Analytics](../../azure-monitor/app/analytics.md) proporciona un lenguaje de consulta eficaz para telemetría. También puede exportar los resultados.
* Si lo que le interesa es [explorar los datos en Power BI](../../azure-monitor/app/export-power-bi.md ), puede hacerlo sin usar la exportación continua.
* La [API de REST de acceso a datos](https://dev.applicationinsights.io/) le permite acceder a datos de telemetría con programación.
* También puede tener acceso a la configuración de la [exportación continua a través de PowerShell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Cuando la exportación continua copie sus datos en el almacenamiento (donde pueden permanecer tanto tiempo como quiera), seguirán estando disponibles en Application Insights durante el [período de retención](../../azure-monitor/app/data-retention-privacy.md) habitual.

## <a name="continuous-export-advanced-storage-configuration"></a>Configuración avanzada del almacenamiento de exportación continua

La exportación continua **no admite** las siguientes características o configuraciones del almacenamiento de Azure:

* El uso de [los firewalls de VNET o Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security) junto con Azure Blob Storage.

* [Almacenamiento inmutable](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) para Azure Blob Storage.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="setup"></a>Creación de una exportación continua.

1. En el recurso de Application Insights de su aplicación en Configuración, a la izquierda, abra Exportación continua y elija **Agregar**:

2. Elija los tipos de datos de telemetría que quiere exportar.

3. Cree o seleccione una [cuenta de almacenamiento de Azure](../../storage/common/storage-introduction.md) donde quiera almacenar los datos. Para más información sobre las opciones de precios de almacenamiento, visite la [página oficial de precios](https://azure.microsoft.com/pricing/details/storage/).

     Haga clic en Agregar, Destino de exportación, Cuenta de almacenamiento y cree un nuevo almacén o elija uno.

    > [!Warning]
    > De forma predeterminada, la ubicación de almacenamiento se establecerá en la misma región geográfica que el recurso de Application Insights. Si los almacena en una región diferente, puede conllevar gastos de transferencia.

4. Cree o seleccione un contenedor en el almacenamiento.

Una vez que ha creado la exportación, comienza el proceso. Solo obtendrá los datos que lleguen después de crear la exportación.

Puede haber un retraso de aproximadamente una hora antes de que aparezcan los datos en el almacenamiento.

### <a name="to-edit-continuous-export"></a>Para editar la exportación continua

Haga clic en Exportación continua y seleccione la cuenta de almacenamiento que desee editar.

### <a name="to-stop-continuous-export"></a>Para detener la exportación continua

Para detener la exportación, haga clic en Deshabilitar. Al hacer clic en Habilitar de nuevo, la exportación se reinicia con nuevos datos. No obtendrá los datos que llegaron al portal mientras estaba deshabilitada la exportación.

Para detener la exportación de forma permanente, elimínela. Al realizar esta acción no se eliminan los datos del almacenamiento.

### <a name="cant-add-or-change-an-export"></a>¿No puede agregar o cambiar una exportación?
* Para agregar o cambiar las exportaciones, necesita derechos de acceso de propietario, colaborador o colaborador de Application Insights. [Más información sobre los roles][roles].

## <a name="analyze"></a> ¿Qué eventos obtiene?
Los datos exportados son la telemetría sin procesar que recibimos de la aplicación, aunque también agregamos los datos de ubicación que calculamos a partir de la dirección IP del cliente.

Los datos que se han descartado por [muestreo](../../azure-monitor/app/sampling.md) no se incluyen en los datos exportados.

No se incluyen otras métricas calculadas. Por ejemplo, no exportamos el uso medio de la CPU, pero sí la telemetría sin procesar a partir de la que se calcula la media.

Los datos también incluyen los resultados de cualquier [prueba web de disponibilidad](../../azure-monitor/app/monitor-web-app-availability.md) que haya configurado.

> [!NOTE]
> **Muestreo.** Si la aplicación envía muchos datos, la característica de muestreo puede operar y enviar solamente una fracción de la telemetría generada. [Aprenda más sobre el muestreo.](../../azure-monitor/app/sampling.md)
>
>

## <a name="get"></a> Inspección de los datos
Puede inspeccionar el almacenamiento directamente en el portal. Haga clic en Inicio en el menú de la izquierda, en la parte superior donde se indica "Servicios de Azure", seleccione **Cuentas de almacenamiento**, seleccione el nombre de la cuenta de almacenamiento, en la página de información general, seleccione **Blobs** bajo Servicios y, por último, seleccione el nombre del contenedor.

Para inspeccionar Azure Storage en Visual Studio, abra **Ver**, **Cloud Explorer**. Si no tienes ese comando de menú, deberá instalar Azure SDK: abra el cuadro de diálogo **Nuevo proyecto**, expanda Visual C#/Nube/ y seleccione **Obtener Microsoft Azure SDK para. NET**.

Al abrir el almacén de blobs, verá un contenedor con un conjunto de archivos blob. El URI de cada archivo que se deriva del nombre del recurso de Application Insights, su clave de instrumentación, y el tipo, fecha y hora de telemetría. (El nombre del recurso está todo en minúsculas y la clave de instrumentación omite guiones).

![Inspección del almacén de blobs con una herramienta apropiada](./media/export-telemetry/04-data.png)

La fecha y hora son UTC e indican cuándo se depositó la telemetría en el almacén, no la hora en que se generó. De modo que si escribe código para descargar los datos, se puede mover linealmente a través de los datos.

Este es el formato de la ruta de acceso:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Where

* `blobCreationTimeUtc` es la hora de creación del blob en el almacenamiento provisional interno.
* `blobDeliveryTimeUtc` es la hora de copia del blob en el almacenamiento de destino de exportación.

## <a name="format"></a> Formato de datos
* Cada blob es un archivo de texto que contiene varias filas separadas por' \n'. Contiene la telemetría procesada durante un período de aproximadamente la mitad de un minuto.
* Cada fila representa un punto de datos de telemetría, como una vista de página o una solicitud.
* Cada fila es un documento JSON sin formato. Si quiere sentarse a mirarlo, ábralo en Visual Studio y elija Editar, Avanzadas, Archivo de formato:

![Visualización de la telemetría con una herramienta apropiada](./media/export-telemetry/06-json.png)

Las duraciones de tiempo son tics, donde 10 000 tics = 1 ms. Por ejemplo, estos valores muestran un tiempo de 1 ms para enviar una solicitud desde el explorador, 3 ms para recibirla y 1,8 s para procesar la página en el explorador:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Referencia detallada del modelo de datos para los tipos y valores de propiedad.](export-data-model.md)

## <a name="processing-the-data"></a>Procesamiento de los datos
En una pequeña escala, puede escribir código para separar sus datos, leerlos en una hoja de cálculo, etc. Por ejemplo:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Para obtener un ejemplo de código mayor, consulte [cómo usar un rol de trabajo][exportasa].

## <a name="delete"></a>Eliminación de los datos antiguos
Es responsable de administrar la capacidad de almacenamiento y de eliminar los datos antiguos, si fuera necesario.

## <a name="if-you-regenerate-your-storage-key"></a>Si vuelve a generar la clave de almacenamiento...
Si cambia la clave para el almacenamiento, la exportación continua dejará de funcionar. Verá una notificación en su cuenta de Azure.

Abra la pestaña Exportación continua y edite la exportación. Modifique el destino de exportación, pero deje el mismo almacenamiento seleccionado. Haga clic en Aceptar para confirmar.

La exportación continua se reiniciará.

## <a name="export-samples"></a>Ejemplos de exportación

* [Exportación a SQL con el Stream Analytics][exportasa]
* [Ejemplo 2 de Stream Analytics](export-stream-analytics.md)

En escalas más grandes, considere la posibilidad de clústeres de Hadoop en [HDInsight](https://azure.microsoft.com/services/hdinsight/) en la nube. HDInsight ofrece una amplia variedad de tecnologías para administrar y analizar macrodatos, y puede usarlo para procesar datos que se han exportado desde Application Insights.

## <a name="q--a"></a>Preguntas y respuestas
* *Lo único que quiero es una descarga única de un gráfico.*  

    Sí, puede hacerlo. En la parte superior de la pestaña, haga clic en **Exportar datos**.
* *Configuro una exportación, pero no hay ningún dato en el almacén.*

    ¿Recibió Application Insights alguna telemetría de su aplicación desde que configuró la exportación? Solo recibirá datos nuevos.
* *Intenté configurar una exportación, pero se deniega el acceso.*

    Si la cuenta pertenece a su organización, debe ser miembro de los grupos de propietarios o colaboradores.
* *¿Puedo exportar directamente a mi propio almacén local?*

    Lamentablemente, no. Nuestro motor de exportación actualmente solo funciona con el almacenamiento de Azure.  
* *¿Hay ningún límite para la cantidad de datos que puedo colocar en mi almacén?*

    No. Seguiremos insertando datos hasta que elimine la exportación. Pararemos si alcanzamos los límites externos del almacenamiento de blobs, pero hasta llegar ahí falta mucho. Depende de usted controlar la cantidad de almacenamiento que usa.  
* *¿Cuántos blobs debería ver en el almacenamiento?*

  * Para cada tipo de datos que seleccionó para exportar, se crea un blob nuevo cada minuto (si los datos están disponibles).
  * Además, para las aplicaciones con mucho tráfico, se asignan unidades de partición adicionales. En este caso, cada unidad crea un blob cada minuto.
* *Volví a generar la clave de mi almacenamiento o cambié el nombre del contenedor, y ahora no funciona la exportación.*

    Edite la exportación y abra la pestaña de destino de la exportación. Deje el mismo almacenamiento seleccionado que antes y haga clic en Aceptar para confirmar. La exportación se reiniciará. Si el cambio estaba dentro de los últimos días, no perderá datos.
* *¿Puedo detener la exportación?*

    Sí. Haga clic en Deshabilitar.

## <a name="code-samples"></a>Ejemplos de código

* [Ejemplo de Stream Analytics](export-stream-analytics.md)
* [Exportación a SQL con el Stream Analytics][exportasa]
* [Referencia detallada del modelo de datos para los tipos y valores de propiedad.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
