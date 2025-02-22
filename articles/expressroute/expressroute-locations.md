---
title: 'Proveedores de conectividad y ubicaciones: Azure ExpressRoute | Microsoft Docs'
description: Este artículo ofrece una introducción detallada de ubicaciones en la que se ofrecen los servicios e información sobre cómo conectarse a regiones de Azure. Ordenado por proveedor de conectividad.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: cherylmc
ms.openlocfilehash: 88972afe1f1e998fc2fb731638332915bd8317b5
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891594"
---
# <a name="expressroute-partners-and-peering-locations"></a>Asociados de ExpressRoute y ubicaciones de emparejamiento

> [!div class="op_single_selector"]
> * [Ubicaciones por proveedor](expressroute-locations.md)
> * [Proveedores por ubicación](expressroute-locations-providers.md)


Las tablas de este artículo proporcionan información acerca de la cobertura geográfica y las ubicaciones de ExpressRoute, los proveedores de conectividad de ExpressRoute y los integradores de sistemas (SI) de ExpressRoute.

> [!Note]
> Las regiones de Azure y las ubicaciones de ExpressRoute son dos conceptos diferentes y conocer dicha diferencia es fundamental para explorar la conectividad de red híbrida de Azure. 
>
>

## <a name="azure-regions"></a>Regiones de Azure
Las regiones de Azure son centros de datos globales en los que se encuentran los recursos de proceso, red y almacenamiento de Azure. Al crear un recurso de Azure, los clientes deben seleccionar la ubicación del recurso. Esta ubicación determina el centro de datos (o la zona de disponibilidad) de Azure en que se crea el recurso.

## <a name="expressroute-locations"></a>Ubicaciones de ExpressRoute
Las ubicaciones de ExpressRoute (a las que a veces se denomina ubicaciones de emparejamiento o ubicaciones de punto de encuentro) son instalaciones de ubicación compartida en las que se encuentran los dispositivos de Microsoft Enterprise Edge (MSEE). Las ubicaciones de ExpressRoute son el punto de entrada a la red de Microsoft (y se distribuyen globalmente, lo que proporciona a los clientes la oportunidad de conectarse a la red de Microsoft en todo el mundo). Estas ubicaciones son donde los asociados de ExpressRoute y los clientes ExpressRoute Direct emiten conexiones cruzadas a la red de Microsoft. En general, no es preciso que la ubicación de ExpressRoute coincida con la región de Azure. Por ejemplo, un cliente puede crear un circuito ExpressRoute con la ubicación de recursos *Este de EE. UU.* , en la ubicación de emparejamiento*Seattle*.

Tendrá acceso a los servicios de Azure en todas las regiones dentro de una región geopolítica si se conectó al menos a una ubicación de ExpressRoute dentro de la región geopolítica.

## <a name="locations"></a>Regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.
La siguiente tabla proporciona un mapa de las regiones de Azure para las ubicaciones de ExpressRoute dentro de una región geopolítica.

| **Región geopolítica** | **Zona** | **Regiones de Azure** | **Ubicaciones de ExpressRoute** |
| --- | --- | --- | --- |
| **Australia Government** | 1 | Centro de Australia, Centro de Australia 2 |Canberra, Canberra 2 |
| **Europa** | 1 |Centro de Francia, Sur de Francia, Europa del Norte, Europa Occidental, Oeste de Reino Unido, Sur de Reino Unido |Ámsterdam, Ámsterdam2, Copenhague, Dublín, Fráncfort, Londres, Londres2, Marsella, Newport (Gales), París, Estocolmo, Zúrich |
| **Norteamérica** | 1 |Este de EE. UU., oeste de EE. UU., este de EE. UU. 2, oeste de EE.UU. 2, centro de EE. UU., centro-sur de EE. UU., centro-norte de EE. UU., centro-oeste de EE. UU., centro de Canadá y este de Canadá |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Ángeles, Miami, Nueva York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Ciudad de Quebec, Toronto |
| **Asia** | 2 |Este de Asia y Sudeste de Asia |RAE de Hong Kong, Kuala Lumpur, Singapur, Singapur2, Taipéi |
| **India** | 2 |India occidental, India central, India del Sur |Chennai (Madrás), Chennai (Madrás)2, Mumbai (Bombay), Mumbai (Bombay)2 |
| **Japón** | 2 |Oeste de Japón y Este de Japón |Osaka, Tokyo |
| **Oceanía** | 2 |Este de Australia y Sudeste de Australia |Auckland, Melbourne, Perth, Sídney |
| **Corea del Sur** | 2 |Corea Central, Corea del Sur |Busan, Seúl|
| **Emiratos Árabes Unidos** | 3 | Centro de Emiratos Árabes Unidos, Norte de Emiratos Árabes Unidos | Dubai, Dubai2 |
| **Sudáfrica** | 3 |Oeste de Sudáfrica, Norte de Sudáfrica |Ciudad del cabo, Johannesburgo |
| **Sudamérica** | 3 |Sur de Brasil |Sao Paulo |


