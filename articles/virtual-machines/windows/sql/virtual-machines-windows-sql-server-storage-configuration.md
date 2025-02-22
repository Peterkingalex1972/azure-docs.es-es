---
title: Configuración del almacenamiento para VM con SQL Server | Microsoft Docs
description: En este tema se describe cómo Azure configura el almacenamiento para las máquinas virtuales de SQL Server durante el aprovisionamiento (modelo de implementación de Resource Manager). También se explica cómo configurar el almacenamiento para sus máquinas virtuales de SQL Server existentes.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.assetid: 169fc765-3269-48fa-83f1-9fe3e4e40947
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/05/2017
ms.author: mathoma
ms.openlocfilehash: e28478d31a674d742870344b33eac6b84c3ae584
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123838"
---
# <a name="storage-configuration-for-sql-server-vms"></a>Configuración del almacenamiento para máquinas virtuales de SQL Server

Al configurar una imagen de máquina virtual de SQL Server en Azure, el Portal le ayuda a automatizar la configuración del almacenamiento. Esto incluye asociar el almacenamiento a la máquina virtual, hacer que el almacenamiento esté accesible para SQL Server y configurarlo para optimizarlo para sus requisitos de rendimiento específicos.

Este tema explica cómo Azure configura el almacenamiento para sus máquinas virtuales de SQL Server durante el aprovisionamiento y para las máquinas virtuales existentes. Esta configuración se basa en los [procedimientos recomendados de rendimiento](virtual-machines-windows-sql-performance.md) para máquinas virtuales de Azure en las que se ejecuta SQL Server.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="prerequisites"></a>Requisitos previos

Para usar la configuración del almacenamiento automática, la máquina virtual requiere las siguientes características:

