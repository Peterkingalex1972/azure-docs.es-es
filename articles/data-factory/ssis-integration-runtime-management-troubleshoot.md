---
title: Solución de problemas con la administración de SSIS Integration Runtime en Azure Data Factory | Microsoft Docs
description: En este artículo se proporciona orientación para solucionar problemas de administración de SSIS Integration Runtime (SSIS IR)
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/08/2019
author: chinadragon0515
ms.author: dashe
ms.reviewer: sawinark
manager: craigg
ms.openlocfilehash: 8abffdf443e26c03c38c12a3947a47a94157c9da
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68609624"
---
# <a name="troubleshoot-ssis-integration-runtime-management-in-azure-data-factory"></a>Solución de problemas con la administración de SSIS Integration Runtime en Azure Data Factory

En este artículo se proporcionan instrucciones para la solución de problemas de administración de Integration Runtime (IR) de Azure SQL Server Integration Services, también conocido como SSIS IR.

## <a name="overview"></a>Información general

Si tiene algún problema durante el aprovisionamiento o el desaprovisionamiento de SSIS IR, verá un mensaje de error en el portal de Microsoft Azure Data Factory o un error devuelto por un cmdlet de PowerShell. El error siempre está en formato de código de error con un mensaje detallado.

Si el código de error es InternalServerError, el servicio tiene problemas transitorios y debe reintentar la operación más tarde. Si el reintento no sirve, póngase en contacto con el equipo de soporte técnico de Azure Data Factory.

De lo contrario, se pueden producir errores en tres dependencias externas principales: un servidor o una instancia administrada de Azure SQL Database, un script de instalación personalizada y una configuración de red virtual.

## <a name="azure-sql-database-server-or-managed-instance-issues"></a>Problemas en el servidor de Azure SQL Database o Instancia administrada

Se requiere un servidor o una instancia administrada de Azure SQL Database si se aprovisiona SSIS IR con la base de datos de catálogo de SSIS. El SSIS IR debe poder acceder al servidor o a la instancia administrada de Azure SQL Database. Además, la cuenta del servidor o de la instancia administrada de Azure SQL Database debe tener permiso para crear una base de datos de catálogo de SSIS (SSISDB). Si se produce un error, se mostrará un código de error con un mensaje de excepción detallado de SQL en el portal de Data Factory. Use la información de la lista siguiente para solucionar los problemas de los códigos de error.

### <a name="azuresqlconnectionfailure"></a>AzureSqlConnectionFailure

Puede que observe este problema cuando vaya a aprovisionar un nuevo SSIS IR o mientras se ejecuta IR. Si experimenta este error durante el aprovisionamiento de IR, puede que reciba un mensaje detallado SqlException en el mensaje de error que indica uno de los siguientes problemas:

* Problema de conexión de red. Compruebe si se puede acceder al nombre de host de SQL Server o de la instancia administrada. Compruebe también que ningún firewall o grupo de seguridad de red (NSG) estén bloqueando el acceso de SSIS IR al servidor.
* Error de inicio de sesión durante la autenticación de SQL. La cuenta proporcionada no puede iniciar sesión en la base de datos de SQL Server. Asegúrese de que se ha proporcionado la cuenta de usuario correcta.
* Error de inicio de sesión durante la autenticación de Microsoft Azure Active Directory (Azure AD) (identidad administrada). Agregue la identidad administrada de su fábrica a un grupo de AAD y asegúrese de que tenga permisos de acceso al servidor de la base de datos de catálogo.
* Tiempo de espera de conexión agotado. Este error siempre se debe a una configuración relacionada con la seguridad. Se recomienda que:
  1. Cree una máquina virtual.
  1. Una la máquina virtual a la misma instancia de IR de Microsoft Azure Virtual Network si IR está en una red virtual.
  1. Instale SSMS y compruebe el estado del servidor o de la instancia administrada de Azure SQL Database.

Para otros problemas, solucione la incidencia mostrada en el mensaje de error detallado de la excepción de SQL. Si sigue teniendo problemas, póngase en contacto con el equipo de soporte técnico del servidor o de la instancia administrada de Azure SQL Database.

Si observa el error mientras se ejecuta IR, es probable que los cambios en el firewall o en el grupo de seguridad de red impidan que el nodo de trabajo de SSIS IR tenga acceso al servidor o a la instancia administrada de Azure SQL Database. Desbloquee el nodo de trabajo de SSIS IR para que pueda acceder al servidor o a la instancia administrada de Azure SQL Database.

### <a name="catalogcapacitylimiterror"></a>CatalogCapacityLimitError

Este es el tipo de mensaje de error que podría aparecer: "La base de datos "SSISDB" ha alcanzado su cuota de tamaño". Cree particiones o elimine datos, quite índices o consulte la documentación para obtener soluciones posibles". 

Las posibles soluciones son:
* Aumente el tamaño de la cuota de la SSISDB.
* Cambie la configuración de SSISDB para reducir el tamaño de las maneras siguientes:
   * Al reducir el período de retención y el número de versiones del proyecto.
   * Reduzca el período de retención del registro.
   * Cambie el nivel predeterminado del registro.

