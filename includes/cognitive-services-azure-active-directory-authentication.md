---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 07/23/2019
ms.openlocfilehash: f8d6e5de7f907ae78958b8c239649f55257bf7f2
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467509"
---
## <a name="authenticate-with-azure-active-directory"></a>Autenticación mediante Azure Active Directory

> [!IMPORTANT]
> Actualmente, **solo** las API Computer Vision, API Face, API Text Analytics y Lector inmersivo admiten la autenticación con Azure Active Directory (AAD).

En las secciones anteriores, le mostramos cómo realizar la autenticación en Azure Cognitive Services mediante una clave de suscripción de un solo servicio o de varios servicios. Aunque estas claves proporcionan un camino rápido y fácil para iniciar el desarrollo, se quedan cortas en escenarios más complejos que requieren controles de acceso basados en rol. Echemos un vistazo a lo que se necesita para autenticarse con Azure Active Directory (AAD).

En las secciones siguientes, usará el entorno de Azure Cloud Shell o la CLI de Azure para crear un subdominio, asignar roles y obtener un token de portador para llamar a Cognitive Services de Azure. Si se bloquea, se proporcionan vínculos en cada sección con todas las opciones disponibles para cada comando de Azure Cloud Shell o la CLI de Azure.

### <a name="create-a-resource-with-a-custom-subdomain"></a>Creación de un recurso con un subdominio personalizado

El primer paso es crear un subdominio personalizado.

1. Para empezar, abra Azure Cloud Shell, después [seleccione una suscripción](https://docs.microsoft.com/powershell/module/servicemanagement/azure/select-azuresubscription?view=azuresmps-4.0.0#description):

   ```azurecli-interactive
   Select-AzureSubscription -SubscriptionName <YOUR_SUBCRIPTION>
   ```

2. A continuación, [cree un recurso de Cognitive Services](https://docs.microsoft.com/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount?view=azps-1.8.0) con un subdominio personalizado. El nombre del subdominio debe ser único globalmente y no puede incluir caracteres especiales, como: ".", "!", ",".

   ```azurecli-interactive
   New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. Si se realiza correctamente, el **punto de conexión** debe mostrar el nombre de subdominio único del recurso.


### <a name="assign-a-role-to-a-service-principal"></a>Asignación de un rol a una entidad de servicio

Ahora que tiene un subdominio personalizado asociado al recurso, deberá asignar un rol a una entidad de servicio.

> [!NOTE]
> Tenga en cuenta que las asignaciones de roles de AAD pueden tardar hasta cinco minutos en propagarse.

1. En primer lugar, vamos a registrar una [aplicación de AAD](https://docs.microsoft.com/powershell/module/Az.Resources/New-AzADApplication?view=azps-1.8.0).

   ```azurecli-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   Va a necesitar el valor de **ApplicationID** en el paso siguiente.

2. Después, tiene que [crear una entidad de servicio](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal?view=azps-1.8.0) para la aplicación de AAD.

   ```azurecli-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Si registra una aplicación en Azure Portal, este paso se completa automáticamente.

3. El último paso es [asignar el rol "Usuario de Cognitive Services"](https://docs.microsoft.com/powershell/module/az.Resources/New-azRoleAssignment?view=azps-1.8.0) a la entidad de servicio (en el ámbito del recurso). Mediante la asignación de un rol, concede acceso a la entidad de servicio a este recurso. Puede conceder a la misma entidad de servicio acceso a varios recursos de la suscripción.
   >[!NOTE]
   > Se utiliza el valor de ObjectId de la entidad de servicio, no el valor de ObjectId de la aplicación.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>Solicitud de ejemplo

En este ejemplo, se usa una contraseña para autenticar la entidad de servicio. El token proporcionado se usa para llamar a la API Computer Vision.

1. Obtenga el **TenantId**:
   ```azurecli-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. Obtenga un token:
   ```azurecli-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $password
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
3. Llame a la API Computer Vision:
   ```azurecli-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

Como alternativa, la entidad de servicio se puede autenticar con un certificado. Además de la entidad de servicio, la entidad de seguridad de usuario también se admite si tiene permisos delegados a través de otra aplicación de AAD. En este caso, en lugar de contraseñas o certificados, se les pedirá a los usuarios la autenticación en dos fases al adquirir el token.
