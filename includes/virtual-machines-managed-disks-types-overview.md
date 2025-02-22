---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7385888c54d46e706621f781a64d12d3ae7aa5fb
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512697"
---
# <a name="what-disk-types-are-available-in-azure"></a>¿Qué tipos de disco están disponibles en Azure?

Actualmente, Azure Managed Disks ofrece cuatro tipos de discos. Cada uno estos tipos está pensado para escenarios de clientes específicos.

## <a name="disk-comparison"></a>Comparación de discos

La tabla siguiente proporciona una comparación entre los discos Ultra, las unidades de estado sólido (SSD) premium, los discos SSD estándar y las unidades de disco duro (HDD) estándar para Managed Disks, a fin de ayudar a decidir qué opción usar.

|   | Disco Ultra   | SSD Premium   | SSD estándar   | HDD estándar   |
|---------|---------|---------|---------|---------|
|Tipo de disco   |SSD   |SSD   |SSD   |HDD   |
|Escenario   |Cargas de trabajo con uso intensivo de E/S, como SAP HANA, bases de dato de capa superior (por ejemplo, SQL y Oracle) y otras cargas de trabajo con muchas transacciones.   |Cargas de trabajo confidenciales de producción y rendimiento   |Servidores web, aplicaciones empresariales poco utilizadas y desarrollo y pruebas   |Copia de seguridad, no crítico, acceso poco frecuente   |
|Tamaño del disco   |65 536 gibibyte (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Rendimiento máx.   |2000 MiB/s    |900 MiB/s   |750 MiB/s   |500 MiB/s   |
|IOPS máx.   |160 000    |20.000   |6,000   |2\.000   |

## <a name="ultra-disk"></a>Disco Ultra

Los discos Ultra de Azure ofrecen un alto rendimiento, un número elevado de IOPS y un almacenamiento en disco coherente y de baja latencia para máquinas virtuales IaaS de Azure. Algunas ventajas adicionales de los discos Ultra incluyen la capacidad de cambiar dinámicamente el rendimiento del disco junto con sus cargas de trabajo sin tener que reiniciar las máquinas virtuales. Además, los discos Ultra son adecuados para cargas de trabajo con grandes cantidades de datos, como SAP HANA, bases de datos de nivel superior y cargas de trabajo que admitan muchas transacciones. Los discos Ultra solo se pueden utilizar como discos de datos. Por ello, recomendamos usar los discos SSD Premium como discos de sistema operativo.

### <a name="performance"></a>Rendimiento

Cuando aprovisione un disco ultra, puede configurar de forma independiente la capacidad y el rendimiento del disco. Los discos Ultra vienen en varios tamaños fijos que van desde los 4 GiB hasta los 64 TiB y cuentan con un modelo de configuración de rendimiento flexible que le permite configurar las unidades IOPS y el rendimiento de forma independiente.

Estas son algunas funcionalidades clave de los discos Ultra:

- Capacidad de disco: intervalos de capacidad de discos Ultra desde 4 GiB hasta 64 TiB.
- IOPS de disco: los dispositivos Ultra admiten límites de IOPS de 300 IOPS/GiB y hasta un máximo de 160 K IOPS por disco. Para recuperar la tasa de unidades IOPS que aprovisionó, asegúrese de que la cantidad de IOPS de disco seleccionadas sea menor que el límite de IOPS de la máquina virtual. El valor mínimo de IOPS por disco es 2 IOPS/GiB, con una línea de base general mínima de 100 IOPS. Por ejemplo, si tuviera un disco Ultra de 4 GiB, tendrá un mínimo de 100 IOPS, en lugar de 8 IOPS.
- Rendimiento del disco: con los discos Ultra, el límite de rendimiento de un solo disco es de 256 KiB/s por cada IOPS aprovisionada, y hasta 2000 MBps como máximo por disco (donde MBps = 10^6 bytes por segundo). El rendimiento mínimo por disco es 4KiB/s por cada IOPS aprovisionada, con una línea de base total como mínima de 1 MBps.
- Los discos Ultra admiten el ajuste de los atributos de rendimiento del disco (IOPS y rendimiento) en tiempo de ejecución sin necesidad de desasociar el disco de la máquina virtual. Cuando se ha enviado una operación de cambio de tamaño del rendimiento del disco en un disco, este cambio puede tardar hasta una hora en surtir efecto.

### <a name="disk-size"></a>Tamaño del disco

|Tamaño de disco (GiB)  |Capacidad de IOPS  |Capacidad de rendimiento (MBps)  |
|---------|---------|---------|
|4     |1,200         |300         |
|8     |2,400         |600         |
|16     |4,800         |1,200         |
|32     |9600         |2\.000         |
|64     |19 200         |2\.000         |
|128     |38 400         |2\.000         |
|256     |76 800         |2\.000         |
|512     |80 000         |2\.000         |
|1024 - 65 536 (los tamaños de este intervalo aumentan en incrementos de 1 TiB)     |160 000         |2\.000         |

### <a name="ga-scope-and-limitations"></a>Ámbito y limitaciones de la disponibilidad general

Por ahora, los discos Ultra tienen limitaciones adicionales, como se indica a continuación:

- Se admiten en las regiones Este de EE. UU. 2, Sudeste Asiático y Norte de Europa, en dos zonas de disponibilidad por región.  
- Solo se podrán usar con las zonas de disponibilidad (los conjuntos de disponibilidad y las implementaciones de máquinas virtuales únicas fuera de las zonas no tendrán la capacidad de adjuntar un disco Ultra).
- Solo son compatibles con las máquinas virtuales ES/DS v3.
- Solo están disponibles como discos de datos y solo admiten el tamaño de sector físico 4k.  
- Solo pueden crearse como discos vacíos.  
- Todavía no admiten instantáneas de disco, imágenes de máquinas virtuales, conjuntos de disponibilidad, Virtual Machine Scale Sets ni Azure Disk Encryption.
- Todavía no admiten la integración con Azure Backup o Azure Site Recovery.