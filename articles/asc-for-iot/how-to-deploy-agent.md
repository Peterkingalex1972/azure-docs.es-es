---
title: Seleccionar e implementar el agente de Azure Security Center para IoT | Microsoft Docs
description: Aprenda a seleccionar e implementar agentes de seguridad de Azure Security Center for IoT en dispositivos IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 32a9564d-16fd-4b0d-9618-7d78d614ce76
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: ffc6ea447ae90649be0455abbed6245c078e518d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68596347"
---
# <a name="select-and-deploy-a-security-agent-on-your-iot-device"></a>Seleccionar e implementar un agente de seguridad en el dispositivo IoT

Azure Security Center para IoT proporciona arquitecturas de referencia para los agentes de seguridad que supervisan y recopilan datos de dispositivos IoT.
Para más información, vea [Arquitectura de referencia del agente de seguridad](security-agent-architecture.md).

Los agentes de seguridad se desarrollan como proyectos de código abierto y están disponibles en dos tipos: <br> [C](https://aka.ms/iot-security-github-c) y [C#](https://aka.ms/iot-security-github-cs).

En este artículo, aprenderá a: 
> [!div class="checklist"]
> * Comparar tipos de agente de seguridad
> * Descubrir las plataformas de agente admitidas
> * Elegir el tipo de agente correcto para su solución

## <a name="understand-security-agent-options"></a>Descripción de las opciones de agente de seguridad

Cada tipo de agente de seguridad de Azure Security Center para IoT ofrece el mismo conjunto de características y admite unas opciones de configuración similares. 

El agente de seguridad basado en C tiene una superficie de memoria menor y es la opción ideal en dispositivos con pocos recursos disponibles. 

|     | Agente de seguridad basado en C | Agente de seguridad basado en C# |
| --- | ----------- | --------- |
| Código abierto | Disponible con [licencia de MIT](https://en.wikipedia.org/wiki/MIT_License) en [GitHub](https://aka.ms/iot-security-github-cs) | Disponible con [licencia de MIT](https://en.wikipedia.org/wiki/MIT_License) en [GitHub](https://aka.ms/iot-security-github-c) |
| Lenguaje de desarrollo    | C | C# |
| ¿Admite plataformas Windows? | Sin | Sí |
| Requisitos previos de Windows | --- | [WMI](https://docs.microsoft.com/windows/desktop/wmisdk/) |
| ¿Admite plataformas Linux? | Sí, x64 y x86 | Sí, solo x64 |
| Requisitos previos de Linux | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins | libunwind8, libcurl3, uuid-runtime, auditd, audispd-plugins, sudo, netstat, iptables |
| Superficie de disco | 10,5 MB | 90 MB |
| Superficie de memoria (promedio) | 5,5 MB | 33 MB |
| [Autenticación](concept-security-agent-authentication-methods.md) en IoT Hub | Sí | Sí |
| [Recopilación](how-to-agent-configuration.md#supported-security-events) de datos de seguridad | Sí | Sí |
| Agregación de datos | Sí | Sí |
| Configuración remota a través de un [módulo de seguridad gemelo](concept-security-module.md) | Sí | Sí |
|

## <a name="security-agent-installation-guidelines"></a>Directrices de instalación del agente de seguridad

En **Windows**: El script Install SecurityAgent.ps1 debe ejecutarse desde una ventana de administrador de PowerShell. 

En **Linux**: InstallSecurityAgent.sh debe ejecutarse como superusuario. Se recomienda colocar "sudo".como prefijo del comando de instalación.


## <a name="choose-an-agent-flavor"></a>Elegir un tipo de agente 

Responda las siguientes preguntas sobre sus dispositivos IoT para seleccionar al agente correcto:

- ¿Usa _Windows Server_ o _Windows IoT Core_? 

    [Implementación de un agente de seguridad basado en C# para Windows](how-to-deploy-windows-cs.md)

- ¿Usa una distribución Linux con arquitectura x86? 

    [Implementación de un agente de seguridad basado en C para Linux](how-to-deploy-linux-c.md)

- ¿Usa una distribución Linux con arquitectura x64?

    Puede usar cualquier tipo de agente. <br>
    [Implementación de un agente de seguridad basado en C para Linux](how-to-deploy-linux-c.md) y/o [Implementación de un agente de seguridad basado en C# para Linux](how-to-deploy-linux-cs.md)

Ambos tipos de agente ofrecen el mismo conjunto de características y admiten opciones de configuración similares.
Vea [Comparación de agentes de seguridad](how-to-deploy-agent.md#understand-security-agent-options) para obtener más información.

## <a name="supported-platforms"></a>Plataformas compatibles

La siguiente lista incluye todas las plataformas admitidas actualmente.

|Agente de Azure Security Center para IoT |Sistema operativo |Arquitectura |
|--------------|------------|--------------|
|C|Ubuntu 16.04 |   x64|
|C|Ubuntu 18.04 |   x64|
|C|Debian 9 |   x64, x86|
|C#|Ubuntu 16.04    |x64|
|C#|Ubuntu 18.04    |x64|
|C#|Debian 9    |x64|
|C#|Windows Server 2016|    X64|
|C#|Compilación 17763 de Windows 10 IoT Core |x64|
|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre las opciones de configuración, prosiga con la guía de procedimientos para configurar el agente. 
> [!div class="nextstepaction"]
> [Tutorial: Configurar agentes de seguridad](./how-to-agent-configuration.md)