### <a name="catalogdbbelongstoanotherir"></a>CatalogDbBelongsToAnotherIR

Este error significa que el servidor o la instancia administrada de Azure SQL Database ya tienen una SSISDB y se usa en otra instancia de IR. Debe proporcionar un servidor o una instancia administrada de Azure SQL Database distintos o eliminar la SSISDB existente y reiniciar la nueva instancia de IR.

### <a name="catalogdbcreationfailure"></a>CatalogDbCreationFailure

Este problema puede ocurrir debido a uno de los siguientes motivos:

* La cuenta de usuario que está configurada para la instancia de SSIS IR no tiene permiso para crear la base de datos. Puede conceder al usuario permiso para crear la base de datos.
* Se agota el tiempo de espera, por ejemplo, tiempo de espera de ejecución o de operación de base de datos, durante la creación de la base de datos. Debe intentar la operación más tarde. Si el reintento no funciona, póngase en contacto con el equipo de soporte técnico del servidor o de la instancia administrada de Azure SQL Database.

Para otras incidencias, compruebe el mensaje de error de excepción de SQL y corrija la incidencia mencionada en los detalles del error. Si sigue teniendo problemas, póngase en contacto con el equipo de soporte técnico del servidor o de la instancia administrada de Azure SQL Database.

### <a name="invalidcatalogdb"></a>InvalidCatalogDb

Este tipo de mensaje de error se parece a este: "Nombre de objeto no válido 'catalog.catalog_properties'”. En esta situación, ya tiene una base de datos denominada SSISDB pero no se creó con SSIS IR; o bien, la base de datos se encuentra en un estado no válido debido a errores en el último aprovisionamiento de SSIS IR. Puede quitar la base de datos existente con el nombre SSISDB o configurar un servidor o una instancia administrada de Azure SQL Database nuevos para IR.

## <a name="custom-setup-issues"></a>Incidencias de instalación personalizada

