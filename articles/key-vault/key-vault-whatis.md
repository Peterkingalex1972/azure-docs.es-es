---
title: ¿Qué es Azure Key Vault? | Microsoft Docs
description: Obtenga información sobre cómo Azure Key Vault protege las claves criptográficas y los secretos que usan los servicios y aplicaciones en la nube.
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: 2786ec387d528e1593e2687d906060f8a2673a8c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934473"
---
# <a name="what-is-azure-key-vault"></a>¿Qué es Azure Key Vault?

Los servicios y las aplicaciones en la nube usan claves criptográficas y secretos para proteger la información. Azure Key Vault protege estos secretos y claves. Cuando se usa Key Vault, se pueden cifrar claves de autenticación, claves de cuenta de almacenamiento, claves de cifrado de datos, archivos .pfx y contraseñas mediante claves protegidas por módulos de seguridad de hardware (HSM).

Key Vault ayuda a solucionar los problemas siguientes:

- **Administración de secretos**: almacene de forma segura y controle estrechamente el acceso a tokens, contraseñas, certificados, claves de API y otros secretos.
- **Administración de claves**: cree y controle las claves de cifrado que cifran sus datos. 
- **Administración de certificados**: aprovisione, administre e implemente certificados públicos y privados de la Capa de sockets seguros y de Seguridad de la capa de transporte (SSL/TLS) para su uso con Azure y sus recursos internos conectados. 
- **Almacenamiento de secretos respaldados por HSM**: use software o dispositivos HSM validados FIPS 140-2 de nivel 2 para proteger los secretos y claves.

## <a name="basic-concepts"></a>Conceptos básicos

Azure Key Vault es una herramienta para almacenar y acceder a los secretos de forma segura. Un secreto es todo aquello a lo cual se desea controlar estrechamente el acceso, como certificados, contraseñas o claves de API. Un almacén es un grupo lógico de secretos.

Estos son otros términos importantes:

- **Tenant**: un inquilino es la organización que posee y administra una instancia específica de los servicios en la nube de Microsoft. Se usa a menudo para hacer referencia al conjunto de servicios de Azure y Office 365 de una organización.

- **Propietario del almacén**: un propietario del almacén puede crear un almacén de claves y obtener acceso completo y control sobre él. El propietario del almacén también puede configurar una auditoría para registrar quién accede a los secretos y claves. Los administradores pueden controlar el ciclo de vida de la clave. Pueden revertir a una nueva versión de la clave, realizar copias de seguridad de ella y efectuar otras tareas relacionadas.

- **Consumidor del almacén**: un consumidor del almacén puede realizar acciones en los recursos del almacén de claves si el propietario del almacén le concede acceso de consumidor. Las acciones disponibles dependen de los permisos concedidos.

- **Recursos**: un recurso es un elemento administrable que está disponible a través de Azure. Ejemplos comunes son una máquina virtual, una cuenta de almacenamiento, una aplicación web, una base de datos y una red virtual. Hay muchos más.

- **Grupo de recursos**: Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. El grupo de recursos puede incluir todos los recursos de la solución o solo aquellos que se desean administrar como grupo. Para decidir cómo asignar los recursos a los grupos de recursos, tenga en cuenta lo que más conviene a su organización.

- **Entidad de servicio**: Una entidad de servicio de Azure es una identidad de seguridad que usan las aplicaciones, los servicios y las herramientas de automatización que ha creado el usuario para acceder a recursos específicos de Azure. Se puede considerar una "identidad de usuario" (nombre de usuario y contraseña o certificado) con un rol específico y permisos estrechamente controlados. A diferencia de una identidad de usuario general, una entidad de servicio solo necesita poder realizar acciones específicas. Mejora la seguridad si le concede el nivel de permiso mínimo necesario para realizar sus tareas de administración.

- [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md): Azure AD es el servicio de Active Directory para un inquilino. Cada directorio tiene uno o varios dominios. Un directorio puede tener varias suscripciones asociadas, pero solo un inquilino.

- **Identificador de inquilino de Azure**: un identificador de inquilino es una manera única para identificar una instancia de Azure Active Directory dentro de una suscripción de Azure.

