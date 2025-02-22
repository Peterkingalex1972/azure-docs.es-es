---
title: Migración de los datos a una cuenta de Cassandra API de Azure Cosmos DB mediante Striim
description: Aprenda a usar Striim para migrar datos desde una instancia de Oracle Database a una cuenta de Cassandra API de Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 31273105c2f4de6950eae6a66c50264803197642
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69981872"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migración de los datos a una cuenta de Cassandra API de Azure Cosmos DB mediante Striim

La imagen de Striim en Azure Marketplace ofrece una migración continua de los datos en tiempo real desde almacenes de datos y bases de datos a Azure. Durante la migración de los datos, puede realizar una desnormalización insertada, trasformar los datos y habilitar análisis en tiempo real y escenarios de creación de informes de datos. Es fácil empezar a trabajar con Striim para migrar continuamente datos empresariales a Cassandra API de Azure Cosmos DB. Azure proporciona una oferta de Marketplace que facilita la implementación de Striim y la migración de los datos a Azure Cosmos DB. 

En este artículo se muestra cómo usar Striim para migrar datos desde una instancia de **Oracle Database** a una **cuenta de Cassandra API de Azure Cosmos DB**.

## <a name="prerequisites"></a>Requisitos previos

* Si no tiene una [suscripción a Azure](/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

* Una instancia de Oracle Database que se ejecute localmente y que contenga algunos datos.

## <a name="deploy-the-striim-marketplace-solution"></a>Implementación de la solución de Marketplace de Striim

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com/).

1. Seleccione **Crear un recurso** y busque **Striim** en Azure Marketplace. Seleccione la primera opción y **Crear**.

   ![Búsqueda del elemento de Marketplace de Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png)

1. A continuación, escriba las propiedades de configuración de la instancia de Striim. El entorno de Striim se implementa en una máquina virtual. En el panel **Básico**, escriba el **nombre de usuario de máquina virtual**, **Contraseña de máquina virtual** (esta contraseña se usa para SSH en la máquina virtual). Seleccione su **Suscripción**, **Grupo de recursos** y **Detalles de ubicación** allí donde desee implementar Striim. Cuando haya terminado, seleccione **Aceptar**.

   ![Configuración de las opciones básicas para Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png)


1. En el panel **Striim Cluster settings** (Configuración del clúster de Striim), elija el tipo de implementación de Striim y el tamaño de la máquina virtual.

   |Configuración | Valor | DESCRIPCIÓN |
   | ---| ---| ---|
   |Tipo de implementación de Striim |Independiente | Striim se puede ejecutar en un tipo de implementación **independiente** o de **clúster**. El modo independiente implementará el servidor Striim en una sola máquina virtual, y lepermite seleccionar el tamaño de las máquinas virtuales dependiendo de su volumen de datos. El modo de clúster implementará el servidor Striim en dos o más máquinas virtuales con el tamaño seleccionado. Los entornos de clúster con más de dos nodos ofrecen conmutación por error y alta disponibilidad automáticas.</br></br> En este tutorial, puede seleccionar la opción Independiente. Use el tamaño "Standard_F4s" predeterminado de la máquina virtual. | 
   | Nombre del clúster de Striim|    <Striim_cluster_Name>|  Nombre del clúster de Striim.|
   | Contraseña del clúster de Striim|   <Striim_cluster_password>|  Contraseña para el clúster.|

   Una vez que rellene el formulario, seleccione **Aceptar** para continuar.

1. En el panel **Striim access settings** (Configuración de acceso de Striim), configure la **Dirección IP pública** (elija los valores predeterminados), **Domain name for Striim** (Nombre de dominio para Striim) y la **Contraseña de administrador** que desea usar para iniciar sesión en la interfaz de usuario de Striim. Configure una red virtual y una subred (elija los valores predeterminados). Tras rellenar los detalles, seleccione **Aceptar** para continuar.

   ![Configuración de acceso de Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png)

1. Azure validará la implementación y se asegurará de que todo esté bien; la validación tarda algunos minutos en completarse. Una vez completada la validación, seleccione **Aceptar**.
  
1. Por último, revise las condiciones de uso y seleccione **Crear** para crear su instancia de Striim. 

## <a name="configure-the-source-database"></a>Configuración de la base de datos de origen

