---
title: Programación de indexadores (Azure Search)
description: Programe los indexadores de Azure Search para indexar el contenido periódicamente o en horas específicas.
ms.date: 05/31/2019
author: HeidiSteen
manager: HeidiSteen
ms.author: heidist
services: search
ms.service: search
ms.devlang: ''
ms.topic: conceptual
ms.openlocfilehash: 245a2139aae0910ea1415811234667f2c06500ec
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855798"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Programación de indexadores para Azure Search
Los indexadores normalmente se ejecutan una vez, inmediatamente después de que se crean. Se pueden ejecutar a petición mediante el portal, la API REST o el SDK de .NET. También se puede configurar un indexador para que se ejecute periódicamente según una programación.

Estas son algunas situaciones en las que resulta útil programar el indexador:

* Los datos de origen cambiarán con el tiempo, y desea que los indexadores de Azure Search procesen automáticamente los datos modificados.
* El índice se rellenará desde varios orígenes de datos y desea asegurarse de que los indexadores se ejecutan a horas distintas para reducir los conflictos.
* Los datos de origen son muy grandes y desea expandir el procesamiento del indexador a lo largo del tiempo. Para más información acerca de la indexación de grandes volúmenes de datos, consulte [Indexación de grandes conjuntos de datos en Azure Search](search-howto-large-index.md).

El programador es una característica integrada de Azure Search. No se puede usar un programador externo para controlar los indexadores de búsqueda.

## <a name="define-schedule-properties"></a>Definición de las propiedades de la programación

Una programación de un indexador tiene dos propiedades:
* **Intervalo**, que define el tiempo que pasa entre las ejecuciones programadas del indexador. El intervalo más pequeño que se permite es 5 minutos, mientras que el mayor es 24 horas.
* **Hora de inicio (UTC)** , que indica la primera hora a el que se debe ejecutar el indexador.

La programación se puede especificar la primera vez que se crear el indexador, o bien se pueden actualizar las propiedades del indexador más adelante. Las programaciones del indexador se pueden establecer mediante el [portal](#portal), la [API REST](#restApi), o el [SDK de .NET](#dotNetSdk).

Solo se puede ejecutar a la vez una ejecución de un indexador. Si se está ejecutando un indexador cuando está programada su próxima ejecución, esta se pospone hasta la próxima hora programada.

Veamos un ejemplo entenderlo concretamente. Supongamos que se configura una programación de indexador con un **intervalo** de cada hora y su **hora de inicio** es el 1 de junio de 2019 a las 8:00:00 A. M. UTC. Esto es lo que puede suceder cuando la ejecución de un indexador tarda más de una hora:

* La primera ejecución del indexador se inicia el 1 de marzo de 2019 a las 8:00 A. M. Suponga que esta ejecución tarda 20 minutos (o un período inferior a 1 hora).
* La segunda ejecución se inicia el 1 de junio de 2019 a las 9:00 A. M UTC. Suponga que esta ejecución tarda 70 minutos (más de una hora) y no se completará hasta 10:10 A. M. UTC.
* La tercera ejecución está programada para empezar a 10:00 A. M. UTC, pero en ese momento todavía se está ejecutando la ejecución anterior. Luego esta ejecución programada se omite. La siguiente ejecución del indexador no se iniciará hasta las 11:00 A. M. UTC.

> [!NOTE]
> Si un indexador se establece en una programación determinada pero se produce repetidamente un error en el mismo documento una y otra vez cada vez se ejecuta, el indexador comenzará a ejecutarse en un intervalo menos frecuente (hasta un máximo de al menos una vez cada 24 horas) hasta que vuelva a avanzar correctamente.  Si cree que solucionó el problema que hacía que el indexador se bloqueara en un punto determinado, puede realizar una ejecución a petición del indexador y, si avanza correctamente, el indexador volverá a su intervalo de programación establecido.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>Definición de una programación en el portal

El Asistente para importar datos del portal le permite definir la programación de un indexador en tiempo de creación. El valor de la programación predeterminada es **Cada hora**, lo que significa que el indexador se ejecuta una vez después de que se crea y se vuelve a ejecutar posteriormente cada hora.

El valor de la programación se puede cambiar a **Una vez** si no desea que el indexador se vuelva a ejecutar automáticamente o a **Diariamente** para que se ejecute una vez al día. Establézcalo en **Personalizado** si desea especificar otro intervalo o una hora de inicio futura concreta.

Si la programación se establece en **Personalizado**, los campos le permiten especificar el **intervalo** y la **hora de inicio (UTC)** . El menor intervalo de tiempo que se permite es 5 minutos, mientras que el mayor es 1440 minutos (24 horas).

   ![Establecimiento de la programación del indexador en el Asistente para importar datos](media/search-howto-schedule-indexers/schedule-import-data.png "Setting indexer schedule in Import Data wizard")

Una vez creado un indexador, puede cambiar la configuración de la programación mediante el panel de edición del indexador. Los campos de Programación son los mismos que los del Asistente para importar datos.

   ![Establecimiento de la programación en el panel de edición del indexador](media/search-howto-schedule-indexers/schedule-edit.png "Setting the schedule in indexer Edit panel")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>Definición de una programación mediante la API REST

La programación de un indexador se puede definir mediante la API REST. Para ello, incluya la propiedad **schedule** al crear o actualizar el indexador. El ejemplo siguiente muestra una solicitud PUT para actualizar un indexador existente:

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

El parámetro **interval** es obligatorio. El intervalo se refiere al tiempo entre el inicio de dos ejecuciones consecutivas de indizador. El intervalo mínimo permitido es de 5 minutos y el máximo de un día. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). El patrón de este es: `P(nD)(T(nH)(nM))`. Ejemplos: `PT15M` para cada 15 minutos, `PT2H` para cada 2 horas.

