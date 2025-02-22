---
title: 'Escalado vertical de características y capacidades: Azure App Service | Microsoft Docs'
description: Aprenda a escalar verticalmente una aplicación de Azure App Service para agregar capacidad y características.
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
ms.assetid: f7091b25-b2b6-48da-8d4a-dcf9b7baccab
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/19/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: cebd6e5c120bdaa5e7dd5b681366e07087cd3498
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70069188"
---
# <a name="scale-up-an-app-in-azure-app-service"></a>Escalado de una aplicación en Azure App Service

En este artículo se muestra cómo escalar aplicaciones en Azure App Service. Existen dos flujos de trabajo para escalar aplicaciones, el escalado vertical y el escalado horizontal, y en este artículo se explica el flujo de trabajo para escalar verticalmente.

* [Escalado vertical](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): obtenga más CPU, memoria, espacio en disco y características adicionales como máquinas virtuales exclusivas, dominios y certificados personalizados, espacios de ensayo, autoescala y mucho más. Para escalar verticalmente, se cambia el plan de tarifa del plan de App Service al que pertenece la aplicación.
* [Escalado horizontal](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): se aumenta el número de instancias de máquina virtual que ejecutan la aplicación.
  Se puede escalar horizontalmente a un máximo de 20 instancias, según el plan de tarifa. [Entornos de App Service](environment/intro.md) en el nivel **Aislado** aumenta aún más el recuento de escalado horizontal a 100 instancias. Para más información sobre el escalado horizontal, consulte [Escalado del número de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md). En ese artículo aprenderá a usar el escalado automático, que consiste en escalar el número de instancias automáticamente en función de las programaciones y reglas predefinidas.

La configuración de escalado tarda solo unos segundos en aplicarse y afecta a todas las aplicaciones del [plan de App Service](../app-service/overview-hosting-plans.md).
No hay que modificar el código ni volver a implementar la aplicación.

Para obtener información de los precios y características de planes de App Service individuales, consulte [Precios de App Service](https://azure.microsoft.com/pricing/details/web-sites/).  

> [!NOTE]
> Antes de cambiar un plan de App Service con el nivel **Gratis** a uno superior, primero debe quitar los [límites de gasto](https://azure.microsoft.com/pricing/spending-limits/) vigentes de la suscripción de Azure. Para ver o cambiar opciones relativas a la suscripción a Azure App Service, vea [Suscripciones a Microsoft Azure][azuresubscriptions].
> 
> 

<a name="scalingsharedorbasic"></a>
<a name="scalingstandard"></a>

## <a name="scale-up-your-pricing-tier"></a>Escalado vertical del plan de tarifa

> [!NOTE]
> Para escalar al nivel **PremiumV2**, vea [Configure PremiumV2 tier for App Service](app-service-configure-premium-tier.md) (Configuración del nivel PremiumV2 para App Service).
>

1. En el explorador, abra [Azure Portal][portal].

1. En el menú de la izquierda de la página de la aplicación de App Service, seleccione **Escalar verticalmente (plan de App Service)** .
   
3. Elija el nivel y, después, seleccione **Aplicar**. Seleccione de entre las distintas categorías (por ejemplo, **Producción**) y, también, **Ver opciones adicionales** para mostrar más niveles.
   
    ![Desplácese para escalar verticalmente la aplicación de Azure.][ChooseWHP]

    Una vez completada la operación, aparecerá una ventana emergente de notificación con una marca de verificación verde de operación correcta.

<a name="ScalingSQLServer"></a>

## <a name="scale-related-resources"></a>Recursos relacionados con el escalado
Si su aplicación depende de otros servicios, como Azure SQL Database o Azure Storage, también puede escalar verticalmente estos recursos por separado. Estos recursos no se administran a través del plan de App Service.

1. En la página **Información general** de la aplicación, seleccione el vínculo **Grupo de recursos**.
   
    ![Escalado vertical de recursos relacionados con la aplicación de Azure](./media/web-sites-scale/RGEssentialsLink.png)

2. En la parte **Resumen** de la página **Grupo de recursos**, seleccione el recurso que quiera escalar. En la siguiente captura de pantalla se muestra un recurso de SQL Database.
   
    ![Navegue a la página del grupo de recursos para escalar verticalmente la aplicación de Azure.](./media/web-sites-scale/ResourceGroup.png)

    Para escalar verticalmente el recurso relacionado, consulte la documentación del tipo de recurso en cuestión. Por ejemplo, para escalar verticalmente una sola instancia de SQL Database, vea [Escalar recursos de base de datos única en Azure SQL Database](../sql-database/sql-database-single-database-scale.md). Para escalar verticalmente un recurso de Azure Database for MySQL, vea [Escalado de recursos de MySQL](../mysql/concepts-pricing-tiers.md#scale-resources).

<a name="OtherFeatures"></a>
<a name="devfeatures"></a>

## <a name="compare-pricing-tiers"></a>Comparar planes de tarifa

Si necesita información detallada, como los tamaños de máquina virtual para cada plan de tarifa, vea [Precios de App Service](https://azure.microsoft.com/pricing/details/app-service).

Para ver una tabla de límites de servicio, presupuestos y restricciones, así como las características admitidas en cada nivel, vea [Límites de App Service](../azure-subscription-service-limits.md#app-service-limits).

<a name="Next Steps"></a>

## <a name="more-resources"></a>Más recursos

Para ver vídeos sobre cómo escalar aplicaciones de App Service, consulte los siguientes recursos:
 
* [Cuándo escalar Azure Websites: con Stefan Schackow](https://azure.microsoft.com/resources/videos/azure-web-sites-free-vs-standard-scaling/)
* [Escalación automática de Azure Websites mediante CPU o programación: con Stefan Schackow](https://azure.microsoft.com/resources/videos/auto-scaling-azure-web-sites/)
* [Escalación de Azure Websites: con Stefan Schackow](https://azure.microsoft.com/resources/videos/how-azure-web-sites-scale/)

<!-- LINKS -->
[vmsizes]:https://azure.microsoft.com/pricing/details/app-service/
[SQLaccountsbilling]:https://go.microsoft.com/fwlink/?LinkId=234930
[azuresubscriptions]:https://account.windowsazure.com/subscriptions
[portal]: https://portal.azure.com/

<!-- IMAGES -->
[ChooseWHP]: ./media/web-sites-scale/scale1ChooseWHP.png
[ResourceGroup]: ./media/web-sites-scale/scale10ResourceGroup.png
[ScaleDatabase]: ./media/web-sites-scale/scale11SQLScale.png
[GeoReplication]: ./media/web-sites-scale/scale12SQLGeoReplication.png
