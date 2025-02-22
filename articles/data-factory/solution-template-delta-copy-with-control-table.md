---
title: Copia diferencial desde base de datos con tabla de control con Azure Data Factory | Microsoft Docs
description: Obtenga información sobre cómo usar una plantilla de solución para copiar incrementalmente filas nuevas o actualizadas solo desde una base de datos con Azure Data Factory.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: douglasl
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/24/2018
ms.openlocfilehash: c32592ce539eeb2dec71792e4a6eb31e7d904eff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60312514"
---
# <a name="delta-copy-from-a-database-with-a-control-table"></a>Copia diferencial desde una base de datos con una tabla de control

En este artículo se describe una plantilla que está disponible para cargar filas nuevas o actualizadas de forma incremental desde una tabla de base de datos en Azure mediante una tabla de control externa que almacena un valor de límite máximo.

Esta plantilla requiere que el esquema de la base de datos de origen contenga una columna de marca de tiempo o una clave de incremento para identificar las filas nuevas o actualizadas.

>[!NOTE]
> Si tiene una columna de marca de tiempo en su base de datos de origen para identificar las filas nuevas o actualizadas, pero no desea crear una tabla de control externa para usarla para la copia diferencial, puede utilizar en su lugar la [herramienta Copiar datos de Azure Data Factory](copy-data-tool.md) para obtener una canalización. Esa herramienta usa una hora programada por el desencadenador como variable para leer las nuevas filas desde la base de datos de origen.

## <a name="about-this-solution-template"></a>Acerca de esta plantilla de solución

Esta plantilla recupera primero el valor de límite anterior y lo compara con el actual. Después, solo copia los cambios de la base de datos de origen, según una comparación entre dos valores de límite. Por último, almacena el nuevo valor de límite máximo en una tabla de control externa para la carga de datos diferencial de la próxima vez.

La plantilla contiene cuatro actividades:
- **Búsqueda** recupera el valor de límite máximo anterior, el cual se almacena en una tabla de control externa.
- Otra actividad de **búsqueda** recupera el valor de límite máximo actual de la base de datos de origen.
- **Copia** copia solo los cambios de la base de datos de origen en el almacén de destino. La consulta que identifica los cambios de la base de datos de origen es similar a 'SELECT * FROM Data_Source_Table WHERE TIMESTAMP_Column > “last high-watermark” and TIMESTAMP_Column <= “current high-watermark”'.
- **SqlServerStoredProcedure** escribe el valor de límite máximo actual en una tabla de control externa para la copia diferencial de la próxima vez.

La plantilla define cinco parámetros:
- *Data_Source_Table_Name* es la tabla de la base de datos de origen de donde desea cargar los datos.
- *Data_Source_WaterMarkColumn* es el nombre de columna en la tabla de origen que se usa para identificar las filas nuevas o actualizadas. El tipo de esta columna suele ser *datetime*, *INT* o similar.
- *Data_Destination_Folder_Path* o *Data_Destination_Table_Name* es el lugar donde los datos se copian en el almacén de destino.
- *Control_Table_Table_Name* es la tabla de control externa que almacena el valor de límite máximo.
- *Control_Table_Column_Name* es la columna en la tabla de control externa que almacena el valor de límite máximo.

## <a name="how-to-use-this-solution-template"></a>Uso de esta plantilla de solución

1. Explore la tabla de origen que desea cargar y defina la columna de límite máximo que puede usarse para identificar las filas nuevas o actualizadas. El tipo de esta columna podría ser *datetime*, *INT* o similar. El valor de esta columna aumenta a medida que se agregan nuevas filas. En la siguiente tabla de origen de ejemplo (data_source_table), podemos usar la columna *LastModifytime* como la columna de límite máximo.

    ```sql
            PersonID    Name    LastModifytime
            1   aaaa    2017-09-01 00:56:00.000
            2   bbbb    2017-09-02 05:23:00.000
            3   cccc    2017-09-03 02:36:00.000
            4   dddd    2017-09-04 03:21:00.000
            5   eeee    2017-09-05 08:06:00.000
            6   fffffff 2017-09-06 02:23:00.000
            7   gggg    2017-09-07 09:01:00.000
            8   hhhh    2017-09-08 09:01:00.000
            9   iiiiiiiii   2017-09-09 09:01:00.000
    ```
    
