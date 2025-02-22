---
title: 'Azure Backup: Creación de almacenes de Recovery Services mediante API REST'
description: Administración de las operaciones de restauración y copia de seguridad de máquina virtual de Azure mediante API REST
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: API REST; copia de seguridad de Azure VM; restauración de Azure VM;
ms.service: backup
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: dacurwin
ms.assetid: e54750b4-4518-4262-8f23-ca2f0c7c0439
ms.openlocfilehash: f60a675b87d989f12ac3e6181f580b8acffa640b
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68688715"
---
# <a name="create-azure-recovery-services-vault-using-rest-api"></a>Creación del almacén de Azure Recovery Services mediante API REST

Los pasos para crear un almacén de Azure Recovery Services con API REST se describen en el [documento de la creación de almacenes de API REST](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate). Vamos a usar este documento como referencia para crear un almacén de prueba llamado "testVault" en "West US".

Para crear o actualizar un almacén de Azure Recovery Services, use la siguiente operación *PUT*.

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.RecoveryServices/vaults/{vaultName}?api-version=2016-06-01
```

## <a name="create-a-request"></a>Creación de una solicitud

Para crear la solicitud *PUT*, se necesita el parámetro `{subscription-id}`. Si tiene varias suscripciones, consulte [Trabajo con varias suscripciones](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). Definirá los elementos `{resourceGroupName}` y `{vaultName}` para sus recursos, junto con el parámetro `api-version`. En este artículo se usa `api-version=2016-06-01`.

Los siguientes encabezados son obligatorios:

| Encabezado de solicitud   | DESCRIPCIÓN |
|------------------|-----------------|
| *Content-Type:*  | Necesario. Establézcalo en `application/json`. |
| *Authorization:* | Necesario. Establézcalo en un [token de acceso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` válido. |

Para más información sobre cómo crear la solicitud, consulte [ Componentes de una solicitud/respuesta de la API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Creación del cuerpo de la solicitud

Para crear el cuerpo de la solicitud, se usan las siguientes definiciones comunes:

|NOMBRE  |Obligatorio  |type  |DESCRIPCIÓN  |
|---------|---------|---------|---------|
|eTag     |         |   Cadena      |  eTag opcional       |
|location     |  true       |Cadena         |   Ubicación del recurso      |
|properties     |         | [VaultProperties](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vaultproperties)        |  Propiedades del almacén       |
|sku     |         |  [Sku](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#sku)       |    Identifica el identificador único de sistema de cada recurso de Azure.     |
|etiquetas     |         | Object        |     Etiquetas del recurso    |

Tenga en cuenta que el nombre del almacén y el nombre del grupo de recursos se proporcionan en el URI de PUT. El cuerpo de solicitud define la ubicación.

## <a name="example-request-body"></a>Cuerpo de solicitud de ejemplo

El cuerpo de ejemplo siguiente se usa para crear un almacén en "West US". Especifique la ubicación. La SKU siempre es "Estándar".

```json
{
  "properties": {},
  "sku": {
    "name": "Standard"
  },
  "location": "West US"
}
```

## <a name="responses"></a>Respuestas

Hay dos respuestas correctas a la operación para crear o actualizar un almacén de Recovery Services:

|NOMBRE  |type  |DESCRIPCIÓN  |
|---------|---------|---------|
|200 OK     |   [Almacén](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)      | OK        |
|201 Creado     | [Almacén](https://docs.microsoft.com/rest/api/recoveryservices/vaults/createorupdate#vault)        |   Creado      |

Para más información sobre las respuestas de API REST, consulte [Process the response message](/rest/api/azure/#process-the-response-message) (Procesamiento del mensaje de respuesta).

### <a name="example-response"></a>Respuesta de ejemplo

Una respuesta *201 Created* (creado) condensada del cuerpo de solicitud de ejemplo anterior muestra que se ha asignado un *identificador* y que *provisioningState* es *Succeeded* (con éxito):

```json
{
  "location": "westus",
  "name": "testVault",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "id": "/subscriptions/77777777-b0c6-47a2-b37c-d8e65a629c18/resourceGroups/Default-RecoveryServices-ResourceGroup/providers/Microsoft.RecoveryServices/vaults/testVault",
  "type": "Microsoft.RecoveryServices/vaults",
  "sku": {
    "name": "Standard"
  }
}
```

## <a name="next-steps"></a>Pasos siguientes

[Creación de una directiva de copia de seguridad para la copia de seguridad de una máquina virtual de Azure en este almacén](backup-azure-arm-userestapi-createorupdatepolicy.md).

Para más información sobre las API REST de Azure, consulte los siguientes documentos:

- [API REST del proveedor de Azure Recovery Services](/rest/api/recoveryservices/)
- [Get started with Azure REST API](/rest/api/azure/) (Introducción a la API REST de Azure)
