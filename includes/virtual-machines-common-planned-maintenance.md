---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: shants123
ms.service: virtual-machines
ms.topic: include
ms.date: 4/30/2019
ms.author: shants
ms.custom: include file
ms.openlocfilehash: c2931fa410cf92755a5df5b7129dcf93de900930
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186295"
---
Azure actualiza periódicamente su plataforma para mejorar la confiabilidad, el rendimiento y la seguridad de la infraestructura de host para las máquinas virtuales. El objetivo de estas actualizaciones va desde la aplicación de revisiones a componentes de software en el entorno de hospedaje hasta la actualización de los componentes de red o la retirada de hardware. 

Las actualizaciones raramente afectan a las máquinas virtuales hospedadas. Cuando las actualizaciones tienen algún efecto, Azure elige el método menos agresivo en las actualizaciones:

- Si la actualización no requiere un reinicio, la máquina virtual se pone en pausa mientras se actualiza el host, o bien se migra directamente a un host ya actualizado.

- Si el mantenimiento requiere un reinicio, se notifica al usuario el mantenimiento planeado. Asimismo, Azure proporciona un período de tiempo en el que puede iniciar el mantenimiento a la hora que le sea más conveniente. La ventana de mantenimiento de autoservicio normalmente es de 30 días, a menos que el mantenimiento sea urgente. Azure está invirtiendo en tecnologías para reducir los casos en los que el mantenimiento planificado de la plataforma requiere el reinicio de la máquina virtual. 

En esta página se describe cómo Azure realiza ambos tipos de mantenimiento. Para obtener más información sobre eventos no planeados (interrupciones), vea  [Administración de la disponibilidad de las máquinas virtuales para Windows](../articles/virtual-machines/windows/manage-availability.md) o el artículo correspondiente para [Linux](../articles/virtual-machines/linux/manage-availability.md).

Dentro de una máquina virtual, puede obtener una notificación sobre el próximo mantenimiento si [usa Scheduled Events para Windows](../articles/virtual-machines/windows/scheduled-events.md) o para [Linux](../articles/virtual-machines/linux/scheduled-events.md).

