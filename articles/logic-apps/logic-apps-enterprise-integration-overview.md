---
title: 'Información general sobre la integración empresarial B2B: Azure Logic Apps | Microsoft Docs'
description: Compilación de flujos de trabajo B2B automatizados para soluciones de integración empresarial con Azure Logic Apps y Enterprise Integration Pack
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: dd517c4d-1701-4247-b83c-183c4d8d8aae
ms.date: 09/08/2016
ms.openlocfilehash: 8c0e47f5bed0799b8536cecb38a85ed76185d0cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60845838"
---
# <a name="overview-b2b-enterprise-integration-scenarios-in-azure-logic-apps-with-enterprise-integration-pack"></a>Información general: Escenarios de integración empresarial B2B en Azure Logic Apps con Enterprise Integration Pack

Si desea obtener flujos de trabajo de negocio a negocio (B2B) y una comunicación sin problemas con Azure Logic Apps, puede habilitar escenarios de integración empresarial con la solución basada en la nube de Microsoft llamada Enterprise Integration Pack. Las organizaciones pueden intercambiar mensajes electrónicamente, incluso si usan formatos y protocolos diferentes. El paquete transforma distintos formatos a un formato que pueden interpretar y procesar los sistemas de las organizaciones. Las organizaciones pueden intercambiar mensajes mediante protocolos estándar del sector, entre ellos [AS2](../logic-apps/logic-apps-enterprise-integration-as2.md), [X12](logic-apps-enterprise-integration-x12.md) y [EDIFACT](../logic-apps/logic-apps-enterprise-integration-edifact.md). También puede proteger los mensajes con cifrado y firmas digitales.

Si está familiarizado con BizTalk Server o Microsoft Azure BizTalk Services, le resultará sencillo utilizar las características de Enterprise Integration Pack, ya que casi todos los conceptos son similares. Una diferencia importante es que Enterprise Integration Pack utiliza las cuentas de integración para simplificar la administración y el almacenamiento de los artefactos empleados en las comunicaciones B2B. 

En cuanto a la arquitectura, Enterprise Integration Pack se basa en "cuentas de integración". Estas cuentas son contenedores basados en la nube que almacenan todos sus artefactos, como esquemas, asociados, certificados, mapas y contratos. Puede usar estos artefactos para diseñar, implementar y mantener sus aplicaciones B2B y también para compilar flujos de trabajo B2B para aplicaciones lógicas. Antes de poder usar estos artefactos, primero debe vincular la cuenta de integración a la aplicación lógica. Después de eso, la aplicación lógica podrá acceder a los artefactos de la cuenta de integración.

## <a name="why-should-you-use-enterprise-integration"></a>¿Por qué se debe utilizar Enterprise Integration Pack?

* Gracias a Enterprise Integration, podrá almacenar todos los artefactos en un solo lugar: la cuenta de integración.
* Puede compilar flujos de trabajo B2B e integrarlos con aplicaciones de software como servicio (SaaS) de terceros, aplicaciones locales y aplicaciones personalizadas mediante el motor de Azure Logic Apps y todos sus conectores.
* Puede crear código personalizado para las aplicaciones lógicas con Azure Functions.

## <a name="how-to-get-started-with-enterprise-integration"></a>¿Cómo se puede empezar a disfrutar de Enterprise Integration Pack?

Puede compilar y administrar aplicaciones B2B con Enterprise Integration Pack mediante el Diseñador de aplicación lógica en **Azure Portal**. También puede administrar las aplicaciones lógicas con [PowerShell](https://docs.microsoft.com/powershell/module/az.logicapp).

A continuación, estos son los pasos de alto nivel que debe llevar a cabo para crear aplicaciones en Azure Portal:

![Imagen de información general](media/logic-apps-enterprise-integration-overview/overview-0.png)  

## <a name="what-are-some-common-scenarios"></a>¿Cuáles son algunos escenarios comunes?

Enterprise Integration Pack admite estos estándares del sector:

* EDI: intercambio electrónico de datos
* EAI: Enterprise Application Integration

## <a name="heres-what-you-need-to-get-started"></a>Requisitos para poder comenzar

* Tener una suscripción de Azure con una cuenta de integración
* Disponer de Visual Studio 2015 para crear esquemas y asignaciones
* [Herramientas de integración de empresas de Microsoft Azure Logic Apps para la versión 2.0 de Visual Studio 2015](https://aka.ms/vsmapsandschemas)  

## <a name="try-it-now"></a>Pruébelo ya

[Implemente una aplicación lógica de envío y recepción de AS2 de ejemplo completamente operativa](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-as2-send-receive) que use las características B2B de Azure Logic Apps.

## <a name="learn-more"></a>Más información
* [Contratos](../logic-apps/logic-apps-enterprise-integration-agreements.md "Información sobre los contratos de Enterprise Integration")
* [Escenarios de negocio a negocio (B2B)](../logic-apps/logic-apps-enterprise-integration-b2b.md "Aprenda a crear Logic Apps con características B2B.")  
* [Certificados](logic-apps-enterprise-integration-certificates.md "Información sobre los certificados de Enterprise Integration")
* [Codificación y descodificación de archivos sin formato](logic-apps-enterprise-integration-flatfile.md "Aprenda a codificar y descodificar contenido de archivos sin formato")  
* [Cuentas de Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md "Información acerca de las cuentas de integración")
* [Asignaciones](../logic-apps/logic-apps-enterprise-integration-maps.md "Información sobre las asignaciones de Enterprise Integration")
* [Asociados](logic-apps-enterprise-integration-partners.md "Información acerca de los asociados de Enterprise Integration")
* [Esquemas](logic-apps-enterprise-integration-schemas.md "Información sobre los esquemas de Enterprise Integration")
* [Validación de mensajes XML](logic-apps-enterprise-integration-xml.md "Aprenda a validar mensajes XML con Logic Apps")
* [Transformación de XML](logic-apps-enterprise-integration-transform.md "Información sobre las asignaciones de Enterprise Integration")
* [Conectores de integración de empresa](../connectors/apis-list.md "Información sobre los conectores de Enterprise Integration Pack")
* [Metadatos de la cuenta de integración](../logic-apps/logic-apps-enterprise-integration-metadata.md "Obtenga más información acerca de los metadatos de la cuenta de integración")
* [Supervisión de mensajes B2B](logic-apps-monitor-b2b-message.md "Más información acerca de la supervisión de mensajes B2B")
* [Seguimiento de mensajes B2B en los registros de Azure Monitor](logic-apps-track-b2b-messages-omsportal.md "Obtenga más información sobre cómo realizar un seguimiento de mensajes B2B en los registros de Azure Monitor")

