---
title: Identidad administrada de Data Factory | Microsoft Docs
description: Obtenga información sobre la identidad administrada de Azure Data Factory.
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: jingwang
ms.openlocfilehash: 3c1bb38eb12ce77d172257706cd458cebda4bd8c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66153426"
---
# <a name="managed-identity-for-data-factory"></a>Identidad administrada de Data Factory

Este artículo ayuda a entender qué es la identidad administrada de Data Factory (anteriormente conocida como Managed Service Identity/MSI) y cómo funciona.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Información general

Al crear una factoría de datos, se puede crear también una identidad administrada. La identidad administrada es una aplicación administrada registrada en Azure Active Directory que representa a esta factoría de datos específica.

La identidad administrada de Data Factory ofrece las características siguientes:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md), en cuyo caso la identidad administrada de Data Factory se usa para la autenticación de Azure Key Vault.
- Conectores incluidos [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure SQL Database](connector-azure-sql-database.md) y [Azure SQL Data Warehouse](connector-azure-sql-data-warehouse.md).
- [Actividad web](control-flow-web-activity.md).

## <a name="generate-managed-identity"></a>Generar identidad administrada

La identidad administrada de Data Factory se genera de la manera siguiente:

- Cuando se crea una factoría de datos mediante **Azure Portal o PowerShell**, la identidad administrada siempre se crea automáticamente.
- Cuando se crea una factoría de datos mediante **SDK**, la identidad administrada se crea solo si se especifica "Identity = new FactoryIdentity()" en el objeto de factoría para la creación. Vea el ejemplo que aparece en el [Inicio rápido de .NET: Crear una factoría de datos](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Cuando se crea una factoría de datos mediante **API de REST**, la identidad administrada solo se crea si se especifica la sección "identity" en el cuerpo de la solicitud. Vea el ejemplo que aparece en el [Inicio rápido de REST: Crear una factoría de datos](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Si observa que la factoría de datos no tiene una identidad administrada asociada tras la instrucción [Recuperar identidad administrada](#retrieve-managed-identity), puede generar una de forma explícita si actualiza la factoría de datos con el iniciador de identidades mediante programación:

- [Generar identidad administrada con PowerShell](#generate-managed-identity-using-powershell)
- [Generar identidad administrada con API de REST](#generate-managed-identity-using-rest-api)
- [Generar identidad administrada con una plantilla de Azure Resource Manager](#generate-managed-identity-using-an-azure-resource-manager-template)
- [Generar identidad administrada con SDK](#generate-managed-identity-using-sdk)

>[!NOTE]
>- La identidad administrada no se puede modificar. La actualización de una factoría de datos que ya tiene una identidad administrada no tiene ningún impacto, la identidad administrada se mantiene sin cambios.
>- Si actualiza una factoría de datos que ya tiene una identidad administrada sin especificar el parámetro "identity" en el objeto de factoría o sin especificar la sección "identity" en el cuerpo de la solicitud de REST, se obtiene un error.
>- Cuando se elimina una factoría de datos, se elimina también la identidad administrada asociada.

### <a name="generate-managed-identity-using-powershell"></a>Generar identidad administrada con PowerShell

Llame al comando **Set-AzDataFactoryV2** de nuevo para ver cómo los campos "Identity" se van generando:

```powershell
PS C:\WINDOWS\system32> Set-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-managed-identity-using-rest-api"></a>Generar identidad administrada con API de REST

Llame a la siguiente API con la sección "identity" en el cuerpo de la solicitud:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2018-06-01
```

**Cuerpo de la solicitud**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Respuesta**: la identidad administrada se crea automáticamente y la sección "identity" se rellena en consecuencia.

```json
{
    "name": "<dataFactoryName>",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2018-06-01"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>"
}
```

### <a name="generate-managed-identity-using-an-azure-resource-manager-template"></a>Generar identidad administrada con una plantilla de Azure Resource Manager

**Plantilla**: agregue "identity": { "type": "SystemAssigned" }.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "resources": [{
        "name": "<dataFactoryName>",
        "apiVersion": "2018-06-01",
        "type": "Microsoft.DataFactory/factories",
        "location": "<region>",
        "identity": {
            "type": "SystemAssigned"
        }
    }]
}
```

### <a name="generate-managed-identity-using-sdk"></a>Generar identidad administrada con SDK

Llame a la función create_or_update de la factoría de datos con Identity=new FactoryIdentity(). Código de ejemplo mediante .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-managed-identity"></a>Recuperar identidad administrada

Puede recuperar la identidad administrada desde Azure Portal o mediante programación. Las secciones siguientes le muestran algunos ejemplos.

>[!TIP]
> Si no ve la identidad administrada, [genérela](#generate-managed-identity) mediante la actualización de la factoría.

### <a name="retrieve-managed-identity-using-azure-portal"></a>Recuperar identidad administrada mediante Azure Portal

Puede encontrar la información de la identidad administrada en Azure Portal -> su factoría de datos -> Propiedades:

- Id. del objeto de identidad administrada
- Inquilino de identidad administrada
- **Id. de aplicación de identidad administrada** > copie este valor

![Recuperar identidad administrada](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-managed-identity-using-powershell"></a>Recuperar identidad administrada mediante PowerShell

Se devuelven el Id. de entidad de seguridad y el Id. de inquilino de la identidad administrada cuando se obtiene una factoría de datos específica del modo siguiente:

```powershell
PS C:\WINDOWS\system32> (Get-AzDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie el identificador de entidad de seguridad y, luego, ejecute el comando de Azure Active Directory siguiente con el Id. de entidad de seguridad como parámetro para obtener el valor de **ApplicationId** que se usará para conceder acceso:

```powershell
PS C:\WINDOWS\system32> Get-AzADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes temas en los que se habla de cuándo y cómo usar la identidad administrada de Data Factory:

- [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md)
- [Copia de datos con Azure Data Lake Storage Gen1 como origen o destino mediante Azure Data Factory](connector-azure-data-lake-store.md)

Vea [¿Qué es Managed Identities for Azure Resources?](/azure/active-directory/managed-identities-azure-resources/overview) para obtener más información sobre las identidades administradas para recursos de Azure en las que se basa la identidad administrada de Data Factory. 
