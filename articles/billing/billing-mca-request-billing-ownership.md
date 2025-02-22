---
title: Obtención de la propiedad de la facturación de las suscripciones a Azure
description: Aprenda a solicitar la propiedad de la facturación de las suscripciones a Azure de otros usuarios.
author: amberbhargava
manager: amberb
editor: banders
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.openlocfilehash: 110bd975de1d865c2aa3d3b088c98cdc8b9e45ad
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019554"
---
# <a name="get-billing-ownership-of-azure-subscriptions-from-other-accounts"></a>Obtención de la propiedad de la facturación de las suscripciones a Azure de otras cuentas

Es posible que desee tomar posesión de las suscripciones a Azure si el propietario de la facturación existente abandona la organización, o que desee pagar las suscripciones mediante su cuenta de facturación. La toma de posesión transfiere las responsabilidades de facturación de las suscripciones a su cuenta.

Este artículo se aplica a una cuenta de facturación para un contrato de cliente de Microsoft. [Compruebe si tiene acceso a un contrato de cliente de Microsoft](#check-for-access).

Para solicitar la propiedad de la facturación, debe ser **propietario de la sección de factura** o **colaborador de la sección de factura**. Para más información, consulte [Tareas y roles de la sección de factura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).

## <a name="request-billing-ownership"></a>Solicitud de propiedad de la facturación

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como propietario o colaborador de la sección de factura de una cuenta de facturación para un contrato de cliente de Microsoft.

2. Busque **Administración de costos + facturación**.

   ![Captura de pantalla que muestra la búsqueda en Azure Portal de Administración de costos + facturación](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)

3. Seleccione **Secciones de factura** en el lado izquierdo. Dependiendo de su acceso, es posible que tenga que seleccionar una cuenta de facturación o un perfil de facturación. En el perfil o la cuenta de facturación, seleccione **Secciones de la factura**.
   
   ![Captura de pantalla que muestra la selección de las secciones de la factura](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Seleccione una sección de la factura en la lista. Una vez que asume la propiedad de las suscripciones, se facturarán en esta sección de la factura.

5. Seleccione **Solicitudes de transferencia** en el lado inferior izquierdo y, luego, **Agregar**.
 
   ![Captura de pantalla que muestra la selección de solicitudes de facturación](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests.png)

6. Escriba la dirección de correo electrónico del usuario al que solicita la propiedad de la facturación. El usuario debe ser un administrador de cuenta en una cuenta de facturación de Microsoft Online Service Program o un propietario de cuenta en un Contrato Enterprise. Para más información, consulte [Visualización de las cuentas de facturación en Azure Portal](billing-view-all-accounts.md). Seleccione **Enviar solicitud de transferencia**.

   ![Captura de pantalla que muestra el envío de una solicitud de transferencia](./media/billing-mca-request-billing-ownership/mca-send-transfer-requests.png)

7. El usuario recibe un correo electrónico con instrucciones para revisar la solicitud de transferencia.

   ![Captura de pantalla que muestra un correo electrónico de solicitud de transferencia](./media/billing-mca-request-billing-ownership/mca-review-transfer-request-email.png)

8. Para aprobar la solicitud de transferencia, el usuario selecciona el vínculo en el correo electrónico y sigue las instrucciones.

    ![Captura de pantalla que muestra un correo electrónico de solicitud de transferencia](./media/billing-mca-request-billing-ownership/mca-review-transfer-request.png)

## <a name="check-the-transfer-request-status"></a>Comprobación del estado de una solicitud de transferencia

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

2. Busque **Administración de costos + facturación**.

   ![Captura de pantalla que muestra la búsqueda en Azure Portal de Administración de costos + facturación](./media/billing-mca-request-billing-ownership/billing-search-cost-management-billing.png)


3. Seleccione **Secciones de factura** en el lado izquierdo. Dependiendo de su acceso, es posible que tenga que seleccionar una cuenta de facturación o un perfil de facturación. En el perfil o la cuenta de facturación, seleccione **Secciones de la factura**.
   
   ![Captura de pantalla que muestra la selección de las secciones de la factura](./media/billing-mca-request-billing-ownership/mca-select-invoice-sections.png)        

4. Seleccione en la lista la sección de la factura para la que envió la solicitud de transferencia.

5. Seleccione **Solicitudes de transferencia** en el lado inferior izquierdo. En la página Solicitudes de transferencia se muestra la siguiente información:

    ![Captura de pantalla que muestra una lista de solicitudes de facturación](./media/billing-mca-request-billing-ownership/mca-select-transfer-requests-for-status.png)

   |Columna|Definición|
   |---------|---------|
   |Fecha de solicitud|La fecha en la que se envió la solicitud de transferencia|
   |Recipient|La dirección de correo electrónico del usuario que envió la solicitud para transferir la propiedad de la facturación|
   |Fecha de expiración|La fecha en que expira la solicitud|
   |Status|El estado de la solicitud de transferencia|

    La solicitud de transferencia puede tener uno de los siguientes estados:

   |Status|Definición|
   |---------|---------|
   |En curso|El usuario no ha aceptado la solicitud de transferencia.|
   |Processing|El usuario aprueba la solicitud de transferencia. La facturación de las suscripciones seleccionadas por el usuario está en proceso de transferencia a la sección de factura|
   |Completed| La facturación de las suscripciones seleccionadas por el usuario se transfiere a la sección de factura.|
   |Finalizado con errores|La solicitud se ha completado pero la facturación de algunas suscripciones que el usuario ha seleccionado no se pudo transferir.|
   |Expirada|El usuario no aceptó la solicitud a tiempo y ha expirado.|
   |Canceled|Un usuario con acceso a la solicitud de transferencia ha cancelado la solicitud.|
   |Rechazado|El usuario ha rechazado la solicitud de transferencia.|

7. Seleccione una solicitud de transferencia para ver los detalles. En la página de detalles de la transferencia se muestra la siguiente información:
   
   ![Captura de pantalla que muestra la lista de las suscripciones transferidas](./media/billing-mca-request-billing-ownership/mca-transfer-completed.png)
    
   |Columna  |Definición|
   |---------|---------|
   |Identificador de la solicitud de transferencia|El identificador único de la solicitud de transferencia. Si envía una solicitud de soporte técnico, comparta el identificador con el Soporte técnico de Azure para agilizar la solicitud de soporte técnico.|
   |Transferencia solicitada el|La fecha en la que se envió la solicitud de transferencia.|
   |Transferencia solicitada por|La dirección de correo electrónico del usuario que envió la solicitud de transferencia.|
   |La solicitud de transferencia caduca el| La fecha en la que expira la solicitud de transferencia.|
   |Dirección de correo electrónico del destinatario|La dirección de correo electrónico del usuario que envió la solicitud para transferir la propiedad de la facturación|
   |Vínculo de transferencia enviado al destinatario|La dirección URL que se envió al usuario para revisar la solicitud de transferencia.|

## <a name="supported-subscription-types"></a>Tipos de suscripciones admitidos

Puede solicitar la propiedad de facturación de los tipos de suscripción que se describen a continuación.

- [Action Pack](https://azure.microsoft.com/offers/ms-azr-0025p/)\* 
- [Azure bajo licencia Open](https://azure.microsoft.com/offers/ms-azr-0111p/)\*
- [Patrocinio de Pase para Azure](https://azure.microsoft.com/offers/azure-pass/)\*
- [Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p/)\*
- [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) 
- [Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Plan de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g/)\*\*
- [Oferta de patrocinio de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/)\*
- [Contrato Enterprise (EA) de Microsoft](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p/)\*
- [Plataformas de MSDN](https://azure.microsoft.com/offers/ms-azr-0062p/)\*
- [Suscriptores de Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/)\*
- [Suscriptores de Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/)\*
- [Suscriptores de Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/)\*
- [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/)\*
- [Suscriptores de Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/)\*

\* Ningún crédito disponible en la suscripción estará disponible en la cuenta nueva después de la transferencia.

\*\* Solo se admite para las suscripciones en cuentas que se crean durante el inicio de sesión en el sitio web de Azure.


## <a name="additional-information"></a>Información adicional

En la sección siguiente se proporciona información adicional acerca de las suscripciones de transferencia.

### <a name="no-service-downtime"></a>No hay ningún tiempo de inactividad del servicio

Los servicios de Azure de la suscripción se siguen ejecutando sin interrupción. Solo cambiamos la relación de facturación de las suscripciones de Azure que el usuario selecciona para transferir.

### <a name="disabled-subscriptions"></a>Suscripciones deshabilitadas

Las suscripciones deshabilitadas no se pueden transferir. Las suscripciones deben estar en estado activo para transferir su propiedad de la facturación.

### <a name="azure-resources-transfer"></a>Transferencia de recursos de Azure

Todos los recursos de las suscripciones, como máquinas virtuales, discos y sitios web se transfieren.

### <a name="azure-marketplace-products-transfer"></a>Transferencia de productos de Azure Marketplace

Los productos de Azure Marketplace se transfieren junto con sus suscripciones correspondientes.

### <a name="azure-reservations-transfer"></a>Transferencia de reservas de Azure

Las reservas de Azure no se mueven automáticamente con las suscripciones. [Póngase en contacto con soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para mover las reservas.

### <a name="access-to-azure-services"></a>Acceso a servicios de Azure

El acceso de los usuarios, grupos o entidades de servicio existentes que se asignó mediante (Azure RBAC (control de acceso basado en rol))[../role-based-access-control/overview.md] no se ve afectado durante la transición.

### <a name="azure-support-plan"></a>Plan de soporte técnico de Azure

El soporte técnico de Azure no se transfiere con las suscripciones. Si el usuario transfiere todas las suscripciones de Azure, pídale que cancele su plan de soporte técnico.

### <a name="charges-for-transferred-subscription"></a>Cargos por la suscripción transferida

El propietario de la facturación original de las suscripciones es responsable de cualquier cargo que se haya informado hasta el momento en que se complete la transferencia. La sección de factura es responsable de los cargos informados desde el momento de la transferencia en adelante. Puede haber algunos cargos que se realizaran antes de la transferencia, pero que se notificaron con posterioridad. Estos cargos se muestran en la sección de factura.

### <a name="cancel-a-transfer-request"></a>Cancelación de una solicitud de transferencia

Puede cancelar la solicitud de transferencia hasta que se apruebe o rechace la solicitud. Para cancelar la solicitud de transferencia, vaya a la [página de detalles de la transferencia](#check-the-transfer-request-status) y seleccione Cancelar en la parte inferior de la página.

### <a name="software-as-a-service-saas-transfer"></a>Transferencia del Software como servicio (SaaS)

No se transfieren los productos de SaaS con las suscripciones. Pida al usuario que [se ponga en contacto con el soporte técnico de Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para transferir la propiedad de la facturación de los productos de SaaS. Junto con la propiedad de la facturación, el usuario también puede transferir la propiedad de los recursos. La propiedad de los recursos le permite realizar operaciones de administración como eliminar y consultar los detalles del producto. El usuario debe ser propietario de un recurso en el producto de SaaS para poder transferir su propiedad.

## <a name="check-for-access"></a>Comprobación del acceso
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Ponerse en contacto con soporte técnico

Si necesita ayuda, [póngase en contacto con soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.

## <a name="next-steps"></a>Pasos siguientes

- La propiedad de la facturación de las suscripciones a Azure se transfiere a la sección de factura. Realice un seguimiento de los cargos de estas suscripciones en [Azure Portal](https://portal.azure.com).
- Asigne permisos para consultar y administrar la facturación de estas suscripciones a otros usuarios. Para más información, consulte [Tareas y roles de la sección de factura](billing-understand-mca-roles.md#invoice-section-roles-and-tasks).
