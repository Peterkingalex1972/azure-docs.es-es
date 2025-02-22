---
title: Límites y cuotas de suscripción de Azure
description: Se proporciona una lista de límites, cuotas y restricciones de suscripción y servicio comunes de Azure. Este artículo incluye información acerca de cómo aumentar los límites junto con los valores máximos.
services: multiple
author: rothja
manager: jeffreyg
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.topic: article
ms.date: 05/30/2019
ms.author: byvinyal
ms.openlocfilehash: 7c4c80950e43f374b40085cb4e1c3e026e5f3abd
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68698279"
---
# <a name="azure-subscription-and-service-limits-quotas-and-constraints"></a>Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure
Este documento enumeran algunos de los límites más comunes de Microsoft Azure, que a veces se denominan cuotas. Actualmente, este documento no cubre todos los servicios de Azure. Con el tiempo, esta lista se expandirá y actualizará para abarcar más servicios.

Consulte [Precios de Azure](https://azure.microsoft.com/pricing/) para más información sobre precios de Azure. Allí, puede calcular los costos mediante el uso de la [Calculadora de precios](https://azure.microsoft.com/pricing/calculator/). También puede ir a la página de detalles de precios de un servicio determinado, por ejemplo, [Máquinas virtuales Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows). Si quiere obtener sugerencias para ayudar a administrar los costos, vea [Prevención de costos inesperados con la administración de costos y facturación de Azure](billing/billing-getting-started.md).

> [!NOTE]
> Si desea aumentar el límite o la cuota por encima del límite predeterminado, [abra una solicitud de soporte técnico al cliente en línea sin cargo alguno](azure-resource-manager/resource-manager-quota-errors.md). Los límites no se pueden aumentar por encima del valor de Límite máximo que se muestra en las tablas siguientes. Si no hay ninguna columna de límite máximo, el recurso no tiene límites ajustables.
>
> Las [suscripciones de evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p) no son aptas para aumentar el límite ni la cuota. Si tiene una [suscripción de evaluación gratuita](https://azure.microsoft.com/offers/ms-azr-0044p), puede actualizar a una suscripción de [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/). Para más información, consulte [Actualización de la suscripción de evaluación gratuita de Azure a pago por uso](billing/billing-upgrade-azure-subscription.md) y [Preguntas más frecuentes sobre la cuenta gratuita de Azure](https://azure.microsoft.com/free/free-account-faq).
>

## <a name="limits-and-azure-resource-manager"></a>Límites y Azure Resource Manager
Ahora es posible combinar varios recursos de Azure en un único grupo de recursos de Azure. Al utilizar grupos de recursos, los límites que una vez fueron globales se convierten en administrados a nivel regional con Azure Resource Manager. Para más información sobre los grupos de recursos de Azure, consulte [Introducción a Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

En la siguiente lista de límites, una nueva tabla refleja las diferencias en los límites cuando se usa Azure Resource Manager. Por ejemplo, hay una tabla de **Límites de suscripción** y una tabla de **Límites de suscripción: Azure Resource Manager**. Cuando un límite se aplica a ambos escenarios, solo se muestra en la primera tabla. A menos que se indique lo contrario, los límites son globales en todas las regiones.

> [!NOTE]
> Las cuotas de los recursos de los grupos de recursos de Azure son accesibles para su suscripción en función de la región y no en función de la suscripción, como las cuotas de administración de servicios. Usemos las cuotas de vCPU como ejemplo. Para solicitar un aumento de cuota con compatibilidad para vCPU, debe decidir cuántas vCPU quiere usar en las distintas regiones. A continuación, realice una solicitud específica para las cuotas de vCPU del grupo de recursos de Azure para las cantidades y regiones que desee. Si necesita usar 30 vCPU en Oeste de Europa para ejecutar la aplicación allí, deberá solicitar específicamente 30 vCPU en Oeste de Europa. Su cuota de vCPU no se aumenta en ninguna otra región: solo Oeste de Europa tiene la cuota de 30 vCPU.
> <!-- -->
> Como resultado, debe decidir cuáles deben ser sus cuotas de grupo de recursos de Azure para la carga de trabajo en cada región. A continuación, solicite esa cantidad en cada región en la que se desea implementar. Consulte [Solución de incidencias de implementación](resource-manager-common-deployment-errors.md) para obtener ayuda para determinar las cuotas actuales para regiones específicas.
>
>

## <a name="service-specific-limits"></a>Límites específicos del servicio
* [Active Directory](#active-directory-limits)
* [API Management](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Automation](#automation-limits)
* [Azure Cache for Redis](#azure-cache-for-redis-limits)
* [Azure Cloud Services](#azure-cloud-services-limits)
* [Azure Cognitive Services](#azure-cognitive-services-limits)
* [Azure Cosmos DB](#azure-cosmos-db-limits)
* [Azure Database for MySQL](#azure-database-for-mysql)
* [Azure Database para PostgreSQL](#azure-database-for-postgresql)
* [DNS de Azure](#azure-dns-limits)
* [Azure Firewall](#azure-firewall-limits)
* [Azure Functions](#functions-limits)
* [Azure Kubernetes Service](#azure-kubernetes-service-limits)
* [Servicio Azure Machine Learning](#azure-machine-learning-service-limits)
* [Azure Maps](#azure-maps-limits)
* [Azure Monitor](#azure-monitor-limits)
* [Azure Policy](#azure-policy-limits)
* [Azure Search](#azure-search-limits)
* [Azure SignalR Service](#azure-signalr-service-limits)
* [Copia de seguridad](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [Azure Container Instances](#container-instances-limits)
* [Container Registry](#container-registry-limits)
* [Content Delivery Network](#content-delivery-network-limits)
* [Factoría de datos](#data-factory-limits)
* [Análisis de Data Lake](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [Database Migration Service](#database-migration-service-limits)
* [Event Grid](#event-grid-limits)
* [Event Hubs](#event-hubs-limits)
* [Front Door Service](#azure-front-door-service-limits)
* [Identity Manager](#identity-manager-limits)
* [IoT Hub](#iot-hub-limits)
* [Servicio IoT Hub Device Provisioning](#iot-hub-device-provisioning-service-limits)
* [Key Vault](#key-vault-limits)
* [Media Services](#media-services-limits)
* [Mobile Services](#mobile-services-limits)
* [Multi-Factor Authentication](#multi-factor-authentication-limits)
* [Redes](#networking-limits)
  * [Application Gateway](#application-gateway-limits)
  * [DNS de Azure](#azure-dns-limits)
  * [Azure Front Door Service](#azure-front-door-service-limits)
  * [Azure Firewall](#azure-firewall-limits)
  * [ExpressRoute](#expressroute-limits)
  * [Equilibrador de carga](#load-balancer)
  * [Dirección IP pública](#publicip-address)
  * [Network Watcher](#network-watcher-limits)
  * [Traffic Manager](#traffic-manager-limits)
  * [Virtual Network](#networking-limits)
* [Centros de notificaciones](#notification-hubs-limits)
* [Grupos de recursos](#resource-group-limits)
* [Control de acceso basado en rol](#role-based-access-control-limits)
* [Scheduler](#scheduler-limits)
* [Service Bus](#service-bus-limits)
* [Recuperación de sitios](#site-recovery-limits)
* [SQL Database](#sql-database-limits)
* [SQL Data Warehouse](#sql-data-warehouse-limits)
* [Storage](#storage-limits)
* [Sistema de StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Suscripción](#subscription-limits)
* [Máquinas virtuales](#virtual-machines-limits)
* [Conjuntos de escalado de máquinas virtuales](#virtual-machine-scale-sets-limits)

### <a name="subscription-limits"></a>Límites de suscripción
#### <a name="subscription-limits---azure-service-management-classic-deployment-model"></a>Límites de suscripción: Azure Service Management (modelo de implementación clásico)
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### <a name="subscription-limits---azure-resource-manager"></a>Límites de suscripción - Azure Resource Manager
Los límites siguientes se aplican cuando se usa Azure Resource Manager y grupos de recursos de Azure. No se enumeran los límites que no han cambiado con Azure Resource Manager. Consulte la tabla anterior para obtener información acerca de esos límites.

Para más información sobre los límites de lectura y escritura de Resource Manager API, vea [Limitación de solicitudes de Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

### <a name="resource-group-limits"></a>Límites de los grupos de recursos
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

### <a name="virtual-machines-limits"></a>Límites de Virtual Machines
#### <a name="virtual-machines-limits"></a>Límites de Virtual Machines
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

#### <a name="virtual-machines-limits---azure-resource-manager"></a>Límites de Virtual Machines: Azure Resource Manager
Los límites siguientes se aplican cuando se usa Azure Resource Manager y grupos de recursos de Azure. No se enumeran los límites que no han cambiado con Azure Resource Manager. Consulte la tabla anterior para obtener información acerca de esos límites.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

#### <a name="shared-image-gallery-limits"></a>Límites de Shared Image Gallery

Hay límites por suscripción, para implementar los recursos con las galerías de imágenes compartidas:
- 100 galerías de imágenes compartidas por suscripción, por región
- 1000 definiciones de imágenes por suscripción, por región
- 10 000 versiones de imágenes por suscripción, por región

### <a name="virtual-machine-scale-sets-limits"></a>Límites de los conjuntos de escalado de máquinas virtuales
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

### <a name="container-instances-limits"></a>Límites de Container Instances
[!INCLUDE [container-instances-limits](../includes/container-instances-limits.md)]

### <a name="container-registry-limits"></a>Límites de Container Registry
En la tabla siguiente se detallan las características y los límites de los [niveles de servicio](./container-registry/container-registry-skus.md) Básico, Estándar y Premium.

[!INCLUDE [container-registry-limits](../includes/container-registry-limits.md)]

### <a name="azure-kubernetes-service-limits"></a>Límites de Azure Kubernetes Service
[!INCLUDE [container-service-limits](../includes/container-service-limits.md)]

### <a name="azure-machine-learning-service-limits"></a>Límites de Azure Machine Learning Service
Los valores más recientes para las cuotas de Proceso de Machine Learning pueden encontrarse en la [Página de cuotas de Azure Machine Learning](../articles/machine-learning/service/how-to-manage-quotas.md).

### <a name="networking-limits"></a>Límites de red
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### <a name="expressroute-limits"></a>Límites de ExpressRoute
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### <a name="application-gateway-limits"></a>Límites de Application Gateway

La tabla siguiente se aplica a v1, v2 y estándar y a SKU de WAF, a menos que se indique lo contrario.
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

#### <a name="network-watcher-limits"></a>Límites de Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

#### <a name="traffic-manager-limits"></a>Límites de Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### <a name="azure-dns-limits"></a>Límites de Azure DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

#### <a name="azure-firewall-limits"></a>Límites de Azure Firewall
[!INCLUDE [azure-firewall-limits](../includes/firewall-limits.md)]

#### <a name="azure-front-door-service-limits"></a>Límites de Azure Front Door Service
[!INCLUDE [azure-front-door-service-limits](../includes/front-door-limits.md)]

### <a name="storage-limits"></a>Límites de Storage
<!--like # storage accts -->
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Para más información sobre los límites de las cuentas de almacenamiento, consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](storage/common/storage-scalability-targets.md).

#### <a name="storage-resource-provider-limits"></a>Límites de proveedor de recursos de Storage

[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

#### <a name="azure-blob-storage-limits"></a>Límites de almacenamiento de blobs de Azure
[!INCLUDE [storage-blob-scale-targets](../includes/storage-blob-scale-targets.md)]

#### <a name="azure-files-limits"></a>Límites de Azure Files
Para más información sobre los límites de Azure Files, consulte [Objetivos de escalabilidad y rendimiento de Azure Files](storage/files/storage-files-scale-targets.md).

[!INCLUDE [storage-files-scale-targets](../includes/storage-files-scale-targets.md)]

#### <a name="azure-file-sync-limits"></a>Límites de Azure File Sync
[!INCLUDE [storage-sync-files-scale-targets](../includes/storage-sync-files-scale-targets.md)]

#### <a name="azure-queue-storage-limits"></a>Límites de Azure Queue Storage
[!INCLUDE [storage-queues-scale-targets](../includes/storage-queues-scale-targets.md)]

#### <a name="azure-table-storage-limits"></a>Límites de Azure Table Storage
[!INCLUDE [storage-tables-scale-targets](../includes/storage-tables-scale-targets.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
#### <a name="virtual-machine-disk-limits"></a>Límites de discos de máquinas virtuales
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Para más información, consulte [Tamaños de máquina virtual](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

#### <a name="managed-virtual-machine-disks"></a>Discos de máquinas virtuales administrados

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

#### <a name="unmanaged-virtual-machine-disks"></a>Discos de máquinas virtuales no administrados

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

### <a name="azure-cloud-services-limits"></a>Límites de Azure Cloud Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

### <a name="azure-cognitive-services-limits"></a>Límites de Azure Cognitive Services
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cognitive-services-limits.md)]

### <a name="app-service-limits"></a>Límites de App Service
Entre los siguientes límites de App Service se incluyen límites para Web Apps, Mobile Apps y API Apps.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### <a name="functions-limits"></a>Límites de Azure Functions
[!INCLUDE [functions-limits](../includes/functions-limits.md)]

### <a name="scheduler-limits"></a>Límites de Scheduler
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### <a name="batch-limits"></a>Límites de Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

### <a name="biztalk-services-limits"></a>Límites de BizTalk Services
La tabla siguiente muestra los límites de Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

### <a name="azure-cosmos-db-limits"></a>Límites de Azure Cosmos DB
Para los límites de Azure Cosmos DB, consulte [Límites de Azure Cosmos DB](cosmos-db/concepts-limits.md).

### <a name="azure-database-for-mysql"></a>Azure Database for MySQL
Para obtener más información sobre los límites de Azure Database for MySQL, consulte [Limitaciones en Azure Database for MySQL](mysql/concepts-limits.md).

### <a name="azure-database-for-postgresql"></a>Azure Database for PostgreSQL
Para obtener más información sobre los límites de Azure Database for PostgreSQL, consulte [Limitaciones en Azure Database for PostgreSQL](postgresql/concepts-limits.md).

### <a name="azure-search-limits"></a>Límites de Azure Search
Los planes de tarifa determinan la capacidad y los límites de su servicio de búsqueda. Los planes incluyen:

* **Gratis** servicio multiinquilino compartido con otros suscriptores de Azure, se ha diseñado para proyectos de evaluación y pequeños desarrollos.
* **Básico** proporciona recursos informáticos dedicados para cargas de trabajo de producción en una escala menor, con hasta tres réplicas para cargas de trabajo de consulta de alta disponibilidad.
* **Estándar** incluye S1, S2, S3 y S3 de alta densidad y es para cargas de trabajo de producción mayores. Existen varios niveles dentro del nivel Estándar para que pueda elegir una configuración de recursos que se adapte mejor al perfil de la carga de trabajo.

**Límites por suscripción**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Límites por servicio de búsqueda**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Para más información sobre otros límites, incluido el tamaño de documento, las consultas por segundo, las claves, las solicitudes y las respuestas, consulte [Límites de servicio en Azure Search](search/search-limits-quotas-capacity.md).

### <a name="media-services-limits"></a>Límites de Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### <a name="content-delivery-network-limits"></a>Límites de Content Delivery Network
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### <a name="mobile-services-limits"></a>Límites de Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### <a name="azure-monitor-limits"></a>Límites de Azure Monitor

#### <a name="alerts"></a>Alertas

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-alerts.md)]

#### <a name="action-groups"></a>Grupos de acciones

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-action-groups.md)]

#### <a name="log-queries-and-language"></a>Consultas de registro y lenguaje

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-log-queries.md)]

#### <a name="log-analytics-workspaces"></a>Áreas de trabajo de Log Analytics

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-workspaces.md)]

#### <a name="application-insights"></a>Application Insights

[!INCLUDE [monitoring-limits](../includes/azure-monitor-limits-app-insights.md)]




### <a name="notification-hubs-limits"></a>Límites de Notification Hubs
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

### <a name="event-hubs-limits"></a>Límites de Event Hubs
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

### <a name="service-bus-limits"></a>Límites de Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### <a name="iot-hub-limits"></a>Límites de IoT Hub
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### <a name="iot-hub-device-provisioning-service-limits"></a>Límites de servicio IoT Hub Device Provisioning
[!INCLUDE [azure-iotdps-limits](../includes/iot-dps-limits.md)]

### <a name="data-factory-limits"></a>Límites de Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### <a name="data-lake-analytics-limits"></a>Límites de Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### <a name="data-lake-store-limits"></a>Límites de Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

### <a name="database-migration-service-limits"></a>Límites de Database Migration Service
[!INCLUDE [database-migration-service-limits](../includes/database-migration-service-limits.md)]

### <a name="stream-analytics-limits"></a>Límites de Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### <a name="active-directory-limits"></a>Límites de Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

### <a name="event-grid-limits"></a>Límites de Event Grid
[!INCLUDE [event-grid-limits](../includes/event-grid-limits.md)]

### <a name="azure-maps-limits"></a>Límites de Azure Maps
[!INCLUDE [maps-limits](../includes/maps-limits.md)]

### <a name="azure-policy-limits"></a>Límites de Azure Policy
[!INCLUDE [policy-limits](../includes/azure-policy-limits.md)]

### <a name="storsimple-system-limits"></a>Límites del sistema StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

### <a name="backup-limits"></a>Límites de Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### <a name="azure-signalr-service-limits"></a>Límites de Azure SignalR Service
[!INCLUDE [signalr-service-limits](../includes/signalr-service-limits.md)]

### <a name="site-recovery-limits"></a>Límites de Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### <a name="api-management-limits"></a>Límites de API Management
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### <a name="azure-cache-for-redis-limits"></a>Límites de Azure Cache for Redis
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### <a name="key-vault-limits"></a>Límites de Key Vault
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### <a name="multi-factor-authentication-limits"></a>Límites de Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### <a name="automation-limits"></a>Límites de Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

### <a name="identity-manager-limits"></a>Límites de Identity Manager
[!INCLUDE [automation-limits](~/includes/managed-identity-limits.md)]

### <a name="role-based-access-control-limits"></a>Límites de control de acceso basado en rol
[!INCLUDE [role-based-access-control-limits](../includes/role-based-access-control-limits.md)]

### <a name="sql-database-limits"></a>Límites de SQL Database
Para los límites de SQL Database, consulte [Límites de recursos de SQL Database para bases de datos únicas](sql-database/sql-database-vcore-resource-limits-single-databases.md), [Límites de recursos de SQL Database para grupos elásticos y bases de datos agrupadas](sql-database/sql-database-vcore-resource-limits-elastic-pools.md) y [Límites de recursos de SQL Database para instancias administradas](sql-database/sql-database-managed-instance-resource-limits.md).

### <a name="sql-data-warehouse-limits"></a>Límites de SQL Data Warehouse
Para los límites de SQL Data Warehouse, consulte [Límites de recursos de SQL Data Warehouse](sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md).

## <a name="see-also"></a>Otras referencias
- [Understand Azure limits and increases](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) (Descripción de los límites de Azure y cómo aumentarlos)
- [Tamaños de máquina virtual y servicio en la nube de Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Tamaños para Azure Cloud Services](cloud-services/cloud-services-sizes-specs.md)
