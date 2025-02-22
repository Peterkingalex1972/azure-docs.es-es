---
title: Información general de la asignación de Data Flow en Azure Data Factory
description: Explicación general de la asignación de flujos de datos en Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65233054"
---
# <a name="what-are-mapping-data-flows"></a>¿Qué es la asignación de instancias de Data Flow?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La asignación de instancias de Data Flow es una transformación de datos diseñada visualmente en Azure Data Factory. Las instancias de Data Flow permiten a los ingenieros de datos desarrollar una lógica de transformación de datos gráfica sin tener que escribir código. Los flujos de datos resultantes se ejecutan como actividades en las canalizaciones de Azure Data Factory mediante clústeres de Azure Databricks de escalabilidad horizontal.

La intención de Data Flow de Azure Data Factory es proporcionar una experiencia totalmente visual sin necesidad de codificación. Sus flujos de datos se ejecutarán en su propio clúster de ejecución para realizar el procesamiento de datos de escalabilidad horizontal. Asimismo, Azure Data Factory controla toda la traducción de código, la optimización de rutas de acceso y la ejecución de trabajos de flujo de datos.

Puede comenzar creando flujos de datos en el modo de depuración, para así poder validar la lógica de transformación de forma interactiva. A continuación, agregue una actividad de Data Flow a la canalización para ejecutar y probar el flujo de datos en la depuración de la canalización, o use "Desencadenar ahora" en la canalización para probar el flujo de datos de una actividad de canalización.

A continuación, debe programar y supervisar sus actividades de flujo de datos mediante las canalizaciones de Azure Data Factory que ejecutan la actividad de Data Flow.

La alternancia del modo de depuración en la superficie de diseño de Data Flow permite la compilación interactiva de las transformaciones de datos. Igualmente, el modo de depuración proporciona un entorno de preparación de datos para la construcción del flujo de datos.
