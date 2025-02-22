---
title: 'Introducción a Azure Search: Azure Search'
description: Azure Search es un servicio de búsqueda de Microsoft completamente administrado hospedado en la nube. Revise las descripciones de características, el flujo de trabajo de desarrollo, comparaciones con otros productos de Búsqueda de Microsoft y cómo empezar.
manager: nitinme
author: HeidiSteen
services: search
ms.service: search
ms.topic: overview
ms.date: 08/21/2019
ms.author: heidist
ms.openlocfilehash: 204951f725c2885fe9f8bf33fffe83e55628dd34
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899684"
---
# <a name="what-is-azure-search"></a>¿Qué es Azure Search?

Azure Search es una solución en la nube de búsqueda como servicio que proporciona a los desarrolladores las API y herramientas necesarias para agregar una experiencia de búsqueda de datos enriquecida en un contenido privado y heterogéneo en las aplicaciones web, para dispositivos móviles y empresariales. El código personalizado invoca la ingesta de datos (indexación) para crear y cargar un índice. Por otra parte, el código de aplicación genera solicitudes de consulta y controla las respuestas. La experiencia de búsqueda se define en el cliente mediante el uso de funciones de Azure Search, con la ejecución de consultas en un índice persistente que crea, es de su propiedad y almacena en Azure Search.

![Arquitectura de Azure Search](media/search-what-is-azure-search/azure-search-diagram.svg "Arquitectura de Azure Search")

La funcionalidad se expone a través de [API de REST](/rest/api/searchservice/) o [SDK de .NET](search-howto-dotnet-sdk.md) sencillos que enmascaran la complejidad inherente de la recuperación de información. Además de las API, Azure Portal proporciona soporte administrativo y de administración de contenido, con herramientas para la creación de prototipos y la consulta de índices. Como el servicio se ejecuta en la nube, Microsoft administra la infraestructura y la disponibilidad.

## <a name="when-to-use-azure-search"></a>Uso de Azure Search

Azure Search es adecuado en los siguientes escenarios de aplicación:

+ Consolidación de tipos de contenido heterogéneos en un único índice privado que admita búsquedas. Las consultas siempre se realizan en un índice que haya creado y en el que haya cargado documentos; este índice siempre reside en la nube del servicio Azure Search. Puede rellenar un índice con secuencias de documentos JSON de cualquier origen o plataforma. Como alternativa, para el contenido proporcionado en Azure, puede usar un *indexador* para introducir datos en un índice. Definir el índice y la propiedad o administración son uno de los principales motivos para usar Azure Search.

+ Implementación sencilla de las características relacionadas con la búsqueda. Las API de Azure Search simplifican la creación de consultas, la navegación por facetas, el filtrado (incluida la búsqueda geoespacial), la asignación de sinónimos, las consultas de escritura anticipada y la optimización de la relevancia. Mediante el uso de las características integradas, puede satisfacer las expectativas del usuario final y ofrecer una experiencia de búsqueda similar a la de los motores de búsqueda web comerciales.

+ Indexación del texto no estructurado, o bien extracción de texto e información de archivos de imagen. La característica de [búsqueda cognitiva](cognitive-search-concept-intro.md) de Azure Search agrega procesamiento mediante inteligencia artificial a una canalización de indexación. Algunos casos de uso comunes son OCR en documentos digitalizados, reconocimiento de entidades y extracción de frases clave en documentos de gran tamaño, detección de idioma y traducción de textos, y análisis de sentimiento.

+ Requisitos lingüísticos obtenidos mediante los analizadores de idioma y los analizadores personalizados de Azure Search. Si tiene contenido que no está en inglés, Azure Search admite tanto los analizadores de Lucene como los procesadores de lenguaje natural de Microsoft. También puede configurar los analizadores para obtener un procesamiento especializado de contenido sin procesar, como el filtrado de los caracteres diacríticos.

<a name="feature-drilldown"></a>

## <a name="feature-descriptions"></a>Descripciones de características

