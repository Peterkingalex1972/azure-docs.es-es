---
title: 'Recuperación ante desastres y conmutación por error de la cuenta de almacenamiento (versión preliminar): Azure Storage'
description: Azure Storage admite la conmutación por error de la cuenta (versión preliminar) en cuentas de almacenamiento con redundancia geográfica. Con la conmutación por error de la cuenta, puede iniciar el proceso de conmutación por error de la cuenta de almacenamiento si el punto de conexión principal deja de estar disponible.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 02/25/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 7785c6b5c575bf862b1ba0edccc75fc1c6031b08
ms.sourcegitcommit: df7942ba1f28903ff7bef640ecef894e95f7f335
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015656"
---
# <a name="disaster-recovery-and-storage-account-failover-preview-in-azure-storage"></a>Recuperación ante desastres y conmutación por error de la cuenta de almacenamiento (versión preliminar) en Azure Storage

Microsoft se esfuerza por garantizar que los servicios de Azure siempre estén disponibles. Sin embargo, es posible que se produzcan interrupciones en el servicio. Si la aplicación requiere resistencia, Microsoft recomienda usar el almacenamiento con redundancia geográfica para que los datos se repliquen en una segunda región. Además, los clientes deben tener implementado un plan de recuperación ante desastres para controlar una interrupción del servicio regional. Parte importante de un plan de recuperación ante desastres es preparar la conmutación por error en el punto de conexión secundario ante la eventualidad de que el punto de conexión principal deje de estar disponible. 

Azure Storage admite la conmutación por error de la cuenta (versión preliminar) en cuentas de almacenamiento con redundancia geográfica. Con la conmutación por error de la cuenta, puede iniciar el proceso de conmutación por error de la cuenta de almacenamiento si el punto de conexión principal deja de estar disponible. La conmutación por error actualiza el punto de conexión secundario para convertirlo en el principal de la cuenta de almacenamiento. Una vez finalizada la conmutación por error, los clientes pueden empezar a escribir en el nuevo punto de conexión principal.

En este artículo se describen los conceptos y el proceso que implica la conmutación por error de una cuenta y se analiza cómo preparar la cuenta de almacenamiento para la recuperación con el menor impacto en el cliente. Para aprender a iniciar la conmutación por error de una cuenta en Azure Portal o PowerShell, consulte el artículo sobre la [iniciación de la conmutación por error de una cuenta (versión preliminar)](storage-initiate-account-failover.md).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="choose-the-right-redundancy-option"></a>Elección de la opción de redundancia correcta

Todas las cuentas de almacenamiento se replican para obtener redundancia. La opción de redundancia que elija para la cuenta depende del grado de resistencia que necesita. Para protegerse contra interrupciones regionales, elija el almacenamiento con redundancia geográfica, ya sea con o sin la opción de acceso de lectura desde la región secundaria:  

El **almacenamiento con redundancia geográfica (GRS)** replica los datos de manera asincrónica en dos regiones geográficas separadas por al menos cientos de kilómetros. Si se produce una interrupción en la región primaria, la región secundaria sirve como origen redundante de los datos. Puede iniciar una conmutación por error para convertir el punto de conexión secundario en el principal.

El **almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS)** ofrece almacenamiento con redundancia geográfica con la ventaja adicional de acceso de lectura en el punto de conexión secundario. Si se produce una interrupción en el punto de conexión principal, las aplicaciones configuradas para RA-GRS y diseñadas para alta disponibilidad pueden seguir leyendo desde el punto de conexión secundario. Microsoft recomienda RA-GRS para obtener la máxima resistencia de las aplicaciones.

Otras opciones de redundancia de Azure Storage incluyen el almacenamiento con redundancia de zona (ZRS), que replica los datos de las zonas de disponibilidad en una sola región, y el almacenamiento con redundancia local (LRS), que replica los datos en un solo centro de datos de una sola región. Si la cuenta de almacenamiento está configurada para ZRS o LRS, puede convertir esa cuenta para usar GRS o RA-GRS. Configurar la cuenta para almacenamiento con redundancia geográfica genera costos adicionales. Para más información, consulte [Replicación de Azure Storage](storage-redundancy.md).

