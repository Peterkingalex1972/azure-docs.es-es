---
title: 'Comprobación de errores de grupo y de nodo: Azure Batch'
description: Errores que se deben comprobar y cómo evitarlos al crear grupos y nodos
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 9481263773cc919fecacce80191cf209ec2a1282
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359247"
---
# <a name="check-for-pool-and-node-errors"></a>Comprobación de errores de grupo y de nodo

Al crear y administrar grupos de Azure Batch, algunas operaciones se realizan de inmediato. Pero otras son asincrónicas y se ejecutan en segundo plano, lo cual tarda varios minutos en completarse.

Detectar errores de las operaciones que tienen lugar de inmediato es sencillo, porque la API, la CLI o la interfaz de usuario devuelven los errores al instante.

En este artículo se tratan las operaciones en segundo plano que pueden realizarse para grupos y nodos de grupo. Se especifica cómo puede detectar y evitar errores.

## <a name="pool-errors"></a>Errores de grupo

### <a name="resize-timeout-or-failure"></a>Cambiar el tamaño del tiempo de espera o error

Cuando crea un nuevo grupo o cambia el tamaño de uno existente, especifica el número de nodos de destino.  La operación de creación o de cambio de tamaño se completa inmediatamente, pero la asignación real de nuevos nodos o la eliminación de nodos existentes podrían tardar varios minutos.  El tiempo de expiración de cambio de tamaño se especifica en la API [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) o [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize). Si Batch no puede obtener el número de nodos de destino durante el tiempo de espera de redimensionamiento, el grupo entra en un estado estable e informa de los errores de redimensionamiento.

La propiedad [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) de la evaluación más reciente indica los errores producidos.

Las causas comunes de los errores de cambio de tamaño son:

- El tiempo de espera del cambio de tamaño es demasiado corto
  - En la mayoría de las circunstancias, el tiempo de expiración predeterminado de 15 minutos es suficiente para asignar o quitar nodos del grupo.
  - Si asigna un gran número de nodos, se recomienda establecer el tiempo de expiración de cambio de tamaño en 30 minutos. Por ejemplo, cuando cambie el tamaño a más de 1 000 nodos de una imagen de Azure Marketplace o a más de 300 nodos de una imagen de máquina virtual personalizada.
