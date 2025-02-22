---
title: Solución de problemas de una tarjeta rechazada en el registro de Azure
description: Resuelva problemas de tarjeta de crédito rechazada durante el registro de Azure en Azure Portal o en el Centro de cuentas.
author: v-miegge
manager: adpick
editor: v-jesits
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/12/2019
ms.author: banders
ms.openlocfilehash: 730238d62e4ee4aad1807a4461c9b26ee1c8485d
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69656839"
---
# <a name="troubleshoot-a-declined-card-at-azure-sign-up"></a>Solución de problemas de una tarjeta rechazada en el registro de Azure

Este artículo ayuda a resolver problemas en los que una tarjeta de crédito se rechaza al registrarse en Azure Portal o en el Centro de cuentas de Azure. Antes de empezar a solucionar el problema, compruebe lo siguiente:

- Compruebe que la información que ha facilitado en el perfil de la cuenta de Azure (como la dirección de correo electrónico de contacto, la dirección postal y el número de teléfono) es correcta.
- Compruebe que la información de la tarjeta de crédito es correcta.
- Compruebe que no tenga ninguna cuenta Microsoft con la misma información.
- No se aceptan tarjetas de débito.

## <a name="issues"></a>Issues

Aquí se enumeran los problemas comunes que pueden hacer que una tarjeta de crédito se rechace al registrarse en Azure.

### <a name="the-credit-card-provider-is-not-accepted-for-your-country"></a>El proveedor de la tarjeta de crédito no se acepta en su país

Cuando elige una tarjeta, Azure muestra únicamente las opciones de tarjeta que son válidas en el país que seleccione. Póngase en contacto con su banco o con el emisor de la tarjeta para confirmar que la tarjeta de crédito tiene habilitada las transacciones internacionales. Para más información sobre los países y las divisas admitidos, vea [Preguntas más frecuentes sobre los precios de Azure](https://azure.microsoft.com/pricing/faq/).

>[!Note]
>Las tarjetas de crédito de American Express no se admiten actualmente como instrumento de pago en la India. No existe ningún plazo relativo a cuándo pasará a ser una forma de pago aceptada.

### <a name="youre-using-a-virtual-or-prepaid-card"></a>Está utilizando una tarjeta virtual o de prepago 

Las tarjetas de crédito o débito virtuales o de prepago no se aceptan como opciones de pago para suscripciones de Azure.

### <a name="your-credit-information-is-inaccurate-or-incomplete"></a>La información de la tarjeta de crédito es inadecuada o está incompleta 

El nombre, la dirección y el código CVV que escriba deben coincidir exactamente con lo que aparece impreso en la tarjeta.

### <a name="the-card-is-inactive-or-blocked"></a>La tarjeta está inactiva o bloqueada 

Póngase en contacto con el banco para asegurarse de que la tarjeta está activa.

Puede que esté experimentando otros problemas de registro 

Para más información sobre cómo solucionar problemas de registro en Azure, vea el siguiente artículo de Knowledge Base: 

[No se puede registrar en Azure en Azure Portal o en el Centro de cuentas de Azure](billing-troubleshoot-azure-sign-up.md)

### <a name="you-represent-a-business-that-doesnt-want-to-pay-by-card"></a>Representa a una empresa que no quiere pagar con tarjeta 

Si representa una empresa, puede usar diferentes métodos de pago de factura (como cheques, cheques urgentes o transferencias electrónicas) para pagar la suscripción de Azure. Tras configurar la cuenta para el pago con factura, no podrá cambiar a otra opción de pago, a menos que tenga un contrato de cliente de Microsoft y se haya suscrito a Azure a través del sitio web de Azure.

Para más información sobre cómo pagar con factura, vea [Pago de las suscripciones de Azure con factura](billing-how-to-pay-by-invoice.md).

### <a name="your-credit-card-information-is-outdated"></a>La información de la tarjeta de crédito está obsoleta 

Para más información sobre cómo administrar la información de la tarjeta (incluido cambiarla o quitarla), vea [Agregar, actualizar o quitar una tarjeta de crédito para Azure](billing-how-to-change-credit-card.md).

## <a name="additional-help-resources"></a>Más recursos de ayuda

Otros artículos de solución de problemas relativos a la facturación y las suscripciones de Azure

- [Problemas de suscripción](billing-troubleshoot-azure-sign-up.md)
- [Problemas de inicio de sesión de suscripción](billing-troubleshoot-sign-in-issue.md)
- [No se han encontrado suscripciones](billing-no-subscriptions-found.md)
- [Se ha deshabilitado la vista de costos empresariales](billing-enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Póngase en contacto con nosotros para obtener ayuda

Si tiene alguna pregunta o necesita ayuda, [cree una solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Pasos siguientes

- [Documentación de Facturación de Azure](index.md)
