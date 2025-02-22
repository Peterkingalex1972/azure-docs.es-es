---
title: 'Registros de contenedores privados de Docker en Azure: información general'
description: Introducción al servicio Azure Container Registry, que proporciona registros de Docker basados en la nube, administrados y privados.
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 06/28/2019
ms.author: stevelas
ms.custom: seodec18, mvc
ms.openlocfilehash: 8b20c54d2151afae227a085dd66a0d31f2b85305
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/18/2019
ms.locfileid: "68310667"
---
# <a name="introduction-to-private-docker-container-registries-in-azure"></a>Introducción a los registros de contenedores privados de Docker en Azure

Azure Container Registry es un servicio privado administrado del Registro de Docker que usa Docker Registry 2.0, que es de código abierto. Cree y mantenga los registros de Azure Container para almacenar y administrar las imágenes privadas del contenedor Docker.

Use registros de contenedor de Azure con el desarrollo de contenedor y las canalizaciones de implementación existentes, o bien use Azure Container Registry Tasks para compilar imágenes de contenedor en Azure. Compile a petición o automatice completamente las compilaciones con desencadenadores como las confirmaciones del código fuente y las actualizaciones de la imagen de base.

Para más información acerca de los conceptos de Docker y del registro, [Introducción a Docker](https://docs.docker.com/engine/docker-overview/) y [Acerca de registros, repositorios e imágenes](container-registry-concepts.md).

## <a name="use-cases"></a>Casos de uso

Extraiga imágenes desde un registro de contenedor de Azure a varios destinos de implementación:

* **Sistemas escalables de orquestación** que administran aplicaciones en contenedores a través de clústeres de hosts, incluidos [Kubernetes](https://kubernetes.io/docs/), [DC/OS](https://docs.mesosphere.com/), y [Docker Swarm](https://docs.docker.com/swarm/).
* Los **Servicios de Azure** que admiten la compilación y ejecución de aplicaciones a escala, entre los que se incluyen [Azure Kubernetes Service (AKS)](../aks/index.yml), [App Service](../app-service/index.yml), [Batch](../batch/index.yml), [Service Fabric](/azure/service-fabric/) y otros más.

Los desarrolladores también pueden insertar en un registro de contenedor como parte de un flujo de trabajo de desarrollo de contenedor. Por ejemplo, puede dirigir un registro de contenedor desde una herramienta de entrega e integración continuas como [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) o [Jenkins](https://jenkins.io/).

Configure ACR Tasks para que recompile automáticamente imágenes de aplicaciones cuando se actualicen sus imágenes base o automatice las compilaciones de imágenes cuando el equipo guarde el código en un repositorio de Git. Cree tareas de varios pasos para automatizar la compilación, prueba y aplicación de revisiones de varias imágenes de contenedor en paralelo en la nube.

Azure proporciona varias herramientas, entre las que se incluyen la Interfaz de la línea de comandos de Azure, Azure Portal y soporte de API para administrar los registros de contenedor de Azure. Opcionalmente, instale la [extensión de Docker para Visual Studio Code](https://code.visualstudio.com/docs/azure/docker) y la extensión de la [cuenta de Azure](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) para trabajar con los registros de contenedor de Azure. Extraiga e inserte imágenes en un registro de contenedor de Azure o ejecute ACR Tasks y, todo ello, en Visual Studio Code.

## <a name="key-features"></a>Principales características

* **SKU de registro**: cree un registro de contenedor, o varios, en la suscripción de Azure. Los registros están disponibles en tres SKU: [Basic, Standard y Premium](container-registry-skus.md), cada una de las cuales admite la integración de webhook, la autenticación del registro con Azure Active Directory y la funcionalidad de eliminación. Aproveche las ventajas del almacenamiento local, cercano a la red, de las imágenes de contenedor mediante la creación de un registro en la misma ubicación de Azure que las implementaciones. Use la característica de [replicación geográfica](container-registry-geo-replication.md) de los registros Premium en escenarios avanzados de replicación y de distribución de imágenes de contenedor. 

  Puede [controlar el acceso](container-registry-authentication.md) a un registro de contenedor mediante una identidad de Azure, una [entidad de servicio](../active-directory/develop/app-objects-and-service-principals.md) respaldada por Azure Active Directory o una cuenta de administrador proporcionada. Inicie sesión en el registro mediante la CLI de Azure o el comando `docker login` estándar.

* **Imágenes y artefactos compatibles**: todas las imágenes se almacenan en un repositorio y cada de ellas es una instantánea de solo lectura de un contenedor compatible con Docker. Los registros de contenedor de Azure pueden incluir imágenes de Windows y de Linux. Controle los nombres de imagen de todas las implementaciones de contenedor. Use [comandos de Docker](https://docs.docker.com/engine/reference/commandline/) estándar para insertar imágenes en un repositorio o extraer una imagen de un repositorio. Además de las imágenes de contenedor de Docker, Azure Container Registry almacena los [formatos de contenido relacionados](container-registry-image-formats.md), como los [gráficos de Helm](container-registry-helm-repos.md) y las imágenes creadas para la [especificación de formato de imagen de Open Container Initiative (OCI)](https://github.com/opencontainers/image-spec/blob/master/spec.md).

* **Azure Container Registry Tasks**: use [Azure Container Registry Tasks](container-registry-tasks-overview.md) (ACR Tasks) para simplificar la creación, prueba, inserción e implementación de imágenes en Azure. Por ejemplo, utilice ACR Tasks para extender el bucle interno de desarrollo a la nube mediante la descarga de operaciones de `docker build` en Azure. Configure las tareas de compilación para automatizar el sistema operativo del contenedor y la canalización de aplicaciones de revisión de marcos, y compile imágenes de forma automática cuando el equipo guarde el código en el control de origen.

  Las [tareas con varios pasos](container-registry-tasks-overview.md#multi-step-tasks) proporcionan definición y ejecución de tareas basadas en pasos para compilar y probar imágenes de contenedor en la nube, y aplicarles revisiones. Los pasos de las tareas definen las operaciones de creación e inserción de imágenes de contenedor individuales. También pueden definir la ejecución de uno o más contenedores, donde cada paso utiliza el contenedor como su entorno de ejecución.

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un registro de contenedor mediante Azure Portal](container-registry-get-started-portal.md)
* [Creación de un registro de contenedor con la CLI de Azure](container-registry-get-started-azure-cli.md)
* [Automatización de compilaciones de contenedor y mantenimiento con ACR Tasks](container-registry-tasks-overview.md)
