---
title: Registro del usuario actual para notificaciones push mediante una API web | Microsoft Docs
description: Obtenga información acerca de cómo solicitar el registro de notificaciones de inserción en una aplicación iOS con Azure Notification Hubs al realizar el registro por la API web de ASP.NET.
services: notification-hubs
documentationcenter: ios
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 4e3772cf-20db-4b9f-bb74-886adfaaa65d
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: ff77a955c34941d87a1f653726ab3f19e84aa440
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61458355"
---
# <a name="register-the-current-user-for-push-notifications-by-using-aspnet"></a>Registro del usuario actual para las notificaciones de inserción mediante ASP.NET

> [!div class="op_single_selector"]
> * [iOS](notification-hubs-ios-aspnet-register-user-from-backend-to-push-notification.md)

## <a name="overview"></a>Información general

En este tema se describe cómo solicitar el registro de las notificaciones de inserción con Azure Notification Hubs al realizar el registro mediante ASP.NET Web API. Este tema amplía el tutorial [Notificación a los usuarios con Notification Hubs]. Debe haber completado ya los pasos necesarios de ese tutorial para crear el servicio móvil autenticado. Para obtener más información acerca del escenario de notificación a los usuarios, consulte [Notificación a los usuarios con Notification Hubs].

## <a name="update-your-app"></a>Actualización de la aplicación

1. En su MainStoryboard_iPhone.storyboard, agregue los siguientes componentes desde la biblioteca de objetos:

   * **Etiqueta**: "Push to User with Notification Hubs"
   * **Etiqueta**: "InstallationId"
   * **Etiqueta**: "User"
   * **Campo de texto**: "User"
   * **Etiqueta**: "Password"
   * **Campo de texto**: "Password"
   * **Botón**: "Login"

     En este punto, su storyboard debe presentar la siguiente apariencia:

     ![][0]

2. En el editor del asistente, cree salidas para todos los controles cambiados y llámelos, conecte los campos de texto con el controlador de vista (delegado) y cree una **acción** para el botón de **inicio de sesión**.

    ![][1]

    El archivo BreakingNewsViewController.h ahora debe contener el siguiente código:

    ```objc
    @property (weak, nonatomic) IBOutlet UILabel *installationId;
    @property (weak, nonatomic) IBOutlet UITextField *User;
    @property (weak, nonatomic) IBOutlet UITextField *Password;

    - (IBAction)login:(id)sender;
    ```
3. Cree una clase llamada `DeviceInfo` y copie el siguiente código en la sección de la interfaz del archivo DeviceInfo.h:

    ```objc
    @property (readonly, nonatomic) NSString* installationId;
    @property (nonatomic) NSData* deviceToken;
    ```
4. Copie el siguiente código en la sección de implementación del archivo DeviceInfo.m:

    ```objc
    @synthesize installationId = _installationId;

    - (id)init {
        if (!(self = [super init]))
            return nil;

        NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
        _installationId = [defaults stringForKey:@"PushToUserInstallationId"];
        if(!_installationId) {
            CFUUIDRef newUUID = CFUUIDCreate(kCFAllocatorDefault);
            _installationId = (__bridge_transfer NSString *)CFUUIDCreateString(kCFAllocatorDefault, newUUID);
            CFRelease(newUUID);

            //store the install ID so we don't generate a new one next time
            [defaults setObject:_installationId forKey:@"PushToUserInstallationId"];
            [defaults synchronize];
        }

        return self;
    }

    - (NSString*)getDeviceTokenInHex {
        const unsigned *tokenBytes = [[self deviceToken] bytes];
        NSString *hexToken = [NSString stringWithFormat:@"%08X%08X%08X%08X%08X%08X%08X%08X",
                                ntohl(tokenBytes[0]), ntohl(tokenBytes[1]), ntohl(tokenBytes[2]),
                                ntohl(tokenBytes[3]), ntohl(tokenBytes[4]), ntohl(tokenBytes[5]),
                                ntohl(tokenBytes[6]), ntohl(tokenBytes[7])];
        return hexToken;
    }
    ```

5. En PushToUserAppDelegate.h, agregue la siguiente propiedad singleton:

    ```objc
    @property (strong, nonatomic) DeviceInfo* deviceInfo;
    ```
6. En el método `didFinishLaunchingWithOptions` en PushToUserAppDelegate.m, agregue el siguiente código:

    ```objc
    self.deviceInfo = [[DeviceInfo alloc] init];

    [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
    ```

    La primera línea inicializa el singleton `DeviceInfo`. La segunda línea inicia el registro de notificaciones de inserción, que ya existe si ya ha completado el tutorial [Introducción a Notification Hubs].
7. En PushToUserAppDelegate.m, implemente el método `didRegisterForRemoteNotificationsWithDeviceToken` en su AppDelegate y agregue el siguiente código:

    ```objc
    self.deviceInfo.deviceToken = deviceToken;
    ```

    De este modo se configura el token del dispositivo para la solicitud.

   > [!NOTE]
   > En este punto, no debería haber ningún otro código en este método. Si ya tiene una llamada al método `registerNativeWithDeviceToken` que se agregó cuando realizó el tutorial [Introducción a Notification Hubs](notification-hubs-ios-apple-push-notification-apns-get-started.md), debe convertir la llamada en comentario o quitarla.

