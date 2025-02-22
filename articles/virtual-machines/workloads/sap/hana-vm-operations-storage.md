---
title: Configuraciones de almacenamiento de máquinas virtuales de Azure en SAP HANA | Microsoft Docs
description: Recomendaciones de almacenamiento para máquinas virtuales con SAP HANA implementado en ellas.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 45d8844a34c5b7d9f36099304c1619e09d39305c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099626"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>Configuraciones de almacenamiento de máquinas virtuales de Azure en SAP HANA

Azure proporciona distintos tipos de almacenamiento adecuados para máquinas virtuales de Azure que ejecutan SAP HANA. Los tipos de almacenamiento de Azure que pueden considerarse para la lista de implementaciones de SAP HANA son: 

- Unidades de disco SSD estándar (SSD)
- Unidades de estado sólido (SSD) prémium
- [Disco Ultra](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/disks-enable-ultra-ssd) 

Para información acerca de estos tipos de disco, consulte el artículo de [Selección de un tipo de disco](https://docs.microsoft.com/azure/virtual-machines/linux/disks-types).

Azure ofrece dos métodos de implementación de discos duros virtuales en Azure Standard Storage y Azure Premium Storage. Si el escenario global lo permite, aproveche las implementaciones de [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). 

Para obtener una lista de tipos de almacenamiento y sus Acuerdos de Nivel de Servicio sobre IOPS y rendimiento del almacenamiento, revise la [documentación de Azure para Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

**Recomendación: use Azure Premium Storage junto con el Acelerador de escritura de Azure y Azure Managed Disks para la implementación**

En el mundo local, casi nunca ha tenido que preocuparse sobre los subsistemas de E/S y sus funcionalidades. El motivo era que el proveedor de las aplicaciones tenía que asegurarse de que se cumplieran los requisitos de almacenamiento mínimo para SAP HANA. Mientras compila la infraestructura de Azure por su cuenta, debe tener en cuenta algunos de esos requisitos. Algunas de las características de rendimiento mínimas que se piden comportan la necesidad de:

- Habilitar la lectura/escritura en **/hana/log** de 250 MB/s con tamaños de E/S de 1 MB
- Habilitar la actividad de lectura de al menos 400 MB/s para **/hana/data** con tamaños de E/S de 16 MB y 64 MB
- Habilitar la actividad de escritura de al menos 250 MB/s para **/hana/data** con tamaños de E/S de 16 MB y 64 MB

Debido a que la baja latencia de almacenamiento es fundamental para los sistemas DBMS, incluso estos DBMS, como SAP HANA, mantienen los datos en memoria. La ruta crítica en el almacenamiento normalmente está relacionada con las escrituras del registro de transacciones de los sistemas DBMS. Sin embargo, también las operaciones como la escritura de puntos de retorno o la carga de datos en memoria después de la recuperación tras un bloqueo pueden ser críticas. Por ello es **obligatorio** aprovechar Azure Premium Disks para los volúmenes **/hana/data** y **/hana/log**. Para lograr el rendimiento mínimo de **/hana/log** y **/hana/data** como desea SAP, tiene que compilar un RAID 0 utilizando MDADM o LVM en varios discos de Azure Premium Storage, y usar los volúmenes RAID como volúmenes de **/hana/data** y **/hana/log**. 

**Recomendación: como tamaños de franja para RAID 0 la recomendación es utilizar:**

- 64 KB o 128 KB para  **/hana/data**
- 32 KB para **/hana/log**

> [!NOTE]
> No es necesario configurar ningún nivel de redundancia con volúmenes RAID, ya que Azure Premium Storage y Azure Standard Storage mantienen tres imágenes de un disco duro virtual. El uso de un volumen RAID tiene como finalidad configurar volúmenes que proporcionen un rendimiento de E/S suficiente.

La acumulación de un número de discos duros virtuales de Azure bajo un RAID es acumulativo por parte de un IOPS y del rendimiento de almacenamiento. Por lo tanto, si coloca una RAID 0 en P30 x 3 discos de Azure Premium Storage, debe proporcionarle tres veces el IOPS y tres veces el rendimiento de almacenamiento de un solo disco P30 de Azure Premium Storage.

Estas recomendaciones de almacenamiento en caché suponen que las características de E/S para SAP HANA que se enumeran son como las siguientes:

- Apenas hay carga de trabajo de lectura en los archivos de datos de HANA. Las excepciones son las operaciones de E/S de tamaño grande después del reinicio de la instancia HANA o cuando se cargan datos en HANA. Otro caso de operaciones de E/S de lectura de mayor tamaño con archivos de datos pueden ser las copias de seguridad de base de datos de HANA. Como resultado, el almacenamiento en caché de lectura no tiene sentido ya que, en la mayoría de los casos, todos los volúmenes de archivos de datos tienen que leerse por completo.
- La escritura en los archivos de datos se produce en ráfagas según los puntos de retorno de HANA y la recuperación tras bloqueos de HANA. La escritura de los puntos de retorno es asincrónica y no bloquea las transacciones de usuario. La escritura de datos durante la recuperación tras bloqueo es esencial en el rendimiento a fin de lograr que el sistema responda de nuevo rápidamente. Sin embargo, la recuperación tras bloqueo debería constituir una situación excepcional.
- Apenas hay lecturas de los archivos de puesta al día de HANA. Las operaciones de E/S grandes al realizar copias de seguridad del registro de transacciones, la recuperación tras bloqueo o en la fase de reinicio de una instancia de HANA constituyen excepciones.  
- La carga principal en el caso del archivo de registro de puesta al día SAP HANA la constituyen las escrituras. Según la naturaleza de la carga de trabajo, puede tener operaciones de E/S de solo 4 KB o llegar a superar 1 MB. La latencia de las escrituras en relación con el registro de puesta al día SAP HANA es esencial para el rendimiento.
- Todas las operaciones de escritura se deben conservar en el disco de forma confiable.

**Recomendación: Como resultado de estos patrones de E/S observados por SAP HANA, debe establecer el almacenamiento en caché de los diferentes volúmenes con Azure Premium Storage como sigue:**

- **/hana/data**: sin almacenamiento en caché
- **/hana/log**: sin almacenamiento en caché; excepción con la serie M (vea más adelante en este documento)
- **/hana/shared**: almacenamiento en caché de lecturas


Tenga también en cuenta el rendimiento global de E/S de la máquina virtual al elegir una máquina virtual o determinar su tamaño. El rendimiento general del almacenamiento de las máquinas virtuales está documentado en el artículo [Tamaños de máquina virtual optimizada para memoria](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory).

## <a name="linux-io-scheduler-mode"></a>Modo de programador de E/S de Linux
Linux tiene varios modos diferentes de programación de E/S. Una recomendación común de los proveedores de Linux y SAP consiste en reconfigurar el modo de programador de E/S para los volúmenes de disco del modo **cfq** en el modo **noop**. Los detalles se incluyen en la [nota de SAP n.º 1984798](https://launchpad.support.sap.com/#/notes/1984787). 


## <a name="production-storage-solution-with-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Solución de almacenamiento de producción con Acelerador de escritura de Azure para máquinas virtuales de serie M de Azure
El Acelerador de escritura de Azure es una funcionalidad que se implementa exclusivamente para las máquinas virtuales de la serie M de Azure. Como el nombre indica, el propósito de la funcionalidad es mejorar la latencia de E/S de las escrituras en Azure Premium Storage. Para SAP HANA, el Acelerador de escritura se supone que debe usarse solo en el volumen **/hana/log**. Por lo tanto, **/hana/data** y **/hana/log** son volúmenes independientes y el Acelerador de escritura de Azure admite solo el volumen **/hana/log**. 

> [!IMPORTANT]
> La certificación de SAP HANA para las máquinas virtuales de serie M de Azure es exclusivamente con el Acelerador de escritura de Azure para el volumen **/hana/log**. Como resultado, las implementaciones de SAP HANA en un escenario de producción en máquinas virtuales de la serie M de Azure se espera que estén configuradas con el Acelerador de escritura de Azure para el volumen **/hana/log**.  

> [!NOTE]
> Para escenarios de producción, compruebe si un determinado tipo de máquina virtual es compatible con SAP HANA de SAP en la [documentación de SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

**Recomendación: las configuraciones recomendadas para escenarios de producción tienen el siguiente aspecto:**

| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | /hana/data | /hana/log | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 2 x P20 | 1 x P20 | 1 x P6 | 1 x P6 |1 x P30 |
| M64s | 1000 GiB | 1000 MB/s | 4 x P20 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 |2 x P30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 2 x P20 | 1 x P30 | 1 x P6 | 1 x P6 | 3 x P30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 2 x P40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P40 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 3 x P50 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 2 x P20 | 1 x P30 | 1 x P10 | 1 x P6 | 4 x P50 |

Los tipos de máquina virtual M416xx_v2 no están disponibles todavía por parte de Microsoft para el público. Compruebe si el rendimiento del almacenamiento para los diferentes volúmenes sugeridos se adapta a la carga de trabajo que se va a ejecutar. Si la carga de trabajo requiere volúmenes mayores para **/hana/data** y **/hana/log**, tiene que aumentar el número de discos duros virtuales de Azure Premium Storage. Ajustar el tamaño de un volumen con más discos duros virtuales de los que se enumeran, aumenta el IOPS y el rendimiento de E/S dentro de los límites del tipo de máquina virtual de Azure.

El Acelerador de escritura de Azure solo funciona en conjunto con [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Por lo que al menos los discos de Azure Premium Storage que forman el volumen **/hana/log** tienen que implementarse como discos administrados.

Hay límites en los discos duros virtuales de Azure Premium Storage por máquina virtual que el Acelerador de escritura de Azure puede admitir. Los límites actuales son:

- 16 VHD para una máquina virtual M128xx y M416xx
- 8 VHD para una máquina virtual M64xx y M208xx
- 4 discos duros virtuales para una máquina virtual M32xx

Puede encontrar instrucciones más detalladas sobre cómo habilitar el Acelerador de escritura de Azure en el artículo [Write Accelerator](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) (Acelerador de escritura).

En la misma documentación puede encontrar información más detallada y las restricciones para el Acelerador de escritura de Azure.

**Recomendación: debe usar el Acelerador de escritura para los discos que forman el volumen/hana/log**


## <a name="cost-conscious-azure-storage-configuration"></a>Configuración de Azure Storage con control de costo
En la tabla siguiente se muestra una configuración de tipos de máquina virtual que los clientes también usan para hospedar SAP HANA en máquinas virtuales de Azure. Puede haber algunos tipos de máquina virtual que no cumplan todos los criterios mínimos de almacenamiento para SAP HANA o no se admitan de forma oficial con SAP HANA en SAP. Pero hasta ahora parece que esas máquinas virtuales presentan un rendimiento adecuado en escenarios que no son de producción. **/hana/data** y **/hana/log** se combinan en un volumen. Como resultado, el uso del Acelerador de escritura de Azure puede ser una limitación en términos de IOPS.

> [!NOTE]
> Para escenarios compatibles con SAP, compruebe si un determinado tipo de máquina virtual es compatible con SAP HANA de SAP en la [documentación de SAP para IaaS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html).

> [!NOTE]
> Un cambio de las recomendaciones anteriores para una solución que considere los costos consiste en cambiar de los [discos HDD estándar de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) a los[discos SSD estándar de Azure](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) de mayor rendimiento y más confiables.


| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | /hana/data y /hana/log<br /> seccionado con LVM o MDAADM | /hana/shared | /root volume | /usr/sap | hana/backup |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E16v3 | 128 GB | 384 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E15 |
| E32v3 | 256 GiB | 768 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| E64v3 | 432 GiB | 1200 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| GS5 | 448 GiB | 2000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E30 |
| M32ts | 192 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M32ls | 256 GiB | 500 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 | 1 x E20 |
| M64ls | 512 GB | 1000 MB/s | 3 x P20 | 1 x E20 | 1 x E6 | 1 x E6 |1 x E30 |
| M64s | 1000 GiB | 1000 MB/s | 2 x P30 | 1 x E30 | 1 x E6 | 1 x E6 |2 x E30 |
| M64ms | 1750 GiB | 1000 MB/s | 3 x P30 | 1 x E30 | 1 x E6 | 1 x E6 | 3 x E30 |
| M128s | 2000 GiB | 2000 MB/s |3 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E40 |
| M128ms | 3800 GiB | 2000 MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 2 x E50 |
| M208s_v2 | 2850 GiB | 1000 MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 |  3 x E40 |
| M208ms_v2 | 5700 GiB | 1000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416s_v2 | 5700 GiB | 2000 MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E40 |
| M416ms_v2 | 11400 GiB | 2000 MB/s | 8 x P40 | 1 x E30 | 1 x E10 | 1 x E6 |  4 x E50 |


Los tipos de máquina virtual M416xx_v2 no están disponibles todavía por parte de Microsoft para el público. Discos recomendados para los tipos de máquinas virtuales más pequeños con un exceso de tamaño de los volúmenes de 3 x P20 con respecto a las recomendaciones de espacio según las [notas del producto sobre el almacenamiento de TDI de SAP](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html). No obstante, la elección que se muestra en la tabla se realizó para proporcionar un rendimiento del disco suficiente para SAP HANA. Si necesitar realizar cambios en el volumen **/hana/backup**, cuyo tamaño se estableció para mantener copias de seguridad que representan el doble del volumen de memoria, puede realizar los ajustes que requiera.   
Compruebe si el rendimiento del almacenamiento para los diferentes volúmenes sugeridos se adapta a la carga de trabajo que se va a ejecutar. Si la carga de trabajo requiere volúmenes mayores para **/hana/data** y **/hana/log**, tiene que aumentar el número de discos duros virtuales de Azure Premium Storage. Ajustar el tamaño de un volumen con más discos duros virtuales de los que se enumeran, aumenta el IOPS y el rendimiento de E/S dentro de los límites del tipo de máquina virtual de Azure. 

> [!NOTE]
> Las configuraciones anteriores NO se beneficiarían del [Acuerdo de nivel de servicio de máquinas virtuales de Azure para una máquina virtual única](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_6/) debido a que utilizan una combinación de Azure Premium Storage y Azure Standard Storage. Sin embargo, la selección se ha elegido para optimizar los costos. Debe elegir el almacenamiento Premium para todos los discos por encima del que aparece como Azure Standard Storage (Sxx) para que la configuración de máquina virtual sea compatible con el Acuerdo de Nivel de Servicio de máquina virtual de Azure único.


> [!NOTE]
> Las recomendaciones de configuración de discos que se han indicado se dirigen a los requisitos mínimos que SAP expresa a sus proveedores de infraestructura. En las implementaciones reales de clientes y en los escenarios de carga de trabajo, se encontraron situaciones en las que estas recomendaciones aún no proporcionaban suficientes funcionalidades. Estas podrían ser situaciones en las que un cliente necesitara una recarga más rápida de los datos después de un reinicio de HANA o en las que las configuraciones de copia de seguridad requirieran un mayor ancho de banda para el almacenamiento. Otros casos incluían **/hana/log**, en los que 5000 IOPS no fueron suficientes para la carga de trabajo específica. Por lo tanto, tome estas recomendaciones como punto de partida y adáptelas en función de los requisitos de la carga de trabajo.
>  

## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>Configuración de almacenamiento en el disco Ultra de Azure para SAP HANA
Microsoft está en proceso de lanzar un nuevo tipo de almacenamiento de Azure denominado [disco Ultra de Azure](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk). La gran diferencia entre el almacenamiento de Azure que se ofrecía hasta ahora y el disco Ultra es que las funcionalidades del disco ya no están asociadas a su tamaño. Como un cliente, para el disco Ultra puede definir estas funcionalidades:

- El tamaño de disco, de 4 GiB a 65 536 GiB
- Intervalo de 100 operaciones de E/S por segundo a 160 000 operaciones de E/S por segundo (el máximo depende también de los tipos de máquina virtual)
- Rendimiento del almacenamiento de 300 MB/s a 2000 MB por segundo

El disco Ultra ofrece la posibilidad de definir un único disco que responda a su rango de necesidades de tamaño, IOPS y rendimiento de disco. en lugar de usar administradores de volúmenes lógicos como LVM o MDADM además de Azure Premium Storage para construir los volúmenes que cumplan los requisitos de rendimiento de almacenamiento y de operaciones de E/S por segundo. Puede ejecutar una configuración combinada entre el disco Ultra y Premium Storage. Como resultado, puede limitar el uso el disco Ultra a los volúmenes /hana/data y /hana/log críticos para el rendimiento y cubrir los demás volúmenes con Azure Premium Storage.

> [!IMPORTANT]
> El disco Ultra todavía no está presente en todas las regiones de Azure y aún no es compatible con todos los tipos de máquinas virtuales. Para obtener información detallada sobre dónde está disponible el disco Ultra y qué familias de máquinas virtuales se admiten, consulte el artículo [¿Qué tipos de disco están disponibles en Azure?](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/disks-types#ultra-disk).

| SKU de la máquina virtual | RAM | Máx. E/S de VM<br /> Throughput | Volumen /hana/data | Rendimiento de E/S de /hana/data | Operaciones de E/S por segundo en /hana/data | Volumen /hana/log | Rendimiento de E/S de /hana/log | Operaciones de E/S por segundo en /hana/log |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E64s_v3 | 432 GiB | 1200 MB/s | 600 GB | 700 Mbps | 7500 | 512 GB | 500 MBps  | 2000 |
| M32ts | 192 GiB | 500 MB/s | 250 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M32ls | 256 GiB | 500 MB/s | < 300 GB | 500 MBps | 7500 | 256 GB | 500 MBps  | 2000 |
| M64ls | 512 GB | 1000 MB/s | 600 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64s | 1000 GiB | 1000 MB/s |  1200 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M64ms | 1750 GiB | 1000 MB/s | 2100 GB | 500 MBps | 7500 | 512 GB | 500 MBps  | 2000 |
| M128s | 2000 GiB | 2000 MB/s |2400 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M128ms | 3800 GiB | 2000 MB/s | 4800 GB | 1200 MBps |9000 | 512 GB | 800 MBps  | 2000 | 
| M208s_v2 | 2850 GiB | 1000 MB/s | 3500 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M208ms_v2 | 5700 GiB | 1000 MB/s | 7200 GB | 1000 MBps | 9000 | 512 GB | 500 MBps  | 2000 | 
| M416s_v2 | 5700 GiB | 2000 MB/s | 7200 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 | 
| M416ms_v2 | 11400 GiB | 2000 MB/s | 14400 GB | 1500 MBps | 9000 | 512 GB | 800 MBps  | 2000 |   

Los tipos de máquina virtual M416xx_v2 no están disponibles todavía por parte de Microsoft para el público. Los valores de la lista están diseñados como punto de partida y deben valorarse según las demandas reales. La ventaja del disco Ultra de Azure es que los valores de IOPS y de rendimiento se pueden adaptar sin necesidad de apagar la máquina virtual o detener la carga de trabajo que se aplica al sistema.   

> [!NOTE]
> Por ahora las instantáneas de almacenamiento con el disco Ultra no están disponibles. Esto bloquea el uso de las instantáneas de máquina virtual con los servicios de Azure Backup

## <a name="next-steps"></a>Pasos siguientes
Para más información, consulte:

- [Guía sobre la alta disponibilidad de SAP HANA para máquinas virtuales de Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview).