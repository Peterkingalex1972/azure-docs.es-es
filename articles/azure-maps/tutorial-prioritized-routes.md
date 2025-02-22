---
title: Rutas múltiples con Azure Maps | Microsoft Docs
description: Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Maps
author: walsehgal
ms.author: v-musehg
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: ad67b17d76e811d5977955c40f444c4b7c0a01e3
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68478845"
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Maps

En este tutorial se muestra cómo usar la cuenta de Azure Maps y servicio de rutas para buscar la ruta al punto de interés, con una prioridad establecida según el modo de desplazamiento. Se muestran dos rutas distintas en el mapa, una para automóviles y otra para camiones que podrían tener restricciones de ruta debido a la altura, el peso o una carga peligrosa. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una nueva página web con Map Control API
> * Visualizar el flujo de tráfico en el mapa
> * Crear consultas de ruta que declaran el modo de desplazamiento
> * Mostrar varias rutas en el mapa

## <a name="prerequisites"></a>Requisitos previos

Antes de continuar, siga los pasos del primer tutorial para la [Creación de una cuenta de Azure Maps](./tutorial-search-location.md#createaccount) y la [Obtención de la clave de suscripción de la cuenta](./tutorial-search-location.md#getkey).

## <a name="create-a-new-map"></a>Creación de un nuevo mapa

En los pasos siguientes se muestra cómo crear una página HTML estática insertada con Map Control API.

1. En la máquina local, cree un nuevo archivo y asígnele el nombre **MapTruckRoute.html**.
2. Agregue los siguientes componentes HTML al archivo:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <title>Map Route</title>
        <meta charset="utf-8">
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

        <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css">
        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>

        <!-- Add a reference to the Azure Maps Services Module JavaScript file. -->
        <script src="https://atlas.microsoft.com/sdk/javascript/service/2/atlas-service.min.js"></script>

        <script>
            var map, datasource, client;

            function GetMap() {
                //Add Map Control JavaScript code here.
            }
        </script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #myMap {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    <body onload="GetMap()">
        <div id="myMap"></div>
    </body>
    </html>
    ```

    Observe que el encabezado HTML incluye los archivos de recursos CSS y JavaScript hospedados por la biblioteca de Control de mapa de Azure. Observe el evento `onload` en el cuerpo de la página, el cual llamará a la función `GetMap` cuando el cuerpo de la página se haya cargado. Esta función contendrá el código JavaScript insertado para acceder a las API de Azure Maps.

3. Agregue el siguiente código JavaScript a la función `GetMap`. Reemplace la cadena `<Your Azure Maps Key>` por la clave principal que copió de la cuenta de Maps.

    ```JavaScript
    //Instantiate a map object
    var map = new atlas.Map("myMap", {
        //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
        authOptions: {
            authType: 'subscriptionKey',
            subscriptionKey: '<Your Azure Maps Key>'
        }
    });
    ```

    La clase `atlas.Map` proporciona el control para un mapa web visual e interactivo, y es un componente de la API de Control de mapa de Azure.

4. Guarde el archivo y ábralo en el explorador. En este punto, tiene un mapa básico que puede desarrollar aún más.

   ![Visualización del mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar el flujo de tráfico

1. Agregue la visualización del flujo de tráfico al mapa. El evento `ready` de los mapas espera hasta que los recursos de los mapas se han cargado y están listos para interactuar de forma segura con ellos.

    ```javascript
    map.events.add("ready", function() {
        // Add Traffic Flow to the Map
        map.setTraffic({
            flow: "relative"
        });
    });
    ```

    En el controlador de eventos `ready` del mapa, la configuración del flujo de tráfico en el mapa está establecida en `relative`, que es la velocidad de la carretera relativa a la libre circulación. También podría establecerlo en velocidad `absolute` de la carretera o `relative-delay`, que muestra la velocidad relativa en el punto en que varía con respecto a la libre circulación.

2. Guarde el archivo **MapTruckRoute.html** y actualice la página en el explorador. Si interactúa con el mapa y lo acerca para centrarse en Los Ángeles, debería ver las calles con los datos de tráfico actuales.

   ![Visualización del mapa del tráfico](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="define-how-the-route-will-be-rendered"></a>Definición de cómo se representará la ruta

En este tutorial, se calcularán y se representarán dos rutas en el mapa. Una ruta con carreteras accesibles para los coches y otra accesible para camiones. Cuando se representen, aparecerá un icono de inicio y otro de fin de ruta, y diferentes líneas de colores para cada ruta.

1. Después de inicializar el mapa, agregue el siguiente código JavaScript en el controlador de eventos `ready` de los mapas.

    ```JavaScript
    //Wait until the map resources have fully loaded.
    map.events.add('ready', function () {

        //Create a data source and add it to the map.
        datasource = new atlas.source.DataSource();
        map.sources.add(datasource);

        //Add a layer for rendering the route lines and have it render under the map labels.
        map.layers.add(new atlas.layer.LineLayer(datasource, null, {
            strokeColor: ['get', 'strokeColor'],
            strokeWidth: ['get', 'strokeWidth'],
            lineJoin: 'round',
            lineCap: 'round'
        }), 'labels');

        //Add a layer for rendering point data.
        map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
            iconOptions: {
                image: ['get', 'icon'],
                allowOverlap: true
            },
            textOptions: {
                textField: ['get', 'title'],
                offset: [0, 1.2]
            },
            filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']] //Only render Point or MultiPoints in this layer.
        }));
    });
    ```
    
    En el controlador de eventos `ready` de los mapas, se crea un origen de datos para almacenar las líneas de las rutas, así como los puntos inicial y final. Se crea una capa de línea y se asocia al origen de datos para definir cómo se representará la línea de la ruta. Las expresiones se usan para recuperar la anchura y el color de línea desde las propiedades de la característica de línea de ruta. Al agregar la capa al mapa, se pasa un segundo parámetro con el valor `'labels'` que especifica que esta capa se debe representar por debajo de las etiquetas del mapa. Esto garantiza que la línea de ruta no cubrirá las etiquetas de la carretera. Se crea una capa de símbolos y se asocia al origen de datos. Esta capa especifica cómo se representarán los puntos inicial y final. En este caso, se han agregado expresiones para recuperar la imagen del icono y la información de la etiqueta de texto a partir de las propiedades de cada objeto de punto. 
    
2. Para este tutorial, establezca el punto inicial como una compañía ficticia en Seattle llamada Fabrikam y el punto de destino como una oficina de Microsoft. En el controlador de eventos `ready` de los mapas, agregue el código siguiente.

    ```JavaScript
    //Create the GeoJSON objects which represent the start and end point of the route.
    var startPoint = new atlas.data.Feature(new atlas.data.Point([-122.356099, 47.580045]), {
        title: 'Fabrikam, Inc.',
        icon: 'pin-blue'
    });

    var endPoint = new atlas.data.Feature(new atlas.data.Point([-122.201164, 47.616940]), {
        title: 'Microsoft - Lincoln Square',
        icon: 'pin-round-blue'
    });
    ```

    Este código crea dos [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar los puntos inicial y final de la ruta. Se agrega una propiedad `title` y `icon` a cada punto.

3. A continuación, agregue el siguiente código JavaScript para agregar los marcadores de los puntos inicial y final al mapa:

    ```JavaScript
    //Add the data to the data source.
    datasource.add([startPoint, endPoint]);

    //Fit the map window to the bounding box defined by the start and end positions.
    map.setCamera({
        bounds: atlas.data.BoundingBox.fromData([startPoint, endPoint]),
        padding: 100
    });
    ```

    Los puntos inicial y final se agregan al origen de datos. El rectángulo delimitador de los puntos inicial y final se calcula utilizando la función `atlas.data.BoundingBox.fromData`. Este rectángulo delimitador se usa para establecer la vista de las cámaras del mapa sobre la ruta completa mediante la función `map.setCamera`. Se agrega un relleno para compensar las dimensiones de píxeles de los iconos de símbolos.

4. Guarde el archivo y actualice el explorador para ver los anclajes en el mapa. Ahora el mapa se centra en Seattle, y puede ver como la chincheta azul redonda marca el punto de inicio y otra chincheta azul el punto de fin.

   ![Visualización del mapa con los puntos de inicio y fin](./media/tutorial-prioritized-routes/pins-map.png)

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Representar las rutas con una prioridad establecida según el modo de desplazamiento

En esta sección se explica cómo usar Route Service API de Maps para buscar varias rutas desde un punto inicial determinado hasta un punto final, según el modo de transporte. El servicio de rutas proporciona varias API para planear la ruta *más rápida*, *más corta*, *más económica* o *más emocionante* entre dos ubicaciones, teniendo en cuenta las condiciones del tráfico en tiempo real. También permite a los usuarios planear rutas futuras mediante el uso de bases de datos que contienen un historial amplio del tráfico y la predicción de duraciones de una ruta en cualquier día y a cualquier hora. Para más información, consulte [Direcciones de GetRoute](https://docs.microsoft.com/rest/api/maps/route/getroutedirections). Todos los bloques de código siguientes se deben agregar **dentro del elemento eventListener de carga de mapas** para asegurarse de que se cargan después de que el mapa se carga completamente.

1. En la función GetMap, agregue lo siguiente al código JavaScript.

    ```JavaScript
    // Use SubscriptionKeyCredential with a subscription key
    var subscriptionKeyCredential = new atlas.service.SubscriptionKeyCredential(atlas.getSubscriptionKey());

    // Use subscriptionKeyCredential to create a pipeline
    var pipeline = atlas.service.MapsURL.newPipeline(subscriptionKeyCredential);

    // Construct the RouteURL object
    var routeURL = new atlas.service.RouteURL(pipeline);
    ```

   `SubscriptionKeyCredential` crea un `SubscriptionKeyCredentialPolicy` para autenticar las solicitudes HTTP en Azure Maps con la clave de suscripción. `atlas.service.MapsURL.newPipeline()` toma la directiva `SubscriptionKeyCredential` y crea una instancia de [canalización](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.pipeline?view=azure-maps-typescript-latest). `routeURL` representa una dirección URL para las operaciones [Route](https://docs.microsoft.com/rest/api/maps/route) de Azure Maps.

2. Después de configurar las credenciales y la dirección URL, agregue el siguiente código JavaScript para construir una ruta desde el punto inicial al final para un camión que transporta mercancía clasificada como USHazmatClass2 y mostrar los resultados.

    ```JavaScript
    //Start and end point input to the routeURL
    var coordinates= [[startPoint.geometry.coordinates[0], startPoint.geometry.coordinates[1]], [endPoint.geometry.coordinates[0], endPoint.geometry.coordinates[1]]];

    //Make a search route request for a truck vehicle type
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates,{
        travelMode: 'truck',
        vehicleWidth: 2,
        vehicleHeight: 2,
        vehicleLength: 5,
        vehicleLoadType: 'USHazmatClass2'
    }).then((directions) => {
        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#2272B9';
        routeLine.properties.strokeWidth = 9;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.
        datasource.add(routeLine, 0);
    });
    ```

    Este fragmento de código anterior consulta el servicio de enrutamiento de Azure Maps con el método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). A continuación, se extrae la línea de ruta de la colección de características de GeoJSON de la respuesta extraída con el método `geojson.getFeatures()`. La línea de ruta se agrega entonces al origen de datos. También agrega un índice de 0 para asegurarse de que se representa antes que otras líneas en el origen de datos. Esto se hace ya que, a menudo, el cálculo de la ruta del camión será más lento que el de la ruta de un coche, y si la línea de ruta del camión se agrega al origen de datos después de la ruta del coche, esta se representará por encima. Se agregan dos propiedades a la línea de ruta del camión, una pincelada con un agradable tono azul y una pincelada de nueve píxeles de ancho.

3. Agregue el siguiente código JavaScript para construir una ruta para un automóvil y mostrar los resultados.

    ```JavaScript
    routeURL.calculateRouteDirections(atlas.service.Aborter.timeout(10000), coordinates).then((directions) => {

        //Get data features from response
        var data = directions.geojson.getFeatures();

        //Get the route line and add some style properties to it.  
        var routeLine = data.features[0];
        routeLine.properties.strokeColor = '#B76DAB';
        routeLine.properties.strokeWidth = 5;

        //Add the route line to the data source. We want this to render below the car route which will likely be added to the data source faster, so insert it at index 0.  
        datasource.add(routeLine);
    });
    ```

    Este fragmento de código anterior consulta el servicio de enrutamiento de Azure Maps con el método [getRouteDirections](https://docs.microsoft.com/javascript/api/azure-maps-rest/atlas.service.models.routedirectionsrequestbody?view=azure-maps-typescript-latest). A continuación, se extrae la línea de ruta de la colección de características de GeoJSON de la respuesta extraída con el método `geojson.getFeatures()`. La línea de ruta se agrega entonces al origen de datos. Se agregan dos propiedades a la línea de ruta del coche, una pincelada con un tono morado y una pincelada de cinco píxeles de ancho.  

4. Guarde el archivo **MapTruckRoute.html** y actualice el explorador para observar el resultado. Para una conexión correcta con las API de Maps, debe ver un mapa similar al siguiente.

    ![Rutas con prioridad establecida con Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    La ruta para camión es azul y más gruesa, mientras que la ruta para automóvil es púrpura y más estrecha. La ruta para automóvil transcurre a través de Lake Washington por la I-90, que pasa por túneles en áreas residenciales y por lo que se restringe la carga de residuos peligrosos. La ruta para camión, que especifica un tipo de carga USHazmatClass2, se dirige correctamente para usar una autopista diferente.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una nueva página web con Map Control API
> * Visualizar el flujo de tráfico en el mapa
> * Crear consultas de ruta que declaran el modo de desplazamiento
> * Mostrar varias rutas en el mapa

> [!div class="nextstepaction"]
> [Ver el código fuente completo](https://github.com/Azure-Samples/AzureMapsCodeSamples/blob/master/AzureMapsCodeSamples/Tutorials/truckRoute.html)

> [!div class="nextstepaction"]
> [Ver un ejemplo publicado](https://azuremapscodesamples.azurewebsites.net/?sample=Multiple%20routes%20by%20mode%20of%20travel)

En el siguiente tutorial se muestra el proceso de creación de un localizador de almacenamiento sencillo mediante Azure Maps.

> [!div class="nextstepaction"]
> [Creación de un localizador de almacén mediante Azure Maps](./tutorial-create-store-locator.md)

> [!div class="nextstepaction"]
> [Use data-driven style expressions](data-driven-style-expressions-web-sdk.md) (Uso de expresiones de estilo controladas por datos)