2. Cree una tabla de control en SQL Server o Azure SQL Database para almacenar el valor de límite máximo para cargar los datos diferenciales. En el siguiente ejemplo, el nombre de la tabla de control es *watermarktable*. En esta tabla, *WatermarkValue* es la columna que almacena el valor de límite máximo y su tipo es *datetime*.

    ```sql
            create table watermarktable
            (
            WatermarkValue datetime,
            );
            INSERT INTO watermarktable
            VALUES ('1/1/2010 12:00:00 AM')
    ```
    
3. Cree un procedimiento almacenado en la misma instancia de SQL Server o Azure SQL Database que utiliza para crear la tabla de control. El procedimiento almacenado se utiliza para escribir el nuevo valor de límite máximo en la tabla de control externa para la carga de datos diferencial de la próxima vez.

    ```sql
            CREATE PROCEDURE update_watermark @LastModifiedtime datetime
            AS

            BEGIN

                UPDATE watermarktable
                SET [WatermarkValue] = @LastModifiedtime 

            END
    ```
    
4. Vaya a la plantilla **Copia diferencial desde base de datos**. Cree una **nueva** conexión a la base de datos de origen desde la que desea copiar datos.

    ![Creación de una nueva conexión con la tabla de origen](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable4.png)

5. Cree una **nueva** conexión al almacén de datos de destino en el que desea copiar los datos.

    ![Creación de una nueva conexión a la tabla de destino](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable5.png)

6. Cree una **nueva** conexión a la tabla de control externa y al procedimiento almacenado que ha creado en los pasos 2 y 3.

    ![Creación de una nueva conexión al almacén de datos de la tabla de control](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable6.png)

7. Seleccione **Usar esta plantilla**.

     ![Uso de esta plantilla](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable7.png)
    
8. Verá la canalización disponible, como se muestra en el ejemplo siguiente:

     ![Revisión de la canalización](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable8.png)

9. Seleccione **Procedimiento almacenado**. En **Nombre del procedimiento almacenado**, elija **[update_watermark]** . Seleccione **Importar parámetro** y, a continuación, seleccione **Agregar contenido dinámico**.  

     ![Establecimiento de la actividad de procedimiento almacenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable9.png) 

10. Escriba el contenido **\@{activity('LookupCurrentWaterMark').output.firstRow.NewWatermarkValue}** y, a continuación, seleccione **Finalizar**.  

     ![Escritura del contenido para los parámetros del procedimiento almacenado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable10.png)      
     
11. Seleccione **Depurar**, escriba los **parámetros** y, a continuación, seleccione **Finalizar**.

    ![Selección de **Depurar**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

12. Se muestran resultados similares a los del ejemplo siguiente:

    ![Revisión del resultado](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable12.png)

13. Puede crear nuevas filas en la tabla de origen. Este es el lenguaje SQL de ejemplo para crear nuevas filas:

    ```sql
            INSERT INTO data_source_table
            VALUES (10, 'newdata','9/10/2017 2:23:00 AM')

            INSERT INTO data_source_table
            VALUES (11, 'newdata','9/11/2017 9:01:00 AM')
    ```
14. Para volver a ejecutar la canalización, seleccione **Depurar**, escriba los **parámetros** y, a continuación, seleccione **Finalizar**.

    ![Selección de **Depurar**](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable11.png)

    Verá que solo las filas nuevas se copiaron en el destino.

15. (Opcional): Si seleccionó SQL Data Warehouse como destino de datos, también debe proporcionar una conexión a una instancia de Azure Blob Storage como almacenamiento provisional, porque así lo requiere SQL Data Warehouse Polybase. Asegúrese de que ya ha creado el contenedor en Blob Storage.
    
    ![Configuración de PolyBase](media/solution-template-delta-copy-with-control-table/DeltaCopyfromDB_with_ControlTable15.png)
    
## <a name="next-steps"></a>Pasos siguientes

- [Copia masiva desde base de datos con tabla de control con Azure Data Factory](solution-template-bulk-copy-with-control-table.md)
- [Copia de archivos de varios contenedores con Azure Data Factory](solution-template-copy-files-multiple-containers.md)
