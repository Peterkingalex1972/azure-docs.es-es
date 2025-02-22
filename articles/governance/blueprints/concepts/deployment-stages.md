---
title: Etapas de implementación de un plano técnico
description: Obtenga información sobre los pasos que siguen los servicios de Azure Blueprints durante la implementación.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d7000813b51fb9c9aae9a21cbded3ae0028e83f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60684699"
---
# <a name="stages-of-a-blueprint-deployment"></a>Etapas de implementación de un plano técnico

Cuando se implementa un plano técnico, el servicio de Azure Blueprints lleva a cabo una serie de acciones para implementar los recursos definidos en el plano. En este artículo se proporcionan detalles sobre lo que implica cada paso.

La implementación del plano técnico se desencadena mediante la asignación de un plano a una suscripción o [la actualización de una asignación existente](../how-to/update-existing-assignments.md). Durante la implementación, Azure Blueprints realiza los siguientes pasos generales:

> [!div class="checklist"]
> - Concesión a Blueprints de derechos de propietario
> - Creación del objeto de asignación del plano técnico
> - Opcional: creación por Blueprints de la identidad administrada **asignada por el sistema**
> - Implementación de artefactos del plano técnico por la identidad administrada
> - Revocación de derechos de identidad administrada **asignada por el sistema** y del servicio Blueprints

## <a name="blueprints-granted-owner-rights"></a>Concesión a Blueprints de derechos de propietario

La entidad de servicio de Azure Blueprints recibe derechos de propietario en relación con la suscripción o las suscripciones asignadas. El rol concedido permite a Blueprints crear y, más adelante revocar, la [identidad administrada asignada por el sistema](../../../active-directory/managed-identities-azure-resources/overview.md).

Los derechos se conceden automáticamente si la asignación se realiza a través del portal. Sin embargo, si la asignación se realiza a través de la API REST, la concesión de derechos debe hacerse con una llamada API independiente. El valor de AppId de Azure Blueprints es `f71766dc-90d9-4b7d-bd9d-4499c4331c3f`, pero el identificador de la entidad de servicio varía según el inquilino. Use la [Graph API de Azure Active Directory](../../../active-directory/develop/active-directory-graph-api.md) y el punto de conexión REST [servicePrincipals](/graph/api/resources/serviceprincipal) para obtener la entidad de servicio. A continuación, conceda a Azure Blueprints el rol _Propietario_ a través del [Portal](../../../role-based-access-control/role-assignments-portal.md), la [CLI de Azure](../../../role-based-access-control/role-assignments-cli.md), [Azure PowerShell](../../../role-based-access-control/role-assignments-powershell.md), la [API REST](../../../role-based-access-control/role-assignments-rest.md) o una [plantilla de Resource Manager](../../../role-based-access-control/role-assignments-template.md).

El servicio Blueprints no implementa directamente los recursos.

## <a name="the-blueprint-assignment-object-is-created"></a>Creación del objeto de asignación del plano técnico

Un usuario, grupo o entidad de servicio asigna un plano técnico a una suscripción. El objeto de asignación existe en el nivel de suscripción donde se asignó el plano técnico. Los recursos creados por la implementación no se generan en el contexto de la entidad de implementación.

Al crear la asignación del plano técnico, se selecciona el tipo de [identidad administrada](../../../active-directory/managed-identities-azure-resources/overview.md). El valor predeterminado es una identidad administrada **asignada por el sistema**. Se puede elegir una identidad administrada **asignada por el usuario**. Cuando se usa una identidad administrada **asignada por el usuario**, deben definirse y concederse los permisos antes de la creación de la asignación del plano técnico.

## <a name="optional---blueprints-creates-system-assigned-managed-identity"></a>Opcional: creación por Blueprints de la identidad administrada asignada por el sistema

Cuando se selecciona una [identidad administrada asignada por el sistema](../../../active-directory/managed-identities-azure-resources/overview.md) durante la asignación, Blueprints crea la identidad y concede a la identidad administrada el rol de [propietario](../../../role-based-access-control/built-in-roles.md#owner). Si se [actualiza una asignación existente](../how-to/update-existing-assignments.md), Blueprints utiliza la identidad administrada creada anteriormente.

La identidad administrada relacionada con la asignación del plano técnico se usa para implementar o volver a implementar los recursos definidos en el plano técnico. Este diseño evita que las asignaciones interfieran de forma inadvertida entre sí.
Este diseño también admite la característica del [bloqueo de recursos](./resource-locking.md) mediante el control de la seguridad de cada recurso implementado desde el plano técnico.

## <a name="the-managed-identity-deploys-blueprint-artifacts"></a>Implementación de artefactos del plano técnico por la identidad administrada

La identidad administrada, a continuación, desencadena las implementaciones de Resource Manager de los artefactos en el plano en el [orden secuenciación](./sequencing-order.md) definido. El orden se puede ajustar para garantizar que los artefactos dependientes de otros artefactos se implementan en el orden correcto.

Un error de acceso de una implementación suele ser el resultado del nivel de acceso concedido a la identidad administrada. El servicio Blueprints administra el ciclo de vida de la seguridad de la identidad administrada **asignada por el sistema**. Sin embargo, el usuario es responsable de administrar los derechos y el ciclo de vida de una identidad administrada **asignada por el usuario**.

## <a name="blueprint-service-and-system-assigned-managed-identity-rights-are-revoked"></a>Revocación de derechos de identidad administrada asignada por el sistema y del servicio Blueprints

Una vez que se completan las implementaciones, Blueprints revoca los derechos de la identidad administrada **asignada por el sistema** de la suscripción. A continuación, el servicio Blueprints revoca sus derechos de la suscripción. La eliminación de derechos evita que Blueprints se convierta en un propietario permanente en una suscripción.

## <a name="next-steps"></a>Pasos siguientes

- Descubra cómo utilizar [parámetros estáticos y dinámicos](parameters.md).
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](resource-locking.md).
- Aprenda a [actualizar las asignaciones existentes](../how-to/update-existing-assignments.md).
- Puede consultar la información de [solución de problemas generales](../troubleshoot/general.md) para resolver los problemas durante la asignación de un plano técnico.