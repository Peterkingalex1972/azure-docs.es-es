---
title: 'Tutorial: Adición de un grupo elástico de Azure SQL Database a un grupo de conmutación por error | Microsoft Docs'
description: Agregue un grupo elástico de Azure SQL Database a un grupo de conmutación por error mediante Azure Portal, PowerShell o la CLI de Azure.
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 06/19/2019
ms.openlocfilehash: 5dd241fed757669cf8bccd96a1de948e8d73a021
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69033256"
---
# <a name="tutorial-add-an-azure-sql-database-elastic-pool-to-a-failover-group"></a>Tutorial: Adición de un grupo elástico de Azure SQL Database a un grupo de conmutación por error

Configure un grupo de conmutación por error para un grupo elástico de Azure SQL Database y pruebe la conmutación por error mediante Azure Portal.  En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Crear una base de datos única de Azure SQL Database.
> - Agregar una base de datos única a un grupo elástico 
> - Crear un [grupo de conmutación por error](sql-database-auto-failover-group.md) para un grupo elástico entre dos servidores lógicos de SQL Server
> - Probar la conmutación por error.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, asegúrese de disponer de los siguientes elementos: 

- Una suscripción de Azure. [Cree una cuenta gratuita](https://azure.microsoft.com/free/) si aún no tiene una.


## <a name="1---create-a-single-database"></a>1\. Creación de una base de datos única 

[!INCLUDE [sql-database-create-single-database](includes/sql-database-create-single-database.md)]

## <a name="2---add-single-database-to-elastic-pool"></a>2\. Adición de una base de datos única a un grupo elástico

1. Elija **Crear un recurso** en la esquina superior izquierda de [Azure Portal](https://portal.azure.com).
1. Escriba `elastic pool` en el cuadro de búsqueda, presione Entrar, seleccione el icono del **grupo de bases de datos elásticas de SQL** y, a continuación, seleccione **Crear**. 

    ![Elección del grupo elástico en Marketplace](media/sql-database-elastic-pool-create-failover-group-tutorial/elastic-pool-market-place.png)

1. Configure el grupo elástico con los siguientes valores:
   - **Nombre**: Proporcione un nombre único para el grupo elástico, como `myElasticPool`. 
   - **Suscripción**: Seleccione la suscripción en la lista desplegable.
   - **ResourceGroup**: Seleccione `myResourceGroup` en la lista desplegable, el grupo de recursos que ha creado en la sección 1. 
   - **Servidor**: Seleccione el servidor que ha creado en la sección 1 en la lista desplegable.  

       ![Creación de un nuevo servidor para el grupo elástico](media/sql-database-elastic-pool-create-failover-group-tutorial/use-existing-server-for-elastic-pool.png)

   - **Proceso y almacenamiento**: Seleccione **Configurar grupo elástico** para configurar el proceso y el almacenamiento, y agregue la base de datos única al grupo elástico. En la pestaña **Configuración de grupo**, deje el valor predeterminado de Gen5 y, con dos núcleos virtuales y 32 GB. 

1. En la página **Configurar**, seleccione la pestaña **Bases de datos** y, después, elija **Agregar base de datos**. Elija la base de datos que ha creado en la sección 1 y, a continuación, seleccione **Aplicar** para agregarla al grupo elástico. Seleccione **Aplicar** de nuevo para aplicar la configuración del grupo elástico y cerrar la página **Configurar**. 

    ![Adición de la base de datos SQL a un grupo elástico](media/sql-database-elastic-pool-create-failover-group-tutorial/add-database-to-elastic-pool.png)

1. Seleccione **Revisar y crear** para revisar la configuración del grupo elástico y después seleccione **Crear** para crear el grupo elástico. 


## <a name="3---create-the-failover-group"></a>3\. Creación de un grupo de conmutación por error 
En este paso, va a crear un [grupo de conmutación por error](sql-database-auto-failover-group.md) entre un servidor de Azure SQL Server existente y uno nuevo en otra región. A continuación, agregue el grupo elástico al grupo de conmutación por error. 


1. Seleccione **Todos los servicios** en la esquina superior izquierda de [Azure Portal](https://portal.azure.com). 
1. Escriba `sql servers` en el cuadro de búsqueda. 
1. (Opcional) Seleccione el icono de estrella junto a Servidores SQL Server para marcar como favorito **Servidor de SQL Server** y agréguelo al panel de navegación izquierdo. 
    
    ![Localización de servidores SQL Server](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Seleccione **Servidores SQL Server** y elija el servidor que ha creado en la sección 1.
1. Seleccione **Grupos de conmutación por error** en el panel **Configuración** y, después, seleccione **Agregar grupo** para crear un nuevo grupo de conmutación por error. 

    ![Adición de un grupo de conmutación por error](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. En la página **Grupo de conmutación por error**, escriba o seleccione los valores siguientes y, después, seleccione **Crear**:
    - **Nombre del grupo de conmutación por error** Escriba un nombre del grupo de conmutación por error único, como `failovergrouptutorial`. 
    - **Servidor secundario**: Seleccione la opción para *configurar los valores obligatorios* y, a continuación, elija **Crear un nuevo servidor**. Como alternativa, puede elegir un servidor ya existente como servidor secundario. Después de especificar los siguientes valores para el nuevo servidor secundario, haga clic en **Seleccionar**. 
        - **Nombre del servidor**: Escriba un nombre único para el servidor secundario, como `mysqlsecondary`. 
        - **Inicio de sesión del administrador del servidor**: Escriba `azureuser`
        - **Contraseña**: Escriba una contraseña compleja que cumpla los requisitos de contraseña.
        - **Ubicación**: Elija una ubicación en la lista desplegable, como Este de EE. UU. 2. Esta ubicación no puede ser la misma que la del servidor principal.

       > [!NOTE]
       > La configuración del firewall y de inicio de sesión del servidor debe coincidir con la del servidor principal. 
    
       ![Creación de un servidor secundario para el grupo de conmutación por error](media/sql-database-single-database-create-failover-group-tutorial/create-secondary-failover-server.png)

1. Seleccione **Bases de datos del grupo** y, a continuación, seleccione el grupo elástico que creó en la sección 2. Debería aparecer una advertencia que le pide que cree un grupo elástico en el servidor secundario. Seleccione la advertencia y, a continuación, elija **Aceptar** para crear el grupo elástico en el servidor secundario. 
        
    ![Agregar un grupo elástico a un grupo de conmutación por error](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Elija **Seleccionar** para aplicar la configuración del grupo elástico al grupo de conmutación por error y, después, seleccione **Crear** para crear el grupo de conmutación por error. Al agregar el grupo elástico al grupo de conmutación por error, se iniciará automáticamente el proceso de replicación geográfica. 


## <a name="4---test-failover"></a>4\. Prueba de la conmutación por error 
En este paso, se producirá un error en el grupo de conmutación por error en el servidor secundario y, a continuación, se realizará la conmutación por recuperación mediante Azure Portal. 

1. Vaya a **Servidores de SQL Server** en [Azure Portal](https://portal.azure.com). 
1. Seleccione **Grupos de conmutación por error** en el panel **Configuración** y, a continuación, elija el grupo de conmutación por error que ha creado en la sección 2. 
  
   ![Seleccione el grupo de conmutación por error en el portal.](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Revise cuál es el servidor principal y cuál es el secundario. 
1. Seleccione **Conmutación por error** en el panel de tareas para conmutar por error el grupo de conmutación por error que contiene el grupo elástico. 
1. Seleccione **Sí** en la advertencia que le notifica que las sesiones de TDS se desconectarán. 

   ![Conmutación por error del grupo de conmutación por error que contiene la base de datos SQL](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Revise cuál es el servidor principal y cuál es el secundario. Si la conmutación por error se realiza correctamente, los dos servidores deben tener los roles intercambiados. 
1. Seleccionar de nuevo **Conmutación por error** para que el grupo de conmutación por error vuelva a la configuración original. 

## <a name="clean-up-resources"></a>Limpieza de recursos 
Limpie los recursos mediante la eliminación del grupo de recursos. 

1. Vaya a su grupo de recursos en [Azure Portal](https://portal.azure.com).
1. Seleccione **Eliminar grupo de recursos** para eliminar todos los recursos del grupo, así como el propio grupo de recursos. 
1. En la nueva ventana, escriba el nombre del grupo de recursos, `myResourceGroup`, y luego seleccione **Eliminar** para eliminar el grupo de recursos.  


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha agregado una base de datos única de Azure SQL Database a un grupo de conmutación por error y ha probado la conmutación por error. Ha aprendido a:

> [!div class="checklist"]
> - Crear una base de datos única de Azure SQL Database.
> - Agregar una base de datos única a un grupo elástico 
> - Crear un [grupo de conmutación por error](sql-database-auto-failover-group.md) para un grupo elástico entre dos servidores lógicos de SQL Server
> - Probar la conmutación por error.

Avance al siguiente tutorial sobre cómo migrar mediante DMS.

> [!div class="nextstepaction"]
> [Tutorial: Migración de SQL Server a una base de datos agrupada mediante DMS](../dms/tutorial-sql-server-to-azure-sql.md?toc=/azure/sql-database/toc.json)
