---
title: Hacer que expiren datos en Azure Cosmos DB con período de vida
description: Con TTL, Microsoft Azure Cosmos DB ofrece la posibilidad de que los documentos se purguen automáticamente del sistema tras un período de tiempo.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: c3e1c4f56c641bf5bfa189836a4bcdf99672a3c1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68597487"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Período de vida (TTL) en Azure Cosmos DB 

Mediante el **período de vida** o TTL, Azure Cosmos DB proporciona la capacidad de eliminar automáticamente elementos de un contenedor después de un determinado período de tiempo. De forma predeterminada, puede establecer el período de vida en el nivel de contenedor e invalidar el valor en cada elemento. Después de establecer el TTL en el nivel de contenedor o de elemento, Azure Cosmos DB eliminará automáticamente estos elementos cuando haya pasado el período de tiempo seleccionado tras la hora de la última modificación. El valor del período de vida se configura en segundos. Cuando se configura el TTL, el sistema elimina automáticamente los elementos expirados en función del valor de TTL, sin necesidad de una operación de eliminación que se emita explícitamente desde la aplicación cliente.

La eliminación de los elementos expirados es una tarea en segundo plano que usa las [unidades de solicitud](request-units.md) restantes, es decir, las unidades de solicitud que no han consumido las solicitudes del usuario. Es posible que se retrase la expiración si el contenedor está sobrecargado y no queda ninguna unidad de solicitud para las tareas de mantenimiento.

## <a name="time-to-live-for-containers-and-items"></a>Período de vida para contenedores y elementos

El valor de período de vida se establece en segundos, y se interpreta como una delta desde el momento en que se modificó por última vez un elemento. Puede establecer el período de vida en un contenedor o en un elemento dentro del contenedor:

1. **Período de vida en un contenedor** (se establece mediante `DefaultTimeToLive`):

   - Si no existe (o está establecido en NULL), los elementos no expiran automáticamente.

   - Si existe y el valor se ha establecido en "-1", es igual a infinito y los documentos no expiran de forma predeterminada.

   - Si existe y el valor se ha establecido en un número *"n"* , los elementos expiran *"n"* segundos después de la última modificación.

2. **Período de vida en un elemento** (se establece mediante `ttl`):

   - Esta propiedad es aplicable solo si está presente el valor `DefaultTimeToLive` y no está establecido en NULL para el contenedor primario.

   - Si está presente, invalida el valor de `DefaultTimeToLive` del contenedor primario.

## <a name="time-to-live-configurations"></a>Configuraciones de período de vida

* Si el TTL se establece en *"n"* en un contenedor, los elementos de ese contenedor expirarán después de *n* segundos.  Si hay elementos en el mismo contenedor que tengan su propio período de vida establecido en -1 (lo que indica que no expiran), o si algunos elementos han invalidado la configuración del período de vida con un número distinto, estos elementos expirarán según su propio valor de TTL configurado. 

* Si no se establece el TTL en un contenedor, el período de vida en un elemento de este contenedor no tiene ningún efecto. 

* Si el TTL en un contenedor se establece en -1, un elemento de este contenedor que tenga el período de vida establecido en n, expirará después de n segundos, y los elementos restantes no expirarán. 

La eliminación de elementos en función del TTL es gratuita. No hay ningún costo adicional (es decir, no se consume ninguna RU adicional) cuando se elimina el elemento como resultado de la expiración de TTL.

## <a name="examples"></a>Ejemplos

En esta sección se muestra algunos ejemplos con distintos valores de período de vida asignados a los contenedores y los elementos:

### <a name="example-1"></a>Ejemplo 1

TTL de contenedor se establece en null (DefaultTimeToLive = null)

|TTL de elemento| Resultado|
|---|---|
|TTL = null|    TTL se deshabilita. El elemento nunca expirará (valor predeterminado).|
|TTL = -1   |TTL se deshabilita. El elemento nunca expirará.|
|TTL = 2000 |TTL se deshabilita. El elemento nunca expirará.|


### <a name="example-2"></a>Ejemplo 2

TTL de contenedor se establece en -1 (DefaultTimeToLive = -1)

|TTL de elemento| Resultado|
|---|---|
|TTL = null |TTL está habilitado. El elemento nunca expirará (valor predeterminado).|
|TTL = -1   |TTL está habilitado. El elemento nunca expirará.|
|TTL = 2000 |TTL está habilitado. El elemento expirará transcurridos 2000 segundos.|


### <a name="example-3"></a>Ejemplo 3

TTL de contenedor se establece en -1000 (DefaultTimeToLive = -1000)

|TTL de elemento| Resultado|
|---|---|
|TTL = null|    TTL está habilitado. El elemento expirará transcurridos 1000 segundos (valor predeterminado).|
|TTL = -1   |TTL está habilitado. El elemento nunca expirará.|
|TTL = 2000 |TTL está habilitado. El elemento expirará transcurridos 2000 segundos.|

## <a name="next-steps"></a>Pasos siguientes

Aprenda a configurar el período de vida con los siguientes artículos:

* [Cómo configurar el período de vida](how-to-time-to-live.md)
