---
title: Puntos de conexión de red virtual y reglas para las bases de datos únicas y agrupadas en Azure SQL | Microsoft Docs
description: Marque una subred como punto de conexión de servicio de red virtual. A continuación, defina el punto de conexión como una regla de red virtual a la ACL de su base de datos de Azure SQL. Su instancia de SQL Database aceptará la comunicación de todas las máquinas virtuales y otros nodos de la subred.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: vanto, genemi
ms.date: 03/12/2019
ms.openlocfilehash: 9b28a8efcc09954d9046ad1dda3ba5f10f45bdfa
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840460"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-database-servers"></a>Usar reglas y puntos de conexión de servicio de red virtual para servidores de bases de datos

Las *reglas de red virtual* son una característica de firewall que controla si el servidor de las bases de datos únicas y el grupo elástico de Azure [SQL Database](sql-database-technical-overview.md) o las bases de datos de [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) acepta las comunicaciones que se envían desde subredes específicas en redes virtuales. En este artículo se explica por qué la característica de regla de red virtual a veces es la mejor opción para permitir la comunicación de forma segura con Azure SQL Database y SQL Data Warehouse.

> [!IMPORTANT]
> Este artículo se aplica a Azure SQL Server y tanto a las bases de datos de SQL Database como a SQL Data Warehouse que se crean en el servidor de Azure SQL. Para simplificar, SQL Database se utiliza cuando se hace referencia tanto a SQL Database como a SQL Data Warehouse. En cambio, este artículo *no* se aplica a la implementación de la **instancia administrada** de Azure SQL Database, ya que no tiene un punto de conexión de servicio asociado a ella.

Para crear una regla de red virtual, primero debe existir un [punto de conexión de servicio de red virtual][vm-virtual-network-service-endpoints-overview-649d] para la regla a la que hacer referencia.

## <a name="how-to-create-a-virtual-network-rule"></a>Creación de una regla de red virtual

