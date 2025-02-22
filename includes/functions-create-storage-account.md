---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 25cfcefb600bc12de3dad5b6fe2bcb76d00f0198
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444107"
---
## <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Functions usa una cuenta de uso general en Azure Storage para mantener el estado y otra información acerca de sus funciones. Cree una cuenta de almacenamiento de uso general en el grupo de recursos que ha creado mediante el comando [az storage account create](/cli/azure/storage/account).

En el siguiente comando, sustituya un nombre de cuenta de almacenamiento único de manera global donde vea el marcador de posición `<storage_name>`. Los nombres de las cuentas de almacenamiento deben tener entre 3 y 24 caracteres y solo pueden incluir números y letras en minúscula.

```azurecli-interactive
az storage account create --name <storage_name> --location westeurope --resource-group myResourceGroup --sku Standard_LRS
```
