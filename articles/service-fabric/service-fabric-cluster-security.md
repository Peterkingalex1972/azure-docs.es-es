---
title: Protección de un clúster de Azure Service Fabric | Microsoft Docs
description: Información acerca de los escenarios de seguridad para un clúster de Azure Service Fabric y las diferentes tecnologías que se pueden usar para implementarlos.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 26b58724-6a43-4f20-b965-2da3f086cf8a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/14/2018
ms.author: atsenthi
ms.openlocfilehash: 6ee7c71a66488e9636752676d68a79fdfaf855cb
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599832"
---
# <a name="service-fabric-cluster-security-scenarios"></a>Escenarios de seguridad de los clústeres de Service Fabric
Un clúster de Azure Service Fabric es un recurso que usted posee. Tiene la responsabilidad de proteger los clústeres para impedir que usuarios no autorizados se conecten a ellos. Proteger el clúster es especialmente importante si en él se ejecutan cargas de trabajo de producción. Aunque es posible crear un clúster sin protección, si este expone los puntos de conexión de administración a Internet público, podrían conectarse a él usuarios anónimos. Los clústeres sin protección no se admiten para cargas de trabajo de producción. 

Este artículo es una introducción a escenarios de seguridad para los clústeres de Azure e independientes, y las distintas tecnologías que se pueden usar para implementarlos:

* Seguridad de nodo a nodo
* Seguridad de cliente a nodo
* Control de acceso basado en rol (RBAC)

## <a name="node-to-node-security"></a>Seguridad de nodo a nodo
La seguridad de nodo a nodo ayuda a proteger la comunicación entre las máquinas virtuales o los equipos de un clúster. Este escenario de seguridad garantiza que solo los equipos autorizados a unirse al clúster pueden participar en el hospedaje de aplicaciones y servicios en el clúster.

![Diagrama de comunicación de nodo a nodo][Node-to-Node]

Los clústeres que se ejecutan en Azure y los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx) para los equipos con Windows Server.

### <a name="node-to-node-certificate-security"></a>Seguridad basada en certificados de nodo a nodo
Service Fabric usa certificados de servidor X.509 que se especifican como parte de las configuraciones del tipo de nodo al crear un clúster. Al final de este artículo se proporciona una introducción a estos certificados y a cómo se pueden adquirir o crear.