Si solo crea una regla de red virtual, puede ir directamente a los pasos y a la explicación que hay [más adelante en este artículo](#anchor-how-to-by-using-firewall-portal-59j).

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminología y descripción

**Red virtual:** puede tener redes virtuales asociadas a la suscripción de Azure.

**Subred:** una red virtual contiene **subredes**. Cualquier máquina virtual (VM) de Azure que tenga se asignará a las subredes. Una subred puede contener varias máquinas virtuales u otros nodos de proceso. Los nodos de proceso que se encuentran fuera de la red virtual no pueden tener acceso a su red virtual a menos que configure la seguridad para que permita el acceso.

**Punto de conexión de servicio de red virtual:** un [punto de conexión de servicio de red virtual][vm-virtual-network-service-endpoints-overview-649d] es una subred cuyos valores de propiedad incluyen uno o más nombres formales de tipo de servicio de Azure. En este artículo nos interesa el nombre de tipo de **Microsoft.Sql**, que hace referencia al servicio de Azure denominado SQL Database.

**Regla de red virtual:** una regla de red virtual para el servidor de SQL Database es una subred que se muestra en la lista de control de acceso (ACL) del servidor de SQL Database. Para estar en la ACL de su SQL Database, la subred debe contener el nombre de tipo **Microsoft.Sql**.

Una regla de red virtual indica a su servidor de SQL Database que acepte las comunicaciones procedentes de todos los nodos que están en la subred.

<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Ventajas de una regla de red virtual

Hasta que no tome medidas, las máquinas virtuales de sus subredes no pueden comunicarse con SQL Database. Una acción que permite establecer la comunicación es la creación de una regla de red virtual. El motivo de la elección del enfoque de la regla de red virtual requiere un análisis de comparación y contraste relacionado con las opciones de seguridad competentes que ofrece el firewall.

### <a name="a-allow-access-to-azure-services"></a>A. Permitir el acceso a servicios de Azure

El panel de firewall tiene el botón **Activado/Desactivado** con la etiqueta **Permitir el acceso a servicios de Azure**. La configuración **Activado** permite las comunicaciones desde todas las direcciones IP de Azure y todas las subredes de Azure. Es posible que estas IP o subredes de Azure no le pertenezcan. Esta configuración **Activado** es probablemente más abierta de lo que le gustaría que fuera su instancia de SQL Database. La característica de la regla de red virtual ofrece un control mucho más granular.

### <a name="b-ip-rules"></a>B. Reglas IP

El firewall de SQL Database le permite especificar intervalos de direcciones IP desde los que se aceptan las comunicaciones en SQL Database. Este enfoque es preciso para las direcciones IP estables que están fuera de la red privada de Azure. Sin embargo, muchos nodos de dentro de la red privada de Azure se configuran con direcciones IP *dinámicas*. Es posible que las direcciones IP dinámicas cambien, por ejemplo, cuando se reinicia la máquina virtual. Sería una locura especificar una dirección IP dinámica en una regla de firewall, en un entorno de producción.

Para recuperar la opción de IP, puede obtener una dirección IP *estática* para la máquina virtual. Para más información, consulte [Configuración de direcciones IP privadas para una máquina virtual mediante Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Sin embargo, el enfoque de IP estática puede resultar difícil de administrar, y es costoso si se realiza a escala. Las reglas de red virtual son más sencillas de establecer y administrar.

> [!NOTE]
> Aún no se puede disponer de SQL Database en una subred. Si su servidor de Azure SQL Database fuera un nodo de una subred de la red virtual, todos los nodos de la red virtual podrían comunicarse con su instancia de SQL Database. En este caso, las máquinas virtuales podrían comunicarse con SQL Database sin necesitar ninguna regla IP ni regla de red virtual.

No obstante, a partir de septiembre de 2017, el servicio de Azure SQL Database aún no se encuentra entre los servicios que se pueden asignar a una subred.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Detalles sobre las reglas de red virtual

En esta sección se describen varios detalles acerca de las reglas de red virtual.

### <a name="only-one-geographic-region"></a>Solo una región geográfica

Cada punto de conexión de servicio de red virtual se aplica solo a una región de Azure. El punto de conexión no permite que otras regiones acepten la comunicación de la subred.

Cualquier regla de red virtual se limita a la región a la que se aplica su punto de conexión subyacente.

### <a name="server-level-not-database-level"></a>Nivel de servidor y no de base de datos

Cada regla de red virtual se aplica a todo el servidor de Azure SQL Database y no solo a una base de datos determinada del servidor. En otras palabras, la regla de red virtual se aplica en el nivel de servidor y no en el nivel de base de datos.

- En cambio, se pueden aplicar reglas IP en cualquier nivel.

### <a name="security-administration-roles"></a>Roles de administración de la seguridad

Existe una separación de los roles de seguridad en la administración de puntos de conexión de servicio de red virtual. Se requiere una acción de cada uno de los roles siguientes:

- **Administrador de red:** &nbsp; se activa el punto de conexión.
- **Administrador de base de datos:** &nbsp; Actualiza la lista de control de acceso (ACL) que se va a agregar a la subred proporcionada en el servidor de SQL Database.

*Alternativa de RBAC:*

Los roles de administrador de red y de base de datos tienen más funcionalidades de las que se necesitan para administrar las reglas de red virtual. Solo se necesita un subconjunto de sus capacidades.

Si quiere, puede optar por usar el [control de acceso basado en rol (RBAC)][rbac-what-is-813s] en Azure para crear un rol personalizado único que tenga solo el subconjunto necesario de funcionalidades. Se podría usar el rol personalizado en lugar del administrador de red o el administrador de la base de datos. El área expuesta de la exposición de seguridad es inferior si agrega un usuario a un rol personalizado, en lugar de agregar el usuario a los otros dos roles de administrador principales.

> [!NOTE]
> En algunos casos, Azure SQL Database y la subred de red virtual se encuentran en distintas suscripciones. En estos casos debe garantizar las siguientes configuraciones:
> - Ambas suscripciones deben estar en el mismo inquilino de Azure Active Directory.
> - El usuario tiene los permisos necesarios para iniciar operaciones como habilitar los puntos de conexión de servicio y agregar una subred de red virtual al servidor especificado.
> - Las dos suscripciones necesitan tener registrado el proveedor Microsoft.Sql.

## <a name="limitations"></a>Limitaciones

Para Azure SQL Database, la característica de regla de red virtual tiene las siguientes limitaciones:

- Una aplicación web se puede asignar a una dirección IP privada en una red virtual o subred. Incluso si los puntos de conexión de servicio están ACTIVADOS desde la red virtual o subred proporcionada, las conexiones de la aplicación web con el servidor tendrán un origen IP público de Azure, no un origen de red virtual o subred. Para habilitar la conectividad desde una aplicación web con un servidor que tenga reglas de firewall de red virtual, debe **Permitir que los servicios de Azure accedan al servidor** en el servidor.

- En el firewall de su instancia de SQL Database, cada regla de red virtual hace referencia a una subred. Todas estas subredes a las que se hace referencia deben estar hospedadas en la misma región geográfica que hospeda la base de datos SQL.

- Cada servidor de Azure SQL Database puede tener hasta 128 entradas de ACL para cualquier red virtual proporcionada.

- Las reglas de red virtual solo se aplican a las redes virtuales de Azure Resource Manager y no a las redes del [modelo de implementación clásico][arm-deployment-model-568f].

- La activación de puntos de conexión de servicio de red virtual en Azure SQL Database también habilita los puntos de conexión para los servicios MySQL y PostgreSQL de Azure. Sin embargo, con los puntos de conexión activados, se puede producir un error al intentar conectarse desde los puntos de conexión con las instancias de MySQL o PostgreSQL.
  - El motivo subyacente es que es probable que MySQL y PostgreSQL no tengan una regla de red virtual configurada. Debe configurar una regla de red virtual de Azure Database for MySQL y PostgreSQL y la conexión se realizará correctamente.

- En el firewall, los intervalos de direcciones IP se aplican a los siguientes elementos de red, pero no las reglas de red virtual:
  - [Red privada virtual (VPN) de sitio a sitio (S2S)][vpn-gateway-indexmd-608y]
  - Entorno local a través de [ExpressRoute][expressroute-indexmd-744v]

### <a name="considerations-when-using-service-endpoints"></a>Consideraciones al usar los puntos de conexión de servicio

Al utilizar los puntos de conexión de servicio para Azure SQL Database, revise las consideraciones siguientes:

- **Se requiere la salida a direcciones IP públicas de Azure SQL Database**: los grupos de seguridad de red (NSG) deben estar abiertos en las direcciones IP de Azure SQL Database para permitir la conectividad. Puede hacerlo mediante el uso de [etiquetas de servicio](../virtual-network/security-overview.md#service-tags) de NSG para Azure SQL Database.

### <a name="expressroute"></a>ExpressRoute

Si usa [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) desde las instalaciones para pares públicos o el emparejamiento de Microsoft, tiene que identificar las direcciones IP de NAT que se usan. Para el emparejamiento público, cada circuito ExpressRoute usa de forma predeterminada dos direcciones IP de NAT que se aplican al tráfico del servicio de Azure cuando el tráfico entra en la red troncal de Microsoft Azure. Para el emparejamiento de Microsoft, las direcciones IP de NAT que se utilizan las proporciona el cliente o el proveedor de servicios. Para permitir el acceso a los recursos de servicio, tiene que permitir estas direcciones IP públicas en la configuración del firewall de IP de recursos. Para encontrar las direcciones de IP de circuito de ExpressRoute de los pares públicos, [abra una incidencia de soporte técnico con ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) a través de Azure Portal. Obtenga más información sobre [NAT para ExpressRoute para emparejamiento público y de Microsoft.](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)
  
Para permitir la comunicación desde el circuito a Azure SQL Database, necesita crear reglas de red IP para las direcciones IP públicas de NAT.

<!--
FYI: Re ARM, 'Azure Service Management (ASM)' was the old name of 'classic deployment model'.
When searching for blogs about ASM, you probably need to use this old and now-forbidden name.
-->

## <a name="impact-of-removing-allow-azure-services-to-access-server"></a>Impacto de eliminar la opción "Permitir que los servicios de Azure accedan al servidor"

Muchos usuarios desean eliminar la opción **Permitir que los servicios de Azure accedan al servidor** de sus servidores de Azure SQL y reemplazarla por una regla de firewall de red virtual.
Pero esto afecta a las características siguientes:

### <a name="import-export-service"></a>Import Export Service

El servicio de importación y exportación de Azure SQL Database se ejecuta en VM de Azure. Estas máquinas virtuales no están en la red virtual y, por tanto, obtienen una dirección IP de Azure al conectarse a la base de datos. Al deshabilitar **Permitir que los servicios de Azure accedan al servidor**, estas máquinas virtuales no podrán acceder a las bases de datos.
Puede solucionar el problema. Ejecute la importación o exportación de BACPAC directamente en el código mediante la API de DACFx. Asegúrese de que la API esté implementada en una máquina virtual que se encuentre en la subred de la red virtual para la cual ha establecido la regla de firewall.

### <a name="sql-database-query-editor"></a>Editor de consultas de SQL Database

El editor de consultas de Azure SQL Database se implementa en máquinas virtuales de Azure. Estas máquinas virtuales no están en la red virtual. Por tanto, las máquinas virtuales obtienen una dirección IP de Azure cuando se conectan a la base de datos. Al deshabilitar **Permitir que los servicios de Azure accedan al servidor**, estas máquinas virtuales no podrán acceder a las bases de datos.

### <a name="table-auditing"></a>Auditoría de tablas

En la actualidad hay dos maneras de habilitar la auditoría en SQL Database. La auditoría de tablas produce un error después de haber habilitado los puntos de conexión de servicio en el servidor de Azure SQL Server. La solución en este caso consiste en cambiar a la auditoría de blobs.

### <a name="impact-on-data-sync"></a>Impacto en la sincronización de datos

Azure SQL Database tiene la característica Sincronización de datos, que se conecta a las bases de datos con direcciones IP de Azure. Al utilizar los puntos de conexión de servicio, es probable que desactive la opción **Permitir que los servicios de Azure accedan al servidor** de acceso al servidor de SQL Database. Esto inhabilitará la característica de sincronización de datos.

## <a name="impact-of-using-vnet-service-endpoints-with-azure-storage"></a>Efectos del uso de puntos de conexión de servicio de la red virtual con Azure Storage

Azure Storage ha implementado la misma característica que permite limitar la conectividad con la cuenta de Azure Storage. Si decide usar esta característica con una cuenta de Azure Storage que se está usando como un servidor de Azure SQL Server, es posible que se produzcan errores. A continuación se muestra una lista de las características de Azure SQL Database y Azure SQL Data Warehouse afectadas por esto, junto a la correspondiente explicación.

### <a name="azure-sql-data-warehouse-polybase"></a>PolyBase de Azure SQL Data Warehouse

PolyBase se suele usar para cargar datos en Azure SQL Data Warehouse desde cuentas de Azure Storage. Si la cuenta de Azure Storage desde la que se cargan los datos limita el acceso a solo un conjunto de subredes de red virtual, se interrumpirá la conectividad de PolyBase a la cuenta. Para habilitar los escenarios de importación y exportación de PolyBase con la conexión de Azure SQL Data Warehouse a Azure Storage protegido para la red virtual, siga los pasos indicados a continuación:

#### <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> El módulo de Azure Resource Manager para PowerShell todavía es compatible con Azure SQL Database, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Los argumentos para los comandos en el módulo Az y en los módulos AzureRm son esencialmente idénticos.

1.  Instale Azure PowerShell mediante esta [guía](https://docs.microsoft.com/powershell/azure/install-az-ps).
2.  Si tiene una cuenta de uso general v1 o de Blob Storage, primero debe actualizar a Uso general v2 mediante esta [guía](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
3.  Debe activar **Permitir que los servicios de Microsoft de confianza accedan a esta cuenta de almacenamiento** en el menú de configuración **Firewalls y redes virtuales** de la cuenta de Azure Storage. Consulte [esta guía](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions) para obtener más información.
 
#### <a name="steps"></a>Pasos
1. En PowerShell, **registre el servidor de SQL Database** con Azure Active Directory (AAD):

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId your-subscriptionId
   Set-AzSqlServer -ResourceGroupName your-database-server-resourceGroup -ServerName your-database-servername -AssignIdentity
   ```
    
   1. Cree una **cuenta de almacenamiento de uso general v2** con esta [guía](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).

   > [!NOTE]
   > - Si tiene una cuenta de uso general v1 o de Blob Storage, **primero debe actualizar a Uso general v2** mediante esta [guía](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade).
   > - Para saber los problemas conocidos con Azure Data Lake Storage Gen2, consulte esta [guía](https://docs.microsoft.com/azure/storage/data-lake-storage/known-issues).
    
1. En la cuenta de almacenamiento, vaya a **Control de acceso (IAM)** y haga clic en **Agregar asignación de roles**. Asigne el rol RBAC **Colaborador de datos de Storage Blob** al servidor de SQL Database.

   > [!NOTE] 
   > Solo los miembros con el privilegio Propietario pueden realizar este paso. Para obtener los distintos roles integrados para los recursos de Azure, consulte esta [guía](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).
  
1. **Conectividad de PolyBase a la cuenta de Azure Storage:**

   1. Cree una **[clave maestra](https://docs.microsoft.com/sql/t-sql/statements/create-master-key-transact-sql)** de base de datos si no la ha creado anteriormente:
       ```SQL
       CREATE MASTER KEY [ENCRYPTION BY PASSWORD = 'somepassword'];
       ```
    
   1. Cree la credencial de ámbito de base de datos con **IDENTITY = "Managed Service Identity"** :

       ```SQL
       CREATE DATABASE SCOPED CREDENTIAL msi_cred WITH IDENTITY = 'Managed Service Identity';
       ```
       > [!NOTE] 
       > - No es necesario especificar SECRET con la clave de acceso de Azure Storage porque este mecanismo usa la [identidad administrada](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) en segundo plano.
       > - El nombre de IDENTITY debe ser **"Managed Service Identity"** (Identidad de servicio administrado) para que la conectividad de PolyBase funcione con la cuenta de Azure Storage protegida en la red virtual.    
    
   1. Cree un origen de datos externo con el esquema abfss:// para conectarse a la cuenta de almacenamiento de uso general v2 mediante PolyBase:

       ```SQL
       CREATE EXTERNAL DATA SOURCE ext_datasource_with_abfss WITH (TYPE = hadoop, LOCATION = 'abfss://myfile@mystorageaccount.dfs.core.windows.net', CREDENTIAL = msi_cred);
       ```
       > [!NOTE] 
       > - Si ya tiene tablas externas asociadas con la cuenta de uso general v1 o de Blob Storage, primero debe quitarlas y, después, quitar el origen de datos externo correspondiente. Luego, cree el origen de datos externo con el esquema abfss:// para conectarse a la cuenta de almacenamiento de uso general v2 como antes, y vuelva a crear todas las tablas externas con este nuevo origen de datos externo. Puede usar el [Asistente Generar y publicar scripts](https://docs.microsoft.com/sql/ssms/scripting/generate-and-publish-scripts-wizard) para generar scripts de creación para todas las tablas externas con facilidad.
       > - Para obtener más información sobre el esquema abfss://, consulte esta [guía](https://docs.microsoft.com/azure/storage/data-lake-storage/introduction-abfs-uri).
       > - Para obtener más información sobre CREATE EXTERNAL DATA SOURCE, consulte esta [guía](https://docs.microsoft.com/sql/t-sql/statements/create-external-data-source-transact-sql).
        
   1. Realice una consulta normal con las [tablas externas](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql).

### <a name="azure-sql-database-blob-auditing"></a>Auditoría de blobs de Azure SQL Database

La auditoría de blobs inserta los registros de auditoría en su propia cuenta de almacenamiento. Si esta cuenta de almacenamiento usa la característica de puntos de conexión de servicio de red virtual, la conectividad entre Azure SQL Database y la cuenta de almacenamiento se interrumpirá.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Agregar una regla de firewall de red virtual al servidor sin tener que activar los puntos de conexión de servicio

Mucho antes de mejorar esta característica, era necesario activar los puntos de conexión de servicio de VNET para poder implementar una regla dinámica de VNET en el firewall. Los punto de conexión relacionaban una subred de VNET determinada con una base de datos de Azure SQL Database. Pero a partir de enero de 2018, puede evitar este requisito si establece la marca **IgnoreMissingVNetServiceEndpoint**.

Si solo establece una regla de firewall, no tendrá el servidor protegido. Por lo tanto, también debe activar los puntos de conexión de servicio de VNET para que la seguridad surta efecto. Al activar los puntos de conexión de servicio, la subred de VNET experimenta cierto tiempo de inactividad hasta que estos puntos se activan totalmente. Esto sucede especialmente en redes virtuales de gran tamaño. Puede usar la marca **IgnoreMissingVNetServiceEndpoint** para reducir o eliminar el tiempo de inactividad durante la activación.

Puede establecer la marca **IgnoreMissingVNetServiceEndpoint** mediante PowerShell. Para más información, consulte [PowerShell para crear una regla y un punto de conexión de servicio de red virtual para Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="errors-40914-and-40615"></a>Errores 40914 y 40615

El error de conexión 40914 se relaciona con *reglas de red virtual*, tal y como se especifica en el panel Firewall de Azure Portal. El error 40615 es similar, excepto que se relaciona con *reglas de direcciones IP* del Firewall.

### <a name="error-40914"></a>Error 40914

*Texto del mensaje:* No se puede abrir el servidor " *[nombre-servidor]* " solicitado por el inicio de sesión. Un cliente no puede acceder al servidor.

*Descripción del error:* el cliente está en una subred que tiene puntos de conexión de servidor de red virtual. Sin embargo, el servidor de Azure SQL Database no tiene ninguna regla de red virtual que conceda a la subred el derecho para comunicarse con la base de datos SQL.

*Resolución de errores:* en el panel Firewall de Azure Portal, use el control de reglas de red virtual para [agregar una regla de red virtual](#anchor-how-to-by-using-firewall-portal-59j) para la subred.

### <a name="error-40615"></a>Error 40615

*Texto del mensaje:* No se puede abrir el servidor "{0}" solicitado por el inicio de sesión. No está permitido que el cliente con la dirección IP "{1}" acceda al servidor.

*Descripción del error:* el cliente intenta conectarse desde una dirección IP que no tiene autorización para conectarse al servidor de Azure SQL Database. El firewall de servidor no tiene ninguna regla de dirección IP que permita que un cliente se comunique con la dirección IP dada a la instancia de SQL Database.

*Resolución de errores:* escriba la dirección IP del cliente como una regla de IP. Para ello, use el panel Firewall de Azure Portal.

Se puede encontrar información de una lista de varios mensajes de error de SQL Database [aquí][sql-database-develop-error-messages-419g].

<a name="anchor-how-to-by-using-firewall-portal-59j" />

## <a name="portal-can-create-a-virtual-network-rule"></a>Se puede crear una regla de red virtual en el portal

En esta sección se muestra cómo puede usar [Azure Portal][http-azure-portal-link-ref-477t] para crear una *regla de red virtual* en Azure SQL Database. La regla indica a su instancia de SQL Database que acepte la comunicación procedente de una subred concreta que se ha etiquetado como *punto de conexión de servicio de red virtual*.

> [!NOTE]
> Asegúrese de que los puntos de conexión de servicio están activados para la subred si piensa quiere agregar un punto de conexión de servicio a las reglas de firewall de red virtual de su servidor de Azure SQL Database.
>
> Si los puntos de conexión de servicio no están activados para la subred, el portal le pedirá que los habilite. Haga clic en el botón **Habilitar** de la misma hoja en la que agrega la regla.

## <a name="powershell-alternative"></a>Alternativa de PowerShell

Un script de PowerShell también puede crear reglas de red virtual. El cmdlet fundamental es **New-AzSqlServerVirtualNetworkRule**. Si le interesa, consulte [PowerShell para crear una regla y un punto de conexión de servicio de red virtual para Azure SQL Database][sql-db-vnet-service-endpoint-rule-powershell-md-52d].

## <a name="rest-api-alternative"></a>Alternativa a la API REST

Internamente, los cmdlets de PowerShell para acciones de red virtual SQL llaman a las API REST. Puede llamar directamente a las API REST.

- [Reglas de red virtual: Operaciones][rest-api-virtual-network-rules-operations-862r]

## <a name="prerequisites"></a>Requisitos previos

Ya debe tener una subred que esté etiquetada con el punto de conexión de servicio de red virtual *nombre de tipo* correspondiente a Azure SQL Database.

- El nombre de tipo de punto de conexión pertinente es **Microsoft.Sql**.
- Si es posible que su subred no se pueda etiquetar con el nombre de tipo, consulte [Comprobar que su subred es un punto de conexión][sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100].

<a name="a-portal-steps-for-vnet-rule-200" />

## <a name="azure-portal-steps"></a>Pasos de Azure Portal

1. Inicie sesión en el [Azure Portal][http-azure-portal-link-ref-477t].

2. A continuación, vaya al portal **Servidores SQL Server** &gt; **Firewall / Redes virtuales**.

3. Establezca el control **Permitir el acceso a los servicios de Azure** en Desactivado.

    > [!IMPORTANT]
    > Si deja el control establecido en Activado, el servidor de Azure SQL Database aceptará la comunicación desde cualquier subred. Si deja el control establecido en Activado, el número de accesos podría ser excesivo desde un punto de vista de seguridad. La característica de punto de conexión de servicio de red virtual de Microsoft Azure, junto con la característica de regla de red virtual de SQL Database, pueden reducir el área expuesta de seguridad.

4. Haga clic en el control **+ Agregar existente**, en la sección **Redes virtuales**.

    ![Haga clic en Agregar existente (punto de conexión de subred, como una regla SQL).][image-portal-firewall-vnet-add-existing-10-png]

5. En el nuevo panel **Crear/Actualizar**, rellene los controles con los nombres de los recursos de Azure.

    > [!TIP]
    > Debe incluir el **prefijo de dirección** correcto de la subred. Puede encontrar el valor en el portal.
    > Vaya a **Todos los recursos**&gt;**Todos los tipos**&gt;**Redes virtuales**. El filtro muestra sus redes virtuales. Haga clic en su red virtual y, a continuación, haga clic en **Subredes**. La columna **INTERVALO DE DIRECCIONES** tiene el prefijo de dirección que necesita.

    ![Rellene los campos de la nueva regla.][image-portal-firewall-create-update-vnet-rule-20-png]

6. Haga clic en el botón **Aceptar** situado cerca de la parte inferior del panel.

7. Vea la regla de red virtual resultante en el panel de firewall.

    ![Vea la nueva regla en el panel de firewall.][image-portal-firewall-vnet-result-rule-30-png]

> [!NOTE]
> Se aplican los siguientes estados a las reglas:
> - **Listo:** indica que la operación que se ha iniciado se ha realizado correctamente.
> - **Error:** indica que se han producido errores en la operación que se ha iniciado.
> - **Eliminado:** solo se aplica a la operación de eliminación e indica que se ha eliminado la regla y que ya no es aplicable.
> - **En curso:** indica que la operación está en curso. Se aplica la regla anterior mientras la operación está en este estado.

<a name="anchor-how-to-links-60h" />

## <a name="related-articles"></a>Artículos relacionados

- [Punto de conexión de servicio de red virtual de Azure][vm-virtual-network-service-endpoints-overview-649d]
- [Reglas de firewall de nivel de servidor y de nivel de base de datos de Azure SQL Database][sql-db-firewall-rules-config-715d]

La característica de la regla de red virtual de Azure SQL Database empezó a estar disponible a finales de septiembre de 2017.

## <a name="next-steps"></a>Pasos siguientes

- [Use PowerShell para crear un punto de conexión de servicio de red virtual y, a continuación, una regla de red virtual para Azure SQL Database.][sql-db-vnet-service-endpoint-rule-powershell-md-52d]
- [Reglas de red virtual: Operaciones][rest-api-virtual-network-rules-operations-862r] con API REST

<!-- Link references, to images. -->

[image-portal-firewall-vnet-add-existing-10-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-add-existing-10.png

[image-portal-firewall-create-update-vnet-rule-20-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-create-update-vnet-rule-20.png

[image-portal-firewall-vnet-result-rule-30-png]: media/sql-database-vnet-service-endpoint-rule-overview/portal-firewall-vnet-result-rule-30.png

<!-- Link references, to text, Within this same GitHub repo. -->

[arm-deployment-model-568f]: ../azure-resource-manager/resource-manager-deployment-model.md

[expressroute-indexmd-744v]: ../expressroute/index.yml

[rbac-what-is-813s]:../role-based-access-control/overview.md

[sql-db-firewall-rules-config-715d]: sql-database-firewall-configure.md

[sql-database-develop-error-messages-419g]: sql-database-develop-error-messages.md

[sql-db-vnet-service-endpoint-rule-powershell-md-52d]: sql-database-vnet-service-endpoint-rule-powershell.md

[sql-db-vnet-service-endpoint-rule-powershell-md-a-verify-subnet-is-endpoint-ps-100]: sql-database-vnet-service-endpoint-rule-powershell.md#a-verify-subnet-is-endpoint-ps-100

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[vm-virtual-network-service-endpoints-overview-649d]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

<!-- Link references, to text, Outside this GitHub repo (HTTP). -->

[http-azure-portal-link-ref-477t]: https://portal.azure.com/

[rest-api-virtual-network-rules-operations-862r]: https://docs.microsoft.com/rest/api/sql/virtualnetworkrules

<!-- ??2
#### Syntax related articles
- REST API Reference, including JSON

- Azure CLI

- ARM templates
-->
