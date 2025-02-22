---
title: Descripción del uso y los cargos detallados | Microsoft Docs
description: Aprenda a leer y comprender el uso y los cargos detallados
author: bandersmsft
manager: micflan
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/24/2019
ms.author: banders
ms.openlocfilehash: 1842d32a838470d9b2af3a778c44c37464d32294
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954338"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Comprenda los términos en el archivo de uso y cargos de Azure

El archivo de uso y cargos detallados contiene el uso valorado diario según las tarifas negociadas, las compras (por ejemplo, reservas de direcciones, precios de Marketplace) y los reembolsos durante el período especificado.
Los precios no incluyen créditos, impuestos ni otros cargos o descuentos.
En la tabla siguiente se cubren los cargos que se incluyen para cada tipo de cuenta.

Tipo de cuenta | Uso de Azure | Uso de Marketplace | Compras | Reembolsos
--- | --- | --- | --- | ---
Contrato Enterprise (EA) | Sí | Sí | Sí | Sin
Contrato de cliente de Microsoft (MCA) | Sí | Sí | Sí | Sí
Pay-as-you-go (PAYG) | Sí | Sí | No | Sin

Para más información sobre los pedidos de Marketplace (también conocidos como servicios externos), consulte [Descripción de los gastos de servicios externos de Azure](billing-understand-your-azure-marketplace-charges.md).

Para obtener instrucciones de descarga, consulte [Obtención de los datos de uso diario y la factura de Azure](billing-download-azure-invoice-daily-usage-date.md).
Puede abrir el archivo CSV de uso y cargos en Microsoft Excel o en otra aplicación de hoja de cálculo.

## <a name="list-of-terms-and-descriptions"></a>Lista de términos y descripciones

En la tabla siguiente se describen los términos importantes que se usan en la versión más reciente del archivo de uso y cargos de Azure.
En la lista se incluyen las cuentas de pago por uso, Contrato Enterprise (EA) y Contrato de cliente de Microsoft (MCA).

