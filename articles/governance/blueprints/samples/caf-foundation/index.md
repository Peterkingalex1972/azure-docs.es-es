---
title: 'Ejemplos: Introducción al plano técnico de fundamentos de CAF'
description: Introducción y arquitectura del ejemplo de plano técnico de fundamentos de CAF.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: b78f81e16afbd044306c6697e0d8823bdd02b81a
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70232876"
---
# <a name="overview-of-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Introducción al ejemplo de plano técnico de fundamentos de Microsoft Cloud Adoption Framework para Azure

El plano técnico de fundamentos de Microsoft Cloud Adoption Framework para Azure (CAF) implementa un conjunto de recursos de infraestructura principales y controles de directiva necesarios para la primera aplicación de Azure para producción. Este plano técnico de fundamentos se basa en el patrón recomendado que se encuentra en CAF.

## <a name="architecture"></a>Arquitectura

En el ejemplo de plano técnico de fundamentos de CAF se implementan los recursos de infraestructura recomendados en Azure que pueden usar las organizaciones para poner en marcha los controles fundamentales necesarios para administrar su propia nube. En este ejemplo se implementarán y aplicarán los recursos, las directivas y las plantillas que permitirán a una organización comenzar con confianza en Azure.

![En la imagen de los fundamentos de CAF se describe lo que se instala como parte de la guía de CAF para la creación de una base para empezar a trabajar con Azure](../../media/caf-blueprints/caf-foundation-architecture.png)

Esta implementación incorpora varios servicios de Azure que se usan para proporcionar una base segura, totalmente supervisada y preparada para la empresa. Este entorno se compone de:

- Una instancia de [Azure Key Vault](../../../../key-vault/key-vault-whatis.md) utilizada para hospedar los secretos empleados por las máquinas virtuales implementadas en el entorno de servicios compartidos
- La implementación de [Log Analytics](../../../../azure-monitor/overview.md) se realiza para garantizar que todas las acciones y servicios se registran en una ubicación central desde el momento en que empieza la implementación segura en las [cuentas de almacenamiento](../../../../storage/common/storage-introduction.md) para el registro de diagnóstico.
- La implementación de [Azure Security Center](../../../../security-center/security-center-intro.md) (versión estándar) proporciona protección contra amenazas para las cargas de trabajo migradas.
- El plano técnico también define e implementa las [directivas de Azure](../../../policy/overview.md), para lo siguiente: 
  - El etiquetado (CostCenter) aplicado a los grupos de recursos
  - Los recursos anexados al grupo de recursos con la etiqueta CostCenter
  - La región de Azure permitida para recursos y grupos de recursos
  - Las SKU de la cuenta de almacenamiento permitida (se eligen durante la implementación)
  - Las SKU de las máquinas virtuales de Azure permitidas (se eligen durante la implementación)
  - La implementación de Network Watch requerida 
  - El cifrado de transferencia segura de la cuenta de almacenamiento de Azure requerido
  - Tipos de recursos denegados (se elige durante la implementación)  
- Iniciativas
  - Habilitación de la supervisión en Azure Security Center (89 directivas)

Todos estos elementos se atienen a los procedimientos probados que se publicaron en [Centro de arquitectura de Azure: Arquitecturas de referencia](/azure/architecture/reference-architectures/).

> [!NOTE]
> Los fundamentos de CAF diseñan una arquitectura básica para las cargas de trabajo.
> Deberá implementar cargas de trabajo adicionales más allá de esta arquitectura básica.

Para más información, consulte el paso de [preparación a Microsoft Cloud Adoption Framework para Azure](/azure/architecture/cloud-adoption/ready/azure-readiness-guide/govern-org-compliance?tabs=AzurePolicy).

## <a name="next-steps"></a>Pasos siguientes

Ya ha revisado la información general y la arquitectura del ejemplo de plano técnico de fundamentos de CAF.

> [!div class="nextstepaction"]
>  [Plano técnico de fundamentos de CAF: Pasos de implementación](./deploy.md)

Artículos adicionales sobre planos técnicos y cómo utilizarlos:

- Información acerca del [ciclo de vida del plano técnico](../../concepts/lifecycle.md).
- Descubra cómo utilizar [parámetros estáticos y dinámicos](../../concepts/parameters.md).
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](../../concepts/sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](../../concepts/resource-locking.md).
- Aprenda a [actualizar las asignaciones existentes](../../how-to/update-existing-assignments.md).