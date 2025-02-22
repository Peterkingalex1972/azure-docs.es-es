---
title: Configuración de una transformación de origen en la característica de asignación de Data Flow de Azure Data Factory
description: Obtenga información sobre cómo configurar una transformación de origen en la asignación de Data Flow.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 974ece9cd035ae29ada38f34b7933d86f682194f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696229"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Transformación de origen de asignación de Data Flow 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Una transformación de origen configura el origen de datos para el flujo de datos. Un flujo de datos puede incluir más de una transformación de origen. Al diseñar flujos de datos, comience siempre con una transformación de origen.

Cada flujo de datos requiere al menos una transformación del origen. Agregue tantos orígenes como sea necesario para completar las transformaciones de datos. Puede combinar esos orígenes con una transformación de combinación o unión.

> [!NOTE]
> Cuando se depura un flujo de datos, los datos se leen desde el origen mediante el uso de la configuración de muestreo o los límites de origen de depuración. Para escribir datos en un origen, debe ejecutar el flujo de datos desde una actividad de Data Flow de canalización. 

![Opciones de transformación de origen en la pestaña Configuración de origen](media/data-flow/source.png "origen")

Asocie la transformación de origen de Data Flow con exactamente un conjunto de datos de Data Factory. El conjunto de datos define la forma y la ubicación de los datos que quiere escribir o leer. Puede usar caracteres comodín y listas de archivos en el origen para trabajar con más de un archivo a la vez.

El uso de una opción de **Wildcard Pattern** (Patrón de comodines) indicará a ADF que recorra todos los archivos y carpetas que coincidan en una única transformación del origen. Se trata de una manera muy eficaz de procesar varios archivos en un único flujo. Para realizar el seguimiento del nombre de archivo que se está procesando actualmente, establezca un nombre para el campo "Column to store file name" (Columna para almacenar un nombre de archivo) en Source Options (Opciones de origen).

> [!NOTE]
> Para agregar más reglas de caracteres comodín, establezca varios patrones de coincidencia de caracteres comodín con el signo + junto al patrón de caracteres comodín existente.

## <a name="data-flow-staging-areas"></a>Áreas de almacenamiento provisional de Data Flow
Data Flow funciona con conjuntos de datos de *almacenamiento provisional* que están en Azure. Use estos conjuntos de datos para el almacenamiento provisional cuando transforme los datos. 

Data Factory tiene acceso a casi 80 conectores nativos. Para incluir datos de esos otros orígenes en el flujo de datos, use la herramienta de actividad de copia para almacenar esos datos en una de las áreas de almacenamiento provisional del conjunto de datos de Data Flow.

## <a name="options"></a>Opciones

Elija las opciones de muestreo y esquema para los datos.

### <a name="schema-drift"></a>Desfase de esquema
El [desfase de esquema](concepts-data-flow-schema-drift.md) es la capacidad de ADF de administrar de forma nativa los esquemas flexibles en los flujos de datos sin necesidad de definir explícitamente los cambios en las columnas.

* Si las columnas de origen van a cambiar con frecuencia, seleccione la opción para **permitir el desfase de esquema**. Esta opción permite que todos los campos de origen entrantes fluyan hasta el receptor a través de las transformaciones.

* Al elegir **Inferir tipos de columnas desfasadas**, se indicará a ADF que defina los tipos de datos para cada nueva columna detectada. Al desactivar esta característica, ADF asumirá la cadena.

### <a name="validate-schema"></a>Validar esquema

Si la versión de entrada del origen de datos no coincide con el esquema definido, la ejecución del flujo de datos generará un error.

![Configuración de origen público que muestra las opciones de validación de esquema, Permitir el desfase de esquema y Muestreo](media/data-flow/source1.png "origen público 1")

### <a name="sample-the-data"></a>Muestreo de datos
Habilite el **muestreo** para limitar el número de filas del origen. Use esta configuración al probar o muestrear datos del origen con fines de depuración.

## <a name="define-schema"></a>Definición del esquema

