---
title: Creación visual en Azure Data Factory | Microsoft Docs
description: Información acerca de cómo utilizar la creación visual de Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: e8b9aa3af6b15272e19589eb422016f4abd9380f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883670"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Creación visual de Azure Data Factory

La experiencia de la interfaz de usuario (UX) de Azure Data Factory le permite crear e implementar visualmente recursos para la factoría de datos sin tener que escribir código. Puede arrastrar y colocar las actividades en un lienzo de canalización, realizar ejecuciones de prueba, depurar de forma iterativa e implementar y supervisar ejecuciones de canalizaciones.

## <a name="authoring-canvas"></a>Lienzo de creación

Para abrir el **lienzo de creación**, haga clic en el icono de lápiz. 

![Lienzo de creación](media/author-visually/authoring-canvas.png)

Aquí creará las canalizaciones, las actividades, los conjuntos de datos, los servicios vinculados, los flujos de datos, los desencadenadores y los entornos de ejecución de integración que componen la fábrica. Para empezar a crear una canalización mediante el lienzo de creación, consulte [Copia de datos mediante la actividad de copia](tutorial-copy-data-portal.md). 

La experiencia de creación visual predeterminada funciona directamente con el servicio Data Factory. La integración de Git de Azure Repos o GitHub también es compatible para admitir el control del código fuente y la colaboración para trabajar en canalizaciones de factoría de datos. Para más información sobre las diferencias entre estas experiencias de creación, consulte [Control de código fuente en Azure Data Factory](source-control.md).

## <a name="expressions-and-functions"></a>Expresiones y funciones

Se pueden usar expresiones y funciones en lugar de valores estáticos para especificar muchas propiedades en Azure Data Factory.

Para especificar una expresión para un valor de propiedad, seleccione **Agregar contenido dinámico** o haga clic en **Alt + P** mientras se centra en el campo.

![Incorporación de contenido dinámico](media/author-visually/dynamic-content-1.png)

Así se abre el **Generador de expresiones de Data Factory**, donde puede generar expresiones a partir de variables del sistema admitidas, salidas de actividad, funciones y variables o parámetros especificados por el usuario. 

![Generador de expresiones](media/author-visually/dynamic-content-2.png)

Para más información sobre el lenguaje de expresión, consulte [Expresiones y funciones de Azure Data Factory](control-flow-expression-language-functions.md).

## <a name="provide-feedback"></a>Envío de comentarios

Seleccione **Comentarios** para comentar sobre las características o para notificar a Microsoft sobre los problemas con la herramienta:

![Comentarios](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la supervisión y la administración de canalizaciones, consulte el artículo [Supervisión y administración de canalizaciones mediante programación](monitor-programmatically.md).
