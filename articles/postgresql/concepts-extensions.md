---
title: 'Uso de extensiones de PostgreSQL en Azure Database for PostgreSQL: un solo servidor'
description: 'Describe la capacidad de ampliar la funcionalidad de la base de datos mediante extensiones en Azure Database for PostgreSQL: un solo servidor.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/26/2019
ms.openlocfilehash: 412ce3c5245f3f22bfb03740a0451670dc6a90a7
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448106"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>Extensiones de PostgreSQL en Azure Database for PostgreSQL: un solo servidor
PostgreSQL ofrece la capacidad de ampliar la funcionalidad de su base de datos mediante extensiones. Las extensiones permiten agrupar varios objetos SQL relacionados en un solo paquete que se puede cargar o quitar de la base de datos con un solo comando. Después de cargarse en la base de datos, las extensiones pueden funcionar de la misma forma que las características integradas. Para obtener más información sobre las extensiones de PostgreSQL, vea  [Empaquetar objetos relacionados en una extensión](https://www.postgresql.org/docs/9.6/static/extend-extensions.html).

## <a name="how-to-use-postgresql-extensions"></a>¿Cómo se utilizan las extensiones de PostgreSQL?
Para poder usar las extensiones de PostgreSQL, es preciso que estén instaladas en su base de datos. Para instalar una extensión específica, ejecute el comando  [CREATE EXTENSION](https://www.postgresql.org/docs/9.6/static/sql-createextension.html)  desde la herramienta psql para cargar los objetos empaquetados en la base de datos.

Azure Database for PostgreSQL actualmente admite un subconjunto de extensiones de claves, como se enumeran a continuación. Aparte de las que se indican, no se admiten otras extensiones. No puede crear su propia extensión con el servicio Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensiones admitidas por Azure Database for PostgreSQL
En las tablas siguientes se enumeran las extensiones estándar de PostgreSQL que Azure Database for PostgreSQL admite actualmente. Esta información también está disponible al ejecutar `SELECT * FROM pg_available_extensions;`.

### <a name="data-types-extensions"></a>Extensiones de tipos de datos

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Proporciona un tipo de datos para las contraseñas de cifrado automático. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Proporciona un tipo de cadena de caracteres que no distingue entre mayúsculas y minúsculas. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Proporciona un tipo de datos para los cubos multidimensionales. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Proporciona un tipo de datos para almacenar conjuntos de pares clave/valor. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Proporciona tipos de datos para los estándares internacionales de numeración de productos. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Proporciona un tipo de datos para las estructuras de árbol jerárquicas. |

### <a name="functions-extensions"></a>Extensiones de funciones

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Proporciona un medio para calcular distancias de círculo máximo en la superficie de la Tierra. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Proporciona varias funciones para determinar las similitudes y la distancia entre las cadenas. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Proporciona funciones y operadores para manipular matrices de enteros sin valores nulos. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Proporciona funciones de cifrado. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Administra las tablas con particiones por hora o identificador. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Proporciona funciones y operadores para determinar la similitud del texto alfanumérico basado en la coincidencia de trigrama. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Proporciona funciones que manipulan la totalidad del contenido de las tablas, incluidas tablas de referencias cruzadas. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genera identificadores únicos universales (UUID). (Vea a continuación una nota sobre esta extensión). |
> | [orafce](https://github.com/orafce/orafce) | Proporciona un subconjunto de funciones y paquetes emulados de bases de datos comerciales. |

### <a name="full-text-search-extensions"></a>Extensiones de búsqueda de texto completo

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Proporciona una plantilla de diccionario de búsqueda de texto para números enteros. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Un diccionario de búsqueda de texto que quita los acentos (signos diacríticos) de lexemas. |

### <a name="index-types-extensions"></a>Extensiones de tipos de índices

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [btree\_gin](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Proporciona clases de operador GIN de ejemplo que implementan el comportamiento similar al del árbol B para determinados tipos de datos. |
> | [btree\_gist](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Proporciona clases de operador de índice de GiST que implementan el árbol B. |

### <a name="language-extensions"></a>Extensiones de lenguaje

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Lenguaje de procedimientos que puede cargar PL/pgSQL. |
> | [plv8](https://plv8.github.io/) | Una extensión del lenguaje JavaScript para PostgreSQL que puede usarse para almacenar procedimientos, desencadenadores, etc. |

### <a name="miscellaneous-extensions"></a>Extensiones variadas

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Proporciona un medio para examinar lo que sucede en la caché del búfer compartido en tiempo real. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Proporciona una manera de cargar datos de relación en la caché del búfer. |
> | [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Proporciona un medio para realizar el seguimiento de las estadísticas de ejecución de todas las instrucciones SQL ejecutadas por un servidor. (Vea a continuación una nota sobre esta extensión). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Proporciona un medio para mostrar información de bloqueo a nivel de fila. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Proporciona un medio para mostrar estadísticas de nivel de tupla. |
> | [postgres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Se trata de un contenedor de datos externo utilizado para tener acceso a los datos almacenados en los servidores externos de PostgreSQL. (Vea a continuación una nota sobre esta extensión).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Proporciona un medio de creación de índices hipotéticos que no consume CPU ni disco. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Módulo que admite conexiones a otras bases de datos de PostgreSQL desde una sesión de base de datos. (Vea a continuación una nota sobre esta extensión). |


### <a name="postgis-extensions"></a>Extensiones de PostGIS

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [PostGIS](https://www.postgis.net/), postgis\_topology, postgis\_tiger\_geocoder, postgis\_sfcgal | Objetos espaciales y geográficos para PostgreSQL. |
> | address\_standardizer, address\_standardizer\_data\_us | Se utilizan para analizar una dirección en los elementos que la componen. Se utilizan para admitir el paso de normalización de la dirección de geocodificación. |
> | [pgrouting](https://pgrouting.org/) | Extiende la base de datos geoespacial de PostGIS/PostgreSQL para proporcionar la funcionalidad de enrutamiento geoespacial. |


### <a name="time-series-extensions"></a>Extensiones de serie temporal

> [!div class="mx-tableFixed"]
> | **Extensión** | **Descripción** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Una base de datos SQL de serie temporal que admite la creación de particiones automatizada para acelerar la ingesta y la consulta. Proporciona funciones analíticas orientadas al tiempo, optimizaciones y escala PostgreSQL para las cargas de trabajo de serie temporal. TimescaleDB se desarrolla y es una marca registrada de [Timescale, Inc.](https://www.timescale.com/) (Vea a continuación una nota sobre esta extensión). |


## <a name="pgstatstatements"></a>pg_stat_statements
La extensión [pg\_stat\_statements](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) está cargada previamente en cada servidor de Azure Database for PostgreSQL para proporcionarle un medio de seguimiento de las estadísticas de ejecución de las instrucciones SQL.
La configuración `pg_stat_statements.track`, que controla las instrucciones que la extensión cuenta, se establece de manera predeterminada en `top`, lo que significa que se realiza el seguimiento de todas las instrucciones que los clientes emiten directamente. Los otros dos niveles de seguimiento son `none` y `all`. Esta configuración es configurable como un parámetro de servidor a través de [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) o la [CLI de Azure](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Hay un equilibrio entre la información de ejecución de consulta que pg_stat_statements proporciona y el impacto en el rendimiento del servidor al registrar cada instrucción SQL. Si no está usando activamente la extensión pg_stat_statements, le recomendamos que establezca `pg_stat_statements.track` en `none`. Tenga en cuenta que algunos servicios de supervisión de terceros pueden basarse en pg_stat_statements para entregar información de rendimiento de consultas, por lo que debe confirmar si este es el caso para usted o no.

## <a name="dblink-and-postgresfdw"></a>dblink y postgres_fdw
dblink y postgres_fdw le permiten conectarse de un servidor PostgreSQL a otro, o a otra base de datos en el mismo servidor. El servidor de recepción debe permitir conexiones del servidor de envío a través de su firewall. Cuando se usan estas extensiones para conectarse entre servidores de Azure Database for PostgreSQL, esto se puede realizar mediante el establecimiento de "Permitir acceso a servicios de Azure" en Activado. Esto también es necesario si desea utilizar las extensiones para volver al mismo servidor. La opción "Permitir el acceso a servicios de Azure" puede encontrarse en la página de Azure Portal para el servidor de Postgres, en Seguridad de la conexión. Si se activa "Permitir el acceso a servicios de Azure", se permitirá en todas las direcciones IP de Azure.

En la actualidad, no se admiten las conexiones salientes desde Azure Database for PostgreSQL, excepto para las conexiones a otros servidores de Azure Database for PostgreSQL.

## <a name="uuid"></a>uuid
Si planea usar `uuid_generate_v4()` desde la extensión uuid-ossp, puede comparar con `gen_random_uuid()` de la extensión pgcrypto para obtener beneficios de rendimiento.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB es una base de datos de serie temporal que se empaqueta como una extensión para PostgreSQL. TimescaleDB proporciona funciones analíticas orientadas al tiempo, optimizaciones y escala Postgres para las cargas de trabajo de serie temporal.

[Más información sobre TimescaleDB](https://docs.timescale.com/latest), una marca registrada de [Timescale, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>Instalación de TimescaleDB
Para instalar TimescaleDB, tendrá que incluirlo en las bibliotecas de carga previa compartidas del servidor. Si cambia el parámetro `shared_preload_libraries` de Postgres deberá **reiniciar el servidor** para que tenga efecto. Puede cambiar los parámetros mediante [Azure Portal](howto-configure-server-parameters-using-portal.md) o la [CLI de Azure](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB se puede habilitar en las versiones 9.6 y 10 de Azure Database for PostgreSQL.

Mediante [Azure Portal](https://portal.azure.com/):

1. Seleccione su servidor de Azure Database for PostgreSQL.

2. En la barra lateral, seleccione **Parámetros del servidor**.

3. Busque el parámetro `shared_preload_libraries`.

4. Seleccione **TimescaleDB**.

5. Seleccione **Guardar** para conservar los cambios. Recibirá una notificación una vez que se haya guardado el cambio. 

6. Después de la notificación, **reinicie** el servidor para aplicar estos cambios. Para aprender a reiniciar un servidor, consulte [Reinicio de un servidor de Azure Database for PostgreSQL mediante Azure Portal](howto-restart-server-portal.md).


Ahora puede habilitar TimescaleDB en la base de datos de Postgres. Conéctese a la base de datos y ejecute el comando siguiente:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Si ve un error, confirme que ha [reiniciado el servidor](howto-restart-server-portal.md) después de guardar shared_preload_libraries. 

Ahora puede crear una hipertabla de TimescaleDB [desde cero](https://docs.timescale.com/getting-started/creating-hypertables) o migrar [datos de serie temporal existentes en PostgreSQL](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Pasos siguientes
Si no ve una extensión que le gustaría utilizar, háganoslo saber. Vote por las solicitudes existentes o cree nuevos comentarios y solicitudes en nuestro [foro de comentarios](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
