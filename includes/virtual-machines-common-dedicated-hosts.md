---
title: archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 07/26/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a627592bdfcbebc3c7fcda911e31c0ae6f4a630f
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976612"
---
## <a name="benefits"></a>Ventajas 

La reserva de todo el host proporciona las siguientes ventajas:

-   Aislamiento del hardware a nivel de servidor físico. No se colocarán otras máquinas virtuales en los hosts. Los hosts dedicados se implementan en los mismos centros de datos y comparten la misma red y la misma infraestructura de almacenamiento subyacente que otros hosts no aislados.
-   Control sobre los eventos de mantenimiento iniciados por la plataforma Azure. Aunque la mayoría de los eventos de mantenimiento tienen poco o ningún impacto en las máquinas virtuales, hay algunas cargas de trabajo confidenciales en las que cada segundo de pausa puede tener un impacto. Con los hosts dedicados, puede participar en una ventana de mantenimiento para reducir el impacto en el servicio.
-   Con la ventaja híbrida de Azure, puede traer sus propias licencias para Windows y SQL a Azure. El uso de las ventajas híbridas proporciona ventajas adicionales. Para más información, consulte [Ventaja híbrida de Azure](https://azure.microsoft.com/pricing/hybrid-benefit/).



## <a name="groups-hosts-and-vms"></a>Grupos, hosts y máquinas virtuales  

![Vista de los nuevos recursos para hosts dedicados.](./media/virtual-machines-common-dedicated-hosts/dedicated-hosts2.png)

Un **grupo host** es un recurso que representa una colección de hosts dedicados. Puede crear un grupo host en una región y una zona de disponibilidad, y agregarle hosts.

Un **host** es un recurso que se asigna a un servidor físico en un centro de datos de Azure. El servidor físico se asigna cuando se crea el host. Un host se crea dentro de un grupo host. Un host tiene una SKU que describe qué tamaños de máquina virtual se pueden crear. Cada host puede hospedar varias máquinas virtuales, de diferentes tamaños, siempre y cuando sean de la misma serie de tamaño.

Al crear una máquina virtual en Azure, puede seleccionar el host dedicado que se va a usar para la máquina virtual. Tiene control total sobre las máquinas virtuales que se colocan en los hosts.


## <a name="high-availability-considerations"></a>Consideraciones acerca de la alta disponibilidad 

Para lograr una alta disponibilidad, debe implementar varias máquinas virtuales, que se distribuyen entre varios hosts (un mínimo de 2). Con Azure Dedicated Host, tiene varias opciones para aprovisionar su infraestructura para dar forma a sus límites de aislamiento de errores.

### <a name="use-availability-zones-for-fault-isolation"></a>Uso de las zonas de disponibilidad para el aislamiento de errores

Las zonas de disponibilidad son ubicaciones físicas exclusivas dentro de una región de Azure. Cada zona de disponibilidad consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes. Un grupo host se crea en una zona de disponibilidad individual. Una vez creada, todos los hosts se colocarán dentro de esa zona. Para lograr una alta disponibilidad entre zonas, es preciso crear varios grupos host (uno por zona) y distribuir los hosts en consecuencia.

Si asigna un grupo host a una zona de disponibilidad, todas las máquinas virtuales creadas en ese host deben crearse en la misma zona.

### <a name="use-fault-domains-for-fault-isolation"></a>Uso de dominios de error para el aislamiento de errores

Se puede crear un host en un dominio de error específico. Al igual que las máquinas virtuales de un conjunto de escalado o de un conjunto de disponibilidad, los hosts de distintos dominios de error se colocarán en distintos bastidores físicos del centro de datos. Al crear un grupo host, es necesario especificar el número de dominios de error. Al crear hosts en el grupo host, se asigna un dominio de error a cada host. Las máquinas virtuales no requieren ninguna asignación de dominio de error.

Los dominios de error no son lo mismo que la colocación. Tener el mismo dominio de error para dos hosts no significa que estén próximos entre sí.

El ámbito de los dominios de error es el grupo host. No debe realizar ninguna suposición sobre la antiafinidad entre dos grupos host (a menos que se encuentren en zonas de disponibilidad diferentes).

Las máquinas virtuales implementadas en hosts con distintos dominios de error, tendrán sus servicios subyacentes de Managed Disks en varias marcas de almacenamiento, con el fin de aumentar la protección del aislamiento de errores.

### <a name="using-availability-zones-and-fault-domains"></a>Uso de zonas de disponibilidad y dominios de error

Puede usar ambas funcionalidades juntas para lograr un mayor aislamiento de los errores. En este caso, especificará la zona de disponibilidad y el número de dominios de error en cada grupo de hosts, asignará un dominio de error a cada uno de los hosts del grupo y asignará una zona de disponibilidad a cada una de las máquinas virtuales

La plantilla de ejemplo de Resource Manager que se encuentra [aquí](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) usa zonas y dominios de error para distribuir los hosts para obtener la máxima resistencia en una región.

## <a name="maintenance-control"></a>Control de mantenimiento

En ocasiones, es posible que la infraestructura que da soporte a las máquinas virtuales se actualice para mejorar la confiabilidad, el rendimiento, la seguridad y para iniciar nuevas características. La plataforma Azure intenta minimizar el impacto del mantenimiento de la plataforma siempre que sea posible, pero los clientes con cargas de trabajo*sensibles al mantenimiento* no pueden tolerar los pocos segundos en los que la máquina virtual debe estar sin funcionamiento o congelada para realizar el mantenimiento.

El **control del mantenimiento** proporciona a los clientes una opción para omitir las actualizaciones de plataforma normales programadas en sus hosts dedicados y, después, aplicarlas en el momento que prefieran en un periodo acumulado de 35 días.

> [!NOTE]
>  El control de mantenimiento se encuentra actualmente en una fase de versión preliminar limitada y requiere un proceso de incorporación. Solicite esta versión preliminar mediante el envío de una [ encuesta de nominación](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR6lJf7DwiQxNmz51ksQvxV9UNUM3UllWUjBMTFZQUFhHUDI0VTBPQlJFNS4u).

## <a name="capacity-considerations"></a>Consideraciones de capacidad

Una vez que se aprovisiona un host dedicado, Azure lo asigna a un servidor físico. De esta forma se garantiza la disponibilidad de la capacidad cuando se necesita aprovisionar la máquina virtual. Azure usa toda la capacidad de la región (o zona) para elegir un servidor físico para el host. También significa que los clientes pueden esperar poder aumentar la superficie de su host dedicado sin tener que preocuparse de quedarse sin espacio en el clúster.

## <a name="quotas"></a>Cuotas

Existe un límite de cuota predeterminado por región de 3000 vCPU para hosts dedicados. Sin embargo, el número de hosts que puede implementar también está limitado por la cuota de la familia de tamaños de máquina virtual que se usa para el host. Por ejemplo, una suscripción de **pago por uso** solo puede tener una cuota de 10 vCPU disponibles para la serie de tamaños Dsv3 en la región Este de EE. UU. En este caso, debe solicitar un aumento de la cuota a un mínimo de 64 vCPU para poder implementar un host dedicado. Seleccione el botón **Solicitar aumento** en la esquina superior derecha para archivar una solicitud, si es necesario.

![Captura de pantalla de la página de uso y cuotas del portal](./media/virtual-machines-common-dedicated-hosts/quotas.png)

Para más información, consulte [Cuotas de vCPU de máquinas virtuales](/azure/virtual-machines/windows/quotas).

## <a name="pricing"></a>Precios

A los usuarios se les cobra por host dedicado, independientemente del número de máquinas virtuales que se implementen. En el extracto mensual, verá un nuevo tipo de recurso facturable de hosts. Las máquinas virtuales de un host dedicado se seguirán mostrando en la instrucción, pero su precio será 0.

El precio del host se establece en función de la familia de máquinas virtuales, del tipo (tamaño de hardware) y de la región. El precio de un host tiene que ver con el tamaño de máquina virtual mayor que admita el host.

Las licencias de software, el almacenamiento y el uso de la red se facturan por separado del host y las máquinas virtuales. No hay ningún cambio en esos elementos facturables.

Para más información, consulte [Precios de Azure Dedicated Host](https://aka.ms/ADHPricing).
 
## <a name="vm-families-and-hardware-generations"></a>Familias de máquinas virtuales y generaciones de hardware

Se define una SKU para un host y representa el tipo y la serie de tamaño de máquina virtual. Puede mezclar varias máquinas virtuales de distintos tamaños en un solo host, siempre que no sean de la misma serie de tamaño. El tipo es la generación de hardware disponible actualmente en la región.

Los distintos `types` de la misma serie de máquinas virtuales serán de distintos proveedores de CPU y tendrán diferentes generaciones de CPU y número de núcleos.

Para más información, consulte la [página de precios](https://aka.ms/ADHPricing) de hosts.

Durante la versión preliminar, se admitirán los siguientes tipos/SKU de host:  DSv3_Type1 y ESv3_Type1

 
## <a name="host-life-cycle"></a>Ciclo de vida del host


Azure supervisa y administra el estado de mantenimiento de los hosts. Al consultar el host se devolverán los siguientes estados:

| Estado de mantenimiento   | DESCRIPCIÓN       |
|----------|----------------|
| Host disponible     | No hay ningún problema conocido en el host.   |
| Host bajo investigación  | Hay algunos problemas en el host y se están examinando. Se trata de un estado transitorio necesario para que Azure intente identificar el ámbito y la causa principal del problema identificado. Es posible que se vean afectadas las máquinas virtuales que se ejecutan en el host. |
| Host pendiente de desasignación   | Azure no puede devolver el host a un estado correcto y le solicita que vuelva a implementar las máquinas virtuales fuera de este host. Si `autoHealingOnFailure` está habilitado, las máquinas virtuales *se recuperan* con un hardware que funcione correctamente. De lo contrario, es posible que la máquina virtual se esté ejecutando en un host que esté a punto de producir un error.|
| Host desasignado  | Todas las máquinas virtuales se han quitado del host. Ya no se le cobrará por este host, ya que el hardware se eliminado de la rotación.   |

