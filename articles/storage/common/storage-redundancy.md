---
title: Redundancia de datos en Azure Storage | Microsoft Docs
description: Los datos de su cuenta de Microsoft Azure Storage se replican para garantizar la durabilidad y la alta disponibilidad. Entre las opciones de redundancia se incluyen el almacenamiento con redundancia local (LRS), el almacenamiento con redundancia de zona (ZRS), el almacenamiento con redundancia geográfica (GRS), el almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS), el almacenamiento con redundancia de zona geográfica (GZRS) (versión preliminar) y el almacenamiento con redundancia de zona geográfica con acceso de lectura (RA-GZRS) (versión preliminar).
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 07/10/2019
ms.author: tamram
ms.reviewer: artek
ms.subservice: common
ms.openlocfilehash: 17d1bd95067c15bd67f80f3713f0e497bff8a68d
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69516112"
---
# <a name="azure-storage-redundancy"></a>Redundancia de Azure Storage

Los datos de su cuenta de almacenamiento de Microsoft Azure se replican siempre para garantizar la durabilidad y la alta disponibilidad. Azure Storage copia los datos para protegerlos de eventos previstos e imprevistos, como errores transitorios del hardware, interrupciones del suministro eléctrico o de la red y desastres naturales masivos. Puede optar por replicar los datos en el mismo centro de datos, en centros de datos zonales que estén en la misma región o en regiones geográficamente separadas.

La replicación garantiza que la cuenta de almacenamiento cumpla el [contrato de nivel de servicio (SLA) para Storage](https://azure.microsoft.com/support/legal/sla/storage/), incluso en caso de errores. Consulte en el SLA información acerca de las garantías de durabilidad y disponibilidad de Azure Storage.

Azure Storage comprueba con regularidad la integridad de los datos almacenados mediante pruebas de redundancia cíclica (CRC). Si se detectan datos dañados, se reparan con los datos redundantes. Azure Storage también calcula las sumas de comprobación de todo el tráfico de red para detectar daños en los paquetes de datos al almacenar o recuperar datos.

## <a name="choosing-a-redundancy-option"></a>Selección de una opción de redundancia

Cuando crea una cuenta de almacenamiento, puede seleccionar una de las siguientes opciones de redundancia:

* [Almacenamiento con redundancia local (LRS)](storage-redundancy-lrs.md)
* [Almacenamiento con redundancia de zona (ZRS)](storage-redundancy-zrs.md)
* [Almacenamiento con redundancia geográfica (GRS)](storage-redundancy-grs.md)
* [Almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS).](storage-redundancy-grs.md#read-access-geo-redundant-storage)
* [Almacenamiento con redundancia de zona geográfica (GZRS)](storage-redundancy-gzrs.md)
* [Almacenamiento con redundancia de zona geográfica con acceso de lectura (RA-GZRS)](storage-redundancy-gzrs.md)

En la tabla siguiente se ofrece una rápida información general del ámbito de durabilidad y disponibilidad que cada estrategia de replicación le proporcionará para un tipo determinado de evento (o evento de impacto similar).

| Escenario                                                                                                 | LRS                             | ZRS                              | GRS/RA-GRS                                  | GZRS/RA-GZRS                               |
| :------------------------------------------------------------------------------------------------------- | :------------------------------ | :------------------------------- | :----------------------------------- | :----------------------------------- |
| Falta de disponibilidad del nodo en un centro de datos                                                                 | Sí                             | Sí                              | Sí                                  | Sí                                  |
| Un centro de datos completo (de zona o no de zona) deja de estar disponible                                           | Sin                              | Sí                              | Sí                                  | Sí                                  |
| Una interrupción en toda la región                                                                                     | Sin                              | No                               | Sí                                  | Sí                                  |
| Acceso de lectura a los datos (en una región remota y con replicación geográfica) en caso de no disponibilidad en toda la región. | Sin                              | Sin                               | Sí (con RA-GRS)                                   | Sí (con RA-GZRS)                                 |
| Diseñado para proporcionar una \_\_ durabilidad de objetos a lo largo de un año determinado.                                          | Como mínimo 99.999999999 % (once nueves) | Como mínimo 99.9999999999 % (doce nueves) | Como mínimo 99.99999999999999 % (dieciséis nueves) | Como mínimo 99.99999999999999 % (dieciséis nueves) |
| Tipos de cuenta de almacenamiento admitidos                                                                   | GPv2, GPv1, Blob                | GPv2                             | GPv2, GPv1, Blob                     | GPv2                     |
| SLA de disponibilidad para las solicitudes de lectura | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) | Al menos un 99,99 % (99,9 % para el nivel de acceso esporádico) |
| SLA de disponibilidad para las solicitudes de escritura | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) | Al menos un 99,9 % (99 % para el nivel de acceso esporádico) |

