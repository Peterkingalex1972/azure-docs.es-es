---
title: Escenarios de uso de Almacén de consultas en Azure Database for PostgreSQL con un único servidor
description: En este artículo se describen algunos escenarios para usar Almacén de consultas en Azure Database for PostgreSQL con un único servidor.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 029c595ba983d3b758568fbacaf6577014d893db
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067315"
---
# <a name="usage-scenarios-for-query-store"></a>Escenarios de uso de Almacén de consultas

**Se aplica a:** Azure Database for PostgreSQL con un único servidor 9.6 y 10

Puede usar Almacén de consultas en una amplia variedad de escenarios en los que sea crítico realizar y un seguimiento y mantener un rendimiento predecible de la carga de trabajo. Considere los ejemplos siguientes: 
- Identificación y optimización de las consultas más costosas 
- Pruebas A/B 
- Mantenimiento de un rendimiento estable durante las actualizaciones 
- Identificación y mejora de las cargas de trabajo puntuales 

## <a name="identify-and-tune-expensive-queries"></a>Identificación y optimización de las consultas costosas 

### <a name="identify-longest-running-queries"></a>Identificación de las consultas de ejecución más prolongada 
Use la vista [Información de rendimiento de consultas](concepts-query-performance-insight.md) en Azure Portal para identificar rápidamente las consultas de ejecución más prolongada. Estas consultas suelen consumir una cantidad importante de recursos. Optimizar las consultas de ejecución más prolongada puede mejorar el rendimiento porque se liberan recursos para otras consultas que se ejecutan en el sistema. 

### <a name="target-queries-with-performance-deltas"></a>Selección de consultas con diferenciales de rendimiento 
Almacén de consultas segmenta los datos de rendimiento en franjas de tiempo, por lo que puede realizar un seguimiento del rendimiento de una consulta a lo largo del tiempo. Esto ayuda a identificar exactamente qué consultas están contribuyendo a que se produzca el aumento en el tiempo total transcurrido. Como resultado, puede delimitar la solución de problemas de la carga de trabajo.

### <a name="tuning-expensive-queries"></a>Optimización de las consultas costosas 
Cuando identifique una consulta cuyo rendimiento no sea óptimo, la acción que realice dependerá de la naturaleza del problema: 
- Use [Recomendaciones de rendimiento](concepts-performance-recommendations.md) para determinar si hay sugerencias de índices. En caso afirmativo, cree el índice y, después, utilice Almacén de consultas para evaluar el rendimiento de las consultas después de crear el índice. 
- Asegúrese de que las estadísticas están actualizadas para las tablas subyacentes utilizadas por la consulta.
- Considere la posibilidad de volver a escribir las consultas costosas. Por ejemplo, aproveche las ventajas de la parametrización de consultas y reduzca el uso de SQL dinámico. Implemente una lógica óptima al leer los datos, por ejemplo, cuando se aplica filtrado en el lado de la base de datos, no en el lado de la aplicación. 


## <a name="ab-testing"></a>Pruebas A/B 
Use Almacén de consultas para comparar el rendimiento de la carga de trabajo antes y después de realizar un cambio que tenga previsto incorporar. Ejemplos de escenarios de uso de Almacén de consultas para evaluar el impacto de un cambio en el entorno o la aplicación sobre el rendimiento de la carga de trabajo: 
- Implementar una nueva versión de una aplicación. 
- Agregar recursos adicionales al servidor. 
- Crear índices que faltan en las tablas a las que hacen referencia las consultas costosas. 
 
En cualquiera de estos escenarios, aplique el flujo de trabajo siguiente: 
1. Ejecute la carga de trabajo con Almacén de consultas antes de efectuar el cambio previsto para generar una referencia de rendimiento. 
2. Aplique los cambios a la aplicación en un momento controlado. 
3. Continúe ejecutando la carga de trabajo durante el tiempo suficiente para generar la imagen de rendimiento del sistema después del cambio. 
4. Compare los resultados de antes y después del cambio. 
5. Decida si desea mantener el cambio o revertirlo. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Identificación y mejora de las cargas de trabajo puntuales 
Algunas cargas de trabajo no tienen consultas dominantes que se puedan optimizar para mejorar el rendimiento general de la aplicación. Esas cargas de trabajo suelen caracterizarse por un número relativamente alto de consultas únicas, cada una de las cuales consumen una parte de los recursos del sistema. Cada consulta única se ejecuta con poca frecuencia, por lo que su consumo individual en tiempo de ejecución no es crítico. Por otro lado, dado que la aplicación genera nuevas consultas todo el tiempo, una parte significativa de los recursos del sistema se dedica a compilar consultas, lo que no es un proceso óptimo. Normalmente, esta situación se produce si la aplicación genera consultas (en lugar de usar consultas parametrizadas o procedimientos almacenados) o si se basa en marcos de asignación relacional de objetos que generan consultas de forma predeterminada. 
 
Si tiene el control del código de la aplicación, podría considere la posibilidad de volver a escribir la capa de acceso a datos para que use consultas parametrizadas o procedimientos almacenados. Sin embargo, para mejorar esta situación sin realizar cambios en la aplicación, también se puede aplicar la parametrización de consultas a toda la base de datos (todas las consultas) o a plantillas de consulta individuales con el mismo hash de consulta. 

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre los [procedimientos recomendados para el uso de Almacén de consultas](concepts-query-store-best-practices.md)