---
title: Protección de una base de datos única o agrupada en Azure SQL Database | Microsoft Docs
description: Un tutorial que le permite conocer las técnicas y características para proteger una base de datos única o agrupada en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.custom: seoapril2019
ms.openlocfilehash: 9850285482db4f5981c183b51152ba1a3ac4975c
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640129"
---
# <a name="tutorial-secure-a-single-or-pooled-database"></a>Tutorial: Protección de una base de datos única o agrupada

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Crear reglas de firewall de nivel de servidor y de base de datos.
> - Configurar un administrador de Azure Active Directory (Azure AD).
> - Administrar el acceso de usuario con la autenticación de SQL, la autenticación de Azure AD y cadenas de conexión seguras.
> - Habilitar características de seguridad, como la seguridad avanzada para los datos, la auditoría, el enmascaramiento de datos y el cifrado.

Azure SQL Database protege los datos de una base de datos única o agrupada al permitir:

- Limitar el acceso mediante reglas de firewall
- Usar mecanismos de autenticación que requieran la identidad
- Usar la autorización con pertenencias y permisos basados en roles
- Habilitar características de seguridad

> [!NOTE]
> Una base de datos de Azure SQL en una instancia administrada se protege mediante reglas de seguridad de red y puntos de conexión privados, como se describe en [Instancia administrada de Azure SQL Database](sql-database-managed-instance-index.yml) y [Arquitectura de conectividad](sql-database-managed-instance-connectivity-architecture.md).

Para más información, consulte los artículos [Información general sobre las funcionalidades de seguridad de Azure SQL Database](/azure/sql-database/sql-database-security-index) y [Funcionalidades](sql-database-security-overview.md).

