---
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/18/2019
ms.author: rogarana
ms.openlocfilehash: 8b25d2395811a2197aff6d653c5038a4380021e9
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68669840"
---
Puede asociar un número de discos de datos a una máquina virtual de Azure. Según los objetivos de escalabilidad y rendimiento de los discos de datos de una máquina virtual, puede determinar el número y el tipo de disco necesarios para satisfacer sus requisitos de capacidad y rendimiento.

> [!IMPORTANT]
> Para obtener un rendimiento óptimo, limite el número de discos muy usados que se conectan a la máquina virtual para evitar una posible limitación. Si todos los discos asociados no se usan mucho al mismo tiempo, la máquina virtual puede admitir un mayor número de discos.

**Para discos administrados de Azure:**

En la tabla siguiente se muestran los límites predeterminado y máximo del número de recursos por región y suscripción. No hay ningún límite en el número de discos administrados, instantáneas e imágenes por grupo de recursos.  

> | Recurso | Límite predeterminado  | Límite máximo |
> | --- | --- | --- |
> | Discos administrados estándar | 50.000 | 50.000 |
> | Discos administrados SSD estándar | 50.000 | 50.000 |
> | Discos administrados Premium | 50.000 | 50.000 |
> | Instantáneas Standard_LRS | 50.000 | 50.000 |
> | Instantáneas Standard_ZRS | 50.000 | 50.000 |
> | Imagen administrada | 50.000 | 50.000 |

* **Para cuentas de almacenamiento estándar:** una cuenta de almacenamiento estándar tiene una tasa de solicitudes máxima total de 20 000 IOPS. El número total de IOPS en todos los discos de máquina virtual de una cuenta de almacenamiento estándar no debe superar este límite.
  
    Puede calcular aproximadamente el número de discos muy usados que admite una sola cuenta de almacenamiento estándar en función del límite de tasa de solicitudes. Por ejemplo, en el caso de una máquina virtual de nivel Básico, el número máximo de discos muy usados está alrededor de 66, que equivale a 20 000/300 IOPS por disco. El número máximo de discos muy usados para una máquina virtual de nivel Estándar es de aproximadamente 40, que equivale a 20 000/500 IOPS por disco. 

* **Para cuentas de almacenamiento premium:** una cuenta de almacenamiento premium tiene una capacidad total máxima de proceso de 50 Gbps. La capacidad total de proceso en todos los discos de la máquina virtual no debe superar este límite.

