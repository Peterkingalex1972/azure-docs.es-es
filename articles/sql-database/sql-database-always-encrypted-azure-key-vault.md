---
title: 'Always Encrypted: SQL Database: Azure Key Vault | Microsoft Docs'
description: En este artículo se muestra cómo proteger los datos confidenciales de una base de datos SQL con cifrado de datos mediante el asistente de Always Encrypted en SQL Server Management Studio.
keywords: cifrado de datos, clave de cifrado, cifrado en la nube
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 924ec20b9922d12da7291dc4f44b7413c68728c6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569582"
---
# <a name="always-encrypted-protect-sensitive-data-and-store-encryption-keys-in-azure-key-vault"></a>Always Encrypted: protección de datos confidenciales y almacenamiento de las claves de cifrado en Azure Key Vault

En este artículo se muestra cómo proteger los datos confidenciales de una base de datos SQL con cifrado de datos mediante el [asistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx) en [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/hh213248.aspx). También incluye instrucciones para almacenar cada clave de cifrado en Azure Key Vault.

Always Encrypted es una nueva tecnología de cifrado de datos de Azure SQL Database y SQL Server que ayuda a proteger los datos confidenciales en reposo en el servidor durante el traslado entre el cliente y el servidor, y mientras los datos están en uso. Always Encrypted garantiza que los datos confidenciales nunca van a aparecer como texto no cifrado dentro del sistema de base de datos. Después de configurar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones que tienen acceso a las claves pueden acceder a los datos de texto no cifrado. Para más información, consulte [Always Encrypted (motor de base de datos)](https://msdn.microsoft.com/library/mt163865.aspx).

Después de configurar la base de datos para usar Always Encrypted, creará una aplicación cliente en C# con Visual Studio para trabajar con los datos cifrados.

Siga los pasos de este artículo y aprenda a configurar Always Encrypted para una base de datos de Azure SQL. En este artículo aprenderá a realizar las siguientes tareas:

* Usar el asistente de Always Encrypted en SSMS para crear [claves de Always Encrypted](https://msdn.microsoft.com/library/mt163865.aspx#Anchor_3).
  * Crear una [clave maestra de columna (CMK)](https://msdn.microsoft.com/library/mt146393.aspx).
  * Crear una [clave de cifrado de columna (CEK)](https://msdn.microsoft.com/library/mt146372.aspx).
* Crear una tabla de base de datos y cifrar columnas.
* Crear una aplicación que inserta, selecciona y muestra los datos de las columnas cifradas.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos del módulo Az y en los módulos AzureRm son esencialmente idénticos.

Para este tutorial, necesitará:

* Una cuenta y una suscripción de Azure. Si no tiene una, suscríbase para [una prueba gratuita](https://azure.microsoft.com/pricing/free-trial/).
* [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) versión 13.0.700.242 o posterior.
* [.NET Framework 4.6](https://msdn.microsoft.com/library/w0x726c2.aspx) o posterior (en el equipo cliente).
* [Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx).
* [Azure PowerShell](/powershell/azure/overview).

## <a name="enable-your-client-application-to-access-the-sql-database-service"></a>Habilitación de la aplicación cliente para obtener acceso al servicio de SQL Database
Debe habilitar la aplicación cliente para obtener acceso al servicio de SQL Database mediante la configuración de una aplicación de Azure Active Directory (AAD) y la copia del *id. de la aplicación* y la *clave* que necesitará para autenticar la aplicación.

Para obtener el *id. de la aplicación* y la *clave*, siga estos pasos acerca de cómo [crear una aplicación de Azure Active Directory y una entidad de servicio con acceso a los recursos](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="create-a-key-vault-to-store-your-keys"></a>Creación de un almacén de claves para guardar las claves
Ahora que la aplicación cliente está configurada y tiene el id. de la aplicación, es el momento de crear un almacén de claves y configurar su directiva de acceso para que el usuario y su aplicación puedan acceder a los secretos del almacén (las claves de Always Encrypted). Los permisos *create*, *get*, *list*, *sign*, *verify*, *wrapKey* y *unwrapKey* son necesarios para crear una nueva clave maestra de columna y configurar el cifrado con SQL Server Management Studio.

Para crear rápidamente un almacén de claves, ejecute el script siguiente. Para obtener una explicación detallada de estos cmdlets y obtener más información sobre cómo crear y configurar un almacén de claves, consulte [¿Qué es Azure Key Vault?](../key-vault/key-vault-overview.md).

```powershell
    $subscriptionName = '<your Azure subscription name>'
    $userPrincipalName = '<username@domain.com>'
    $applicationId = '<application ID from your AAD application>'
    $resourceGroupName = '<resource group name>'
    # Use the same resource group name when creating your SQL Database below
    $location = '<datacenter location>'
    $vaultName = 'AeKeyVault'


    Connect-AzAccount
    $subscriptionId = (Get-AzSubscription -SubscriptionName $subscriptionName).Id
    Set-AzContext -SubscriptionId $subscriptionId

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzKeyVault -VaultName $vaultName -ResourceGroupName $resourceGroupName -Location $location

    Set-AzKeyVaultAccessPolicy -VaultName $vaultName -ResourceGroupName $resourceGroupName -PermissionsToKeys create,get,wrapKey,unwrapKey,sign,verify,list -UserPrincipalName $userPrincipalName
    Set-AzKeyVaultAccessPolicy  -VaultName $vaultName  -ResourceGroupName $resourceGroupName -ServicePrincipalName $applicationId -PermissionsToKeys get,wrapKey,unwrapKey,sign,verify,list
```



## <a name="create-a-blank-sql-database"></a>Crear una instancia en blanco en SQL Database
1. Inicie sesión en el [Azure Portal](https://portal.azure.com/).
2. Vaya a **Crear un recurso** > **Bases de datos** > **SQL Database**.
3. Cree una base de datos **en blanco** denominada **Clinic** en un servidor nuevo o existente. Para obtener instrucciones detalladas para crear una base de datos en Azure Portal, consulte [Su primera base de datos de Azure SQL](sql-database-single-database-get-started.md).
   
    ![Crear una base de datos en blanco](./media/sql-database-always-encrypted-azure-key-vault/create-database.png)

Necesitará la cadena de conexión más adelante en el tutorial; por lo tanto, después de crear la base de datos, vaya a la nueva base de datos Clinic y copie la cadena de conexión. Puede obtener la cadena de conexión en cualquier momento, pero es fácil copiarla en el Portal de Azure.

1. Vaya a **Bases de datos SQL** > **Clinic** > **Mostrar las cadenas de conexión de la base de datos**.
2. Copie la cadena de conexión para **ADO.NET**.
   
    ![Copiar la cadena de conexión](./media/sql-database-always-encrypted-azure-key-vault/connection-strings.png)

## <a name="connect-to-the-database-with-ssms"></a>Conéctese a la base de datos con SSMS
Abra SSMS y conéctese al servidor con la base de datos Clinic.

1. Abra SSMS. (Vaya a **Conectar** > **Motor de base de datos** para abrir la ventana **Conectar con el servidor** si no está abierta).
2. Escriba su nombre del servidor y las credenciales. El nombre del servidor puede encontrarse en la hoja de la base de datos SQL y en la cadena de conexión que copió anteriormente. Escriba el nombre de servidor completo, incluido *database.windows.net*.
   
    ![Copiar la cadena de conexión](./media/sql-database-always-encrypted-azure-key-vault/ssms-connect.png)

Si se abre la ventana **Nueva regla de firewall** , inicie sesión en Azure y permita que SSMS cree una nueva regla de firewall.

## <a name="create-a-table"></a>Creación de una tabla
En esta sección, creará una tabla para almacenar datos de pacientes. Inicialmente no están cifrados; configurará el cifrado en la sección siguiente.

1. Expanda **Bases de datos**.
2. Haga clic con el botón derecho en la base de datos **Clinic** y haga clic en **Nueva consulta**.
3. Pegue el siguiente código Transact-SQL (T-SQL) en la nueva ventana de consulta y haga clic en **Ejecutar** .

```sql
        CREATE TABLE [dbo].[Patients](
         [PatientId] [int] IDENTITY(1,1),
         [SSN] [char](11) NOT NULL,
         [FirstName] [nvarchar](50) NULL,
         [LastName] [nvarchar](50) NULL,
         [MiddleName] [nvarchar](50) NULL,
         [StreetAddress] [nvarchar](50) NULL,
         [City] [nvarchar](50) NULL,
         [ZipCode] [char](5) NULL,
         [State] [char](2) NULL,
         [BirthDate] [date] NOT NULL
         PRIMARY KEY CLUSTERED ([PatientId] ASC) ON [PRIMARY] );
         GO
```

## <a name="encrypt-columns-configure-always-encrypted"></a>Cifrado de columnas (configurar Always Encrypted)
SSMS proporciona un asistente para ayudar a configurar Always Encrypted fácilmente, que configura automáticamente la clave maestra de columna, la clave de cifrado de columna y las columnas cifradas.

1. Expandaa **Bases de datos** > **Clinic** > **Tablas**.
2. Haga clic con el botón derecho en la tabla **Patients** y seleccione **Cifrar columnas** para abrir el asistente de Always Encrypted:
   
    ![Cifrar columnas](./media/sql-database-always-encrypted-azure-key-vault/encrypt-columns.png)

El Asistente para Always Encrypted incluye las siguientes secciones: **Selección de columnas**, **Configuración de la clave maestra**, **Validación** y **Resumen**.

### <a name="column-selection"></a>Selección de columnas
En la página **Introducción**, haga clic en **Siguiente** para abrir la página **Selección de columnas**. En esta página, seleccione las columnas que desea cifrar, [el tipo de cifrado y qué clave de cifrado de columna (CEK)](https://msdn.microsoft.com/library/mt459280.aspx#Anchor_2) desea usar.

Cifre la información **SSN** y **BirthDate** de cada paciente. La columna SSN usará cifrado determinista, que admite búsquedas de igualdad, combinaciones y agrupaciones. La columna BirthDate usará cifrado aleatorio, que no admite operaciones.

Establezca el **Tipo de cifrado** de la columna SSN en **Determinista** y la columna BirthDate en **Aleatoria**. Haga clic en **Next**.

![Cifrar columnas](./media/sql-database-always-encrypted-azure-key-vault/column-selection.png)

### <a name="master-key-configuration"></a>Configuración de la clave maestra
En la página **Configuración de la clave maestra** se configura la clave maestra de columna (CMK) y se selecciona el proveedor del almacén de claves donde se almacenará la CMK. Actualmente, puede almacenar una CMK en el almacén de certificados de Windows, en Azure Key Vault o en un módulo de seguridad de hardware (HSM).

En este tutorial se muestra cómo almacenar las claves en Azure Key Vault.

1. Seleccione **Azure Key Vault**.
2. Seleccione el almacén de claves deseado en la lista desplegable.
3. Haga clic en **Next**.

![Configuración de la clave maestra](./media/sql-database-always-encrypted-azure-key-vault/master-key-configuration.png)

### <a name="validation"></a>Validación
Puede cifrar las columnas ahora o guardar un script de PowerShell para ejecutarlo más tarde. Para este tutorial, seleccione **Continuar para finalizar ahora** y haga clic en **Siguiente**.

### <a name="summary"></a>Resumen
Compruebe que la configuración sea correcta y haga clic en **Finalizar** para completar la configuración de Always Encrypted.

![Resumen](./media/sql-database-always-encrypted-azure-key-vault/summary.png)

### <a name="verify-the-wizards-actions"></a>Comprobación de las acciones del asistente
Una vez finalizado el asistente, la base de datos estará configurada para Always Encrypted. El asistente habrá realizado las siguientes acciones:

* Creación de una clave maestra de columna (CMK) y almacenamiento en Azure Key Vault.
* Creación de una clave de cifrado de columna (CMK) y almacenamiento en Azure Key Vault.
* Configuración de las columnas seleccionadas para el cifrado. La tabla Patients aún no tiene datos, pero los datos existentes en las columnas seleccionadas ahora están cifrados.

Para comprobar la creación de las claves en SSMS, expanda **Clinic** > **Seguridad** > **Claves de Always Encrypted**.

## <a name="create-a-client-application-that-works-with-the-encrypted-data"></a>Crear una aplicación cliente que funcione con los datos cifrados
Ahora que Always Encrypted está configurado, vamos a crear una aplicación que realice operaciones de *inserción* y *selección* en las columnas cifradas.  

> [!IMPORTANT]
> La aplicación debe usar objetos [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx) al pasar datos de texto sin cifrar al servidor con columnas de Always Encrypted. Se generará una excepción al pasar valores literales sin usar objetos SqlParameter.
> 
> 

1. Abra Visual Studio y cree una nueva **Aplicación de consola** de C# (Visual Studio 2015 y versiones anteriores) o una **Aplicación de consola (.NET Framework)** (Visual Studio 2017 y versiones posteriores). Asegúrese de que el proyecto esté establecido en **.NET Framework 4.6** o versiones posteriores.
2. Use el nombre **AlwaysEncryptedConsoleAKVApp** para el proyecto y haga clic en **Aceptar**.
3. Instale los siguientes paquetes NuGet; para ello, vaya a**Herramientas** > **Administrador de paquetes NuGet** > **Consola del Administrador de paquetes**.

Ejecute estas dos líneas de código en la Consola del Administrador de paquetes.

```powershell
    Install-Package Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
```


## <a name="modify-your-connection-string-to-enable-always-encrypted"></a>Modificar la cadena de conexión para habilitar Always Encrypted
En esta sección se explica cómo habilitar Always Encrypted en la cadena de conexión de su base de datos.

Para habilitar Always Encrypted, debe agregar la palabra clave **Column Encryption Setting** a la cadena de conexión y establecerla en **Enabled**.

Puede establecerla directamente en la cadena de conexión o mediante [SqlConnectionStringBuilder](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.aspx). La aplicación de ejemplo de la siguiente sección muestra cómo usar **SqlConnectionStringBuilder**.

### <a name="enable-always-encrypted-in-the-connection-string"></a>Modificar Always Encrypted en la cadena de conexión
Agregue la siguiente palabra clave a la cadena de conexión.

    Column Encryption Setting=Enabled


### <a name="enable-always-encrypted-with-sqlconnectionstringbuilder"></a>Habilitar Always Encrypted con SqlConnectionStringBuilder
El siguiente código muestra cómo habilitar Always Encrypted estableciendo [SqlConnectionStringBuilder.ColumnEncryptionSetting](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectionstringbuilder.columnencryptionsetting.aspx) en [Enabled](https://msdn.microsoft.com/library/system.data.sqlclient.sqlconnectioncolumnencryptionsetting.aspx).

```CS
    // Instantiate a SqlConnectionStringBuilder.
    SqlConnectionStringBuilder connStringBuilder =
       new SqlConnectionStringBuilder("replace with your connection string");

    // Enable Always Encrypted.
    connStringBuilder.ColumnEncryptionSetting =
       SqlConnectionColumnEncryptionSetting.Enabled;
```

## <a name="register-the-azure-key-vault-provider"></a>Registro del proveedor de Azure Key Vault
El código siguiente muestra cómo registrar el proveedor de Azure Key Vault con el controlador de ADO.NET.

```csharp
    private static ClientCredential _clientCredential;

    static void InitializeAzureKeyVaultProvider()
    {
       _clientCredential = new ClientCredential(applicationId, clientKey);

       SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
          new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

       Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
          new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

       providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
       SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
    }
```

## <a name="always-encrypted-sample-console-application"></a>Aplicación de consola de ejemplo de Always Encrypted
En este ejemplo se muestra cómo:

* Modificar la cadena de conexión para habilitar Always Encrypted.
* Registrar Azure Key Vault como proveedor de almacén de claves de la aplicación.  
* Insertar datos en las columnas cifradas.
* Seleccionar un registro mediante el filtrado de un valor específico de una columna cifrada.

Reemplace el contenido de **Program.cs** por el código siguiente. Reemplace la cadena de conexión para la variable global connectionString en la línea directamente anterior al método Main por una cadena de conexión válida desde el Portal de Azure. Este es el único cambio que necesita realizar en este código.

Ejecute la aplicación para ver Always Encrypted en acción.
```CS
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using System.Data;
    using System.Data.SqlClient;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.SqlServer.Management.AlwaysEncrypted.AzureKeyVaultProvider;

    namespace AlwaysEncryptedConsoleAKVApp
    {
    class Program
    {
        // Update this line with your Clinic database connection string from the Azure portal.
        static string connectionString = @"<connection string from the portal>";
        static string applicationId = @"<application ID from your AAD application>";
        static string clientKey = "<key from your AAD application>";


        static void Main(string[] args)
        {
            InitializeAzureKeyVaultProvider();

            Console.WriteLine("Signed in as: " + _clientCredential.ClientId);

            Console.WriteLine("Original connection string copied from the Azure portal:");
            Console.WriteLine(connectionString);

            // Create a SqlConnectionStringBuilder.
            SqlConnectionStringBuilder connStringBuilder =
                new SqlConnectionStringBuilder(connectionString);

            // Enable Always Encrypted for the connection.
            // This is the only change specific to Always Encrypted
            connStringBuilder.ColumnEncryptionSetting =
                SqlConnectionColumnEncryptionSetting.Enabled;

            Console.WriteLine(Environment.NewLine + "Updated connection string with Always Encrypted enabled:");
            Console.WriteLine(connStringBuilder.ConnectionString);

            // Update the connection string with a password supplied at runtime.
            Console.WriteLine(Environment.NewLine + "Enter server password:");
            connStringBuilder.Password = Console.ReadLine();


            // Assign the updated connection string to our global variable.
            connectionString = connStringBuilder.ConnectionString;


            // Delete all records to restart this demo app.
            ResetPatientsTable();

            // Add sample data to the Patients table.
            Console.Write(Environment.NewLine + "Adding sample patient data to the database...");

            InsertPatient(new Patient()
            {
                SSN = "999-99-0001",
                FirstName = "Orlando",
                LastName = "Gee",
                BirthDate = DateTime.Parse("01/04/1964")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0002",
                FirstName = "Keith",
                LastName = "Harris",
                BirthDate = DateTime.Parse("06/20/1977")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0003",
                FirstName = "Donna",
                LastName = "Carreras",
                BirthDate = DateTime.Parse("02/09/1973")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0004",
                FirstName = "Janet",
                LastName = "Gates",
                BirthDate = DateTime.Parse("08/31/1985")
            });
            InsertPatient(new Patient()
            {
                SSN = "999-99-0005",
                FirstName = "Lucy",
                LastName = "Harrington",
                BirthDate = DateTime.Parse("05/06/1993")
            });


            // Fetch and display all patients.
            Console.WriteLine(Environment.NewLine + "All the records currently in the Patients table:");

            foreach (Patient patient in SelectAllPatients())
            {
                Console.WriteLine(patient.FirstName + " " + patient.LastName + "\tSSN: " + patient.SSN + "\tBirthdate: " + patient.BirthDate);
            }

            // Get patients by SSN.
            Console.WriteLine(Environment.NewLine + "Now lets locate records by searching the encrypted SSN column.");

            string ssn;

            // This very simple validation only checks that the user entered 11 characters.
            // In production be sure to check all user input and use the best validation for your specific application.
            do
            {
                Console.WriteLine("Please enter a valid SSN (ex. 999-99-0003):");
                ssn = Console.ReadLine();
            } while (ssn.Length != 11);

            // The example allows duplicate SSN entries so we will return all records
            // that match the provided value and store the results in selectedPatients.
            Patient selectedPatient = SelectPatientBySSN(ssn);

            // Check if any records were returned and display our query results.
            if (selectedPatient != null)
            {
                Console.WriteLine("Patient found with SSN = " + ssn);
                Console.WriteLine(selectedPatient.FirstName + " " + selectedPatient.LastName + "\tSSN: "
                    + selectedPatient.SSN + "\tBirthdate: " + selectedPatient.BirthDate);
            }
            else
            {
                Console.WriteLine("No patients found with SSN = " + ssn);
            }

            Console.WriteLine("Press Enter to exit...");
            Console.ReadLine();
        }


        private static ClientCredential _clientCredential;

        static void InitializeAzureKeyVaultProvider()
        {

            _clientCredential = new ClientCredential(applicationId, clientKey);

            SqlColumnEncryptionAzureKeyVaultProvider azureKeyVaultProvider =
              new SqlColumnEncryptionAzureKeyVaultProvider(GetToken);

            Dictionary<string, SqlColumnEncryptionKeyStoreProvider> providers =
              new Dictionary<string, SqlColumnEncryptionKeyStoreProvider>();

            providers.Add(SqlColumnEncryptionAzureKeyVaultProvider.ProviderName, azureKeyVaultProvider);
            SqlConnection.RegisterColumnEncryptionKeyStoreProviders(providers);
        }

        public async static Task<string> GetToken(string authority, string resource, string scope)
        {
            var authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(resource, _clientCredential);

            if (result == null)
                throw new InvalidOperationException("Failed to obtain the access token");
            return result.AccessToken;
        }

        static int InsertPatient(Patient newPatient)
        {
            int returnValue = 0;

            string sqlCmdText = @"INSERT INTO [dbo].[Patients] ([SSN], [FirstName], [LastName], [BirthDate])
     VALUES (@SSN, @FirstName, @LastName, @BirthDate);";

            SqlCommand sqlCmd = new SqlCommand(sqlCmdText);


            SqlParameter paramSSN = new SqlParameter(@"@SSN", newPatient.SSN);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            SqlParameter paramFirstName = new SqlParameter(@"@FirstName", newPatient.FirstName);
            paramFirstName.DbType = DbType.String;
            paramFirstName.Direction = ParameterDirection.Input;

            SqlParameter paramLastName = new SqlParameter(@"@LastName", newPatient.LastName);
            paramLastName.DbType = DbType.String;
            paramLastName.Direction = ParameterDirection.Input;

            SqlParameter paramBirthDate = new SqlParameter(@"@BirthDate", newPatient.BirthDate);
            paramBirthDate.SqlDbType = SqlDbType.Date;
            paramBirthDate.Direction = ParameterDirection.Input;

            sqlCmd.Parameters.Add(paramSSN);
            sqlCmd.Parameters.Add(paramFirstName);
            sqlCmd.Parameters.Add(paramLastName);
            sqlCmd.Parameters.Add(paramBirthDate);

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();
                }
                catch (Exception ex)
                {
                    returnValue = 1;
                    Console.WriteLine("The following error was encountered: ");
                    Console.WriteLine(ex.Message);
                    Console.WriteLine(Environment.NewLine + "Press Enter key to exit");
                    Console.ReadLine();
                    Environment.Exit(0);
                }
            }
            return returnValue;
        }


        static List<Patient> SelectAllPatients()
        {
            List<Patient> patients = new List<Patient>();


            SqlCommand sqlCmd = new SqlCommand(
              "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients]",
                new SqlConnection(connectionString));


            using (sqlCmd.Connection = new SqlConnection(connectionString))

            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patients.Add(new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            });
                        }
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }

            return patients;
        }


        static Patient SelectPatientBySSN(string ssn)
        {
            Patient patient = new Patient();

            SqlCommand sqlCmd = new SqlCommand(
                "SELECT [SSN], [FirstName], [LastName], [BirthDate] FROM [dbo].[Patients] WHERE [SSN]=@SSN",
                new SqlConnection(connectionString));

            SqlParameter paramSSN = new SqlParameter(@"@SSN", ssn);
            paramSSN.DbType = DbType.AnsiStringFixedLength;
            paramSSN.Direction = ParameterDirection.Input;
            paramSSN.Size = 11;

            sqlCmd.Parameters.Add(paramSSN);


            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    SqlDataReader reader = sqlCmd.ExecuteReader();

                    if (reader.HasRows)
                    {
                        while (reader.Read())
                        {
                            patient = new Patient()
                            {
                                SSN = reader[0].ToString(),
                                FirstName = reader[1].ToString(),
                                LastName = reader["LastName"].ToString(),
                                BirthDate = (DateTime)reader["BirthDate"]
                            };
                        }
                    }
                    else
                    {
                        patient = null;
                    }
                }
                catch (Exception ex)
                {
                    throw;
                }
            }
            return patient;
        }


        // This method simply deletes all records in the Patients table to reset our demo.
        static int ResetPatientsTable()
        {
            int returnValue = 0;

            SqlCommand sqlCmd = new SqlCommand("DELETE FROM Patients");
            using (sqlCmd.Connection = new SqlConnection(connectionString))
            {
                try
                {
                    sqlCmd.Connection.Open();
                    sqlCmd.ExecuteNonQuery();

                }
                catch (Exception ex)
                {
                    returnValue = 1;
                }
            }
            return returnValue;
        }
    }

    class Patient
    {
        public string SSN { get; set; }
        public string FirstName { get; set; }
        public string LastName { get; set; }
        public DateTime BirthDate { get; set; }
    }
    }
```


## <a name="verify-that-the-data-is-encrypted"></a>Comprobación del cifrado de los datos
Para comprobar rápidamente que los datos reales del servidor están cifrados, podemos consultar los datos de los pacientes con SSMS (mediante la conexión actual en la que el parámetro **Column Encryption Setting** aún no está habilitado).

Ejecute la siguiente consulta en la base de datos Clinic.

```sql
    SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
```

Puede ver que las columnas cifradas no contienen datos de texto no cifrado.

   ![Nueva aplicación de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-encrypted.png)

Para usar SSMS para acceder a los datos de texto simple, primero deberá asegurarse de que el usuario tiene los permisos adecuados para Azure Key Vault: *get*, *unwrapKey* y *verify*. Para obtener información detallada, consulte [Creación y almacenamiento de claves maestras de columna (Always Encrypted)](https://docs.microsoft.com/sql/relational-databases/security/encryption/create-and-store-column-master-keys-always-encrypted).

A continuación, agregue el parámetro *Column Encryption Setting=enabled* durante la conexión.

1. En SSMS, haga clic con el botón derecho en el servidor en el **Explorador de objetos** y elija **Desconectar**.
2. Haga clic en **Conectar** > **Motor de base de datos** para abrir la ventana **Conectar con el servidor** y haga clic en **Opciones**.
3. Haga clic en **Parámetros de conexión adicionales** y escriba **Column Encryption Setting=enabled**.
   
    ![Nueva aplicación de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-connection-parameter.png)
4. Ejecute la siguiente consulta en la base de datos Clinic.

   ```sql
      SELECT FirstName, LastName, SSN, BirthDate FROM Patients;
   ```

     Ahora puede ver los datos de texto no cifrado en las columnas cifradas.
     ![Nueva aplicación de consola](./media/sql-database-always-encrypted-azure-key-vault/ssms-plaintext.png)


## <a name="next-steps"></a>Pasos siguientes
Después de crear una base de datos que usa Always Encrypted, es posible que quiera hacer lo siguiente:

* [Rotar y limpiar las claves](https://msdn.microsoft.com/library/mt607048.aspx).
* [Migrar los datos que ya están cifrados con Always Encrypted](https://msdn.microsoft.com/library/mt621539.aspx).

## <a name="related-information"></a>Información relacionada
* [Always Encrypted (desarrollo de clientes)](https://msdn.microsoft.com/library/mt147923.aspx)
* [Cifrado de datos transparente](https://msdn.microsoft.com/library/bb934049.aspx)
* [Cifrado de SQL Server](https://msdn.microsoft.com/library/bb510663.aspx)
* [Asistente de Always Encrypted](https://msdn.microsoft.com/library/mt459280.aspx)
* [Blog de Always Encrypted](https://blogs.msdn.com/b/sqlsecurity/archive/tags/always-encrypted/)