Para obtener instrucciones sobre la administración del mantenimiento planeado, vea [Control de las notificaciones de mantenimiento planeado para Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o el artículo correspondiente para [Windows](../articles/virtual-machines/windows/maintenance-notifications.md).

## <a name="maintenance-that-doesnt-require-a-reboot"></a>Mantenimiento que no requiere un reinicio

Como se ha indicado anteriormente, la mayoría de las actualizaciones de plataforma no afectan a las máquinas virtuales del cliente. Cuando no es posible realizar una actualización sin causar impacto, Azure elige el mecanismo de actualización con menor impacto en las máquinas virtuales del cliente. 

La mayor parte del mantenimiento sin impacto pone en pausa la máquina virtual durante menos de 10 segundos. En algunos casos, Azure usa mecanismos de mantenimiento de conservación de memoria. Estos mecanismos ponen en pausa la máquina virtual durante un máximo de 30 segundos y conservan la memoria en la RAM. La máquina virtual se reanuda y su reloj se sincroniza automáticamente. 

El mantenimiento de conservación de memoria funciona para más del 90 por ciento de las máquinas virtuales de Azure. No funciona para las series H, M, N y G. Azure usa cada vez más las tecnologías de migración en vivo y mejora el mecanismo de mantenimiento que conserva la memoria para así reducir la duración de la pausa.  

Estas operaciones de mantenimiento que no requieren un reinicio se aplican por dominio de error cada vez. También se detienen si reciben señales de estado de advertencia. 

Estos tipos de actualizaciones pueden afectar a algunas aplicaciones. Cuando la máquina virtual se migra en vivo a otro host, es posible que algunas cargas de trabajo sensibles sufran una pequeña degradación del rendimiento en los pocos minutos anteriores a la pausa de la máquina virtual. Para preparar el mantenimiento de la máquina virtual y reducir el impacto durante el mantenimiento de Azure, intente [usar Scheduled Events para Windows](../articles/virtual-machines/windows/scheduled-events.md) o [Linux](../articles/virtual-machines/linux/scheduled-events.md) para estas aplicaciones. Azure está trabajando en características de control de mantenimiento para estas aplicaciones sensibles. 

### <a name="live-migration"></a>Migración en vivo

La migración en vivo es una operación que no requiere un reinicio y que conserva la memoria para la máquina virtual. Provoca una pausa o inmovilización, y normalmente no dura más de 5 segundos. Excepto para las series G, M, N y H, todas las máquinas virtuales de infraestructura como servicio (IaaS) son aptas para la migración en vivo. Las máquinas virtuales aptas representan más del 90 por ciento de las máquinas virtuales IaaS que se implementan en la flota de Azure. 

La plataforma Azure inicia la migración en vivo en los escenarios siguientes:
- Mantenimiento planeado
- Error de hardware
- Optimizaciones de asignación

Algunos escenarios de mantenimiento planeado usan la migración en vivo, y se puede usar Scheduled Events para saber de antemano cuando se iniciarán las operaciones de migración en vivo.

La migración en vivo también se puede usar para mover máquinas virtuales cuando los algoritmos de Azure Machine Learning predicen un error inminente de hardware o cuando se quieran optimizar las asignaciones de máquina virtual. Para obtener más información sobre el modelado predictivo que detecta las instancias de hardware degradado, vea [Improving Azure VM resiliency with predictive machine learning and live migration](https://azure.microsoft.com/blog/improving-azure-virtual-machine-resiliency-with-predictive-ml-and-live-migration/?WT.mc_id=thomasmaurer-blog-thmaure) (Mejora de la resistencia de las máquinas virtuales de Azure con la migración en vivo y el aprendizaje automático predictivo). Las notificaciones de migración en vivo aparecen en los registros de Monitor y Service Health de Azure Portal, así como en Scheduled Events si utiliza estos servicios.

## <a name="maintenance-that-requires-a-reboot"></a>Mantenimiento que requiere un reinicio

En el raro caso en que las máquinas virtuales deban reiniciarse para realizar el mantenimiento planeado, recibirá una notificación de antemano. El mantenimiento planeado tiene dos fases: la fase de autoservicio y una fase de mantenimiento programado.

Durante la *fase de autoservicio*, que normalmente tarda cuatro semanas, se inicia el mantenimiento en las máquinas virtuales. Como parte del autoservicio, puede consultar cada máquina virtual para ver su estado y el resultado de la última solicitud de mantenimiento.

Cuando inicia el mantenimiento de autoservicio, su máquina virtual se vuelve a implementar en un nodo ya actualizado. Como la máquina virtual se reinicia, se pierde el disco temporal y se actualizan las direcciones IP dinámicas asociadas con la interfaz de red virtual.

Si surge un error durante el mantenimiento de autoservicio, la operación se detiene, la máquina virtual no se actualiza y se le ofrece la posibilidad de reintentar el mantenimiento de autoservicio. 

Cuando finaliza la fase de autoservicio, comienza la *fase de mantenimiento programado*. Durante esta fase, puede seguir realizando consultas dentro de la fase de mantenimiento, pero no podrá iniciar el mantenimiento usted mismo.

Para obtener más información sobre la administración del mantenimiento que requiere un reinicio, vea [Control de las notificaciones de mantenimiento planeado para Linux](../articles/virtual-machines/linux/maintenance-notifications.md) o el artículo correspondiente para [Windows](../articles/virtual-machines/windows/maintenance-notifications.md). 

### <a name="availability-considerations-during-scheduled-maintenance"></a>Consideraciones sobre disponibilidad durante el mantenimiento programado 

Si decide esperar a la fase de mantenimiento programado, hay algunas cosas que debe tener en cuenta para mantener la máxima disponibilidad de las máquinas virtuales. 

#### <a name="paired-regions"></a>Regiones emparejadas

Cada región de Azure se empareja con otra región de la misma proximidad geográfica. Juntas, forman un par de regiones. Durante la fase de mantenimiento programado, Azure solo actualiza las máquinas virtuales en una sola región perteneciente a un par de regiones. Por ejemplo, al actualizar las máquinas virtuales de la zona Centro-norte de EE. UU., Azure no actualizará las máquinas virtuales de Centro-sur de EE. UU. al mismo tiempo. Sin embargo, otras regiones como Europa del Norte pueden estar en mantenimiento al mismo tiempo que el Este de EE. UU. Comprender cómo funcionan los pares de regiones puede ayudar a distribuir mejor las máquinas virtuales entre regiones. Para más información, consulte [Regiones de Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

#### <a name="availability-sets-and-scale-sets"></a>Conjuntos de disponibilidad y conjuntos de escalado

Al implementar una carga de trabajo en máquinas virtuales de Azure, puede crear las máquinas virtuales dentro de un *conjunto de disponibilidad* para proporcionar alta disponibilidad a la aplicación. Mediante los conjuntos de disponibilidad, puede asegurarse de que durante una interrupción o en los eventos de mantenimiento que requieren un reinicio, al menos una máquina virtual estará disponible.

En un conjunto de disponibilidad, las máquinas virtuales individuales se reparten entre 20 dominios de actualización (UD) como máximo. Durante el mantenimiento planeado, solo un dominio de actualización está disponible en un momento determinado. Los dominios de actualización no necesariamente se actualizan de forma secuencial. 

Los *conjuntos de escalado* de máquinas virtuales son un recurso informático de Azure que se puede usar para implementar y administrar un conjunto de máquinas virtuales idénticas como un recurso único. El conjunto de escalado se implementa automáticamente entre dominios de actualización, como las máquinas virtuales de un conjunto de disponibilidad. Igual que sucede con los conjuntos de disponibilidad, cuando se usan conjuntos de escalado, solo un dominio de actualización se actualiza en un momento determinado durante el mantenimiento programado.

Para obtener más información sobre la configuración de las máquinas virtuales para la alta disponibilidad, vea  [Administración de la disponibilidad de las máquinas virtuales para Windows](../articles/virtual-machines/windows/manage-availability.md) o el artículo correspondiente para [Linux](../articles/virtual-machines/linux/manage-availability.md).