- **Identidades administradas**: Azure Key Vault proporciona una manera de almacenar de forma segura las credenciales y otras claves y secretos, pero el código tiene que autenticarse en Key Vault para recuperarlos. El uso de una identidad administrada permite solucionar este problema más fácilmente, al proporcionar a los servicios de Azure una identidad administrada automáticamente en Azure AD. Puede usar esta identidad para autenticarse Key Vault o en cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. Para obtener más información, consulte la imagen siguiente y la [introducción a las identidades administradas para los recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

    ![Diagrama sobre cómo funcionan las identidades administradas para los recursos de Azure](./media/key-vault-whatis/msi.png)

## <a name="authentication"></a>Authentication
Para realizar cualquier operación con Key Vault, deberá autenticarse en la solución primero. Hay tres formas de autenticarse en Key Vault:

- [Identidades administradas de recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md): cuando implementa una aplicación en una máquina virtual en Azure, puede asignar una identidad a la máquina virtual que tiene acceso a Key Vault. También puede asignar identidades a [otros recursos de Azure ](../active-directory/managed-identities-azure-resources/overview.md). La ventaja de este enfoque es que la aplicación o el servicio no administran la rotación del primer secreto. Azure alterna automáticamente la identidad. Este enfoque es un procedimiento recomendado. 
- **Entidad de servicio y certificado**: puede usar una entidad de servicio y un certificado asociado que tenga acceso a Key Vault. No se recomienda este enfoque porque el propietario o el desarrollador de la aplicación debe girar el certificado.
- **Entidad de servicio y secreto**: aunque puede usar una entidad de servicio y un secreto para autenticarse en Key Vault, no se recomienda que lo haga. Es difícil girar automáticamente el secreto de arranque que se utiliza para autenticarse en Key Vault.


## <a name="key-vault-roles"></a>Roles de Key Vault

Utilice la tabla siguiente para comprender mejor cómo Key Vault puede ayudar a satisfacer las necesidades de los programadores y de los administradores de seguridad.

| Role | Declaración del problema | Resuelto por Azure Key Vault |
| --- | --- | --- |
| Desarrollador para una aplicación de Azure |“Quiero escribir una aplicación para Azure que use claves para el cifrado y la firma. Pero quiero que estas claves sean externas a mi aplicación, de forma que la solución sea adecuada para aplicaciones distribuidas geográficamente. <br/><br/>Quiero que tanto las claves como los secretos estén protegidos sin tener que escribir el código. Por último, quiero poder usar fácilmente las claves y los secretos desde mis aplicaciones, y que el rendimiento sea óptimo". |√ Las claves se almacenan en un almacén y las invoca un identificador URI cuando se necesitan.<br/><br/> √ Las claves se protegen mediante Azure, para lo que se usan algoritmos estándar del sector, longitudes de clave y módulos de seguridad de hardware.<br/><br/> √ Las claves se procesan en los HSM que residen en los mismos centros de datos de Azure que la aplicaciones. Este método proporciona mayor confiabilidad y menor latencia que las claves que se encuentran en una ubicación independiente, como por ejemplo si son locales. |
| Desarrollador para software como servicio (SaaS) |“No quiero asumir la responsabilidad, ni tampoco la posible responsabilidad, de las claves y los secretos de inquilino de mis clientes. <br/><br/>Quiero que los clientes posean y administren sus claves, de modo que pueda concentrarme en mi trabajo, que es proporcionar las características de software principales". |√ Los clientes pueden importar sus propias claves a Azure y administrarlas. Cuando una aplicación SaaS necesita realizar operaciones criptográficas mediante las claves de los clientes, Key Vault las hace en nombre de la aplicación. La aplicación no ve las claves de los clientes. |
| Responsable principal de la seguridad (CSO) |“Quiero saber que nuestras aplicaciones cumplen con HSM FIPS 140-2 de nivel 2 para administración de claves segura. <br/><br/>Deseo asegurarme de que mi organización tiene el control del ciclo de vida de las claves y puedo supervisar el uso de estas. <br/><br/>Y aunque usamos varios servicios y recursos de Azure, quiero administrar las claves desde una ubicación única en Azure.” |√ Los HSM tienen la validación FIPS 140-2 de nivel 2.<br/><br/>√ Key Vault está diseñado de modo que Microsoft no pueda ver ni extraer sus claves.<br/><br/>El uso de la clave √ se registra en tiempo casi real.<br/><br/>√ El almacén proporciona una sola interfaz, independientemente del número de almacenes que tenga en Azure, las regiones que admitan y las aplicaciones que los usen. |

Cualquiera con una suscripción a Azure puede crear y usar instancias de Key Vault. Aunque Key Vault beneficia a los desarrolladores y los administradores de seguridad, el administrador de una organización que administra otros servicios de Azure puede implementarlo y administrarlo. Por ejemplo, este administrador puede iniciar sesión con una suscripción de Azure, crear un almacén para la organización en el que almacenar las claves y, después, asumir la responsabilidad de las tareas operativas, como:

- Crear o importar una clave o un secreto
- Revocar o eliminar una clave o un secreto
- Autorizar usuarios o aplicaciones para acceder al almacén de claves para que puedan administrar o usar sus claves y secretos
- Configurar el uso de claves (por ejemplo, para firmar o cifrar)
- Supervisar el uso de claves

A continuación, este administrador proporciona a los desarrolladores los URI para llamar desde sus aplicaciones. Este administrador también ofrece información de registro del uso de claves al administrador de seguridad. 

![Introducción al funcionamiento de Azure Key Vault][1]

Los desarrolladores también pueden administrar las claves directamente mediante API. Para más información, consulte la [guía para desarrolladores de Key Vault](key-vault-developers-guide.md).

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [proteger su almacén](key-vault-secure-your-key-vault.md).

<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
Azure Key Vault está disponible en la mayoría de las regiones. Para obtener más información, consulte la [página de precios de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