> [!NOTE]
> El almacenamiento con redundancia de zona geográfica (GZRS) y el almacenamiento con redundancia de zona geográfica con acceso de lectura (RA-GZRS) se encuentran actualmente en versión preliminar, pero aún no están disponibles en las mismas regiones que la conmutación por error de la cuenta administrada por el cliente. Por este motivo, los clientes no pueden administrar actualmente eventos de conmutación por error de cuenta con cuentas GZRS y RA-GZRS. Durante la versión preliminar, Microsoft administrará los eventos de conmutación por error que afecten a las cuentas GZRS o RA-GZRS.

> [!WARNING]
> El almacenamiento con redundancia geográfica supone un riesgo de pérdida de datos. Los datos se replican en la región secundaria de manera asincrónica, lo que significa que hay un retraso entre el momento en que los datos escritos en la región principal se escriben en la región secundaria. Si se produce una interrupción, se perderán las operaciones de escritura del punto de conexión principal que todavía no se hayan replicado en el punto de conexión secundario.

## <a name="design-for-high-availability"></a>Diseño para lograr alta disponibilidad

Es importante diseñar la aplicación para lograr alta disponibilidad desde el principio. Consulte estos recursos de Azure para instrucciones sobre cómo diseñar la aplicación y planificar la recuperación ante desastres:

* [Diseño de aplicaciones resistentes de Azure](https://docs.microsoft.com/azure/architecture/resiliency/): información general de los conceptos clave para diseñar aplicaciones altamente disponibles en Azure.
* [Lista de comprobación de disponibilidad](https://docs.microsoft.com/azure/architecture/checklist/availability): lista de comprobación para verificar que la aplicación implementa los mejores procedimientos recomendados para lograr alta disponibilidad.
* [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](storage-designing-ha-apps-with-ragrs.md): instrucciones de diseño para compilar aplicaciones para aprovechar RA-GRS.
* [Tutorial: Creación de una aplicación de alta disponibilidad con Blob Storage](../blobs/storage-create-geo-redundant-storage.md): tutorial que muestra cómo compilar una aplicación altamente disponible que cambia entre puntos de conexión de manera automática a medida que se simulan errores y recuperaciones. 

Además, tenga en cuenta estos procedimientos recomendados para mantener la alta disponibilidad de los datos de Azure Storage:

* **Discos:** use [Azure Backup](https://azure.microsoft.com/services/backup/) para crear copias de seguridad de los discos de VM que usan las máquinas virtuales de Azure. Considere también la posibilidad de usar [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) para proteger las máquinas virtuales en caso de un desastre regional.
* **Blobs en bloques:** active la [eliminación temporal](../blobs/storage-blob-soft-delete.md) para proteger contra eliminaciones o sobrescrituras de nivel de objeto o copie los blobs en bloques en otra cuenta de almacenamiento de otra región mediante[AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) o la [biblioteca de movimiento de datos de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* **Archivos:** use [AzCopy](storage-use-azcopy.md) o [Azure PowerShell](storage-powershell-guide-full.md) para copiar los archivos en otra cuenta de almacenamiento en una región distinta.
* **Tablas:** use [AzCopy](storage-use-azcopy.md) para exportar datos de tabla a otra cuenta de almacenamiento en una región distinta.

## <a name="track-outages"></a>Seguimiento de las interrupciones

Los clientes se pueden suscribir al [panel de Azure Service Health](https://azure.microsoft.com/status/) para hacer el seguimiento del estado de Azure Storage y otros servicios de Azure.

Microsoft también recomienda diseñar la aplicación para prepararse para la posibilidad de errores de escritura. La aplicación debe exponer los errores de escritura de manera de avisarle sobre la posibilidad de que se produzca una interrupción en la región primaria.

## <a name="understand-the-account-failover-process"></a>Descripción del proceso de conmutación por error de una cuenta

La conmutación por error de una cuenta administrada por el cliente (versión preliminar) le permite conmutar por error toda una cuenta de almacenamiento en la región secundaria si la región primaria deja de estar disponible por cualquier motivo. Cuando se fuerza una conmutación por error en la región secundaria, los clientes pueden empezar a escribir datos en el punto de conexión secundario una vez que se completa la conmutación por error. Por lo general, la conmutación por error tarda aproximadamente una hora.

### <a name="how-an-account-failover-works"></a>Funcionamiento de la conmutación por error de una cuenta

En circunstancias normales, un cliente escribe los datos en una cuenta de Azure Storage en la región primaria y esos datos se replican de forma asincrónica en la región secundaria. En la imagen siguiente se muestra el escenario donde está disponible la región primaria:

![Los clientes escriben los datos en la cuenta de almacenamiento de la región primaria](media/storage-disaster-recovery-guidance/primary-available.png)

Si el punto de conexión principal deja de estar disponible por cualquier motivo, el cliente ya no puede escribir en la cuenta de almacenamiento. En la imagen siguiente muestra el escenario en que la región primaria ha dejado de estar disponible, pero todavía no se ha realizado la recuperación:

![La región primaria no está disponible, por lo que los clientes no pueden escribir datos](media/storage-disaster-recovery-guidance/primary-unavailable-before-failover.png)

El cliente inicia la conmutación por error de la cuenta en el punto de conexión secundario. El proceso de conmutación por error actualiza la entrada DNS que proporciona Azure Storage, de modo tal que el punto de conexión secundario se convierte en el nuevo punto de conexión principal de la cuenta de almacenamiento, tal como se muestra en la imagen siguiente:

![El cliente inicia la conmutación por error de la cuenta en el punto de conexión secundario](media/storage-disaster-recovery-guidance/failover-to-secondary.png)

El acceso de escritura se restaura para las cuentas de GRS y RA-GRS una vez que la entrada DNS se actualiza y las solicitudes se dirigen al nuevo punto de conexión principal. Los puntos de conexión de servicio de almacenamiento existentes para blobs, tablas, colas y archivos no cambian después de la conmutación por error.

> [!IMPORTANT]
> Una vez que se completa la conmutación por error, la cuenta de almacenamiento se configura para que use la redundancia local en el nuevo punto de conexión principal. Para reanudar la replicación en el nuevo punto de conexión secundario, configure la cuenta para que vuelva a usar el almacenamiento con redundancia geográfica (ya sea RA-GRS o GRS).
>
> Tenga en cuenta que convertir una cuenta LRS en una cuenta RA-GRS o GRS supone un costo. Este costo se aplica a la actualización de la cuenta de almacenamiento de la nueva región primaria para usar RA-GRS o GRS después de una conmutación por error.  

### <a name="anticipate-data-loss"></a>Previsión de la pérdida de datos

> [!CAUTION]
> Por lo general, la conmutación por error de una cuenta implica perder algunos datos. Es importante entender las implicaciones que tiene iniciar la conmutación por error de una cuenta.  

Como los datos se escriben de manera asincrónica desde la región primaria a la secundaria, siempre se produce un retraso antes de que una escritura en la región primaria se replique en la secundaria. Si la región primaria deja de estar disponible, puede que las escrituras más recientes todavía no se hayan replicado en la región secundaria.

Cuando se fuerza una conmutación por error, todos los datos de la región primaria se pierden cuando la región secundaria se convierte en la nueva región primaria y la cuenta de almacenamiento está configurada para usar la redundancia local. Todos los datos ya replicados en la región secundaria se conservan cuando se produce la conmutación por error. Sin embargo, los datos escritos en la región primaria que todavía no se replican en la región secundaria se pierden definitivamente. 

La propiedad **Last Sync Time** indica la hora más reciente en que se garantiza que los datos de la región primaria se escribieron en la secundaria. Todos los datos escritos antes de la hora de la última sincronización están disponibles en la región secundaria, mientras que es posible que los datos escritos después de la hora de la última sincronización no se hayan escrito en la secundaria y pueden haberse perdido. Use esta propiedad si se produce una interrupción para calcular la cantidad de datos perdidos que puede haber al iniciar la conmutación por error de una cuenta. 

Como procedimiento recomendado, diseñe la aplicación para que pueda usar la hora de la última sincronización para evaluar la pérdida de datos esperada. Por ejemplo, si registra todas las operaciones de escritura, puede comparar la hora de las últimas operaciones de escritura con la hora de la última sincronización para determinar las escrituras que no se sincronizaron en la región secundaria.

### <a name="use-caution-when-failing-back-to-the-original-primary"></a>Precaución al conmutar por recuperación en la región primaria original

Después de conmutar por error desde la región primaria a la secundaria, la cuenta de almacenamiento se configura con redundancia local en la nueva región primaria. Puede configurar la cuenta para usar la redundancia geográfica nuevamente si la actualiza para que use GRS o RA-GRS. Cuando la cuenta se vuelve a configurar para usar la redundancia geográfica después de una conmutación por error, la nueva región primaria empieza de inmediato a replicar los datos en la nueva región secundaria, que antes de la conmutación por error original era la primaria. Sin embargo, puede pasar un tiempo antes de que los datos existentes en la región primaria se repliquen totalmente en la nueva región secundaria.

Una vez que la cuenta de almacenamiento se vuelve a configurar para la redundancia geográfica, es posible iniciar otra conmutación por error desde la nueva región primaria de vuelta a la nueva secundaria. En este caso, la región primaria original antes de la conmutación por error vuelve a serlo y se configura para usar la redundancia local. Todos los datos de la región primaria posterior a la conmutación por error (la región secundaria original) se pierden. Si la mayoría de los datos de la cuenta de almacenamiento nunca se ha replicado en la nueva región secundaria antes de la conmutación por recuperación, podría ocurrir una pérdida de datos importante. 

Para evitar que suceda esto, compruebe el valor de la propiedad **Hora de última sincronización** antes de la conmutación por recuperación. Compare la hora de última sincronización con las últimas horas en que los datos se escribieron en la nueva región primaria para evaluar la pérdida de datos esperada. 

## <a name="initiate-an-account-failover"></a>Iniciación de una conmutación por error de la cuenta

Puede iniciar la conmutación por error de una cuenta desde Azure Portal, PowerShell, la CLI de Azure o la API de proveedor de recursos de Azure Storage. Para más información sobre cómo iniciar una conmutación por error, consulte el artículo sobre la [iniciación de la conmutación por error de una cuenta (versión preliminar)](storage-initiate-account-failover.md).

## <a name="about-the-preview"></a>Acerca de la versión preliminar

La conmutación por error de una cuenta está disponible en versión preliminar para todos los clientes que usan GRS o RA-GRS con implementaciones de Azure Resource Manager. Se admiten los tipos de cuenta de Uso general v1, Uso general v2 y Cuenta de Blob Storage. La conmutación por error de una cuenta está disponible actualmente en estas regiones:

- Oeste de EE. UU. 2
- Centro-oeste de EE. UU.

La versión preliminar está pensada para usos distintos del de producción. En este momento no hay contratos de nivel de servicio de producción disponibles.

### <a name="register-for-the-preview"></a>Registro para obtener la versión preliminar

Para registrarse y obtener la versión preliminar, ejecute estos comandos en PowerShell. Asegúrese de reemplazar el marcador de posición que aparece entre corchetes por su propio identificador de suscripción:

```powershell
Connect-AzAccount -SubscriptionId <subscription-id>
Register-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

Puede tardar entre 1 y 2 días en recibir la aprobación para obtener la versión preliminar. Para comprobar que se aprobó el registro, ejecute el comando siguiente:

```powershell
Get-AzProviderFeature -FeatureName CustomerControlledFailover -ProviderNamespace Microsoft.Storage
```

### <a name="additional-considerations"></a>Consideraciones adicionales 

Revise las consideraciones adicionales que se describen en esta sección para entender cómo las aplicaciones y los servicios pueden verse afectados cuando se fuerza una conmutación por error durante el período de versión preliminar.

#### <a name="azure-virtual-machines"></a>Máquinas virtuales de Azure

Las máquinas virtuales (VM) de Azure no conmutan por error como parte de la conmutación por error de una cuenta. Si la región primaria deja de estar disponible y se realiza la conmutación por error en la región secundaria, deberá volver a crear cualquier máquina virtual después de la operación. 

#### <a name="azure-unmanaged-disks"></a>Discos no administrados de Azure

Como procedimiento recomendado, Microsoft aconseja convertir los discos no administrados en discos administrados. Sin embargo, si necesita conmutar por error una cuenta que contiene discos no administrados conectados a máquinas virtuales de Azure, deberá apagar la máquina virtual antes de iniciar la conmutación por error.

Los discos no administrados se almacenan como blobs en páginas en Azure Storage. Cuando una máquina virtual se ejecuta en Azure, se conceden los discos no administrados que están conectados a la VM. La conmutación por error de una cuenta no puede continuar si hay una concesión sobre un blob. Para realizar la conmutación por error, siga estos pasos:

1. Antes de empezar, anote los nombres de los discos no administrados, sus números de unidad lógica (LUN) y la máquina virtual a la que están conectados. Si lo hace, será más fácil volver a conectar los discos después de la conmutación por error. 
2. Apague la máquina virtual.
3. Elimine la máquina virtual, pero conserve los archivos VHD para los discos no administrados. Anote la hora a la que eliminó la máquina virtual.
4. Espere hasta que se actualice la **hora de la última sincronización** y sea posterior a la hora en la que eliminó la máquina virtual. Este paso es importante, porque si el punto de conexión secundario no se actualiza por completo con los archivos VHD cuando se produce la conmutación por error, es posible que la máquina virtual no funcione correctamente en la nueva región primaria.
5. Inicie la conmutación por error de la cuenta.
6. Espere hasta que se complete la conmutación por error de la cuenta y que la región secundaria se haya convertido en la nueva región primaria.
7. Cree una máquina virtual en la nueva región primaria y vuelva a conectar los discos duros virtuales.
8. Inicie la nueva máquina virtual.

Recuerde que los datos almacenados en un disco temporal se pierden cuando se apaga la máquina virtual.

### <a name="unsupported-features-or-services"></a>Características o servicios no compatibles
Las siguientes características o servicios no son compatibles con la conmutación por error de una cuenta en la versión preliminar:

- Azure File Sync no admite la conmutación por error de una cuenta de almacenamiento. No se debe realizar la conmutación por error de las cuentas de almacenamiento que contienen recursos compartidos de archivos de Azure y que se usan como puntos de conexión de nube en Azure File Sync. Si se hace, la sincronización dejará de funcionar y también podría provocar una pérdida inesperada de datos en el caso de archivos recién orgaizados en capas.  
- No se pueden conmutar por error las cuentas de almacenamiento que usan el espacio de nombres jerárquico de Azure Data Lake Storage Gen2.
- No se puede conmutar por error una cuenta de almacenamiento que contiene blobs archivados. Mantenga los blobs archivados en otra cuenta de almacenamiento que no planee conmutar por error.
- No se puede conmutar por error una cuenta de almacenamiento que contiene blobs en bloques Premium. Las cuentas de almacenamiento que admiten los blobs en bloques Premium actualmente no admiten la redundancia geográfica.
- Una vez completada la conmutación por error, las siguientes características dejarán de funcionar si estaban habilitadas originalmente: [Suscripciones a eventos](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-overview), [Directivas de ciclo de vida](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts), [Registro de Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/about-storage-analytics-logging).

## <a name="copying-data-as-an-alternative-to-failover"></a>Copia de datos como alternativa a la conmutación por error

Si la cuenta de almacenamiento está configurada para RA-GRS es porque tiene acceso de lectura a los datos con el punto de conexión secundario. Si prefiere no realizar la conmutación por error en caso de que se produzca una interrupción en la región primaria, puede usar herramientas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md) o la [biblioteca de movimiento de datos de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar datos de la cuenta de almacenamiento que se encuentra en la región secundaria a otra cuenta de almacenamiento en una región no afectada. Luego puede apuntar sus aplicaciones a esa cuenta de almacenamiento para obtener disponibilidad de lectura y escritura.

## <a name="microsoft-managed-failover"></a>Conmutación por error administrada por Microsoft

En casos extremos en los que se pierde una región debido a un desastre importante, Microsoft puede iniciar una conmutación por error regional. En este caso, no se requieren acciones por su parte. No tendrá acceso de escritura a la cuenta de almacenamiento hasta que se complete la conmutación por error administrada por Microsoft. Las aplicaciones pueden leer de la región secundaria si la cuenta de almacenamiento está configurada para RA-GRS. 

## <a name="see-also"></a>Otras referencias

* [Initiate an account failover (preview)](storage-initiate-account-failover.md) (Iniciación de la conmutación por error de una cuenta [versión preliminar])
* [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](storage-designing-ha-apps-with-ragrs.md)
* [Tutorial: Creación de una aplicación de alta disponibilidad con Blob Storage](../blobs/storage-create-geo-redundant-storage.md) 
