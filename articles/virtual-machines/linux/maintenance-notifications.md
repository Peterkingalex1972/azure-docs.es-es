---
title: Control de las notificaciones de mantenimiento de máquinas virtuales Linux en Azure | Microsoft Docs
description: Ver las notificaciones de mantenimiento de máquinas virtuales Linux que se ejecutan en Azure e iniciar el mantenimiento de autoservicio.
services: virtual-machines-linux
documentationcenter: ''
author: shants123
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/30/2019
ms.author: shants
ms.openlocfilehash: c82bacfdff1c9d939016b48735f7917e7d34b47f
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67849676"
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>Control de las notificaciones de mantenimiento planeado de máquinas virtuales Linux

Azure realiza periódicamente actualizaciones para mejorar la confiabilidad, el rendimiento y la seguridad de la infraestructura host de las máquinas virtuales. Las actualizaciones son cambios como la aplicación de revisiones en el entorno de hospedaje o la actualización y la retirada de hardware. La mayoría de estas actualizaciones se realizan sin afectar a las máquinas virtuales hospedadas. Sin embargo, hay casos en los que las actualizaciones tienen un impacto:

- Si el mantenimiento no requiere un reinicio, Azure usa la migración en contexto para pausar la máquina virtual mientras se actualiza el host. Estas operaciones de mantenimiento sin reinicialización se aplican dominio por dominio, si tienen errores, y el progreso se detiene si se recibe alguna señal de estado de advertencia.

- Si el mantenimiento requiere un reinicio, el usuario recibe un aviso de cuándo está programado el mantenimiento. En estos casos, se le asigna también un período de tiempo (normalmente 30 días) donde puede iniciar el mantenimiento a la hora que le resulte más cómodo.


El mantenimiento planeado que requiere un reinicio se programa en olas. Cada ola tiene un ámbito diferente (regiones).

- Una ola comienza con una notificación a los clientes. De forma predeterminada, la notificación se envía al propietario de la suscripción y a los copropietarios. Con las [alertas del registro de actividad](../../azure-monitor/platform/activity-logs-overview.md) de Azure es posible agregar más destinatarios y opciones de mensajería, como el correo electrónico, los SMS y los webhooks, a las notificaciones.  
- En el momento de la notificación, aparece una *ventana de autoservicio*. Durante esta ventana, que suele ser de 30 días, puede ver qué máquinas virtuales se incluyen en esta ola e iniciar de forma proactiva el mantenimiento en función de las necesidades que dicte su programación.
- Después de la ventana de autoservicio, comienza una *ventana de mantenimiento programado*. Mientras está ventana está activa Azure programa el mantenimiento necesario y lo aplica a la máquina virtual. 

El objetivo de tener dos ventanas es proporcionar tiempo suficiente para que pueda iniciar el mantenimiento y reiniciar la máquina virtual sabiendo cuando iniciará Azure el mantenimiento automáticamente.


Puede usar Azure Portal, PowerShell, la API de REST y la CLI para consultar las ventanas de mantenimiento de las máquinas virtuales e iniciar el mantenimiento de autoservicio.

 
## <a name="should-you-start-maintenance-using-during-the-self-service-window"></a>¿Debe comenzar el mantenimiento durante la ventana de autoservicio?  

Las directrices siguientes están pensadas para ayudarle a decidir si debe utilizar esta funcionalidad e iniciar el mantenimiento a su ritmo. 

> [!NOTE] 
> Es posible que el mantenimiento de autoservicio no esté disponible para todas las máquinas virtuales. Para determinar si su máquina virtual se puede volver a implementar de forma proactiva, busque **Iniciar ahora** en el estado del mantenimiento. El mantenimiento de autoservicio no está disponible para Cloud Services (rol de trabajo o web) y Service Fabric.


El mantenimiento de autoservicio no se recomienda para implementaciones que usen **conjuntos de disponibilidad**, ya que éstas son configuraciones de alta disponibilidad, en las que en un momento dado solo resulta afectado un dominio de actualización. 
- Deje que Azure desencadene el mantenimiento. Para el mantenimiento que requiera el reinicio, tenga en cuenta que el mantenimiento se realizará dominio de actualización por dominio de actualización, que los dominios de actualización no reciben necesariamente el mantenimiento de forma secuencial y que hay una pausa de 30 minutos entre los dominios de actualización. 
- Si una pérdida temporal de una parte de su capacidad (1/recuento de dominio de actualización) supone un problema, se puede compensar fácilmente mediante la asignación de más instancias durante el período de mantenimiento. 
- Para el mantenimiento que no requiere el reinicio, las actualizaciones se aplican en el nivel de dominio de error. 

