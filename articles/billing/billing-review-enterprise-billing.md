---
title: Revisión de los datos de facturación de inscripción de empresa de Azure con API REST | Microsoft Docs
description: Aprenda cómo usar las API REST de Azure para revisar la información de facturación de inscripción de empresa.
services: billing
documentationcenter: na
author: lleonard-msft
manager: ''
editor: ''
ms.assetid: 82D50B98-40F2-44B1-A445-4391EA9EBBAA
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/06/2018
ms.author: banders
ms.openlocfilehash: 25d9b48696dc2a83ea0ba77c1be2c7aad7627fff
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443140"
---
# <a name="review-enterprise-enrollment-billing-using-rest-apis"></a>Revisión de la facturación de inscripción de empresa mediante las API REST

Las API de informes de Azure le ayudarán a revisar y administrar los costos de Azure.

En este artículo, aprenderá a recuperar la información de facturación asociada con las cuentas de facturación, las cuentas de departamento o las cuentas de inscripción de Contrato Enterprise (EA) mediante las API de REST de Azure. 

## <a name="individual-account-billing"></a>Cuenta de facturación individual

Para obtener los detalles de uso de las cuentas de un departamento, siga estos pasos:

```http
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

El parámetro `{billingAccountId}` es necesario y debe contener el identificador de la cuenta.

Los siguientes encabezados son obligatorios: 

|Encabezado de solicitud|DESCRIPCIÓN|  
|--------------------|-----------------|  
|*Content-Type:*|Necesario. Establézcalo en `application/json`.|  
|*Authorization:*|Necesario. Establézcalo en una `Bearer` [clave de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) válida. |  

Este ejemplo muestra una llamada sincrónica que devuelve detalles para el ciclo de facturación actual. Por razones de rendimiento, las llamadas sincrónicas devuelven información del último mes.  También puede llamar a la [API de forma asincrónica](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para devolver datos durante 36 meses.


## <a name="response"></a>Response  

Se devuelve el código de estado 200 (OK) en el caso de una respuesta correcta, que contiene una lista de costos detallados de la cuenta.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/BillingAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        ...
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        "isEstimated": false,
        ...
      }
    }
  ]
}
```  

Este ejemplo es la versión abreviada; para ver una descripción completa de cada campo de respuesta y el control de errores, consulte [Get usage detail for a billing account](/rest/api/consumption/usagedetails/list#billingaccountusagedetailslist) (Obtención de detalles de uso para una cuenta de facturación).

## <a name="department-billing"></a>Facturación de departamento 

Obtenga detalles de uso agregados para todas las cuentas de un departamento. 

```http
GET https://management.azure.com/providers/Microsoft.Billing/departments/{departmentId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

El parámetro `{departmentId}` es necesario y debe contener el identificador del departamento de la cuenta de inscripción.

Los siguientes encabezados son obligatorios: 

|Encabezado de solicitud|DESCRIPCIÓN|  
|--------------------|-----------------|  
|*Content-Type:*|Necesario. Establézcalo en `application/json`.|  
|*Authorization:*|Necesario. Establézcalo en una `Bearer` [clave de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) válida. |  

Este ejemplo muestra una llamada sincrónica que devuelve detalles para el ciclo de facturación actual. Por razones de rendimiento, las llamadas sincrónicas devuelven información del último mes.  También puede llamar a la [API de forma asincrónica](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para devolver datos durante 36 meses.

### <a name="response"></a>Response  

Se devuelve el código de estado 200 (OK) en el caso de una respuesta correcta, que contiene una lista de información de uso detallada y los costos de un determinado período de facturación, además del identificador de factura del departamento.


En el ejemplo siguiente se muestra la salida de la API REST para el departamento `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/Departments/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        "instanceName": "shared1",
        "instanceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-Web-eastasia/providers/Microsoft.Web/sites/shared1",
        "instanceLocation": "eastasia",
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
```  

Este ejemplo es la versión abreviada; para ver una descripción completa de cada campo de respuesta y el control de errores, consulte [Get usage detail for a department](/rest/api/consumption/usagedetails/list#departmentusagedetailslist) (Obtención de detalles de uso para un departamento).

## <a name="enrollment-account-billing"></a>Facturación de la cuenta de inscripción

Obtenga detalles de uso agregados para la cuenta de inscripción.

```http
GET GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/{enrollmentAccountId}/providers/Microsoft.Consumption/usageDetails?api-version=2018-06-30
Content-Type: application/json   
Authorization: Bearer
```

El parámetro `{enrollmentAccountId}` es necesario y debe contener el identificador de la cuenta de inscripción.

Los siguientes encabezados son obligatorios: 

|Encabezado de solicitud|DESCRIPCIÓN|  
|--------------------|-----------------|  
|*Content-Type:*|Necesario. Establézcalo en `application/json`.|  
|*Authorization:*|Necesario. Establézcalo en una `Bearer` [clave de API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) válida. |  

Este ejemplo muestra una llamada sincrónica que devuelve detalles para el ciclo de facturación actual. Por razones de rendimiento, las llamadas sincrónicas devuelven información del último mes.  También puede llamar a la [API de forma asincrónica](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#asynchronous-call-polling-based) para devolver datos durante 36 meses.

### <a name="response"></a>Response  

Se devuelve el código de estado 200 (OK) en el caso de una respuesta correcta, que contiene una lista de información de uso detallada y los costos de un determinado período de facturación, además del identificador de factura del departamento.

En el ejemplo siguiente se muestra la salida de la API REST para la inscripción Enterprise `1234`.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702/providers/Microsoft.Consumption/usageDetails/usageDetailsId1",
      "name": "usageDetailsId1",
      "type": "Microsoft.Consumption/usageDetails",
      "properties": {
        "billingPeriodId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/billingPeriods/201702",
        "invoiceId": "/providers/Microsoft.Billing/EnrollmentAccounts/1234/providers/Microsoft.Billing/invoices/201703-123456789",
        "usageStart": "2017-02-13T00:00:00Z",
        "usageEnd": "2017-02-13T23:59:59Z",
        ....
        "currency": "USD",
        "usageQuantity": 0.00328,
        "billableQuantity": 0.00328,
        "pretaxCost": 0.67,
        ...
      }
    }
  ]
}
``` 

Este ejemplo es la versión abreviada; para ver una descripción completa de cada campo de respuesta y el control de errores, consulte [Get usage detail for an enrollment account](/rest/api/consumption/usagedetails/list#enrollmentaccountusagedetailslist) (Obtención de detalles de uso de una cuenta de inscripción).

## <a name="next-steps"></a>Pasos siguientes 
- Revisión de la [introducción a Enterprise Reporting](https://docs.microsoft.com/azure/billing/billing-enterprise-api)
- Investigación de la [API REST de facturación de empresa](https://docs.microsoft.com/rest/api/billing/)   
- [Get started with Azure REST API](https://docs.microsoft.com/rest/api/azure/) (Introducción a la API REST de Azure)   
