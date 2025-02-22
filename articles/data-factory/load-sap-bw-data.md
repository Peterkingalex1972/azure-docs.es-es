---
title: Carga de datos de SAP Business Warehouse mediante Azure Data Factory | Microsoft Docs
description: Use Azure Data Factory para copiar datos desde SAP Business Warehouse (BW)
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: jingwang
ms.openlocfilehash: 3c846ab3e81e7ab8a4948aa4ed96cfa75e8eb3f4
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449697"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Copia de datos desde SAP Business Warehouse mediante Azure Data Factory

Este artículo muestra el uso de Azure Data Factory para copiar datos en Azure Data Lake Storage Gen2 desde una instancia de SAP Business Warehouse (BW) con Open Hub. Puede usar un proceso similar para copiar datos a otros [almacenes de datos receptores admitidos](copy-activity-overview.md#supported-data-stores-and-formats).

> [!TIP]
> Para obtener información general sobre cómo copiar datos desde SAP BW, incluida la Integración de Open Hub con SAP BW y el flujo de extracción delta, consulte [Copia de datos desde SAP Business Warehouse con Open Hub en Azure Data Factory](connector-sap-business-warehouse-open-hub.md).

## <a name="prerequisites"></a>Requisitos previos

- **Azure Data Factory**: Siga los pasos necesarios para [crear una factoría de datos](quickstart-create-data-factory-portal.md#create-a-data-factory), si aún no tiene ninguna.

- **Destino Open Hub (OHD) para SAP BW con el tipo de destino "Tabla de base de datos"** : Para crear un OHD o para comprobar que su OHD está configurado correctamente para la integración de Data Factory, consulte la sección [Configuraciones de destino de Open Hub para SAP BW](#sap-bw-open-hub-destination-configurations) de este artículo.

- **El usuario de SAP BW necesita los siguientes permisos**:

  - Autorización para las llamadas a funciones remotas (RFC) y SAP BW.
  - Permisos para la actividad “Execute” del objeto de autorización **S_SDSAUTH**.

- **Un [entorno de ejecución de integración autohospedado (IR)](concepts-integration-runtime.md#self-hosted-integration-runtime) con el conector de SAP para .NET 3.0**. Siga estos pasos de configuración:

  1. Instale y registre el entorno de ejecución de integración autohospedado, versión 3.13 o posterior. (Este proceso se describe más adelante en este artículo).

  2. Descargue el [conector de SAP de 64 bits para Microsoft.NET 3.0](https://support.sap.com/en/product/connectors/msnet.html) del sitio web de SAP e instálelo en la misma máquina que el IR autohospedado. Durante la instalación, asegúrese de que selecciona **Install Assemblies to GAC** (Instalar ensamblados en GAC) en el cuadro de diálogo **Optional setup steps** (Pasos de configuración opcionales), como se muestra en la siguiente imagen:

     ![Cuadro de diálogo de configuración del conector de SAP para .NET](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>Realización de una copia completa de Open Hub para SAP BW

En Azure Portal, vaya a la factoría de datos. Seleccione **Author & Monitor** (Creación y supervisión) para abrir la interfaz de usuario de Data Factory en una pestaña independiente.

1. En la página **Let's get started** (Introducción), seleccione **Copy Data** (Copiar datos) para iniciar la herramienta Copiar datos.

2. En la página **Propiedades**, especifique **Nombre de tarea** y seleccione **Siguiente**.

3. En la página **Almacén de datos de origen**, haga clic en **+ Crear una conexión nueva**. Seleccione **SAP BW Open Hub** en la galería de conectores, y después seleccione **Continuar**. Para filtrar los conectores puede escribir **SAP** en el cuadro de búsqueda.

4. En la página **Specify SAP BW Open Hub connection** (Especificar conexión de Open Hub para SAP BW), siga estos pasos para crear una nueva conexión.

   ![Página de creación de servicio vinculado de Open Hub para SAP BW](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   1. Desde la lista **Connect via integration runtime** (Conectar a través del entorno de ejecución de integración), seleccione un IR autohospedado existente. O si no tiene ninguno, seleccione la creación de uno.

      Para crear un nuevo IR autohospedado, seleccione **+Nuevo**y, a continuación, seleccione **Autohospedado**. Escriba un **Nombre** y, a continuación, seleccione **Siguiente**. Seleccione **Configuración rápida** para instalar en el equipo actual, o siga en **Configuración manual** los pasos que se proporcionan.

      Como se mencionó en los [Requisitos previos](#prerequisites), asegúrese de que tiene el conector de SAP para Microsoft .NET 3.0 instalado en el mismo equipo en el que se ejecuta el IR autohospedado.

   2. Rellene los datos de **Nombre del servidor**, **Número del sistema**, **Id. de cliente,** **Idioma** (si no es **EN**), **Nombre de usuario** y **Contraseña** de SAP BW.

   3. Seleccione **Probar conexión** para validar la configuración y, después, seleccione **Finalizar**.

   4. Se crea una nueva conexión. Seleccione **Next** (Siguiente).

5. En la página **Select Open Hub Destinations** (Seleccionar destinos de Open Hub), busque los destinos de Open Hub que están disponibles en su SAP BW. Seleccione el destino de Open Hub para copiar los datos y, a continuación, seleccione **Siguiente**.

   ![Tabla de selección de destinos de Open Hub para SAP BW](media/load-sap-bw-data/select-sap-bw-open-hub-table.png)

6. Especifique un filtro, si lo necesita. Si el destino de Open Hub solo contiene datos de una única ejecución de un proceso de transferencia de datos (DTP) con un identificador de solicitud único, o está seguro de que el proceso de transferencia de datos ha finalizado y quiere copiar los datos, desactive la casilla **Exclude Last Request** (Excluir la última solicitud).

   Más información acerca de estos valores en la sección [Configuraciones de destino de Open Hub para SAP BW](#sap-bw-open-hub-destination-configurations) de este artículo. Seleccione **Validar** para comprobar qué datos se devolverán. Luego, seleccione **Siguiente**.

   ![Configuración del filtro de Open Hub para SAP BW](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

7. En la página **Almacén de datos de destino**, seleccione **+ Crear una conexión nueva** > **Azure Data Lake Storage Gen2** > **Continuar**.

8. En la página **Specify Azure Data Lake Storage connection** (Especificar conexión de Azure Data Lake Storage), siga estos pasos para crear una nueva conexión.

   ![Página de creación de servicio vinculado de ADLS Gen2](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

   1. Seleccione la cuenta habilitada para Data Lake Storage Gen2 en la lista desplegable **Nombre**.
   2. Seleccione **Finalizar** para crear la conexión. Luego, seleccione **Siguiente**.

9. En la página **Choose the output file or folder** (Elegir archivo o carpeta de salida) escriba **copyfromopenhub** como nombre de la carpeta de salida. Luego, seleccione **Siguiente**.

   ![Elija la página de la carpeta de salida](media/load-sap-bw-data/choose-output-folder.png)

10. En la página **File format setting** (Configuración del formato de archivo), seleccione **Next** (Siguiente) para usar la configuración predeterminada.

    ![Página de especificación del formato de receptor](media/load-sap-bw-data/specify-sink-format.png)

11. En la página **Settings** (Configuración), haga clic en **Performance settings** (Configuración de rendimiento). Escriba un valor para **Degree of copy parallelism** (Grado de paralelismo de la copia) como por ejemplo 5 para cargar desde SAP BW en paralelo. Luego, seleccione **Siguiente**.

    ![Configuración de la copia](media/load-sap-bw-data/configure-copy-settings.png)

12. Revise la configuración en la página **Summary** (Resumen). Luego, seleccione **Siguiente**.

13. En la página **Deployment** (Implementación), seleccione **Monitor** (Supervisión) para supervisar la canalización.

    ![Página Deployment (Implementación)](media/load-sap-bw-data/deployment.png)

14. Observe que la pestaña **Monitor** (Supervisión) a la izquierda de la página, se selecciona automáticamente. La columna **Actions** (Acciones) incluye los vínculos para ver los detalles de la ejecución de actividad y volver a ejecutar la canalización.

    ![Vista de supervisión de la canalización](media/load-sap-bw-data/pipeline-monitoring.png)

15. Para ver las ejecuciones de actividad asociadas con la ejecución de canalización, seleccione el vínculo **View Activity Runs** (Ver ejecuciones de actividad) en la columna **Actions** (Acciones). Como solo hay una actividad (actividad de copia) en la canalización, solo verá una entrada. Para volver a la vista de ejecuciones de canalización, seleccione el vínculo **Pipelines** (Canalizaciones) de la parte superior. Seleccione **Refresh** (Actualizar) para actualizar la lista.

    ![Pantalla de supervisión de actividades](media/load-sap-bw-data/activity-monitoring.png)

16. Para supervisar los detalles de la ejecución de cada actividad de copia, seleccione el vínculo con un icono de gafas **Details** (Detalles) debajo de **Actions** (Acciones) en la vista de supervisión de la actividad. Los detalles disponibles incluyen el volumen de datos copiados del origen al receptor, el rendimiento de los datos, los pasos de ejecución y su duración y las configuraciones utilizadas.

    ![Detalles de la supervisión de la actividad](media/load-sap-bw-data/activity-monitoring-details.png)

17. Para ver el **identificador de solicitud máxima**, vuelva a la vista de supervisión de actividades **Output** (Salida) en **Actions** (Acciones).

    ![Pantalla de salida de la actividad](media/load-sap-bw-data/activity-output.png)

    ![Vista de detalles de salida de la actividad](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>Copia incremental de Open Hub para SAP BW

> [!TIP]
> Consulte la información sobre el [flujo de extracción delta del conector de Open Hub para SAP BW](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow) ver cómo el conector de Open Hub para SAP BW en Data Factory copia los datos incrementales desde SAP BW. Este artículo también puede ayudarle a entender la configuración básica del conector.

Ahora, vamos a seguir configurando la copia incremental de Open Hub para SAP BW.

La copia incremental utiliza un mecanismo de "límite máximo" que se basa en el **Identificador de solicitud**. El DTP genera ese identificador automáticamente en el destino de Open Hub para SAP BW. En el siguiente diagrama se ilustra este flujo de trabajo:

![Diagrama del flujo de trabajo de copia incremental](media/load-sap-bw-data/incremental-copy-workflow.png)

En la página **Let's get started** (Introducción) de la factoría de datos, seleccione **Create pipeline from template** (Creación de una canalización a partir de una plantilla) para usar la plantilla integrada.

1. Busque **SAP BW** para encontrar y seleccionar la plantilla **Incremental copy from SAP BW to Azure Data Lake Storage Gen2** (Copia incremental de SAP BW en Azure Data Lake Storage Gen2). Esta plantilla copia los datos en Azure Data Lake Storage Gen2. Puede usar un flujo de trabajo similar para copiar a otros tipos de receptor.

2. En la página principal de la plantilla, seleccione o cree las siguientes tres conexiones y, a continuación, seleccione **Usar esta plantilla** en la esquina inferior derecha de la ventana.

   - **Azure Blob Storage**: En este tutorial, usamos Azure Blob Storage para almacenar el límite máximo, que es el *Identificador de solicitud máxima copiada* .
   - **Open Hub para SAP BW**: Este es el origen desde el que se copian los datos. Consulte el tutorial anterior de copia completa para la configuración detallada.
   - **Azure Data Lake Storage Gen2**: Este es el receptor en el que se van a copiar los datos. Consulte el tutorial anterior de copia completa para la configuración detallada.

   ![Plantilla de la copia incremental de Open Hub para SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. Esta plantilla genera una canalización con las siguientes tres actividades y hace que se encadenen cuando se ejecutan correctamente: *Lookup*, *Copy Data* y *Web*.

   Acuda a la pestaña **Parámetros** de la canalización. Verá todas las configuraciones que tiene que proporcionar.

   ![Configuración de la copia incremental de Open Hub para SAP BW](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: Especifique el nombre de la tabla de Open Hub de la que se van a copiar los datos.

   - **ADLSGen2SinkPath**: Especifique la ruta de acceso del destino de Azure Data Lake Storage Gen2 en el que se van a copiar los datos. Si no existe la ruta de acceso, la actividad de copia de Data Factory crea una ruta de acceso durante la ejecución.

   - **HighWatermarkBlobPath**: Especifique la ruta de acceso para almacenar el valor de límite máximo, como `container/path`.

   - **HighWatermarkBlobName**: Especifique el nombre del blob para almacenar el valor de límite máximo, como `requestIdCache.txt`. En el Blob Storage, vaya a la ruta de acceso correspondiente de HighWatermarkBlobPath+HighWatermarkBlobName, como *container/path/requestIdCache.txt*. Cree un blob con contenido 0.

      ![Blob content (Contenido del blob)](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: En esta plantilla, se usa WebActivity para llamar a Azure Logic Apps para establecer el valor de límite máximo en Blob Storage. También puede usar Azure SQL Database para almacenarlo. Use una actividad de procedimiento almacenado para actualizar el valor.

      En primer lugar tiene que crear una aplicación lógica, como se muestra en la siguiente imagen. A continuación, pegue la **URL de HTTP POST**.

      ![Configuración de la aplicación lógica](media/load-sap-bw-data/logic-app-config.png)

      1. Vaya a Azure Portal. Seleccione un nuevo servicio de **Logic Apps**. Seleccione **+Aplicación lógica en blanco** para ir al **Diseñador de Logic Apps**.

      2. Cree un desencadenador de **Cuando se recibe una solicitud HTTP**. Especifique el cuerpo de la solicitud HTTP como sigue:

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. Agregue una acción **Crear blob**. Para **Ruta de acceso a la carpeta** y **Nombre del blob**, utilice los mismos valores que ha configurado previamente en **HighWatermarkBlobPath** y **HighWatermarkBlobName**.

      4. Seleccione **Guardar**. A continuación, copie el valor de la **URL de HTTP POST** a usar en la canalización de Data Factory.

4. Después de proporcionar los parámetros de canalización de Data Factory, seleccione **Depurar** > **Finalizar** para invocar una ejecución para validar la configuración. O también puede seleccionar **Publicar todo** para publicar los cambios y, a continuación, seleccionar **Desencadenador** para realizar una ejecución.

## <a name="sap-bw-open-hub-destination-configurations"></a>Configuraciones de destino de Open Hub para SAP BW

Esta sección presenta la configuración del lado de SAP BW para usar el conector de Open Hub para SAP BW en Data Factory para copiar los datos.

### <a name="configure-delta-extraction-in-sap-bw"></a>Configuración de la extracción delta en SAP BW

Si necesita tanto la copia histórica como la incremental o solo la copia incremental, configure la extracción delta en SAP BW.

1. Cree el destino de Open Hub. Puede crear el destino de Open Hub en SAP transacción RSA1, que crea automáticamente la transformación necesaria y el proceso de transferencia de datos. Use la configuración siguiente:

   - **ObjectType**: Puede usar cualquier tipo de objeto. En este caso, usamos **InfoCube** como ejemplo.
   - **Tipo de destino**: Seleccione **Database Table** (Tabla de base de datos).
   - **Key of the Table** (Clave de la tabla): Seleccione **Technical Key** (Clave técnica).
   - **Extracción**: Seleccione **Keep Data and Insert Records into Table** (Mantener los datos e insertar registros en la tabla).

   ![Cuadro de diálogo de creación de una extracción delta de destino de Open Hub para SAP BW](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![Cuadro de diálogo de creación de una extracción delta2 de destino de Open Hub para SAP BW](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   Puede aumentar el número de procesos de trabajo de SAP que se ejecutan en paralelo para el DTP:

   ![create-sap-bw-ohd-delta3](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. Programe el DTP en cadenas de proceso.

   Un DTP delta para un cubo solo funciona si no se han comprimido las filas necesarias. Asegúrese de que no se está ejecutando compresión del cubo BW antes del DTP en la tabla de Open Hub. La manera más fácil de hacerlo es integrar el DTP en las cadenas de proceso existentes. En el ejemplo siguiente, el DTP (al destino de Open Hub) se inserta en la cadena de proceso entre los pasos *Adjust* (acumulación agregada) y *Collapse* (compresión de cubo).

   ![Gráfico del diagrama del flujo de la cadena de proceso de creación de SAP BW](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>Configuración de la extracción completa en SAP BW

Además de la extracción delta, puede ser conveniente una extracción completa del mismo InfoProvider de SAP BW. Normalmente, esto se aplica si desea realizar la copia completa, pero no incremental, o si desea una [extracción delta resync](#resync-delta-extraction).

No puede tener más de un DTP para el mismo destino de Open Host. Por lo tanto, tiene que crear un destino de Open Host adicional antes de la extracción delta.

![Creación de un destino de Open Host para SAP BW completo](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

Para un destino de Open Host de carga completa, elija opciones distintas a las de la extracción delta:

- En el destino de Open Host: Establezca la opción **Extraction** (Extracción) **Delete Data and Insert Records** (Eliminar datos e insertar registros). En caso contrario, se extraerán datos muchas veces cuando se repita el DTP en una cadena de proceso BW.

- En el DTP: Establezca **Extraction Mode** (Modo de extracción) en **Full** (Completo). Tiene que cambiar el DTP que se creó automáticamente desde **Delta** a **Full** (Completo) inmediatamente después de que se haya creado el destino de Open Host, como se muestra en esta imagen:

   ![Cuadro de diálogo de creación de destino de Open Host para SAP BW configurado para la extracción "Completa"](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- En el conector de Open Hub para BW de Data Factory: Desactivar **Exclude last request** (Excluir la última solicitud). En caso contrario, no se extraerá nada.

Normalmente, ejecutará manualmente el DTP. O bien, puede crear una cadena de proceso para el DTP completo. Suele ser una cadena aparte que es independiente de las cadenas de proceso existentes. En cualquier caso, *Asegúrese de que ha finalizado el DTP antes de iniciar la extracción mediante la copia de Data Factory*. En caso contrario, se copiarán solo datos parciales.

### <a name="run-delta-extraction-the-first-time"></a>Ejecute la extracción delta por primera vez

La primera extracción delta es técnicamente una *extracción completa*. De forma predeterminada, el conector de Open Hub para SAP BW excluye la última solicitud, cuando copia los datos. Para la primera extracción delta, no se extraen datos mediante la actividad de copia de Data Factory, hasta que un DTP posterior genera datos delta en la tabla con un identificador de solicitud independiente. Existen dos formas de evitar este escenario:

- Desactivar la opción **Exclude last request** (Excluir la última solicitud) para la primera extracción delta. Asegúrese de que el primer DTP delta ha finalizado antes de iniciar la extracción delta por primera vez.
-  Usar el procedimiento para volver a sincronizar la extracción delta, tal como se describe en la sección siguiente.

### <a name="resync-delta-extraction"></a>Resincronización de la extracción delta

Los siguientes escenarios cambian los datos en los cubos de SAP BW, pero el DTP no los tiene en cuenta:

- Eliminación selectiva de SAP BW (de filas mediante el uso de cualquier condición de filtro)
- Eliminación de la solicitud de SAP BW (de solicitudes defectuosa)

Un destino de Open Hub para SAP no es un destino de datos controlados data mart (en todos los paquetes de soporte técnico de SAP BW desde 2015). Por lo tanto, puede eliminar datos de un cubo sin cambiar los datos en el destino de Open Host. Tiene que volver a sincronizar los datos del cubo con Data Factory:

1. Ejecute una extracción completa en Data Factory (con un DTP completo en SAP).
2. Elimine todas las filas de la tabla de Open Hub para el DTP delta.
3. Establezca el estado del DTP delta en **Fetched** (Obtenido).

Después de esto, todos los DTP delta y las extracciones delta de Data Factory funcionan según lo previsto.

Para establecer el estado de los DTP delta en **Fetched** (Obtenido), puede usar la siguiente opción para ejecutar manualmente el DTP delta:

    *No Data Transfer; Delta Status in Source: Fetched*

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre la compatibilidad del conector de Open Hub para SAP BW:

> [!div class="nextstepaction"]
>[Conector de Open Hub para SAP Business Warehouse](connector-sap-business-warehouse-open-hub.md)
