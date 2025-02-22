---
title: Uso de Azure Data Factory para migrar datos de Amazon S3 a Azure Storage | Microsoft Docs
description: Use Azure Data Factory para migrar datos de Amazon S3 a Azure Storage.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 8/04/2019
ms.openlocfilehash: c56e6e004fe7f63725b5f6f4b9c71f60cc7b91ed
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828852"
---
# <a name="use-azure-data-factory-to-migrate-data-from-amazon-s3-to-azure-storage"></a>Uso de Azure Data Factory para migrar datos de Amazon S3 a Azure Storage 

Azure Data Factory proporciona un mecanismo eficaz, sólido y rentable para migrar datos a escala desde Amazon S3 a Azure Blob Storage o Azure Data Lake Storage Gen2.  En este artículo se proporciona la siguiente información para ingenieros de datos y desarrolladores: 

> [!div class="checklist"]
> * Rendimiento 
> * Resistencia de copia
> * Seguridad de las redes
> * Arquitectura de soluciones de alto nivel 
> * Procedimientos recomendados para la implementación  

## <a name="performance"></a>Rendimiento

ADF ofrece una arquitectura sin servidor que permite paralelismo en diferentes niveles, lo que permite a los desarrolladores crear canalizaciones para aprovechar al máximo el ancho de banda de red, así como el ancho de banda y las IOPS de almacenamiento para maximizar el rendimiento del movimiento de datos para su entorno. 

Nuestros clientes han migrado correctamente petabytes de datos, compuestos por cientos de millones de archivos, de Amazon S3 a Azure Blob Storage, con un rendimiento sostenido de 2 Gbps y superior. 

![rendimiento](media/data-migration-guidance-s3-to-azure-storage/performance.png)

La imagen anterior muestra cómo puede lograr una gran velocidad de movimiento de datos a través de diferentes niveles de paralelismo:
 
