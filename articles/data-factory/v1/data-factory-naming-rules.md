---
title: Reglas para asignar nombres a entidades de Azure Data Factory | Microsoft Docs
description: Describe las reglas de nomenclatura para las entidades de Factoría de datos.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: bc5e801d-0b3b-48ec-9501-bb4146ea17f1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: daf0b1c12ab10230690a62eb5dc772417d8b92f3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60824242"
---
# <a name="azure-data-factory---naming-rules"></a>Azure Data Factory: reglas de nomenclatura
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte las [reglas de nomenclatura de Data Factory](../naming-rules.md).

La tabla siguiente proporciona las reglas de nomenclatura para los artefactos de Factoría de datos.

| NOMBRE | Exclusividad del nombre | Comprobaciones de validación |
|:--- |:--- |:--- |
| Data Factory |Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas, es decir, `MyDF` y `mydf` hacen referencia a la misma factoría de datos. |<ul><li>Cada factoría de datos está asociada exactamente a una suscripción de Azure.</li><li>Los nombres de objeto deben comenzar por una letra o un número, y pueden contener solo letras, números y el carácter de guión (-).</li><li>Los caracteres de guión (-) debe estar inmediatamente precedidos y seguidos por una letra o un número. No se permiten guiones consecutivos en los nombres de contenedor.</li><li>El nombre puede tener entre 3 y 63 caracteres.</li></ul> |
| Servicios, tablas o canalizaciones vinculados |Único en una factoría de datos. Los nombres no distinguen mayúsculas de minúsculas. |<ul><li>Número máximo de caracteres incluido en un nombre de tabla: 260.</li><li>Los nombres de objeto deben empezar con una letra, un número o un carácter de subrayado (_).</li><li>No se permiten los caracteres siguientes: “.”, “+”, “?”, “/”, “<”, ”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> |
| Grupo de recursos |Único en Microsoft Azure. Los nombres no distinguen mayúsculas de minúsculas. |<ul><li>Número máximo de caracteres: 1000.</li><li>El nombre puede contener letras, dígitos y los siguientes caracteres: “-”, “_”, “,” y “.”</li></ul> |