| Búsqueda&nbsp;principal&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  | Características |
|-------------------|----------|
|Búsqueda de texto de forma libre | La [**búsqueda de texto completo**](search-lucene-query-architecture.md) es el principal caso de uso para la mayoría de las aplicaciones basadas en búsquedas. Las consultas se formulan con una sintaxis compatible. <br/><br/>[**Sintaxis de consulta simple**](query-simple-syntax.md) ofrece operadores lógicos, de búsqueda de frase, de sufijo y de precedencia.<br/><br/>[**Sintaxis de consulta de Lucene**](query-lucene-syntax.md) incluye todas las operaciones en sintaxis simple, con extensiones para la búsqueda aproximada, búsqueda de proximidad, incremento de términos y expresiones regulares.|
| Relevancia | La [**puntuación simple**](index-add-scoring-profiles.md) es una ventaja clave de Azure Search. Los perfiles de puntuación sirven para modelar la relevancia en función de los valores de los propios documentos. Por ejemplo, tal vez se desea que los productos más recientes o con descuento aparezcan arriba en los resultados de búsqueda. También se pueden crear perfiles de puntuación mediante etiquetas para puntuaciones personalizadas, según las preferencias de búsqueda de los clientes de las que se ha hecho seguimiento y se han almacenado por separado. |
| Búsqueda georreferenciada | Azure Search procesa, filtra y muestra las ubicaciones geográficas. Permite a los usuarios explorar datos basados en la proximidad de un resultado de búsqueda a una ubicación física. [Vea este vídeo](https://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data) o [revise este ejemplo](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) para más información. |
| Filtros y facetas | La [**navegación por facetas**](search-faceted-navigation.md) se habilita con un único parámetro de consulta. Azure Search devuelve una estructura de navegación por facetas que puede utilizar como código subyacente a una lista de categorías para el filtrado autodirigido (por ejemplo, para filtrar los elementos de catálogo por intervalo de precios o marca). <br/><br/> Se pueden usar [**filtros**](query-odata-filter-orderby-syntax.md) para incorporar fácilmente la navegación por facetas en la interfaz de usuario de la aplicación, mejorar la formulación de consulta y filtrar según los criterios especificados por el usuario o el desarrollador. Los filtros se crean con la sintaxis de OData. |
| Características de la experiencia del usuario | La función [**autocompletar**](search-autocomplete-tutorial.md) puede habilitarse para realizar sugerencias de búsqueda para las consultas en una barra de búsqueda. <br/><br/>La función [**sugerencias de búsqueda**](https://docs.microsoft.com/rest/api/searchservice/suggesters) también funciona fuera de las entradas de texto parcial en una barra de búsqueda, pero los resultados son documentos reales en el índice en lugar de términos de consulta. <br/><br/>Los [**sinónimos**](search-synonyms.md) asocian términos equivalentes que expanden implícitamente el ámbito de una consulta, sin que el usuario tenga que proporcionar los términos alternativos. <br/><br/>El [**resaltado de referencias**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) aplica un formato de texto a una palabra clave coincidente en los resultados de búsqueda. Se puede elegir qué campos devuelven los fragmentos resaltados.<br/><br/>La [**ordenación**](https://docs.microsoft.com/rest/api/searchservice/Search-Documents) se ofrece para varios campos mediante el esquema de índice y se activa o se desactiva en el momento de la consulta con un único parámetro de búsqueda.<br/><br/> La [**paginación**](search-pagination-page-layout.md) y la limitación de los resultados de búsqueda se aplican fácilmente con el control de precisión que Azure Search ofrece para los resultados de búsqueda.  <br/><br/>|

| Enriquecimiento&nbsp;por IA&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;       | Características |
|-------------------|----------|
|Documentos enriquecidos con inteligencia artificial | [**Cognitive Search**](cognitive-search-concept-intro.md) para el análisis de texto e imágenes se puede aplicar a una canalización de indexación para extraer información de texto a partir de contenido sin formato. Algunos ejemplos de [habilidades integradas](cognitive-search-predefined-skills.md) incluyen el reconocimiento óptico de caracteres (para realizar búsquedas en archivos JPEG escaneados), el reconocimiento de entidades (identificación de una organización, nombre o ubicación) y el reconocimiento de frases principales. También puede [codificar habilidades personalizadas](cognitive-search-create-custom-skill-example.md) para adjuntar a la canalización. |
| Enriquecimientos almacenados para su análisis y consumo| [**Knowledge Store (versión preliminar)** ](knowledge-store-concept-intro.md) es una extensión de indexación basada en inteligencia artificial. Con el almacenamiento de Azure como back-end, puede guardar enriquecimientos creados durante la indización. Estos artefactos pueden usarse para ayudarle a diseñar mejores conjuntos de aptitudes, o a crear formas y estructuras a partir de datos amorfos o ambiguos. Puede crear proyecciones de estas estructuras que estén dirigidas a cargas de trabajo o usuarios específicos. También puede analizar directamente los datos extraídos o cargarlos en otras aplicaciones.<br/><br/> |

| Importación/indexación&nbsp;de datos | Características |
|----------------------------------|----------|
| Orígenes de datos | Los índices de Azure Search aceptan datos de cualquier origen, siempre que se envíe como estructura de datos JSON. <br/><br/> Los [**indizadores**](search-indexer-overview.md) automatizan la ingesta de datos para orígenes de datos de Azure compatibles y se encargan de la serialización de JSON. Conéctese a [Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md), [Azure Cosmos DB](search-howto-index-cosmosdb.md) o [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) para extraer el contenido que se puede buscar en los almacenes de datos principales. Los indexadores de Azure Blog pueden realizar el *descifrado de documentos* para [extraer texto de los principales formatos de archivo](search-howto-indexing-azure-blob-storage.md), incluidos los documentos de Microsoft Office, PDF y HTML. |
| Estructuras de datos jerárquicas y anidadas | Los [**tipos complejos**](search-howto-complex-data-types.md) y las colecciones le permiten modelar casi cualquier tipo de estructura JSON como un índice de Azure Search. La cardinalidad "uno a varios" y "varios a varios" se puede expresar de forma nativa a través de colecciones, tipos complejos y colecciones de tipos complejos.|
| Análisis lingüístico | Los analizadores son componentes que se usan para el procesamiento de texto durante las operaciones de indexación y búsqueda. Hay dos tipos. <br/><br/>Los [**analizadores léxicos personalizados**](index-add-custom-analyzers.md) se usan para las consultas de búsqueda complejas, mediante la coincidencia de fonética y expresiones regulares. <br/><br/>Los [**analizadores de idioma**](index-add-language-analyzers.md) de Lucene o de Microsoft se usan para controlar de manera inteligente la lingüística específica del idioma, como tiempos verbales, género, nombres plurales irregulares (por ejemplo, "régimen" frente a "regímenes"), separación de palabras compuestas, separación de palabras (para idiomas sin espacios) y mucho más. <br/><br/>|


| Nivel&nbsp;de plataforma&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Características |
|-------------------|----------|
| Herramientas para la creación de prototipos y la inspección | En el portal, puede usar el [**Asistente para importación de datos**](search-import-data-portal.md) para configurar los indexadores, el diseñador de índices para crear un índice y el [**Explorador de búsqueda**](search-explorer.md) para probar consultas y perfeccionar los perfiles de puntuación. También puede abrir cualquier índice para ver su esquema. |
| Supervisión y diagnóstico | [**Habilite las características de supervisión**](search-monitor-usage.md) para ir más allá de las "métricas de un vistazo" que están siempre disponibles en el portal. Las métricas sobre consultas por segundo, latencia y limitación se capturan y notifican en páginas del portal sin que sea necesaria ninguna configuración adicional. <br/><br/>El [**análisis de tráfico de búsqueda**](search-traffic-analytics.md) es otra alternativa de supervisión, en la que los datos tanto del servidor como del cliente se recopilan y analizan para descubrir información acerca de lo que los usuarios están escribiendo en el cuadro de búsqueda. |
| Cifrado del servidor | El [**cifrado en reposo administrado por Microsoft**](search-security-overview.md#encrypted-transmission-and-storage) está integrado en la capa de almacenamiento interno y es irrevocable. Si lo desea, puede complementar el cifrado predeterminado con [ **claves de cifrado administradas por el cliente (versión preliminar)** ](search-security-manage-encryption-keys.md). Las claves que se crean y administran en Azure Key Vault se usan para cifrar los índices y asignaciones de sinónimos en Azure Search. |
| Infraestructura | La **plataforma de alta disponibilidad** garantiza una experiencia de servicio de búsqueda muy confiable. Cuando se escala correctamente, [Azure Search ofrece un contrato de nivel de servicio del 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).<br/><br/> **Totalmente administrada y escalable** como cualquier solución completa, Azure Search no requiere ninguna administración de la infraestructura en absoluto. El servicio se puede adaptar a sus necesidades mediante el escalado en dos dimensiones para controlar un mayor almacenamiento de documentos, mayores cargas de consultas o ambos.<br/><br/>|

## <a name="how-to-use-azure-search"></a>Uso de Azure Search
### <a name="step-1-provision-service"></a>Paso 1: Servicio de aprovisionamiento
Puede aprovisionar un servicio de Azure Search en [Azure Portal](https://portal.azure.com/) o mediante la [API de Administración de recursos de Azure](/rest/api/searchmanagement/). Puede elegir cualquier servicio gratuito compartido con otros suscriptores o un [nivel de pago](https://azure.microsoft.com/pricing/details/search/) que dedica recursos utilizados solo por su servicio. Para los niveles de pago, es posible escalar un servicio en dos dimensiones: 

- Agregar réplicas para aumentar su capacidad de administrar cargas de consulta elevadas.   
- Agregar particiones para aumentar el almacenamiento de más documentos. 

Controlar el almacenamiento de documentos y el procesamiento de consultas por separado, con lo que podrá calibrar los recursos en función de los requisitos de producción.

### <a name="step-2-create-index"></a>Paso 2: Creación de índice
Para cargar el contenido en el que quiere realizar búsquedas, primero debe definir un índice de Azure Search. Un índice es como una tabla de base de datos que contiene los datos y puede aceptar consultas de búsqueda. El esquema de índice se define para su asignación y reflejo en la estructura de los documentos que desea buscar, igual que los campos de una base de datos.

Es posible crear un esquema en Azure Portal, o mediante programación con el [SDK de .NET](search-howto-dotnet-sdk.md) o la [API de REST](/rest/api/searchservice/).

### <a name="step-3-load-data"></a>Paso 3: Carga de datos
Una vez definido un índice, estará listo para cargar contenido. Puede usar un modelo de inserción o de extracción.

El modelo de extracción recupera datos de orígenes de datos externos. Se admite mediante *indexadores* que agilizan y automatizan diversos aspectos de la ingesta de datos, como conectarse, leer y serializar datos. Los [indexadores](/rest/api/searchservice/Indexer-operations) están disponibles para Azure Cosmos DB, Azure SQL Database, Azure Blob Storage y SQL Server hospedado en Azure Virtual Machines. Puede configurar un indexador para una actualización de datos programada o a petición.

El modelo de inserción se proporciona a través del SDK o las API de REST usadas para enviar documentos actualizados a un índice. Se pueden insertar datos desde prácticamente cualquier conjunto de datos usando el formato JSON. Consulte [Agregar, actualizar o eliminar documentos](/rest/api/searchservice/addupdate-or-delete-documents) o [Cómo usar Azure Search desde una aplicación .NET](search-howto-dotnet-sdk.md) para obtener instrucciones sobre la carga de datos.

### <a name="step-4-search"></a>Paso 4: Search
Tras completar un índice, puede [generar consultas de búsqueda](search-query-overview.md) para el punto de conexión de servicio mediante solicitudes HTTP sencillas con [API REST](/rest/api/searchservice/Search-Documents) o el [SDK de .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations).

Consulte [Creación de la primera aplicación de búsqueda](tutorial-csharp-create-first-app.md) para crear y, posteriormente, ampliar una página web que recoja los datos que especifique el usuario y controle los resultados. También puede usar llamadas a [Postman para REST interactivo](search-get-started-postman.md) o el [Explorador de búsqueda](search-explorer.md) integrado en Azure Portal para consultar un índice existente.

## <a name="how-it-compares"></a>Comparación

Los clientes a menudo preguntan cuáles son las diferencias de Azure Search con respecto a otras soluciones relacionadas de búsqueda. En la tabla siguiente se resumen las principales diferencias.

| En comparación con | Principales diferencias |
|-------------|-----------------|
|Bing | [Bing Web Search API](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/) busca en los índices de Bing.com aquellos términos que coincidan con lo que envía. Los índices se compilan a partir de HTML, XML y otro tipo de contenido web en sitios públicos. [Bing Custom Search](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/) tiene los mismos cimientos y ofrece la misma tecnología de rastreador (crawler) para tipos de contenido web y cuyo destino son los sitios web individuales.<br/><br/>Azure Search busca un índice que usted defina, completado con datos y documentos de su propiedad, a menudo procedentes de diversas fuentes. Azure Search tiene funcionalidades de rastreo para algunos orígenes de datos a través de [indexadores](search-indexer-overview.md), pero puede insertar cualquier documento JSON que se ajuste a su esquema de índice en un recurso sencillo y consolidado en el que se puedan realizar búsquedas. |
|Búsqueda de bases de datos | Muchas plataformas de base de datos incluyen una experiencia de búsqueda integrada. SQL Server tiene [búsqueda de texto completo](https://docs.microsoft.com/sql/relational-databases/search/full-text-search). Cosmos DB y otras tecnologías similares tienen índices que se pueden consultar. Al evaluar productos que combinan búsqueda y almacenamiento, la elección puede ser complicada. Muchas soluciones usan: DBMS para el almacenamiento y Azure Search para las características de búsqueda especializadas.<br/><br/>En comparación con la búsqueda de DBMS, Azure Search almacena contenido de orígenes heterogéneos y ofrece características de procesamiento de texto especializadas como el procesamiento de texto en función del idioma (lematización o formas de las palabras) en [56 idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support). También admite la autocorrección de palabras mal escritas, [sinónimos](https://docs.microsoft.com/rest/api/searchservice/synonym-map-operations), [sugerencias](https://docs.microsoft.com/rest/api/searchservice/suggestions), [controles de puntuación](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index), [facetas](https://docs.microsoft.com/azure/search/search-filters-facets) y [tokenización personalizada](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search). El [motor de búsqueda de texto completo](search-lucene-query-architecture.md) en Azure Search está compilado en Apache Lucene, un estándar del sector en cuanto a recuperación de información. Aunque Azure Search conserva los datos en forma de índice invertido, rara vez sustituye a un verdadero almacenamiento de datos. Para más información, consulte esta [entrada del foro](https://stackoverflow.com/questions/40101159/can-azure-search-be-used-as-a-primary-database-for-some-data). <br/><br/>El uso de recursos es otro punto de inflexión en esta categoría. Tanto la indexación como algunas operaciones conllevan a menudo muchos cálculos. La descarga de la búsqueda desde DBMS en una solución dedicada en la nube conserva los recursos del sistema para el procesamiento de transacciones. Además, mediante la externalización de la búsqueda se puede ajustar fácilmente la escala para que se adapte al volumen de consultas.|
|Solución de búsqueda dedicada | Suponiendo que se haya decidido por una búsqueda dedicada con la funcionalidad de todo el espectro, al final se realiza una comparación de categorías entre en soluciones locales y un servicio en la nube. Muchas tecnologías de búsqueda ofrecen control sobre la indexación y las canalizaciones de consultas, acceso a una sintaxis de filtrado y consultas más completa, control sobre la clasificación y la relevancia, y características para la búsqueda inteligente y autodirigida. <br/><br/>Un servicio en la nube es la opción adecuada si desea una solución llave en mano con costos generales y un mantenimiento mínimos, y una escala ajustable. <br/><br/>Dentro del paradigma de la nube, varios proveedores ofrecen características de línea base similares, con búsqueda de texto completo, búsqueda geográfica y capacidad de controlar cierto nivel de ambigüedad en entradas de búsqueda. Normalmente, es una [característica especializada](#feature-drilldown) o la facilidad y sencillez general de las API, las herramientas y la administración, lo que determina la solución que mejor se ajusta a nuestras necesidades. |

Entre los proveedores de servicios en la nube, Azure Search es el más potente para cargas de trabajo de búsqueda de texto completo en almacenes de contenido y bases de datos de Azure, y para aplicaciones que se basan principalmente en la búsqueda para navegar por el contenido y recuperar información. 

Las principales ventajas incluyen:

+ Integración de datos de Azure (rastreadores o crawlers) en el nivel de indexación
+ Azure Portal para administración central
+ Escalado, confiabilidad y disponibilidad internacional de Azure
+ Procesamiento mediante IA de datos sin procesar para mejorar las búsquedas, incluido el texto de imágenes, o bien la identificación de patrones en contenido no estructurado.
+ Análisis lingüístico y personalizado, con analizadores para la búsqueda de texto completo en 56 idiomas
+ [Características principales comunes a las aplicaciones centradas en la búsqueda](#feature-drilldown): puntuación, uso de facetas, sugerencias, sinónimos, búsqueda geográfica y muchas más.

> [!Note]
> Los orígenes de datos que no sean de Azure son totalmente compatibles, pero se basan en una metodología de inserción de código más intensiva en lugar de indexadores. Con las API, puede canalizar cualquier colección de documentos JSON hacia un índice de Azure Search.

Entre nuestros clientes, los que son capaces de utilizar la gama más amplia de características de Azure Search pueden usar catálogos en línea, programas de línea de negocio y aplicaciones de detección de documentos.

## <a name="rest-api--net-sdk"></a>API REST |SDK de .NET

Aunque muchas tareas se pueden realizar en el portal, Azure Search está diseñado para desarrolladores que desean integrar la funcionalidad de búsqueda en aplicaciones ya existentes. Las siguientes interfaces de programación están disponibles.

|Plataforma |Descripción |
|-----|------------|
|[REST](/rest/api/searchservice/) | Comandos HTTP admitidos por cualquier plataforma de programación y lenguaje, como Xamarin, Java y JavaScript|
|[SDK de .NET](search-howto-dotnet-sdk.md) | El contenedor de .NET para la API de REST proporciona codificación eficaz en C# y otros lenguajes de código administrado que tienen como destino .NET Framework |

## <a name="free-trial"></a>Evaluación gratuita
Los suscriptores de Azure pueden [aprovisionar un servicio en el nivel Gratis](search-create-service-portal.md).

Si no es un suscriptor, puede [abrir una cuenta de Azure de forma gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Obtenga créditos que puede usar para probar los servicios de Azure de pago. Después de que se agoten los créditos, puede mantener la cuenta y usar los [servicios gratuitos de Azure](https://azure.microsoft.com/free/). No se le realizará ningún cargo en su tarjeta de crédito a menos que cambie explícitamente la configuración y lo solicite.

Opcionalmente, puede [activar las ventajas de suscriptores de MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Su suscripción a MSDN le proporciona créditos todos los meses que puede usar para servicios de Azure de pago. 

## <a name="how-to-get-started"></a>Primeros pasos

1. Cree un [servicio gratis](search-create-service-portal.md). Todas las guías de inicio rápido y los tutoriales se pueden realizar en el servicio gratis.

2. Siga paso a paso el [tutorial sobre el uso de herramientas integradas para la indexación y la realización de consultas](search-get-started-portal.md). Obtenga información sobre los conceptos importantes y familiarícese con la información que proporciona el portal.

3. Continúe con el desarrollo de código mediante .NET o la API REST:

   + El artículo sobre el [uso del SDK de .NET](search-howto-dotnet-sdk.md) muestra el flujo de trabajo principal en código administrado.  
   + El artículo de [introducción a la API de REST](https://github.com/Azure-Samples/search-rest-api-getting-started) muestra los mismos pasos con la API de REST. También puede usar esta guía de inicio rápido para llamar a las API de REST de Postman o Fiddler: [Exploración de las API de REST de Azure Search](search-get-started-postman.md).

## <a name="watch-this-video"></a>Vea este vídeo

Los motores de búsqueda son los controladores habituales de recuperación de información en las aplicaciones móviles, en la web y en almacenes de datos corporativos. Azure Search le proporciona herramientas para crear una experiencia de búsqueda similar a la de los grandes sitios web comerciales.

En este vídeo de 9 minutos del administrador de programas Liam Cavanagh, aprenda cómo puede beneficiar a su aplicación la integración de un motor de búsqueda. Unas breves demostraciones presentan las características clave de Azure Search y el aspecto típico que tiene un flujo de trabajo. 

>[!VIDEO https://channel9.msdn.com/Events/Connect/2016/138/player]
 
+ Los minutos 0 a 3 describen las características y los casos de uso.
+ Los minutos 3 y 4 explican el aprovisionamiento del servicio. 
+ Los minutos 4 a 6 describen el Asistente para la importación de datos utilizado para crear un índice con el conjunto de datos integrados de propiedades inmobiliarias.
+ Los minutos 6 a 9 muestran el Explorador de búsqueda y varias consultas.
