---
title: Envío de notificaciones push a dispositivos iOS concretos mediante Azure Notification Hubs | Microsoft Docs
description: En este tutorial aprenderá a usar Azure Notification Hubs para enviar notificaciones push a dispositivos iOS específicos.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6ead4169-deff-4947-858c-8c6cf03cc3b2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/28/2019
ms.author: jowargo
ms.openlocfilehash: f83afa62859dee5963749daf2555af08cf6a0e0b
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663821"
---
# <a name="tutorial-push-notifications-to-specific-ios-devices-using-azure-notification-hubs"></a>Tutorial: Envío de notificaciones push a dispositivos iOS concretos mediante Azure Notification Hubs

[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>Información general

En este tutorial se explica cómo puede usar Azure Notification Hubs para difundir notificaciones de noticias de última hora en una aplicación iOS. Cuando lo complete, podrá registrar las categorías de noticias de última hora en las que esté interesado y recibir solo notificaciones de inserción para esas categorías. Este escenario es un patrón común para muchas aplicaciones en las que las notificaciones tienen que enviarse a grupos de usuarios que han mostrado previamente interés en ellas, por ejemplo, lectores RSS, aplicaciones para aficionados a la música, etc.

Los escenarios de difusión se habilitan mediante la inclusión de una o más *etiquetas* cuando se crea un registro en el Centro de notificaciones. Cuando las notificaciones se envían a una etiqueta, los dispositivos registrados en la etiqueta reciben la notificación. Puesto que las etiquetas son cadenas simples, no tendrán que aprovisionarse antes. Para información sobre las etiquetas, consulte [Expresiones de etiqueta y enrutamiento de Notification Hubs](notification-hubs-tags-segment-push-message.md).

En este tutorial, realizará los siguientes pasos:

> [!div class="checklist"]
> * Adición de una selección de categorías a la aplicación
> * Envío de notificaciones con etiquetas
> * Envío de notificaciones desde el dispositivo
> * Ejecución de la aplicación y generación de notificaciones

## <a name="prerequisites"></a>Requisitos previos

Este tema se basa en la aplicación que creó en el [Tutorial: Envío de notificaciones push a aplicaciones iOS mediante Azure Notification Hubs][get-started]. Antes de comenzar este tutorial, debe haber completado el [Tutorial: Envío de notificaciones push a aplicaciones iOS mediante Azure Notification Hubs][get-started].

## <a name="add-category-selection-to-the-app"></a>Adición de una selección de categorías a la aplicación

El primer paso es agregar los elementos de la interfaz de usuario al guión gráfico existente que permiten al usuario seleccionar las categorías que se van a registrar. Las categorías seleccionadas por un usuario se almacenan en el dispositivo. Cuando la aplicación se inicia, se crea un registro de dispositivos en el Centro de notificaciones con las categorías seleccionadas como etiquetas.

1. En su **MainStoryboard_iPhone.storyboard**, agregue los siguientes componentes desde la biblioteca de objetos:

   * Una etiqueta con el texto "Breaking News",
   * Etiquetas con los textos de categoría "World", "Politics", "Business", "Technology", "Science", "Sports",
   * Seis modificadores, uno por categoría, establecen que el **estado** de cada modificador sea **Off** (Desactivado) de forma predeterminada.
   * Un botón etiquetado con "Subscribe"

     El guión gráfico debe tener el aspecto siguiente:

     ![Generador de interfaz de Xcode][3]

2. En el editor del asistente, cree medios para todos los modificadores y llámelos "WorldSwitch", "PoliticsSwitch", "BusinessSwitch", "TechnologySwitch", "ScienceSwitch" y "SportsSwitch".
3. Cree una acción para el botón denominada `subscribe`; su `ViewController.h` debe contener el código siguiente:

    ```objc
    @property (weak, nonatomic) IBOutlet UISwitch *WorldSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *PoliticsSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *BusinessSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *TechnologySwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *ScienceSwitch;
    @property (weak, nonatomic) IBOutlet UISwitch *SportsSwitch;

    - (IBAction)subscribe:(id)sender;
    ```

4. Cree una nueva **clase Cocoa Touch** denominada `Notifications`. Copie el siguiente código en la sección de interfaz del archivo Notifications.h:

    ```objc
    @property NSData* deviceToken;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName;

    - (void)storeCategoriesAndSubscribeWithCategories:(NSArray*)categories
                completion:(void (^)(NSError* error))completion;

    - (NSSet*)retrieveCategories;

    - (void)subscribeWithCategories:(NSSet*)categories completion:(void (^)(NSError *))completion;
    ```

5. Agregue la siguiente directiva de importación a Notifications.m:

    ```objc
    #import <WindowsAzureMessaging/WindowsAzureMessaging.h>
    ```

6. Copie el siguiente código en la sección de implementación del archivo Notifications.m:

    ```objc
    SBNotificationHub* hub;

    - (id)initWithConnectionString:(NSString*)listenConnectionString HubName:(NSString*)hubName{

        hub = [[SBNotificationHub alloc] initWithConnectionString:listenConnectionString
                                    notificationHubPath:hubName];

        return self;
    }

    - (void)storeCategoriesAndSubscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];
        [defaults setValue:[categories allObjects] forKey:@"BreakingNewsCategories"];

        [self subscribeWithCategories:categories completion:completion];
    }

    - (NSSet*)retrieveCategories {
        NSUserDefaults* defaults = [NSUserDefaults standardUserDefaults];

        NSArray* categories = [defaults stringArrayForKey:@"BreakingNewsCategories"];

        if (!categories) return [[NSSet alloc] init];
        return [[NSSet alloc] initWithArray:categories];
    }

    - (void)subscribeWithCategories:(NSSet *)categories completion:(void (^)(NSError *))completion
    {
        //[hub registerNativeWithDeviceToken:self.deviceToken tags:categories completion: completion];

        NSString* templateBodyAPNS = @"{\"aps\":{\"alert\":\"$(messageParam)\"}}";

        [hub registerTemplateWithDeviceToken:self.deviceToken name:@"simpleAPNSTemplate" 
            jsonBodyTemplate:templateBodyAPNS expiryTemplate:@"0" tags:categories completion:completion];
    }
    ```

    Esta clase usa el almacenamiento local para almacenar y recuperar las categorías de noticias que este dispositivo recibe. También contiene un método para registrar estas categorías mediante un registro [Plantilla](notification-hubs-templates-cross-platform-push-messages.md) .

7. En el archivo `AppDelegate.h`, agregue una instrucción de importación para `Notifications.h` y agregue una propiedad para una instancia de la clase `Notifications`:

    ```objc
    #import "Notifications.h"

    @property (nonatomic) Notifications* notifications;
    ```

8. En el método `didFinishLaunchingWithOptions` en `AppDelegate.m`, agregue el código para inicializar la instancia de notificaciones al comienzo del método.  
    `HUBNAME` y `HUBLISTENACCESS` (que se definen en `hubinfo.h`) deberían tener ya reemplazados los marcadores de posición `<hub name>` y `<connection string with listen access>` por el nombre del centro de notificaciones y la cadena de conexión de *DefaultListenSharedAccessSignature* que obtuvo anteriormente.

    ```objc
    self.notifications = [[Notifications alloc] initWithConnectionString:HUBLISTENACCESS HubName:HUBNAME];
    ```

    > [!NOTE]
    > Puesto que las credenciales que se distribuyen con una aplicación de cliente no son normalmente seguras, solo debe distribuir la clave para el acceso de escucha con la aplicación cliente. El acceso de escucha permite a la aplicación el registro de notificaciones, pero los registros existentes no pueden modificarse y las notificaciones no se pueden enviar. La clave de acceso completo se usa en un servicio back-end protegido para el envío de notificaciones y el cambio de registros existentes.

9. En el método `didRegisterForRemoteNotificationsWithDeviceToken` en `AppDelegate.m`, reemplace el código en el método por el código siguiente para pasar el token del dispositivo a la clase `notifications`. La clase `notifications` realiza el registro para las notificaciones con las categorías. Si el usuario cambia las selecciones de categoría, llamamos al método `subscribeWithCategories` en respuesta al botón **subscribe** para actualizarlas.

    > [!NOTE]
    > Puesto que el token del dispositivo asignado por el Servicio de notificaciones de inserción de Apple (APNS) puede cambiar en cualquier momento, debe registrar las notificaciones con frecuencia para evitar errores de notificación. En este ejemplo se registra la notificación cada vez que se inicia la aplicación. En las aplicaciones que se ejecutan con frecuencia, más de una vez al día, es posible que pueda omitir el registro para conservar el ancho de banda si pasa menos de un día del registro previo.

    ```objc
    self.notifications.deviceToken = deviceToken;

    // Retrieves the categories from local storage and requests a registration for these categories
    // each time the app starts and performs a registration.

    NSSet* categories = [self.notifications retrieveCategories];
    [self.notifications subscribeWithCategories:categories completion:^(NSError* error) {
        if (error != nil) {
            NSLog(@"Error registering for notifications: %@", error);
        }
    }];
    ```

    En este punto, no debería haber ningún otro código en el método `didRegisterForRemoteNotificationsWithDeviceToken`.

10. Los métodos siguientes ya deben estar presentes en `AppDelegate.m` después de completar el tutorial [Introducción a Notification Hubs][get-started]. De lo contrario, agréguelos.

    ```objc
    - (void)MessageBox:(NSString *)title message:(NSString *)messageText
    {

        UIAlertView *alert = [[UIAlertView alloc] initWithTitle:title message:messageText delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
        [alert show];
    }

    - (void)application:(UIApplication *)application didReceiveRemoteNotification:
       (NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       [self MessageBox:@"Notification" message:[[userInfo objectForKey:@"aps"] valueForKey:@"alert"]];
     }
    ```

    Este método controla las notificaciones recibidas cuando la aplicación está en ejecución mostrando un **UIAlert**sencillo.

11. En `ViewController.m`, agregue una instrucción `import` para `AppDelegate.h` y copie el código siguiente en el método `subscribe` generado por XCode. Este código actualiza el registro de notificación para usar las nuevas etiquetas de categoría que el usuario ha elegido en la interfaz de usuario.

    ```objc
    #import "Notifications.h"

    NSMutableArray* categories = [[NSMutableArray alloc] init];

    if (self.WorldSwitch.isOn) [categories addObject:@"World"];
    if (self.PoliticsSwitch.isOn) [categories addObject:@"Politics"];
    if (self.BusinessSwitch.isOn) [categories addObject:@"Business"];
    if (self.TechnologySwitch.isOn) [categories addObject:@"Technology"];
    if (self.ScienceSwitch.isOn) [categories addObject:@"Science"];
    if (self.SportsSwitch.isOn) [categories addObject:@"Sports"];

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    [notifications storeCategoriesAndSubscribeWithCategories:categories completion: ^(NSError* error) {
        if (!error) {
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:"Notification" message:"Subscribed" delegate:self
            cancelButtonTitle:@"OK" otherButtonTitles: nil];
            [alert show];
        } else {
            NSLog(@"Error subscribing: %@", error);
        }
    }];
    ```

    Este método crea una `NSMutableArray` de categorías y usa la clase `Notifications` para almacenar la lista en el almacenamiento local y registrar las etiquetas correspondientes en el centro de notificaciones. Cuando se modifican las categorías, el registro vuelve a crearse con las nuevas categorías.

12. En `ViewController.m`, agregue el siguiente código en el método `viewDidLoad` para establecer la interfaz de usuario según las categorías previamente guardadas.

    ```objc
    // This updates the UI on startup based on the status of previously saved categories.

    Notifications* notifications = [(AppDelegate*)[[UIApplication sharedApplication]delegate] notifications];

    NSSet* categories = [notifications retrieveCategories];

    if ([categories containsObject:@"World"]) self.WorldSwitch.on = true;
    if ([categories containsObject:@"Politics"]) self.PoliticsSwitch.on = true;
    if ([categories containsObject:@"Business"]) self.BusinessSwitch.on = true;
    if ([categories containsObject:@"Technology"]) self.TechnologySwitch.on = true;
    if ([categories containsObject:@"Science"]) self.ScienceSwitch.on = true;
    if ([categories containsObject:@"Sports"]) self.SportsSwitch.on = true;
    ```

La aplicación ahora almacena un conjunto de categorías en el almacenamiento local del dispositivo usado para registrarse en el Centro de notificaciones cuando se inicia la aplicación. El usuario puede cambiar la selección de categorías en tiempo de ejecución y hacer clic en el método `subscribe` para actualizar el registro para el dispositivo. A continuación, actualiza la aplicación para que envíe notificaciones de noticias de última hora directamente en la propia aplicación.

## <a name="optional-send-tagged-notifications"></a>(Opcional) Envío de notificaciones con etiquetas

Si no tiene acceso a Visual Studio, puede pasar a la siguiente sección y enviar notificaciones desde la propia aplicación. También puede enviar la notificación de plantilla adecuada desde [Azure Portal] mediante la pestaña de depuración para el centro de notificaciones.

[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="optional-send-notifications-from-the-device"></a>(Opcional) Enviar notificaciones desde el dispositivo

Normalmente se pueden enviar notificaciones por un servicio de back-end pero puede enviar notificaciones de última hora directamente desde la aplicación. Para ello, actualice el método `SendNotificationRESTAPI` que se define en el tutorial [Introducción a Notification Hubs][get-started].

1. En `ViewController.m`, actualice el método `SendNotificationRESTAPI` como sigue para que tome un parámetro para la etiqueta de categoría y que envíe una notificación de [plantilla](notification-hubs-templates-cross-platform-push-messages.md) adecuada.

    ```objc
    - (void)SendNotificationRESTAPI:(NSString*)categoryTag
    {
        NSURLSession* session = [NSURLSession sessionWithConfiguration:[NSURLSessionConfiguration
                                    defaultSessionConfiguration] delegate:nil delegateQueue:nil];

        NSString *json;

        // Construct the messages REST endpoint
        NSURL* url = [NSURL URLWithString:[NSString stringWithFormat:@"%@%@/messages/%@", HubEndpoint,
                                            HUBNAME, API_VERSION]];

        // Generated the token to be used in the authorization header.
        NSString* authorizationToken = [self generateSasToken:[url absoluteString]];

        //Create the request to add the template notification message to the hub
        NSMutableURLRequest *request = [NSMutableURLRequest requestWithURL:url];
        [request setHTTPMethod:@"POST"];

        // Add the category as a tag
        [request setValue:categoryTag forHTTPHeaderField:@"ServiceBusNotification-Tags"];

        // Template notification
        json = [NSString stringWithFormat:@"{\"messageParam\":\"Breaking %@ News : %@\"}",
                categoryTag, self.notificationMessage.text];

        // Signify template notification format
        [request setValue:@"template" forHTTPHeaderField:@"ServiceBusNotification-Format"];

        // JSON Content-Type
        [request setValue:@"application/json;charset=utf-8" forHTTPHeaderField:@"Content-Type"];

        //Authenticate the notification message POST request with the SaS token
        [request setValue:authorizationToken forHTTPHeaderField:@"Authorization"];

        //Add the notification message body
        [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];

        // Send the REST request
        NSURLSessionDataTask* dataTask = [session dataTaskWithRequest:request
                    completionHandler:^(NSData *data, NSURLResponse *response, NSError *error)
            {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*) response;
                if (error || httpResponse.statusCode != 200)
                {
                    NSLog(@"\nError status: %d\nError: %@", httpResponse.statusCode, error);
                }
                if (data != NULL)
                {
                    //xmlParser = [[NSXMLParser alloc] initWithData:data];
                    //[xmlParser setDelegate:self];
                    //[xmlParser parse];
                }
            }];

        [dataTask resume];
    }
    ```

2. En `ViewController.m`, actualice la acción `Send Notification`, tal como se muestra en el código siguiente. De esta forma, se envían las notificaciones mediante cada etiqueta individualmente y se envían a varias plataformas.

    ```objc
    - (IBAction)SendNotificationMessage:(id)sender
    {
        self.sendResults.text = @"";

        NSArray* categories = [NSArray arrayWithObjects: @"World", @"Politics", @"Business",
                                @"Technology", @"Science", @"Sports", nil];

        // Lets send the message as breaking news for each category to WNS, FCM, and APNS
        // using a template.
        for(NSString* category in categories)
        {
            [self SendNotificationRESTAPI:category];
        }
    }
    ```

3. Recompile el proyecto y asegúrese de que no haya errores de compilación.

## <a name="run-the-app-and-generate-notifications"></a>Ejecución de la aplicación y generación de notificaciones

1. Presione el botón Ejecutar para compilar el proyecto e iniciar la aplicación. Seleccione algunas opciones de noticias de última hora para suscribirse y después presione el botón **Subscribe** . Debería ver un cuadro de diálogo que indica que las notificaciones se han suscrito.

    ![Ejemplo de notificación en iOS][1]

    Al elegir **Subscribe**, la aplicación convierte las categorías seleccionadas en etiquetas y solicita un nuevo registro de dispositivo para las etiquetas seleccionadas desde el Centro de notificaciones.

2. Escriba un mensaje que se enviará como noticias de última hora y luego presione el botón **Enviar notificación** . También puede ejecutar la aplicación de la consola .NET para generar notificaciones.

    ![Cambio de las preferencias de notificación en iOS][2]

3. Cada dispositivo suscrito a las noticias de última hora recibe las notificaciones de las noticias importantes que acaba de enviar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial se han enviado notificaciones de difusión a los dispositivos iOS concretos a que se han registrado para las categorías. Para aprender a insertar notificaciones en función de la ubicación, pase al tutorial siguiente:

> [!div class="nextstepaction"]
>[Envío de notificaciones push localizadas](notification-hubs-ios-xplat-localized-apns-push-notification.md)

<!-- Images. -->
[1]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-subscribed.png
[2]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios1.png
[3]: ./media/notification-hubs-ios-send-breaking-news/notification-hub-breakingnews-ios2.png

<!-- URLs. -->
[How To: Service Bus Notification Hubs (iOS Apps)]: https://msdn.microsoft.com/library/jj927168.aspx
[Use Notification Hubs to broadcast localized breaking news]: notification-hubs-ios-xplat-localized-apns-push-notification.md
[Mobile Service]: /develop/mobile/tutorials/get-started
[Notify users with Notification Hubs]: notification-hubs-aspnet-backend-ios-notify-users.md
[Notification Hubs Guidance]: https://msdn.microsoft.com/library/dn530749.aspx
[Notification Hubs How-To for iOS]: https://msdn.microsoft.com/library/jj927168.aspx
[get-started]: notification-hubs-ios-apple-push-notification-apns-get-started.md
[Azure Portal]: https://portal.azure.com