## <a name="regions-and-geopolitical-boundaries-for-national-clouds"></a>Regiones y límites geopolíticos para nubes nacionales
En la tabla siguiente se proporciona información sobre las regiones y los límites geopolíticos para nubes nacionales.

| **Región geopolítica** | **Regiones de Azure** | **Ubicaciones de ExpressRoute** |
| --- | --- | --- |
| **Nube del gobierno de Estados Unidos** |Arizona Gob. EE. UU., Iowa Gob. EE. UU., Texas Gob. EE.UU., Virginia Gob. EE.UU., US DoD Central, US DoD Este  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Este de China** |Este de China, Este de China 2 |Shanghai, Shanghai2 |
| **Norte de China** |Norte de China, Norte de China 2 |Beijing, Beijing2 |
| **Alemania** |Centro de Alemania, Alemania oriental |Berlín+, Fráncfort |

No se admite la conectividad entre las regiones geopolíticas en el SKU de ExpressRoute estándar. Debe habilitar el complemento premium de ExpressRoute para admitir conectividad global. No se admite la conectividad con entornos de nube nacionales. Puede trabajar con su proveedor de conectividad si surge tal necesidad.

## <a name="partners"></a>Proveedores de conectividad ExpressRoute

La siguiente tabla muestra las ubicaciones por proveedor de servicios. Si desea ver los proveedores disponibles según la ubicación, consulte [Proveedores de servicios por ubicación](expressroute-locations-providers.md).


### <a name="production-azure"></a>Azure para producción

