---
title: Introducción a Azure Blueprint
description: Conozca el modo en que el servicio Azure Blueprints permite crear, definir e implementar artefactos en el entorno de Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/08/2019
ms.topic: overview
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: be7e3043172f988a429bbf02dec08bfbbc1a70b7
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/08/2019
ms.locfileid: "68848433"
---
# <a name="overview-of-the-azure-blueprints-service"></a>Introducción al servicio Azure Blueprints

Del mismo modo que un plano técnico permite a un ingeniero o a un arquitecto bosquejar los parámetros de diseño de un proyecto, Azure Blueprint permite a los grupos de arquitectos de la nube y de TI central definir un conjunto repetible de recursos de Azure que implementa y cumple los estándares de la organización, sus requisitos y sus patrones. Azure Blueprint permite a los equipos de desarrollo aprovisionar y crear rápidamente nuevos entornos sabiendo que se crean cumpliendo los estándares organizativos y que contienen un conjunto de componentes integrados, como las redes, para acelerar el desarrollo y la entrega.

Los planos técnicos son una manera declarativa de organizar la implementación de varias plantillas de recursos y de otros artefactos, como son:

- Asignaciones de roles
- Asignaciones de directiva
- Plantillas del Administrador de recursos de Azure
- Grupos de recursos

El servicio Azure Blueprints está respaldado por la distribución global [Azure Cosmos DB](../../cosmos-db/introduction.md).
Los objetos de plano técnico se replican en varias regiones de Azure. Esta replicación proporciona baja latencia, alta disponibilidad y acceso coherente a los objetos de plano técnico, con independencia de la región en la que Blueprints implemente el recurso.

## <a name="how-its-different-from-resource-manager-templates"></a>En qué difiere de las plantillas de Resource Manager

El servicio está diseñado para ayudar con la _configuración del entorno_. A menudo, esta configuración consta de un conjunto de grupos de recursos, directivas, asignaciones de roles e implementaciones de plantillas de Resource Manager. Un plano es un paquete para reunir cada uno de estos tipos de _artefacto_ y permite componer y crear la versión de ese paquete, incluso mediante una canalización CI/CD. En última instancia, cada uno se asigna a una suscripción en una única operación que se puede auditar y seguir.

Casi todo lo que se puede querer incluir para la implementación en un plano técnico se puede conseguir con una plantilla de Resource Manager. Sin embargo, una plantilla de Resource Manager es un documento que no existe de forma nativa en Azure, cada una se almacena localmente o en el control de código fuente. La plantilla se usa para las implementaciones de uno o varios recursos de Azure pero, una vez implementados los recursos, no hay conexión activa ni relación con la plantilla.

Con Azure Blueprint, la relación entre la definición del plano técnico (lo _que debe ser_ implementado) y su asignación (lo _que se ha_ implementado) permanece. Esta conexión permite un seguimiento mejorado y la auditoría de las implementaciones. Los planos técnicos también pueden actualizar varias suscripciones a la vez que se rigen por el mismo plano técnico.

No hay que elegir entre una plantilla de Resource Manager y un plano técnico. Cada instancia del plano técnico puede tener o no _artefactos_ de una plantilla de Resource Manager. Esto significa que se pueden reutilizar los trabajos anteriores para desarrollar y mantener una biblioteca de plantillas de Resource Manager en Azure Blueprint.

## <a name="how-its-different-from-azure-policy"></a>¿En qué difiere de Azure Policy?

Un plano técnico es un paquete o contenedor para crear conjuntos muy específicos de estándares, patrones y requisitos relacionados con la implementación de los servicios en la nube de Azure, la seguridad y el diseño que se pueden reutilizar para garantizar la coherencia y el cumplimiento normativo.

Una [directiva](../policy/overview.md) es un sistema de denegación explícita y permisos predeterminados que se centra en las propiedades de recursos durante su implementación y para los recursos ya existentes. Admite la gobernanza de la nube al validar que los recursos de una suscripción cumplen los requisitos y estándares.