Cuando los archivos de origen no están fuertemente tipados (por ejemplo, archivos sin formato en lugar de archivos Parquet), defina los tipos de datos para cada campo aquí en la transformación de origen.  

![Configuración de transformación de origen en la pestaña Definir esquema](media/data-flow/source2.png "origen 2")

Más adelante, puede cambiar los nombres de columna en una transformación que seleccione. Use una transformación de columna derivada para cambiar los tipos de datos. Para los orígenes fuertemente tipados, puede modificar los tipos de datos en una transformación que seleccione más adelante. 

![Tipos de datos en una transformación seleccionada](media/data-flow/source003.png "tipos de datos")

### <a name="optimize-the-source-transformation"></a>Optimización de la transformación de origen

En la pestaña **Optimizar** de la transformación de origen, es posible que vea un tipo de partición de **origen**. Esta opción solo está disponible cuando el origen es Azure SQL Database. Esto se debe a que Data Factory intenta establecer conexiones en paralelo para ejecutar consultas grandes en el origen de SQL Database.

![Configuración de la partición de origen](media/data-flow/sourcepart3.png "creación de particiones")

No es necesario que cree particiones de los datos en el origen de SQL Database, pero las particiones son útiles para consultas grandes. Puede basar la partición en una columna o una consulta.

### <a name="use-a-column-to-partition-data"></a>Uso de una columna para crear particiones de datos

En la tabla de origen, seleccione una columna en la que crear una partición. También debe establecer el número de particiones.

### <a name="use-a-query-to-partition-data"></a>Uso de una consulta para crear particiones de datos

Puede elegir crear una partición de las conexiones según una consulta. Solo tiene que escribir el contenido de un predicado WHERE. Por ejemplo, escriba año > 1980.

## <a name="source-file-management"></a>Administración de archivos de origen

Elija la configuración para administrar archivos en el origen. 

![Nueva configuración de origen](media/data-flow/source2.png "Nueva configuración")

* **Ruta de acceso con carácter comodín**: En el contenedor de origen, elija una serie de archivos que coincidan con un patrón. Esta configuración invalida cualquier archivo en la definición del conjunto de datos.

Ejemplos de caracteres comodín:

* ```*``` Representa cualquier conjunto de caracteres.
* ```**``` Representa el anidamiento recursivo de directorios.
* ```?``` Reemplaza un carácter.
* ```[]``` Coincide con uno de los caracteres entre corchetes.

* ```/data/sales/**/*.csv``` Obtiene todos los archivos csv que se encuentra en /data/sales.
* ```/data/sales/20??/**``` Obtiene todos los archivos del siglo XX.
* ```/data/sales/2004/*/12/[XY]1?.csv``` Obtiene todos los archivos csv de diciembre de 2004 que comienzan con X o Y precedido por un número de 2 dígitos.

El contenedor tiene que especificarse en el conjunto de datos. La ruta de acceso con carácter comodín, por tanto, también debe incluir la ruta de acceso de la carpeta de la carpeta raíz.

* **Partition Root Path** (Ruta de acceso de la raíz de la partición): Si tiene carpetas con particiones en el origen de archivo con un formato ```key=value``` (por ejemplo, year = 2019), puede pedir al ADF que asigne el nivel superior del árbol de carpetas de la partición a un nombre de columna del flujo de datos.

En primer lugar, establezca un comodín que incluya todas las rutas de acceso que sean carpetas con particiones y, además, los archivos de hoja que desee leer.

![Configuración del archivo de origen de la partición](media/data-flow/partfile2.png "Configuración del archivo de la partición")

Ahora, use el valor de Partition Root Path (Ruta de acceso de la raíz de la partición) para indicar a ADF cuál es el nivel superior de la estructura de carpetas. Ahora, cuando vea el contenido de los datos, verá que ADF agregará las particiones resueltas que se encuentran en cada uno de los niveles de carpetas.

![Ruta de acceso de la raíz de la partición](media/data-flow/partfile1.png "Vista previa de Ruta de acceso de la raíz de la partición")

