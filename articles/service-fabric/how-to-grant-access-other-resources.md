---
title: 'Azure Service Fabric: concesión de acceso a otros recursos de Azure para una aplicación de Service Fabric | Microsoft Docs'
description: En este artículo se explica el proceso para que una aplicación de Service Fabric habilitada para identidades administradas tenga acceso a otros recursos de Azure que admiten la autenticación basada en Azure Active Directory.
services: service-fabric
author: athinanthny
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.date: 08/08/2019
ms.author: atsenthi
ms.openlocfilehash: 07b26fb86392b26ef45c4370741a32efc7dc436b
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640927"
---
# <a name="granting-a-service-fabric-applications-managed-identity-access-to-azure-resources-preview"></a>Concesión de acceso a recursos de Azure para la identidad administrada de una aplicación de Service Fabric (versión preliminar)

Para que la aplicación pueda usar su identidad administrada a fin de acceder a otros recursos, es necesario conceder permisos a dicha identidad en el recurso de Azure protegido al que se va a acceder. La concesión de permisos suele ser una acción de administración en el "plano de control" del servicio de Azure propietario del recurso protegido enrutado a través de Azure Resource Manager, que aplicará cualquier comprobación aplicable de acceso basado en roles.

La secuencia exacta de pasos dependerá del tipo de recurso de Azure al que se acceda, así como del lenguaje o del cliente que se use para conceder permisos. En el resto del artículo se da por hecho que se ha asignado a la aplicación una identidad asignada por el usuario y se incluyen varios ejemplos típicos para mayor comodidad, pero esto no constituye una referencia exhaustiva sobre el tema. Consulte la documentación de los servicios de Azure correspondientes para obtener instrucciones actualizadas sobre la concesión de permisos.  

## <a name="granting-access-to-azure-storage"></a>Concesión de acceso a Azure Storage
Puede usar la identidad administrada de la aplicación de Service Fabric (en este caso, asignada por el usuario) para recuperar los datos de un blob de Azure Storage. Conceda a la identidad los permisos necesarios en Azure Portal mediante los pasos siguientes:

1. Vaya a la cuenta de almacenamiento.
2. Haga clic en el vínculo Control de acceso (IAM) en el panel izquierdo.
3. (Opcional) Compruebe el acceso existente. Seleccione la identidad administrada asignada por el usuario o por el sistema en el control "Buscar" y elija la identidad adecuada en la lista de resultados que aparece.
4. Haga clic en + Agregar asignación de rol en la parte superior de la página para agregar una asignación de roles nueva para la identidad de la aplicación.
En la lista desplegable de Rol, seleccione Lector de datos de Storage Blob.
5. En la lista desplegable siguiente, en Asignar acceso a, elija `User assigned managed identity`.
6. Después, asegúrese de que la suscripción adecuada aparece en el menú desplegable Suscripción y establezca Grupo de recursos en Todos los grupos de recursos.
7. En Seleccionar, elija la identidad asignada por el usuario correspondiente a la aplicación de Service Fabric y haga clic en Guardar.

La compatibilidad con identidades administradas de Service Fabric asignadas por el sistema no incluye la integración en Azure Portal. Si la aplicación usa una identidad asignada por el sistema, primero tendrá que buscar el identificador de cliente de la identidad de la aplicación y, luego, repetir los pasos anteriores, pero seleccionando la opción `Azure AD user, group, or service principal` en el control Buscar.

## <a name="granting-access-to-azure-key-vault"></a>Concesión de acceso a Azure Key Vault
De forma similar al proceso para acceder al almacenamiento, puede aprovechar la identidad administrada de una aplicación de Service Fabric para acceder a una instancia de Azure Key Vault. Los pasos para conceder acceso en Azure Portal son semejantes a los indicados anteriormente y no se repetirán aquí. Consulte la imagen siguiente para ver las diferencias.

![Directiva de acceso de Key Vault](../key-vault/media/vs-secure-secret-appsettings/add-keyvault-access-policy.png)

En el ejemplo siguiente se muestra cómo conceder acceso a un almacén mediante una implementación de plantilla. Agregue el fragmento de código siguiente como otra entrada bajo el elemento `resources` de la plantilla.

```json
    {
        "type": "Microsoft.KeyVault/vaults/accessPolicies",
        "name": "[concat(parameters('keyVaultName'), '/add')]",
        "apiVersion": "2018-02-14",
        "properties": {
            "accessPolicies": [
                {
                    "tenantId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').tenantId]",
                    "objectId": "[reference(variables('userAssignedIdentityResourceId'), '2018-11-30').principalId]",
                    "dependsOn": [
                        "[variables('userAssignedIdentityResourceId')]"
                    ],
                    "permissions": {
                        "keys":         ["get", "list"],
                        "secrets":      ["get", "list"],
                        "certificates": ["get", "list"]
                    }
                }
            ]
        }
    },
```

Para obtener más información, consulte [Almacenes: actualización de la directiva de acceso](https://docs.microsoft.com/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una aplicación de Azure Service Fabric con una identidad administrada asignada por el sistema](./how-to-deploy-service-fabric-application-system-assigned-managed-identity.md)

* [Implementación de una aplicación de Azure Service Fabric con una identidad administrada asignada por el usuario](./how-to-deploy-service-fabric-application-user-assigned-managed-identity.md)