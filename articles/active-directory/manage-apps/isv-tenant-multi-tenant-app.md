---
title: Crear un inquilino de Azure para una aplicación multiinquilino
description: Guía para proveedores de software independientes sobre la integración con Azure Active Directory
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeeds
ms.collection: M365-identity-device-management
ms.openlocfilehash: 69cc625500af60a753ad8e7db0363954088f3307
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659450"
---
# <a name="create-an-azure-tenant-for-a-multi-tenant-application"></a>Crear un inquilino de Azure para una aplicación multiinquilino  

Para proporcionar acceso a la aplicación multiinquilino, debe crear un inquilino de Azure Active Directory a fin de registrar la aplicación y habilitar la federación de identidades del cliente. Vea [Selección del protocolo de federación correcto para una aplicación multiinquilino](isv-choose-multi-tenant-federation.md). Este inquilino permite probar la aplicación y la federación en un entorno similar a los entornos de Azure AD de los clientes.

## <a name="costs-of-hosting-a-multi-tenant-application"></a>Costos de hospedaje de una aplicación multiinquilino

Azure Active Directory se ofrece en tres planes de tarifa: Gratis, Básico y Premium. [Vea la comparación de características detallada](https://azure.microsoft.com/pricing/details/active-directory/).

Puede crear la suscripción de Azure y Azure Active Directory de forma gratuita y usar características básicas.

## <a name="create-your-tenant"></a>Crear el inquilino

1. Cree el inquilino. Vea [Configuración de un entorno de desarrollo](../develop/quickstart-create-new-tenant.md).

2. Habilite y pruebe el acceso de inicio de sesión único a la aplicación,

   a. **En aplicaciones de OIDC u Oath**, [registre la aplicación](../develop/quickstart-register-app.md) como una aplicación multiinquilino. Seleccione la opción Cuentas en cualquier directorio organizativo y cuentas Microsoft personales en Tipos de cuenta admitidos.

   b. **En aplicaciones basadas en SAML y WS-Fed**, [configure el inicio de sesión único basado en SAML](configure-single-sign-on-non-gallery-applications.md) mediante una plantilla genérica de SAML en Azure AD.

También puede [convertir una aplicación de un solo inquilino en multiinquilino](../develop/howto-convert-app-to-be-multi-tenant.md) si fuera necesario.

## <a name="next-steps"></a>Pasos siguientes

[Integración del inicio de sesión único en la aplicación](isv-sso-content.md)