* **Lista de archivos**: Se trata de un conjunto de archivos. Cree un archivo de texto que incluya una lista de archivos de ruta de acceso relativa para procesar. Apunte a este archivo de texto.
* **Columna para almacenar el nombre de archivo**: Almacene el nombre del archivo de origen en una columna de los datos. Escriba aquí un nombre nuevo para almacenar la cadena de nombre de archivo.
* **Tras finalizar**: Elija no hacer nada con el archivo de origen después de que se ejecute el flujo de datos o bien elimine o mueva el archivo de origen. Las rutas de acceso para mover los archivos de origen son relativas.

Para mover archivos de origen a otra ubicación posterior al procesamiento, primero seleccione "Mover" para la operación de archivo. A continuación, establezca el directorio "from". Si no usa ningún carácter comodín para la ruta de acceso, la configuración de "from" será la misma carpeta que la carpeta de origen.

Si tiene una ruta de acceso de origen con un comodín, su sintaxis será como esta:

```/data/sales/20??/**/*.csv```

puede especificar "from" como

```/data/sales```

y "to" como

```/backup/priorSales```

En este caso, todos los subdirectorios que cuyo origen se encuentra en /data/sales se mueven a /backup/priorSales.

### <a name="sql-datasets"></a>Conjuntos de datos de SQL

Si el origen está en SQL Database o SQL Data Warehouse, tendrá opciones adicionales para la administración de archivos de origen.

* **Consultar** escriba una consulta SQL para el origen. Esta configuración invalidará cualquier tabla que haya elegido en el conjunto de datos. Tenga en cuenta que las cláusulas **Ordenar por** no se admiten aquí, pero puede establecer una instrucción SELECT FROM completa. También puede usar las funciones de tabla definidas por el usuario. **select * from udfGetData()** es un UDF in SQL que devuelve una tabla. Esta consulta genera una tabla de origen que puede usar en el flujo de datos.
* **Tamaño del lote**: escriba un tamaño de lote para fragmentar datos grandes en lecturas.
* **Nivel de aislamiento**: El valor predeterminado para los orígenes de SQL en flujos de datos de asignación de ADF es Read Uncommitted. Puede cambiar el nivel de aislamiento aquí a uno de estos valores:
* Read Committed
* Read Uncommitted
* Repeatable Read
* Serializable
* Ninguno (ignorar el nivel de aislamiento)

![Nivel de aislamiento](media/data-flow/isolationlevel.png "Nivel de aislamiento")

> [!NOTE]
> Las operaciones de archivo solo se ejecutan cuando el flujo de datos se inicia desde una ejecución de canalización (depuración o ejecución de canalización) que usa la actividad de ejecución de Data Flow de una canalización. Las operaciones de archivo *no* se ejecutan en modo de depuración de Data Flow.

### <a name="projection"></a>Proyección

Al igual que los esquemas en los conjuntos de datos, la proyección de un origen define las columnas, los tipos y los formatos de datos de los datos de origen. 

![Configuración de la pestaña Proyección](media/data-flow/source3.png "Proyección")

Si el archivo de texto no tiene ningún esquema definido, seleccione **Detectar tipo de datos** para que Data Factory muestree e infiera los tipos de datos. Seleccione **Definir formato predeterminado** para detectar automáticamente los formatos de datos predeterminados. 

Puede modificar los tipos de datos de columna en una posterior transformación de columna derivada. Use una transformación de selección para modificar los nombres de columna.

![Configuración de los formatos de datos predeterminados](media/data-flow/source2.png "Formatos predeterminados")

### <a name="add-dynamic-content"></a>Incorporación de contenido dinámico
Al hacer clic dentro de los campos en el panel de configuración, verá un hipervínculo para "Agregar contenido dinámico". Si selecciona iniciar el Generador de expresiones, establecerá los valores de forma dinámica mediante expresiones, valores literales estáticos o parámetros.

![Parámetros](media/data-flow/params6.png "Parámetros")

## <a name="next-steps"></a>Pasos siguientes

Comience a compilar una [transformación de columna derivada](data-flow-derived-column.md) y una [transformación de selección](data-flow-select.md).
