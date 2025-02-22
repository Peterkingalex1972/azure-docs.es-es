---
title: 'Creación de una SAS de delegación de usuarios para un contenedor o blob con la CLI de Azure (versión preliminar): Azure Storage'
description: Obtenga información sobre cómo crear una SAS de delegación de usuarios con credenciales de Azure Active Directory en Azure Storage mediante la CLI de Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/12/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: blobs
ms.openlocfilehash: ef51a1b130323a8799d5334d8d043fda08fcc7ef
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69896959"
---
# <a name="create-a-user-delegation-sas-for-a-container-or-blob-with-the-azure-cli-preview"></a>Creación de una SAS de delegación de usuarios para un contenedor o blob con la CLI de Azure (versión preliminar)

[!INCLUDE [storage-auth-sas-intro-include](../../../includes/storage-auth-sas-intro-include.md)]

En este artículo se muestra cómo usar las credenciales de Azure Active Directory (Azure AD) para crear una SAS de delegación de usuarios para un contenedor o blob con la CLI de Azure (versión preliminar).

[!INCLUDE [storage-auth-user-delegation-include](../../../includes/storage-auth-user-delegation-include.md)]

## <a name="install-the-latest-version-of-the-azure-cli"></a>Instalar la versión más reciente de la CLI de Azure

Para usar la CLI de Azure para asegurar una SAS con credenciales de Azure AD, primero asegúrese de haber instalado la última versión de la CLI de Azure. Para obtener más información sobre cómo instalar la CLI de Azure, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli).

## <a name="sign-in-with-azure-ad-credentials"></a>Iniciar sesión con las credenciales de Azure AD

Inicie sesión en la CLI de Azure con sus credenciales de Azure AD. Para obtener más información, consulte [Inicio de sesión con la CLI de Azure](/cli/azure/authenticate-azure-cli).

## <a name="assign-permissions-with-rbac"></a>Asignación de permisos con RBAC

Para crear una SAS de delegación de usuarios desde Azure PowerShell, se debe asignar a la cuenta de Azure AD utilizada para iniciar sesión en la CLI de Azure un rol que incluya la acción **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey**. Gracias a este permiso, la cuenta de Azure AD puede solicitar la *clave de delegación de usuarios*. La clave de delegación de usuarios se usa para firmar la SAS de delegación de los usuarios. El rol que proporciona la acción **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey** debe asignarse en el nivel de la cuenta de almacenamiento, el grupo de recursos o la suscripción.

Si no tiene permisos suficientes para asignar roles de RBAC a la entidad de seguridad de Azure AD, puede que tenga que pedir al propietario o administrador de la cuenta que asigne los permisos necesarios.

En el ejemplo siguiente se asigna el rol de **colaborador de datos de Storage Blob**, que incluye la acción **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey**. El ámbito del rol se encuentra en el nivel de la cuenta de almacenamiento.

No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

Para obtener más información sobre los roles integrados que incluyen la acción **Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey**, consulte [Roles integrados en los recursos de Azure](../../role-based-access-control/built-in-roles.md).

## <a name="use-azure-ad-credentials-to-secure-a-sas"></a>Uso de credenciales de Azure AD para proteger una SAS

Cuando se crea una SAS de delegación de usuarios con la CLI de Azure, la clave de delegación de usuarios que se usa para firmar la SAS se crea de forma implícita. La hora de inicio y la hora de expiración que especifique para la SAS también se usan como hora de inicio y hora de expiración para la clave de delegación de usuarios.

Dado que el intervalo máximo de validez de la clave de delegación de usuarios es de siete días a partir de la fecha de inicio, debe especificar una hora de expiración para la SAS que está en el plazo de siete días a partir de la hora de inicio. La SAS no es válida después de que expire la clave de delegación de usuarios, por lo que una SAS con un tiempo de expiración de más de siete días seguirá siendo válida solo durante siete días.

Al crear una SAS de delegación de usuarios, se requieren `--auth-mode login` y `--as-user parameters`. Especifique un *inicio de sesión* para el parámetro `--auth-mode` y que así las solicitudes realizadas a Azure Storage estén autorizadas con sus credenciales de Azure AD. Especifique el parámetro `--as-user` para indicar que la SAS devuelta debe ser una SAS de delegación de usuarios.

### <a name="create-a-user-delegation-sas-for-a-container"></a>Creación de una SAS de delegación de usuarios para un contenedor

Para crear una SAS de delegación de usuarios para un contenedor con la CLI de Azure, llame al comando [az storage container generate-sas](/cli/azure/storage/container#az-storage-container-generate-sas).

Los permisos admitidos para una SAS de delegación de usuarios en un contenedor incluyen Agregar, Crear, Eliminar, Enumerar, Leer y Escribir. Los permisos se pueden especificar individualmente o combinados. Para obtener más información sobre estos permisos, consulte [Crear una SAS de delegación de usuarios](/rest/api/storageservices/create-user-delegation-sas).

En el ejemplo siguiente se devuelve un token de SAS de delegación de usuarios para un contenedor. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli-interactive
az storage container generate-sas \
    --account-name <storage-account> \
    --name <container> \
    --permissions acdlrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
```

El token de SAS de delegación de usuarios devuelto será similar al siguiente:

```
se=2019-07-27&sp=r&sv=2018-11-09&sr=c&skoid=<skoid>&sktid=<sktid>&skt=2019-07-26T18%3A01%3A22Z&ske=2019-07-27T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

### <a name="create-a-user-delegation-sas-for-a-blob"></a>Creación de una SAS de delegación de usuarios para un blob

Para crear una SAS de delegación de usuarios para un blob con la CLI de Azure, llame al comando [az storage blob generate-sas](/cli/azure/storage/blob#az-storage-blob-generate-sas).

Los permisos admitidos para una SAS de delegación de usuarios en un blob incluyen Agregar, Crear, Eliminar, Leer y Escribir. Los permisos se pueden especificar individualmente o combinados. Para obtener más información sobre estos permisos, consulte [Crear una SAS de delegación de usuarios](/rest/api/storageservices/create-user-delegation-sas).

La siguiente sintaxis devuelve una SAS de delegación de usuarios para un blob. En el ejemplo se especifica el parámetro `--full-uri`, que devuelve el URI del blob con el token de SAS anexado. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli-interactive
az storage blob generate-sas \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --permissions acdrw \
    --expiry <date-time> \
    --auth-mode login \
    --as-user
    --full-uri
```

El URI del token de SAS de delegación de usuarios devuelto será similar al siguiente:

```
https://storagesamples.blob.core.windows.net/sample-container/blob1.txt?se=2019-08-03&sp=rw&sv=2018-11-09&sr=b&skoid=<skoid>&sktid=<sktid>&skt=2019-08-02T2
2%3A32%3A01Z&ske=2019-08-03T00%3A00%3A00Z&sks=b&skv=2018-11-09&sig=<signature>
```

> [!NOTE]
> Una SAS de delegación de usuarios no admite la definición de permisos con una directiva de acceso almacenada.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de una SAS de delegación de usuario (API de REST)](/rest/api/storageservices/create-user-delegation-sas)
- [Obtener la operación de clave de delegación de usuario](/rest/api/storageservices/get-user-delegation-key)
