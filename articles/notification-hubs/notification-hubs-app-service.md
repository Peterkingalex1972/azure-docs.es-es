---
title: Integración con App Service Mobile Apps
description: Obtenga información sobre cómo funciona Azure Notification Hubs con Azure App Service Mobile Apps.
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
documentationcenter: ''
ms.assetid: 83132dff-a01d-4b31-a426-b57496852b81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: multiple
ms.devlang: multiple
ms.topic: overview
ms.custom: mvc
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: bb39949bc21ece819b7ac6e279390f8bdb0ee00a
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157453"
---
# <a name="integration-with-app-service-mobile-apps"></a>Integración con App Service Mobile Apps

> [!NOTE]
> Microsoft se compromete a proporcionar compatibilidad total con App Service Mobile Apps, incluida la compatibilidad con la última versión del sistema operativo, las correcciones de errores, las mejoras de la documentación y las revisiones de PR de la comunidad. Tenga en cuenta que el equipo del producto no está invirtiendo actualmente en ningún trabajo sobre nuevas características para Azure Mobile Apps. Valoramos significativamente las contribuciones de la comunidad a todas las áreas de Azure Mobile Apps.

Para facilitar una experiencia perfecta y unificadora entre servicios de Azure, [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) tiene compatibilidad integrada con notificaciones push mediante Notification Hubs. [App Service Mobile Apps](../app-service-mobile/app-service-mobile-value-prop.md) ofrece una plataforma de desarrollo de aplicaciones móviles altamente escalable y disponible globalmente para desarrolladores empresariales e integradores de sistemas que proporciona un amplio conjunto de funcionalidades a desarrolladores móviles.

Los desarrolladores de aplicaciones móviles pueden usar Notification Hubs con el siguiente flujo de trabajo:

1. Recuperar controlador PNS de dispositivo
2. Registrar el dispositivo con Notification Hubs a través de la API adecuada del registro del SDK de cliente de Mobile Apps

    > [!NOTE]
    > Tenga en cuenta que Mobile Apps elimina todas las etiquetas en los registros por motivos de seguridad. Trabaje con Notification Hubs desde su back-end directamente para asociar etiquetas a dispositivos.

3. Enviar notificaciones desde su back-end de aplicación con Notification Hubs

Estas son algunas ventajas para los desarrolladores de esta integración:

- **SDK de cliente de Mobile Apps**:  Estos SDK de multiplataforma ofrecen API simples para el registro y se comunican con el centro de notificaciones vinculado con la aplicación móvil automáticamente. Los desarrolladores no tienen que indagar en las credenciales de Notification Hubs y trabajar con un servicio adicional.
  - *Inserción en usuario*: Los SDK etiquetan automáticamente el dispositivo específico con un identificador de usuario autenticado de Mobile Apps para habilitar la inserción en un escenario de usuario.
  - *Inserción en dispositivo*: Los SDK utilizan automáticamente el identificador de instalación de Mobile Apps como GUID para registrarse con Notification Hubs, lo que permite que los desarrolladores no tengan que mantener varios GUID de servicio.
- **Modelo de instalación**: Mobile Apps funciona con el modelo de inserción más reciente de Notification Hubs para representar todas las propiedades de inserción asociadas a un dispositivo en una instalación de JSON que se alinea con los servicios de notificaciones push y resulta fácil de usar.
- **Flexibilidad**: Los desarrolladores siempre pueden elegir trabajar con Notification Hubs directamente, incluso con la integración implementada.
- **Experiencia integrada en [Azure Portal](https://portal.azure.com)**: La inserción como capacidad se representa visualmente en Mobile Apps y los desarrolladores pueden trabajar fácilmente con el centro de notificaciones asociado a través de Mobile Apps.