La instalación personalizada proporciona una interfaz para agregar sus propios pasos de instalación durante el aprovisionamiento o la reconfiguración de SSIS IR. Para más información, consulte [Instalación personalizada de Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

Asegúrese de que el contenedor incluya solo los archivos de instalación personalizada necesarios; todos los archivos del contenedor se descargarán en el nodo de trabajo de SSIS IR. Se recomienda probar el script de instalación personalizada en una máquina local para corregir cualquier problema de ejecución antes de ejecutarlo en SSIS IR.

Mientras se ejecuta IR, se comprobará el contenedor de scripts de instalación personalizada, dado que SSIS IR se actualiza periódicamente. Esta actualización requiere acceso al contenedor para descargar el script de instalación personalizada e instalarlo de nuevo. El proceso también comprueba si el contenedor es accesible y si existe el archivo main.cmd.

Para cualquier error relacionado con la instalación personalizada, verá un código de error CustomSetupScriptFailure con subcódigo como CustomSetupScriptBlobContainerInaccessible o CustomSetupScriptNotFound.

### <a name="customsetupscriptblobcontainerinaccessible"></a>CustomSetupScriptBlobContainerInaccessible

Este error significa que SSIS IR no puede acceder al contenedor de blobs de Azure para la instalación personalizada. Compruebe que el URI de SAS del contenedor sea accesible y que no haya expirado.

Detenga el IR si está en ejecución, vuelva a configurarlo con el nuevo URI de SAS del contenedor de instalación personalizada y, luego, reinícielo.

### <a name="customsetupscriptnotfound"></a>CustomSetupScriptNotFound

Este error significa que SSIS IR no puede encontrar un script de instalación personalizada (main.cmd) en el contenedor de blobs. Asegúrese de que main.cmd exista en el contenedor, que es el punto de entrada para la instalación personalizada.

### <a name="customsetupscriptexecutionfailure"></a>CustomSetupScriptExecutionFailure

Este error significa que se produjo un error en la ejecución del script de instalación personalizada (main.cmd). Pruebe primero el script en la máquina local, o compruebe los registros de ejecución de la instalación personalizada en el contenedor de blobs.

### <a name="customsetupscripttimeout"></a>CustomSetupScriptTimeout

Este error indica un tiempo de espera en la ejecución del script de instalación personalizada. Asegúrese de que el contenedor de blobs contiene solo los archivos de instalación personalizada necesarios. También debe comprobar los registros de ejecución de la instalación personalizada en el contenedor de blobs. El período máximo para la instalación personalizada e de 45 minutos antes de que se agote el tiempo de espera, y el período máximo incluye el tiempo necesario para descargar todos los archivos del contenedor e instalarlos en SSIS IR. Si es necesario un período más largo, genere una incidencia de soporte técnico.

### <a name="customsetupscriptloguploadfailure"></a>CustomSetupScriptLogUploadFailure

Este error significa que se ha producido un error al intentar cargar los registros de ejecución de la instalación personalizada en el contenedor de blobs. Este problema se produce porque SSIS IR no tiene permisos de escritura en el contenedor de blobs o debido a problemas de almacenamiento o red. Si la instalación personalizada se realiza correctamente, este error no afectará a ninguna función de SSIS, pero se perderán los registros. Si se produce un error en la instalación personalizada y el registro no se carga, se notificará este error primero para que el registro pueda cargarse y analizarse. Además, después de resolver esta incidencia, notificaremos otras más específicas. Si la incidencia no se resuelve tras un reintento, póngase en contacto con el equipo de soporte técnico de Azure Data Factory.

## <a name="virtual-network-configuration"></a>Configuración de redes virtuales

Al unir SSIS IR a Azure Virtual Network, SSIS IR usa la red virtual que se encuentra en la suscripción del usuario. Para más información, consulte [Unión de Azure-SSIS Integration Runtime a una red virtual](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Cuando haya una incidencia relacionada con Virtual Network, verá uno de los siguientes errores.

### <a name="invalidvnetconfiguration"></a>InvalidVnetConfiguration

Este error puede producirse por diversos motivos. Para solucionarlo, consulte las secciones [Prohibido](#forbidden), [InvalidPropertyValue](#invalidpropertyvalue) y [MisconfiguredDnsServerOrNsgSettings](#misconfigureddnsserverornsgsettings).

### <a name="forbidden"></a>Prohibido

Este tipo de error puede parecerse a este: "Subnetid no está habilitado para la cuenta actual. El proveedor de recursos Microsoft.Batch no está registrado en la misma suscripción que la red virtual".

Estos detalles significan que Azure Batch no puede tener acceso a la red virtual. Registre el proveedor de recursos Microsoft.Batch en la misma suscripción que Virtual Network.

### <a name="invalidpropertyvalue"></a>InvalidPropertyValue

Este tipo de error puede parecerse a uno de los siguientes: 

- "La red virtual especificada no existe o el servicio Batch no tiene acceso a ella".
- "La subred especificada XXX no existe".

Estos errores significan que la red virtual no existe, que el servicio Azure Batch no puede acceder a ella o que la subred proporcionada no existe. Asegúrese de que la red virtual y la subred existen y que Azure Batch puede acceder a ellas.

### <a name="misconfigureddnsserverornsgsettings"></a>MisconfiguredDnsServerOrNsgSettings

Este tipo de mensaje de error puede parecerse a este: "No se pudo aprovisionar Integration Runtime en la red virtual. Si están configurados el servidor DNS o el grupo de seguridad de red, asegúrese de que el primero sea accesible y de que el segundo esté configurado correctamente".

En esta situación, es probable que tenga una configuración personalizada del servidor DNS o de NSG, lo que impide que el nombre del servidor de Azure que se requiere en SSIS IR se resuelva o se tenga acceso a él. Para más información, consulte [Configuración de Virtual Network para SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si el problema persiste, póngase en contacto con el equipo de soporte técnico de Azure Data Factory.

### <a name="vnetresourcegrouplockedduringupgrade"></a>VNetResourceGroupLockedDuringUpgrade

SSIS IR se actualizará automáticamente de forma periódica. Durante la actualización se crea un nuevo grupo de Azure Batch y se elimina el grupo de Azure Batch anterior. Además, se eliminan los recursos relacionados con Virtual Network del grupo anterior y se crean los recursos relacionados con Virtual Network en su suscripción. Este error significa que no se pudieron eliminar los recursos relacionados con Virtual Network del grupo anterior debido a un bloqueo de eliminación en el nivel de suscripción o grupo de recursos. Dado que el cliente controla y establece el bloqueo de eliminación, debe quitarlo en esta situación.

### <a name="vnetresourcegrouplockedduringstart"></a>VNetResourceGroupLockedDuringStart

Si se produce un error en el aprovisionamiento de SSIS IR, se eliminan todos los recursos que se crearon. Sin embargo, si hay un bloqueo de eliminación de recursos en el nivel de suscripción o grupo de recursos, los recursos de Virtual Network no se eliminan como estaba previsto. Para corregir este error, quite el bloqueo de eliminación y reinicie el IR.

### <a name="vnetresourcegrouplockedduringstop"></a>VNetResourceGroupLockedDuringStop

Cuando detenga SSIS IR, se eliminarán todos los recursos relacionados con Virtual Network. Sin embargo, se puede producir un error en la eliminación si hay un bloqueo de eliminación de recursos en el nivel de suscripción o grupo de recursos. Aquí, también, el cliente controla y establece el bloqueo de eliminación. Por lo tanto, debe quitar el bloqueo de eliminación y, luego, detener de nuevo el SSIS IR.

### <a name="nodeunavailable"></a>NodeUnavailable

Este error se produce cuando se ejecuta IR y significa que su estado ya no es correcto. Este error siempre se debe a un cambio en la configuración del servidor DNS o de NSG que impide que SSIS IR se conecte a un servicio necesario. Dado que es el cliente quien controla la configuración del servidor DNS y de NSG, debe corregir los problemas de bloqueo en su lado. Para más información, consulte [Configuración de Virtual Network para SSIS IR](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). Si el problema persiste, póngase en contacto con el equipo de soporte técnico de Azure Data Factory.
