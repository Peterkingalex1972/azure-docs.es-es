---
title: Lista de comprobación de seguridad de Azure Service Fabric | Microsoft Docs
description: En este artículo se proporciona un conjunto de comprobaciones de la seguridad de Azure Service Fabric.
services: security
documentationcenter: na
author: unifycloud
manager: barbkess
editor: tomsh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2019
ms.author: tomsh
ms.openlocfilehash: c30b70d2fccb7580dcb94c2322c0ad3a52461f34
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68927721"
---
# <a name="azure-service-fabric-security-checklist"></a>Lista de comprobación de seguridad de Azure Service Fabric
En este artículo se proporciona una sencilla lista de comprobación que le ayudará a proteger el entorno de Azure Service Fabric.

## <a name="introduction"></a>Introducción
Azure Service Fabric es una plataforma de sistemas distribuidos que facilita el empaquetamiento, la implementación y la administración de microservicios escalables y confiables. Service Fabric también aborda los desafíos importantes en el desarrollo y la administración de aplicaciones en la nube. Los desarrolladores y administradores pueden evitar problemas complejos de infraestructura y centrarse en su lugar en las cargas de trabajo más exigentes y críticas que son escalables, confiables y fáciles de administrar.

## <a name="checklist"></a>Lista de comprobación
Utilice la siguiente lista de comprobación como ayuda para asegurarse de que no ha pasado por alto ningún problema importante en la administración y configuración de una solución segura de Azure Service Fabric.


|Categoría de la lista de comprobación| DESCRIPCIÓN |
| ------------ | -------- |
|[Control de acceso basado en roles (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md) | <ul><li>El control de acceso permite al administrador de clústeres limitar el acceso a determinadas operaciones de clúster para distintos grupos de usuarios, lo que aumenta la seguridad del clúster.</li><li>Los administradores tienen acceso total a las capacidades de administración (incluidas las capacidades de lectura y escritura). </li><li> Los usuarios, de forma predeterminada, tienen acceso de solo lectura a las capacidades de administración (por ejemplo, capacidad de consulta) y a la capacidad para resolver las aplicaciones y los servicios.</li></ul>|
|[Certificados X.509 y Service Fabric](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Los [certificados](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/working-with-certificates) usados en clústeres que ejecutan cargas de trabajo de producción deben crearse mediante un servicio de certificados de Windows Server configurado correctamente u obtenerse de una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) autorizada.</li><li>No use nunca en producción [certificados temporales o de pruebas](https://docs.microsoft.com/dotnet/framework/wcf/feature-details/how-to-create-temporary-certificates-for-use-during-development) creados con herramientas como [MakeCert.exe](https://msdn.microsoft.com/library/windows/desktop/aa386968.aspx). </li><li>Puede usar un [certificado autofirmado](../../service-fabric/service-fabric-windows-cluster-x509-security.md), pero solo para los clústeres de prueba y no para los que se encuentran en fase de producción.</li></ul>|
|[Seguridad de clúster](../../service-fabric/service-fabric-cluster-security.md) | <ul><li>Entre los escenarios de seguridad de clúster se incluye la seguridad de nodo a nodo, la seguridad de cliente a nodo y el [control de acceso basado en roles (RBAC)](../../service-fabric/service-fabric-cluster-security-roles.md).</li></ul>|
|[Autenticación del clúster](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Autentica la [comunicación de nodo a nodo](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/service-fabric/service-fabric-cluster-security.md) para la federación del clúster. </li></ul>|
|[Autenticación de servidor](../../service-fabric/service-fabric-cluster-creation-via-arm.md) | <ul><li>Autentica los [puntos de conexión de administración del clúster](../../service-fabric/service-fabric-cluster-creation-via-portal.md) en un cliente de administración.</li></ul>|
|[Seguridad de las aplicaciones](../../service-fabric/service-fabric-cluster-creation-via-arm.md)| <ul><li>Cifrado y descifrado de los valores de configuración de aplicación.</li><li>   Cifrado de datos entre nodos durante la replicación.</li></ul>|
|[Certificado de clúster](../../service-fabric/service-fabric-windows-cluster-x509-security.md) | <ul><li>Este certificado es necesario para proteger la comunicación entre los nodos de un clúster.</li><li>    Establezca la huella digital del certificado principal en la sección Thumbprint y la del secundario en la variable ThumbprintSecondary.</li></ul>|
|[ServerCertificate](../../service-fabric/service-fabric-windows-cluster-x509-security.md)| <ul><li>Este certificado se presenta al cliente cuando intenta conectarse a este clúster. Puede utilizar dos certificados de servidor diferentes, uno principal y otro secundario para la actualización.</li></ul>|
|ClientCertificateThumbprints| <ul><li>Se trata de un conjunto de certificados que desea instalar en los clientes autenticados. </li></ul>|
|ClientCertificateCommonNames| <ul><li>Establezca el nombre común del primer certificado de cliente para CertificateCommonName. CertificateIssuerThumbprint es la huella digital del emisor de este certificado. </li></ul>|
|ReverseProxyCertificate| <ul><li>Se trata de un certificado opcional que se puede especificar si desea proteger el [proxy inverso](../../service-fabric/service-fabric-reverseproxy.md). </li></ul>|
|Key Vault| <ul><li>Se usa para administrar certificados para clústeres de Service Fabric en Azure.  </li></ul>|


## <a name="next-steps"></a>Pasos siguientes

- [Procedimientos recomendados de seguridad de Service Fabric](service-fabric-best-practices.md)
- [Proceso de actualización del clúster de Service Fabric y expectativas del usuario](../../service-fabric/service-fabric-cluster-upgrade.md)
- [Administración de aplicaciones de Service Fabric en Visual Studio](../../service-fabric/service-fabric-manage-application-in-visual-studio.md).
- [Introducción al modelo de mantenimiento de Service Fabric](../../service-fabric/service-fabric-health-introduction.md).