En esta sección, va a configurar la instancia de Oracle Database como origen del movimiento de datos.  Necesitará el [controlador JDBC de Oracle](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) para conectarse a Oracle. Para leer los cambios en su instancia de Oracle Database de origen, puede usar [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) o las [API XStream](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647). El controlador JDBC de Oracle tiene que estar presente en classpath de Java de Striim para leer, escribir o conservar datos de la instancia de Oracle Database.

Descargue el controlador [ojdbc8.jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) en su máquina local. Lo instalará en el clúster de Striim posteriormente.

## <a name="configure-target-database"></a>Configuración de la base de datos de destino

En esta sección, configurará la cuenta de Cassandra API de Azure Cosmos DB como destino del movimiento de datos.

1. Creación de una [cuenta de Cassandra API de Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) mediante Azure Portal.

1. Vaya al panel **Explorador de datos** en su cuenta de Azure Cosmos. Seleccione **Nueva tabla** para crear un nuevo contenedor. Supongamos que migra *productos* y datos de *pedidos* desde la instancia de Oracle Database a Azure Cosmos DB. Cree un nuevo espacio de claves llamado **StriimDemo** con un contenedor de pedidos. Aprovisione el contenedor con **1000 RU**(en este ejemplo se usan 1000 RU, pero debe usar el rendimiento estimado para su carga de trabajo) y **/ORDER_ID** como clave principal. Estos valores variarán dependiendo de sus datos de origen. 

   ![Creación de una cuenta de Cassandra API](./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png)

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Configuración de Oracle para el flujo de datos de Azure Cosmos DB

1. Ahora, volvamos a Striim. Antes de interactuar con Striim, instale el controlador JDBC de Oracle que descargó anteriormente.

1. Vaya a la instancia de Striim que implementó en Azure Portal. Seleccione el botón **Conectar** en la barra de menús superior y, en la pestaña **SSH**, copie la dirección URL en el campo **Iniciar sesión con la cuenta local de VM**.

   ![Obtención de la dirección URL de SSH](./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png)

1. Abra una nueva ventana de terminal y ejecute el comando SSH que copió desde Azure Portal. En este artículo se usa el terminal en MacOS. Puede seguir las instrucciones similares mediante PuTTY u otro cliente SSH en una máquina Windows. Cuando se le solicite, escriba **sí** para continuar y la **contraseña** que ha establecido para la máquina virtual en el paso anterior.

   ![Conexión a la máquina virtual de Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png)

1. Ahora, abra una nueva pestaña de terminal para copiar el archivo **ojdbc8.jar** que descargó anteriormente. Use el siguiente comando SCP para copiar el archivo jar desde su máquina local en la carpeta tmp de la instancia de Striim que se ejecuta en Azure:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   ![Copia del archivo Jar desde la ubicación de la máquina en Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png)

1. A continuación, vuelva a la ventana donde usó SSH en la instancia de Striim e inicie sesión como sudo. Mueva el archivo **ojdbc8.jar** del directorio **/tmp** al directorio **lib** de su instancia de Striim con los siguientes comandos:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   ![Movimiento del archivo Jar a la carpeta lib](./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png)


1. En la misma ventana de terminal, reinicie el servidor Striim ejecutando los siguientes comandos:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Striim tardará un minuto en iniciarse. Si desea ver el estado, ejecute el siguiente comando: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Ahora, vuelva a Azure y copie la dirección IP pública de su máquina virtual de Striim. 

   ![Copia de la dirección IP de la máquina virtual de Striim](./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png)

1. Para ir a la interfaz de usuario web de Striim, abra una nueva pestaña en un explorador y copie la IP pública seguida de: 9080. Inicie sesión con el nombre de usuario **administrador**, junto con la contraseña de administrador que especificó en Azure Portal.

   ![Inicio de sesión en Striim](./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png)

1. Ahora llegará a la página principal de Striim. Hay tres paneles diferentes: **Dashboards**, **Apps** y **SourcePreview**. El panel Dashboards le permite mover datos en tiempo real y visualizarlos. En el panel Apps se incluyen sus canalizaciones de streaming de datos o flujos de datos. En el lado derecho de la página se encuentra SourcePreview, donde puede obtener una versión preliminar de sus datos antes de moverlos.