Término | Tipo de cuenta | DESCRIPCIÓN
--- | --- | ---
AccountName | EA, pago por uso | Nombre para mostrar de la cuenta de inscripción de EA o de la cuenta de facturación de pago por uso.
AccountOwnerId<sup>1</sup> | EA, pago por uso | Identificador único de la cuenta de inscripción de EA o de la cuenta de facturación de pago por uso.
AdditionalInfo | Todo | Metadatos específicos del servicio. Por ejemplo, un tipo de imagen de una máquina virtual.
BillingAccountId<sup>1</sup> | Todo | Identificador único de la cuenta de facturación raíz.
BillingAccountName | Todo | Nombre de la cuenta de facturación.
BillingCurrency | Todo | Moneda asociada con la cuenta de facturación.
BillingPeriod | EA, pago por uso | El período de facturación del cargo.
BillingPeriodEndDate | Todo | La fecha de finalización del período de facturación.
BillingPeriodStartDate | Todo | La fecha de finalización del período de facturación.
BillingProfileId<sup>1</sup> | Todo | Identificador único de la inscripción de EA, suscripción de pago por uso, perfil de facturación de MCA o cuenta consolidada de AWS.
BillingProfileName | Todo | Nombre de la inscripción de EA, suscripción de pago por uso, perfil de facturación de MCA o cuenta consolidada de AWS.
ChargeType | Todo | Indica si el cargo representa la utilización (**uso**), una compra (**Compra**) o un reembolso (**Reembolso**).
ConsumedService | Todo | Nombre del servicio al que está asociado el cargo.
CostCenter<sup>1</sup> | EA, MCA | Centro de coste definido en la suscripción para el seguimiento de los costes (disponible solo en períodos de facturación abiertos para cuentas de MCA).
Coste | EA, pago por uso | Véase CostInBillingCurrency.
CostInBillingCurrency | MCA | Coste del cargo en la moneda de facturación antes de créditos o impuestos.
CostInPricingCurrency | MCA | Coste del cargo en la moneda de precios antes de créditos o impuestos.
Moneda | EA, pago por uso | Véase BillingCurrency.
Date<sup>1</sup> | Todo | La fecha de utilización o compra del cargo.
EffectivePrice | Todo | Precio unitario combinado para el período. Los precios combinados promedian las fluctuaciones del precio unitario, como los niveles graduados, lo que reduce el precio a medida que la cantidad aumenta con el tiempo.
ExchangeRateDate | MCA | Fecha en la que se estableció el tipo de cambio.
ExchangeRatePricingToBilling | MCA | Tipo de cambio que se usa para convertir el coste en la moneda de precios a la moneda de facturación.
Frecuencia | Todo | Indica si se espera que un cargo se repita. Los cargos pueden producirse una vez (**Una vez**), repetirse mensual o anualmente (**Periódico**) o basado en la utilización (**Basado en uso**).
InvoiceId | Pago por uso, MCA | Identificador único del id. de documento que se muestra en el archivo PDF de la factura.
InvoiceSection | MCA | Véase InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | Identificador único para el departamento de EA o la sección de la factura de MCA.
InvoiceSectionName | EA, MCA | Nombre del departamento de EA o la sección de la factura de MCA.
IsAzureCreditEligible | Todo | Indica si el pago del cargo es elegible por usar créditos de Azure (valores: True, False).
Location | MCA | Ubicación del centro de datos donde se ejecuta el recurso.
MeterCategory | Todo | El nombre de la categoría de clasificación del medidor. Por ejemplo, *Servicios en la nube* o *Redes*.
MeterId<sup>1</sup> | Todo | Identificador único del medidor.
MeterName | Todo | Nombre del medidor.
MeterRegion | Todo | Nombre de la ubicación del centro de datos para los servicios cuyo precio se basa en la ubicación. Véase Ubicación.
MeterSubCategory | Todo | El nombre de la categoría de subclasificación del medidor.
OfferId<sup>1</sup> | Todo | Nombre de la oferta comprada.
PartNumber<sup>1</sup> | EA, pago por uso | Identificador que se usa para obtener los precios específicos de medidor.
PlanName | EA, pago por uso | Nombre del plan de Marketplace.
PreviousInvoiceId | MCA | Referencia a una factura original si este elemento de línea es un reembolso.
PricingCurrency | MCA | Moneda que se usa cuando la clasificación se basa en precios negociados.
Producto | Todo | Nombre del producto.
ProductId<sup>1</sup> | MCA | Identificador único del producto.
ProductOrderId | Todo | Identificador único del pedido de producto.
ProductOrderName | Todo | Nombre único para el pedido del producto.
PublisherName | Todo | Editor de los servicios de Marketplace.
PublisherType | Todo | Tipo de publicador (valores: **Azure**, **AWS**, **Marketplace**).
Cantidad | Todo | Número de unidades compradas o consumidas.
ReservationId | EA, MCA | Identificador único de la instancia de la reserva comprada.
ReservationName | EA, MCA | Nombre de la instancia de la reserva comprada.
ResourceGroup | Todo | Nombre del [grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) en el que se encuentra el recurso.
ResourceId<sup>1</sup> | Todo | Identificador único del recurso de [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/resources).
ResourceLocation | Todo | Ubicación del centro de datos donde se ejecuta el recurso. Véase Ubicación.
ResourceName | EA, pago por uso | Nombre del recurso.
ResourceType | MCA | Tipo de instancia del recurso.
ServiceFamily | MCA | Familia de servicios a la que pertenece el servicio.
ServiceInfo1 | Todo | Metadatos específicos del servicio.
ServiceInfo2 | Todo | Campo heredado con metadatos específicos del servicio opcional.
ServicePeriodEndDate | MCA | Fecha de finalización del período de calificación que ha definido y bloqueado los precios para el servicio consumido o adquirido.
ServicePeriodStartDate | MCA | Fecha de inicio del período de calificación que ha definido y bloqueado los precios para el servicio consumido o adquirido.
SubscriptionId<sup>1</sup> | Todo | Identificador único de la suscripción de Azure.
SubscriptionName | Todo | Nombre de la suscripción de Azure.
Tags<sup>1</sup> | Todo | Etiquetas asignadas al recurso. No incluye etiquetas de grupo de recursos. Se puede usar para agrupar o distribuir los costes para el contracargo interno. Para más información, consulte [Organize your Azure resources with tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) (Organización de los recursos de Azure con etiquetas).
Término | Todo | Muestra el plazo de validez de la oferta. Por ejemplo:  En el caso de instancias reservadas, muestra 12 meses como plazo. Para compras puntuales o compras periódicas, el plazo es de 1 mes (SaaS, soporte de Marketplace). Esto no es aplicable para el consumo de Azure.
UnitOfMeasure | Todo | Unidad de medida para facturar por el servicio Por ejemplo, los servicios de proceso se facturan por hora.
UnitPrice | EA, pago por uso | El precio unitario del cargo.

_<sup>**1**</sup> Campos que se usan para crear un identificador único para un único registro de costo._

Tenga en cuenta que algunos campos pueden diferir en cuanto a mayúsculas, minúsculas y espacios entre un tipo de cuenta y otro.
Las versiones anteriores de archivos de utilización de pago por uso tienen secciones independientes para el resumen de cuenta y el uso diario.

### <a name="list-of-terms-from-older-apis"></a>Lista de términos de API anteriores
En la tabla siguiente se asignan los términos que se usan en las API anteriores a los nuevos términos. Consulte la tabla anterior para ver las descripciones.

Término anterior | Nuevo término
--- | ---
ConsumedQuantity | Cantidad
IncludedQuantity | N/D
InstanceId | ResourceId
Tarifa | EffectivePrice
Unidad | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date


## <a name="ensure-charges-are-correct"></a>Asegurarse de que los cargos son correctos

Para más información sobre la utilización y los cargos detallados, lea la descripción de la factura de [pago por uso](./billing-understand-your-bill.md) o [contrato del cliente de Microsoft](billing-mca-understand-your-bill.md).

## <a name="need-help-contact-us"></a>¿Necesita ayuda? Póngase en contacto con nosotros.

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Pasos siguientes

- [Visualización y descarga de la factura de Microsoft Azure](billing-download-azure-invoice.md)
- [Visualización y descarga de los datos de uso y los cargos de Microsoft Azure](billing-download-azure-daily-usage.md)
