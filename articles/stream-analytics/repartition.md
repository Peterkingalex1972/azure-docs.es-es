---
title: Vuelva a crear particiones para optimizar el procesamiento con Azure Stream Analytics
description: En este artículo se describe cómo usar la creación de particiones para optimizar los trabajos de Azure Stream Analytics que no se pueden ejecutar en paralelo.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 07/26/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9c802e6d23daf502da351549c66a7dae1247c068
ms.sourcegitcommit: f5cc71cbb9969c681a991aa4a39f1120571a6c2e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68517353"
---
# <a name="use-repartitioning-to-optimize-processing-with-azure-stream-analytics"></a>Vuelva a crear particiones para optimizar el procesamiento con Azure Stream Analytics

En este artículo se muestra cómo usar la creación de particiones para escalar la consulta de Azure Stream Analytics en escenarios que no se pueden [ejecutar completamente en paralelo](stream-analytics-scale-jobs.md).

Es posible que no pueda usar la [paralelización](stream-analytics-parallelization.md) si:

* No controla la clave de partición para el flujo de entrada.
* El origen divide la entrada en varias particiones que se deben combinar posteriormente. 

## <a name="how-to-repartition"></a>Cómo volver a crear particiones

Se requiere volver a crear particiones, o remodelar, cuando se procesan los datos de una secuencia que no está particionada de acuerdo con un esquema de entrada natural, como **PartitionId** para Event Hubs. Al volver a particionar, cada partición se puede procesar de forma independiente, lo que le permite escalar horizontalmente la canalización de streaming.

Para volver a crear particiones, use la palabra clave **INTO** tras una instrucción **PARTITION BY** en la consulta. En el ejemplo siguiente, se crean particiones de los datos por **DeviceID** en un número de particiones de 10. El hash de **DeviceID** se usa para determinar qué partición aceptará determinada subsecuencia. Los datos se vacían de forma independiente para cada flujo con particiones, suponiendo que la salida admita escrituras con particiones y que tenga 10 particiones.

```sql
SELECT * 
INTO output
FROM input
PARTITION BY DeviceID 
INTO 10
```

En la consulta de ejemplo siguiente se combinan dos flujos de datos con particiones. Al combinar dos flujos de datos con particiones, los flujos deben tener la misma clave de partición y el mismo número. El resultado es un flujo que tiene el mismo esquema de partición.

```sql
WITH step1 AS (SELECT * FROM input1 PARTITION BY DeviceID INTO 10),
step2 AS (SELECT * FROM input2 PARTITION BY DeviceID INTO 10)

SELECT * INTO output FROM step1 PARTITION BY DeviceID UNION step2 PARTITION BY DeviceID
```

El esquema de salida debe coincidir con el número y la clave del esquema de flujo para que cada subflujo se pueda vaciar de forma independiente. El flujo también se podría combinar y volver a particionar de nuevo mediante un esquema diferente antes del vaciado, pero debe evitar ese método porque se suma a la latencia general del procesamiento y aumenta el uso de los recursos.

## <a name="streaming-units-for-repartitions"></a>Unidades de streaming para las reparticiones

Experimente y observe el uso de recursos de su trabajo para determinar el número exacto de particiones que necesita. El número de [unidades de streaming (SU)](stream-analytics-streaming-unit-consumption.md) debe ajustarse según los recursos físicos necesarios para cada partición. En general, se necesitan seis unidades de streaming para cada partición. Si no hay suficientes recursos asignados al trabajo, el sistema aplicará la repartición solo si beneficia al trabajo.

## <a name="repartitions-for-sql-output"></a>Reparticiones para la salida de SQL

Cuando el trabajo usa SQL Database para la salida, use el reparticionamiento explícito para hacer coincidir el recuento de particiones óptimo y maximizar el rendimiento. Dado que SQL funciona mejor con ocho escritores, volver a particionar el flujo con ocho antes del vaciado, o en algún otro flujo ascendente, puede beneficiar el rendimiento del trabajo. Para obtener más información, vea [Salida de Azure Stream Analytics a Azure SQL Database](stream-analytics-sql-output-perf.md).


## <a name="next-steps"></a>Pasos siguientes

* [Introducción al uso de Azure Stream Analytics](stream-analytics-introduction.md)
* [Aprovechamiento de la paralelización de consultas en Azure Stream Analytics](stream-analytics-parallelization.md)