---
title: 'Configuración de la autenticación de Azure Active Directory: SQL | Microsoft Docs'
description: Aprenda a conectarse a SQL Database, Instancia administrada y SQL Data Warehouse mediante Autenticación de Azure Active Directory después de configurar Azure AD.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: data warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto, carlrab
ms.date: 03/12/2019
ms.openlocfilehash: b6414ac41b1bb43e3fe1470a7ae2b1358126003a
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68569684"
---
# <a name="configure-and-manage-azure-active-directory-authentication-with-sql"></a>Configuración y administración de la autenticación de Azure Active Directory con SQL

En este artículo se muestra cómo crear y rellenar una instancia de Azure AD y, a continuación, usarla con [Azure SQL Database](sql-database-technical-overview.md), [Instancia administrada](sql-database-managed-instance.md) y [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Para ver un resumen, consulte [Autenticación con Azure Active Directory](sql-database-aad-authentication.md).

> [!NOTE]
> Este artículo se aplica a Azure SQL Server y tanto a las bases de datos de SQL Database como a SQL Data Warehouse que se crean en el servidor de Azure SQL. Para simplificar, SQL Database se utiliza cuando se hace referencia tanto a SQL Database como a SQL Data Warehouse.
> [!IMPORTANT]  
> La conexión a una instancia de SQL Server que se ejecute en una máquina virtual de Azure no se admite si se usa una cuenta de Azure Active Directory. Utilice en su lugar una cuenta de Active Directory del dominio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager de PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos en el módulo Az y en los módulos AzureRm son esencialmente idénticos.

## <a name="create-and-populate-an-azure-ad"></a>Crear y rellenar una instancia de Azure AD.

Cree una instancia de Azure AD y rellénela con usuarios y grupos. Azure AD puede ser el dominio administrado de Azure AD inicial. Azure AD también puede ser una instancia local de Active Directory Domain Services que se federa con Azure AD.

Para obtener más información, consulte [Integrating your on-premises identities with Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) (Integración de sus identidades locales con Azure Active Directory), [Incorporación de su nombre de dominio personalizado a Azure Active Directory](../active-directory/active-directory-domains-add-azure-portal.md), [Microsoft Azure now supports federation with Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/) (Microsoft Azure admite ahora Windows Server Active Directory), [Administración de su directorio de Azure AD](../active-directory/fundamentals/active-directory-administer.md), [Administrar Azure AD mediante Windows PowerShell](/powershell/azure/overview) y [Hybrid Identity Required Ports and Protocols](../active-directory/hybrid/reference-connect-ports.md) (Puertos y protocolos requeridos para la identidad híbrida).

## <a name="associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Asociación o adición de una suscripción de Azure a Azure Active Directory

1. Asocie la suscripción de Azure a Azure Active Directory mediante la creación del directorio como un directorio de confianza para la suscripción de Azure que hospeda la base de datos. Para obtener información, vea [Asociación de las suscripciones de Azure con Azure AD](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
2. Use el modificador del directorio en Azure Portal para cambiar a la suscripción asociada con el dominio.

   **Información adicional**: Cada suscripción de Azure tiene una relación de confianza con una instancia de Azure AD. Esto significa que confía en ese directorio para autenticar usuarios, servicios y dispositivos. Varias suscripciones pueden confiar en el mismo directorio, pero una suscripción confía solo en un único directorio. Esta relación de confianza que tiene una suscripción con un directorio es diferente de la relación que tiene una suscripción con todos los demás recursos de Azure (sitios web, bases de datos etc.), que son más parecidos a los recursos secundarios de una suscripción. Si una suscripción expira, el acceso a esos otros recursos asociados a la suscripción también se detiene. Sin embargo, el directorio permanece en Azure y puede asociar otra suscripción a ese directorio y continuar con la administración de los usuarios del directorio. Para más información sobre recursos, consulte [Descripción de acceso a los recursos de Azure](../active-directory/active-directory-b2b-admin-add-users.md). Para más información sobre esta relación de confianza, vea [Asociación o adición de una suscripción de Azure a Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="create-an-azure-ad-administrator-for-azure-sql-server"></a>Creación de un administrador de Azure AD para Azure SQL Server

Cada servidor de Azure SQL Server (que hospeda una instancia de SQL Database o SQL Data Warehouse) se inicia con una única cuenta de administrador, que es el administrador de todo el servidor de Azure SQL Server. Se debe crear un segundo administrador de SQL Server que sea una cuenta de Azure AD. Esta entidad de seguridad se crea como un usuario de base de datos independiente en la base de datos maestra. Como administradores, las cuentas de administrador del servidor son miembros del rol **db_owner** en todas las bases de datos de usuarios y se especifican en cada base de datos de usuarios como el usuario **dbo**. Para más información sobre las cuentas de administrador de servidor, consulte [Administración de bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md).

Cuando se usa Azure Active Directory con replicación geográfica, el administrador de Azure Active Directory debe configurarse para los servidores principal y secundarios. Si un servidor no tiene un administrador de Azure Active Directory, los usuarios y los inicios de sesión de Azure Active Directory reciben un error "No se puede conectar con el servidor".

> [!NOTE]
> Los usuarios que no se basen en una cuenta de Azure AD (incluida la cuenta de administrador del servidor Azure SQL Server) no pueden crear usuarios basados en Azure AD porque no tienen permisos para validar los usuarios de la base de datos propuesta con Azure AD.

## <a name="provision-an-azure-active-directory-administrator-for-your-managed-instance"></a>Aprovisionamiento de un administrador de Azure Active Directory para su instancia administrada

> [!IMPORTANT]
> Siga estos pasos únicamente si va a aprovisionar una instancia administrada. Esta operación solo la puede ejecutar el administrador global o de la compañía en Azure AD. Los pasos siguientes describen el proceso de concesión de permisos a los usuarios con privilegios diferentes en el directorio.

La instancia administrada necesita permisos para leer Azure AD para realizar correctamente las tareas, como la autenticación de los usuarios a través de la pertenencia a grupos de seguridad o la creación de nuevos usuarios. Para que esto funcione, debe conceder permisos a la instancia administrada para leer Azure AD. Hay dos formas de hacerlo: desde el portal y desde PowerShell. Los siguientes pasos describen ambos métodos.

1. En Azure Portal, en la esquina superior derecha, seleccione la conexión para desplegar una lista de posibles instancias de Active Directory.
2. Elija el Active Directory correcto como el valor predeterminado de Azure AD.

   En este paso se vincula la suscripción asociada con Active Directory con Instancia administrada, asegurándose de que la misma suscripción se usa tanto para Azure AD como para Instancia administrada.
3. Navegue a la Instancia administrada y seleccione una que desee usar para la integración de Azure AD.

   ![aad](./media/sql-database-aad-authentication/aad.png)

4. Seleccione el banner encima de la página de administrador de Active Directory y conceda permiso al usuario actual. Si inició sesión como administrador global o de la compañía en Azure AD, puede hacerlo desde Azure Portal o mediante PowerShell con el script siguiente.

    ![grant permissions-portal](./media/sql-database-aad-authentication/grant-permissions.png)

    ```powershell
    # Gives Azure Active Directory read permission to a Service Principal representing the Managed Instance.
    # Can be executed only by a "Company Administrator" or "Global Administrator" type of user.

    $aadTenant = "<YourTenantId>" # Enter your tenant ID
    $managedInstanceName = "MyManagedInstance"

    # Get Azure AD role "Directory Users" and create if it doesn't exist
    $roleName = "Directory Readers"
    $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    if ($role -eq $null) {
        # Instantiate an instance of the role template
        $roleTemplate = Get-AzureADDirectoryRoleTemplate | Where-Object {$_.displayName -eq $roleName}
        Enable-AzureADDirectoryRole -RoleTemplateId $roleTemplate.ObjectId
        $role = Get-AzureADDirectoryRole | Where-Object {$_.displayName -eq $roleName}
    }

    # Get service principal for managed instance
    $roleMember = Get-AzureADServicePrincipal -SearchString $managedInstanceName
    $roleMember.Count
    if ($roleMember -eq $null)
    {
        Write-Output "Error: No Service Principals with name '$    ($managedInstanceName)', make sure that managedInstanceName parameter was     entered correctly."
        exit
    }
    if (-not ($roleMember.Count -eq 1))
    {
        Write-Output "Error: More than one service principal with name pattern '$    ($managedInstanceName)'"
        Write-Output "Dumping selected service principals...."
        $roleMember
        exit
    }

    # Check if service principal is already member of readers role
    $allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
    $selDirReader = $allDirReaders | where{$_.ObjectId -match     $roleMember.ObjectId}

    if ($selDirReader -eq $null)
    {
        # Add principal to readers role
        Write-Output "Adding service principal '$($managedInstanceName)' to     'Directory Readers' role'..."
        Add-AzureADDirectoryRoleMember -ObjectId $role.ObjectId -RefObjectId     $roleMember.ObjectId
        Write-Output "'$($managedInstanceName)' service principal added to     'Directory Readers' role'..."

        #Write-Output "Dumping service principal '$($managedInstanceName)':"
        #$allDirReaders = Get-AzureADDirectoryRoleMember -ObjectId $role.ObjectId
        #$allDirReaders | where{$_.ObjectId -match $roleMember.ObjectId}
    }
    else
    {
        Write-Output "Service principal '$($managedInstanceName)' is already     member of 'Directory Readers' role'."
    }
    ```

5. Después de que la operación se complete correctamente, la siguiente notificación se mostrará en la esquina superior derecha:

    ![success](./media/sql-database-aad-authentication/success.png)

6. Ahora puede elegir el administrador de Azure AD para su instancia administrada. Para ello, en la página Administrador de Active Directory, seleccione el comando **Establecer administrador**.

    ![set-admin](./media/sql-database-aad-authentication/set-admin.png)

7. En la página de administrador de AAD, busque un usuario, seleccione el usuario o grupo que convertirá en administrador y después haga clic en **Seleccionar**.

   En la hoja Administrador de Active Directory se muestran todos los miembros y grupos de Active Directory. No se pueden seleccionar los usuarios o grupos que aparecen atenuados porque no se admiten como administradores de Azure AD. Consulte la lista de administradores admitidos en la sección [Características y limitaciones de Azure AD](sql-database-aad-authentication.md#azure-ad-features-and-limitations). El control de acceso basado en rol (RBAC) se aplica solo a Azure Portal y no se propaga a SQL Server.

    ![add-admin](./media/sql-database-aad-authentication/add-admin.png)

8. En la parte superior de la página Administrador de Active Directory, seleccione **Guardar**.

    ![Guardar](./media/sql-database-aad-authentication/save.png)

    El proceso de cambio del el administrador puede tardar varios minutos. El nuevo administrador aparecerá después en el cuadro Administrador de Active Directory.

Después de aprovisionar un administrador de Azure AD para Instancia administrada, puede empezar a crear entidades de seguridad del servidor de Azure AD (**versión preliminar pública**) con la sintaxis <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>. Para obtener más información, consulte la [introducción sobre la Instancia administrada](sql-database-managed-instance.md#azure-active-directory-integration).

> [!TIP]
> Si más adelante desea quitar un administrador, en la parte superior de la página Administrador de Active Directory, seleccione **Quitar administrador** y después **Guardar**.

## <a name="provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server"></a>Aprovisionamiento de un administrador de Azure Active Directory para el servidor de Azure SQL Database

> [!IMPORTANT]
> Simplemente siga estos pasos si va a aprovisionar un servidor de Azure SQL Database o Data Warehouse.

Los dos procedimientos siguientes muestran cómo aprovisionar un administrador de Azure Active Directory para el servidor de Azure SQL Server en Azure Portal y mediante el uso de PowerShell.

### <a name="azure-portal"></a>Portal de Azure

1. En [Azure Portal](https://portal.azure.com/), en la esquina superior derecha, seleccione la conexión para desplegar una lista de posibles instancias de Active Directory. Elija el Active Directory correcto como el valor predeterminado de Azure AD. En este paso se vincula la asociación de la suscripción de Active Directory con Azure SQL Server, de modo que se asegura de que la misma suscripción se use tanto para Azure AD como para SQL Server. (El servidor de Azure SQL se puede hospedar en Azure SQL Database o en Azure SQL Data Warehouse). ![choose-ad][8]

2. En el banner de la izquierda, seleccione **Todos los servicios** y, en el filtro, escriba **SQL Server**. Seleccione **Servidores SQL Server**.

    ![sqlservers.png](media/sql-database-aad-authentication/sqlservers.png)

    >[!NOTE]
    > En esta página, antes de seleccionar **Servidores SQL Server**, puede seleccionar la **estrella** junto al nombre para incluir la categoría en *favoritos* y agregar **Servidores SQL Server** a la barra de navegación izquierda.

3. En la página **SQL Server**, seleccione **Administrador de Active Directory**.
4. En la página **Administrador de Active Directory**, seleccione **Establecer administrador**.  ![seleccionar Active Directory](./media/sql-database-aad-authentication/select-active-directory.png)  

5. En la página **Agregar administrador**, busque un usuario, seleccione el usuario o grupo que convertirá en administrador y después haga clic en **Seleccionar**. En la hoja Administrador de Active Directory se muestran todos los miembros y grupos de Active Directory. No se pueden seleccionar los usuarios o grupos que aparecen atenuados porque no se admiten como administradores de Azure AD. (Consulte la lista de administradores admitidos en la sección **Características y limitaciones de Azure AD** de [Uso de la autenticación de Azure Active Directory con SQL Database o SQL Data Warehouse](sql-database-aad-authentication.md)). El control de acceso basado en rol (RBAC) se aplica solo al portal y no se propaga a SQL Server.
    ![Seleccionar administrador](./media/sql-database-aad-authentication/select-admin.png)  

6. En la parte superior de la página **Administrador de Active Directory**, seleccione **GUARDAR**.
    ![Guardar administrador](./media/sql-database-aad-authentication/save-admin.png)

El proceso de cambio del el administrador puede tardar varios minutos. El nuevo administrador aparece en el cuadro **Administrador de Active Directory** .

   > [!NOTE]
   > Al configurar el nuevo administrador de Azure AD, el nuevo nombre del administrador (usuario o grupo) no puede estar en la base de datos maestra virtual como usuario de autenticación de SQL Server. Si estuviera presente, se producirá un error en el programa de instalación del administrador de Azure AD y, por lo tanto, deberá revertir su creación e indicar que ese administrador (nombre) ya existe. Como dicho usuario de autenticación de SQL Server no forma parte de Azure AD, se producirá un error cada vez que intente conectarse al servidor mediante la autenticación de Azure AD.

Si más adelante desea quitar un administrador, en la parte superior de la página **Administrador de Active Directory**, seleccione **Quitar administrador** y después **Guardar**.

### <a name="powershell"></a>PowerShell

Para ejecutar los cmdlets de PowerShell, necesitará tener Azure PowerShell instalado y en marcha. Para obtener información detallada, vea [Instalación y configuración de Azure PowerShell](/powershell/azure/overview). Para aprovisionar un administrador de Azure AD, debe ejecutar los siguientes comandos de Azure PowerShell:

- Connect-AzAccount
- Select-AzSubscription

Cmdlets que se usan para aprovisionar y administrar administradores de Azure AD:

| Nombre del cmdlet | DESCRIPCIÓN |
| --- | --- |
| [Set-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/set-azsqlserveractivedirectoryadministrator) |Aprovisiona un administrador de Azure Active Directory para Azure SQL Server o para Azure SQL Data Warehouse. (Debe ser de la suscripción actual). |
| [Remove-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/remove-azsqlserveractivedirectoryadministrator) |Quita un administrador de Azure Active Directory para Azure SQL Server o para Azure SQL Data Warehouse. |
| [Get-AzSqlServerActiveDirectoryAdministrator](/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator) |Devuelve información sobre un administrador de Azure Active Directory configurado actualmente para el servidor de Azure SQL Server o para Azure SQL Data Warehouse. |

Use el comando de PowerShell get-help para ver más información de cada uno de estos comandos; por ejemplo, ``get-help Set-AzSqlServerActiveDirectoryAdministrator``.

El script siguiente aprovisiona un grupo de administradores de Azure AD denominado **DBA_Group** (id. de objeto `40b79501-b343-44ed-9ce7-da4c8cc7353f`) para el servidor **demo_server**, en un grupo de recursos llamado **Group-23**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group"
```

El parámetro de entrada **DisplayName** acepta tanto el nombre para mostrar de Azure AD, como el nombre principal de usuario. Por ejemplo, ``DisplayName="John Smith"`` y ``DisplayName="johns@contoso.com"``. En los grupos de Azure AD solo se admite el nombre para mostrar de Azure AD.

> [!NOTE]
> El comando ```Set-AzSqlServerActiveDirectoryAdministrator``` de Azure PowerShell no le impedirá aprovisionar administradores de Azure AD para usuarios no admitidos. Es posible aprovisionar un usuario no admitido, pero no podrá conectarse a una base de datos.

En el ejemplo siguiente se usa el elemento opcional **ObjectID**:

```powershell
Set-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23"
-ServerName "demo_server" -DisplayName "DBA_Group" -ObjectId "40b79501-b343-44ed-9ce7-da4c8cc7353f"
```

> [!NOTE]
> El elemento **ObjectID** de Azure AD es necesario cuando el valor **DisplayName** no es único. Para recuperar los valores **ObjectID** y **DisplayName**, use la sección Active Directory del Portal de Azure clásico y visualice las propiedades de un usuario o grupo.

En el ejemplo siguiente se devuelve información sobre el administrador actual de Azure AD para el servidor de Azure SQL Server:

```powershell
Get-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server" | Format-List
```

En el ejemplo siguiente se quita un administrador de Azure AD:

```powershell
Remove-AzSqlServerActiveDirectoryAdministrator -ResourceGroupName "Group-23" -ServerName "demo_server"
```

También puede aprovisionar un administrador de Azure Active Directory mediante las API de REST. Para más información, consulte [Service Management REST API Reference and Operations for Azure SQL Databases](https://docs.microsoft.com/rest/api/sql/) (Referencia de la API REST de administración de servicio y operaciones para bases de datos de Azure SQL).

### <a name="cli"></a>CLI  

También puede aprovisionar un administrador de Azure AD mediante una llamada a los siguientes comandos de la CLI:

| Get-Help | DESCRIPCIÓN |
| --- | --- |
|[az sql server ad-admin create](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-create) |Aprovisiona un administrador de Azure Active Directory para Azure SQL Server o para Azure SQL Data Warehouse. (Debe ser de la suscripción actual). |
|[az sql server ad-admin delete](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-delete) |Quita un administrador de Azure Active Directory para Azure SQL Server o para Azure SQL Data Warehouse. |
|[az sql server ad-admin list](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-list) |Devuelve información sobre un administrador de Azure Active Directory configurado actualmente para el servidor de Azure SQL Server o para Azure SQL Data Warehouse. |
|[az sql server ad-admin update](https://docs.microsoft.com/cli/azure/sql/server/ad-admin#az-sql-server-ad-admin-update) |Actualiza el administrador de Azure Active Directory para un servidor Azure SQL Server o para Azure SQL Data Warehouse. |

Para más información acerca de los comandos de la CLI, consulte [SQL - az sql](https://docs.microsoft.com/cli/azure/sql/server).  

## <a name="configure-your-client-computers"></a>Configurar los equipos cliente.

En todos los equipos cliente, desde el que las aplicaciones o los usuarios se conectan a Azure SQL Database o a SQL Data Warehouse mediante identidades de Azure AD, debe instalar el software siguiente:

- .NET Framework 4.6 o posterior desde [https://msdn.microsoft.com/library/5a4x27ek.aspx](https://msdn.microsoft.com/library/5a4x27ek.aspx).
- La Biblioteca de autenticación de Azure Active Directory para SQL Server (**ADALSQL.DLL**) está disponible en varios idiomas (x86 y amd64) en el centro de descarga en la sección [Biblioteca de autenticación de Microsoft Active Directory para Microsoft SQL Server](https://www.microsoft.com/download/details.aspx?id=48742).

Puede cumplir estos requisitos mediante:

- La instalación de [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) o de [SQL Server Data Tools para Visual Studio 2015](https://msdn.microsoft.com/library/mt204009.aspx) cumple los requisitos de .NET Framework 4.6.
- SSMS instala la versión x86 de **ADALSQL.DLL**.
- SSMS instala la versión amd64 de **ADALSQL.DLL**.
- La versión más reciente de Visual Studio de la sección [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) cumple con los requisitos de .NET Framework 4.6, pero no instala la versión amd64 necesaria de **ADALSQL.DLL**.

## <a name="create-contained-database-users-in-your-database-mapped-to-azure-ad-identities"></a>Crear usuarios de base de datos independiente  en la base de datos y asignados a identidades de Azure AD.

>[!IMPORTANT]
>Instancia administrada ahora admite entidades de seguridad del servidor de Azure AD (inicios de sesión) (**versión preliminar pública**), lo que le permite crear inicios de sesión de usuarios, grupos o aplicaciones de Azure AD. Las inicios de sesión entidades de seguridad del servidor de Azure AD ofrecen la posibilidad de autenticarse en la Instancia administrada sin requerir la creación de usuarios de base de datos como un usuario de base de datos independiente. Para obtener más información, consulte la [introducción sobre la Instancia administrada](sql-database-managed-instance.md#azure-active-directory-integration). Para conocer la sintaxis de creación de entidades de seguridad (inicios de sesión) de un servidor de Azure AD, consulte <a href="/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">CREATE LOGIN</a>.

La autenticación de Azure Active Directory requiere que los usuarios de la base de datos se creen como usuarios de bases de datos independientes. Un usuario de base de datos independiente basado en una identidad de Azure AD es un usuario de base de datos que no tiene un inicio de sesión en la base de datos maestra y que se asigna a una identidad en el directorio de Azure AD que está asociado a la base de datos. La identidad de Azure AD puede ser una cuenta de usuario individual o un grupo. Para más información sobre los usuarios de bases de datos independientes, vea [Usuarios de bases de datos independientes: cómo hacer que la base de datos sea portátil](https://msdn.microsoft.com/library/ff929188.aspx).

> [!NOTE]
> Los usuarios de base de datos (a excepción de los administradores) no se pueden crear mediante Azure Portal. Los roles de RBAC no se propagan a SQL Data Warehouse, SQL Database o SQL Server. Los roles de RBAC de Azure se utilizan para administrar los recursos de Azure y no se aplican a los permisos de base de datos. Por ejemplo, el rol **Colaborador de SQL Server** no concede acceso para conectarse a SQL Database o SQL Data Warehouse. El permiso de acceso tiene que concederse directamente en la base de datos mediante instrucciones de Transact-SQL.
> [!WARNING]
> No se admiten los caracteres especiales como dos puntos `:` o la "Y" comercial `&` cuando se incluyen como nombres de usuario en las sentencias CREATE LOGIN y CREATE USER de T-SQL.

Para crear un usuario de base de datos independiente basada en Azure AD (que no sea el administrador del servidor que es el propietario de la base de datos), conéctese a la base de datos con una identidad de Azure AD, como un usuario con al menos el permiso **ALTER ANY USER** . Después, use la sintaxis de Transact-SQL siguiente:

```sql
CREATE USER <Azure_AD_principal_name> FROM EXTERNAL PROVIDER;
```

*Azure_AD_principal_name* puede ser el nombre principal de usuario de un usuario de Azure AD, o el nombre para mostrar de un grupo de Azure AD.

**Ejemplos:** para crear un usuario de base de datos independiente que represente un usuario de dominio administrado o federado de Azure AD:

```sql
CREATE USER [bob@contoso.com] FROM EXTERNAL PROVIDER;
CREATE USER [alice@fabrikam.onmicrosoft.com] FROM EXTERNAL PROVIDER;
```

Para crear un usuario de base de datos contenido que represente un grupo de dominio federado o de Azure AD, proporcione el nombre para mostrar de un grupo de seguridad:

```sql
CREATE USER [ICU Nurses] FROM EXTERNAL PROVIDER;
```

Para crear un usuario de base de datos independiente que represente a una aplicación que se conecta mediante un token de Azure AD:

```sql
CREATE USER [appName] FROM EXTERNAL PROVIDER;
```

> [!TIP]
> No puede crear directamente un usuario a partir de una instancia de Azure Active Directory distinta a la que esté asociada a su suscripción de Azure. Sin embargo, se pueden agregar miembros de otras instancias de AD que sean usuarios importados en el Active Directory asociado (que se conocen como "usuarios externos") a un grupo de Active Directory del AD inquilino. Al crear un usuario de la base de datos independiente para ese grupo de AD, los usuarios del Active Directory externo pueden obtener acceso a SQL Database.

Para más información sobre la creación de usuarios de bases de datos independientes basados en identidades de Azure Active Directory, vea [CREAR USUARIO (Transact-SQL)](https://msdn.microsoft.com/library/ms173463.aspx).

> [!NOTE]
> Si se quita el administrador de Azure Active Directory de Azure SQL Server, se impide que cualquier usuario de autenticación de Azure AD pueda conectarse al servidor. Si es necesario, un administrador de SQL Database puede quitar manualmente a usuarios de Azure AD no utilizados.
> [!NOTE]
> Si recibe la notificación **Tiempo de espera de conexión agotado**, es posible que deba establecer el parámetro `TransparentNetworkIPResolution` de la cadena de conexión en False. Para más información, consulte [Connection timeout issue with .NET Framework 4.6.1 – TransparentNetworkIPResolution](https://blogs.msdn.microsoft.com/dataaccesstechnologies/20../../connection-timeout-issue-with-net-framework-4-6-1-transparentnetworkipresolution/) (Problema de tiempo de espera de conexión agotado con .NET Framework 4.6.1: TransparentNetworkIPResolution).

Cuando se crea un usuario de base de datos, dicho usuario recibe el permiso **CONNECT** y puede conectarse a esa base de datos como un miembro con el rol **PUBLIC**. En un principio, los únicos permisos disponibles para el usuario son los permisos que se conceden al rol **PUBLIC** o cualquier otro permiso que se conceda a los grupos de Azure AD de los que sea miembro. Cuando se aprovisiona un usuario de base de datos de independiente basada en AD Azure, se pueden conceder permisos adicionales al usuario, del mismo modo que se conceden permisos a cualquier otro tipo de usuario. Normalmente, se conceden permisos a roles de base de datos y después se agregan usuarios a los roles. Para obtener más información, consulte [Conceptos básicos de los permisos de los motores de las bases de datos](https://social.technet.microsoft.com/wiki/contents/articles/4433.database-engine-permission-basics.aspx). Para obtener más información sobre los roles especiales de SQL Database, consulte [Administración de bases de datos e inicios de sesión en Azure SQL Database](sql-database-manage-logins.md).
Una cuenta de usuario de dominio federado que se importa en un dominio administrado como un usuario externo debe usar la identidad del dominio administrado.

> [!NOTE]
> Los usuarios de Azure AD se marcan en los metadatos de la base de datos con el tipo E (EXTERNAL_USER) y en los grupos con el tipo X (EXTERNAL_GROUPS). Para obtener más información, consulte [sys.database_principals (Transact-SQL)](https://msdn.microsoft.com/library/ms187328.aspx).
>

## <a name="connect-to-the-user-database-or-data-warehouse-by-using-ssms-or-ssdt"></a>Conexión con la base de datos de usuario o el almacenamiento de datos mediante SQL Server Management Studio o SQL Server Data Tools  

Para confirmar que el administrador de Azure AD está correctamente configurado, conéctese a la base de datos **maestra** con la cuenta de administrador de Azure AD.
Para aprovisionar un usuario de base de datos independiente basada en un directorio de Azure AD (que no sea el administrador del servidor que es el propietario de la base de datos), conéctese a la base de datos con una identidad de Azure AD que tenga acceso a la base de datos.

> [!IMPORTANT]
> La autenticación de Azure Active Directory es compatible con [SQL Server 2016 Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) y [SQL Server Data Tools](https://msdn.microsoft.com/library/mt204009.aspx) en Visual Studio 2015. La versión de agosto de 2016 de SSMS también incluye compatibilidad con la autenticación universal de Active Directory, que permite a los administradores requerir Multi-Factor Authentication con una llamada de teléfono, un mensaje de texto, tarjetas inteligentes con PIN o una notificación de aplicación móvil.

## <a name="using-an-azure-ad-identity-to-connect-using-ssms-or-ssdt"></a>Uso de una identidad de Azure AD para conectarse mediante SQL Server Management Studio o SQL Server Database Tools

Los procedimientos siguientes muestran cómo conectarse a una instancia de SQL Database con una identidad de Azure AD mediante SQL Server Management Studio o SQL Server Database Tools.

### <a name="active-directory-integrated-authentication"></a>Autenticación integrada de Active Directory

Use este método si tiene la sesión iniciada en Windows con sus credenciales de Azure Active Directory desde un dominio federado.

1. Inicie Management Studio o Data Tools y, en el cuadro de diálogo **Conectar con el servidor** (o **Conectarse al motor de base de datos**), en el cuadro **Autenticación**, seleccione **Integrada de Active Directory**. No se necesita ni se puede especificar una contraseña para la conexión, ya que aparecen las credenciales existentes.

    ![Selección de autenticación integrada de Active Directory][11]
2. Seleccione el botón **Opciones** y, en la página **Propiedades de conexión**, en el cuadro **Conectar con base de datos**, escriba el nombre de la base de datos de usuarios a la que quiere conectarse. (La opción **Nombre de dominio o ID de inquilino de AD** solo se admite para las opciones **Universal con conexión MFA**, en caso contrario se atenúa).  

    ![Selección del nombre de la base de datos][13]

## <a name="active-directory-password-authentication"></a>Autenticación de contraseña de Active Directory

Use este método al conectarse con un nombre de entidad de seguridad de Azure AD mediante el dominio administrado de Azure AD. También puede utilizarlo para la cuenta federada sin acceso al dominio, por ejemplo, cuando se trabaja de forma remota.

Utilice este método para autenticarse en SQL DB/DW con Azure AD para los usuarios de Azure AD federados o nativos. Un usuario nativo es uno creado explícitamente en Azure AD y que se autentica mediante un nombre de usuario y una contraseña, mientras que un usuario federado es un usuario de Windows cuyo dominio está federado con Azure AD. El último método (usuario y contraseña) puede usarse cuando un usuario desea usar sus credenciales de Windows, pero su máquina local no está unida al dominio (es decir, mediante un acceso remoto). En este caso, un usuario de Windows puede indicar su cuenta de dominio y contraseña, y autenticarse en SQL DB/DW mediante credenciales federadas.

1. Inicie Management Studio o Data Tools y, en el cuadro de diálogo **Conectar con el servidor** (o **Conectarse al motor de base de datos**), en el cuadro **Autenticación**, seleccione **Contraseña de Active Directory**.
2. En el cuadro **Nombre de usuario**, escriba el nombre de usuario de Azure Active Directory con el formato **nombreDeUsuario\@dominio.com**. Los nombres de usuario deben corresponderse con una cuenta de Azure Active Directory o una cuenta de un dominio federado con Azure Active Directory.
3. En el cuadro **Contraseña** , escriba la contraseña de usuario de la cuenta de Azure Active Directory o de la cuenta de dominio federado.

    ![Selección de autenticación de contraseña de Active Directory][12]
4. Seleccione el botón **Opciones** y, en la página **Propiedades de conexión**, en el cuadro **Conectar con base de datos**, escriba el nombre de la base de datos de usuarios a la que quiere conectarse. (Consulte el gráfico que aparece en la opción anterior).

## <a name="using-an-azure-ad-identity-to-connect-from-a-client-application"></a>Uso de una identidad de Azure AD para conectarse desde una aplicación cliente

Los procedimientos siguientes muestran cómo conectarse a una instancia de SQL Database con una identidad de Azure AD desde una aplicación cliente.

### <a name="active-directory-integrated-authentication"></a>Autenticación integrada de Active Directory

Para usar la autenticación integrada de Windows, la instancia de Active Directory del dominio se debe federar con Azure Active Directory. La aplicación cliente (o un servicio) que se conecte a la base de datos debe ejecutarse en un equipo unido a un dominio con las credenciales de dominio de un usuario.

Para conectarse a una base de datos mediante autenticación integrada y una identidad de Azure AD, la palabra clave Authentication de la cadena de conexión de la base de datos debe establecerse como Active Directory Integrated. En el siguiente ejemplo de código de C# se usa ADO.NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Integrated; Initial Catalog=testdb;";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

No se admite la palabra clave ``Integrated Security=True`` de la cadena de conexión para conectarse a Azure SQL Database. Al crear una conexión ODBC tendrá que quitar espacios y establecer la autenticación en 'ActiveDirectoryIntegrated'.

### <a name="active-directory-password-authentication"></a>Autenticación de contraseña de Active Directory

Para conectarse a una base de datos mediante autenticación integrada y una identidad de Azure AD, la palabra clave Authentication debe establecerse como Active Directory Password. La cadena de conexión debe contener las palabras clave y los valores User ID/UID y Password/PWD. En el siguiente ejemplo de código de C# se usa ADO.NET.

```csharp
string ConnectionString =
@"Data Source=n9lxnyuzhv.database.windows.net; Authentication=Active Directory Password; Initial Catalog=testdb;  UID=bob@contoso.onmicrosoft.com; PWD=MyPassWord!";
SqlConnection conn = new SqlConnection(ConnectionString);
conn.Open();
```

Puede encontrar más información sobre los métodos de autenticación de Azure AD con los ejemplos de código de demostración que se encuentran disponibles en la [demostración de la autenticación de Azure AD de GitHub](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/security/azure-active-directory-auth).

## <a name="azure-ad-token"></a>Token de Azure AD

Este método de autenticación permite a los servicios de nivel intermedio conectarse a Azure SQL Database o Azure SQL Data Warehouse mediante la obtención de un token de Azure Active Directory (AAD). Permite escenarios sofisticados, incluida la autenticación basada en certificados. Para usar la autenticación de token de Azure AD tiene que realizar cuatro pasos básicos:

1. Registrar la aplicación con Azure Active Directory y obtener el identificador de cliente para el código.
2. Crear un usuario de base de datos que represente a la aplicación. (Completado anteriormente en el paso 6).
3. Crear un certificado en el equipo cliente que va ejecuta la aplicación.
4. Agregar el certificado como una clave para la aplicación.

Cadena de conexión de ejemplo:

```csharp
string ConnectionString =@"Data Source=n9lxnyuzhv.database.windows.net; Initial Catalog=testdb;"
SqlConnection conn = new SqlConnection(ConnectionString);
conn.AccessToken = "Your JWT token"
conn.Open();
```

Para más información, consulte el [blog de seguridad de SQL Server](https://blogs.msdn.microsoft.com/sqlsecurity/20../../token-based-authentication-support-for-azure-sql-db-using-azure-ad-auth/). Para obtener información acerca de cómo agregar un certificado, consulte [Introducción a la autenticación basada en certificados de Azure Active Directory](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).

### <a name="sqlcmd"></a>sqlcmd

Las instrucciones siguientes permiten conectar con la versión 13.1 de sqlcmd, que está disponible en el [Centro de descarga](https://go.microsoft.com/fwlink/?LinkID=825643).

```cmd
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net  -G  
sqlcmd -S Target_DB_or_DW.testsrv.database.windows.net -U bob@contoso.com -P MyAADPassword -G -l 30
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de acceso y control en SQL Database, consulte [Control de acceso a Azure SQL Database](sql-database-control-access.md).
- Para obtener información general de los inicios de sesión, usuarios y roles de base de datos de SQL Database, consulte [Control y concesión de acceso a bases de datos](sql-database-manage-logins.md).
- Para más información acerca de las entidades de seguridad de bases de datos, consulte [Entidades de seguridad](https://msdn.microsoft.com/library/ms181127.aspx).
- Para más información acerca de los roles de base de datos, consulte [Roles de nivel de base de datos](https://msdn.microsoft.com/library/ms189121.aspx).
- Para más información general acerca de las reglas de firewall de SQL Database, consulte [Introducción a las reglas de firewall de Azure SQL Database](sql-database-firewall-configure.md).

<!--Image references-->

[1]: ./media/sql-database-aad-authentication/1aad-auth-diagram.png
[2]: ./media/sql-database-aad-authentication/2subscription-relationship.png
[3]: ./media/sql-database-aad-authentication/3admin-structure.png
[4]: ./media/sql-database-aad-authentication/4select-subscription.png
[5]: ./media/sql-database-aad-authentication/5ad-settings-portal.png
[6]: ./media/sql-database-aad-authentication/6edit-directory-select.png
[7]: ./media/sql-database-aad-authentication/7edit-directory-confirm.png
[8]: ./media/sql-database-aad-authentication/8choose-ad.png
[10]: ./media/sql-database-aad-authentication/10choose-admin.png
[11]: ./media/sql-database-aad-authentication/active-directory-integrated.png
[12]: ./media/sql-database-aad-authentication/12connect-using-pw-auth2.png
[13]: ./media/sql-database-aad-authentication/13connect-to-db2.png