> [!TIP]
> El siguiente módulo de Microsoft Learn le ayuda a aprender de forma gratuita sobre cómo [proteger la instancia de Azure SQL Database](https://docs.microsoft.com/learn/modules/secure-your-azure-sql-database/).

## <a name="prerequisites"></a>Requisitos previos

Para completar el tutorial, asegúrese de que cuenta con estos requisitos previos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms)
- Un servidor y una base de datos de Azure SQL
  - Créelos con [Azure Portal](sql-database-single-database-get-started.md), la [CLI](sql-database-cli-samples.md) o [PowerShell](sql-database-powershell-samples.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Para realizar todos los pasos del tutorial, inicie sesión en [Azure Portal](https://portal.azure.com/).

## <a name="create-firewall-rules"></a>Creación de reglas de firewall

En Azure, las bases de datos SQL están protegidas mediante firewalls. De forma predeterminada, todas las conexiones al servidor y a las bases de datos se rechazan, excepto las conexiones desde otros servicios de Azure. Para más información, consulte [Reglas de firewall de nivel de base de datos y de nivel de servidor de Azure SQL Database](sql-database-firewall-configure.md).

Establezca **Permitir el acceso a servicios de Azure** en **OFF** (DESACTIVADO) para configurar la opción más segura. A continuación, cree una [dirección IP reservada (implementación clásica)](../virtual-network/virtual-networks-reserved-public-ip.md) para el recurso que tenga que conectar, como una máquina virtual de Azure o un servicio en la nube, y permita solo a esa dirección IP el acceso mediante el firewall. Si usa el modelo de implementación de [Resource Manager](/azure/virtual-network/virtual-network-ip-addresses-overview-arm), es necesaria una dirección IP pública dedicada para cada recurso.

> [!NOTE]
> SQL Database se comunica a través del puerto 1433. Si intenta conectarse desde dentro de una red corporativa, es posible que el firewall de la red no permita el tráfico de salida a través del puerto 1433. En ese caso, no puede conectarse al servidor de Azure SQL Database, salvo que el administrador abra el puerto 1433.

### <a name="set-up-sql-database-server-firewall-rules"></a>Configuración de reglas de firewall del servidor de SQL Database

Las reglas de firewall de IP en el nivel de servidor se aplican a todas las bases de datos del mismo servidor de SQL Database.

Para configurar una regla de firewall de nivel de servidor:

1. En Azure Portal, seleccione **Bases de datos SQL** en el menú de la izquierda y seleccione la base de datos en la página **Bases de datos SQL**.

    ![regla de firewall del servidor](./media/sql-database-security-tutorial/server-name.png)

    > [!NOTE]
    > Copie el nombre completo del servidor (como *suservidor.database.windows.net*) para usarlo más adelante en el tutorial.

1. En la página **Información general**, seleccione **Establecer el firewall del servidor**. Se abre la página **Configuración del firewall** del servidor de bases de datos.

   1. Seleccione **Agregar IP de cliente** en la barra de herramientas para agregar la dirección IP actual a la nueva regla de firewall. La regla puede abrir el puerto 1433 para una única dirección IP o un intervalo de direcciones IP. Seleccione **Guardar**.

      ![establecer regla de firewall del servidor](./media/sql-database-security-tutorial/server-firewall-rule2.png)

   1. Seleccione **Aceptar** y, después, cierre la página **Configuración de firewall**.

Ahora puede conectarse a cualquier base de datos del servidor con la dirección IP o el intervalo de direcciones IP especificados.

> [!IMPORTANT]
> De forma predeterminada, el acceso mediante el firewall de SQL Database se habilita para todos los servicios de Azure en **Permitir el acceso a servicios de Azure**. Elija **OFF** (DESACTIVADO) para deshabilitar el acceso para todos los servicios de Azure.

### <a name="setup-database-firewall-rules"></a>Configuración de reglas de firewall para las bases de datos

Las reglas de firewall de nivel de base de datos solo se aplican a bases de datos individuales. La base de datos conservará estas reglas durante una conmutación por error del servidor. Las reglas de firewall de nivel de base de datos solo pueden configurarse mediante instrucciones de Transact-SQL (T-SQL) y únicamente después de haber configurado una regla de firewall de nivel de servidor.

Para configurar una regla de firewall de nivel de base de datos:

1. Conéctese a la base de datos, por ejemplo, mediante [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. En el **Explorador de objetos**, haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**.

1. En la ventana de consulta, agregue esta instrucción y reemplace la dirección IP por su dirección IP pública:

    ```sql
    EXECUTE sp_set_database_firewall_rule N'Example DB Rule','0.0.0.4','0.0.0.4';
    ```

1. En la barra de herramientas, seleccione **Ejecutar** para crear la regla de firewall.

> [!NOTE]
> También puede crear una regla de firewall de nivel de servidor en SSMS mediante el comando [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database?view=azuresqldb-current), aunque debe estar conectado a la base de datos *maestra*.

## <a name="create-an-azure-ad-admin"></a>Creación de un administrador de Azure AD

Asegúrese de que está usando el dominio administrado de Azure Active Directory (AD) adecuado. Seleccione el dominio de AD en la esquina superior derecha de Azure Portal. Este proceso confirma que la misma suscripción se usa tanto para Azure AD como para el servidor SQL Server que hospeda el almacenamiento de datos o la base de datos de Azure SQL.

   ![elegir ad](./media/sql-database-security-tutorial/8choose-ad.png)

Para establecer el administrador de Azure AD:

1. En Azure Portal, en la página **SQL Server**, seleccione **Administrador de Active Directory**. A continuación, seleccione **Establecer administrador**.

    ![seleccione active directory](./media/sql-database-security-tutorial/admin-settings.png)  

    > [!IMPORTANT]
    > Debe ser "Administrador de empresa" o "Administrador global" para realizar esta tarea.

1. En la página **Agregar administrador**, busque y seleccione el usuario o el grupo de AD, y elija **Seleccionar**. Se muestran todos los miembros y grupos de Active Directory; no se admiten las entradas atenuadas como administradores de Azure AD. Consulte [Características y limitaciones de Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations).

    ![Seleccionar administrador](./media/sql-database-security-tutorial/admin-select.png)

    > [!IMPORTANT]
    > El control de acceso basado en rol (RBAC) se aplica solo al portal y no se propaga a SQL Server.

1. En la parte superior de la página **Administrador de Active Directory**, seleccione **Guardar**.

    El proceso de cambio de un administrador puede tardar varios minutos. El nuevo administrador aparecerá en el cuadro **Administrador de Active Directory**.

> [!NOTE]
> Al configurar un administrador de Azure AD, el nombre del nuevo administrador (usuario o grupo) no puede existir como un usuario de autenticación de SQL Server en la base de datos *maestra*. Si estuviera, se producirá un error en la instalación, se revertirán los cambios y se indicará que ese nombre de administrador ya existe. Como el usuario de autenticación de SQL Server no forma parte de Azure AD, se producirá un error cada vez que se intente conectar el usuario con la autenticación de Azure AD.

Para más información acerca de la configuración de Azure AD, consulte:

- [Integración de las identidades locales con Azure AD](../active-directory/hybrid/whatis-hybrid-identity.md)
- [Incorporación del nombre de dominio personalizado mediante el portal de Azure Active Directory](../active-directory/active-directory-domains-add-azure-portal.md)
- [Microsoft Azure now supports federation with Windows Server AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure ahora admite la federación con Windows Server AD)
- [Administración del directorio de Azure AD](../active-directory/fundamentals/active-directory-administer.md)
- [Administración de Azure AD con PowerShell](/powershell/azure/overview?view=azureadps-2.0)
- [La identidad híbrida requería puertos y protocolos](../active-directory/hybrid/reference-connect-ports.md)

## <a name="manage-database-access"></a>Administración del acceso a las bases de datos

Para administrar el acceso de la base de datos, agregue usuarios a la base de datos o permita el acceso de los usuarios con cadenas de conexión segura. Las cadenas de conexión son útiles para las aplicaciones externas. Para más información, consulte [Control de acceso de Azure SQL](sql-database-control-access.md) y [Autenticación de Azure AD](sql-database-aad-authentication.md).

Para agregar usuarios, elija el tipo de autenticación de base de datos:

- **Autenticación de SQL**, que usa un nombre de usuario y una contraseña para iniciar sesión, y son válidos únicamente en el contexto de una base de datos específica dentro de un servidor.

- **Autenticación de Azure AD**, que usa identidades administradas por Azure AD.

### <a name="sql-authentication"></a>Autenticación de SQL

Para agregar un usuario con la autenticación de SQL:

1. Conéctese a la base de datos, por ejemplo, mediante [SQL Server Management Studio](./sql-database-connect-query-ssms.md).

1. En el **Explorador de objetos**, haga clic con el botón derecho en la base de datos y elija **Nueva consulta**.

1. En la ventana de consulta, escriba el comando siguiente:

    ```sql
    CREATE USER ApplicationUser WITH PASSWORD = 'YourStrongPassword1';
    ```

1. En la barra de herramientas, seleccione **Ejecutar** para crear el usuario.

1. De forma predeterminada, el usuario puede conectarse a la base de datos, pero no tiene permisos para leer o escribir datos. Para conceder estos permisos, ejecute los dos comandos siguientes en una nueva ventana de consulta:

    ```sql
    ALTER ROLE db_datareader ADD MEMBER ApplicationUser;
    ALTER ROLE db_datawriter ADD MEMBER ApplicationUser;
    ```

> [!NOTE]
> Cree cuentas sin privilegios de administrador en el nivel de base de datos, a menos que haya que ejecutar tareas de administrador tales como crear nuevos usuarios.

### <a name="azure-ad-authentication"></a>Autenticación de Azure AD

La autenticación de Azure Active Directory requiere que los usuarios de la base de datos se creen como independientes. Un usuario de base de datos independiente se asigna a una identidad en el directorio de Azure AD asociado a la base de datos y no tiene inicio de sesión en la base de datos *maestra*. La identidad de Azure AD puede ser una cuenta de usuario individual o un grupo. Para más información, consulte [Usuarios de base de datos independiente: hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx) y revise el [Tutorial de Azure AD](./sql-database-aad-authentication-configure.md) acerca de cómo autenticarse con Azure AD.

> [!NOTE]
> Los usuarios de base de datos (a excepción de los administradores) no se pueden crear mediante Azure Portal. Los roles de Azure RBAC no se propagan a los almacenes de servidores, bases de datos o datos SQL. Se utilizan solo para administrar los recursos de Azure y no se aplican a los permisos de base de datos.
>
> Por ejemplo, el rol *Colaborador de SQL Server* no concede acceso para conectarse a una base de datos o a un almacenamiento de datos. Este permiso tiene que concederse dentro de la base de datos mediante instrucciones de T-SQL.

> [!IMPORTANT]
> No se admiten caracteres especiales, como los dos puntos `:` o la "y" comercial `&`, en los nombres de usuario de las instrucciones `CREATE LOGIN` y `CREATE USER` de T-SQL.

Para agregar un usuario con la autenticación de Azure AD:

1. Conéctese a su servidor SQL de Azure con una cuenta de Azure AD con al menos el permiso *ALTER ANY USER*.

1. En el **Explorador de objetos**, haga clic con el botón derecho en la base de datos y seleccione **Nueva consulta**.

1. En la ventana de consulta, escriba el siguiente comando y reemplace `<Azure_AD_principal_name>` por el nombre principal del usuario de Azure AD o el nombre para mostrar del grupo de Azure AD:

   ```sql
   CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
   ```

> [!NOTE]
> Los usuarios de Azure AD se marcan en los metadatos de la base de datos con el tipo `E (EXTERNAL_USER)` y el tipo `X (EXTERNAL_GROUPS)` para grupos. Para obtener más información, consulte [sys.database_principals (Transact-SQL)](/sql/relational-databases/system-catalog-views/sys-database-principals-transact-sql).

### <a name="secure-connection-strings"></a>Cadenas de conexión seguras

Para garantizar una conexión cifrada y segura entre la aplicación cliente y SQL Database, se debe configurar una cadena de conexión para:

- Solicitar una conexión cifrada.
- No confiar en el certificado de servidor.

La conexión se establece mediante la Seguridad de la capa de transporte (TLS) y se reduce el riesgo de ataques de tipo "Man in the middle". Las cadenas de conexión están disponibles para cada base de datos y están preconfiguradas para admitir los controladores de cliente como ADO.NET, JDBC, ODBC y PHP. Para información sobre TLS y la conectividad, consulte [Consideraciones de TLS](sql-database-connect-query.md#tls-considerations-for-sql-database-connectivity).

Para copiar una cadena de conexión segura:

1. En Azure Portal, seleccione **Bases de datos SQL** en el menú de la izquierda y seleccione la base de datos en la página **Bases de datos SQL**.

1. En la página **Información general**, haga clic en **Mostrar las cadenas de conexión de la base de datos**.

1. Seleccione una pestaña de controlador y copie la cadena de conexión completa.

    ![Cadena de conexión ADO.NET](./media/sql-database-security-tutorial/connection.png)

## <a name="enable-security-features"></a>Habilitar características de seguridad

Azure SQL Database proporciona características de seguridad que son accesibles mediante Azure Portal. Estas características están disponibles tanto para la base de datos como para el servidor, excepto el enmascaramiento de datos, que solo está disponible en la base de datos. Para más información, consulte [Advanced Data Security](sql-database-advanced-data-security.md), [Auditoría](sql-database-auditing.md), [Enmascaramiento dinámico de datos](sql-database-dynamic-data-masking-get-started.md) y [Cifrado de datos transparente](transparent-data-encryption-azure-sql.md).

### <a name="advanced-data-security"></a>Advanced Data Security

La característica Advanced Data Security detecta amenazas a medida que se producen y proporciona alertas de seguridad sobre actividades anómalas. Los usuarios pueden explorar los eventos sospechosos con la característica de autoría y determinar si el evento pretendía acceder a los datos de la base de datos, infringir su seguridad o aprovechar sus vulnerabilidades. Los usuarios también obtienen una visión general de la seguridad que incluye una evaluación de las vulnerabilidades y la herramienta de detección y clasificación de datos.

> [!NOTE]
> Un ejemplo de amenaza es la inyección de código SQL, un proceso mediante el cual los atacantes insertan SQL malintencionado en los datos de entrada de la aplicación. Después, una aplicación puede ejecutar el código SQL malintencionado sin saberlo y permitir el acceso de los atacantes para infringir la seguridad o modificar los datos de la base de datos.

Para habilitar Advanced Data Security:

1. En Azure Portal, seleccione **Bases de datos SQL** en el menú de la izquierda y seleccione la base de datos en la página **Bases de datos SQL**.

1. En la página **Información general**, seleccione el vínculo **Nombre de servidor**. Se abrirá la página del servidor de bases de datos.

1. En la página **Servidor SQL Server**, busque la sección **Seguridad** y seleccione **Advanced Data Security**.

   1. Seleccione **ACTIVADA** en **Advanced Data Security** para habilitar la característica. Elija una cuenta de almacenamiento para guardar los resultados de la evaluación de vulnerabilidad. Después, seleccione **Guardar**.

      ![Panel de navegación](./media/sql-database-security-tutorial/threat-settings.png)

      También puede configurar mensajes de correo electrónico para recibir las alertas de seguridad, los detalles de almacenamiento y los tipos de detección de amenazas.

1. Vuelva a la página **Bases de datos SQL** de la base de datos y seleccione **Advanced Data Security** en la sección **Seguridad**. Aquí encontrará varios indicadores de seguridad disponibles para la base de datos.

    ![Estado de amenaza](./media/sql-database-security-tutorial/threat-status.png)

Si se detectan actividades anómalas, recibirá un correo electrónico con información sobre el evento. Por ejemplo, la naturaleza de la actividad, la base de datos, el servidor, la hora del evento, las posibles causas y las acciones recomendadas para investigar y mitigar la posible amenaza. Si se recibe dicho correo electrónico, seleccione el vínculo **Registro de auditoría de SQL Azure** para abrir Azure Portal y mostrar los registros de auditorías pertinentes para el momento del evento.

   ![Correo electrónico de detección de amenazas](./media/sql-database-security-tutorial/threat-email.png)

### <a name="auditing"></a>Auditoría

La característica de auditoría realiza un seguimiento de los eventos de base de datos y escribe los eventos en un registro de auditoría en un almacenamiento de Azure, registros de Azure Monitor o en un centro de eventos. La auditoría ayuda a mantener el cumplimiento de las normativas y conocer la actividad de las bases de datos, así como las discrepancias y anomalías que pueden indicar la existencia de posibles infracciones de la seguridad.

Para habilitar la auditoría:

1. En Azure Portal, seleccione **Bases de datos SQL** en el menú de la izquierda y seleccione la base de datos en la página **Bases de datos SQL**.

1. En la sección **Seguridad**, seleccione **Auditoría**.

1. En las opciones de **Auditoría**, establezca los valores siguientes:

   1. Establezca **Auditoría** en **ACTIVADA**.

   1. En **Destino del registro de auditoría**, seleccione alguna de las opciones siguientes:

       - **Almacenamiento**, una cuenta de Azure Storage donde se guardan los registros de eventos, que se pueden descargar como archivos *.xel*.

          > [!TIP]
          > Use la misma cuenta de almacenamiento para todas las bases de datos auditadas con el fin de obtener el máximo partido de las plantillas de informes de auditorías.

       - **Log Analytics**, que almacena automáticamente los eventos para realizar consultas o un análisis más profundo.

           > [!NOTE]
           > Se requiere un **área de trabajo de Log Analytics** para admitir características avanzadas, como análisis, reglas de alerta personalizadas y exportaciones de Excel o Power BI. Sin un área de trabajo, solo está disponible el editor de consultas.

       - **Centro de eventos**, que permite enrutar los eventos para usarlos en otras aplicaciones.

   1. Seleccione **Guardar**.

      ![Configuración de auditoría](./media/sql-database-security-tutorial/audit-settings.png)

1. Ahora puede seleccionar **Ver registros de auditoría** para ver los datos de eventos de base de datos.

    ![Registros de auditoría](./media/sql-database-security-tutorial/audit-records.png)

> [!IMPORTANT]
> Consulte en [Auditoría de base de datos SQL](sql-database-auditing.md) cómo personalizar aún más los eventos de auditoría con PowerShell o la API REST.

### <a name="dynamic-data-masking"></a>Enmascaramiento de datos dinámicos

La función de enmascaramiento de datos ocultará automáticamente los datos confidenciales de la base de datos.

Para habilitar el enmascaramiento de datos:

1. En Azure Portal, seleccione **Bases de datos SQL** en el menú de la izquierda y seleccione la base de datos en la página **Bases de datos SQL**.

1. En la sección **Seguridad**, seleccione **Enmascaramiento dinámico de datos**.

1. En la opción **Enmascaramiento dinámico de datos**, seleccione **Agregar máscara** para agregar una regla de enmascaramiento. Azure rellenará automáticamente los esquemas de base de datos disponibles, las tablas y las columnas para elegir.

    ![Configuración de máscara](./media/sql-database-security-tutorial/mask-settings.png)

1. Seleccione **Guardar**. Ahora se enmascara la información seleccionada para mantener la privacidad.

    ![Ejemplo de máscara](./media/sql-database-security-tutorial/mask-query.png)

### <a name="transparent-data-encryption"></a>Cifrado de datos transparente

La característica de cifrado cifra automáticamente los datos en reposo y no requiere cambios en las aplicaciones que acceden a la base de datos cifrada. En las bases de datos nuevas, el cifrado está activado de forma predeterminada. También puede cifrar los datos con SSMS y la característica [Always encrypted](sql-database-always-encrypted.md).

Para habilitar o comprobar el cifrado:

1. En Azure Portal, seleccione **Bases de datos SQL** en el menú de la izquierda y seleccione la base de datos en la página **Bases de datos SQL**.

1. En la sección **Seguridad**, seleccione **Cifrado de datos transparente**.

1. Si es necesario, establezca **Cifrado de datos** en **ACTIVADO**. Seleccione **Guardar**.

    ![Cifrado de datos transparente](./media/sql-database-security-tutorial/encryption-settings.png)

> [!NOTE]
> Para ver el estado de cifrado, conéctese a la base de datos mediante [SSMS](./sql-database-connect-query-ssms.md) y consulte la columna `encryption_state` de la vista [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql). El estado `3` indica que la base de datos está cifrada.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a mejorar la seguridad de una base de datos con unos sencillos pasos. Ha aprendido a:

> [!div class="checklist"]
> - Crear reglas de firewall de nivel de servidor y de base de datos.
> - Configurar un administrador de Azure Active Directory (Azure AD).
> - Administrar el acceso de usuario con la autenticación de SQL, la autenticación de Azure AD y cadenas de conexión seguras.
> - Habilitar características de seguridad, como la seguridad avanzada para los datos, la auditoría, el enmascaramiento de datos y el cifrado.

En el siguiente tutorial aprenderá a implementar una distribución geográfica.

> [!div class="nextstepaction"]
>[Implementación de una base de datos distribuida geográficamente](sql-database-implement-geo-distributed-database.md)