La inclusión de una directiva en un plano técnico permite la creación del diseño o el patrón correcto durante la asignación del plano técnico. La inclusión de la directiva garantiza que solo se pueden realizar en el entorno cambios aprobados o esperados para proteger el cumplimiento continuo en el ámbito del plano técnico.

Se puede incluir una directiva como uno de muchos _artefactos_ en una definición de plano técnico. Los planos técnicos también permiten usar parámetros con las directivas y las iniciativas.

## <a name="blueprint-definition"></a>Definición del plano técnico

Un plano técnico se compone de _artefactos_. Los planos técnicos actualmente admiten los siguientes recursos como artefactos:

|Recurso  | Opciones de la jerarquía| DESCRIPCIÓN  |
|---------|---------|---------|
|Grupos de recursos | Subscription | Cree un nuevo grupo de recursos para que lo usen otros artefactos incluidos en el plano técnico.  Estos grupos de recursos de marcador de posición permiten organizar los recursos exactamente como desee que se estructuren y proporciona un limitador de ámbito para los artefactos de asignación de roles y directivas, así como plantillas de Azure Resource Manager. |
|Plantilla del Administrador de recursos de Azure | Suscripción, grupo de recursos | Las plantillas se utilizan para crear entornos complejos. Ejemplo de entornos: una granja de servidores SharePoint, Azure Automation State Configuration o un área de trabajo de Log Analytics. |
|Asignación de directiva | Suscripción, grupo de recursos | Permite la asignación de una directiva o iniciativa a la suscripción a la que está asignado el plano técnico. La directiva o iniciativa debe estar dentro del ámbito de la ubicación de la definición del plano técnico. Si la directiva o iniciativa tiene parámetros, estos se asignan en la creación del plano técnico o durante su asignación. |
|Asignación de roles | Suscripción, grupo de recursos | Agregue un grupo o usuario existente a un rol integrado para asegurarse de que las personas adecuadas siempre tienen derechos de acceso a los recursos. Las asignaciones de roles se pueden definir para toda la suscripción o anidarse para un grupo de recursos específico incluido en el plano técnico. |

### <a name="blueprint-definition-locations"></a>Ubicaciones de definición de plano técnico

Al crear una definición de plano técnico, definirá dónde se guarda. Los planos técnicos se pueden guardar en un [grupo de administración](../management-groups/overview.md) o suscripción a los que tenga acceso de **colaborador**. Si la ubicación es un grupo de administración, el plano técnico está disponible para asignarlo a cualquier suscripción secundaria de ese grupo de administración.

### <a name="blueprint-parameters"></a>Parámetros de plano técnico

Los planos técnicos pueden pasar parámetros a una iniciativa o directiva, o a una plantilla de Azure Resource Manager.
Cuando algún _artefacto_ se agrega a un plano técnico, el autor es capaz de decidir si proporcionar un valor definido para cada asignación de plano técnico o permitir que cada asignación del plano técnico proporcione un valor en el momento de la asignación. Esta flexibilidad permite definir un valor predeterminado para todos los usos del plano técnico o que esa decisión se tome en el momento de la asignación.

> [!NOTE]
> Un plano técnico puede tener sus propios parámetros, pero actualmente solo se pueden crear si el plano técnico se genera desde la API REST en lugar de mediante el Portal.

Para más información, consulte el artículo sobre los [parámetros de un plano técnico](./concepts/parameters.md).

### <a name="blueprint-publishing"></a>Publicación de planos técnicos

Cuando se crea por primera vez un plano técnico, se considera que está en modo **borrador**. Cuando está preparado para asignarse, se debe **publicar**. La publicación requiere la definición de una cadena de **versión** (letras, números y guiones con una longitud máxima de 20 caracteres) junto con **notas de cambio** opcionales. La **versión** la diferencia de los cambios futuros del mismo plano técnico y permite la asignación de cada versión. Esto también significa que a la misma suscripción se le pueden asignar **versiones** diferentes del mismo plano técnico. Cuando se realizan cambios adicionales en el plano técnico, la **versión** **publicada** sigue existiendo, al igual que los **cambios sin publicar**. Una vez completados los cambios, el plano técnico actualizado se **publica** con una **versión** nueva y única, y ahora también se puede asignar.

