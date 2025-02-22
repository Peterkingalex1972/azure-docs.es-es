---
title: Incorporación de artefactos a entornos del servicio de integración (ISE) en Azure Logic Apps
description: Agregue aplicaciones lógicas, conectores personalizados y cuentas de integración al entorno del servicio de integración (ISE) para acceder a redes virtuales de Azure (VNET), a la vez que se mantiene privado y aislado del servicio público o "global" de Azure.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: df43b52514eebc3216dbec01cff0d8a3b14e7940
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517421"
---
# <a name="add-artifacts-to-your-integration-service-environment-ise-in-azure-logic-apps"></a>Incorporación de artefactos al entorno del servicio de integración (ISE) en Azure Logic Apps

Después de crear un [entorno del servicio de integración (](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)ISE), agregue artefactos como aplicaciones lógicas, cuentas de integración y conectores personalizados para que puedan acceder a los recursos de la red virtual de Azure.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* El ISE que creó para ejecutar las aplicaciones lógicas. Si no tiene un ISE, [cree primero uno](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

<a name="create-logic-apps-environment"></a>

## <a name="create-logic-apps-in-an-ise"></a>Creación de aplicaciones lógicas en un ISE

Para crear aplicaciones lógicas que se ejecuten en el entorno del servicio de integración (ISE), siga estos pasos:

1. Busque el ISE y ábralo, si todavía no está abierto. En el menú de ISE, en **Configuración**, seleccione **Aplicaciones lógicas** > **Agregar**.

   ![Incorporación de una nueva aplicación lógica a ISE](./media/add-artifacts-integration-service-environment-ise/add-logic-app-to-ise.png)

   O bien

   En el menú principal de Azure, seleccione **Crear un recurso** > **Integración** > **Aplicación lógica**.

1. Proporcione el nombre, la suscripción de Azure y el grupo de recursos de Azure (nuevo o existente) que se usará para la aplicación lógica.

1. En la lista **Ubicación**, en la sección **Entornos de servicio de integración**, seleccione su ISE, por ejemplo:

   ![Selección del entorno de servicio de integración](./media/add-artifacts-integration-service-environment-ise/create-logic-app-with-integration-service-environment.png)

   > [!IMPORTANT]
   > Si quiere usar las aplicaciones lógicas con una cuenta de integración, todas ellas deben usar el mismo ISE.

1. Siga [creando la aplicación lógica de manera habitual](../logic-apps/quickstart-create-first-logic-app-workflow.md).

   Para conocer las diferencias de funcionamiento de los desencadenadores y las acciones y saber cómo se etiquetan cuando usa un ISE en comparación con el servicio global de Logic Apps, consulte [Isolated versus global in the ISE overview](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#difference) (Información general del ISE: aislado vs. global).

1. Para administrar las aplicaciones lógicas y las conexiones de API en su ISE, consulte [Administración del entorno del servicio de integración](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-integration-account-environment"></a>

## <a name="create-integration-accounts-in-an-ise"></a>Creación de cuentas de integración en un ISE

Según la [SKU de ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) que se seleccionó en la creación, el ISE incluye el uso de una cuenta de integración específica sin costo adicional. Las aplicaciones lógicas que existen en un entorno del servicio de integración (ISE) pueden hacer referencia solo a cuentas de integración que existan en el mismo ISE. Por lo tanto, para que una cuenta de integración trabaje con aplicaciones lógicas en un ISE, tanto la cuenta de integración como las aplicaciones lógicas deben usar el *mismo entorno* como ubicación. Para más información sobre las cuentas de integración y los ISE, consulte [Cuentas de integración con ISE](connect-virtual-network-vnet-isolated-environment-overview.md#create-integration-account-environment
).

Para crear una cuenta de integración que use un ISE, siga estos pasos:

1. Busque el ISE y ábralo, si todavía no está abierto. En el menú de ISE, en **Configuración**, seleccione **Cuentas de integración** > **Agregar**.

   ![Agregar una cuenta de integración nueva a ISE](./media/add-artifacts-integration-service-environment-ise/add-integration-account-to-ise.png)

   O bien

   En el menú principal de Azure, seleccione **Crear un recurso** > **Integración** > **Cuenta de integración**.

1. Proporcione el nombre, la suscripción de Azure, el grupo de recursos de Azure (nuevo o existente) y el plan de tarifa que usará para la cuenta de integración.

1. En la lista **Ubicación**, en la sección **Entornos de servicio de integración**, seleccione el mismo ISE que usan las aplicaciones lógicas, por ejemplo:

   ![Selección del entorno de servicio de integración](./media/add-artifacts-integration-service-environment-ise/create-integration-account-with-integration-service-environment.png)

1. [Vincule la aplicación lógica a la cuenta de integración como siempre](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md#link-account).

1. Continúe agregando artefactos a su cuenta de integración, como [socios comerciales](../logic-apps/logic-apps-enterprise-integration-partners.md) y [contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md).

1. Para administrar las cuentas de integración en su ISE, consulte [Administración del entorno del servicio de integración](../logic-apps/ise-manage-integration-service-environment.md).

<a name="create-custom-connectors-environment"></a>

## <a name="create-custom-connectors-in-an-ise"></a>Creación de conectores personalizados en un ISE

Para usar conectores personalizados en su ISE, créelos desde directamente dentro de su ISE.

1. Busque el ISE y ábralo, si todavía no está abierto. En el menú de ISE, en **Configuración**, seleccione **Conectores personalizados** > **Agregar**.

   ![Crear un conector personalizado](./media/add-artifacts-integration-service-environment-ise/add-custom-connector-to-ise.png)

1. Proporcione el nombre, la suscripción de Azure y el grupo de recursos de Azure (nuevo o existente) que se usará para el conector personalizado.

1. En la lista **Ubicación**, en la sección **Entornos de servicio de integración**, seleccione el mismo ISE que usan las aplicaciones lógicas y, después, seleccione **Crear**, por ejemplo:

   ![Selección del entorno de servicio de integración](./media/add-artifacts-integration-service-environment-ise/create-custom-connector-with-integration-service-environment.png)

1. Seleccione el nuevo conector personalizado y, a continuación, seleccione **Editar**, por ejemplo:

   ![Selección y edición del conector personalizado](./media/add-artifacts-integration-service-environment-ise/edit-custom-connectors.png)

1. Continúe creando el conector de la manera habitual a partir de [una definición de OpenAPI](https://docs.microsoft.com/connectors/custom-connectors/define-openapi-definition#import-the-openapi-definition) o [SOAP](https://docs.microsoft.com/connectors/custom-connectors/create-register-logic-apps-soap-connector#2-define-your-connector).

1. Para administrar los conectores personalizados en su ISE, consulte [Administración del entorno del servicio de integración](../logic-apps/ise-manage-integration-service-environment.md).

## <a name="next-steps"></a>Pasos siguientes

* [Administración de entornos del servicio de integración](../logic-apps/ise-manage-integration-service-environment.md)
