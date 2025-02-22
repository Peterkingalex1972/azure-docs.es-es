---
title: 'Introducción a App Service en Linux: Azure | Microsoft Docs'
description: Información acerca de Azure App Service en Linux.
keywords: azure app service, linux, oss
services: app-service
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 7c00946ed9de88df43a4435c23ebef27b5c64e5c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70071323"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Introducción a Azure App Service en Linux

[Azure App Service](../overview.md) es una plataforma de procesos completamente administrada que se ha optimizado para el hospedaje de sitios y aplicaciones web. Los clientes pueden usar App Service en Linux para hospedar aplicaciones web de forma nativa en Linux para pilas de aplicaciones admitidas. En la sección [Lenguajes](#languages) se muestran las pilas de aplicaciones que son compatibles actualmente.

## <a name="languages"></a>Languages

App Service en Linux es compatible con un toda una serie de imágenes integradas para aumentar la productividad del desarrollador. Si el tiempo de ejecución que la aplicación requiere no es compatible con las imágenes integradas, se ofrecen instrucciones sobre cómo [generar su propia imagen de Docker](tutorial-custom-docker-image.md) para implementar en Web App for Containers.

| Idioma | Versiones compatibles |
|---|---|
| Node.js | 4.4, 4.5, 4.8, 6.2, 6.6, 6.9, 6.10, 6.11, 8.0, 8.1, 8.2, 8.8, 8.9, 8.11, 8.12, 9.4, 10.1, 10.10, 10.14 |
| Java * | Tomcat 8.5, 9.0, Java SE, WildFly 14 (todos ellos ejecutan JRE 8) |
| PHP | 5.6, 7.0, 7.2, 7.3 |
| Python | 2.7, 3.6, 3.7 |
| .NET Core | 1.0, 1.1, 2.0, 2.1, 2.2 |
| Ruby | 2.3, 2.4, 2.5, 2.6 |

## <a name="deployments"></a>Implementaciones

* FTP
* Git local
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* Entornos de ensayo
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) y DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>Consola, publicación y depuración

* Entornos
* Implementaciones
* Consola básica
* SSH

## <a name="scaling"></a>Escalado

* Los clientes pueden escalar o reducir aplicaciones web verticalmente mediante la modificación del nivel en su [plan de App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json).

## <a name="locations"></a>Ubicaciones

Consulte el [panel de estado de Azure](https://azure.microsoft.com/status).

## <a name="limitations"></a>Limitaciones

Azure Portal solo muestra las características que funcionan actualmente para Web Apps for Containers. A medida que se habiliten más características, estas aparecerán en el portal.

App Service en Linux solo se admite con los planes de App Service [Gratis, Básico, Estándar y Premium](https://azure.microsoft.com/pricing/details/app-service/plans/); no tiene un nivel [Compartido](https://azure.microsoft.com/pricing/details/app-service/plans/). No se puede crear Aplicación web en Linux en un plan de App Service que ya hospeda Web Apps que no sean de Linux.  

Debido a una limitación actual, para el mismo grupo de recursos no puede mezclar aplicaciones de Windows y Linux en la misma región.

## <a name="troubleshooting"></a>solución de problemas

Si la aplicación no se inicia o desea comprobar el registro desde la aplicación, compruebe los registros de Docker en el directorio LogFiles. A este directorio se accede a través del sitio SCM o a través de FTP. Para registrar `stdout` y `stderr` desde el contenedor, debe habilitar **Registro de contenedor de Docker** en **Registros de App Service**. Esta configuración surte efecto inmediatamente. App Service detecta el cambio y reinicia automáticamente el contenedor.

Puede acceder al sitio SCM desde **Herramientas avanzadas** en el menú **Herramientas de desarrollo**.

![Uso de Kudu para ver registros de Docker][1]

## <a name="next-steps"></a>Pasos siguientes

Los siguientes artículos introducirán App Service en Linux con aplicaciones web escritas en varios lenguajes:

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.js](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [Aplicaciones de varios contenedores](quickstart-multi-container.md)

Para más información sobre App Service en Linux, consulte:

* [Peguntas más frecuentes acerca de App Service para Linux](app-service-linux-faq.md)
* [Compatibilidad de SSH con Azure App Service en Linux](app-service-linux-ssh-support.md)
* [Configuración de entornos de ensayo en Azure App Service](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Implementación continua de Docker Hub](app-service-linux-ci-cd.md)

Puede publicar preguntas y problemas en [nuestro foro](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
