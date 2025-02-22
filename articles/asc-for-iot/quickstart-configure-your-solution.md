---
title: Configuración de la solución Azure Security Center para IoT | Microsoft Docs
description: Aprenda a configurar una solución de IoT de un extremo a otro mediante Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: ae2207e8-ac5b-4793-8efc-0517f4661222
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 15c8aabbcb19d009bb202d111dc7f80da4cebff1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597034"
---
# <a name="quickstart-configure-your-iot-solution"></a>Inicio rápido: Configuración de una solución de IoT

En este artículo se explica cómo realizar la configuración inicial de una solución de seguridad de IoT mediante Azure Security Center para IoT. 

## <a name="azure-security-center-for-iot"></a>Preguntas más frecuentes de Azure Security Center for IoT

Azure Security Center para IoT proporciona una seguridad completa, de un extremo a otro, para las soluciones de IoT que usen Azure.

Con Azure Security Center para IoT se puede supervisar toda una solución de IoT desde un solo panel, así como presentar todos los dispositivos de IoT, las plataformas de IoT y los recursos de back-end de Azure.

Una vez que se habilita en IoT Hub, Azure Security Center para IoT identifica automáticamente otros servicios de Azure, que también estén conectados a IoT Hub y que estén relacionados con la solución de IoT.

Además de la detección automática de relaciones, se pueden elegir los otros grupos de recursos de Azure que se etiquetan como parte de la solución de IoT.

Las opciones que se seleccionen permiten agregar suscripciones completas, grupos de recursos o recursos individuales.

Después de definir todas las relaciones de los recursos, Azure Security Center para IoT aprovecha Azure Security Center para generar alertas y recomendaciones de seguridad relativas a estos recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adición de recursos de Azure a una solución de IoT

Para agregar un recurso nuevo a la solución de IoT, siga estos pasos: 

1. Abra **IoT Hub** en Azure Portal. 
2. Seleccione y abra **Resources** (Recursos) en **Security**  (Seguridad) en el menú izquierdo. 
3. Seleccione **Editar** y elija los grupos de recursos que pertenecen a la solución de IoT.
5. Haga clic en **Agregar**. 

Felicidades. Ha agregado un nuevo grupo de recursos a la solución de IoT.

Azure Security Center para IoT ahora supervisa los grupos de recursos que acaba de agregar y muestra alertas y recomendaciones de seguridad pertinentes como parte de la solución de IoT.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear módulos de seguridad...

> [!div class="nextstepaction"]
> [Creación de módulos de seguridad](quickstart-create-security-twin.md)