* Aprovisionada con una [imagen de la galería de SQL Server](virtual-machines-windows-sql-server-iaas-overview.md#payasyougo).
* Usa el [modelo de implementación de Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md).
* Usa [discos SSD Premium](../disks-types.md).

## <a name="new-vms"></a>Nuevas máquinas virtuales

En las secciones siguientes se describe cómo configurar el almacenamiento para nuevas máquinas virtuales de SQL Server.

### <a name="azure-portal"></a>Portal de Azure

Al aprovisionar una máquina virtual de Azure mediante una imagen de la galería de SQL Server, puede configurar automáticamente el almacenamiento para la nueva máquina virtual. Especifique el tamaño del almacenamiento, los límites de rendimiento y el tipo de carga de trabajo. En la siguiente captura de pantalla se muestra la hoja Configuración de almacenamiento utilizada durante el aprovisionamiento de la máquina virtual de SQL.

![Configuración del almacenamiento de máquinas virtuales de SQL Server durante el aprovisionamiento](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-provisioning.png)

En función de lo que elija, Azure realiza las siguientes tareas de configuración del almacenamiento después de crear la máquina virtual:

* Crea y asocia discos SSD Premium a la máquina virtual.
* Configura los discos de datos para que sean accesibles para SQL Server.
* Configura los discos de datos en un grupo de almacenamiento en función de los requisitos de tamaño y rendimiento (IOPS y rendimiento) especificados.
* Asocia el grupo de almacenamiento a una unidad nueva en la máquina virtual.
* Optimiza esta nueva unidad en función de su tipo de carga de trabajo (almacenamiento de datos, procesamiento de transaccional o general).

Para más información sobre cómo Azure define la configuración del almacenamiento, consulte la sección [Configuración del almacenamiento](#storage-configuration). Para ver información más detallada acerca de cómo crear una máquina virtual con SQL Server en Azure Portal, consulte el [tutorial de aprovisionamiento](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="resource-manage-templates"></a>Plantillas de Resource Manager

Si utiliza las siguientes plantillas de Resource Manager, se asocian dos discos de datos premium de forma predeterminada, sin configuración del grupo de almacenamiento. Sin embargo, puede personalizar estas plantillas para cambiar el número de discos de datos premium que se asocian a la máquina virtual.

* [Creación de máquinas virtuales con Automated Backup](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autobackup)
* [Creación de máquinas virtuales con la aplicación de revisión automatizada](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-autopatching)
* [Creación de máquinas virtuales con la integración de AKV](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-sql-full-keyvault)

## <a name="existing-vms"></a>Máquinas virtuales existentes

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Para las máquinas virtuales de SQL Server existentes, puede modificar algunas opciones de configuración del almacenamiento en el Portal de Azure. Abra el [recurso máquinas virtuales SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) y seleccione **Información general**. La página SQL Server Overview (Información general de SQL Server) muestra el uso del almacenamiento actual de la máquina virtual. En este gráfico se muestran todas las unidades que existen en la máquina virtual. Para cada unidad, el espacio de almacenamiento se muestra en cuatro secciones:

* Datos SQL
* Registro de SQL
* Otros (almacenamiento que no es de SQL)
* Disponible

Para modificar la configuración de almacenamiento, seleccione **Configurar** en **Configuración**. 

![Configuración del almacenamiento para la máquina virtual de SQL Server existente](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-configuration-existing.png)

Las opciones de configuración que se ven varían en función de si ha utilizado esta característica antes. Cuando se utiliza por primera vez, puede especificar los requisitos de almacenamiento para una nueva unidad. Si ha utilizado esta característica anteriormente para crear una unidad, puede ampliar el almacenamiento de esa unidad.

### <a name="use-for-the-first-time"></a>Uso por primera vez

Si es la primera vez que usa esta característica, puede especificar los límites de tamaño y rendimiento del almacenamiento para una nueva unidad. Esta experiencia es similar a lo que vería en el tiempo de aprovisionamiento. La principal diferencia es que no se permiten especificar el tipo de carga de trabajo. Esta restricción impide que se interrumpa cualquier configuración de SQL Server existente en la máquina virtual.

Azure crea una unidad en función de sus especificaciones. En este escenario, Azure realiza las siguientes tareas de configuración del almacenamiento:

* Crea y asocia los discos de datos de almacenamiento premium a la máquina virtual.
* Configura los discos de datos para que sean accesibles para SQL Server.
* Configura los discos de datos en un grupo de almacenamiento en función de los requisitos de tamaño y rendimiento (IOPS y rendimiento) especificados.
* Asocia el grupo de almacenamiento a una unidad nueva en la máquina virtual.

Para más información sobre cómo Azure define la configuración del almacenamiento, consulte la sección [Configuración del almacenamiento](#storage-configuration).

### <a name="add-a-new-drive"></a>Incorporación de una nueva unidad

Si ya ha configurado el almacenamiento en la máquina virtual de SQL Server, al expandirlo aparecen dos nuevas opciones. La primera opción es agregar una nueva unidad, lo que puede aumentar el nivel de rendimiento de la máquina virtual.

Sin embargo, después de agregar la unidad, debe realizar una configuración manual adicional para conseguir el aumento del rendimiento.

### <a name="extend-the-drive"></a>Ampliación de la unidad

La otra opción para expandir el almacenamiento es ampliar la unidad existente. Esta opción aumenta el almacenamiento disponible para la unidad, pero no mejora el rendimiento. Con los grupos de almacenamiento, no se puede modificar el número de columnas una vez creado el grupo de almacenamiento. El número de columnas determina el número de escrituras en paralelo, que pueden distribuirse en los discos de datos. Por lo tanto, los discos de datos agregados no pueden aumentar el rendimiento. Solo pueden proporcionar más espacio de almacenamiento para los datos que se escriben. Esta limitación también significa que, al ampliar la unidad, el número de columnas determina el número mínimo de discos de datos que se pueden agregar. Por tanto, si crea un grupo de almacenamiento con cuatro discos de datos, el número de columnas también es cuatro. Cada vez que amplíe el almacenamiento, debe agregar al menos cuatro discos de datos.

![Ampliación de una unidad para una máquina virtual de SQL](./media/virtual-machines-windows-sql-storage-configuration/sql-vm-storage-extend-a-drive.png)

## <a name="storage-configuration"></a>Configuración de almacenamiento

Esta sección proporciona una referencia para los cambios en la configuración del almacenamiento que Azure realiza automáticamente durante el aprovisionamiento de la máquina virtual de SQL o la configuración en Azure Portal.

* Si ha seleccionado menos de dos TB de almacenamiento para la máquina virtual, Azure no crea ningún grupo de almacenamiento.
* Si ha seleccionado al menos dos TB de almacenamiento para la máquina virtual, Azure configura un grupo de almacenamiento. La siguiente sección de este tema proporciona los detalles de la configuración del grupo de almacenamiento.
* La configuración automática del almacenamiento siempre utiliza discos de datos P30 de [discos SSD Premium](../disks-types.md). En consecuencia, hay una asignación 1:1 entre el número de terabytes seleccionado y el número de discos de datos asociados a la máquina virtual.

Para más información, consulte la página [Storage pricing](https://azure.microsoft.com/pricing/details/storage) (Precios de almacenamiento) en la pestaña **Almacenamiento en disco** .

### <a name="creation-of-the-storage-pool"></a>Creación del grupo de almacenamiento

Azure usa la siguiente configuración para crear el grupo de almacenamiento en máquinas virtuales de SQL Server.

| Configuración | Valor |
| --- | --- |
| Stripe size (Tamaño de las franjas) |256 KB (almacenamiento de datos); 64 KB (transaccional) |
| Tamaños de disco |1 TB cada uno |
| Memoria caché |Lectura |
| Tamaño de la asignación |Tamaño de la unidad de asignación NTFS = 64 KB |
| Inicialización de archivo instantáneo |habilitado |
| Bloquear páginas en memoria |habilitado |
| Recuperación |Recuperación simple (sin resistencia) |
| Número de columnas |Número de discos de datos<sup>1</sup> |
| TempDB location (Ubicación de TempDB) |Almacenada en discos de datos<sup>2</sup> |

<sup>1</sup> después de crear el grupo de almacenamiento, no puede modificar el número de columnas en el grupo de almacenamiento.

<sup>2</sup> esta configuración solo se aplica a la primera unidad que se crea con la característica de configuración del almacenamiento.

## <a name="workload-optimization-settings"></a>Configuración de optimización de la carga de trabajo

En la tabla siguiente se describen las opciones de tres tipos de carga de trabajo disponibles y sus optimizaciones correspondientes:

| Tipo de carga de trabajo | DESCRIPCIÓN | Optimizaciones |
| --- | --- | --- |
| **General** |Configuración predeterminada que admite la mayoría de las cargas de trabajo |None |
| **Procesamiento transaccional** |Optimiza el almacenamiento para las cargas de trabajo OLTP de bases de datos tradicionales. |Marca de seguimiento 1117<br/>Marca de seguimiento 1118 |
| **Almacenamiento de datos** |Optimiza el almacenamiento para las cargas de trabajo de informes y análisis. |Marca de seguimiento 610<br/>Marca de seguimiento 1117 |

> [!NOTE]
> Solo puede especificar el tipo de carga de trabajo cuando se aprovisiona una máquina virtual de SQL; para ello, selecciónelo en el paso de configuración del almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

Para ver otros temas sobre la ejecución de SQL Server en Azure Virtual Machines, consulte [SQL Server en Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md).