Configure la seguridad basada en certificados al crear el clúster, mediante Azure Portal, las plantillas de Azure Resource Manager o una plantilla JSON independiente. El comportamiento predeterminado del SDK de Service Fabric es implementar e instalar el certificado con el certificado que vaya a expirar más tarde; el comportamiento clásico posibilitaba definir los certificados principales y secundarios a fin de permitir las sustituciones iniciadas manualmente. No se recomienda usarse con la nueva funcionalidad. Los certificados principales que se usarán serán los que tengan una fecha de expiración más tardía y deberían ser diferentes de los certificados de cliente de solo lectura y de los de cliente de administración que establezca para la [seguridad de cliente a nodo](#client-to-node-security).

Para aprender a configurar la seguridad basada en certificados en un clúster para Azure, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para aprender a configurar la seguridad basada en certificados en los clústeres para uno de Windows Server independiente, consulte [Protección de un clúster independiente en Windows mediante certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="node-to-node-windows-security"></a>Seguridad de Windows de nodo a nodo
Para aprender a configurar la seguridad de Windows para clústeres de Windows Server independientes, consulte [Proteger un clúster independiente en Windows mediante la seguridad de Windows](service-fabric-windows-cluster-windows-security.md).

## <a name="client-to-node-security"></a>Seguridad de cliente a nodo
La seguridad de cliente a nodo autentica los clientes y ayuda a proteger la comunicación entre un cliente y los nodos individuales del clúster. Este tipo de seguridad ayuda a garantizar que solo los usuarios autorizados accedan al clúster y a las aplicaciones implementadas en él. Los clientes se identifican exclusivamente mediante sus credenciales de seguridad de Windows o las credenciales de seguridad del certificado.

![Diagrama de comunicación de cliente a nodo][Client-to-Node]

Los clústeres que se ejecutan en Azure y los independientes que se ejecutan en Windows pueden utilizar una [seguridad basada en certificados](https://msdn.microsoft.com/library/ff649801.aspx) o la [seguridad de Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Seguridad basada en certificados de cliente a nodo
Configure la seguridad basada en certificados de cliente a nodo al crear el clúster, mediante Azure Portal, las plantillas de Resource Manager o una plantilla JSON independiente. Para crear el certificado, especifique un certificado de cliente de administración o de cliente de usuario. Es recomendable que los certificados de cliente de administración y de cliente de usuario que especifique sean diferentes de los certificados principales y secundarios que determine para la [seguridad de nodo a nodo](#node-to-node-security). De forma predeterminada, los certificados de clúster para la seguridad de nodo a nodo se agregan a la lista de certificados de administración de cliente permitidos.

Los clientes que se conectan al clúster mediante el certificado de administración tienen acceso completo a las funcionalidades de administración. Los clientes que se conectan al clúster mediante el certificado de cliente de usuario de solo lectura tienen acceso de este tipo a las funcionalidades de administración. Estos certificados se usan para el control de acceso basado en rol (RBAC) que se describe más adelante en este artículo.

Para aprender a configurar la seguridad basada en certificados en un clúster para Azure, consulte [Creación de un clúster de Service Fabric con Azure Resource Manager](service-fabric-cluster-creation-via-arm.md).

Para aprender a configurar la seguridad basada en certificados en los clústeres para uno de Windows Server independiente, consulte [Protección de un clúster independiente en Windows mediante certificados X.509](service-fabric-windows-cluster-x509-security.md).

### <a name="client-to-node-azure-active-directory-security-on-azure"></a>Seguridad de Azure Active Directory de cliente a nodo en Azure
Azure AD permite a las organizaciones (conocidas como inquilinos) administrar el acceso de los usuarios a las aplicaciones. Las aplicaciones se dividen en las que tienen interfaz de usuario de inicio de sesión basada en web y las que tienen una experiencia de cliente nativa. En caso de que no haya creado un inquilino, lea [Obtención de un inquilino de Azure Active Directory][active-directory-howto-tenant].

Un clúster de Service Fabric ofrece diversos puntos de entrada a su funcionalidad de administración, como [Service Fabric Explorer][service-fabric-visualizing-your-cluster] y [Visual Studio][service-fabric-manage-application-in-visual-studio] basados en web. Como resultado, creará dos aplicaciones de Azure AD para controlar el acceso al clúster, una aplicación web y otra nativa.

Para los clústeres que se ejecutan en Azure también se puede proteger el acceso a los puntos de conexión de administración con Azure Active Directory (Azure AD). Para aprender a crear los artefactos de Azure AD y a rellenarlos al crear el clúster, consulte [Configuración de Azure AD para autenticar a los clientes](service-fabric-cluster-creation-setup-aad.md).

## <a name="security-recommendations"></a>Recomendaciones de seguridad
Para los clústeres de Service Fabric implementados en una red pública hospedada en Azure, la recomendación para la autenticación mutua de cliente a nodo es:
*   Usar Azure Active Directory para la identidad del cliente
*   Un certificado para la identidad del servidor y el cifrado SSL de la comunicación HTTP

Para los clústeres de Service Fabric implementados en una red pública hospedada en Azure, la recomendación para la seguridad de nodo a nodo es usar un certificado de clúster para autenticar los nodos. 


Para los clústeres de Windows Server independientes, si tiene Windows Server 2012 R2 y Windows Active Directory, se recomienda la seguridad de Windows con las cuentas de servicio administradas de grupo. De lo contrario, use la seguridad de Windows con cuentas de Windows.

## <a name="role-based-access-control-rbac"></a>Control de acceso basado en rol (RBAC)
Puede usar el control de acceso para limitarlo a determinadas operaciones de clúster para los diferentes grupos de usuarios. Esto ayuda a que el clúster esté más protegido. Se admiten dos tipos de control de acceso para los clientes que se conectan a un clúster: rol de administrador y usuario.

Los usuarios con el rol de administrador tienen acceso total a las funcionalidades de administración (incluidas las de lectura y escritura). Los usuarios con el rol de usuario, de forma predeterminada, tienen acceso de solo lectura a las funcionalidades de administración (por ejemplo, funcionalidad de consulta). También pueden resolver aplicaciones y servicios.

Los roles de administrador y de usuario del cliente se definen al crear el clúster. Para asignar roles, proporcione identidades diferentes (por ejemplo, con certificados o Azure AD) a cada tipo de rol. Para más información sobre la configuración del control de acceso predeterminada y cómo cambiarla, consulte [Control de acceso basado en roles para clientes de Service Fabric](service-fabric-cluster-security-roles.md).

## <a name="x509-certificates-and-service-fabric"></a>Certificados X.509 y Service Fabric
Los certificados digitales X.509 se usan habitualmente para autenticar clientes y servidores. También se usan para cifrar y firmar digitalmente los mensajes. Service Fabric usa certificados X.509 para proteger un clúster y proporcionar características de seguridad de las aplicaciones. Para más información acerca de los certificados digitales X.509, consulte [Trabajar con certificados](https://msdn.microsoft.com/library/ms731899.aspx). [Key Vault](../key-vault/key-vault-overview.md) se usa para administrar certificados para clústeres de Service Fabric en Azure.

Algunos puntos importantes que hay que tener en cuenta:

* Para crear certificados para clústeres que ejecutan cargas de trabajo de producción, use un servicio de certificados de Windows Server correctamente configurado o uno de una [entidad de certificación (CA)](https://en.wikipedia.org/wiki/Certificate_authority) autorizada.
* Absténgase de usar certificados temporales o de prueba que se crean con herramientas como MakeCert.exe en un entorno de producción.
* Puede usar un certificado autofirmado, pero solo en un clúster de prueba. No utilice un certificado autofirmado en un entorno de producción.
* Al generar la huella digital del certificado, asegúrese de generar una huella digital SHA1. SHA1 es lo que se usa al configurar las huellas digitales del certificado del cliente y del clúster.

### <a name="cluster-and-server-certificate-required"></a>Certificado de clúster y servidor (obligatorio)
Estos certificados (uno principal y, opcionalmente, uno secundario) son necesarios para proteger un clúster e impedir que se acceda a él sin autorización. Estos certificados permiten la autenticación del servidor y del clúster.

La autenticación del clúster autentica la comunicación de nodo a nodo para la federación del clúster. Solo los nodos que pueden probar su identidad con este certificado pueden unirse al clúster. La autenticación del servidor autentica los puntos de conexión de administración del clúster en un cliente de administración, de forma que este sabe que está hablando con el clúster real y no con un intermediario. Este certificado proporciona también SSL para la API de administración de HTTPS y para Service Fabric Explorer sobre HTTPS. Cuando un nodo o un cliente autentica un nodo, una de las comprobaciones iniciales se ocupa del valor del nombre común en el campo **Firmante**. Este nombre común o uno de los nombres alternativos del sujeto (SAN) del certificado deben encontrarse en la lista de nombres comunes permitidos.

El certificado debe cumplir los siguientes requisitos:

* El certificado debe contener una clave privada. Estos certificados suelen tener normalmente las extensiones .pfx o .pem  
* El certificado debe crearse para el intercambio de claves, que se pueda exportar a un archivo Personal Information Exchange (.pfx).
* El **nombre de sujeto del certificado debe coincidir con el dominio usado para acceder al clúster de Service Fabric**. Esta coincidencia es necesaria para proporcionar un certificado SSL al punto de conexión de administración HTTPS y a la utilidad Service Fabric Explorer del clúster. No puede obtener un certificado SSL de una entidad de certificación (CA) para el dominio *.cloudapp.azure.com. Debe adquirir un nombre de dominio personalizado para el clúster. Cuando solicite un certificado de una CA, el nombre de sujeto del certificado debe coincidir con el nombre del dominio personalizado del clúster.

Hay otras cuestiones que deben tenerse en cuenta:

* El campo **Asunto** puede tener varios valores. Los valores llevan un prefijo de inicialización para indicar el tipo de valor. Normalmente, la inicialización es **CN** (para *nombre común*); por ejemplo, **CN = www\.contoso.com**. 
* El campo **Firmante** puede estar en blanco. 
* Si el campo opcional **Nombre alternativo de firmante** está relleno, debe contener tanto el nombre común del certificado como una entrada por nombre alternativo de firmante. Estos se especifican como valores de **Nombre DNS**. Para aprender a generar certificados que tengan SAN, consulte [Cómo agregar un nombre alternativo del sujeto a un certificado LDAP seguro](https://support.microsoft.com/kb/931351).
* El valor del campo **Propósitos planteados** del certificado debe incluir un valor apropiado, como **Autenticación de servidor** o **Autenticación de cliente**.

### <a name="application-certificates-optional"></a>Certificados de aplicación (opcionales)
Se puede instalar un número cualquiera de certificados adicionales en un clúster para proteger la aplicación. Antes de crear el clúster, tenga en cuenta los escenarios de seguridad de las aplicaciones que requieren que se instale un certificado en los nodos, por ejemplo:

* Cifrado y descifrado de los valores de configuración de aplicación.
* Cifrado de datos entre nodos durante la replicación.

El concepto de creación de clústeres seguros es el mismo, tanto si son de Linux como de Windows.

### <a name="client-authentication-certificates-optional"></a>Certificados de autenticación de cliente (opcional)
Se puede especificar cualquier cantidad de certificados adicionales para las operaciones de cliente administrador o usuario. El cliente puede usar este certificado cuando se requiera autenticación mutua. Los certificados de cliente normalmente no los emite una entidad de certificación de terceros. En su lugar, el almacén Personal de la ubicación del usuario actual suele contener los certificados de cliente colocados ahí por una entidad de certificación raíz. El certificado debe tener como **Propósitos planteados** el valor de **Autenticación de cliente**.  

De forma predeterminada, el certificado de clúster tiene privilegios de cliente administrador. Estos certificados de cliente adicionales no deben instalarse en el clúster, pero se especifican como que se permiten en la configuración del clúster.  Sin embargo, los certificados de cliente deben estar instalados en las máquinas cliente para conectarse al clúster y realizar cualquier operación.

> [!NOTE]
> Todas las operaciones de administración en el clúster de Service Fabric requieren certificados de servidor. No se pueden usar certificados de cliente para la administración.

## <a name="next-steps"></a>Pasos siguientes
* [Creación de un clúster en Azure con una plantilla de Resource Manager](service-fabric-cluster-creation-via-arm.md) 
* [Creación de un clúster mediante Azure Portal](service-fabric-cluster-creation-via-portal.md)

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png

[active-directory-howto-tenant]:../active-directory/develop/quickstart-create-new-tenant.md
[service-fabric-visualizing-your-cluster]: service-fabric-visualizing-your-cluster.md
[service-fabric-manage-application-in-visual-studio]: service-fabric-manage-application-in-visual-studio.md
