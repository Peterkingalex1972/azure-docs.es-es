---
title: Configuración de entornos de ciencia de datos en Azure para usarse en el proceso de ciencia de datos en equipos
description: Configure entornos de ciencia de datos de Azure para utilizarse en el proceso de ciencia de datos en equipos.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/29/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 028f289092a0183072ab0ba0be29f9d1f79781bc
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534326"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Configuración de entornos de ciencia de datos para utilizarse en el proceso de ciencia de datos en equipos
El proceso de ciencia de datos en equipos usa una variedad de entornos de ciencia de datos para almacenar, procesar y analizar datos. Incluye Azure Blob Storage, varios tipos de máquinas virtuales de Azure, clústeres de HDInsight (Hadoop) y áreas de trabajo de Azure Machine Learning. La decisión sobre el entorno que se debe usar depende del tipo y de la cantidad de datos que se quieran modelar y del destino de los datos en la nube. 

* Para obtener orientación sobre las cuestiones que se deben tener en cuenta al tomar esta decisión, consulte [Planear su entorno de ciencia de datos de Azure Machine Learning](plan-your-environment.md). 
* Para ver un catálogo de algunos de los escenarios que se pueden encontrar al realizar el análisis avanzado, consulte [Escenarios para análisis avanzado en Aprendizaje automático de Azure](plan-sample-scenarios.md)

Los artículos siguientes describen cómo configurar los diversos entornos de ciencia de datos que el proceso de ciencia de datos en equipos usa.

* [Cuenta de almacenamiento de Azure](../../storage/common/storage-quickstart-create-account.md)
* [Clúster de HDInsight (Hadoop)](customize-hadoop-cluster.md)
* [Área de trabajo de Machine Learning Studio](../studio/create-workspace.md)

**Microsoft Data Science Virtual Machine (DSVM)** también está disponible como una imagen de máquina virtual (VM) de Azure. Esta máquina virtual se preinstala y configura con varias herramientas populares que se usan habitualmente para el análisis de datos y el aprendizaje automático. DSVM está disponible en Windows y Linux. Para más información, consulte [Introducción a Data Science Virtual Machine basada en la nube para Linux y Windows](../data-science-virtual-machine/overview.md).

Aprenda a crear:

- [Windows DSVM](../data-science-virtual-machine/provision-vm.md)
- [Ubuntu DSVM](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [CentOS DSVM](../data-science-virtual-machine/linux-dsvm-intro.md)
