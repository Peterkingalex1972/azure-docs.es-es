---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/06/2019
ms.author: rogarana
ms.openlocfilehash: 9f259c3e403e933c847ac56000b1db2cd594caf5
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449941"
---
| Recurso | Recursos compartidos de archivos estándar | Recursos compartidos de archivos Premium |
|----------|---------------|------------------------------------------|
| Tamaño máximo de un recurso compartido de archivos | Sin mínimo; pago por uso | 100 GiB; aprovisionado |
| Tamaño máximo de un recurso compartido de archivos | 5 TiB (disponibilidad general), 100 TiB (versión preliminar) | 100 TiB |
| Tamaño máximo de un archivo en un recurso compartido de archivos | 1 TiB | 1 TiB |
| Número máximo de archivos en un recurso compartido de archivos | Sin límite | Sin límite |
| Número máximo de IOPS por recurso compartido | 1000 IOPS (disponibilidad general), 10 000 IOPS (versión preliminar) | 100.000 IOPS |
| Número máximo de directivas de acceso almacenadas por recurso compartido de archivos | 5 | 5 |
| Rendimiento de destino para un único recurso compartido de archivos | Hasta 60 MiB/s (disponibilidad general), hasta 300 MiB/s (versión preliminar) | Consulte los valores de entrada y salida del recurso compartido de archivos Premium|
| Salida máxima para un único recurso compartido de archivos | Consulte el rendimiento de destino del recurso compartido de archivos estándar. | Hasta 6204 MiB/s |
| Entrada máxima para un único recurso compartido de archivos | Consulte el rendimiento de destino del recurso compartido de archivos estándar. | Hasta 4136 MiB/s |
| Número máximo de identificadores abiertos por archivo | 2\.000 identificadores abiertos | 2\.000 identificadores abiertos |
| Número máximo de instantáneas de recurso compartido | 200 instantáneas de recurso compartido | 200 instantáneas de recurso compartido |
| Longitud máxima del nombre de objeto (archivos y directorios) | 2048 caracteres | 2048 caracteres |
| Número máximo de componentes de la ruta de acceso (en la ruta de acceso \A\B\C\D, cada letra es un componente) | 255 caracteres | 255 caracteres |