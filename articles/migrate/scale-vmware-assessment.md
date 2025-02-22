---
title: Evaluación de grandes cantidades de VM de VMware para la migración a Azure con Azure Migrate | Microsoft Docs
description: Describe cómo evaluar grandes cantidades de VM de VMware para su migración a Azure mediante el servicio Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: hamusa
ms.openlocfilehash: c9c57a07100f2ea6db86408826bf74d05c8df5aa
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2019
ms.locfileid: "67868678"
---
# <a name="assess-large-numbers-of-vmware-vms-for-migration-to-azure"></a>Evaluación de grandes cantidades de VM de VMware para la migración a Azure


En este artículo se describe cómo evaluar grandes cantidades (> 1000-35 000) de VM de VMware locales para su migración a Azure mediante la herramienta de evaluación del servidor de Azure Migrate.

[Azure Migrate](migrate-services-overview.md) proporciona un centro de herramientas que le ayuda a detecta las aplicaciones, la infraestructura y las cargas de trabajo, a evaluarlas y a migrarlas a Microsoft Azure. Este centro incluye herramientas de Azure Migrate y ofertas de fabricantes de software independientes (ISV) de terceros. 

En este artículo, aprenderá a:
> [!div class="checklist"]
> * Planificar la evaluación a escala.
> * Configurar los permisos de Azure y preparar VMware para la evaluación.
> * Crear un proyecto de Azure Migrate y crear una evaluación.
> * Revisar la evaluación mientras planea la migración.


> [!NOTE]
> Si quiere probar una prueba de concepto para evaluar un par de VM antes de realizar la evaluación a escala, siga nuestra [serie de tutoriales](tutorial-prepare-vmware.md).

## <a name="plan-for-assessment"></a>Planeación de la evaluación

Al planear la evaluación de un gran número de VM de VMware, hay un par de cosas que hay que tener en cuenta:

- **Planeación de proyectos de Azure Migrate**: Averigüe cómo implementar proyectos de Azure Migrate. Por ejemplo, si los centros de datos están en zonas geográficas diferentes, o si necesita almacenar metadatos relacionados con la detección, la evaluación o la migración en una zona geográfica diferente, es posible que necesite varios proyectos. 
- **Planear dispositivos**: Azure Migrate usa un dispositivo de Azure Migrate local, implementado como VM de VMware, para detectar continuamente VM. El dispositivo supervisa los cambios de entorno, como la incorporación de VM, discos o adaptadores de red. También envía metadatos y datos de rendimiento acerca de ellos a Azure. Debe averiguar el número de dispositivos que necesitará implementar.
- **Planear cuentas para la detección**: El dispositivo Azure Migrate usa una cuenta con acceso a vCenter Server para detectar VM con fines de evaluación y migración. Si detecta más de 10 000 VM, configure varias cuentas.


## <a name="planning-limits"></a>Límites de planeación
 
Use los límites resumidos en esta tabla para la planeación.

**Planeamiento** | **Límites**
--- | --- 
**Proyectos de Azure Migrate** | Evalúe hasta 35 000 VM en un proyecto.
**Dispositivo con Azure Migrate** | Un dispositivo solo puede conectarse a una instancia de vCenter Server.<br/><br/> Un dispositivo solo se puede asociar con un único proyecto de Azure Migrate.<br/> Un dispositivo puede detectar hasta 10 000 VM en una instancia de vCenter Server.
**Evaluación de Azure Migrate** | Puede acceder hasta a 35 000 máquinas virtuales en una única evaluación.

Teniendo en cuenta estos límites, estas son algunas implementaciones de ejemplo:


