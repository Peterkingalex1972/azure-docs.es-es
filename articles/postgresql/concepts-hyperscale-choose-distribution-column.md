---
title: 'Escoger las columnas de distribución en Azure Database for PostgreSQL: Hiperescala (Citus) (versión preliminar)'
description: Buenas opciones para las columnas de distribución en escenarios comunes de hiperescala
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e9fba14b8979f739fd29bc277e32fb544221d08a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65078991"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus-preview"></a>Escoger las columnas de distribución en Azure Database for PostgreSQL: Hiperescala (Citus) (versión preliminar)

Elegir la columna de distribución de cada tabla es **una de las decisiones de modelado más importantes**. La hiperescala almacena filas en particiones según el valor de la columna de distribución de las filas.

La elección correcta agrupa los datos relacionados en los mismos nodos físicos, lo que agiliza las consultas y agrega soporte técnico a todas las características de SQL. Si elige de forma incorrecta, el sistema se ejecutará lentamente y no admitirá todas las características de SQL en los nodos.

Esta sección proporciona consejos sobre las columnas de distribución para los dos escenarios más comunes de hiperescala.

### <a name="multi-tenant-apps"></a>Aplicaciones de multiinquilino

La arquitectura de multiinquilino usa una forma de modelado jerárquico de base de datos para distribuir consultas entre nodos en el grupo de servidores.  La parte superior de la jerarquía de datos se conoce como *id. de inquilino* y debe almacenarse en una columna en cada tabla.

La hiperescala inspecciona las consultas para ver qué id. de inquilino implican, y encuentra la partición de tabla correspondiente. Asimismo, dirige la consulta a un único nodo de trabajo que contiene la partición. La ejecución de una consulta con todos los datos relevantes colocados en el mismo nodo se denomina colocación.

El siguiente diagrama ilustra la colocación en el modelo de datos del multiinquilino. Contiene dos tablas, Cuentas y Campañas, cada una distribuida mediante `account_id`. Los cuadros sombreados representan particiones, y cada uno de sus colores representa en qué nodo de trabajo están. Las particiones verdes se almacenan juntas en un nodo de trabajo y las azules en otro. Observe cómo una consulta de unión entre las tablas Cuentas y Campañas tendría todos los datos necesarios juntos en un nodo, cuando restringe ambas tablas al mismo id.\_de cuenta.