8. En el archivo `PushToUserAppDelegate.m`, agregue el siguiente método de controlador:

    ```objc
    * (void) application:(UIApplication *) application didReceiveRemoteNotification:(NSDictionary *)userInfo {
       NSLog(@"%@", userInfo);
       UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
                             [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
                             @"OK" otherButtonTitles:nil, nil];
       [alert show];
    }
    ```

    Este método muestra una alerta en la interfaz de usuario cuando su aplicación recibe notificaciones durante su ejecución.

9. Abra el archivo `PushToUserViewController.m` y escriba la siguiente implementación:

    ```objc
    - (BOOL)textFieldShouldReturn:(UITextField *)theTextField {
        if (theTextField == self.User || theTextField == self.Password) {
            [theTextField resignFirstResponder];
        }
        return YES;
    }
    ```

10. En el método `viewDidLoad` del archivo `PushToUserViewController.m`, inicialice la etiqueta `installationId` como sigue:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];
    Self.installationId.text = deviceInfo.installationId;
    ```

11. Agregue las siguientes propiedades en la interfaz en `PushToUserViewController.m`:

    ```objc
    @property (readonly) NSOperationQueue* downloadQueue;
    - (NSString*)base64forData:(NSData*)theData;
    ```

12. Luego, agregue la siguiente implementación:

    ```objc
    - (NSOperationQueue *)downloadQueue {
        if (!_downloadQueue) {
            _downloadQueue = [[NSOperationQueue alloc] init];
            _downloadQueue.name = @"Download Queue";
            _downloadQueue.maxConcurrentOperationCount = 1;
        }
        return _downloadQueue;
    }

    // base64 encoding
    - (NSString*)base64forData:(NSData*)theData
    {
        const uint8_t* input = (const uint8_t*)[theData bytes];
        NSInteger length = [theData length];

        static char table[] = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/=";

        NSMutableData* data = [NSMutableData dataWithLength:((length + 2) / 3) * 4];
        uint8_t* output = (uint8_t*)data.mutableBytes;

        NSInteger i;
        for (i=0; i < length; i += 3) {
            NSInteger value = 0;
            NSInteger j;
            for (j = i; j < (i + 3); j++) {
                value <<= 8;

                if (j < length) {
                    value |= (0xFF & input[j]);
                }
            }

            NSInteger theIndex = (i / 3) * 4;
            output[theIndex + 0] =                    table[(value >> 18) & 0x3F];
            output[theIndex + 1] =                    table[(value >> 12) & 0x3F];
            output[theIndex + 2] = (i + 1) < length ? table[(value >> 6)  & 0x3F] : '=';
            output[theIndex + 3] = (i + 2) < length ? table[(value >> 0)  & 0x3F] : '=';
        }

        return [[NSString alloc] initWithData:data encoding:NSASCIIStringEncoding];
    }
    ```

13. Copie el siguiente código en el método controlador `login` creado por XCode:

    ```objc
    DeviceInfo* deviceInfo = [(PushToUserAppDelegate*)[[UIApplication sharedApplication]delegate] deviceInfo];

    // build JSON
    NSString* json = [NSString stringWithFormat:@"{\"platform\":\"ios\", \"instId\":\"%@\", \"deviceToken\":\"%@\"}", deviceInfo.installationId, [deviceInfo getDeviceTokenInHex]];

    // build auth string
    NSString* authString = [NSString stringWithFormat:@"%@:%@", self.User.text, self.Password.text];

    NSMutableURLRequest* request = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"http://nhnotifyuser.azurewebsites.net/api/register"]];
    [request setHTTPMethod:@"POST"];
    [request setHTTPBody:[json dataUsingEncoding:NSUTF8StringEncoding]];
    [request addValue:[@([json lengthOfBytesUsingEncoding:NSUTF8StringEncoding]) description] forHTTPHeaderField:@"Content-Length"];
    [request addValue:@"application/json" forHTTPHeaderField:@"Content-Type"];
    [request addValue:[NSString stringWithFormat:@"Basic %@",[self base64forData:[authString dataUsingEncoding:NSUTF8StringEncoding]]] forHTTPHeaderField:@"Authorization"];

    // connect with POST
    [NSURLConnection sendAsynchronousRequest:request queue:[self downloadQueue] completionHandler:^(NSURLResponse* response, NSData* data, NSError* error) {
        // add UIAlert depending on response.
        if (error != nil) {
            NSHTTPURLResponse* httpResponse = (NSHTTPURLResponse*)response;
            if ([httpResponse statusCode] == 200) {
                UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Back-end registration" message:@"Registration successful" delegate:nil cancelButtonTitle: @"OK" otherButtonTitles:nil, nil];
                [alert show];
            } else {
                NSLog(@"status: %ld", (long)[httpResponse statusCode]);
            }
        } else {
            NSLog(@"error: %@", error);
        }
    }];
    ```

    Este método obtiene un ID de instalación y un canal para las notificaciones de inserción y los envía, junto con el tipo de dispositivo, al método de Web API autenticado que crea un registro en Notification Hubs. Esta API web se definió en [Notificación a los usuarios con Notification Hubs].

Ahora que la aplicación de cliente se ha actualizado, regrese a [Notificación a los usuarios con Notification Hubs] y actualice el servicio móvil para enviar notificaciones mediante Notification Hubs.

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios1.png
[1]: ./media/notification-hubs-ios-aspnet-register-user-push-notifications/notification-hub-user-aspnet-ios2.png

<!-- URLs. -->
[Notificación a los usuarios con Notification Hubs]: notification-hubs-aspnet-backend-ios-apple-apns-notification.md
[Introducción a Notification Hubs]: notification-hubs-ios-apple-push-notification-apns-get-started.md
