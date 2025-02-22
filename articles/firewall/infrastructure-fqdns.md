---
title: FQDN de infraestructura para Azure Firewall
description: Obtenga información sobre los FQDN de infraestructura en Azure Firewall
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: 34201a0eb4139de64261f77f285096a2aa2dd3aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61066333"
---
# <a name="infrastructure-fqdns"></a>FQDN de infraestructura

Azure Firewall incluye una colección de reglas integradas para FQDN de infraestructura que están permitidos de forma predeterminada. Estos FQDN son específicos para la plataforma y no se pueden usar para otros fines. 

En la colección integrada de reglas se incluyen los siguientes servicios:

- Acceso de proceso al repositorio de imágenes de la plataforma (PIR) de almacenamiento
- Acceso al almacenamiento de estado de los discos administrados
- Azure Diagnostics y registro (MDS)
- Azure Active Directory

## <a name="overriding"></a>Invalidación 

Puede invalidar esta colección integrada de reglas de infraestructura creando una colección de reglas de aplicación Denegar todo que se procese en último lugar. Se procesará siempre antes que la colección de reglas de infraestructura. Cualquier cosa que no esté en la colección de reglas de infraestructura se denegará de forma predeterminada.

## <a name="next-steps"></a>Pasos siguientes

- Consulte el tutorial [Implementación y configuración de Azure Firewall](tutorial-firewall-deploy-portal.md).