- Cuota de núcleos insuficiente
  - Una cuenta de Batch tiene un número límite de núcleos que puede asignar en todos los grupos. Batch dejará de asignar nodos cuando se alcance esa cuota. [Puede aumentar](https://docs.microsoft.com/azure/batch/batch-quota-limit) la cuota de núcleos para que Batch pueda asignar más nodos.
- Direcciones IP de subred insuficientes cuando un [grupo está en una red virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Una subred de red virtual debe tener suficientes direcciones IP sin asignar que pueda asignar a cada nodo del grupo solicitado. En caso contrario, los nodos no se pueden crear.
- Recursos insuficientes cuando un [grupo está en una red virtual](https://docs.microsoft.com/azure/batch/batch-virtual-network)
  - Puede crear recursos, como equilibradores de carga, direcciones IP públicas y grupos de seguridad de red, en la misma suscripción que la cuenta de Batch. Compruebe que las cuotas de suscripción sean suficientes para estos recursos.
- Grupos de gran tamaño con imágenes de máquina virtual personalizadas
  - Los grupos grandes que usan imágenes de máquina virtual personalizadas pueden tardar más tiempo en asignarse y pueden producirse tiempos de expiración de cambio de tamaño.  Consulte [Uso de una imagen personalizada para crear un grupo de máquinas virtuales](https://docs.microsoft.com/azure/batch/batch-custom-images) para las recomendaciones sobre los límites y la configuración.

### <a name="automatic-scaling-failures"></a>Errores de escalado automático

También puede establecer Azure Batch para escalar automáticamente el número de nodos de un grupo. Se definen los parámetros para la [fórmula de escalado automático para un grupo](https://docs.microsoft.com/azure/batch/batch-automatic-scaling). El servicio Batch usa la fórmula para evaluar periódicamente el número de nodos del grupo y establecer un nuevo número de destino. Pueden producirse los siguientes tipos de problemas:

- Error de la evaluación de escalado automático.
- La operación de cambio de tamaño resultante produce un error y agota el tiempo de expiración.
- Un problema con la fórmula de escalado automático da lugar a valores incorrectos de destino de nodo. El cambio de tamaño funciona o agota el tiempo de expiración.

Puede obtener información sobre la última evaluación de escalado automático mediante la propiedad [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun). Esta propiedad informa del tiempo de evaluación, los valores y el resultado, así como de los errores de rendimiento.

Un [evento completo de cambio de tamaño de grupo](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event) captura información sobre todas las evaluaciones.

### <a name="delete"></a>Eliminar

Cuando se elimina un grupo que contiene nodos, Batch elimina los nodos en primer lugar. A continuación, elimina el propio objeto de grupo. Los nodos del grupo pueden tardar unos minutos en eliminar.

Batch establece el [estado del grupo](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate) en **deleting** durante el proceso de eliminación. La aplicación que realiza la llamada puede detectar si la eliminación del grupo está tardando demasiado tiempo mediante las propiedades **state** y **stateTransitionTime**.

## <a name="pool-compute-node-errors"></a>Errores de nodo de proceso de grupo

Aun cuando Batch asigne correctamente los nodos de un grupo, distintos problemas pueden provocar que algunos de los nodos sean incorrectos y no puedan ejecutar tareas. Estos nodos siguen incurriendo en cargos, por lo que es importante detectar los problemas para evitar pagar por los nodos que no se pueden usar.

### <a name="start-task-failures"></a>Errores de la tarea de inicio

Es posible que desee especificar una [tarea de inicio](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask) opcional para un grupo. Al igual que con cualquier tarea, se puede usar una línea de comandos y archivos de recursos para descargar desde el almacenamiento. La tarea de inicio se ejecuta para cada nodo después de iniciarse. La propiedad **waitForSuccess** especifica si Batch espera hasta que la tarea de inicio se complete correctamente antes de programar las tareas para un nodo.

¿Qué ocurre si ha configurado el nodo para esperar la finalización correcta de la tarea de inicio, pero se produce un error en ella? En ese caso, el nodo no se podrá usar, pero seguirá incurriendo en cargos.

Se pueden detectar los errores de la tarea de inicio mediante las propiedades [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) y [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) de la propiedad de nodo [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) de nivel superior.

Una tarea de inicio con errores también hace que Batch establezca el nodo [state](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) en **starttaskfailed**, si **waitForSuccess** estaba establecido en **true**.

Al igual que con cualquier tarea, puede haber varias causas para que se produzcan errores en la tarea de inicio.  Para solucionar problemas, compruebe los archivos stdout, stderr y cualquier otro archivo de registro específico de la tarea.

Las tareas de inicio deben ser reentrantes, ya que es posible que la tarea de inicio se ejecute varias veces en el mismo nodo. La tarea de inicio se ejecuta cuando se restablece o se reinicia la imagen inicial de un nodo. En raras ocasiones, una tarea de inicio se ejecutará después de que un evento provoque el reinicio de un nodo, donde uno de los discos efímeros o del sistema operativo restableció la imagen inicial, mientras que el otro no. Dado que las tareas de inicio de Batch (como todas las tareas de Batch) se ejecutan desde el disco efímero, esto no suele ser un problema, pero en algunos casos en los que la tarea de inicio está instalando una aplicación en el disco del sistema operativo y manteniendo otros datos en el disco efímero, puede causar problemas porque los elementos no están sincronizados. Proteja la aplicación como corresponda si usa ambos discos.

### <a name="application-package-download-failure"></a>Error al descargar el paquete de aplicación

Puede especificar uno o varios paquetes de aplicación para un grupo. Batch descarga los archivos de paquete especificados en cada nodo y descomprime los archivos después de que se haya iniciado el nodo, pero antes de que se programen las tareas. Es habitual usar una línea de comandos de tarea de inicio junto con los paquetes de aplicación. Por ejemplo, para copiar archivos en otra ubicación o para ejecutar la configuración.

La propiedad [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) del nodo notifica un error al descargar y descomprimir un paquete de aplicación. El estado del nodo se establece en **inutilizable**.

### <a name="container-download-failure"></a>Error de descarga del contenedor

Puede especificar una o varias referencias de contenedor en un grupo. Batch descarga los contenedores especificados en cada nodo. La propiedad [errores](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) del nodo notifica un error al descargar un contenedor y establece el estado del nodo en **inutilizable**.

### <a name="node-in-unusable-state"></a>Nodo en estado unusable

Azure Batch puede establecer el [estado del nodo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate) en **unusable** por diversos motivos. Con el estado del nodo establecido en **unusable**, no se pueden programar tareas para el nodo, pero sigue generando cargos.

Los nodos con un estado **inutilizable**, pero sin [errores](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) implica que Batch no se puede comunicar con la VM. En este caso, Batch siempre intenta recuperar la VM. Batch no intentará recuperar automáticamente VM que no puedan instalar los paquetes de aplicaciones o contenedores, aunque su estado sea **inutilizable**.

Si Batch puede determinar la causa, la propiedad [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) del nodo la notifica.

Otros ejemplos de causas de nodos **unusable** incluyen:

- Una imagen de máquina virtual personalizada no es válida. Por ejemplo, una imagen que no está preparada correctamente.

- Se mueve una máquina virtual debido a un error de infraestructura o una actualización de bajo nivel. Batch recupera el nodo.

- Se ha implementado una imagen de VM en el hardware no compatible. Por ejemplo, se intenta ejecutar una imagen de HPC de CentOS en una VM [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series).

- Las VM están en una [red virtual de Azure](batch-virtual-network.md), y se ha bloqueado el tráfico a los puertos claves.

- Las máquinas virtuales están en una red virtual, pero el tráfico saliente hacia el almacenamiento de Azure está bloqueado.

- Las máquinas virtuales están en una red virtual con una configuración de DNS de cliente y el servidor DNS no puede resolver el almacenamiento de Azure.

### <a name="node-agent-log-files"></a>Archivos de registro del agente de nodo

El proceso del agente Batch que se ejecuta en cada nodo del grupo puede proporcionar archivos de registro que resulten útiles si necesita ponerse en contacto con el soporte técnico sobre un problema en el nodo de grupo. Los archivos de registro para un nodo se pueden cargar a través de Azure Portal, Batch Explorer o una [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs). Es útil cargar y guardar los archivos de registro. A continuación, puede eliminar el nodo o el grupo para ahorrar el costo de los nodos en ejecución.

### <a name="node-disk-full"></a>Disco del nodo lleno

Batch usa la unidad temporal para una máquina virtual de nodos de grupo para los archivos de trabajo, los archivos de tareas y los archivos compartidos. 

- Archivos de paquetes de aplicación
- Archivos de recursos de tareas
- Archivos específicos de la aplicación descargados en una de las carpetas de Batch
- Archivos stdout y stderr para cada ejecución de aplicación de tareas
- Archivos de salida específicos de la aplicación

Algunos de estos archivos solo se escriben una vez cuando se crean los nodos de grupo, como los paquetes de aplicación de grupos o los archivos de recursos de tarea de inicio de grupo. Incluso si solo se escriben una vez cuando se crea el nodo, si estos archivos son demasiado grandes, podrían llenar la unidad temporal.

Otros archivos se escriben para cada tarea que se ejecuta en un nodo, como stdout y stderr. Si un gran número de tareas se ejecutan en el mismo nodo o los archivos de tareas son demasiado grandes, podrían llenar la unidad temporal.

El tamaño de la unidad temporal depende del tamaño de la máquina virtual. Una consideración que se debe tener en cuenta al seleccionar un tamaño de máquina virtual es asegurarse de que la unidad temporal tenga espacio suficiente.

- En el Azure Portal al agregar un grupo, se puede mostrar la lista completa de tamaños de máquina virtual y hay una columna de "Tamaño de disco de recursos".
- Los artículos que describen todos los tamaños de máquina virtual tienen tablas con una columna de "Almacenamiento temporal"; por ejemplo [Tamaños de máquinas virtuales optimizadas para proceso](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

En el caso de los archivos escritos por cada tarea, se puede especificar un tiempo de retención para cada tarea que determine durante cuánto tiempo se conservan los archivos de tareas antes de que se limpien automáticamente. Se puede reducir el tiempo de retención para reducir los requisitos de almacenamiento.

Si el espacio en disco temporal se rellena, el nodo dejará de ejecutar las tareas en ese momento. En el futuro, se informará de [un error en el nodo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror).


## <a name="next-steps"></a>Pasos siguientes

Compruebe que ha establecido la aplicación para implementar la comprobación de errores exhaustiva, especialmente para las operaciones asincrónicas. Puede ser crítico detectar y diagnosticar problemas con prontitud.
