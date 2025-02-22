---
title: Descripción y uso de ámbitos de Azure Cost Management | Microsoft Docs
description: Este artículo le ayudará a comprender los ámbitos de administración de facturación y recursos disponibles en Azure y cómo usarlos en Cost Management y las API.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 66bad9c9c647fe87fdcf6b99a8d17f319b1ef9fc
ms.sourcegitcommit: 75a56915dce1c538dc7a921beb4a5305e79d3c7a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68479973"
---
# <a name="understand-and-work-with-scopes"></a>Descripción y uso de ámbitos

Este artículo le ayudará a comprender los ámbitos de administración de facturación y recursos disponibles en Azure y cómo usarlos en Cost Management y las API.

## <a name="scopes"></a>Ámbitos

Un _ámbito_ es un nodo de la jerarquía de recursos de Azure en el que los usuarios de Azure AD pueden acceder y administrar servicios. La mayoría de los recursos de Azure se crean e implementan en grupos de recursos que forman parte de suscripciones. Microsoft también ofrece dos jerarquías por encima de las suscripciones de Azure que disponen de roles especializados para administrar datos de facturación:
- Datos de facturación, como pagos y facturas
- Servicios en la nube, como gobernanza de costos y directivas

En los ámbitos es donde puede administrar los datos de facturación, tener roles específicos para pagos, ver facturas y realizar la administración de cuentas generales. Los roles de facturación y cuentas se administran independientemente de aquellos que se usan en la administración de recursos, los cuales usan el [control de acceso basado en rol de Azure](../role-based-access-control/overview.md). Para distinguir claramente la finalidad de los ámbitos independientes, incluidas las diferencias en el control de acceso, estos se conocen como _ámbitos de facturación_ y _ámbitos con RBAC_, respectivamente.

## <a name="how-cost-management-uses-scopes"></a>Uso de los ámbitos por parte de Cost Management

Cost Management funciona en todos los ámbitos por encima de los recursos para permitir a las organizaciones administrar los costos en el nivel al que estas tienen acceso, tanto si es a toda la cuenta de facturación o a un único grupo de recursos. Aunque los ámbitos de facturación varían en función de su contrato de Microsoft (tipo de suscripción), los ámbitos con RBAC no lo hacen.

## <a name="azure-rbac-scopes"></a>Ámbitos con RBAC de Azure

Azure admite tres ámbitos para la administración de recursos. Cada ámbito admite la administración de accesos y la gobernanza incluido, aunque sin limitarse a ello, la administración de costos.

- [**Grupos de administración**](../governance/management-groups/index.md): contenedores jerárquicos, de hasta ocho niveles, para organizar las suscripciones de Azure.

    Tipo de recurso: [Microsoft.Management/managementGroups](/rest/api/resources/managementgroups)

- **Suscripciones**: contenedores principales para recursos de Azure.

    Tipo de recurso: [Microsoft.Resources/subscriptions](/rest/api/resources/subscriptions)