**No** utilice el mantenimiento de autoservicio en los escenarios siguientes: 
- Si apaga las máquinas virtuales con frecuencia, ya sea manualmente, mediante DevTest Labs, con el apagado automático o de forma programada, puede revertir el estado de mantenimiento y, por tanto, provocar mayor tiempo de inactividad.
- En las máquinas virtuales de corta duración que sepa que se van a eliminar antes del final del mantenimiento. 
- En el caso de las cargas de trabajo con un estado grande almacenadas en el disco local (efímero) en el que se desea realizar el mantenimiento tras la actualización. 
- En los casos en los que se cambia el tamaño de una máquina virtual con frecuencia, ya que pudo revertir el estado de mantenimiento. 
- Si ha realizado eventos programados que permiten una conmutación por error proactiva o un apagado ordenado de la carga de trabajo, 15 minutos antes de que comience el apagado de mantenimiento

**Use** el mantenimiento de autoservicio si tiene planeado ejecutar la máquina virtual de forma ininterrumpida durante la fase de mantenimiento programado y no se produce ninguna de las contraindicaciones anteriores. 

Se recomienda usar el mantenimiento de autoservicio en los siguientes casos:
- Es preciso comunicar una ventana de mantenimiento exacta a la dirección o al cliente final. 
- Es preciso completar el mantenimiento en una fecha determinada. 
- Es preciso controlar la secuencia de mantenimiento, por ejemplo, la aplicación de niveles múltiples para garantizar la recuperación segura.
- Se necesitan más de 30 minutos de recuperación de la máquina virtual entre dos dominios de actualización (UD). Para controlar el tiempo entre los dominios de actualización, no puede desencadenar el mantenimiento en las máquinas virtuales en más de un dominio de actualización (UD) a la vez.



## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>Visualización de las máquinas virtuales programadas para mantenimiento mediante la CLI

Se puede ver la información de mantenimiento planeado con [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest).
 
La información de mantenimiento se devuelve solo si hay mantenimiento planeado. Si no hay ningún mantenimiento programado que afecte a la máquina virtual, el comando no devuelve ninguna información de mantenimiento. 

```azure-cli
az vm get-instance-view -g rgName -n vmName
```

Los siguientes valores se devuelven en MaintenanceRedeployStatus: 

| Valor | DESCRIPCIÓN   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica si puede iniciar el mantenimiento en la máquina virtual en este momento |
| PreMaintenanceWindowStartTime         | El comienzo de la ventana de autoservicio de mantenimiento en la que puede iniciar el mantenimiento en la máquina virtual |
| PreMaintenanceWindowEndTime           | El final de la ventana de autoservicio de mantenimiento en la que puede iniciar el mantenimiento en la máquina virtual |
| MaintenanceWindowStartTime            | El comienzo de la ventana de mantenimiento programado en la que Azure inicia el mantenimiento en la máquina virtual |
| MaintenanceWindowEndTime              | El final de la ventana de mantenimiento programado en la que Azure inicia el mantenimiento de la máquina virtual |
| LastOperationResultCode               | El resultado del último intento de iniciar el mantenimiento en la máquina virtual |




## <a name="start-maintenance-on-your-vm-using-cli"></a>Inicio del mantenimiento en la máquina virtual mediante la CLI

La siguiente llamada iniciará el mantenimiento en una máquina virtual si `IsCustomerInitiatedMaintenanceAllowed` está establecido en true.