**Servidor vCenter** | **VM en el servidor** | **Recomendación** | **Acción**
---|---|---
Uno | < 10 000 | Un proyecto de Azure Migrate.<br/> Un dispositivo.<br/> Una cuenta de vCenter para la detección. | Configure el dispositivo y conéctese a vCenter Server con una cuenta.
Uno | 10 000 | Un proyecto de Azure Migrate.<br/> Varios dispositivos.<br/> Varias cuentas de vCenter. | Configure el dispositivo para cada 10 000 VM.<br/><br/> Configure cuentas de vCenter y divida el inventario para limitar el acceso de una cuenta a menos de 10 000 VM.<br/> Conecte cada dispositivo a vCenter Server con una cuenta.<br/> Puede analizar las dependencias entre las máquinas que se detectan con diferentes dispositivos.
Múltiple | < 10 000 |  Un proyecto de Azure Migrate.<br/> Varios dispositivos.<br/> Una cuenta de vCenter para la detección. | Configure los dispositivos y conéctese a vCenter Server con una cuenta.<br/> Puede analizar las dependencias entre las máquinas que se detectan con diferentes dispositivos.
Múltiple | > 10 000 | Un proyecto de Azure Migrate.<br/> Varios dispositivos.<br/> Varias cuentas de vCenter. | Si vCenter Server detectó menos de 10 000 VM, configure un dispositivo para cada instancia de vCenter Server.<br/><br/> Si vCenter Server detectó más de 10 000 VM, configure un dispositivo para cada 10 000 VM.<br/> Configure cuentas de vCenter y divida el inventario para limitar el acceso de una cuenta a menos de 10 000 VM.<br/> Conecte cada dispositivo a vCenter Server con una cuenta.<br/> Puede analizar las dependencias entre las máquinas que se detectan con diferentes dispositivos.


## <a name="plan-discovery-in-a-multi-tenant-environment"></a>Planeación de la detección en un entorno de varios inquilinos

Si está planeando un entorno de varios inquilinos, puede limitar la detección en vCenter Server.

- Puede establecer el ámbito de detección del dispositivo en un centro de datos de vCenter Server, varios clústeres o una carpeta de clústeres, varios hosts o una carpeta de hosts, o VM individuales.
- Si el entorno se comparte entre inquilinos y quiere detectar cada inquilino por separado, puede limitar el acceso a la cuenta de vCenter que el dispositivo usa para la detección. 
    - Puede que quiera limitarse a las carpetas de VM si los inquilinos comparten hosts. Azure Migrate no puede detectar VM si a la cuenta de vCenter se le ha concedido acceso en el nivel de carpeta de VM de vCenter. Si tiene intención de limitar el ámbito de detección por carpetas de VM, asegúrese de que la cuenta de vCenter tiene asignado acceso de solo lectura asignado a nivel de VM. [Aquí](tutorial-assess-vmware.md#scoping-discovery) encontrará más información sobre el ámbito de la detección.

## <a name="prepare-for-assessment"></a>Preparación para la evaluación

Preparar Azure y VMware para la evaluación del servidor. 

1. Consulte [los requisitos y las limitaciones de compatibilidad de VMware](migrate-support-matrix-vmware.md).
2. Configure los permisos de la cuenta de Azure para interactuar con Azure Migrate.
3. Preparar VMware para la evaluación.

Siga las instrucciones de [este tutorial](tutorial-prepare-vmware.md) para definir estas configuraciones.


## <a name="create-a-project"></a>Crear un proyecto

Según los requisitos de planeación, haga lo siguiente:

1. Cree un proyecto de Azure Migrate.
2. Agregue la herramienta de evaluación del servidor de Azure Migrate a los proyectos.

[Más información](how-to-add-tool-first-time.md)

## <a name="create-and-review-an-assessment"></a>Creación y revisión de una evaluación

1. Cree evaluaciones para VM de VMware.
1. Revise las evaluaciones en preparación para la planeación de la migración.


Siga las instrucciones de [este tutorial](tutorial-assess-vmware.md) para definir estas configuraciones.
    

## <a name="next-steps"></a>Pasos siguientes

En este artículo:
 
> [!div class="checklist"] 
> * Planeó escalar evaluaciones de Azure Migrate para VM de VMware
> * Preparó Azure y VMware para la evaluación
> * Creó un proyecto de Azure Migrate y ejecutó las evaluaciones
> * Revisó las evaluaciones como preparación para la migración.

Ahora, [obtenga información](concepts-assessment-calculation.md) sobre cómo se calculan las evaluaciones y cómo [modificarlas](how-to-modify-assessment.md).