El valor **startTime** opcional indica cuándo deben comenzar las ejecuciones programadas. Si se omite, se usará la hora UTC actual. Este tiempo puede estar en el pasado, en cuyo caso la primera ejecución se programa como si el indexador se hubiera ejecutado continuamente desde el valor de **starTime**.

También puede ejecutar un indexador a petición en cualquier momento mediante la llamada Run Indexer. Para más información acerca de cómo ejecutar los indizadores y establecer las programaciones del indexador, consulte [Run Indexer](https://docs.microsoft.com/rest/api/searchservice/run-indexer) (Ejecución del indexador), [Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer) (Obtención del indexador) y [Update Indexer](https://docs.microsoft.com/rest/api/searchservice/update-indexer) (Actualización del indexador) en la referencia de la API REST.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>Definición de una programación mediante el SDK de .NET

La programación de un indexador se puede definir mediante el SDK de .NET de Azure Search. Para ello, incluya la propiedad **schedule** al crear o actualizar cualquier indexador.

En el siguiente ejemplo de C# se crea un indexador, para lo que se usa un origen de datos y un índice predefinidos, y se establece su programación para que se ejecute una vez al día y que empiece en 30 minutos, a partir de este momento:

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
Si el parámetro **schedule** se omite, el indexador se ejecutará una sola vez inmediatamente después de su creación.

El parámetro **startTime** se puede establecer en un momento del pasado. En ese caso, la primera ejecución se programa como si el indexador se hubiera ejecutado continuamente desde el valor de **startTime**.

Para definir la programación se usa la clase [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet). El constructor **IndexingSchedule** requiere que se especifique un parámetro **interval** mediante un objeto **TimeSpan**. El valor de intervalo más pequeño que se permite es 5 minutos, mientras que el mayor es 24 horas. El segundo parámetro **startTime**, que se especifica como un objeto **DateTimeOffset**, es opcional.

El SDK de .NET permite controlar las operaciones del indexador mediante la clase [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) clase y su propiedad [Indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers), que implementa métodos de la interfaz de **IIndexersOperations**. 

Puede ejecutar un indexador a petición en cualquier momento mediante uno de los siguientes métodos: [Run](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync) o [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync).

Para más información acerca de la creación, actualización y ejecución de indexadores, consulte [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet).