Se replican todos los datos de la cuenta de almacenamiento, incluidos los blobs en bloques y los blobs en anexos, blobs en páginas, colas, tablas y archivos. Todos los tipos de cuentas de almacenamiento se replican, aunque ZRS requiere una cuenta de almacenamiento de uso general v2.

Para obtener información sobre los precios de las diferentes opciones de redundancia, vea [Precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/). 

Para obtener información acerca de las garantías de durabilidad y disponibilidad de Azure Storage, vea [Acuerdo de Nivel de Servicio de Azure](https://azure.microsoft.com/support/legal/sla/storage/).

> [!NOTE]
> Azure Premium Storage solo admite almacenamiento con redundancia local (LRS).

## <a name="changing-replication-strategy"></a>Cambio de estrategia de replicación

Puede cambiar la estrategia de replicación de la cuenta de almacenamiento mediante [Azure Portal](https://portal.azure.com/), [Azure Powershell](storage-powershell-guide-full.md), la [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) o una de las [bibliotecas de cliente de Azure Storage](https://docs.microsoft.com/azure/index#pivot=sdkstools). Si cambia el tipo de replicación de la cuenta de almacenamiento, no se producirá ningún tiempo de inactividad.

   > [!NOTE]
   > Actualmente, no puede usar las bibliotecas de cliente de Azure Portal o Azure Storage para convertir su cuenta a ZRS, GZRS o RA-GZRS. Para obtener más detalles sobre cómo migrar su cuenta a ZRS, consulte [Almacenamiento con redundancia de zona (ZRS): aplicaciones de Azure Storage de alta disponibilidad](storage-redundancy-zrs.md). Para obtener más detalles sobre cómo migrar a GZRS o RA-GZRS, consulte [Almacenamiento con redundancia de zona geográfica para obtener alta disponibilidad y durabilidad máxima (versión preliminar)](storage-redundancy-zrs.md).
    
### <a name="are-there-any-costs-to-changing-my-accounts-replication-strategy"></a>¿Hay algún costo al cambiar la estrategia de replicación de mi cuenta?

Depende de la ruta de acceso de la conversión. Si se ordenan de la más económica a la más cara, las ofertas de redundancia de Azure Storage son LRS, ZRS, GRS, RA-GRS, GZRS y RA-GZRS. Por ejemplo, si se pasa *de* LRS a cualquier otra opción, se incurrirá en cargos adicionales porque se pasará a un nivel de redundancia más sofisticado. Si se migra *a* GRS o RA-GRS se incurrirá en un cargo por ancho de banda de salida porque los datos (en la región primaria) se están replicando en la región secundaria remota. Este cargo es un costo único en la instalación inicial. Después de copiar los datos, ya no hay ningún cargo adicional de migración. Solo se le cobra por replicar cualquier dato nuevo o actualizado en los datos existentes. Para obtener más información sobre los cargos de ancho de banda, consulte la [página de precios de Azure Storage](https://azure.microsoft.com/pricing/details/storage/blobs/).

Si migra la cuenta de almacenamiento de GRS a LRS no hay ningún costo adicional, pero los datos replicados se eliminarán de la ubicación secundaria.

Si migra la cuenta de almacenamiento de RA-GRS a GRS o LRS, esa cuenta se factura como RA-GRS durante 30 días más después de la fecha de conversión.

## <a name="see-also"></a>Otras referencias

- [Almacenamiento con redundancia local (LRS): redundancia de datos de bajo costo para Azure Storage](storage-redundancy-lrs.md)
- [Almacenamiento con redundancia de zona (ZRS): aplicaciones de Azure Storage de alta disponibilidad](storage-redundancy-zrs.md)
- [Almacenamiento con redundancia geográfica (GRS): replicación entre regiones para Azure Storage](storage-redundancy-grs.md)
- [Almacenamiento con redundancia de zona geográfica (GZRS) para obtener alta disponibilidad y durabilidad máxima (versión preliminar)](storage-redundancy-gzrs.md)
- [Objetivos de escalabilidad y rendimiento de Azure Storage](storage-scalability-targets.md)
- [Diseño de aplicaciones de alta disponibilidad mediante RA-GRS](../storage-designing-ha-apps-with-ragrs.md)
- [Opciones de redundancia de Microsoft Azure Storage y almacenamiento con redundancia geográfica con acceso de lectura](https://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/11/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx)
- [SOSP Paper - Azure Storage: A Highly Available Cloud Storage Service with Strong Consistency](https://blogs.msdn.com/b/windowsazurestorage/archive/2011/11/20/windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency.aspx) (Documentación de SOSP: Azure Storage: Un servicio de almacenamiento en nube altamente disponible de gran coherencia).