| **Proveedor de servicios** | **Microsoft Azure** | **Office 365 y Dynamics 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/connectivity-services/azure-expressroute)** |Compatible |Compatible |Melbourne, Sidney |
| **[Airtel](https://www.airtel.in/business/#/)** | Compatible | Compatible | Chennai (Madrás)2, Mumbai (Bombay)2 |
| **[Aryaka Networks](https://www.aryaka.com/)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, RAE de Hong Kong, Sao Paulo, Seattle, Silicon Valley, Singapur, Tokio, Washington DC |
| **[Centros de datos de Ascenty](https://www.ascenty.com/en/cloud/microsoft-express-route)** |Compatible |Compatible |Sao Paulo |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Hong Kong, Londres, Silicon Valley, Singapur, Sidney, Tokio, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Compatible |Compatible |Montreal, Toronto, Ciudad de Quebec |
| **[British Telecom](https://www.globalservices.bt.com/en/solutions/products/bt-compute-for-microsoft-azure)** |Compatible |Compatible |Ámsterdam, RAE de Hong Kong, Johannesburgo, Londres, Newport (Gales), Sao Paulo, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[C3ntro](https://www.c3ntro.com/data1/express-route1.php)** |Compatible |Compatible |Miami |
| **CDC** | Compatible | Compatible | Canberra, Canberra 2 |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Compatible |Compatible |Ámsterdam2, Chicago, Hong Kong, Las Vegas, Nueva York, París, San Antonio, Silicon Valley, Tokio, Toronto, Washington DC |
| **Chief Telecom** |Compatible |Compatible |Hong Kong, Taipéi |
| **China Telecom Global** |Compatible |Compatible |RAE de Hong Kong |
| **[Cologix](https://www.cologix.com/hyperscale/microsoft-azure/)** |Compatible |Compatible |Chicago, Dallas, Montreal, Toronto, Washington DC |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Compatible |Compatible |Ámsterdam, Dublín, Londres, Newport, Osaka, París, Singapur2, Tokio |
| **[Comcast](https://business.comcast.com/landingpage/microsoft-azure)** |Compatible |Compatible |Chicago, Silicon Valley, Washington DC |
| **[CoreSite](https://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Compatible |Compatible |Chicago, Denver, Los Ángeles, Nueva York, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2 |
| **[DE-CIX](https://www.de-cix.net/en/de-cix-service-world/directcloud/find-a-cloud-service/detail/microsoft-azure)** | Compatible |Compatible |Ámsterdam2, Fráncfort, Marsella|
| **[Devoli](https://devoli.com/expressroute)** | Compatible |Compatible | Auckland, Melbourne, Sidney |
| **du datamena** |Compatible |Compatible | Dubai2 |
| **eir** |Compatible |Compatible |Dublín|
| **[Epsilon Global Communications](https://www.epsilontel.com/solutions/direct-cloud-connect)** |Compatible |Compatible |Singapur, Singapur2 |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Compatible |Compatible |Ámsterdam, Atlanta, Chicago, Dallas, Dublín, RAE de Hong Kong, Londres, Londres2, Los Ángeles, Melbourne, Miami, Nueva York, Osaka, París, Sao Paulo, Seattle, Silicon Valley, Singapur, Sydney, Tokio, Toronto, Washington DC |
| **Etisalat UAE** |Compatible |Compatible |Dubai|
| **[euNetworks](https://eunetworks.com/services/solutions/cloud-connect/microsoft-azure-expressroute/)** |Compatible |Compatible |Ámsterdam, Dublín, Londres |
| **FarEasTone** |Compatible |Compatible |Taipéi|
| **GÉANT** |Compatible |Compatible |Ámsterdam |
| **[Global Cloud Xchange (GCX)](https://globalcloudxchange.com/cloud-platform/cloud-x-fusion/)** | Compatible| Compatible | Chennai, Mumbai |
| **[InterCloud](https://www.intercloud.com/)** |Compatible |Compatible |Ámsterdam, Chicago, Londres, Nueva York, París, Silicon Valley, Singapur, Washington DC, Zúrich |
| **[Internet2](https://www.internet2.edu/products-services/cloud-services-applications/microsoft-azure/#service-cloud-connect)** |Compatible |Compatible |Chicago, Dallas, Silicon Valley, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](https://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Compatible |Compatible |Osaka, Tokyo |
| **[Internet Solutions - Cloud Connect](https://www.is.co.za/solution/cloud-connect/)** |Compatible |Compatible |Ciudad del Cabo, Johannesburgo, Londres |
| **[Interxion](https://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Compatible |Compatible |Ámsterdam, Ámsterdam2, Copenhague, Dublín, Fráncfort, Londres, Marsella, París, Zúrich |
| **[IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)**|Compatible |Compatible | Ámsterdam, Londres2, Silicon Valley, Toronto |
| **Jaguar Network** |Compatible |Compatible |Marsella|
| **[Jisc](https://www.jisc.ac.uk/microsoft-azure-expressroute)** |Compatible |Compatible |Londres |
| **[KINX](https://www.kinx.net/service/network/cloudhub/ms-expressroute/?lang=en)** |Compatible |Compatible |Seúl |
| **[Kordia](https://www.kordia.co.nz/cloudconnect)** | Compatible |Compatible |Auckland, Sídney |
| **[KPN](https://www.kpn.com/zakelijk/cloud/connect.htm)** | Compatible | Compatible | Ámsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Londres, Newport (Gales), Sao Paulo, Seattle, Silicon Valley, Singapur y Washington DC |
| **LG CNS** |Compatible |Compatible |Busan, Seúl |
| **[Liquid Telecom](https://www.liquidtelecom.com/products-and-services/cloud.html)** |Compatible |Compatible |Ciudad del cabo, Johannesburgo |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible |Compatible |Ámsterdam, Atlanta, Auckland, Chicago, Dallas, Denver, Dublín, RAE de Hong Kong, Las Vegas, Londres, Los Ángeles, Melbourne, Miami, Montreal, Nueva York, Perth, Quebec City, San Antonio, Seattle, Silicon Valley, Singapur, Singapur2, Sídney, Toronto, Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** |Compatible |Compatible |Londres |
| **[Neutrona Networks](https://www.neutrona.com/index.php/azure-expressroute/)** |Compatible |Compatible |Dallas, Los Ángeles, Miami, Sao Paulo, Washington DC |
| **[Datos de última generación](https://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Compatible |Compatible |Newport (Gales) |
| **[NEXTDC](https://www.nextdc.com/services/axon-ethernet/microsoft-expressroute)** |Compatible |Compatible |Melbourne, Perth, Sidney |
| **[NTT Communications](https://www.ntt.com/en/services/network/virtual-private-network.html)** |Compatible |Compatible |Ámsterdam, RAE de Hong Kong, Londres, Los Angeles, Osaka, Singapur, Sídney, Tokio, Washington DC |
| **[NTT EAST](https://flets.com/cloudgateway/crossconnect/)** |Compatible |Compatible |Tokio |
| **[NTT SmartConnect](https://cloud.nttsmc.com/cxc/azure.html)** |Compatible |Compatible |Osaka |
| **[Optus](https://www.optus.com.au/enterprise/networking/network-connectivity/express-link)** |Compatible |Compatible |Melbourne, Sidney |
| **[Orange](https://www.orange-business.com/en/products/business-vpn-galerie)** |Compatible |Compatible |Ámsterdam, Fráncfort, RAE de Hong Kong, Johannesburgo, Londres, París, Sao Paulo, Silicon Valley, Singapur, Sidney, Tokio, Washington DC |
| **[Orixcom](https://www.orixcom.com/cloud-solutions/)** | Compatible | Compatible | Dubai2 |
| **[PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)** |Compatible |Compatible |Chicago, Silicon Valley, Washington DC |
| **[PCCW Global Limited](https://consoleconnect.com/clouds/#azureRegions)** |Compatible |Compatible |Chicago, RAE de Hong Kong, Londres |
| **[Sejong Telecom](https://www.sejongtelecom.net/en/pages/service/cloud_ms)** |Compatible |Compatible |Seúl |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Compatible |Compatible |Chennai (Madrás), Mumbai (Bombay)2 |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Compatible |Compatible |Singapur, Singapur2 |
| **[Softbank](https://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Compatible |Compatible |Osaka, Tokyo |
| **[Spark NZ](https://www.sparkdigital.co.nz/solutions/connectivity/cloud-connect/)** |Compatible |Compatible |Auckland, Sídney |
| **[Sprint](https://business.sprint.com/solutions/cloud-networking/)** |Compatible |Compatible |Chicago, Silicon Valley, Washington DC |
| **[Tata Communications](https://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Compatible |Compatible |Ámsterdam, Chennai, RAE de Hong Kong, Londres, Mumbai, Sao Paulo, Silicon Valley, Singapur, Washington DC |
| **Telecity Group** |Compatible |Compatible |Ámsterdam |
| **[Telefónica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Compatible |Compatible |Ámsterdam, Sao Paulo |
| **[Telehouse - KDDI](https://www.telehouse.net/solutions/cloud-services/cloud-link)**  |Compatible |Compatible |Londres |
| **Telenor** |Compatible |Compatible |Ámsterdam y Londres |
| **[Telia Carrier](https://teliacarrier.com/our-services/connectivity/cloud-connect.html?title=Cloud%20Connect)** | Compatible | Compatible |Ámsterdam, Chicago, Dallas, Hong Kong, Londres, París, Silicon Valley, Estocolmo, Washington DC |
| **Telmex Uninet**| Compatible | Compatible | Dallas |
| **[Telstra Corporation](https://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Compatible |Compatible |Melbourne, Singapur, Sidney |
| **[Telus](https://www.telus.com)** |Compatible |Compatible |Montreal, Seattle, Toronto |
| **[Teraco](https://www.teraco.co.za/services/africa-cloud-exchange/)** |Compatible |Compatible |Ciudad del cabo, Johannesburgo |
| **[TIME dotCom](https://www.time.com.my/enterprise/connectivity/cloud-interconnect)** | Compatible | Compatible | Kuala Lumpur |
| **[Transtelco](https://transtelco.net/enterprise-services/)** |Compatible |Compatible |Dallas, +Los Ángeles|
| **[UOLDIVEO](https://www.uoldiveo.com.br/)** |Compatible |Compatible |Sao Paulo |
| **[Verizon](https://enterprise.verizon.com/products/network/application-enablement/secure-cloud-interconnect/)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, RAE de Hong Kong, Londres, Mumbai, Silicon Valley, Singapur, Sídney, Tokio, Toronto, Washington DC |
| **[Vocus Group NZ](https://www.vocus.co.nz/business/cloud-data-centres)** | Compatible | Compatible | Auckland, Sídney |
| **[Vodafone](https://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Compatible |Compatible |Ámsterdam2, Londres, Singapur |
| **Vodafone Idea** | Compatible | Compatible | Bombay, Bombay2 |
| **[Zayo](https://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Compatible |Compatible |Ámsterdam, Chicago, Dallas, Denver, Londres, Los Ángeles, Montreal, Nueva York, París, Seattle, Silicon Valley, Toronto, Washington DC |

 **+** indica próximamente

### <a name="national-cloud-environment"></a>Entorno de nube nacional

### <a name="us-government-cloud"></a>Nube del gobierno de Estados Unidos

| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[AT&amp;T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Compatible |Compatible |Chicago, Phoenix, Washington DC |
| **[CenturyLink Cloud Connect](https://www.centurylink.com/cloudconnect)** |Compatible |Compatible |Nueva York, Phoenix, San Antonio, Washington DC |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Compatible |Compatible |Chicago, Dallas, Nueva York, Seattle, Silicon Valley, Washington D.C. |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Compatible |Compatible |Chicago, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible | Compatible | Chicago, Dallas, San Antonio, Seattle, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Compatible |Compatible |Chicago, Dallas, Nueva York, Silicon Valley, Washington DC |

### <a name="china"></a>China

| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **China Telecom** |Compatible |No compatible |Beijing, Beijing2, Shanghái, Shanghái2 |
| **[GDS](http://en.gds-services.com/news_detail/newsId=21.html)** |Compatible |No compatible |Beijing2, Shanghai2 |

Para obtener más información, consulte [ExpressRoute en China](http://www.windowsazure.cn/home/features/expressroute/).

### <a name="germany"></a>Alemania

| **Proveedor de servicios** | **Microsoft Azure** | **Office 365** | **Ubicaciones** |
| --- | --- | --- | --- |
| **[Colt](https://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Compatible |No compatible |Fráncfort |
| **[Equinix](https://www.equinix.com/partners/microsoft-azure/)** |Compatible |No compatible |Fráncfort |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Compatible |No compatible |Berlín |
| **Interxion** |Compatible |No compatible |Fráncfort |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Compatible  | No compatible | Berlín |
| **T-Systems** |Compatible |No compatible |Berlín |

## <a name="connectivity-through-exchange-providers"></a>Conectividad a través de proveedores de intercambio

Si su proveedor de conectividad no aparece en la lista de las secciones anteriores, puede crear una conexión.

* Consulte con el proveedor de conectividad para ver si existe una conexión con cualquiera de los intercambios de la tabla anterior. Puede comprobar los vínculos siguientes para recopilar más información sobre los servicios ofrecidos por proveedores de Exchange. Varios proveedores de conectividad ya están conectados a los intercambios de Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/products/interconnection/cloud-connect/)
  * [IX Reach](https://www.ixreach.com/partners/cloud-partners/microsoft-azure/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NextDC](https://www.nextdc.com/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/) 
* Haga que el proveedor de conectividad amplíe su red a la ubicación de emparejamiento que elija.
  * Asegúrese de que su proveedor de conectividad amplíe la conectividad con una alta disponibilidad para que no haya ningún punto único de error.
* Solicitar un circuito ExpressRoute con el intercambio como proveedor de conectividad para conectarse a Microsoft.
  * Siga los pasos de la sección [Creación de un circuito ExpressRoute](expressroute-howto-circuit-classic.md) para configurar la conectividad.

## <a name="connectivity-through-additional-service-providers"></a>Conectividad a través de proveedores de servicios adicionales

| **Proveedor de conectividad** | **Exchange** | **Ubicaciones** |
| --- | --- | --- |
| **[1CLOUDSTAR](https://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapur |
| **[Airgate Technologies, Inc.](https://www.airgate.ca/expressroute)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](https://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |Nueva York, Washington DC |
| **[Arteria Networks Corporation](https://www.arteria-net.com/business/service/cloud/sca/)** |Equinix |Tokio |
| **[Axtel](https://alestra.mx/landing/expressrouteazure/)** |Equinix |Dallas|
| **[Beanfield Metroconnect](https://www.beanfield.com/cloud-exchange/)** |Megaport |Toronto|
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londres |
| **[BICS](https://bics.com/bics-solutions-suite/cloud-connect/)** | Equinix | Ámsterdam, Fráncfort, Londres, Singapur y Washington DC |
| **[BroadBand Tower, Inc.](https://www.bbtower.co.jp/product-service/data-center/network/dcconnect-for-azure/)** | Equinix | Tokio |
| **[C3ntro Telecom](https://www.c3ntro.com/data/express-route)** | Equinix, Megaport | Dallas |
| **[Chief](https://www.chief.com.tw/dispPageBox/ct.aspx?ddsPageID=ENCLOUDSERVICE&dbid=4852937342)** | Equinix | RAE de Hong Kong |
| **[Cinia](https://www.cinia.fi/en/services/connectivity-services/direct-public-cloud-connection.html)** | Equinix, Megaport | Fráncfort, Hamburgo |
| **[CloudXpress](https://www2.telenet.be/fr/business/produits-services/internet/cloudxpress/)** | Equinix | Ámsterdam | 
| **[CMC Telecom]( https://cmctelecom.vn/san-pham/value-added-service-and-it/cmc-telecom-cloud-express-en/)** | Equinix | Singapur | 
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[CoreAzure](http://coreazure.com/expressroute)**| Equinix | Londres |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)**| Equinix | Dallas, Silicon Valley, Washington DC |
| **[Crown Castle](https://fiber.crowncastle.com/solutions/added/cloud-connect)**| Equinix | Atlanta, Chicago, Dallas, Los Ángeles, Nueva York, Washington DC |
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](https://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | London, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Ámsterdam |
| **[Exponencial E](https://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londres |
| **[Fastweb S.p.A](https://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Ámsterdam |
| **[Fibrenoire](https://www.fibrenoire.ca/en/cloudextn)** | Megaport | Ciudad de Quebec |
| **[Gtt Communications Inc](https://www.gtt.net)** |Equinix | Washington DC |
| **[Gulf Bridge International](https://www.gbiinc.com/microsoft-azure-expressroute/)** | Equinix | Ámsterdam |
| **[HSO](https://www.hso.co.uk/products/cloud-direct)** |Equinix | Londres, Slough |
| **[IVedha Inc](http://www.ivedha.com/cloud/manage-azure-cloud/express-route-4/)**| Equinix | Toronto |
| **[Kaalam Telecom Bahrain B.S.C](http://www.kalaam-telecom.com/en/inbusiness/expressroute.html)**| Level 3 Communications |Ámsterdam |
| **LGA Telecom** |Equinix |Singapur|
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |RAE de Hong Kong 
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sidney |
| **[MainOne](https://www.mainone.net/services/connectivity/cloud-connect/)** |Equinix | Ámsterdam |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[MTN](https://www.mtnbusiness.com/en/enterprise/Pages/microsoft-express-route.aspx)** | Teraco | Ciudad del cabo, Johannesburgo |
| **[NexGen Networks](https://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londres |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Ámsterdam, Fráncfort |
| **[POST Telecom Luxembourg](https://www.teralinksolutions.com/cloud-connectivity/cloudbridge-to-azure-expressroute/)**|Equinix | Ámsterdam |
| **[Proximus](https://www.proximus.be/en/id_b_cl_proximus_external_cloud_connect/companies-and-public-sector/discover/magazines/expert-blog/proximus-external-cloud-connect.html)**|Equinix | Ámsterdam, Dublín, Londres, París |
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Fráncfort |  
| **Rogers** | Cologix y Equinix | Montreal, Toronto |
| **[Spectrum Enterprise](https://enterprise.spectrum.com/services/cloud/cloud-connect.html)** | Equinix | Chicago, Dallas, Los Ángeles, Nueva York, Silicon Valley | 
| **[Tamares Telecom](https://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londres | 
| **[TDC Erhverv](https://tdc.dk/Produkter/cloudaccessplus)** | Equinix | Ámsterdam | 
| **[Telecom Italia Sparkle](https://www.tisparkle.com/our-platform/corporate-platform/sparkle-cloud-connect#catalogue)**| Equinix | Ámsterdam |
| **[Telekom Deutschland GmbH](https://cloud.telekom.de/de/infrastruktur/managed-it-services/managed-hybrid-infrastructure-mit-microsoft-azure)** | Interxion | Ámsterdam, Fráncfort |
| **[Telia](https://www.telia.se/foretag/losningar/produkter-tjanster/datanet)** | Equinix | Ámsterdam |
| **[ThinkTel](https://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapur |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Equinix | Sao Paulo |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | Nueva York |
| **[Windstream](https://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **[X2nsat Inc.](https://www.x2nsat.com/expressroute/)** |Coresite |Silicon Valley, Silicon Valley 2|
| **Zain** |Equinix |Londres|
| **[Zertia](https://www.zertia.es)**| Nivel 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Cologix y Equinix | Montreal, Toronto |

## <a name="connectivity-through-datacenter-providers"></a>Conectividad a través de proveedores de centro de datos

| **Proveedor** | **Exchange** |
| --- | --- |
| **[CyrusOne](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/cloud-connectivity-reaching-amazon-microsoft-google-and-more/microsoft-azure-expressroute/?doing_wp_cron=1498512235.6733090877532958984375)** | Megaport, PacketFabric |
| **[Cyxtera](https://www.cyxtera.com/data-center-services/interconnection)** | Megaport, PacketFabric |
| **[Databank](https://www.databank.com/platforms/connectivity/cloud-direct-connect/)** | Megaport |
| **[DataFoundry](https://www.datafoundry.com/services/cloud-connect/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | IX Reach, Megaport PacketFabric |
| **[EdgeConnex](https://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport, PacketFabric |
| **[Flexential](https://www.flexential.com/connectivity/cloud-connect-microsoft-azure-expressroute)** | IX Reach, Megaport, PacketFabric |
| **[QTS Data Centers](https://www.qtsdatacenters.com/hybrid-solutions/connectivity/azure-cloud )** | Megaport, PacketFabric |
| **[Stream Data Centers]( https://www.streamdatacenters.com/products-services/network-cloud/ )** | Megaport |
| **[Centros de datos de RagingWire](https://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | IX Reach, Megaport, PacketFabric |
| **[vXchnge](https://www.vxchnge.com/colocation-services/interconnection)** | IX Reach, Megaport |
| **[Centros de datos de T5](https://t5datacenters.com/network-cloud-connect/)** | IX Reach |

## <a name="connectivity-through-national-research-and-education-networks-nren"></a>Conectividad a través de redes nacionales de investigación y educación (NREN)

| **Proveedor**|
| --- |
| **AARNET**| 
| **DeIC, a través de GÉANT**|
| **GARR, a través de GÉANT**|
| **GÉANT**|
| **HEAnet a través de GÉANT**|
| **Internet2**|
| **JISC**|
| **RedIRIS a través de GÉANT**|
| **SINET**|
| **Surfnet a través de GÉANT**|

* Si su proveedor de conectividad no aparece aquí, compruebe si están conectados a alguno de los proveedores de intercambio de ExpressRoute enumerados anteriormente.

## <a name="expressroute-system-integrators"></a>Integradores de sistemas de ExpressRoute
Habilitar la conectividad privada para la adaptación a sus necesidades puede ser complicado según la escala de la red. Puede trabajar con cualquiera de los integradores de sistemas que aparecen en la tabla siguiente para ayudarle con la incorporación a ExpressRoute.

| **Integrador de sistemas** | **Continente** |
| --- | --- |
| **[Altogee](https://altogee.be/diensten/express-route/)** | Europa |
| **[Avanade Inc.](https://www.avanade.com/)** | Asia, Europa, Norteamérica y Sudamérica |
| **[Bright Skies GmbH](https://bskies.io/expressroute)** | Europa
| **[Ensyst](https://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](https://www.equinix.com/services/consulting/)** | Norteamérica |
| **[FlexManage](https://www.flexmanage.com/cloud)** | Norteamérica |
| **[Lightstream](https://www.lightstream.tech/partners/microsoft-azure/)** | Norteamérica |
| **[The IT Consultancy Group](https://itconsult.com.au/)** | Australia |
| **[MOQdigital](https://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Alemania) |
| **[Nelite](https://www.nelite.com/offres-services/)** | Europa |
| **[Firma nueva](https://newsignature.com/technologies/express-route/)** | Europa |
| **[OneAs1a](https://www.oneas1a.com/connectivity.html)** | Asia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](https://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Norteamérica |
| **[Presidio](https://info.presidio.com/microsoft-azure-expressroute)** | Norteamérica |
| **[sol-tec](https://www.sol-tec.com/services)** | Europa |
| **[Venha Pra Nuvem](https://venhapranuvem.com.br/)** | Sudamérica |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |

## <a name="next-steps"></a>Pasos siguientes
* Para obtener más información acerca de ExpressRoute, consulte [P+F de ExpressRoute](expressroute-faqs.md).
* Asegúrese de que se cumplen todos los requisitos previos. Consulte [Requisitos previos de ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mapa de ubicación"
