---
title: Seguridad de una instancia administrada de Azure SQL Database mediante entidades de seguridad del servidor de Azure AD | Microsoft Docs
description: Conozca las técnicas y características para proteger una instancia administrada en Azure SQL Database y usar las entidades de seguridad del servidor de Azure AD (inicios de sesión)
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: tutorial
author: VanMSFT
ms.author: vanto
ms.reviewer: carlrab
ms.date: 02/20/2019
ms.openlocfilehash: 87bd22ec4f2cfae62d1f80284ad8346ca292d016
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567676"
---
# <a name="tutorial-managed-instance-security-in-azure-sql-database-using-azure-ad-server-principals-logins"></a>Tutorial: Seguridad de una instancia administrada de Azure SQL Database mediante entidades de seguridad del servidor de Azure AD (inicios de sesión)

Instancia administrada proporciona casi todas las características de seguridad que tiene el último motor de base de datos de SQL Server local (Enterprise Edition):

- Límite del acceso en un entorno aislado
- Uso de mecanismos de autenticación que requieran la identidad (Azure AD, autenticación de SQL)
- Usar la autorización con pertenencias y permisos basados en roles
- Habilitar características de seguridad

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Crear una entidad de seguridad de un servidor (inicio de sesión) de Azure Active Directory para una instancia administrada
> - Conceder permisos a las entidades de seguridad (inicios de sesión) de un servidor de Azure AD en una instancia administrada
> - Crear usuarios de Azure AD desde entidades de seguridad (inicios de sesión) de un servidor de Azure AD
> - Asignar permisos a usuarios de Azure AD y administrar la seguridad de base de datos
> - Usar la suplantación con usuarios de Azure AD
> - Usar consultas entre bases de datos con usuarios de Azure AD
> - Aprenda sobre las características de seguridad, como la protección contra amenazas, la auditoría, el enmascaramiento de datos y el cifrado.

> [!NOTE]
> Las entidades de seguridad (inicios de sesión) de un servidor de Azure AD para instancias administradas se encuentran en **versión preliminar pública**.

Para más información, consulte los artículos de [introducción a Instancia administrada de Azure SQL Database](sql-database-managed-instance-index.yml) y sus [funcionalidades](sql-database-managed-instance.md).

## <a name="prerequisites"></a>Requisitos previos

Para completar el tutorial, asegúrese de que cuenta con estos requisitos previos:

- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)
- Una instancia administrada de Azure SQL Database
  - Siga este artículo: [Inicio rápido: Creación de una Instancia administrada de Azure SQL Database](sql-database-managed-instance-get-started.md)
- Poder acceder a la instancia administrada y [aprovisionar un administrador de Azure AD para ella](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance). Para obtener más información, consulte:
    - [Conexión de aplicaciones a una instancia administrada](sql-database-managed-instance-connect-app.md). 
    - [Arquitectura de conectividad de la instancia administrada](sql-database-managed-instance-connectivity-architecture.md)
    - [Configuración y administración de la autenticación de Azure Active Directory con SQL](sql-database-aad-authentication-configure.md)

## <a name="limiting-access-to-your-managed-instance"></a>Límite del acceso a la instancia administrada

Solo se puede acceder a las instancias administradas mediante una dirección IP privada. No hay puntos de conexión de servicio disponibles para conectarse a una instancia administrada desde fuera de la red de instancia administrada. Al igual que en un entorno aislado de SQL Server local, las aplicaciones o los usuarios necesitan acceso a la red de instancia administrada (VNet) antes de que se pueda establecer una conexión. Para más información, consulte el artículo [Conexión de la aplicación a una instancia administrada](sql-database-managed-instance-connect-app.md).

> [!NOTE] 
> Dado que solo se puede acceder a las instancias administradas dentro de su red virtual, no se aplican [las reglas de firewall de SQL Database](sql-database-firewall-configure.md). Las instancias administradas tienen su propio [firewall integrado](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).

## <a name="create-an-azure-ad-server-principal-login-for-a-managed-instance-using-ssms"></a>Creación de una entidad de seguridad (inicio de sesión) de un servidor de Azure AD para una instancia administrada mediante SSMS

La primera entidad de seguridad (inicio de sesión) de un servidor de Azure AD debe crearla la cuenta estándar de SQL Server (no Azure AD) que es `sysadmin`. Consulte los artículos siguientes para ver ejemplos de conexión a la instancia administrada:

- [Inicio rápido: Configuración de una máquina virtual de Azure para conectarse a una instancia administrada](sql-database-managed-instance-configure-vm.md)
- [Inicio rápido: Configuración de una conexión de punto a sitio a una instancia administrada desde el entorno local](sql-database-managed-instance-configure-p2s.md)

> [!IMPORTANT]
> El administrador de Azure AD usado para configurar la instancia administrada no se puede usar para crear una entidad de seguridad (inicio de sesión) de un servidor de Azure AD dentro de la instancia administrada. Debe crear la primera entidad de seguridad (inicio de sesión) de un servidor de Azure AD mediante una cuenta de SQL Server que sea `sysadmin`. Esta es una limitación temporal que se quitará cuando las entidades de seguridad (inicios de sesión) de un servidor de Azure AD pasen a ser de disponibilidad general. Si intenta usar una cuenta de administrador de Azure AD para crear el inicio de sesión, verá el siguiente error: `Msg 15247, Level 16, State 1, Line 1 User does not have permission to perform this action.`

1. Inicie sesión en la instancia administrada con una cuenta estándar de SQL Server (no de Azure AD) que sea `sysadmin`, mediante [SQL Server Management Studio](sql-database-managed-instance-configure-p2s.md#use-ssms-to-connect-to-the-managed-instance).

2. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.

3. En la ventana de consulta, utilice la siguiente sintaxis para crear un inicio de sesión para una cuenta local de Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    En este ejemplo se crea un inicio de sesión para la cuenta nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    USE master
    GO
    CREATE LOGIN [nativeuser@aadsqlmi.onmicrosoft.com] FROM EXTERNAL PROVIDER
    GO
    ```

4. En la barra de herramientas, seleccione **Ejecutar** para crear el inicio de sesión.

5. Compruebe el inicio de sesión recientemente agregado, mediante la ejecución del siguiente comando T-SQL:

    ```sql
    SELECT *  
    FROM sys.server_principals;  
    GO
    ```

    ![native-login.png](media/sql-database-managed-instance-security-tutorial/native-login.png)

Para más información, consulte [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current).

## <a name="granting-permissions-to-allow-the-creation-of-managed-instance-logins"></a>Concesión de permisos para permitir la creación de inicios de sesión de instancia administrada

Para crear otras entidades de seguridad (inicios de sesión) de un servidor de Azure AD, se deben otorgar roles o permisos de SQL Server a la entidad de seguridad (SQL o Azure AD).

### <a name="sql-authentication"></a>Autenticación de SQL

- Si el inicio de sesión es una entidad de seguridad de SQL, solo los inicios de sesión que forman parte del rol `sysadmin` pueden utilizar el comando create para crear inicios de sesión para una cuenta de Azure AD.

### <a name="azure-ad-authentication"></a>Autenticación de Azure AD

- Para permitir que la entidad de seguridad (inicio de sesión) de un servidor de Azure AD recién creada pueda crear otros inicios de sesión para otros usuarios, grupos o aplicaciones de Azure AD, conceda el rol del servidor `sysadmin` o `securityadmin` de inicio de sesión. 
- Como mínimo, se debe conceder el permiso **ALTER ANY LOGIN** a la entidad de seguridad (inicio de sesión) de un servidor de Azure AD para crear otras entidades de seguridad (inicios de sesión) de un servidor de Azure AD. 
- De forma predeterminada, el permiso estándar concedido a las nuevas entidades de seguridad (inicios de sesión) de un servidor de Azure AD es: **CONNECT SQL** y **VIEW ANY DATABASE**.
- El rol del servidor `sysadmin` se puede conceder a muchas entidades de seguridad (inicios de sesión) de un servidor de Azure AD dentro de una instancia administrada.

Para agregar el inicio de sesión para el rol de servidor `sysadmin`:

1. Vuelva a iniciar sesión en la instancia administrada o use la conexión existente con la entidad de seguridad SQL que tenga el rol `sysadmin`.

1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.

1. Conceda a la entidad de seguridad (inicio de sesión) de un servidor de Azure AD el rol de servidor `sysadmin` mediante la siguiente sintaxis de T-SQL:

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER login_name
    GO
    ```

    En el siguiente ejemplo se concede el rol de servidor `sysadmin` al inicio de sesión nativeuser@aadsqlmi.onmicrosoft.com.

    ```sql
    ALTER SERVER ROLE sysadmin ADD MEMBER [nativeuser@aadsqlmi.onmicrosoft.com]
    GO
    ```

## <a name="create-additional-azure-ad-server-principals-logins-using-ssms"></a>Creación de entidades de seguridad (inicios de sesión) de un servidor de Azure AD adicionales mediante SSMS

Cuando se ha creado la entidad de seguridad (inicio de sesión) de un servidor de Azure AD y se le han dado los privilegios `sysadmin`, dicho inicio de sesión puede crear otros inicios mediante la cláusula **FROM EXTERNAL PROVIDER** con **CREATE LOGIN**.

1. Conéctese a la instancia administrada con la entidad de seguridad (inicio de sesión) de un servidor de Azure AD mediante SQL Server Management Studio. Escriba el nombre de host de la instancia administrada. Para la autenticación en SSMS, hay tres opciones para elegir al iniciar sesión con una cuenta de AD de Azure:

   - Active Directory - Universal compatible con MFA
   - Active Directory - Contraseña
   - Active Directory - Integrado </br>

     ![ssms-login-prompt.png](media/sql-database-managed-instance-security-tutorial/ssms-login-prompt.png)

     Para más información, consulte el artículo siguiente: [Autenticación universal con SQL Database y SQL Data Warehouse (compatibilidad de SSMS con MFA)](sql-database-ssms-mfa-authentication.md)

1. Seleccione **Active Directory - Universal compatible con MFA**. Esto abre una ventana de inicio de sesión de Multi-Factor Authentication (MFA). Inicie sesión con la contraseña de Azure AD.

    ![mfa-login-prompt.png](media/sql-database-managed-instance-security-tutorial/mfa-login-prompt.png)

1. En el **Explorador de objetos** de SSMS, haga clic con el botón derecho en el servidor datos y elija **Nueva consulta**.
1. En la ventana de consulta, utilice la siguiente sintaxis para crear un inicio de sesión para otra cuenta de Azure AD:

    ```sql
    USE master
    GO
    CREATE LOGIN login_name FROM EXTERNAL PROVIDER
    GO
    ```

    En este ejemplo se crea un inicio de sesión para el usuario de Azure AD bob@aadsqlmi.net, cuyo dominio aadsqlmi.net está federado con el dominio aadsqlmi.onmicrosoft.com de Azure AD.

    Ejecute el siguiente comando T-SQL. Las cuentas de Azure AD federadas son los reemplazos de instancia administrada para usuarios e inicios de sesión de Windows locales.

    ```sql
    USE master
    GO
    CREATE LOGIN [bob@aadsqlmi.net] FROM EXTERNAL PROVIDER
    GO
    ```

1. Cree una base de datos en la instancia administrada con la sintaxis [CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-mi-current). Esta base de datos se usará para probar los inicios de sesión de usuario en la sección siguiente.
    1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.
    1. En la ventana de consulta, utilice la siguiente sintaxis para crear una base de datos denominada **MyMITestDB**.

        ```sql
        CREATE DATABASE MyMITestDB;
        GO
        ```

1. Cree un inicio de sesión de instancia administrada para un grupo de Azure AD. El grupo deberá existir en Azure AD antes de poder agregar el inicio de sesión a la instancia administrada. Consulte [Creación de un grupo básico e incorporación de miembros con Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Cree un grupo _mygroup_ y agregue miembros a este grupo.

1. Abra una nueva ventana de consulta en SQL Server Management Studio.

    En este ejemplo se asume que existe un grupo denominado _mygroup_ en Azure AD. Ejecute el comando siguiente:

    ```sql
    USE master
    GO
    CREATE LOGIN [mygroup] FROM EXTERNAL PROVIDER
    GO
    ```

1. Como prueba, inicie sesión en la instancia administrada con el inicio de sesión o grupo que se acaban de crear. Abra una nueva conexión a la instancia administrada y use el nuevo inicio de sesión al autenticarse.
1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta** para la nueva conexión.
1. Compruebe los permisos del servidor para la entidad de seguridad (inicio de sesión) de un servidor de Azure AD recién creada, para lo que debe ejecutar el siguiente comando:

      ```sql
      SELECT * FROM sys.fn_my_permissions (NULL, 'DATABASE')
      GO
      ```

> [!NOTE]
> Se admiten usuarios invitados de Azure AD en los inicios de sesión de instancia administrada, solo cuando se agregan como parte de un grupo de Azure AD. Un usuario invitado de Azure AD es una cuenta que se ha invitado a la instancia de Azure AD a la que pertenece la instancia administrada, desde otra instancia de Azure AD. Por ejemplo, joe@contoso.com (cuenta de Azure AD) o steve@outlook.com (cuenta MSA) pueden agregarse a un grupo en el dominio aadsqlmi de Azure AD. Una vez que los usuarios se agregan a un grupo, se puede crear un inicio de sesión en la base de datos **maestra** de la instancia administrada para el grupo mediante la sintaxis **CREATE LOGIN**. Los usuarios invitados que son miembros de este grupo pueden conectarse a la instancia administrada con sus inicios de sesión actuales (por ejemplo, joe@contoso.com o steve@outlook.com).

## <a name="create-an-azure-ad-user-from-the-azure-ad-server-principal-login-and-give-permissions"></a>Creación de un usuario de Azure AD desde la entidad de seguridad (inicio de sesión) de un servidor de Azure AD y concesión de permisos

La autorización en bases de datos individuales funciona de forma muy parecida en la instancia administrada que con SQL Server local. Se puede crear un usuario a partir de un inicio de sesión existente en una base de datos y se le pueden proporcionar permisos sobre esa base de datos, o agregarse a un rol de base de datos.

Ahora que hemos creado una base de datos llamada **MyMITestDB** y un inicio de sesión que solo tiene permisos predeterminados, el siguiente paso es crear un usuario a partir de ese inicio de sesión. Por el momento, el inicio de sesión puede conectarse a la instancia administrada y ver todas las bases de datos, pero no puede interactuar con las bases de datos. Si inicia sesión con la cuenta de Azure AD que tiene los permisos predeterminados e intenta expandir la base de datos recientemente creada, verá el siguiente error:

![ssms-db-not-accessible.png](media/sql-database-managed-instance-security-tutorial/ssms-db-not-accessible.png)

Para más información sobre la concesión de permisos de base de datos, consulte [Introducción a los permisos de los motores de bases de datos](/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions).

### <a name="create-an-azure-ad-user-and-create-a-sample-table"></a>Creación de un usuario de Azure AD y creación de una tabla de ejemplo

1. Inicie sesión en la instancia administrada con una cuenta `sysadmin` mediante SQL Server Management Studio.
1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.
1. En la ventana de consulta, utilice la siguiente sintaxis para crear un usuario de Azure AD a partir de una entidad de seguridad (inicio de sesión) de un servidor de Azure AD:

    ```sql
    USE <Database Name> -- provide your database name
    GO
    CREATE USER user_name FROM LOGIN login_name
    GO
    ```

    En el ejemplo siguiente se crea un usuario bob@aadsqlmi.net en el inicio de sesión bob@aadsqlmi.net:.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [bob@aadsqlmi.net] FROM LOGIN [bob@aadsqlmi.net]
    GO
    ```

1. También se admite la creación de un usuario de Azure AD a partir de una entidad de seguridad (inicio de sesión) de un servidor de Azure AD que sea un grupo.

    En el ejemplo siguiente se crea un inicio de sesión para el grupo _mygroup_ de Azure AD que existe en la instancia de Azure AD.

    ```sql
    USE MyMITestDB
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    ```

    Todos los usuarios que pertenecen a **mygroup** pueden acceder a la base de datos **MyMITestDB**.

    > [!IMPORTANT]
    > Cuando cree un usuario en **USER** a partir de una entidad de seguridad (inicio de sesión) de un servidor de Azure AD, especifique valor de user_name igual que el valor de login_name que se obtiene de **LOGIN**.

    Para más información, consulte [CREATE USER](/sql/t-sql/statements/create-user-transact-sql?view=azuresqldb-mi-current).

1. En una nueva ventana de consulta, cree una tabla de prueba con el siguiente comando T-SQL:

    ```sql
    USE MyMITestDB
    GO
    CREATE TABLE TestTable
    (
    AccountNum varchar(10),
    City varchar(255),
    Name varchar(255),
    State varchar(2)
    );
    ```

1. Cree una conexión en SSMS con el usuario que se ha creado. Observará que no se puede ver la tabla **TestTable** que se ha creado por el rol `sysadmin` anterior. Hay que proporcionar al usuario los permisos para leer los datos de la base de datos.

1. Puede comprobar el permiso actual del usuario mediante la ejecución del siguiente comando:

    ```sql
    SELECT * FROM sys.fn_my_permissions('MyMITestDB','DATABASE')
    GO
    ```

### <a name="add-users-to-database-level-roles"></a>Adición de usuarios a roles de nivel de base de datos

Para que el usuario vea los datos en la base de datos, hay que proporcionar [roles de nivel de base de datos](/sql/relational-databases/security/authentication-access/database-level-roles) al usuario.

1. Inicie sesión en la instancia administrada con una cuenta `sysadmin` mediante SQL Server Management Studio.

1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.

1. Conceda al usuario de Azure AD el rol de base de datos `db_datareader` mediante la siguiente sintaxis T-SQL:

    ```sql
    Use <Database Name> -- provide your database name
    ALTER ROLE db_datareader ADD MEMBER user_name
    GO
    ```

    En el ejemplo siguiente se proporciona al usuario bob@aadsqlmi.net y al grupo _mygroup_ permisos `db_datareader` en la base de datos **MyMITestDB**:

    ```sql
    USE MyMITestDB
    GO
    ALTER ROLE db_datareader ADD MEMBER [bob@aadsqlmi.net]
    GO
    ALTER ROLE db_datareader ADD MEMBER [mygroup]
    GO
    ```

1. Compruebe que el usuario de Azure AD que se creó en la base de datos existe mediante la ejecución del siguiente comando:

    ```sql
    SELECT * FROM sys.database_principals
    GO
    ```

1. Cree una nueva conexión a la instancia administrada con el usuario al que se le ha agregado el rol `db_datareader`.
1. Expanda la base de datos en el **Explorador de objetos** para ver la tabla.

    ![ssms-test-table.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table.png)

1. Abra una nueva ventana de consulta y ejecute la siguiente instrucción SELECT:

    ```sql
    SELECT *
    FROM TestTable
    ```

    ¿Puede ver los datos de la tabla? Debería ver las columnas que se devuelven.

    ![ssms-test-table-query.png](media/sql-database-managed-instance-security-tutorial/ssms-test-table-query.png)

## <a name="impersonating-azure-ad-server-level-principals-logins"></a>Suplantación de entidades de seguridad de nivel de servidor de Azure AD (inicios de sesión)

La instancia administrada admite la suplantación de las entidades de seguridad a nivel de servidor de Azure AD (inicios de sesión).

### <a name="test-impersonation"></a>Prueba de suplantación

1. Inicie sesión en la instancia administrada con una cuenta `sysadmin` mediante SQL Server Management Studio.

1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.

1. En la ventana de consulta, utilice el siguiente comando para crear un procedimiento almacenado:

    ```sql
    USE MyMITestDB
    GO  
    CREATE PROCEDURE dbo.usp_Demo  
    WITH EXECUTE AS 'bob@aadsqlmi.net'  
    AS  
    SELECT user_name();  
    GO
    ```

1. Utilice el siguiente comando para ver que el usuario que está suplantando al ejecutar el procedimiento almacenado es **bob\@aadsqlmi.net**.

    ```sql
    Exec dbo.usp_Demo
    ```

1. Pruebe la suplantación con la instrucción EXECUTE AS LOGIN:

    ```sql
    EXECUTE AS LOGIN = 'bob@aadsqlmi.net'
    GO
    SELECT SUSER_SNAME()
    REVERT
    GO
    ```

> [!NOTE]
> Solo las entidades de seguridad (inicios de sesión) a nivel de servidor SQL que forman parte del rol `sysadmin` pueden ejecutar las siguientes operaciones dirigidas a las entidades de seguridad de Azure AD: 
> - EXECUTE AS USER
> - EXECUTE AS LOGIN

## <a name="using-cross-database-queries-in-managed-instances"></a>Uso de consultas entre bases de datos en instancias administradas

Las consultas entre bases de datos se admiten en las cuentas de Azure AD con entidades de seguridad (inicios de sesión) de un servidor de Azure AD. Para probar una consulta entre bases de datos con un grupo de Azure AD, hay que crear otra base de datos y una tabla. Puede omitir la creación de otra base de datos y tabla si ya existe una.

1. Inicie sesión en la instancia administrada con una cuenta `sysadmin` mediante SQL Server Management Studio.
1. En el **Explorador de objetos**, haga clic con el botón derecho en el servidor y elija **Nueva consulta**.
1. En la ventana de consulta, utilice el siguiente comando para crear una base de datos denominada **MyMITestDB2** y una tabla llamada **TestTable2**:

    ```sql
    CREATE DATABASE MyMITestDB2;
    GO
    USE MyMITestDB2
    GO
    CREATE TABLE TestTable2
    (
    EmpId varchar(10),
    FirstName varchar(255),
    LastName varchar(255),
    Status varchar(10)
    );
    ```

1. En una nueva ventana de consulta, ejecute el siguiente comando para crear el usuario _mygroup_ en la nueva base de datos **MyMITestDB2** y conceda los permisos SELECT en esa base de datos a _mygroup_:

    ```sql
    USE MyMITestDB2
    GO
    CREATE USER [mygroup] FROM LOGIN [mygroup]
    GO
    GRANT SELECT TO [mygroup]
    GO
    ```

1. Inicie sesión en la instancia administrada mediante SQL Server Management Studio como miembro del grupo _mygroup_ de Azure AD. Abra una nueva ventana de consulta y ejecute la instrucción SELECT entre las bases de datos:

    ```sql
    USE MyMITestDB
    SELECT * FROM MyMITestDB2..TestTable2
    GO
    ```

    Debería ver los resultados de la tabla en **TestTable2**.

## <a name="additional-scenarios-supported-for-azure-ad-server-principals-logins-public-preview"></a>Escenarios adicionales que se admiten para las entidades de seguridad (inicios de sesión) de un servidor de Azure AD (versión preliminar pública) 

- Se admiten la administración del agente SQL y las ejecuciones de los trabajos en las entidades de seguridad (inicios de sesión) de un servidor de Azure AD.
- Las entidades de seguridad (inicios de sesión) de un servidor de Azure AD pueden ejecutar operaciones de copia de seguridad y restauración de bases de datos.
- [Auditoría](sql-database-managed-instance-auditing.md) de todas las instrucciones relacionadas con las entidades de seguridad (inicios de sesión) de un servidor de Azure AD y eventos de autenticación.
- Conexión de administrador dedicada para las entidades de seguridad (inicios de sesión) de un servidor de Azure AD que son miembros del rol del servidor `sysadmin`.
- Se admiten las entidades de seguridad (inicios de sesión) de un servidor de Azure AD con el uso de la [utilidad sqlcmd](/sql/tools/sqlcmd-utility) y la herramienta [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
- Se admiten desencadenadores de inicio de sesión para los eventos de inicio de sesión procedentes de las entidades de seguridad (inicios de sesión) de un servidor de Azure AD.
- Se puede configurar Service Broker y el correo electrónico de la base de datos mediante las entidades de seguridad (inicios de sesión) de un servidor de Azure AD.


## <a name="next-steps"></a>Pasos siguientes

### <a name="enable-security-features"></a>Habilitar características de seguridad

Consulte el siguiente artículo sobre las [características de seguridad de las funcionalidades de la instancia administrada](sql-database-managed-instance.md#azure-sql-database-security-features) para obtener una lista completa de formas de proteger la base de datos. Se abordan las características de seguridad siguientes:

- [Auditoría de Instancia administrada](sql-database-managed-instance-auditing.md) 
- [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine)
- [Detección de amenazas](sql-database-managed-instance-threat-detection.md) 
- [Enmascaramiento de datos dinámicos](/sql/relational-databases/security/dynamic-data-masking)
- [Seguridad de nivel de fila](/sql/relational-databases/security/row-level-security) 
- [Cifrado de datos transparente (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql)

### <a name="managed-instance-capabilities"></a>Funcionalidades de instancia administrada

Para obtener una visión general completa de las funcionalidades de una instancia administrada, consulte:

> [!div class="nextstepaction"]
> [Funcionalidades de Instancia administrada](sql-database-managed-instance.md)