```azure-cli
az vm perform-maintenance -g rgName -n vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="classic-deployments"></a>Implementaciones clásicas

Si todavía tiene máquinas virtuales heredadas que se implementaron según el modelo de implementación clásica, puede usar la CLI de Azure clásica para consultar las máquinas virtuales e iniciar el mantenimiento.

Asegúrese de que se encuentra en el modo correcto para trabajar con máquinas virtuales clásicas. Para ello, escriba lo siguiente:

```
azure config mode asm
```

Para obtener el estado de mantenimiento de una máquina virtual denominada *myVM*, escriba lo siguiente:

```
azure vm show myVM 
``` 

Para iniciar el mantenimiento en una máquina virtual clásica denominada *myVM* en el servicio *myService* y la implementación *myDeployment*, escriba lo siguiente:

```
azure compute virtual-machine initiate-maintenance --service-name myService --name myDeployment --virtual-machine-name myVM
```


## <a name="faq"></a>Preguntas más frecuentes


**P: ¿Por qué hay que reiniciar las máquinas virtuales ahora?**

**R:** Aunque la mayoría de las actualizaciones de la plataforma de Azure no afectan a la disponibilidad de la máquina virtual, hay casos en los que es imposible evitar reiniciar las máquinas virtuales hospedadas en Azure. Hemos acumulado varios cambios que requieren el reinicio de nuestros servidores, lo que conllevará el reinicio de las máquinas virtuales.

**P: Si sigo las recomendaciones para lograr una alta disponibilidad mediante un conjunto de disponibilidad, ¿estoy seguro?**

**R:** Las máquinas virtuales implementadas en un conjunto de disponibilidad o en conjuntos de escalado de máquinas virtuales tienen la noción de dominios de actualización (UD). Al realizar mantenimiento, Azure respeta la restricción de UD, por lo que no reiniciará máquinas virtuales de diferentes UD (del mismo conjunto de disponibilidad).  Azure también espera al menos 30 minutos antes de pasar al siguiente grupo de máquinas virtuales. 

Para más información sobre la alta disponibilidad, consulte [Availability for virtual machines in Azure](availability.MD) (Disponibilidad para máquinas virtuales en Azure).

**P: ¿Cómo recibo notificaciones acerca del mantenimiento planeado?**

**R:** Una oleada de mantenimiento planeado se inicia mediante el establecimiento de una programación en una o varias regiones de Azure. Poco después, se envía una notificación por correo electrónico a los propietarios de las suscripciones (un correo electrónico por suscripción). Mediante Alertas de registro de actividad se pueden configurar más canales y destinatarios de esta notificación. En caso de que implemente una máquina virtual en una región en la que ya se ha programado un mantenimiento planeado, no recibirá la notificación, sino que tendrá que comprobar el estado de mantenimiento de la máquina virtual.

**P: No veo ninguna indicación de mantenimiento planeado en el portal, Powershell o la CLI. ¿Qué está pasando?**

**R:** La información relacionada con el mantenimiento planeado está disponible durante una tanda de mantenimiento planeado solo para las máquinas virtuales que van a verse afectadas por el mismo. En otras palabras, si no ve datos, es posible que la oleada de mantenimiento se haya completado (o que no haya empezado) o que la máquina virtual ya esté alojada en un servidor actualizado.

**P: ¿Existe alguna una manera de saber exactamente cuándo se verá afectada mi máquina virtual?**

**R:** Al establecer la programación, definimos una ventana de tiempo de varios días. Sin embargo, se desconoce la secuencia exacta de los servidores (y las máquinas virtuales) en esta ventana. Los clientes que deseen conocer el tiempo exacto para sus máquinas virtuales pueden usar [eventos programados](scheduled-events.md) y realizar consultas desde la propia máquina virtual y recibir una notificación 15 minutos antes de que una máquina virtual se reinicie.

**P: ¿Cuánto tiempo tardará en reiniciar mi máquina virtual?**

**R:**  En función del tamaño de la máquina virtual, el reinicio podría tardar varios minutos durante la ventana de mantenimiento de autoservicio. En los reinicios ejecutados por Azure en la ventana de mantenimiento programada, el reinicio suele tardar unos 25 minutos. Tenga en cuenta que, si usa Cloud Services (roles de trabajo o web), Virtual Machine Scale Sets o conjuntos de disponibilidad, se le proporcionarán treinta minutos entre cada grupo de máquinas virtuales (UD) durante la ventana de mantenimiento programado.

**P: ¿Cuál es la experiencia en el caso de los conjuntos de escalado de máquinas virtuales?**

**R:** El mantenimiento planeado está ahora disponible para los conjuntos de escalado de máquinas virtuales. Para instrucciones sobre cómo iniciar el mantenimiento de autoservicio, consulte el documento [Notificaciones de mantenimiento planeado de conjuntos de escalado de máquinas virtuales](../../virtual-machine-scale-sets/virtual-machine-scale-sets-maintenance-notifications.md).

**P: ¿Cómo es la experiencia en el caso de Cloud Services (roles de trabajo o web) y Service Fabric?**

**R:** Aunque estas plataformas resultan afectadas por el mantenimiento planeado, se considera que los clientes que las usan están seguros, ya que solo resultarán afectadas en cualquier momento las máquinas virtuales de un dominio de actualización (UD) individual. El mantenimiento de autoservicio no está disponible para Cloud Services (rol de trabajo o web) y Service Fabric.

**P: No veo información de mantenimiento en mis máquinas virtuales. ¿A qué se debe?**

**R:** Hay varios motivos por los que no ve información de mantenimiento en sus máquinas virtuales:
1.  Utiliza una suscripción marcada como interna de Microsoft.
2.  Las máquinas virtuales no están programadas para su mantenimiento. Es posible que la tanda de mantenimiento haya finalizado, se haya cancelado o se haya modificado para que las máquinas virtuales dejen de verse afectadas por ella.
3.  La columna **Mantenimiento** no se ha agregado a la vista de la lista de máquinas virtuales. Aunque hemos agregado esta columna a la vista predeterminada, los clientes que se han configurado para ver las columnas no predeterminadas deben agregar manualmente la columna de **mantenimiento** a la vista de lista de máquinas virtuales.

**P: Está programado que se realice un segundo mantenimiento programado de mi máquina virtual. ¿Por qué?**

**R:** Hay varios casos en los que verá que el mantenimiento de su máquina virtual está programado después de que se haya completado el ciclo de mantenimiento y reimplementación:
1.  Hemos cancelado la oleada de mantenimiento y la hemos reiniciado con otra carga útil. Es posible que hayamos detectado una carga útil con errores y que necesitemos implementar una carga adicional.
2.  El *servicio de una máquina virtual se ha reparado* en otro nodo debido a un error de hardware.
3.  Ha seleccionado detener (desasignar) y reiniciar la máquina virtual.
4.  El **apagado automático** se ha activado en la máquina virtual.


## <a name="next-steps"></a>Pasos siguientes

Obtenga información acerca de cómo puede registrarse para eventos de mantenimiento desde la máquina virtual con [Eventos programados](scheduled-events.md).
