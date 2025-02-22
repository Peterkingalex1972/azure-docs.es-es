---
title: Escalabilidad automática y Application Gateway con redundancia de zona v2
description: En este artículo se presenta la SKU Standard_v2 y WAF_v2 de la aplicación de Azure, que incluye características de escalabilidad automática y redundancia de zona.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/13/2019
ms.author: victorh
ms.openlocfilehash: 8e79fd1a839113cad5a3a36c01855d98793d7032
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655309"
---
# <a name="autoscaling-and-zone-redundant-application-gateway-v2"></a>Escalabilidad automática y Application Gateway con redundancia de zona v2 

Application Gateway y el firewall de aplicaciones web (WAF) también están disponibles en una SKU Standard_v2 y WAF_v2. La SKU v2 ofrece mejoras de rendimiento y agrega compatibilidad con nuevas características esenciales, como el escalado automático, la redundancia de zona y las IP virtuales estáticas. La nueva SKU v2 sigue admitiendo las características existentes en la SKU Standard y WAF, salvo algunas excepciones que se indican en la sección de [comparación](#differences-with-v1-sku).

La nueva SKU v2 incluye las siguientes mejoras:

- **Escalabilidad automática**: Las implementaciones de Application Gateway o WAF en la SKU de escalado automática pueden escalarse o reducirse verticalmente en función de los cambiantes patrones de la carga de tráfico. La escalabilidad automática también elimina el requisito de tener elegir un tamaño de implementación o un número de instancias durante el aprovisionamiento. Esta SKU ofrece verdadera elasticidad. En la SKU Standard_v2 y WAF_v2, Application Gateway puede funcionar con una capacidad fija (escalabilidad automática deshabilitada) y en modo de escalabilidad automática habilitada. El modo de capacidad fija es útil para escenarios con cargas de trabajo coherentes y predecibles. El modo de escalabilidad automática es útil en aplicaciones que tendrán variación en el tráfico de las aplicaciones.
- **Redundancia de zona**: una implementación de Application Gateway o WAF puede abarcar varias zonas de disponibilidad, lo que elimina la necesidad de aprovisionar instancias de Application Gateway independientes en cada zona con una instancia de Traffic Manager. Puede elegir una o varias zonas donde se implementarán las instancias de Application Gateway, lo que mejora la resistencia ante errores de zona. El grupo de back-end de las aplicaciones se puede distribuir de manera similar entre las zonas de disponibilidad.

  La redundancia de zona están disponibles solo donde estén disponibles las zonas de Azure. En otras regiones, se admiten todas las demás características. Para más información, consulte [¿Qué son las zonas de disponibilidad en Azure?](../availability-zones/az-overview.md#services-support-by-region)
- **VIP estática**: la SKU v2 de Application Gateway admite ahora exclusivamente el tipo de IP virtual estática. Con esto se garantiza que la IP virtual asociada a la puerta de enlace de aplicaciones no cambia durante la vigencia de la implementación, ni siquiera tras un reinicio.  No hay una IP virtual estática en la v1, por lo que debe usar la URL de la puerta de enlace de aplicaciones en lugar de la dirección IP para el enrutamiento del nombre de dominio a App Services a través de la puerta de enlace de aplicaciones.
- **Reescritura de encabezados**: Application Gateway permite agregar, quitar o actualizar los encabezados de solicitud y respuesta HTTP con la SKU v2. Para más información, consulte [Rescritura de encabezados HTTP con Application Gateway](rewrite-http-headers.md).
- **Integración de Key Vault (versión preliminar)** : Application Gateway v2 admite la integración con Key Vault (en versión preliminar pública) para certificados de servidor que se adjuntan a los clientes de escucha con HTTPS habilitado. Para obtener más información, consulte [Terminación SSL con certificados de Key Vault](key-vault-certs.md).
- **Controlador de entrada de Azure Kubernetes Service (AKS) (versión preliminar)** : El controlador de entrada de Application Gateway v2 permite que Azure Application Gateway se utilice como entrada para una instancia de Azure Kubernetes Service (AKS) llamada clúster AKS. Para obtener más información, consulte la [página de documentación](https://azure.github.io/application-gateway-kubernetes-ingress/).
- **Mejoras de rendimiento**: la SKU v2 ofrece un rendimiento cinco veces superior en la descarga de SSL en comparación con la SKU Standard/WAF.
- **Menores tiempos de implementación y actualización**: la SKU v2 proporciona menores tiempos de implementación y actualización en comparación con la SKU Standard/WAF. Esto también incluye los cambios de configuración en WAF.

![](./media/application-gateway-autoscaling-zone-redundant/application-gateway-autoscaling-zone-redundant.png)

## <a name="supported-regions"></a>Regiones admitidas

La SKU Standard_v2 y WAF_v2 está disponible en las siguientes regiones: Centro-norte de EE. UU., Centro-sur de EE. UU., Oeste de EE. UU., Oeste de EE. UU. 2, Este de EE. UU., Este de EE. UU. 2, Centro de EE. UU., Norte de Europa, Europa Occidental, Sudeste Asiático, Centro de Francia, Oeste de Reino Unido, Japón Oriental, Japón Occidental, Este de Australia, Sudeste de Australia, Centro de Canadá, Este de Canadá, Asia Oriental, Centro de Corea del Sur, Sur de Corea del Sur, India del Sur, Sur de Reino Unido, Centro de la India, Oeste de la India, India del Sur.

## <a name="pricing"></a>Precios

Con la SKU v2, el modelo de precios está orientado al consumo y ya no está asociado a recuentos de instancias o tamaños. El modelo de precios de la SKU v2 presenta dos componentes:

- **Precio fijo**: precio por hora (o por fracción de hora) para aprovisionar una instancia de Gateway Standard_v2 o WAF_v2.
- **Precio por unidad de capacidad**: este es el costo basado en el consumo que se suma al costo fijo. La unidad de capacidad se cobra también por hora o fracciones de hora de proceso. Hay tres dimensiones para la unidad de capacidad: unidad de proceso, conexiones persistentes y rendimiento. La unidad de proceso es una medida de la capacidad de procesador consumida. Los factores que afectan a la unidad de proceso son las conexiones TLS/seg, los cálculos de reescritura de direcciones URL y el procesamiento de reglas de WAF. La conexión persistente es una medida de las conexiones TCP establecidas para la puerta de enlace de aplicaciones en un intervalo de facturación determinado. El rendimiento es el promedio de megabits/seg procesados por el sistema en un intervalo de facturación determinado.

Cada unidad de capacidad se compone a lo sumo de: 1 unidad de proceso, o 2500 conexiones persistentes, o rendimiento de 2,22 Mbps.

Orientación para la unidad de proceso:

- **Standard_v2**: cada unidad de proceso tiene capacidad para aproximadamente 50 conexiones por segundo con el certificado TLS de la clave RSA de 2048 bits.
- **WAF_v2**: cada unidad de proceso puede admitir aproximadamente 10 solicitudes simultáneas por segundo para una combinación de tráfico del 70-30 %, con el 70 % de solicitudes de menos de 2 KB de GET/POST y el resto más. El rendimiento de WAF no se ve afectado por el tamaño de respuesta actualmente.

> [!NOTE]
> Cada instancia ahora puede admitir aproximadamente 10 unidades de capacidad.
> El número de solicitudes que puede controlar una unidad de proceso depende de varios criterios, como el tamaño de la clave del certificado TLS, el algoritmo de intercambio de claves, la reescritura de encabezados y, en el caso de WAF, el tamaño de la solicitud entrante. Le recomendamos que realice pruebas de aplicación para determinar la tasa de solicitudes por unidad de proceso. Tanto la unidad de capacidad como la unidad de proceso estarán disponibles como una métrica antes de que comience la facturación.

La siguiente tabla muestra precios de ejemplo y solo tiene fines ilustrativos.

**Precio en el Este de EE. UU.** :

|              Nombre de SKU                             | Precio fijo ($/h)  | Precio de unidad de capacidad ($/unidad de capacidad-h)   |
| ------------------------------------------------- | ------------------- | ------------------------------- |
| Standard_v2                                       |    0,20             | 0,0080                          |
| WAF_v2                                            |    0,36             | 0,0144                          |

Para obtener mas información de precios, consulte la [página de precios](https://azure.microsoft.com/pricing/details/application-gateway/). La facturación está programada para que comience el 1 de julio de 2019.

**Ejemplo 1**

Un servicio de Application Gateway Standard_v2 se aprovisiona sin escalado automático en modo de escalado manual con capacidad fija de cinco instancias.

Precio fijo = 744(horas) * 0,20 $ = 148,8 $ <br>
Unidades de capacidad = 744 (horas) * 10 unidades de capacidad por cada instancia * 5 instancias * 0,008 $ por hora de unidad de capacidad = 297,6 $

Precio total: 148,8 $ + 297,6 $ = 446,4 $

**Ejemplo 2**

Se aprovisiona un servicio de Application Gateway Standard_v2 durante un mes y en este tiempo recibe 25 nuevas conexiones SSL/seg, un promedio de transferencia de datos de 8,88 Mbps. Suponiendo que las conexiones tienen una duración breve, el precio sería:

Precio fijo = 744(horas) * 0,20 $ = 148,8 $

Precio de unidad de capacidad = 744(horas) * Máx (unidad de proceso de 25/50 para conexiones por segundo, unidad de capacidad de 8,88/2,22 para rendimiento) * 0,008 $ = 744 * 4 * 0,008 = 23,81 $.

Precio total = 148,8 $ + 23,81 = 172,61 $

> [!NOTE]
> La función Max devuelve el valor más grande en un par de valores.

**Ejemplo 3**

Se aprovisiona un servicio Application Gateway WAF_v2 durante un mes. En este tiempo, recibe 25 nuevas conexiones SSL por segundo, un promedio de transferencia de datos de 8,88 mbps, y realiza 80 solicitudes por segundo. Suponiendo que las conexiones son de corta duración, y que el cálculo de unidad de proceso para la aplicación admite 10 solicitudes por segundo por unidad de proceso, el precio sería:

Precio fijo = 744(horas) * 0,36 $ = 267,84 $

Precio de unidad de capacidad = 744(horas) * Máx (unidad de proceso máx [25/50 para conexiones por segundo, solicitudes por segundo de WAF de 80/10], unidad de capacidad de 8,88/2,22 para rendimiento) * 0.0144 $ = 744 * 8 * 0,0144 = 85.71 $.

Precio total = 267,84 $ + 85,71 $ = 353,55 $

> [!NOTE]
> La función Max devuelve el valor más grande en un par de valores.

## <a name="scaling-application-gateway-and-waf-v2"></a>Escalado de Application Gateway y WAF v2

Application Gateway and WAF pueden configurarse para escalarse de dos maneras:

- **Escalado automático**: con el escalado automático habilitado, las SKU v2 de Application Gateway y WAF se escalan o reducen verticalmente en función de los requisitos del tráfico de la aplicación. Este modo ofrece una mayor elasticidad a su aplicación y elimina la necesidad de adivinar el tamaño de la puerta de enlace de aplicaciones o el número de instancias. Este modo también le permite ahorrar costos al no requerir que se ejecuten puertas de enlace a una capacidad máxima de aprovisionamiento para una carga de tráfico máxima prevista. Los clientes deben especificar un número de instancias mínimo y opcionalmente máximo. La capacidad mínima garantiza que Application Gateway y WAF v2 no caigan por debajo del número mínimo de instancias especificado, incluso en ausencia de tráfico. Se le facturará esta capacidad mínima incluso si no hay tráfico. Opcionalmente, también puede especificar un número máximo de instancias, lo que garantiza que Application Gateway no se amplíe más allá del número de instancias especificado. Se le seguirá facturando por la cantidad de tráfico atendido por el servicio Gateway. Los números de instancias pueden oscilar entre 0 y 125. El valor predeterminado para el número máximo de instancias es 20 si no se especifica.
- **Manual**: también puede elegir el modo Manual para que la puerta de enlace no se escale automáticamente. En este modo, si hay más tráfico del que puede asumir Application Gateway o WAF, podría perderse tráfico. Con el modo manual, es obligatorio especificar el número de instancias. El número de instancias puede variar de 1 a 125 instancias.

## <a name="feature-comparison-between-v1-sku-and-v2-sku"></a>Comparación de características entre SKU v1 y SKU v2

En la tabla siguiente se comparan las características disponibles con cada SKU.

|                                                   | SKU v1   | SKU v2   |
| ------------------------------------------------- | -------- | -------- |
| Escalado automático                                       |          | &#x2713; |
| Redundancia de zona                                   |          | &#x2713; |
| IP virtual estática                                        |          | &#x2713; |
| Controlador de entrada de Azure Kubernetes Service (AKS) |          | &#x2713; |
| Integración de Azure Key Vault                       |          | &#x2713; |
| Reescritura de encabezados HTTP(S)                           |          | &#x2713; |
| Enrutamiento basado en dirección URL                                 | &#x2713; | &#x2713; |
| Hospedaje de varios sitios                             | &#x2713; | &#x2713; |
| Redireccionamiento de tráfico                               | &#x2713; | &#x2713; |
| Firewall de aplicaciones web (WAF)                    | &#x2713; | &#x2713; |
| Terminación de la Capa de sockets seguros (SSL)            | &#x2713; | &#x2713; |
| Cifrado SSL de un extremo a otro                         | &#x2713; | &#x2713; |
| Afinidad de sesión                                  | &#x2713; | &#x2713; |
| Páginas de error personalizadas                                | &#x2713; | &#x2713; |
| Compatibilidad con WebSocket                                 | &#x2713; | &#x2713; |
| Compatibilidad con HTTP/2                                    | &#x2713; | &#x2713; |
| Purga de la conexión                               | &#x2713; | &#x2713; |

> [!NOTE]
> La SKU v2 de escalado automático ahora admite [sondeos de estado predeterminados](application-gateway-probe-overview.md#default-health-probe) para supervisar automáticamente el estado de todos los recursos en su grupo de back-end y resaltar aquellos miembros de back-end que se considere que no están en buen estado. El sondeo de estado predeterminado se configura automáticamente para back-ends que no tienen ninguna configuración de sondeo personalizado. Para obtener más información, consulte [Información general sobre la supervisión de estado de la puerta de enlace de aplicaciones](application-gateway-probe-overview.md).

## <a name="differences-with-v1-sku"></a>Diferencias con la SKU v1

|Diferencia|Detalles|
|--|--|
|Certificado de autenticación|No compatible.<br>Para más información, consulte [Introducción a SSL de un extremo a otro con Application Gateway](ssl-overview.md#end-to-end-ssl-with-the-v2-sku).|
|Combinación de Application Gateway Standard y Standard_v2 en la misma subred|No compatible|
|Ruta definida por el usuario (UDR) en la subred de Application Gateway|No compatible|
|NSG para el intervalo de puertos de entrada| - 65200 a 65535 para la SKU Standard_v2<br>- 65503 a 65534 para la SKU Standard.<br>Para más información, consulte las [preguntas más frecuentes](application-gateway-faq.md#are-network-security-groups-supported-on-the-application-gateway-subnet).|
|Registros de rendimiento en Azure Diagnostics|No compatible.<br>Se deben usar las métricas de Azure.|
|Facturación|La facturación está programada para que comience el 1 de julio de 2019.|
|Modo FIPS|Actualmente no se admiten.|
|Solo modo ILB|Actualmente no se admite. Se admiten los modos público e ILB juntos.|
|Integración de Netwatcher|No compatible.|
|Integración en Azure Security Center|No disponible todavía.

## <a name="migrate-from-v1-to-v2"></a>Migración de v1 a v2

Hay un script de Azure PowerShell disponible en la galería de PowerShell para ayudarle a migrar desde la v1 de Application Gateway/WAF al SKU de escalado automático v2. Este script le ayuda a copiar la configuración de la puerta de enlace v1. La migración del tráfico sigue siendo su responsabilidad. Para más información, consulte [Migración de Azure Application Gateway de v1 a v2](migrate-v1-v2.md).

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido: Dirección del tráfico web con Azure Application Gateway: Azure Portal](quick-create-portal.md)
- [Creación de una puerta de enlace de aplicaciones con redundancia de zona, escalabilidad automática y una dirección IP virtual reservada con Azure PowerShell](tutorial-autoscale-ps.md)
- Más información acerca de [Application Gateway](overview.md).
- Más información acerca de [Azure Firewall](../firewall/overview.md).
