---
title: 'Procedimientos recomendados para usar Key Vault: Azure Key Vault | Microsoft Docs'
description: En este documento se explican algunos de los procedimientos recomendados para utilizar Key Vault.
services: key-vault
author: msmbaldwin
manager: barbkess
tags: azure-key-vault
ms.service: key-vault
ms.topic: conceptual
ms.date: 03/07/2019
ms.author: mbaldwin
ms.openlocfilehash: eb7150d0b1c3a4a312b0c05ba7612960aaf640f6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65227942"
---
# <a name="best-practices-to-use-key-vault"></a>Procedimientos recomendados para utilizar Key Vault

## <a name="control-access-to-your-vault"></a>Controlar el acceso al almacén

Azure Key Vault es un servicio en la nube que protege las claves de cifrado y los secretos, como certificados, cadenas de conexión y contraseñas. Dado que estos datos son confidenciales y críticos para la empresa, debe proteger el acceso a los almacenes de claves, de modo que solo se admita el acceso de las aplicaciones y los usuarios autorizados. En este [artículo](key-vault-secure-your-key-vault.md) se proporciona información general sobre modelo de acceso de Key Vault. Se explican la autenticación y la autorización, y se describe cómo proteger el acceso a los almacenes de claves.

Estas son algunas sugerencias a la hora de controlar el acceso al almacén:
1. Bloquee el acceso a la suscripción, al grupo de recursos y a los distintos Key Vault (RBAC)
2. Cree directivas de acceso para cada almacén.
3. Establezca la entidad de seguridad de acceso con privilegios mínimos para conceder el acceso.
4. Active el Firewall y los [puntos de conexión de servicio de red virtual](key-vault-overview-vnet-service-endpoints.md).

## <a name="use-separate-key-vault"></a>Usar un Key Vault independiente

Nuestra recomendación es usar un almacén por cada aplicación y cada entorno (desarrollo, preproducción y producción). Esto ayuda a no compartir secretos entre los entornos y, también, a reducir la amenaza en el caso de infracción.

## <a name="backup"></a>Copia de seguridad

Asegúrese de hacer copias de seguridad del [almacén](https://blogs.technet.microsoft.com/kv/2018/07/20/announcing-backup-and-restore-of-keys-secrets-and-certificates/) periódicamente, cuando actualice, elimine o cree objetos dentro de un almacén.

## <a name="turn-on-logging"></a>Activar el registro

[Active el registro](key-vault-logging.md) en el almacén. De igual modo, configure alertas.

## <a name="turn-on-recovery-options"></a>Activar las opciones de recuperación

1. Active la [eliminación temporal](key-vault-ovw-soft-delete.md).
2. Active la protección de purgas si quiere tener protección frente a posibles eliminaciones forzadas de secretos o del almacén, incluso con la eliminación temporal activada.
