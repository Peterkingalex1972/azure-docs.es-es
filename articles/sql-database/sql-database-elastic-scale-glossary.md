---
title: Glosario de las herramientas de Elastic Database | Microsoft Docs
description: Explicación de los términos usados en las herramientas de bases de datos elásticas
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/04/2018
ms.openlocfilehash: 3ed0cc9dce312cb9736b3e32ba46d2cb1cca3ef8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568462"
---
# <a name="elastic-database-tools-glossary"></a>Glosario de las herramientas de Elastic Database

Los siguientes términos se definen para las [herramientas de Elastic Database](sql-database-elastic-scale-introduction.md), una característica de Azure SQL Database. Las herramientas se usan para administrar [mapas de particiones](sql-database-elastic-scale-shard-map-management.md) e incluyen la [biblioteca de cliente](sql-database-elastic-database-client-library.md), la [herramienta de división y combinación](sql-database-elastic-scale-overview-split-and-merge.md), los [grupos elásticos](sql-database-elastic-pool.md) y las [consultas](sql-database-elastic-query-overview.md). 

Estos términos se usan en [Incorporación de una partición con herramientas de Elastic Database](sql-database-elastic-scale-add-a-shard.md) y [Uso de la clase RecoveryManager para solucionar problemas de asignaciones de particiones](sql-database-elastic-database-recovery-manager.md).

![Términos de Escalado elástico][1]

**Base de datos**: Una base de datos de Azure SQL. 

**Enrutamiento dependiente de los datos**: la funcionalidad que permite que una aplicación se conecte a una partición dada una clave de partición específica. Consulte [Enrutamiento dependiente de los datos](sql-database-elastic-scale-data-dependent-routing.md). Comparar con **[Multi-Shard Query](sql-database-elastic-scale-multishard-querying.md)** .

**Mapa de particiones global**: el mapa entre las claves de particionamiento y sus respectivas particiones dentro de un **conjunto de particiones**. El mapa de particiones global se almacena en el **administrador de mapas de particiones**. Comparar con **mapa de particiones local**.

**Mapa de particiones de lista**: un mapa de particiones en el que las claves de particionamiento se asignan de manera individual. Comparar con **Mapa de particiones de intervalo**.   

**Mapa de particiones local**: almacenado en una partición, el mapa de particiones local contiene asignaciones para los shardlets que residen en la partición.

**Consulta a través de particiones múltiples**: la capacidad de emitir una consulta a través de varias particiones; los conjuntos de resultados se devuelven usando la semántica UNION ALL (conocida también como "consulta de distribución ramificada"). Comparar con **enrutamiento dependiente de datos**.

**Multiinquilino** e **Inquilino único**: muestra una base de datos de inquilino único y multiinquilino:

![Bases de datos de inquilino único y multiinquilino](./media/sql-database-elastic-scale-glossary/multi-single-simple.png)

Aquí se muestra una representación de bases de datos de inquilino único y multiinquino **particionadas** . 

![Bases de datos de inquilino único y multiinquilino](./media/sql-database-elastic-scale-glossary/shards-single-multi.png)

**Mapa de particiones de intervalo**: un mapa de particiones en el que la estrategia de distribución de particiones se basa en varios intervalos de valores contiguos. 

**Tablas de referencia**: tablas no particionadas, pero que se replican entre particiones. Por ejemplo, los códigos postales se pueden almacenar en una tabla de referencia. 

**Partición**: una base de datos de Azure SQL que almacena datos desde un conjunto de datos particionados. 

**Elasticidad de partición**: la posibilidad de realizar **escalado horizontal** y **escalado vertical**.

**Tablas particionadas**: tablas que se particionan, es decir, cuyos datos se distribuyen entre particiones según sus valores de clave de particionamiento. 

**Clave de particionamiento**: un valor de columna que determina cómo se distribuyen los datos entre particiones. El tipo de valor puede ser uno de los siguientes: **int**, **bigint**, **varbinary** o **uniqueidentifier**. 

**Conjunto de particiones**: la recopilación de particiones que se atribuyen al mismo mapa de particiones en el administrador de mapa de particiones.  

**Shardlet**: todos los datos asociados con un valor único de una clave de particionamiento en una partición. Un shardlet es la unidad de desplazamiento de datos más pequeña posible cuando se distribuyen tablas particionadas. 

**Mapa de particiones**: el conjunto de asignaciones entre las claves de particionamiento y sus respectivas particiones.

**Administrador de mapas de particiones**: un almacén de datos y objetos de administración que contiene los mapas de particiones, ubicaciones de particiones y asignaciones para uno o más conjuntos de particiones.

![Asignaciones][2]

## <a name="verbs"></a>Verbos
**Escalado horizontal**: el acto de escalar o reducir horizontalmente una recopilación de particiones agregando o quitando particiones de un mapa de particiones.

![Escalado horizontal y vertical][3]

**Combinar**: el acto de mover shardlets desde dos particiones a una partición y actualizar el mapa de particiones como corresponda.

**Desplazamiento de shardlets**: el acto ve mover un shardlet a una partición distinta. 

**Particionar**: el acto de particionar de manera horizontal datos estructurados de manera idéntica entre varias bases de datos según una clave de particionamiento.

**Dividir**: el acto de mover varios shardlets desde una partición a otra (normalmente nueva). El usuario proporciona una clave de particionamiento como el punto de división.

**Escalado vertical**: el acto de escalar (o reducir) verticalmente el tamaño de proceso de una partición individual. Por ejemplo, cambiando una partición de Standard a Premium (lo que genera más recursos informáticos). 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-glossary/glossary.png
[2]: ./media/sql-database-elastic-scale-glossary/mappings.png
[3]: ./media/sql-database-elastic-scale-glossary/h_versus_vert.png