1. Seleccione el panel **Apps**, por ahora nos centraremos en este panel. Existen numerosas aplicaciones de ejemplo que puede usar para aprender sobre Striim. De todas formas, en este artículo creará las suyas propias. Seleccione el botón **Agregar aplicación** en la esquina superior derecha.

   ![Incorporación de la aplicación Striim](./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png)

1. Hay diversas formas de crear aplicaciones Striim. Seleccione **Empezar de cero** para este escenario.

   ![Inicio de la aplicación desde cero](./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png)

1. Asigne un nombre descriptivo a su aplicación, algo como **oraToCosmosDB**, y seleccione **Guardar**.

   ![Creación de una aplicación](./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png)

1. Llegará a Flow Designer, donde puede arrastrar y colocar conectores listos para usar para crear sus aplicaciones de streaming. Escriba **Oracle** en la barra de búsqueda y arrastre y coloque el origen **Oracle CDC** en el lienzo de la aplicación.  

   ![Origen Oracle CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png)

1. Escriba las propiedades de configuración de origen de su instancia de Oracle. El nombre de origen es simplemente una convención de nomenclatura para la aplicación Striim. Puede usar un nombre como, por ejemplo, **src_onPremOracle**. Indique también otros datos como el tipo de adaptador, la dirección URL de conexión, el nombre de usuario, la contraseña o el nombre de la tabla. Seleccione **Guardar** para continuar.

   ![Configuración de parámetros de origen](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png)

1. Ahora, haga clic en el icono de onda del flujo para conectar la instancia de Azure Cosmos DB de destino. 

   ![Conectar a destino](./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png)

1. Escriba las propiedades de configuración de su instancia de Azure Cosmos DB de destino y seleccione **Guardar** para continuar.

   ![Configuración de propiedades de destino](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png)

   ![Configuración de propiedades de destino](./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png)

1. Ahora, continuaremos y ejecutaremos la aplicación Striim. En la barra de menús superior, seleccione **Creado** y, a continuación, **Implementar aplicación**. En la ventana de implementación, puede especificar si desea ejecutar determinadas partes de su aplicación en partes específicas de su topología de implementación. Puesto que estamos ejecutando en una topología de implementación simple a través de Azure, usaremos la opción predeterminada.

   ![Implementar la aplicación](./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png)


1. Ahora, continuaremos y obtendremos la versión preliminar del flujo para ver el flujo de datos a través de Striim. Haga clic en el icono de onda y en el icono de ojo situado junto al mismo. Tras la implementación, puede obtener la versión preliminar del flujo para ver el flujo de datos a través de esta. Seleccione el icono de **onda** y el **ojo** situado junto al mismo. Seleccione el botón **Implementado** en la barra de menús superior e **Iniciar aplicación**.

   ![Inicio de la aplicación](./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png)

1. Mediante el uso de un lector **CDC (captura de datos modificados)** , Striim recogerá solo los nuevos cambios en la base de datos. Si tiene datos que fluyen a través de sus tablas de origen, los verá. Sin embargo, puesto que esta es una tabla de ejemplo, el origen no se conecta a ninguna aplicación. Si usa un generador de datos de ejemplo, podrá insertar una cadena de eventos en su instancia de Oracle Database.

1. Verá cómo fluyen los datos a través de la plataforma Striim. Striim también recoge todos los metadatos asociados a su tabla, lo que resulta útil a la hora de supervisar los datos y garantizar que estos vayan a parar al destino adecuado.

   ![Configuración de la canalización CDC](./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png)

1. Por último, iniciemos sesión en Azure y vayamos a su cuenta de Azure Cosmos. Actualice Data Explorer y podrá ver que han llegado esos datos. 

Mediante el uso de la solución de Striim en Azure, puede migrar datos continuamente a Azure Cosmos DB desde diversos orígenes como Oracle, Cassandra, MongoDB y unos cuantos más a Azure Cosmos DB. En caso de que haya algún problema al configurar la ruta de migración con Striim, envíe una solicitud de soporte técnico en el [sitio web de Striim](https://go2.striim.com/request-support-striim).


## <a name="next-steps"></a>Pasos siguientes

* Si va a migrar datos a SQL API de Azure Cosmos DB, consulte [cómo migrar datos a la cuenta de Cassandra API mediante Striim](cosmosdb-sql-api-migrate-data-striim.md)

* [Supervisión y depuración de los datos con métricas de Azure Cosmos DB](use-metrics.md)