## <a name="blueprint-assignment"></a>Asignación de plano técnico

Cada **versión** **publicada** de un plano técnico se puede asignar a una suscripción existente (con una longitud máxima del nombre de 90 caracteres). En el portal, el plano técnico tiene de forma predeterminada la **versión** **publicada** más recientemente. Si hay parámetros de artefacto (o parámetros de plano técnico), se definirán durante el proceso de asignación.

## <a name="permissions-in-azure-blueprints"></a>Permisos de Azure Blueprint

Para poder utilizar planos técnicos, se le deben conceder permisos mediante el [control de acceso basado en rol](../../role-based-access-control/overview.md) (RBAC). Para crear planos técnicos, su cuenta necesita los siguientes permisos:

- `Microsoft.Blueprint/blueprints/write`: para crear una definición de plano técnico
- `Microsoft.Blueprint/blueprints/artifacts/write`: para crear artefactos en una definición de plano técnico
- `Microsoft.Blueprint/blueprints/versions/write`: para publicar un plano técnico

Para eliminar planos técnicos, su cuenta necesita los siguientes permisos:

- `Microsoft.Blueprint/blueprints/delete`
- `Microsoft.Blueprint/blueprints/artifacts/delete`
- `Microsoft.Blueprint/blueprints/versions/delete`

> [!NOTE]
> Los permisos de definición de plano técnico deben ser concedidos o heredados en el ámbito de la suscripción o grupo de administración donde se guarda.

Para asignar o cancelar la asignación de un plano técnico, la cuenta necesita los siguientes permisos:

- `Microsoft.Blueprint/blueprintAssignments/write`: para asignar un plano técnico
- `Microsoft.Blueprint/blueprintAssignments/delete`: para cancelar la asignación de un plano técnico

> [!NOTE]
> Como las asignaciones de planos técnicos se crean en una suscripción, los permisos de asignación y de cancelación de la asignación deben concederse o heredarse en el ámbito de una suscripción.

Todos los permisos anteriores se incluyen en el rol **Propietario**. El rol **Colaborador** ha creado permisos para crear y eliminar planos técnicos, pero no tiene permisos de asignación de planos técnicos. Si estos roles integrados no satisfacen sus necesidades de seguridad, considere la posibilidad de crear un [rol personalizado](../../role-based-access-control/custom-roles.md).

> [!NOTE]
> Si usa una identidad administrada asignada por el sistema, la entidad de servicio para Azure Blueprints requiere el rol **Propietario** en la suscripción asignada con el fin de habilitar la implementación. Si usa el portal, este rol se le concede automáticamente y se revoca para la implementación. Si usa la API REST, este rol se debe conceder manualmente, pero se sigue revocando de forma automática una vez finalizada la implementación. Si usa una identidad administrada asignada por el usuario, solo el usuario que crea la asignación de plano técnico necesita permisos de **propietario**.

## <a name="naming-limits"></a>Límites de nomenclatura

Esta es una lista de las limitaciones que existen para determinados campos:

|Object|Campo|Caracteres permitidos|Máx. Length|
|-|-|-|-|
|Plano técnico|NOMBRE|letras, números, guiones y puntos|48|
|Plano técnico|Versión|letras, números, guiones y puntos|20|
|Asignación de plano técnico|NOMBRE|letras, números, guiones y puntos|90|
|Artefactos de plano técnico|NOMBRE|letras, números, guiones y puntos|48|

## <a name="video-overview"></a>Introducción en vídeo

La siguiente introducción sobre Azure Blueprints es de Azure Fridays. Para descargar el vídeo, visite [Azure Fridays - An overview of Azure Blueprints](https://channel9.msdn.com/Shows/Azure-Friday/An-overview-of-Azure-Blueprints) (Azure Fridays - Introducción a Azure Blueprints) en Channel 9.

> [!VIDEO https://www.youtube.com/embed/cQ9D-d6KkMY]

## <a name="next-steps"></a>Pasos siguientes

- [Creación de un plano técnico: portal](create-blueprint-portal.md)
- [Creación de un plano técnico: API REST](create-blueprint-rest-api.md)
