---
title: 'Azure Backup: Restauración de máquinas virtuales de Azure mediante API REST'
description: Administración de operaciones de restauración de copia de seguridad de máquina virtual de Azure mediante API REST
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: API REST; copia de seguridad de Azure VM; restauración de Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 09/12/2018
ms.author: dacurwin
ms.assetid: b8487516-7ac5-4435-9680-674d9ecf5642
ms.openlocfilehash: bdbceec2f1d0a900ffdb392d8a0505ce11419036
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954904"
---
# <a name="restore-azure-virtual-machines-using-rest-api"></a>Restauración de máquinas virtuales de Azure mediante API REST

Una vez completada la copia de seguridad de una máquina virtual de Azure mediante Azure Backup, se pueden restaurar las máquinas o los discos o archivos de Azure completos a partir de la misma copia de seguridad. En este artículo se describe cómo restaurar una máquina virtual de Azure o los discos mediante la API REST.

Para cualquier operación de restauración, se tiene que identificar primero el punto de recuperación correspondiente.

## <a name="select-recovery-point"></a>Selección de punto de recuperación

Se pueden enumerar los puntos de recuperación disponibles de un elemento de copia de seguridad mediante la [enumeración de puntos de recuperación de la API REST](https://docs.microsoft.com/rest/api/backup/recoverypoints/list). Es una sencilla operación *GET* con todos los valores pertinentes.

```http
GET https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints?api-version=2016-12-01
```

`{containerName}` y `{protectedItemName}` son tal y como se construyen [aquí](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` es "Azure".

El identificador URI de *GET* tiene todos los parámetros necesarios. No es necesario un cuerpo de solicitud adicional.

### <a name="responses"></a>Respuestas

|NOMBRE  |type  |DESCRIPCIÓN  |
|---------|---------|---------|
|200 OK     |   [RecoveryPointResourceList](https://docs.microsoft.com/rest/api/backup/recoverypoints/list#recoverypointresourcelist)      |       OK  |

#### <a name="example-response"></a>Respuesta de ejemplo

Una vez que se emita el identificador URI de *GET*, se devuelve una respuesta 200 (OK).

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-client-request-id: c48f4436-ce3f-42da-b537-12710d4d1a24; c48f4436-ce3f-42da-b537-12710d4d1a24
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14998
x-ms-correlation-request-id: 03453538-2f8d-46de-8374-143ccdf60f40
x-ms-routing-request-id: SOUTHINDIA:20180604T071851Z:03453538-2f8d-46de-8374-143ccdf60f40
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 07:18:51 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "value": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/20982486783671",
      "name": "20982486783671",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "AppConsistent",
        "recoveryPointTime": "2018-06-04T06:06:00.5121087Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/VM;testRG;testVM/recoveryPoints/23358112038108",
      "name": "23358112038108",
      "type": "Microsoft.RecoveryServices/vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints",
      "properties": {
        "objectType": "IaasVMRecoveryPoint",
        "recoveryPointType": "CrashConsistent",
        "recoveryPointTime": "2018-06-03T06:20:56.3043878Z",
        "recoveryPointAdditionalInfo": "",
        "sourceVMStorageType": "NormalStorage",
        "isSourceVMEncrypted": false,
        "isInstantIlrSessionActive": false,
        "recoveryPointTierDetails": [
          {
            "type": 1,
            "status": 1
          },
          {
            "type": 2,
            "status": 1
          }
        ],
        "isManagedVirtualMachine": true,
        "virtualMachineSize": "Standard_A1_v2",
        "originalStorageAccountOption": false
      }
    },
......
```

El punto de recuperación se identifica con el campo `{name}` en la respuesta anterior.

## <a name="restore-disks"></a>Restauración de los discos

Si es necesario personalizar la creación de una máquina virtual a partir de los datos de copia de seguridad, se puede restaurar solo los discos en una cuenta de almacenamiento elegida y crear una máquina virtual a partir de esos discos según sus requisitos. La cuenta de almacenamiento debe estar en la misma región que el almacén de Recovery Services y no debe ser una zona redundante. Tanto los discos como la configuración de la copia de seguridad de la máquina virtual ("vmconfig.json") se almacenarán en la cuenta de almacenamiento dada.

Desencadenar la restauración de discos es una solicitud *POST*. Para más información acerca de la operación de restauración de discos, consulte la [API REST "desencadenar restauración"](https://docs.microsoft.com/rest/api/backup/restores/trigger).

```http
POST https://management.azure.com/Subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}/backupFabrics/{fabricName}/protectionContainers/{containerName}/protectedItems/{protectedItemName}/recoveryPoints/{recoveryPointId}/restore?api-version=2016-12-01
```

`{containerName}` y `{protectedItemName}` son tal y como se construyen [aquí](backup-azure-arm-userestapi-backupazurevms.md#example-responses-1). `{fabricName}` es "Azure" y el `{recoveryPointId}` es el `{name}` campo del punto de recuperación mencionado [anteriormente](#example-response).

### <a name="create-request-body"></a>Creación del cuerpo de la solicitud

Para desencadenar una restauración de disco desde una copia de seguridad de máquina virtual de Azure, los siguientes son los componentes del cuerpo de la solicitud.

|NOMBRE  |type  |DESCRIPCIÓN  |
|---------|---------|---------|
|properties     | [IaaSVMRestoreRequest](https://docs.microsoft.com/rest/api/backup/restores/trigger#iaasvmrestorerequest)        |    RestoreRequestResourceProperties     |

Para obtener una lista completa de las definiciones del cuerpo de la solicitud y otros detalles, consulte el [documento de la API REST sobre desencadenar la restauración](https://docs.microsoft.com/rest/api/backup/restores/trigger#request-body).

#### <a name="example-request"></a>Solicitud de ejemplo

El cuerpo de solicitud siguiente define las propiedades necesarias para desencadenar una restauración del disco.

```json
{
  "properties": {
    "objectType": "IaasVMRestoreRequest",
    "recoveryPointId": "20982486783671",
    "recoveryType": "RestoreDisks",
    "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
    "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testAccount",
    "region": "westus",
    "createNewCloudService": false,
    "originalStorageAccountOption": false,
    "encryptionDetails": {
      "encryptionEnabled": false
    }
  }
}
```

### <a name="response"></a>Response

La activación de un disco de restauración es una [operación asincrónica](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations). Significa que esta operación crea otra que tiene que ser seguida por separado.

Devuelve las dos respuestas: 202 (Accepted) (aceptado) cuando se crea otra operación y, a continuación, 200 (OK) cuando se completa dicha operación.

|NOMBRE  |type  |DESCRIPCIÓN  |
|---------|---------|---------|
|202 - Aceptado     |         |     Accepted    |

#### <a name="example-responses"></a>Respuestas de ejemplo

Una vez enviado el URI de *POST* para desencadenar la restauración de discos, la respuesta inicial es 202 (Accepted) (aceptado) con un encabezado de ubicación o Azure-async-header.

```http
HTTP/1.1 202 Accepted
Pragma: no-cache
Retry-After: 60
Azure-AsyncOperation: https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationsStatus/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Content-Type-Options: nosniff
x-ms-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-client-request-id: a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b; a15ce064-25bd-4ac6-87e5-e3bc6ec65c0b
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-writes: 1198
x-ms-correlation-request-id: 893fe372-8d6c-4c56-b589-45a95eeef95f
x-ms-routing-request-id: SOUTHINDIA:20180604T130003Z:893fe372-8d6c-4c56-b589-45a95eeef95f
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:00:03 GMT
Location: https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
X-Powered-By: ASP.NET
```

A continuación, realice un seguimiento de la operación resultante con el encabezado de ubicación o el encabezado Azure-AsyncOperation y un simple comando *GET*.

```http
GET https://management.azure.com/subscriptions//subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testVaultRG/providers/microsoft.recoveryservices/vaults/testVault/backupFabrics/Azure/protectionContainers/iaasvmcontainer;iaasvmcontainerv2;testRG;testVM/protectedItems/vm;testRG;testVM/operationResults/781a0f18-e250-4d73-b059-5e9ffed4069e?api-version=2016-12-01
```

Una vez completada la operación, devuelve 200 (OK) con el identificador del trabajo de restauración resultante en el cuerpo de respuesta.

```http
HTTP/1.1 200 OK
Pragma: no-cache
X-Content-Type-Options: nosniff
x-ms-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-client-request-id: a7f3a144-ed80-41ee-bffe-ae6a90c35a2f; a7f3a144-ed80-41ee-bffe-ae6a90c35a2f
Strict-Transport-Security: max-age=31536000; includeSubDomains
x-ms-ratelimit-remaining-subscription-reads: 14973
x-ms-correlation-request-id: ea2a8011-eb83-4a4b-9ed2-e694070a966a
x-ms-routing-request-id: SOUTHINDIA:20180604T130917Z:ea2a8011-eb83-4a4b-9ed2-e694070a966a
Cache-Control: no-cache
Date: Mon, 04 Jun 2018 13:09:17 GMT
Server: Microsoft-IIS/8.0
X-Powered-By: ASP.NET

{
  "id": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "name": "781a0f18-e250-4d73-b059-5e9ffed4069e",
  "status": "Succeeded",
  "startTime": "2018-06-04T13:00:03.8068176Z",
  "endTime": "2018-06-04T13:00:03.8068176Z",
  "properties": {
    "objectType": "OperationStatusJobExtendedInfo",
    "jobId": "3021262a-fb3a-4538-9b37-e3e97a386093"
  }
}
```

Puesto que el trabajo de copia de seguridad es una operación de larga duración, se debe seguir como se explica en el [documento sobre la supervisión de trabajos con API REST](backup-azure-arm-userestapi-managejobs.md#tracking-the-job).

Una vez completado el trabajo de larga duración, los discos y la configuración de la máquina virtual incluida en la copia de seguridad ("VMConfig.json") estarán presentes en la cuenta de almacenamiento dada.

## <a name="restore-as-another-virtual-machine"></a>Restauración como otra máquina virtual

[Seleccione el punto de recuperación](#select-recovery-point) y cree el cuerpo de solicitud tal como se especifica a continuación para crear otra máquina virtual de Azure con los datos del punto de recuperación.

El cuerpo de solicitud siguiente define las propiedades necesarias para desencadenar una restauración de la máquina virtual.

```json
{
  "parameters": {
        "subscriptionId": "00000000-0000-0000-0000-000000000000",
        "resourceGroupName": "testVaultRG",
        "vaultName": "testVault",
        "fabricName": "Azure",
        "containerName": "IaasVMContainer;iaasvmcontainerv2;testRG;testVM",
        "protectedItemName": "VM;iaasvmcontainerv2;testRG;testVM",
        "recoveryPointId": "348916168024334",
        "api-version": "2016-12-01",
      "parameters": {
        "properties": {
          "objectType":  "IaasVMRestoreRequest",
          "recoveryPointId": "348916168024334",
          "recoveryType": "AlternateLocation",
          "sourceResourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Compute/virtualMachines/testVM",
          "targetVirtualMachineId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Compute/virtualmachines/targetVM",
          "targetResourceGroupId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG",
          "storageAccountId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testRG/providers/Microsoft.Storage/storageAccounts/testingAccount",
          "virtualNetworkId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet",
          "subnetId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/targetRG/providers/Microsoft.Network/virtualNetworks/testNet/subnets/default",
          "region": "westus",
          "createNewCloudService": false,
          "originalStorageAccountOption": false,
          "encryptionDetails": {
            "encryptionEnabled": false
          }
        }
      }
    }
}
```

La respuesta debe controlarse de la misma manera que [se explicó anteriormente para restaurar los discos](#response).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las API REST de Azure Backup, consulte los siguientes documentos:

- [API REST del proveedor de Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)