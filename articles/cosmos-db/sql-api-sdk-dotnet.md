---
title: 'Azure Cosmos DB: API, SDK y recursos de .NET para SQL'
description: Obtenga toda la información sobre la API y el SDK de .NET para SQL, incluidas la fechas de lanzamiento, fechas de retirada y cambios realizados entre las versiones del SDK para .NET de Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 4380bf81d05aa5247b57605b2aa53d24a73a0f68
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638575"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>SDK de Azure Cosmos DB para .NET para SQL API: descarga y notas de la versión
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
> * [Fuente de cambios de .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Proveedor de recursos de REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk Executor: .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor: Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Descarga del SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**Documentación de la API**|[Documentación de referencia de API de .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Muestras**|[Ejemplos de código de .NET](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Primeros pasos**|[Introducción al SDK para .NET de Azure Cosmos DB](sql-api-get-started.md)|
|**Tutorial de la aplicación web**|[Desarrollo de aplicaciones web con Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Plataforma admitida actualmente**|[Microsoft .NET 4.5 Framework](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Notas de la versión

> [!NOTE]
> Si usa .NET Framework, consulte la versión 3.x más reciente del [SDK de .NET](sql-api-sdk-dotnet-standard.md), que tiene como destino .NET Standard. 

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* La versión de System.Net.Http del SDK coincide con lo definido en el paquete NuGet.
* Permite que las solicitudes de escritura se devuelvan a una región diferente si la original produce un error.
* Agrega una directiva de reintentos de sesión para la solicitud de escritura.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Se corrige la condición de carrera de seguimiento para las consultas que causaron páginas vacías.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Mayor tamaño de precisión decimal para consultas de LINQ.
* Incorporación de nuevas clases CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType y PartitionKeyDefinitionVersion
* Incorporación de TimeToLivePropertyPath a DocumentCollection
* Incorporación de CompositeIndexes y SpatialIndexes a IndexPolicy
* Incorporación de Version a PartitionKeyDefinition
* Incorporación de None a PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * Incorporación de IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection y MaxTcpConnectionsPerEndpoint a ConnectionPolicy.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Mejoras de diagnósticos

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Se ha agregado la configuración de variable de entorno "POCOSerializationOnly".

* Se ha quitado DocumentDB.Spatial.Sql.dll y ahora se incluye en Microsoft.Azure.Documents.ServiceInterop.dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Mejora en la lógica de reintento durante la conmutación por error para las llamadas de ejecución StoredProcedure.

* DocumentClientEventSource se ha vuelto una base de datos única. 

* Corrección en el tiempo de espera de GatewayAddressCache que no respetaba ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Para el diagnóstico de transporte directo o TCP, se ha agregado TransportException, un tipo de excepción interna del SDK. Cuando aparece en los mensajes de excepción, este tipo imprime información adicional para solucionar problemas de conectividad de cliente.

* Se ha agregado una sobrecarga de constructor nueva que toma un objeto HttpMessageHandler, una pila de controlador HTTP que se usa para enviar solicitudes HttpClient (por ejemplo, HttpClientHandler).

* Se ha corregido un error por el que el encabezado con valores NULL no se controlaba correctamente.

* Se ha mejorado la validación de caché de las colecciones.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* System.Net.Security actualizada a 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Mejoras en el seguimiento de diagnósticos

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Se agregó más resistencia a errores transitorios de solicitud de varias regiones.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Se agregó compatibilidad con escrituras de varias regiones.
* Mejoras en el rendimiento de las consultas entre particiones con TOP y MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Se agregó la compatibilidad con la cancelación de solicitudes.
* Se agregó SetCurrentLocation a ConnectionPolicy, que rellena automáticamente las ubicaciones preferidas según la región.
* Se corrigió el error en las consultas de partición cruzada con Mín./máx. y un filtro que coincide con "ningún documento" en una partición individual.
* Los métodos de DocumentClient ahora tienen paridad con IDocumentClient.
* Se actualizó la pila de transporte TCP directo para reducir el número de conexiones establecidas.
* Se agregó la compatibilidad para TCP de modo directo para clientes que no sean Windows.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Se agregó la compatibilidad con la cancelación de solicitudes.
* Se agregó SetCurrentLocation a ConnectionPolicy, que rellena automáticamente las ubicaciones preferidas según la región.
* Se corrigió el error en las consultas de partición cruzada con Mín./máx. y un filtro que coincide con "ningún documento" en una partición individual.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview

* Los métodos de DocumentClient ahora tienen paridad con IDocumentClient.
* Se actualizó la pila de transporte TCP directo para reducir el número de conexiones establecidas.
* Se agregó la compatibilidad para TCP de modo directo para clientes que no sean Windows.

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Se agregó la propiedad ConsistencyLevel a FeedOptions.
* Se agregó JsonSerializerSettings a RequestOptions y FeedOptions.
* Se agregó EnableReadRequestsFallback a ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Se ha corregido KeyNotFoundException para el orden de particiones cruzadas por consultas en casos excepcionales.
* Se ha corregido el error, donde no se ha respetado el atributo JsonProperty de la cláusula select para consultas LINQ.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Se ha corregido un error que se producía en determinadas condiciones de carrera, que daba como resultado errores "Microsoft.Azure.Documents.NotFoundException: la sesión de lectura no está disponible para el token de sesión de entrada" intermitentes cuando se usa el nivel de coherencia de sesión.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Se ha corregido la regresión en la que FeedOptions.MaxItemCount = -1 produjo una excepción System.ArithmeticException: el tamaño de la página es negativo.
* Se agregó una nueva función ToString() a QueryMetrics.
* Se expusieron las estadísticas de partición en la lectura de las colecciones.
* Se agregó la propiedad PartitionKey a ChangeFeedOptions.
* Correcciones de errores leves.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Agrega la capacidad de especificar índices únicos para los documentos mediante el uso de la propiedad UniqueKeyPolicy en DocumentCollection.
* Se ha corregido un error en el cual la configuración personalizada de JsonSerializer no se estaba respetando para algunas consultas y para la ejecución de procedimientos almacenados.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Cambio de personalización de marca de Azure DocumentDB a Azure Cosmos DB en la documentación de referencia de API, la información de metadatos de los ensamblados y el paquete NuGet. 
* Exposición de la información de diagnóstico y la latencia de la respuesta de las solicitudes enviadas con el modo de conexión directa. Los nombres de propiedad son RequestDiagnosticsString y RequestLatency en la clase ResourceResponse.
* Esta versión del SDK requiere la versión más reciente del emulador de Azure Cosmos DB disponible para su descarga desde https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Cambios internos para los ensamblados de amigos de Microsoft.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Se han agregado varias mejoras y correcciones que aumentan la confiabilidad.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Se agregó compatibilidad para PartitionKeyRangeId como un FeedOption para definir el ámbito de los resultados de la consulta en un valor de intervalo de claves de partición específico. 
* Se agregó compatibilidad para StartTime como un ChangeFeedOption para empezar la búsqueda de los cambios después de ese momento.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Se corrigió un problema en la clase JsonSerializable que podría provocar una excepción de desbordamiento de pila.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Se corrigió un problema que requiere volver a compilar la aplicación debido a la incorporación de JsonSerializerSettings como un parámetro opcional en el constructor DocumentClient.
* Se marcó el constructor DocumentClient como obsoleto, porque se requería JsonSerializerSettings como el último parámetro para permitir los valores predeterminados de los parámetros ConnectionPolicy y ConsistencyLevel cuando se especifica el parámetro JsonSerializerSettings.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Se agregó compatibilidad con la especificación de JsonSerializerSettings personalizado al crear instancias de [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Se corrigió un problema que afectaba a las máquinas x64 que no admiten instrucciones SSE4 y que producía una excepción SEHException al ejecutar consultas SQL de Azure Cosmos DB.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Se agregó compatibilidad con un nuevo nivel de coherencia denominado ConsistentPrefix.
*   Se agregó compatibilidad con métricas de consulta para particiones individuales.
*   Se agregó compatibilidad para limitar el tamaño del token de continuación para las consultas.
*   Se agregó compatibilidad para un seguimiento más detallado de las solicitudes con error.
*   Se hicieron algunas mejoras de rendimiento en el SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funcionalmente es igual a 1.13.3. Se hicieron algunos cambios internos.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funcionalmente es igual a 1.13.2. Se hicieron algunos cambios internos.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Se ha corregido un problema que omitía el valor PartitionKey proporcionado en FeedOptions para consultas de funciones agregadas.
* Se ha corregido un problema en el control transparente de administración de particiones durante la ejecución entre particiones de la consulta Order By.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Se ha corregido un problema que provocaba interbloqueos en algunas de las API asincrónicas cuando se empleaban en el contexto de ASP.NET.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Correcciones para aumentar la resistencia de los SDK frente a la conmutación automática por error en determinadas condiciones.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Se ha corregido un problema que en ocasiones provoca una excepción WebException: No se pudo resolver el nombre remoto.
* Se ha agregado compatibilidad para leer un documento escrito directamente mediante la adición de nuevas sobrecargas a la API ReadDocumentAsync.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* Se agregó compatibilidad con consultas de agregación para LINQ (COUNT, MIN, MAX, SUM y AVG).
* Se corrigió un problema de pérdida de memoria para el objeto ConnectionPolicy causado por el uso del controlador de eventos.
* Se corrigió un problema por el que UpsertAttachmentAsync no funcionaba cuando se usaba el valor ETag.
* Se corrigió un problema por el que la continuación de la consulta order-by en la partición cruzada no funcionaba cuando se ordenaba por un campo de cadena.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Se agregó compatibilidad con consultas de agregación (COUNT, MIN, MAX, SUM y AVG). Consulte [Compatibilidad con agregación](sql-query-aggregates.md).
* Reducción del procesamiento mínimo en las colecciones particionadas de 10 100 RU/s a 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Corrección para un problema por el cual algunas de las consultas entre particiones no funcionaban correctamente en el proceso de host de 32 bits.
* Corrección para un problema por el cual el contenedor de la sesión no se actualizaba con el token para solicitudes erróneas en el modo de puerta de enlace.
* Corrección para un problema por el cual una consulta con llamadas UDF en proyección daba error en algunos casos.
* Revisiones de rendimiento de lado cliente para aumentar el procesamiento de lectura y escritura de las solicitudes.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Corrección para un problema por el cual el contenedor de la sesión no se actualizaba con el token para solicitudes erróneas.
* Se agregó compatibilidad para que el SDK funcione en un proceso de host de 32 bits. Tenga en cuenta que si utiliza consultas entre particiones, se recomienda el procesamiento de host de 64 bits para obtener un mejor rendimiento.
* Se mejoró el rendimiento en escenarios con consultas que tienen un número elevado de valores de clave de partición en una expresión IN.
* Se rellenaron diversas estadísticas de cuotas de recursos en ResourceResponse para las solicitudes de lectura de recopilación de documentos al establecer la opción de solicitud de PopulateQuotaInfo.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Corrección menor del rendimiento de la API de CreateDocumentCollectionIfNotExistsAsync incluida en 1.11.0.
* Corrección del rendimiento en el SDK para escenarios que implican un alto grado de solicitudes simultáneas.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Compatibilidad con nuevas clases y nuevos métodos para procesar la [fuente de cambios](change-feed.md) de documentos que forman parte de una colección.
* Compatibilidad con la continuación de consultas entre particiones y algunas mejoras de rendimiento para las consultas entre particiones.
* Adición de métodos CreateDatabaseIfNotExistsAsync y CreateDocumentCollectionIfNotExistsAsync.
* Compatibilidad con LINQ para las funciones del sistema: IsDefined, IsNull e IsPrimitive.
* Corrección para colocación automática de bins de los ensamblados Microsoft.Azure.Documents.ServiceInterop.dll y DocumentDB.Spatial.Sql.dll a la carpeta bin de la aplicación cuando se usa el paquete NuGet con proyectos que tienen herramientas de project.json.
* Compatibilidad con la emisión de los seguimientos de ETW de lado cliente que podrían resultar útiles en escenarios de depuración.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Se agregó compatibilidad de conectividad directa con colecciones con particiones.
* Mejoró el rendimiento para el nivel de coherencia de uso vinculado.
* Se han agregado los tipos de datos Polygon y LineString al especificar la directiva de indización de colecciones para las consultas espaciales de geovallado.
* Se agregó compatibilidad de LINQ con StringEnumConverter, IsoDateTimeConverter y UnixDateTimeConverter, a la vez que se traducen los predicados.
* Se corrigieron varios errores de SDK.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Se ha corregido un problema que provocaba la siguiente NotFoundException: La sesión de lectura no está disponible para el token de sesión de entrada. Esta excepción se produjo en algunos casos al consultar la región de lectura de una cuenta distribuida geográficamente.
* Se ha expuesto la propiedad ResponseStream en la clase ResourceResponse que permite el acceso directo a la secuencia subyacente de una respuesta.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Se han modificado las clases ResourceResponse, FeedResponse, StoredProcedureResponse y MediaResponse a fin de implementar la interfaz pública correspondiente para que se puedan simular con la finalidad de realizar una implementación basada en pruebas (TDD).
* Se ha corregido un problema que generaba un encabezado de clave de partición con formato incorrecto al usar un objeto JsonSerializerSettings personalizado para serializar los datos.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Se ha corregido un problema que provocaba el siguiente error en consultas de larga ejecución: El token de autorización no es válido en el momento actual.
* Se corrige un problema que quitaba la colección SqlParameterCollection original de las consultas multipartición principal/ordenar por.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Se ha agregado compatibilidad con las consultas paralelas en las colecciones particionadas.
* Se ha agregado compatibilidad con las consultas ORDER BY y TOP entre particiones en las colecciones particionadas.
* Se han corregido las referencias que faltaban en DocumentDB.Spatial.Sql.dll y Microsoft.Azure.Documents.ServiceInterop.dll, que se necesitan para hacer referencia a un proyecto de Azure Cosmos DB con una referencia al paquete NuGet de Azure Cosmos DB.
* Se ha corregido la funcionalidad relacionada con el uso de parámetros de diferentes tipos al utilizar las funciones definidas por el usuario de LINQ. 
* Se ha corregido un error en las cuentas replicadas globalmente que provocaba que las llamadas de Upsert se dirigieran a ubicaciones de lectura y no de escritura.
* Se han agregado a la interfaz IDocumentClient los métodos que faltaban: 
  * El método UpsertAttachmentAsync que toma mediaStream y opciones como parámetros
  * El método CreateAttachmentAsync que toma opciones como un parámetro
  * El método CreateOfferQuery que toma querySpec como un parámetro
* Se ha quitado el sello de las clases públicas expuestas en la interfaz IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Se ha agregado compatibilidad con cuentas de base de datos de varias regiones.
* Se ha agregado compatibilidad con el reintento en solicitudes limitadas.  El usuario puede personalizar el número de reintentos y el tiempo de espera máximo mediante la configuración de la propiedad ConnectionPolicy.RetryOptions.
* Se ha agregado una nueva interfaz IDocumentClient que define las firmas de todas las propiedades y métodos de DocumentClient.  Como parte de este cambio, también se han cambiado los métodos de extensión que crean IQueryable y IOrderedQueryable por los métodos de la propia clase DocumentClient.
* Se ha agregado la opción de configuración para definir el valor de ServicePoint.ConnectionLimit para un identificador URI de punto de conexión dado de Azure Cosmos DB.  Use ConnectionPolicy.MaxConnectionLimit para cambiar el valor predeterminado, que está establecido en 50.
* Se ha dejado de utilizar IPartitionResolver y su implementación.  La compatibilidad con IPartitionResolver está ahora obsoleta. Se recomienda usar colecciones con particiones para conseguir un almacenamiento y un rendimiento más elevados.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Se ha agregado una sobrecarga al identificador URI según el método ExecuteStoredProcedureAsync que toma RequestOptions como un parámetro.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Se ha agregado compatibilidad con período de vida (TTL) para los documentos.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Se corrige un error en el paquete de Nuget del SDK de .NET para empaquetarlo como parte de una solución de servicio en la nube de Azure.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Se han implementado [colecciones particionadas](partition-data.md) y [niveles de rendimiento definidos por el usuario](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Corregido]** La consulta del punto de conexión de Azure Cosmos DB genera: "System.Net.Http.HttpRequestException: Error al copiar el contenido en una secuencia".

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Compatibilidad de LINQ ampliada, incluidos nuevos operadores de paginación, expresiones condicionales y comparación de intervalos.
  * Operador Take para habilitar el comportamiento de SELECT TOP en LINQ
  * Operador CompareTo para habilitar las comparaciones de intervalos de cadenas
  * Operadores conditional (?) y coalesce (??)
* **[Corregido]** ArgumentOutOfRangeException al combinar la proyección Model en la consulta Where-In de LINQ. [81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Corregido]** Si Select no es la última expresión, el proveedor LINQ suponía que no había ninguna proyección y generaba SELECT * incorrectamente.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Upsert implementada, métodos de UpsertXXXAsync agregados
* Mejoras de rendimiento para todas las solicitudes
* Compatibilidad del proveedor LINQ con los métodos condicionales, de fusión y CompareTo para cadenas
* **[Corregido]** proveedor LINQ--&gt; la implementación del método Contains en List para generar el mismo SQL que en IEnumerable y en una matriz
* **[Corregido]** BackoffRetryUtility usa el mismo HttpRequestMessage de nuevo en lugar de crear uno nuevo al reintentar
* **[Obsoleto]** UriFactory.CreateCollection--&gt; debería usar ahora UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Corregido]** Problemas de localización al usar la información de referencia cultural diferente de EN como nl-NL etc. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Se agregó el enrutamiento basado en identificador
  * Nuevo asistente UriFactory para ayudar a construir vínculos de recursos basados en identificador
  * Nuevas sobrecargas en DocumentClient para tener en cuenta URI
* Se agregaron IsValid() y IsValidDetailed() en LINQ para geoespaciales
* Compatibilidad mejorada con el proveedor LINQ:
  * **Math** : Abs, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate
  * **String** : Concat, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper
  * **Array** : Concat, Contains, Count
  * **IN**

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Se agregó compatibilidad para la modificación de las directivas de indexación.
  * Nuevo método de ReplaceDocumentCollectionAsync en DocumentClient
  * Nueva propiedad IndexTransformationProgress en ResourceResponse\<T> para el seguimiento del porcentaje de progreso de cambios de la directiva de índice
  * DocumentCollection.IndexingPolicy ahora es mutable
* Se agregó compatibilidad para consulta e indexación espacial.
  * Nuevo espacio de nombres Microsoft.Azure.Documents.Spatial para serializar y deserializar tipos espaciales como punto y polígono
  * Nueva clase de SpatialIndex para la indexación de datos GeoJSON almacenados en Cosmos DB
* **[Corregido]** Consulta SQL incorrecta generada a partir de la expresión LINQ [n.º 38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Se agregó una dependencia de Newtonsoft.Json v5.0.7.
* Se hicieron cambios para admitir Order By:
  
  * Compatibilidad del proveedor LINQ para OrderBy() u OrderByDescending()
  * IndexingPolicy para admitir Order By 
    
    **Posible cambio importante** 
    
    Si tiene un código existente que aprovisiona colecciones con una directiva de indexación personalizada, debe actualizar el código existente para admitir la nueva clase IndexingPolicy. Si no tiene ninguna directiva de indexación personalizada, este cambio no le afectará.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Se agregó compatibilidad para las particiones de datos mediante las nuevas clases HashPartitionResolver y RangePartitionResolver y el IPartitionResolver.
* Se agregó serialización de DataContract.
* Se agregó compatibilidad con GUID en el proveedor LINQ.
* Se agregó compatibilidad con UDF en LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* SDK de GA

## <a name="release--retirement-dates"></a>Fechas de lanzamiento y de retirada
Microsoft notifica la retirada de un SDK con al menos **12 meses** de antelación para facilitar la transición a una versión compatible o más reciente.

Solo se agregan nuevas características, funcionalidad y optimizaciones al SDK actual, por lo que se recomienda actualizar siempre a la última versión del SDK tan pronto como sea posible. 

El servicio rechaza cualquier solicitud realizada a Azure Cosmos DB mediante un SDK retirado.

<br/>

| Versión | Fecha de lanzamiento | Fecha de retirada |
| --- | --- | --- |
| [2.5.1](#2.5.1) |02 de julio de 2019 |--- |
| [2.4.1](#2.4.1) |20 de junio de 2019 |--- |
| [2.4.0](#2.4.0) |5 de mayo de 2019 |--- |
| [2.3.0](#2.3.0) |4 de abril de 2019 |--- |
| [2.2.3](#2.2.3) |11 de febrero de 2019 |--- |
| [2.2.2](#2.2.2) |6 de febrero de 2019 |--- |
| [2.2.1](#2.2.1) |24 de diciembre de 2018 |--- |
| [2.2.0](#2.2.0) |7 de diciembre de 2018 |--- |
| [2.1.3](#2.1.3) |15 de octubre de 2018 |--- |
| [2.1.2](#2.1.2) |04 de octubre de 2018 |--- |
| [2.1.1](#2.1.1) |27 de septiembre de 2018 |--- |
| [2.1.0](#2.1.0) |21 de septiembre de 2018 |--- |
| [2.0.0](#2.0.0) |07 de septiembre de 2018 |--- |
| [1.22.0](#1.22.0) |19 de abril de 2018 |--- |
| [1.21.1](#1.20.1) |9 de marzo de 2018 |--- |
| [1.20.2](#1.20.1) |21 de febrero de 2018 |--- |
| [1.20.1](#1.20.1) |5 de febrero de 2018 |--- |
| [1.19.1](#1.19.1) |16 de noviembre de 2017 |--- |
| [1.19.0](#1.19.0) |10 de noviembre de 2017 |--- |
| [1.18.1](#1.18.1) |07 de noviembre de 2017 |--- |
| [1.18.0](#1.18.0) |17 de octubre de 2017 |--- |
| [1.17.0](#1.17.0) |10 de agosto de 2017 |--- |
| [1.16.1](#1.16.1) |7 de agosto de 2017 |--- |
| [1.16.0](#1.16.0) |2 de agosto de 2017 |--- |
| [1.15.0](#1.15.0) |30 de junio de 2017 |--- |
| [1.14.1](#1.14.1) |23 de mayo de 2017 |--- |
| [1.14.0](#1.14.0) |10 de mayo de 2017 |--- |
| [1.13.4](#1.13.4) |09 de mayo de 2017 |--- |
| [1.13.3](#1.13.3) |06 de mayo de 2017 |--- |
| [1.13.2](#1.13.2) |19 de abril de 2017 |--- |
| [1.13.1](#1.13.1) |29 de marzo de 2017 |--- |
| [1.13.0](#1.13.0) |24 de marzo de 2017 |--- |
| [1.12.2](#1.12.2) |20 de marzo de 2017 |--- |
| [1.12.1](#1.12.1) |14 de marzo de 2017 |--- |
| [1.12.0](#1.12.0) |15 de febrero de 2017 |--- |
| [1.11.4](#1.11.4) |06 de febrero de 2017 |--- |
| [1.11.3](#1.11.3) |26 de enero de 2017 |--- |
| [1.11.1](#1.11.1) |21 de diciembre de 2016 |--- |
| [1.11.0](#1.11.0) |08 de diciembre de 2016 |--- |
| [1.10.0](#1.10.0) |27 de septiembre de 2016 |--- |
| [1.9.5](#1.9.5) |01 de septiembre de 2016 |--- |
| [1.9.4](#1.9.4) |24 de agosto de 2016 |--- |
| [1.9.3](#1.9.3) |15 de agosto de 2016 |--- |
| [1.9.2](#1.9.2) |23 de julio de 2016 |--- |
| [1.8.0](#1.8.0) |14 de junio de 2016 |--- |
| [1.7.1](#1.7.1) |06 de mayo de 2016 |--- |
| [1.7.0](#1.7.0) |26 de abril de 2016 |--- |
| [1.6.3](#1.6.3) |08 de abril de 2016 |--- |
| [1.6.2](#1.6.2) |29 de marzo de 2016 |--- |
| [1.5.3](#1.5.3) |19 de febrero de 2016 |--- |
| [1.5.2](#1.5.2) |14 de diciembre de 2015 |--- |
| [1.5.1](#1.5.1) |23 de noviembre de 2015 |--- |
| [1.5.0](#1.5.0) |05 de octubre de 2015 |--- |
| [1.4.1](#1.4.1) |25 de agosto de 2015 |--- |
| [1.4.0](#1.4.0) |13 de agosto de 2015 |--- |
| [1.3.0](#1.3.0) |05 de agosto de 2015 |--- |
| [1.2.0](#1.2.0) |06 de julio de 2015 |--- |
| [1.1.0](#1.1.0) |30 de abril de 2015 |--- |
| [1.0.0](#1.0.0) |08 de abril de 2015 |--- |


## <a name="faq"></a>Preguntas más frecuentes
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Otras referencias
Para más información sobre Cosmos DB, consulte la página del servicio [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