- Una única actividad de copia puede aprovechar recursos de proceso escalables: si usa Azure Integration Runtime, puede especificar [hasta 256 DIU](https://docs.microsoft.com/azure/data-factory/copy-activity-performance#data-integration-units) para cada actividad de copia en modo sin servidor; si usa un entorno de ejecución de integración autohospedado, puede escalar verticalmente la máquina o escalar horizontalmente a varias máquinas ([hasta 4 nodos](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)) manualmente y una única actividad de copia creará particiones de su conjunto de archivos en todos los nodos. 
- Una única actividad de copia lee y escribe en el almacén de datos mediante varios subprocesos. 
- El flujo de control de ADF puede iniciar varias actividades de copia en paralelo, por ejemplo, mediante un [bucle ForEach](https://docs.microsoft.com/azure/data-factory/control-flow-for-each-activity). 

## <a name="resilience"></a>Resistencia

Dentro de una ejecución de una única actividad de copia, ADF tiene un mecanismo de reintento integrado para que pueda controlar cierto nivel de errores transitorios en los almacenes de datos o en la red subyacente. 

Al realizar copias binarias de S3 a BLOB y de S3 a ADLS Gen2, ADF realiza automáticamente puntos de control.  Si se ha producido un error en la ejecución de la actividad de copia o se ha agotado el tiempo de espera, en un reintento posterior (asegúrese de que el número de reintentos > 1), la copia se reanuda desde el último punto de error en lugar de comenzar desde el principio. 

## <a name="network-security"></a>Seguridad de las redes 

De forma predeterminada, ADF transfiere datos de Amazon S3 a Azure Blob Storage o Azure Data Lake Storage Gen2 mediante una conexión cifrada a través del protocolo HTTPS.  HTTPS proporciona cifrado de datos en tránsito y evita la interceptación y ataques de tipo "Man in the middle". 

Como alternativa, si no desea que los datos se transfieran a través de la red pública de Internet, puede lograr una mayor seguridad mediante la transferencia de datos a través de un vínculo de emparejamiento privado entre AWS Direct Connect y Azure Express Route.  Consulte la arquitectura de la solución siguiente para obtener información sobre cómo lograr esto. 

## <a name="solution-architecture"></a>Arquitectura de la solución

Migración de datos a través de la red pública de Internet:

![solución-arquitectura-red-pública](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-public-network.png)

- En esta arquitectura, los datos se transfieren de forma segura mediante HTTPS a través de la red pública de Internet. 
- Tanto el origen en Amazon S3 como el destino en Azure Blob Storage o Azure Data Lake Storage Gen2 están configurados para permitir el tráfico desde todas las direcciones IP de red.  Consulte la segunda arquitectura siguiente para obtener información sobre cómo puede restringir el acceso a la red a un intervalo de IP específico. 
- Puede escalar fácilmente la cantidad de potencia en modo sin servidor para utilizar totalmente la red y el ancho de banda de almacenamiento, para obtener así el mejor rendimiento para su entorno. 
- Tanto la migración de la instantánea inicial como la migración de datos diferencial se pueden realizar con esta arquitectura. 

Migración de datos a través de un vínculo privado: 

![solución-arquitectura-red-privada](media/data-migration-guidance-s3-to-azure-storage/solution-architecture-private-network.png)

- En esta arquitectura, la migración de datos se realiza a través de un vínculo de emparejamiento privado entre AWS Direct Connect y Azure Express Route, de modo que los datos nunca atraviesan la red pública de Internet.  Requiere el uso de AWS VPC y la red virtual de Azure. 
- Debe instalar el entorno de ejecución de integración autohospedado de ADF en una máquina virtual de Windows dentro de la red virtual de Azure para lograr esta arquitectura.  Puede escalar verticalmente las máquinas virtuales del entorno de ejecución de integración autohospedado o escalar horizontalmente a varias máquinas virtuales (hasta 4 nodos) manualmente para aprovechar al máximo el ancho de banda/IOPS de almacenamiento y de la red. 
- Si es aceptable transferir datos a través de HTTPS, pero desea limitar el acceso de red al origen de S3 a un intervalo de IP específico, puede adoptar una variación de esta arquitectura quitando AWS VPC y reemplazando el vínculo privado por HTTPS.  Deberá mantener Azure virtual y el entorno de ejecución de integración autohospedado en la máquina virtual de Azure para tener una dirección IP estática enrutable públicamente para el uso de listas blancas. 
- Tanto la migración de datos de la instantánea inicial como la migración de datos diferencial se pueden realizar con esta arquitectura. 

## <a name="implementation-best-practices"></a>Procedimientos recomendados para la implementación 

### <a name="authentication-and-credential-management"></a>Autenticación y administración de identidades 

- Para autenticarse en una cuenta de Amazon S3, debe usar la [clave de acceso para la cuenta de IAM](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service#linked-service-properties). 
- Se admiten varios tipos de autenticación para conectarse a Azure Blob Storage.  Se recomienda encarecidamente el uso de [identidades administradas para recursos de Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#managed-identity): sobre la base de una identidad de ADF administrada automáticamente en Azure AD, permite configurar canalizaciones sin proporcionar credenciales en la definición del servicio vinculado.  Como alternativa, puede autenticarse en Azure Blob Storage mediante la [entidad de servicio](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#service-principal-authentication), la [firma de acceso compartido](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#shared-access-signature-authentication) o la [clave de la cuenta de almacenamiento](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#account-key-authentication). 
- También se admiten varios tipos de autenticación para conectarse a Azure Data Lake Storage Gen2.  Se recomienda encarecidamente el uso de [identidades administradas para recursos de Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#managed-identity), aunque también se puede usar la [entidad de servicio](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#service-principal-authentication) o la [clave de la cuenta de almacenamiento](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#account-key-authentication). 
- Si no usa identidades administradas para recursos de Azure, es muy recomendable [almacenar las credenciales en Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault) para facilitar la administración y la rotación de las claves de forma centralizada sin modificar los servicios vinculados de ADF.  Este es también uno de los [procedimientos recomendados para CI/CD](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd). 

### <a name="initial-snapshot-data-migration"></a>Migración de datos de instantánea inicial 

La partición de datos se recomienda especialmente al migrar más de 10 TB de datos.  Para crear una partición de los datos, utilice el valor de "prefijo" para filtrar las carpetas y los archivos de Amazon S3 por nombre y, a continuación, cada trabajo de copia de ADF podrá copiar una partición cada vez.  Puede ejecutar varios trabajos de copia de ADF simultáneamente para mejorar el rendimiento. 

Si se produce un error en alguno de los trabajos de copia debido a una incidencia transitoria de la red o del almacén de datos, puede volver a ejecutar el trabajo de copia con errores para volver a cargar esa partición específica de AWS S3.  El resto de los trabajos de copia que cargan otras particiones no se verán afectados. 

### <a name="delta-data-migration"></a>Migración de datos diferencial 

La manera más eficaz de identificar archivos nuevos o modificados de AWS S3 es mediante la convención de nomenclatura con particiones de tiempo: cuando los datos de AWS S3 se han particionado en el tiempo con información del intervalo de tiempo en el nombre de archivo o carpeta (por ejemplo,/aaaa/mm/dd/archivo.csv), la canalización puede identificar fácilmente qué archivos o carpetas copiar de forma incremental. 

Como alternativa, si los datos de AWS S3 no tienen particiones de tiempo, ADF puede identificar los archivos nuevos o modificados por el valor de LastModifiedDate (fecha de la última modificación).   Para ello, ADF examinará todos los archivos de AWS S3 y solo copiará el archivo nuevo y actualizado cuya marca de tiempo de última modificación sea mayor que un valor determinado.  Tenga en cuenta que si tiene un gran número de archivos en S3, el análisis de archivos inicial podría tardar mucho tiempo, independientemente de la cantidad de archivos que cumplan la condición de filtro.  En este caso, se recomienda crear primero particiones de los datos, con el mismo valor de "prefijo" para la migración de instantánea inicial, de modo que el análisis de archivos pueda realizarse en paralelo.  

### <a name="for-scenarios-that-require-self-hosted-integration-runtime-on-azure-vm"></a>Escenarios que requieren un entorno de ejecución de integración autohospedado en una máquina virtual de Azure 

Si va a migrar datos a través de un vínculo privado o si desea permitir un intervalo de IP específico en el firewall de Amazon S3, debe instalar un entorno de ejecución de integración autohospedado en la máquina virtual Windows de Azure. 

- La configuración recomendada para empezar para cada máquina virtual de Azure es Standard_D32s_v3 con 32 vCPU y 128 GB de memoria.  Puede seguir supervisando el uso de la CPU y la memoria de la máquina virtual del entorno de ejecución de integración durante la migración de datos para ver si necesita escalar verticalmente la máquina virtual para mejorar el rendimiento o reducir verticalmente la máquina virtual para ahorrar costos. 
- También puede escalar horizontalmente asociando hasta 4 nodos de máquina virtual con un solo entorno de ejecución de integración autohospedado.  Un único trabajo de copia que se ejecute en un entorno de ejecución de integración autohospedado particionará automáticamente el conjunto de archivos y aprovechará todos los nodos de máquina virtual para copiar los archivos en paralelo.  Para lograr una alta disponibilidad, se recomienda empezar con 2 nodos de máquina virtual para evitar un único punto de error durante la migración de datos. 

### <a name="rate-limiting"></a>Limitación de frecuencia 

Como procedimiento recomendado, lleve a cabo una prueba de concepto de rendimiento con un conjunto de datos de ejemplo representativo, a fin de determinar el tamaño de partición adecuado. 

Comience con una sola partición y una única actividad de copia con la configuración de DIU predeterminada.  Aumente gradualmente el valor de DIU hasta alcanzar el límite de ancho de banda de la red o el límite de ancho de banda/IOPS de los almacenes de datos, o hasta alcanzar el máximo de 256 DIU permitido en una única actividad de copia. 

A continuación, aumente gradualmente el número de actividades de copia simultáneas hasta alcanzar los límites de su entorno. 

Si encuentra errores de limitación notificados por la actividad de copia de ADF, reduzca el valor de simultaneidad o DIU en ADF, o considere la posibilidad de aumentar los límites de ancho de banda/IOPS de la red y los almacenes de datos.  

### <a name="estimating-price"></a>Estimación del precio 

> [!NOTE]
> Este es un ejemplo de precios hipotético.  Los precios reales dependen del rendimiento real de su entorno.

Considere la siguiente canalización construida para migrar datos de S3 a Azure Blob Storage: 

![precios-canalización](media/data-migration-guidance-s3-to-azure-storage/pricing-pipeline.png)

Supongamos lo siguiente: 

- El volumen total de datos es de 2 PB. 
- La migración de datos a través de HTTPS usa la primera arquitectura de la solución. 
- Los 2 PB se dividen en particiones de 1 K y cada copia mueve una partición. 
- Cada copia se configura con DIU = 256 y consigue un rendimiento de 1 Gbps. 
- La simultaneidad de ForEach se establece en 2 y el rendimiento agregado es de 2 Gbps. 
- En total, se tardan 292 horas en completar la migración. 

Este es el precio estimado en función de las suposiciones anteriores: 

![precios-tabla](media/data-migration-guidance-s3-to-azure-storage/pricing-table.png)


### <a name="additional-references"></a>Referencias adicionales 
- [Conector de Amazon Simple Storage Service](https://docs.microsoft.com/azure/data-factory/connector-amazon-simple-storage-service)
- [Conector de Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
- [Conector de Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
- [Guía de optimización y rendimiento de la actividad de copia](https://docs.microsoft.com/azure/data-factory/copy-activity-performance)
- [Creación y configuración de un entorno de ejecución de integración autohospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)
- [Alta disponibilidad y escalabilidad de un entorno de ejecución de integración autohospedado](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#high-availability-and-scalability)
- [Consideraciones sobre la seguridad del movimiento de datos](https://docs.microsoft.com/azure/data-factory/data-movement-security-considerations)
- [Almacenamiento de credenciales en Azure Key Vault](https://docs.microsoft.com/azure/data-factory/store-credentials-in-key-vault)
- [Copia incremental de nuevos archivos por el nombre de archivo con particiones de tiempo](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-partitioned-file-name-copy-data-tool)
- [Copia incremental de archivos nuevos y modificados según LastModifiedDate](https://docs.microsoft.com/azure/data-factory/tutorial-incremental-copy-lastmodified-copy-data-tool)
- [Página de precios de ADF](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>Pasos siguientes

- [Copia de archivos de varios contenedores con Azure Data Factory](solution-template-copy-files-multiple-containers.md)