---
title: archivo de inclusión
description: archivo de inclusión
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/07/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: a843821a7720ddcb17f8a369d012facd7ba81e35
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68912491"
---
<!-- This contains intro text for the "Get an IoT hub connection string" section in the iot-hub-lang-lang-twin-getstarted.md files-->

En este artículo, se crea un servicio back-end que agrega propiedades deseadas a un dispositivo gemelo y luego consulta el registro de identidades para buscar todos los dispositivos con propiedades notificadas que se han actualizado en consecuencia. El servicio necesita el permiso **Conectar al servicio** para modificar las propiedades deseadas de un dispositivo gemelo y el permiso **Lectura del Registro** para consultar el registro de identidades. No hay ninguna directiva de acceso compartido predeterminada que contenga solo estos dos permisos, por lo que tendrá que crearla.