- [**Grupos de recursos**](../azure-resource-manager/resource-group-overview.md#resource-groups): agrupaciones lógicas de recursos relacionados de una solución de Azure que comparten el mismo ciclo de vida. Por ejemplo, los recursos que se implementan y eliminan a la vez.

    Tipo de recurso: [Microsoft.Resources/subscriptions/resourceGroups](/rest/api/resources/resourcegroups)

Los grupos de administración le permiten organizar las suscripciones en una jerarquía. Por ejemplo, podría crear una jerarquía de organización lógica mediante grupos de administración. Posteriormente, podría proporcionar suscripciones a los equipos para cargas de trabajo de producción y de desarrollo y pruebas. Y, finalmente, podría crear grupos de recursos en las suscripciones para administrar cada subsistema o componente.

La creación de una jerarquía organizativa permite que el cumplimiento en materia de costos y directivas se incluya en el nivel organizativo. Posteriormente, cada director puede ver y analizar sus costos actuales. También pueden crear presupuestos para contrarrestar patrones de gastos negativos y optimizar costos con las recomendaciones de Advisor en el nivel inferior.

La concesión de acceso para ver los costos y, opcionalmente, administrar la configuración de estos, como los presupuestos y las exportaciones, se realiza en ámbitos de gobernanza mediante el control de acceso basado en rol de Azure. Puede usar el control de acceso basado en rol de Azure para conceder acceso a los usuarios y grupos de Azure AD para que puedan realizar un conjunto preestablecido de acciones que se definen en un rol de un ámbito específico o en un nivel inferior. Por ejemplo, un rol asignado a un ámbito de grupo de administración concede también los mismos permisos a suscripciones y grupos de recursos anidados.

Cost Management es compatible con los siguientes roles integrados para cada uno de los siguientes ámbitos:

- [**Propietario**](../role-based-access-control/built-in-roles.md#owner): puede ver los costos y administrar todo, incluida la configuración de costos.
- [**Colaborador**](../role-based-access-control/built-in-roles.md#contributor): puede ver los costos y administrar todo, incluida la configuración de costos, pero sin incluir el control de acceso.
- [**Lector**](../role-based-access-control/built-in-roles.md#reader): puede ver todo, incluidos los datos de costos y la configuración, pero no realizar cambios.
- [**Colaborador de Cost Management**](../role-based-access-control/built-in-roles.md#cost-management-contributor): puede ver los costos, administrar la configuración de costos y ver las recomendaciones.
- [**Lector de Cost Management**](../role-based-access-control/built-in-roles.md#cost-management-reader): puede ver los costos, la configuración de costos y ver las recomendaciones.

Colaborador de Cost Management es el rol con menos privilegios que se recomienda. Permite a los usuarios acceso para crear y administrar presupuestos y exportaciones para supervisar de manera más eficaz los costos. Los colaboradores de Cost Management también podrían requerir roles adicionales para admitir escenarios de administración de costos globales. Considere los siguientes escenarios:

- **Actuación cuando se superan los presupuestos**: los colaboradores de Cost Management también necesitan acceso para crear y administrar grupos de acciones para reaccionar automáticamente a usos por encima del límite. Considere la posibilidad de conceder el rol de [colaborador de supervisión](../role-based-access-control/built-in-roles.md#monitoring-contributor) a un grupo de recursos que contiene el grupo de acciones que se debe usar cuando se superan los umbrales del presupuesto. La automatización de acciones concretas requiere roles adicionales para los servicios específicos utilizados como, por ejemplo, Automation y Azure Functions.
- **Programación de exportación de datos de costo**: los colaboradores de Cost Management también necesitan acceso para administrar las cuentas de almacenamiento y programar una exportación para copiar datos en una de ellas. Considere la posibilidad de conceder el rol [Colaborador de la cuenta de almacenamiento](../role-based-access-control/built-in-roles.md#storage-account-contributor) a un grupo de recursos que contenga la cuenta de almacenamiento donde se exportan los datos de costos.
- **Visualización de las recomendaciones de ahorro de costos**: los lectores y los colaboradores de Cost Management tienen permiso para *ver* las recomendaciones sobre costos de forma predeterminada. No obstante, el acceso para actuar sobre las recomendaciones de costos requiere acceso a los recursos individuales. Considere la posibilidad de conceder un [rol específico de servicio](../role-based-access-control/built-in-roles.md#built-in-role-descriptions) si desea actuar en una recomendación basada en costos.

## <a name="enterprise-agreement-scopes"></a>Ámbitos del Contrato Enterprise

Las cuentas de facturación del Contrato Enterprise (EA), también denominadas inscripciones, tienen los siguientes ámbitos:

- [**Cuenta de facturación**](../billing/billing-view-all-accounts.md): representa una inscripción de EA. Las facturas se generan en este ámbito. Las compras que no están basadas en uso, como Marketplace y las reservas, solo estarán disponibles en este ámbito. Dichas compras no están representadas en las cuentas de departamentos o de inscripción.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Enrollment)`
- **Departamento**: agrupación opcional de las cuentas de inscripción.

    Tipo de recurso: `Billing/billingAccounts/departments`

- **Cuenta de inscripción**: representa a un único propietario de la cuenta. No admite la concesión de acceso a varios usuarios.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/enrollmentAccounts`

Aunque los ámbitos de gobernanza están enlazados a un único directorio, los ámbitos de facturación de EA no lo están. Una cuenta de facturación de EA puede tener suscripciones en cualquier número de directorios de Azure AD.

Los ámbitos de facturación de EA admiten los siguientes roles:

- **Administrador de empresa**: puede administrar la configuración y el acceso de la cuenta de facturación, puede ver todos los costos y puede administrar la configuración de estos. Por ejemplo, presupuestos y exportaciones. En la práctica, el ámbito de facturación de EA es el mismo que el del [rol RBAC de Azure de colaborador de Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Usuario de solo lectura de Enterprise**: puede ver la configuración de la cuenta de facturación, los datos y la configuración de los costos. Por ejemplo, presupuestos y exportaciones. En la práctica, el ámbito de facturación de EA es el mismo que el del [rol RBAC de Azure de lector de Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador de departamento**: puede administrar la configuración del departamento como, por ejemplo, el centro de costo y puede acceder, ver todos los costos y administrar la configuración de estos. Por ejemplo, presupuestos y exportaciones.  El valor **DA view charges** (El administrador del departamento ve los cargos) debe estar habilitado para que los administradores de departamentos y los usuarios de solo lectura puedan ver los costos. Si este valor está deshabilitado, los usuarios del departamento no podrán ver los costos en ningún nivel, incluso si son propietarios de una cuenta o suscripción.
- **Usuario de solo lectura del departamento**: puede ver la configuración del departamento, los datos y la configuración de los costos. Por ejemplo, presupuestos y exportaciones. Si este valor está deshabilitado, los usuarios del departamento no podrán ver los costos en ningún nivel, incluso si son propietarios de una cuenta o suscripción.
- **Propietario de la cuenta**: puede administrar la configuración de la cuenta de inscripción (por ejemplo, el centro de costo), ver todos los costos y administrar la configuración de estos (por ejemplo, los presupuestos y las exportaciones) para la cuenta de inscripción. El valor **AO view charges** (El propietario de la cuenta ve los cargos) debe estar habilitado para que los propietarios de la cuentas y los usuarios de RBAC puedan ver los costos.

Los usuarios de cuentas de facturación de EA no tienen acceso directo a las facturas. Las facturas están disponibles desde un sistema de licencias por volumen externo.

Las suscripciones de Azure se anidan en cuentas de inscripción. Los usuarios de facturación tienen acceso a los datos de costo de las suscripciones y grupos de recursos que están bajo sus respectivos ámbitos. No tienen acceso para ver ni administrar recursos en Azure Portal. Para que los usuarios de facturación puedan ver los costos, deben ir a **Administración de costos + facturación** en la lista de servicios de Azure Portal. Después, pueden filtrar los costos de las suscripciones y grupos de recursos específicos sobre los cuales deben informar.

Los usuarios de facturación no tienen acceso a grupos de administración ya que no están explícitamente incluidos en una cuenta de facturación específica. Se debe conceder acceso a los grupos de administración de manera explícita. Los grupos de administración incluyen los costos de todas las suscripciones anidadas. Sin embargo, solo incluyen las compras basadas en uso. No incluyen compras como las reservas u ofertas de terceros en Marketplace. Para ver estos costos, use la cuenta de facturación de EA.

## <a name="individual-agreement-scopes"></a>Ámbitos de contrato individuales

Las suscripciones de Azure creadas a partir de ofertas individuales como en el caso del tipo de pago por uso y otros tipos relacionados como evaluación gratuita y ofertas de desarrollo y pruebas, no tienen un ámbito de cuenta de facturación explícito. En su lugar, cada suscripción tiene un propietario o administrador de la cuenta, como el propietario de la cuenta de EA.

- [**Cuenta de facturación**](../billing/billing-view-all-accounts.md): representa a un único propietario de la cuenta para una o varias suscripciones de Azure. Actualmente no admite la concesión de acceso a varios usuarios ni acceso a las vistas de costos agregadas.

    Tipo de recurso: No aplicable

Los administradores de cuentas individuales de suscripciones de Azure pueden ver y administrar datos de facturación, como facturas y pagos, desde el [Centro de cuentas de Azure](https://account.azure.com/subscriptions). Sin embargo, no pueden ver los datos de costos ni administrar los recursos en Azure Portal. Para conceder acceso al administrador de la cuenta, use los roles de Cost Management que se han mencionado anteriormente.

A diferencia de los de EA, los administradores de cuentas individuales de suscripciones de Azure pueden ver sus facturas en Azure Portal. Tenga en cuenta que los roles Lector y Colaborador de Cost Management no proporcionan acceso a las facturas. Para más información, consulte [Concesión de acceso a la facturación](../billing/billing-manage-access.md##give-read-only-access-to-billing).

## <a name="microsoft-customer-agreement-scopes"></a>Ámbitos de contrato de cliente de Microsoft

Las cuentas de facturación de los contratos de cliente de Microsoft tienen los siguientes ámbitos:

- **Cuenta de facturación**: representa un contrato de cliente para varios productos y servicios de Microsoft. Las cuentas de facturación de los contratos de cliente no son funcionalmente las mismas que las de las inscripciones de EA. Las inscripciones de EA se alinean más estrechamente a los perfiles de facturación.

    Tipo de recurso: `Microsoft.Billing/billingAccounts (accountType = Organization)`

- **Perfil de facturación**: define las suscripciones que se incluyen en una factura. Los perfiles de facturación son el equivalente funcional de una inscripción de EA, ya que ese es el ámbito en el que se generan las facturas. De manera similar, las compras que no están basadas en uso, como Marketplace y las reservas, solo estarán disponibles en este ámbito. No están incluidas en las secciones de la factura.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/billingProfiles`

- **Sección de factura**: representa un grupo de suscripciones en una factura o perfil de facturación. Las secciones de las facturas son como los departamentos, varios usuarios pueden acceder a una sección de factura.

    Tipo de recurso: `Microsoft.Billing/billingAccounts/invoiceSections`

A diferencia de los ámbitos de facturación de EA, las cuentas de facturación de los contratos de cliente _están_ enlazadas a un único directorio y no pueden tener suscripciones en varios directorios de Azure AD.

Los ámbitos de facturación de los contratos de cliente admiten los siguientes roles:

- **Propietario**: puede administrar la configuración de facturación y el acceso, ver todos los costos y administrar la configuración de estos. Por ejemplo, presupuestos y exportaciones. En la práctica, el ámbito de facturación de los contratos de cliente es el mismo que el del [rol RBAC de Azure de colaborador de Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Colaborador**: puede administrar la configuración de facturación excepto el acceso, ver todos los costos y administrar la configuración de estos. Por ejemplo, presupuestos y exportaciones. En la práctica, el ámbito de facturación de los contratos de cliente es el mismo que el del [rol RBAC de Azure de colaborador de Cost Management](../role-based-access-control/built-in-roles.md#cost-management-contributor).
- **Lector**: puede ver la configuración de facturación, los datos de costos y la configuración de estos. Por ejemplo, presupuestos y exportaciones. En la práctica, este ámbito de facturación de los contratos de cliente es el mismo que el del [rol RBAC de Azure de lector de Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Administrador de facturación**: puede ver y pagar facturas, y puede ver los datos y la configuración de los costos. Por ejemplo, presupuestos y exportaciones. En la práctica, este ámbito de facturación de los contratos de cliente es el mismo que el del [rol RBAC de Azure de lector de Cost Management](../role-based-access-control/built-in-roles.md#cost-management-reader).
- **Creador de la suscripción de Azure**: puede crear suscripciones de Azure, ver los costos y administrar la configuración de estos. Por ejemplo, presupuestos y exportaciones. En la práctica, este ámbito de facturación de los contratos de cliente es el mismo que el del rol Propietario de la cuenta de las inscripciones de EA.

Las suscripciones de Azure están anidadas en las secciones de la factura, al igual que lo están en las cuentas de las inscripciones de EA. Los usuarios de facturación tienen acceso a los datos de costo de las suscripciones y grupos de recursos que están bajo sus respectivos ámbitos. Sin embargo, no tienen acceso para ver ni administrar recursos en Azure Portal. Para que los usuarios de facturación puedan ver los costos, deben ir a **Administración de costos + facturación** en la lista de servicios de Azure Portal. Después, pueden filtrar los costos de las suscripciones y grupos de recursos específicos sobre los cuales deben informar.

Los usuarios de facturación no tienen acceso a los grupos de administración ya que no están explícitamente incluidos en una cuenta de facturación. No obstante, cuando se habilitan los grupos de administración para la organización, todos los costos de suscripción se incluyen en la cuenta de facturación y en el grupo de administración raíz ya que ambos están limitados a un único directorio. Los grupos de administración solo incluyen compras basadas en el uso. Las compras como las reservas y las ofertas de Marketplace de terceros no están incluidas en los grupos de administración. Por lo tanto, puede que la cuenta de facturación y el grupo de administración raíz notifiquen totales diferentes. Para ver estos costos, use la cuenta de facturación o el perfil de facturación correspondiente.

## <a name="aws-scopes"></a>Ámbitos de AWS

Una vez completada la integración de AWS, consulte [Instalación y configuración de la integración de AWS](aws-integration-set-up-configure.md). Están disponibles los ámbitos siguientes:

- **Cuenta de facturación externa**: representa un contrato de cliente con un proveedor de terceros. Es similar a la cuenta de facturación del Contrato Enterprise.

    Tipo de recurso: `Microsoft.CostManagement/externalBillingAccounts`
    
- **Suscripción externa**: representa una cuenta operativa del cliente con un proveedor de terceros. Es similar a una suscripción de Azure.

    Tipo de recurso: `Microsoft.CostManagement/externalSubscriptions`

## <a name="cloud-solution-provider-csp-scopes"></a>Ámbitos de Proveedor de soluciones en la nube (CSP)

Los asociados de Proveedor de soluciones en la nube no se admiten en Cost Management actualmente. En su lugar, puede usar el [Centro de partners](https://docs.microsoft.com/azure/cloud-solution-provider/overview/partner-center-overview).

## <a name="switch-between-scopes-in-cost-management"></a>Paso de un ámbito a otro en Cost Management

Todas las vistas de Cost Management en Azure Portal incluyen un cuadro de selección de **ámbito** en la zona superior izquierda de la vista. Utilícela para cambiar rápidamente de ámbito. Haga clic en el cuadro de selección de **ámbito** para abrir el selector de ámbitos. Muestra las cuentas de facturación, el grupo de administración raíz y todas las suscripciones que no están anidadas en este grupo. Para seleccionar un ámbito, haga clic en el fondo para resaltarlo y, a continuación, haga clic en **Seleccionar** en la parte inferior. Para la exploración en profundidad de los ámbitos anidados como, por ejemplo, los grupos de recursos de una suscripción, haga clic en el vínculo del nombre del ámbito. Para seleccionar el ámbito primario en cualquier nivel anidado, haga clic en **Seleccionar este &lt;ámbito&gt;** en la parte superior del selector.

## <a name="identify-the-resource-id-for-a-scope"></a>Identificación del identificador de recurso de un ámbito

Cuando trabaja con las API de Cost Management, conocer el ámbito es fundamental. Use la siguiente información para crear el URI de ámbito adecuado para las API de Cost Management.

### <a name="billing-accounts"></a>Cuentas de facturación

1. Abra Azure Portal y, a continuación, vaya a **Administración de costos + facturación** en la lista de servicios.
2. Seleccione **Propiedades** en el menú de la cuenta de facturación.
3. Copie el identificador de la cuenta de facturación.
4. El ámbito es: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}"`

### <a name="billing-profiles"></a>Perfiles de facturación

1. Abra Azure Portal y, a continuación, vaya a **Administración de costos + facturación** en la lista de servicios.
2. Seleccione **Perfiles de facturación** en el menú de la cuenta de facturación.
3. Haga clic en el nombre del perfil de facturación deseado.
4. Seleccione **Propiedades** en el menú del perfil de facturación.
5. Copie la cuenta de facturación y los identificadores de los perfiles de facturación.
6. El ámbito es: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}"`

### <a name="invoice-sections"></a>Secciones de la factura

1. Abra Azure Portal y, a continuación, vaya a **Administración de costos + facturación** en la lista de servicios.
2. Seleccione **Secciones de factura** en el menú de la cuenta de facturación.
3. Haga clic en el nombre de la sección de factura que desee.
4. Seleccione **Propiedades** en el menú de sección de factura.
5. Copie la cuenta de facturación y los identificadores de las secciones de factura.
6. El ámbito es: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/invoiceSections/{invoiceSectionId}"`

### <a name="ea-departments"></a>Departamentos de EA

1. Abra Azure Portal y, a continuación, vaya a **Administración de costos + facturación** en la lista de servicios.
2. Seleccione **Departamentos** en el menú de la cuenta de facturación.
3. Haga clic en el nombre del departamento deseado.
4. Seleccione **Propiedades** en el menú de departamentos.
5. Copie la cuenta de facturación y los identificadores de los departamentos.
6. El ámbito es: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/departments/{departmentId}"`

### <a name="ea-enrollment-account"></a>Cuenta de inscripción de EA

1. Abra Azure Portal y vaya a **Administración de costos + facturación** en la lista de servicios.
2. Seleccione **Cuentas de inscripción** en el menú de la cuenta de facturación.
3. Haga clic en el nombre de la cuenta de inscripción que desee.
4. Seleccione **Propiedades** en el menú de la cuenta de inscripción.
5. Copie la cuenta de facturación y los identificadores de las cuentas de inscripción.
6. El ámbito es: `"/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/enrollmentAccounts/{enrollmentAccountId}"`

### <a name="management-group"></a>Grupo de administración

1. Abra Azure Portal y vaya a **Grupos de administración** en la lista de servicios.
2. Vaya al grupo de administración que desee.
3. Copie el identificador del grupo de administración de la tabla.
4. El ámbito es: `"/providers/Microsoft.Management/managementGroups/{id}"`

### <a name="subscription"></a>Subscription

1. Abra Azure Portal y vaya a **Suscripciones** en la lista de servicios.
2. Copie el identificador de suscripción de la tabla.
3. El ámbito es: `"/subscriptions/{id}"`

### <a name="resource-groups"></a>Grupos de recursos

1. Abra Azure Portal y vaya a **Grupos de recursos** en la lista de servicios.
2. Haga clic en el nombre del grupo de recursos que desee.
3. Seleccione **Propiedades** en el menú del grupo de recursos.
4. Copie el valor del campo de identificador de recurso.
5. El ámbito es: `"/subscriptions/{id}/resourceGroups/{name}"`

Cost Management es actualmente compatible con [Azure Global](https://management.azure.com) y [Azure Government](https://management.usgovcloudapi.net). Para más información sobre Azure Government, consulte los [puntos de conexión de las API de Azure Global y Azure Government](../azure-government/documentation-government-developer-guide.md#endpoint-mapping) _._

## <a name="next-steps"></a>Pasos siguientes

- Si aún no ha completado la primera guía rápida de Cost Management, léala en [Start analyzing costs](quick-acm-cost-analysis.md) (Comenzar a analizar los costos).