![Colocación multiinquilino](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Para aplicar este diseño en su propio esquema, debe identificar qué constituye un inquilino en su aplicación. Las instancias comunes incluyen la empresa, la cuenta, la organización o el cliente. El nombre de la columna será algo como `company_id` o `customer_id`. Examine cada una de sus consultas y pregúntese: ¿esto funcionaría si tuviera cláusulas WHERE adicionales para restringir todas las tablas implicadas en las filas con el mismo id. de inquilino?
Las consultas del modelo multiinquilino se limitan al alcance de un inquilino; por ejemplo, las consultas sobre ventas o inventario se encuentran dentro de un determinado almacén.

#### <a name="best-practices"></a>Prácticas recomendadas

-   **Tablas que una\_ columna de**id. de inquilino distribuye en particiones. Por ejemplo, en una aplicación de SaaS donde los inquilinos son empresas, el id.\_del inquilino será probablemente el id.\_de la empresa.
-   **Convierta pequeñas tablas entre inquilinos en tablas de referencia.** Cuando varios inquilinos comparten una pequeña tabla de información, distribúyala como una tabla de referencia.
-   **Restrinja la opción para filtrar todas las consultas de las aplicaciones en función del id.\_del inquilino.** Cada consulta debe solicitar información para un inquilino a la vez.

Lea el [tutorial de multiinquilino](./tutorial-design-database-hyperscale-multi-tenant.md) para ver un ejemplo de cómo crear este tipo de aplicación.

### <a name="real-time-apps"></a>Aplicaciones en tiempo real

La arquitectura de multiinquilino introduce una estructura jerárquica y utiliza la colocación de datos para enrutar las consultas por cada inquilino. Por el contrario, las arquitecturas en tiempo real dependen de las propiedades de distribución específicas de sus datos para lograr un procesamiento altamente paralelo.

Usamos "id. de entidad" como término para las columnas de distribución en el modelo en tiempo real. Las entidades típicas son usuarios, hosts o dispositivos.

Las consultas en tiempo real suelen solicitar agregados numéricos agrupados en función de la fecha o la categoría. La hiperescala envía estas consultas a cada partición para obtener resultados parciales y reúne la respuesta final en el nodo de coordinador. Las consultas se ejecutan más rápido cuando contribuyen tantos nodos como sea posible y cuando ningún nodo debe realizar una cantidad desproporcionada de trabajo.

#### <a name="best-practices"></a>Prácticas recomendadas

-   **Elija una columna con alta cardinalidad, como la columna de distribución.** A modo de comparación, un campo de \"estado\" en una tabla de orden con los valores "nuevo", "pagado" y "enviado" es una mala elección de la columna de distribución. Como asume solo esos pocos valores, se limita la cantidad de particiones que pueden contener los datos y la cantidad de nodos que pueden procesarlos. Entre las columnas con alta cardinalidad, también es bueno elegir aquellas que se usan frecuentemente en cláusulas de grupos o como teclas de unión.
-   **Elija una columna con distribución uniforme.** Si distribuye una tabla en una columna sesgada con ciertos valores en común, entonces los datos de la tabla tenderán a acumularse en ciertas particiones. Los nodos que sostienen esas particiones terminarán trabajando más que otros nodos.
-   **Distribuya las tablas de hechos y dimensiones en sus columnas comunes.**
    Su tabla de hechos solo puede tener una clave de distribución. Las tablas que se unen en otra clave no se colocarán con la tabla de hechos. Elija la dimensión que quiera colocar en función de la frecuencia con la que se une y el tamaño de las filas de unión.
-   **Cambie algunas tablas de dimensiones para que sean tablas de referencia.** Si una tabla de dimensión no puede colocarse con la tabla de hechos, puede mejorar el rendimiento de la consulta distribuyendo copias de la tabla de dimensión a todos los nodos en forma de una tabla de referencia.

Lea el [tutorial del panel en tiempo real](./tutorial-design-database-hyperscale-realtime.md) para ver un ejemplo de cómo crear este tipo de aplicación.

### <a name="timeseries-data"></a>Datos de serie temporal

En una carga de trabajo de serie temporal, las aplicaciones consultan información reciente mientras archivan información antigua.

El error más común en el modelado de la información de series temporales en la hiperescala es usar la marca de tiempo como una columna de distribución. Una distribución de hash basada en el tiempo distribuirá los tiempos al azar en diferentes particionesen lugar de mantener los rangos de tiempo juntos en particiones. Las consultas que implican cierto tiempo, generalmente hacen referencia a rangos de tiempo (por ejemplo, los datos más recientes), por lo que tal distribución de hash llevaría a la sobrecarga de la red.

#### <a name="best-practices"></a>Prácticas recomendadas

-   **No elija una marca de tiempo como columna de distribución.** Elija una columna de distribución diferente. En una aplicación de multiinquilino use el id. del inquilino, o en una aplicación en tiempo real use el id. de la entidad.
-   **En su lugar, use la partición de la tabla PostgreSQL para el tiempo.** Use la partición de tablas para dividir una tabla grande de datos ordenados en función del tiempo en múltiples tablas heredadas, cada una con diferentes rangos de tiempo.  La distribución de una tabla particionada de Postgres en hiperescala crea particiones para las tablas heredadas.

Lea el [tutorial de la serie temporal](https://aka.ms/hyperscale-tutorial-timeseries) para ver un ejemplo de cómo crear este tipo de aplicación.

## <a name="next-steps"></a>Pasos siguientes
- Aprenda cómo la [colocación](concepts-hyperscale-colocation.md) entre datos distribuidos ayuda a que las consultas se ejecuten rápidamente
