---
title: Novedades Notas de la versión de Azure Active Directory | Microsoft Docs
description: Obtenga información acerca de las novedades de Azure Active Directory, como, por ejemplo, las notas de la versión más recientes, los problemas conocidos, las correcciones de errores, las funcionalidades en desuso y los próximos cambios.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f2f1f7f5d8509a28557e9704bc178be465b1ee
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648661"
---
# <a name="whats-new-in-azure-active-directory"></a>¿Cuáles son las novedades de Azure Active Directory?

>Reciba notificaciones para volver a visitar esta página y obtener actualizaciones; para ello, copie y pegue la dirección URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` en el ![icono del lector de fuentes icono RSS](./media/whats-new/feed-icon-16x16.png) lector de fuentes.

En Azure AD se realizan mejoras de forma continua. Para mantenerse al día con los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores
- Funciones obsoletas
- Planes de cambios

Esta página se actualiza mensualmente, por lo que se recomienda visitarla con frecuencia. Si busca elementos que tengan más de 6 meses, puede encontrarlos en el [Archivo de novedades de Azure Active Directory](whats-new-archive.md).

---

## <a name="august-2019"></a>Agosto de 2019

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora la memoria caché compartida de MSAL.NET para escenarios en representación

**Tipo:** Corregido  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios

A partir de la versión 5.0.0-preview de la biblioteca de autenticación de Azure AD (ADAL.NET), los desarrolladores de aplicaciones deben [serializar una caché por cuenta para las aplicaciones web y API web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). En caso contrario, algunos escenarios que usan el [flujo en representación de](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), junto con algunos casos de uso específicos de `UserAssertion`, pueden dar como resultado una elevación de privilegios. Para evitar esta vulnerabilidad, ADAL.NET ignora ahora la caché compartida de la biblioteca de autenticación de Microsoft para dotnet (MSAL.NET) para escenarios en representación.

Para más información acerca de este problema, consulte [Vulnerabilidad de elevación de privilegios de la Biblioteca de autenticación de Active Directory](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

## <a name="july-2019"></a>Julio de 2019

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Planeado su cambio: actualización del servicio Proxy de la aplicación para que admita solo TLS 1.2

**Tipo:** Plan de cambio  
**Categoría del servicio:** Proxy de aplicaciones  
**Funcionalidad del producto:** Control de acceso

Como ayuda para que le proporcionemos nuestro cifrado más seguro, vamos a empezar a limitar el acceso del servicio Proxy de la aplicación para solo a los protocolos TLS 1.2. Esta limitación se distribuirá inicialmente a los clientes que ya usen protocolos TLS 1,2, por lo que no verá el impacto. Los protocolos TLS 1.0 y TLS 1.1 quedarán totalmente en desuso el 31 de agosto de 2019. Los clientes que aún usen TLS 1.0 y TLS 1.1 recibirán un aviso con tiempo suficiente para prepararse para este cambio.

Para mantener la conexión con el servicio Proxy de la aplicación durante todo este cambio, es aconsejable asegurarse de que las combinaciones de cliente-servidor y explorador web-servidor se actualizan para que usen TLS 1.2. También es aconsejable asegurarse de incluir los sistemas cliente que usan los empleados para acceder a las aplicaciones publicadas mediante el servicio Proxy de la aplicación.

Para obtener más información, vea [Adición de una aplicación local para el acceso remoto mediante el proxy de aplicación en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Planeado su cambio: hay actualizaciones de diseño disponibles para la Galería de aplicaciones

**Tipo:** Plan de cambio  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** SSO

Se van a realizar nuevos cambios en la interfaz de usuario en el diseño del área **Agregar desde la galería** de la hoja **Agregar una aplicación**. Estos cambios le ayudarán a encontrar más fácilmente las aplicaciones que admiten el aprovisionamiento automático, OpenID Connect, Lenguaje de marcado de aserción de seguridad (SAML) y el inicio de sesión único (SSO) con contraseña.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Planeado su cambio: Eliminación de la dirección IP del servidor MFA de la dirección IP de Office 365

**Tipo:** Plan de cambio  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Vamos a quitar la dirección IP del servidor MFA del [servicio web de URL y dirección IP de Office 365](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Si actualmente utiliza estas páginas para actualizar la configuración del firewall, debe asegurarse de incluir también la lista de direcciones IP documentadas en la sección **requisitos del firewall del servidor Azure Multi-Factor Authentication** del artículo[Introducción a Servidor Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>Los tokens que solo sean de aplicación ahora requieren que la aplicación cliente exista en el inquilino de recursos

**Tipo:** Corregido  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios

El 26 de julio de 2019 cambiamos la forma en que proporcionamos tokens solo de aplicaciones a través [de la concesión de credenciales de cliente](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). Anteriormente, las aplicaciones podían obtener tokens para llamar a otras aplicaciones, independientemente de si la aplicación cliente se encontraba en el inquilino. Hemos actualizado este comportamiento para que a los recursos de inquilino único, a veces denominados API Web, solo puedan llamarlos las aplicaciones cliente que existen en el inquilino de recursos.

Si la aplicación no se encuentra en el inquilino de recursos, recibirá el siguiente mensaje de error: `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` Para solucionar este problema, debe crear la entidad de servicio de la aplicación cliente en el inquilino, para lo que debe usar el [punto de conexión de consentimiento del administrador](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) o [mediante PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), lo que garantiza que el inquilino ha dado a la aplicación permiso para operar dentro del inquilino.

Para más información, consulte [Novedades en la autenticación](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> El consentimiento existente entre el cliente y la API sigue sin ser necesario. Las aplicaciones aún deben realizar sus propias comprobaciones para saber si tienen autorización.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nuevo inicio de sesión sin contraseña para Azure AD mediante claves de seguridad FIDO2

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios

Los clientes de Azure AD ahora pueden establecer directivas para administrar las claves de seguridad FIDO2 para los usuarios y grupos de su organización. Los usuarios finales también pueden registrar automáticamente sus claves de seguridad, usar las claves para iniciar sesión con sus cuentas Microsoft en sitios web mientras estén en dispositivos compatibles con FIDO, así como iniciar sesión en sus dispositivos de Windows 10 unidos a Azure AD.

Para más información, consulte [Habilitación del inicio de sesión sin contraseña para Azure AD (versión preliminar)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) para obtener información relacionada con el administrador y [Configuración de la información de seguridad para usar una clave de seguridad (versión preliminar)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) para la información relacionada con el usuario final.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (julio de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros

En julio de 2019, hemos agregado estas 18 nuevas aplicaciones con compatibilidad con la federación a la galería de aplicaciones:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright Pattern Omnichannel Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [Clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [Hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/) y [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Para obtener más información acerca de las aplicaciones, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial). Para obtener más información para que una aplicación se muestre en la galería de aplicaciones de Azure AD, consulte [Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatización del aprovisionamiento de cuentas de usuario para estas aplicaciones SaaS recién admitidas

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Supervisión e informes

Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para obtener más información sobre cómo proteger mejor la organización mediante el aprovisionamiento de cuentas de usuario de forma automatizada, vea [Automatización del aprovisionamiento de usuarios para aplicaciones SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nueva etiqueta de servicio de Azure AD Domain Services para el grupo de seguridad de red

**Tipo:** Nueva característica  
**Categoría del servicio:** Azure AD Domain Services  
**Funcionalidad del producto:** Azure AD Domain Services

Si está cansado de administrar largas listas de rangos y direcciones IP, puede usar la nueva etiqueta de servicio de red **AzureActiveDirectoryDomainServices** en el grupo de seguridad de red de Azure para ayudar a proteger el tráfico que entra en la subred de la red virtual de Azure AD Domain Services.

Para más información acerca de esta nueva etiqueta de servicio, consulte [Grupos de seguridad de red para Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuevas auditorías de seguridad para Azure AD Domain Services (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Azure AD Domain Services  
**Funcionalidad del producto:** Azure AD Domain Services

Nos complace anunciar el lanzamiento de la auditoría de seguridad de Azure AD Domain Services en versión preliminar pública. La auditoría de seguridad le ayuda a proporcionar información crítica acerca de los servicios de autenticación mediante la transmisión en secuencias de eventos de auditoría de seguridad a recursos de destino, entre los que se incluyen Azure Storage, áreas de trabajo de Azure Log Analytics y Azure Event Hubs, mediante el portal de Azure AD Domain Services.

Para más información, consulte [Habilitación de auditorías de seguridad para Azure AD Domain Services (versión preliminar)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Conclusiones y uso de los nuevos métodos de autenticación (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Restablecimiento de contraseñas de autoservicio  
**Funcionalidad del producto:** Supervisión e informes

Los informes de las conclusiones y uso de los nuevos métodos de autenticación pueden ayudarle a entender cómo se registran y usan características como Azure Multi-Factor Authentication y el restablecimiento de contraseña de autoservicio en su organización, lo que incluye el número de usuarios registrados para cada característica, la frecuencia con que se usa el restablecimiento de contraseña de autoservicio para restablecer contraseñas y el método de restablecimiento.

Para más información, consulte [Conclusiones y uso de los métodos de autenticación (versión preliminar)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Hay nuevos informes de seguridad disponibles para todos los administradores de Azure AD (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Ahora, todos los administradores de Azure ad pueden seleccionar el banner de la parte superior de los informes de seguridad existentes, como el informe **Usuarios marcados en riesgo**, para empezar a usar la nueva experiencia de seguridad, como se muestra en los informes **Usuarios de riesgo**  e **Inicios de sesión de riesgo**. Con el tiempo, todos los informes de seguridad pasarán de las versiones anteriores a las nuevas y los informes nuevos le proporcionarán las siguientes funcionalidades adicionales:

- Filtrado y ordenación avanzados

- Acciones en masa, como descartar el riesgo del usuario

- Confirmación de entidades en peligro o seguras

- Estado de riesgo, que abarca: En riesgo, Descartado, Corregido y Confirmado (en peligro)

Para más información, consulte [Informe de usuarios de riesgo](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) e [Informe de inicios de sesión peligrosos](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuevas auditorías de seguridad para Azure AD Domain Services (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Azure AD Domain Services  
**Funcionalidad del producto:** Azure AD Domain Services

Nos complace anunciar el lanzamiento de la auditoría de seguridad de Azure AD Domain Services en versión preliminar pública. La auditoría de seguridad le ayuda a proporcionar información crítica acerca de los servicios de autenticación mediante la transmisión en secuencias de eventos de auditoría de seguridad a recursos de destino, entre los que se incluyen Azure Storage, áreas de trabajo de Azure Log Analytics y Azure Event Hubs, mediante el portal de Azure AD Domain Services.

Para más información, consulte [Habilitación de auditorías de seguridad para Azure AD Domain Services (versión preliminar)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nueva federación directa B2B mediante SAML/WS-FED (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** B2B  
**Funcionalidad del producto:** B2B/B2C

La federación directa facilita el trabajo con aquellos con asociados cuya solución de identidad administrada de tecnologías de la información no es Azure AD, ya que se trabaja con sistemas de identidad que admiten los estándares SAML o WS-Fed. Después de configurar una relación de federación directa con un asociado, todos los usuarios a los que invite desde dicho dominio pueden colaborar con usted mediante su cuenta de organización existente, lo que hace que la experiencia de sus invitados sea más fluida.

Para más información, consulte [Federación directa con AD FS y proveedores de terceros para usuarios invitados (versión preliminar)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatización del aprovisionamiento de cuentas de usuario para estas aplicaciones SaaS recién admitidas

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Supervisión e informes

Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Para más información acerca de cómo proteger mejor una organización mediante el aprovisionamiento automatizado de cuentas de usuario, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nueva comprobación de nombres de grupos duplicados en el portal de Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Colaboración

Ahora, al crear o actualizar un nombre de grupo desde el portal de Azure AD, realizaremos una comprobación para ver si está duplicando un nombre de grupo existente en el recurso. Si determinamos que el nombre ya lo está usando otro grupo, se le pedirá que lo modifique.

Para más información, consulte [Administración de grupos en el portal de Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD ahora admite parámetros de consulta estáticos en las direcciones URI de respuesta (redireccionamiento)

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios

Las aplicaciones de Azure AD ahora pueden registrar y usar identificadores URI (redireccionamiento) con parámetros de consulta estáticos (por ejemplo, `https://contoso.com/oauth2?idp=microsoft`) para las solicitudes de OAuth 2.0. El parámetro de consulta estático está sujeto a la coincidencia de cadenas en los identificadores URI de respuesta, al igual que las restantes partes del identificador URI. Si no hay ninguna cadena registrada que coincida con el identificador URI de redireccionamiento con la URL descodificada, se rechazará la solicitud. Si se encuentra el identificador URI, se usa toda la cadena para redirigir al usuario, incluido el parámetro de consulta estático.

Los identificadores URI de redireccionamiento dinámico siguen estando prohibidos, ya que representan un riesgo para la seguridad y no se pueden usar para conservar la información de estado a través de una solicitud de autenticación. Para este propósito, use el parámetro `state`.

Actualmente, las pantallas de registro de aplicaciones del Azure Portal aún bloquean los parámetros de consulta. Sin embargo, puede editar manualmente el manifiesto de la aplicación para agregar y probar los parámetros de consulta en su aplicación. Para más información, consulte [Novedades en la autenticación](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>Los registros de actividad (MS Graph API) para Azure AD ahora están disponibles a través de los cmdlets de PowerShell

**Tipo:** Nueva característica  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** Supervisión e informes

Nos complace anunciar que los registros de actividad de Azure AD (informes de auditoría e inicio de sesión) ahora están disponibles a través del módulo de Azure AD PowerShell. Antes podía crear sus propios scripts mediante los de MS Graph API y ahora hemos ampliado esa funcionalidad a los cmdlets de PowerShell.

Para más información acerca de cómo usar estos cmdlets, consulte [Cmdlets de PowerShell de Azure AD para informes](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controles de filtro actualizados para los registros de auditoría e inicio de sesión en Azure AD

**Tipo:** Característica modificada  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** Supervisión e informes

Hemos actualizado los informes de auditoría e inicio de sesión, por lo que ahora puede aplicar varios filtros sin tener que agregarlos como columnas en las pantallas de informes. Además, ahora puede decidir el número de filtros que desea mostrar en la pantalla. Estas actualizaciones funcionan conjuntamente para que los informes sean más fáciles de leer y se ajusten más a sus necesidades.

Para más información acerca de estas actualizaciones, consulte [Filtrado de registros de auditoría](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) y [Filtrado de las actividades de inicio de sesión](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)de actividades de inicio de sesión.

---

## <a name="june-2019"></a>Junio de 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nueva API riskDetections de Microsoft Graph (versión preliminar)

**Tipo:** Nueva característica  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Nos complace anunciar que la nueva API riskDetections de Microsoft Graph ya se encuentra en fase de versión preliminar pública. Puede usar esta nueva API para ver una lista de detecciones de riesgos de inicio de sesión y usuario relativos a Identity Protection de su organización. También puede utilizarla para consultar más eficazmente las detecciones de riesgos, incluidos detalles sobre el tipo de detección, el estado, el nivel y mucho más.

Para obtener más información, vea la [documentación de referencia de la API de detección de riesgos](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (junio de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros

En junio de 2019, hemos agregado estas 22 nuevas aplicaciones con compatibilidad con la federación a la galería de aplicaciones:

[Azure AD SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [Azure VPN Client](https://portal.azure.com/), [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial), [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz In-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager for Oracle Retail Merchandising, Oracle Access Manager for Oracle E-Business Suite, Oracle IDCS for E-Business Suite, Oracle IDCS for PeopleSoft y Oracle IDCS for JD Edwards

Para obtener más información acerca de las aplicaciones, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial). Para obtener más información para que una aplicación se muestre en la galería de aplicaciones de Azure AD, consulte [Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatización del aprovisionamiento de cuentas de usuario para estas aplicaciones SaaS recién admitidas

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Supervisión e informes

Ahora, puede automatizar la creación, actualización y eliminación de cuentas de usuario para estas aplicaciones recién integradas:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Para obtener más información sobre cómo proteger mejor la organización mediante el aprovisionamiento de cuentas de usuario de forma automatizada, vea [Automatización del aprovisionamiento de usuarios para aplicaciones SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Consulta del progreso en tiempo real del servicio de aprovisionamiento de Azure AD

**Tipo:** Característica modificada  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Administración del ciclo de vida de la identidad

Hemos actualizado la experiencia de aprovisionamiento de Azure AD para incluir una nueva barra de progreso que muestra a qué altura está el proceso de aprovisionamiento de usuario. Esta experiencia mejorada también proporciona información sobre el número de usuarios aprovisionados durante el ciclo actual, así como los usuarios aprovisionados hasta la fecha.

Para obtener más información, vea [Comprobación del estado de aprovisionamiento](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user).

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Ahora, la personalización de marca aparece en las pantallas de cierre de sesión y de error

**Tipo:** Característica modificada  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios

Hemos actualizado Azure AD para que, ahora, la personalización de marca de su empresa aparezca en las pantallas de cierre de sesión y de error, así como en la página de inicio de sesión. Para activar esta característica no es preciso hacer nada, Azure AD usa los recursos que ya están configurados en el área **Personalización de marca de empresa** de Azure Portal.

Para obtener más información sobre cómo configurar la personalización de marca de su empresa, vea [Incorporación de la personalización de marca en las páginas de Azure Active Directory de la organización](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>El Servidor Azure Multi-Factor Authentication (MFA) ya no está disponible para nuevas implementaciones

**Tipo:** En desuso  
**Categoría del servicio:** MFA  
**Funcionalidad del producto:** Seguridad y protección de la identidad

A partir del 1 de julio de 2019, Microsoft ya no ofrecerá el Servidor MFA para implementaciones nuevas. Ahora, los clientes nuevos que quieran exigir la autenticación multifactor en su organización deberán usar Azure Multi-factor Authentication en la nube. Los clientes que tengan activado el Servidor MFA desde antes del 1 de julio no apreciarán ningún cambio. Podrán seguir descargando las versiones más recientes, obteniendo actualizaciones futuras y generando credenciales de activación.

Para obtener más información, vea [Introducción al Servidor Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Para obtener más información sobre Azure Multi-Factor Authentication en la nube, vea [Planeación de una implementación de Azure Multi-Factor Authentication en la nube](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Mayo de 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Cambio de servicio: próxima compatibilidad únicamente con protocolos TLS 1.2 en el servicio Application Proxy

**Tipo:** Plan de cambio  
**Categoría del servicio:** Proxy de aplicaciones  
**Funcionalidad del producto:** Control de acceso

A fin de proporcionar el mejor cifrado a nuestros clientes, estamos limitando el acceso a únicamente protocolos TLS 1.2 en el servicio Application Proxy. Este cambio se está implantando gradualmente en clientes que ya usan protocolos TLS 1.2 exclusivamente, por lo que no deberían apreciar cambio alguno.

La obsolescencia de TLS 1.0 y TLS 1.1 tendrá lugar el 31 de agosto de 2019, pero avisaremos con antelación para que tenga tiempo para prepararse para este cambio. Para prepararse para este cambio, asegúrese de que sus combinaciones de servidor cliente y servidor de examinador (incluidos los clientes que los usuarios usan para tener acceso a aplicaciones publicadas a través de Application Proxy) se actualizan para usar el protocolo TLS 1.2 y, así, mantengan la conexión con el servicio Application Proxy. Para obtener más información, vea [Adición de una aplicación local para el acceso remoto mediante el proxy de aplicación en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Utilización del informe de uso y conclusiones para ver datos de inicio de sesión relativos a las aplicaciones

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Supervisión e informes

Ahora, puede usar el informe de uso y conclusiones, ubicado en el área **Aplicaciones empresariales** de Azure Portal, para obtener una vista de los datos de inicio de sesión relativos a las aplicaciones, con información sobre lo siguiente:

- Aplicaciones más usadas en la organización

- Aplicaciones con los inicios de sesión con más errores

- Principales errores de inicio de sesión de cada aplicación

Para obtener más información sobre esta característica, vea [Informe de uso y conclusiones en el portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report).

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatización del aprovisionamiento de usuarios a aplicaciones en la nube mediante Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Supervisión e informes

Siga estos nuevos tutoriales para utilizar el servicio de aprovisionamiento de Azure AD para automatizar la creación, eliminación y actualización de cuentas de usuario de las siguientes aplicaciones en la nube:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

También puede seguir este nuevo [tutorial de Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), que proporciona información sobre cómo aprovisionar objetos de grupo.

Para obtener más información sobre cómo proteger mejor la organización mediante el aprovisionamiento de cuentas de usuario de forma automatizada, vea [Automatización del aprovisionamiento de usuarios para aplicaciones SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>La puntuación segura de identidad ya está disponible en Azure AD (disponibilidad general)

**Tipo:** Nueva característica  
**Categoría del servicio:** N/D  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Ahora, puede supervisar y mejorar su posición de seguridad de identidad gracias a la característica de puntuación segura de identidad de Azure AD. La característica de puntuación segura de identidad utiliza un mismo panel para ayudarle a:

- Medir de forma objetiva su nivel de seguridad de la identidad, según una puntuación de entre 1 y 223.

- Planear la realización de mejoras en la seguridad de la identidad.

- Ver si las mejoras han logrado sus objetivos de seguridad.

Para obtener más información sobre la característica de puntuación segura de identidad, vea [¿Qué es la puntuación segura de identidad en Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>Nueva experiencia Registros de aplicaciones ya disponible (disponibilidad general)

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Experiencia para el desarrollador

La nueva experiencia [Registros de aplicaciones](https://aka.ms/appregistrations) ya está disponible en versión de disponibilidad general. Esta nueva experiencia incluye todas las características clave a las que está acostumbrado de Azure Portal y del portal de Registros de aplicaciones, y las mejora a través de lo siguiente:

- **Una mejor administración de las aplicaciones.** En lugar de ver las aplicaciones en diversos portales, ahora puede verlas todas en una misma ubicación.

- **Un registro de aplicaciones simplificado.** Desde la experiencia de navegación mejorada a la experiencia de selección de permisos renovada, ahora es más fácil registrar y administrar sus aplicaciones.

- **Una información más pormenorizada.** Encontrará más detalles sobre la aplicación, incluidas guías de inicio rápido y otras muchas cosas.

Para obtener más información, vea [Plataforma de identidad de Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) y la entrada de blog que anuncia que la [experiencia Registros de aplicación ya está disponible de forma general](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) .

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuevas capacidades disponibles en la API correspondiente a los usuarios de riesgo de Identity Protection

**Tipo:** Nueva característica  
**Categoría del servicio:** Protección de identidad  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Nos complace anunciar que, ahora, puede usar la API correspondiente a los usuarios de riesgo para recuperar el historial de riesgos de los usuarios, descartar usuarios de riesgo y confirmar usuarios como usuarios de riesgo. Este cambio ayuda a actualizar el estado de riesgo de los usuarios de forma más eficaz y a entender sus historiales de riesgos.

Para obtener más información, vea la [documentación de referencia de la API correspondiente a los usuarios de riesgo](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (mayo de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros

En mayo de 2019, hemos agregado estas 21 nuevas aplicaciones con compatibilidad con la federación a la galería de aplicaciones:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial) y [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Para obtener más información acerca de las aplicaciones, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial). Para obtener más información para que una aplicación se muestre en la galería de aplicaciones de Azure AD, consulte [Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Experiencias mejoradas de administración y creación de grupos en el portal de Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Colaboración

Hemos realizado mejoras en las experiencias relativas a grupos del portal de Azure AD. Estas mejoras permiten a los administradores administrar de mejor forma las listas de grupos y las listas de miembros, así como proporcionar más opciones de creación.

Estas mejoras incluyen:

- Filtrado básico por tipo de pertenencia y tipo de grupo.

- Incorporación de columnas nuevas, como Origen y Dirección de correo electrónico.

- Posibilidad de seleccionar varios grupos, miembros y listas de propietarios para eliminarlos con facilidad.

- Posibilidad de elegir una dirección de correo electrónico y de agregar propietarios durante la creación del grupo.

Para obtener más información vea [Creación de un grupo básico e incorporación de miembros con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configuración de una directiva de nomenclatura para grupos de Office 365 en el portal de Azure AD (disponibilidad general)

**Tipo:** Característica modificada  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Colaboración

Ahora, los administradores pueden configurar una directiva de nomenclatura de grupos de Office 365 mediante el portal de Azure AD. Este cambio ayuda a aplicar las convenciones de nomenclatura coherentes para los grupos de Office 365 creados o editados por los usuarios de su organización.

La directiva de nomenclatura de grupos de Office 365 se puede configurar de dos maneras diferentes:

- Definir prefijos o sufijos, que se agregan automáticamente al nombre de un grupo.

- Cargar un conjunto personalizado de palabras bloqueadas de la organización que no se permiten en los nombres de grupo (por ejemplo, "CEO, nómina, RR. HH.").

Para obtener más información, vea [Aplicación de una directiva de nomenclatura en los grupos de Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Ahora, los puntos de conexión de la API de Microsoft Graph están disponibles para los registros de actividad de Azure AD (disponibilidad general)

**Tipo:** Característica modificada  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** Supervisión e informes

Nos complace anunciar la disponibilidad general de compatibilidad de puntos de conexión de la API de Microsoft Graph en los registros de actividad de Azure AD. Con este lanzamiento, ahora puede usar la versión 1.0 tanto de los registros de auditoría de Azure AD como de las API de registros de inicio de sesión.

Para obtener más información, vea [Información general sobre la API de registros de auditoría de Azure AD](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Ahora, los administradores pueden usar el acceso condicional en el proceso de registro combinado (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad  

Ahora, los administradores pueden crear directivas de acceso condicional para su uso en la página de registro combinado. Esto incluye la puesta en marcha de directivas que permiten el registro bajo estos supuestos:

- Los usuarios están en una red de confianza.

- Los usuarios presentan un bajo riesgo de inicio de sesión.

- Los usuarios están en un dispositivo administrado.

- Los usuarios han aceptado las condiciones de uso de la organización.

Para obtener más información sobre el acceso condicional y el restablecimiento de contraseña, puede ver la entrada de blog sobre el [acceso condicional en la experiencia de registro combinado de restablecimiento de contraseña y MFA de Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348). Para obtener más información sobre las directivas de acceso condicional en el proceso de registro combinado, vea [Directivas de acceso condicional para el registro combinado](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Para obtener más información sobre la característica Condiciones de uso de Azure AD, vea [Característica Condiciones de uso de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use).

---

## <a name="april-2019"></a>Abril de 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nueva detección de la inteligencia sobre amenazas de Azure AD ya disponible como parte de Azure AD Identity Protection

**Tipo:** Nueva característica  
**Categoría del servicio:** Azure AD Identity Protection  
**Funcionalidad del producto:** Seguridad y protección de la identidad

La detección de la inteligencia sobre amenazas de Azure AD ya está disponible como parte de la característica actualizada Azure AD Identity Protection. Esta nueva funcionalidad ayuda a identificar una actividad de usuario poco común para el usuario en cuestión o una actividad que encaja con patrones de ataque conocidos según los orígenes de la inteligencia sobre amenazas internas y externas de Microsoft.

Para obtener más información sobre la versión actualizada de Azure AD Identity Protection, vea la entrada de blog sobre las [cuatro grandes mejoras de Azure AD Identity Protection que ya están en fase de versión preliminar pública](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) y el artículo [¿Qué es Azure Active Directory Identity Protection (actualizado)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) . Para obtener más información sobre la detección de inteligencia sobre amenazas de Azure AD, vea el artículo [Eventos de riesgo de Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence).

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>La administración de derechos de Azure AD ya está disponible (versión preliminar)

**Tipo:** Nueva característica  
**Categoría del servicio:** Identity Governance  
**Funcionalidad del producto:** Identity Governance

La administración de derechos de Azure AD, ahora en versión preliminar pública, permite a los clientes delegar la administración de paquetes de acceso, que define cómo los empleados y los asociados comerciales pueden solicitar acceso, quién debe aprobar ese acceso y cuánto va a durar ese acceso. Los paquetes de acceso pueden administrar la pertenencia a grupos de Azure AD y Office 365, las asignaciones de roles en aplicaciones empresariales y las asignaciones de roles para sitios de SharePoint Online. Encontrará más información sobre la administración de derechos en el artículo de [información general sobre la administración de derechos de Azure AD](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Para obtener más información sobre el amplio abanico de características de Azure AD Identity Governance, incluidos Privileged Identity Management, revisiones del acceso y las condiciones de uso, vea [¿Qué es Azure AD Identity Governance?](../governance/identity-governance-overview.md)

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configuración de una directiva de nomenclatura para grupos de Office 365 en el portal de Azure AD (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Colaboración

Ahora, los administradores pueden configurar una directiva de nomenclatura de grupos de Office 365 mediante el portal de Azure AD. Este cambio ayuda a aplicar las convenciones de nomenclatura coherentes para los grupos de Office 365 creados o editados por los usuarios de su organización.

La directiva de nomenclatura de grupos de Office 365 se puede configurar de dos maneras diferentes:

- Definir prefijos o sufijos, que se agregan automáticamente al nombre de un grupo.

- Cargar un conjunto personalizado de palabras bloqueadas de la organización que no se permiten en los nombres de grupo (por ejemplo, "CEO, nómina, RR. HH.").

Para obtener más información, vea [Aplicación de una directiva de nomenclatura en los grupos de Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Los registros de actividad de Azure AD ya están disponibles en Azure Monitor (disponibilidad general)

**Tipo:** Nueva característica  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** Supervisión e informes

Para que sea más fácil atender sus comentarios sobre las visualizaciones de los registros de actividad de Azure AD, hemos incluido una nueva característica Información en Log Analytics. Esta característica sirve para obtener información sobre los recursos de Azure AD mediante nuestras plantillas interactivas, llamadas libros. Estos libros pregenerados proporcionan detalles sobre las aplicaciones o los usuarios, como lo siguiente:

- **Inicios de sesión** Proporciona detalles sobre las aplicaciones y los usuarios, incluida la ubicación de inicio de sesión, el sistema operativo o el cliente y versión del explorador que se usa y el número de inicios de sesión correctos o con errores.

- **Autenticación heredada y acceso condicional** Proporciona detalles sobre las aplicaciones y los usuarios que usan la autenticación heredada, incluido el uso de Multi-Factor Authentication desencadenado por las directivas de acceso condicional, las aplicaciones que usan directivas de acceso condicional, etc.

- **Análisis de errores de inicio de sesión** Ayuda a averiguar si los errores de inicio de sesión se deben a una acción del usuario, a problemas de directiva o a la infraestructura existente.

- **Informes personalizados** Puede crear libros o editar los ya existentes para ayudar a personalizar la característica Información de su organización.

Para obtener más información, vea [Cómo usar los libros de Azure Monitor en informes de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (abril de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros

En abril de 2019, hemos agregado estas 21 nuevas aplicaciones con compatibilidad con la federación a la galería de aplicaciones:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial) e [Indiggo](https://indiggolead.com/)

Para obtener más información acerca de las aplicaciones, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial). Para obtener más información para que una aplicación se muestre en la galería de aplicaciones de Azure AD, consulte [Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nueva opción de frecuencia de revisiones de acceso y selección de varios roles

**Tipo:** Nueva característica  
**Categoría del servicio:** Revisiones de acceso  
**Funcionalidad del producto:** Identity Governance

Las nuevas actualizaciones de las revisiones de acceso de Azure AD permiten lo siguiente:

- Cambiar la frecuencia de las revisiones de acceso a **Semestral**, además de las opciones ya existentes (Semanal, Mensual, Trimestral y Anual).

- Seleccionar varios roles de recursos de Azure y de Azure AD al crear una revisión de acceso único. En esta situación, todos los roles se configuran igual y se notifica a todos los revisores al mismo tiempo.

Para obtener más información sobre cómo crear una revisión de acceso, vea [Creación de una revisión de acceso de los grupos o las aplicaciones en las revisiones de acceso de Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Transición de los sistemas de alertas por correo electrónico de Azure AD Connect en curso; se está enviando información de nuevo remitente de correo electrónico a algunos clientes

**Tipo:** Característica modificada  
**Categoría del servicio:** Sincronización de AD  
**Funcionalidad del producto:** Plataforma

Azure AD Connect está en fase de transición de nuestros sistemas de alertas de correo electrónico, lo que puede hacer que algunos clientes vean un nuevo remitente de correo electrónico. Para solucionar este problema, hay que agregar `azure-noreply@microsoft.com` a la lista de permitidos de la organización, o no podrá seguir recibiendo alertas importantes de los servicios de Office 365, Azure o Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Los cambios en los sufijos UPN ya se realizan correctamente entre dominios federados en Azure AD Connect

**Tipo:** Corregido  
**Categoría del servicio:** Sincronización de AD  
**Funcionalidad del producto:** Plataforma

Ahora puede cambiar correctamente el sufijo UPN de un usuario de un dominio federado a otro en Azure AD Connect. Esta corrección implica que ya no debería aparecer el mensaje de error FederatedDomainChangeError durante el ciclo de sincronización, ni recibir una notificación por correo electrónico que informa de que el objeto no se puede actualizar en Azure Active Directory porque el atributo [FederatedUser.UserPrincipalName] no es válido y pide actualizar el valor en los servicios de directorio local.

Para obtener más información, vea [Solución de errores durante la sincronización](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Mayor seguridad con la directiva de acceso condicional basada en la protección de aplicaciones en Azure AD (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Ahora, el acceso condicional basado en la protección de aplicaciones está disponible a través de la directiva **Requerir protección de aplicaciones**. Esta nueva directiva ayuda a aumentar la seguridad de la organización al tratar de impedir que:

- Los usuarios obtengan acceso a las aplicaciones sin contar con una licencia de Microsoft Intune.

- Los usuarios no puedan obtener una directiva de protección de aplicaciones de Microsoft Intune.

- Los usuarios obtengan acceso a las aplicaciones sin una directiva de protección de aplicaciones configurada de Microsoft Intune.

Para obtener más información, vea [Cómo usar la aplicación Requerir protección de aplicaciones para el acceso a aplicaciones en la nube mediante el acceso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nueva compatibilidad con el acceso condicional y el inicio de sesión único de Azure AD en Microsoft Edge (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Hemos mejorado la compatibilidad de Azure AD para Microsoft Edge, incluido proporcionar nueva compatibilidad con el inicio de sesión único en Azure AD y el acceso condicional. Si ya usó previamente Microsoft Intune Managed Browser, ahora puede usar Microsoft Edge en su lugar.

Para obtener más información sobre cómo configurar y administrar los dispositivos y aplicaciones mediante el acceso condicional, vea [Requerir dispositivos administrados para el acceso a aplicaciones en la nube con el acceso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) y [Requerir aplicaciones de cliente aprobadas para el acceso a aplicaciones en la nube con el acceso condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Para obtener más información sobre cómo administrar el acceso mediante Microsoft Edge con directivas de Microsoft Intune, vea [Administración del acceso web con un explorador protegido por directiva de Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzo de 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Identity Experience Framework y las directivas personalizadas ya están disponibles en Azure Active Directory B2C (GA)

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

Ahora es posible crear directivas personalizadas en Azure AD B2C, incluidas las siguientes tareas, que se admiten a escala y bajo nuestro SLA de Azure:

- Crear y cargar trayectos de autenticación personalizada de los usuarios mediante directivas personalizadas.

- Describir los recorridos del usuario paso a paso como intercambios entre proveedores de notificaciones.

- Definir la creación de ramas condicional en recorridos del usuario.

- Transformar y asignar notificaciones para su uso en las comunicaciones y las decisiones en tiempo real.

- Usar servicios habilitados por la API de REST en los trayectos de autenticación personalizada de los usuarios. Por ejemplo, con proveedores de correo electrónico, CRM y sistemas de autorización propietarios.

- Federarse con proveedores de identidades que cumplen con el protocolo OpenIDConnect. Por ejemplo, con Azure AD multiinquilino, proveedores de cuentas de redes sociales o proveedores de verificación de dos factores.

Para obtener más información sobre la creación de directivas personalizadas, vea [Notas para desarrolladores de directivas personalizadas en Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) y lea la [la entrada de blog de Alex Simon, que incluye casos prácticos](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (marzo de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros

En marzo de 2019, hemos agregado estas 14 nuevas aplicaciones con compatibilidad con la federación a la galería de aplicaciones:

[ISEC7 Mobile Exchange Delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Explanation-Based Auditing System](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool Performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial) y [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Para obtener más información acerca de las aplicaciones, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial). Para obtener más información para que una aplicación se muestre en la galería de aplicaciones de Azure AD, consulte [Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuevos conectores de aprovisionamiento de Zscaler y Atlassian en la galería Azure AD (marzo de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aprovisionamiento de aplicaciones  
**Funcionalidad del producto:** Integración de terceros

Automatice la creación, la actualización y la eliminación de cuentas de usuario para las siguientes aplicaciones:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning), [Zscaler Three](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning) y [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

Para obtener más información sobre cómo proteger mejor la organización a través del aprovisionamiento automatizado de cuentas de usuario, vea [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Restaurar y administrar los grupos de Office 365 eliminados en el portal de Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Administración de grupos  
**Funcionalidad del producto:** Colaboración

Ahora es posible ver y administrar los grupos de Office 365 eliminados desde el portal de Azure AD. Este cambio ayuda a ver qué grupos se pueden restaurar, además de permitir eliminar de forma permanente los grupos que la organización no necesite.

Para obtener más información, vea [Restauración de grupos expirados o eliminados](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>El inicio de sesión único está ahora disponible para aplicaciones locales protegidas por SAML de Azure AD a través del proxy de la aplicación (versión preliminar pública)

**Tipo:** Nueva característica  
**Categoría del servicio:** Proxy de aplicaciones  
**Funcionalidad del producto:** Control de acceso

Ahora se puede proporcionar una experiencia de inicio de sesión único (SSO) en aplicaciones locales con autenticación SAML, junto con acceso remoto a estas aplicaciones a través del proxy de la aplicación. Para obtener más información sobre cómo configurar el SSO de SAML con las aplicaciones locales, vea [Inicio de sesión único de SAML para aplicaciones locales con proxy de aplicación (versión preliminar)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Se interrumpirán las aplicaciones cliente en bucles de solicitud para mejorar la confiabilidad y la experiencia del usuario

**Tipo:** Nueva característica  
**Categoría del servicio:** Autenticaciones (inicios de sesión)  
**Funcionalidad del producto:** Autenticación de usuarios

Las aplicaciones cliente pueden emitir incorrectamente cientos de solicitudes del mismo inicio de sesión durante un breve período de tiempo. Todas estas solicitudes, aprobadas o no, contribuyen a una experiencia de usuario deficiente y a mayores cargas de trabajo para el IDP, lo que aumenta la latencia de todos los usuarios y reduce la disponibilidad del IDP.

Esta actualización envía el error `invalid_grant`: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` a las aplicaciones cliente que emiten solicitudes duplicadas varias veces en un breve período de tiempo, más allá del ámbito de operación normal. Las aplicaciones cliente que experimentan este problema deben mostrar un mensaje interactivo que requiera que el usuario vuelva a iniciar sesión. Para obtener más información sobre este cambio y sobre cómo corregir la aplicación si se encuentra con este error, vea [Novedades en la autenticación](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Ya disponible la nueva experiencia de usuario de los registros de auditoría

**Tipo:** Característica modificada  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** Supervisión e informes

Hemos creado una nueva página **Registros de auditoría** de Azure AD para mejorar la legibilidad y la búsqueda de información. Para ver la página **Registros de auditoría**, seleccione **Registros de auditoría** en la sección **Actividad** de Azure AD.

![Nueva página Registros de auditoría con información de ejemplo](media/whats-new/audit-logs-page.png)

Para obtener más información sobre la página **Registros de auditoría**, vea [Informes de actividad de auditoría en el portal de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuevas advertencias e instrucciones para ayudar a evitar el bloqueo accidental del administrador por una mala configuración de las directivas de acceso condicional

**Tipo:** Característica modificada  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Seguridad y protección de la identidad

Para evitar que los administradores se bloqueen accidentalmente a sí mismos en sus propios inquilinos a causa de una mala configuración de las directivas de acceso, hemos creado nuevas advertencias y hemos actualizado las instrucciones en Azure Portal. Para obtener más información sobre las nuevas instrucciones, vea [¿Cuáles son las dependencias del servicio de acceso condicional de Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Experiencia mejorada de las condiciones de uso en dispositivos móviles

**Tipo:** Característica modificada  
**Categoría del servicio:** Términos de uso  
**Funcionalidad del producto:** Gobernanza

Hemos actualizado y mejorado el modo en el que los usuarios leen y aceptan las condiciones de uso en un dispositivo móvil. Ahora es posible ampliar y reducir el texto, volver atrás, descargar la información y seleccionar hipervínculos. Para obtener más información sobre las nuevas condiciones de uso, vea [Característica Condiciones de uso de Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Disponible la nueva experiencia de descarga de los registros de actividad de Azure AD

**Tipo:** Característica modificada  
**Categoría del servicio:** Informes  
**Funcionalidad del producto:** Supervisión e informes

Ahora se pueden descargar grandes cantidades de registros de actividad directamente desde Azure Portal. Este cambio le permite:

- Descargar hasta 250 000 filas.

- Recibir una notificación cuando se complete la descarga.

- Personalizar el nombre del archivo.

- Determinar el formato de salida como JSON o CSV.

Para más información acerca de esta característica, consulte [Inicio rápido: Descarga de un informe de auditoría mediante Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report).

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Cambio importante: modificaciones en la evaluación de estado de Exchange ActiveSync (EAS)

**Tipo:** Plan de cambio  
**Categoría del servicio:** Acceso condicional  
**Funcionalidad del producto:** Control de acceso

Estamos actualizando el modo en que Exchange ActiveSync (EAS) evalúa las condiciones siguientes:

- Ubicación del usuario, basada en el país, la región o la dirección IP.

- Riesgo de inicio de sesión

- Plataforma de dispositivo

Si ha usado estas condiciones anteriormente en las directivas de acceso condicional, tenga en cuenta que el comportamiento de la condición puede cambiar. Por ejemplo, si ha usado la condición de ubicación de usuario en una directiva, es posible que la directiva se omita ahora en función de la ubicación del usuario.

---

## <a name="february-2019"></a>Febrero de 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Cifrado de tokens SAML de Azure AD configurable (versión preliminar pública) 

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** SSO

Ahora se puede configurar cualquier aplicación SAML compatible para que reciba tokens SAML cifrados. Cuando se configura y se usa con una aplicación, Azure AD cifra las aserciones SAML emitidas mediante una clave pública que se obtiene de un certificado almacenado en Azure AD.

Para obtener más información sobre cómo configurar el cifrado de tokens SAML, vea [Configuración del cifrado de tokens SAML de Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creación de una revisión de acceso para grupos o aplicaciones con las revisiones de acceso de Azure AD

**Tipo:** Nueva característica  
**Categoría del servicio:** Revisiones de acceso  
**Funcionalidad del producto:** Gobernanza

Ahora se pueden incluir varios grupos o aplicaciones en una única revisión de acceso de Azure AD para comprobar la pertenencia a grupos o la asignación de aplicaciones. Las revisiones de acceso con varios grupos o aplicaciones se configuran usando los mismos valores y todos los revisores incluidos son notificados al mismo tiempo.

Para obtener más información sobre cómo crear una revisión de acceso con las revisiones acceso de Azure AD, vea [Creación de una revisión de acceso de grupos o aplicaciones con las revisiones de acceso de Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuevas aplicaciones federadas disponibles en la galería de aplicaciones de Azure AD (febrero de 2019)

**Tipo:** Nueva característica  
**Categoría del servicio:** Aplicaciones empresariales  
**Funcionalidad del producto:** Integración de terceros
 
En febrero de 2019, hemos agregado estas 27 nuevas aplicaciones con compatibilidad con la federación a nuestra galería de aplicaciones:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Permission Click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Seismic](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial) y [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Para obtener más información acerca de las aplicaciones, consulte [Integración de aplicación SaaS con Azure Active Directory](https://aka.ms/appstutorial). Para obtener más información para que una aplicación se muestre en la galería de aplicaciones de Azure AD, consulte [Aprenda a mostrar su aplicación en la galería de aplicaciones de Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Mejora del registro MFA/SSPR combinado

**Tipo:** Característica modificada  
**Categoría del servicio:** Restablecimiento de contraseñas de autoservicio  
**Funcionalidad del producto:** Autenticación de usuarios

En respuesta a los comentarios de los clientes, se ha mejorado la experiencia en versión preliminar del registro combinado de MFA/SSPR para que los usuarios puedan registrar de forma más rápida su información de seguridad para MFA y SSPR. 

**Si quiere activar ahora la experiencia mejorada para los usuarios, siga estos pasos:**

1. Inicie sesión como administrador global o administrador de usuarios en Azure Portal y vaya a **Azure Active Directory > Configuración de usuario > Administrar la configuración de las características en versión preliminar del panel de acceso**. 

2. En la opción **Usuarios que pueden utilizar las características en versión preliminar para registrar y administrar la información de seguridad – actualizar**, elija activar las características para un **Grupo seleccionado de usuarios** o para **Todos los usuarios**.

A lo largo de las próximas semanas se retirará la capacidad de activar la antigua experiencia combinada de vista previa de registro MFA/SSPR para los inquilinos que todavía no la hayan activado.

**Para ver si el control se retirará en su inquilino, siga estos pasos:**

1. Inicie sesión como administrador global o administrador de usuarios en Azure Portal y vaya a **Azure Active Directory > Configuración de usuario > Administrar la configuración de las características en versión preliminar del panel de acceso**.  

2. Si la opción **Usuarios que pueden utilizar las características en versión preliminar para registrar y administrar la información de seguridad** está establecida en **Ninguno**, la opción se retirará del inquilino.

Independientemente de si con anterioridad se ha activado la antigua experiencia combinada de vista previa de registro MFA/SSPR, la antigua experiencia se desactivará en una fecha futura. Por ello, es muy recomendable que cambie a la nueva experiencia lo antes posible.

Para obtener más información sobre la experiencia de registro mejorada, vea la entrada de blog sobre las [interesantes mejoras en la experiencia de registro combinado MFA y restablecimiento de contraseña de Azure AD](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Experiencia actualizada de la administración de directivas para flujos de usuario

**Tipo:** Característica modificada  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

Se ha actualizado el proceso de creación y administración de directivas para facilitar los flujos del usuario (anteriormente conocidos como directivas integradas). Esta experiencia nueva es ahora la predeterminada para todos los inquilinos de Azure AD.

Si quiere hacer algún otro comentario o sugerencia, use los iconos de sonrisa o desaprobación del área **Envíenos comentarios** en la parte superior de la pantalla del portal.

Para obtener más información sobre la nueva experiencia de administración de directivas, vea la entrada de blog [Azure AD B2C tiene ahora la personalización de JavaScript y muchas más características nuevas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Elección de versiones específicas de elementos de página proporcionadas por Azure AD B2C

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

Ahora puede elegir una versión específica de los elementos de página proporcionados por Azure AD B2C. Si selecciona una versión específica, puede probar las actualizaciones antes de que aparezcan en la página y predecir el comportamiento. Además, ahora puede decidir aplicar versiones específicas de la página para permitir las personalizaciones de JavaScript. Para activar esta característica, vaya a la página **Propiedades** en los flujos de usuario.

Para obtener más información sobre cómo elegir versiones específicas de los elementos de la página, vea la entrada de blog [Azure AD B2C tiene ahora la personalización de JavaScript y muchas más características nuevas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisitos de contraseña del usuario final configurables para B2C (GA)

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

Ya es posible configurar la complejidad de las contraseñas que usan los usuarios finales de la organización, en lugar de tener que utilizar la directiva nativa de contraseñas de Azure AD. En la hoja **Propiedades** de los flujos de usuario (antes conocidos como directivas integradas), puede elegir una complejidad de contraseña **Simple** o **Segura**, o bien puede crear un conjunto de requisitos **Personalizado**.

Para obtener más información sobre la configuración de los requisitos de complejidad de contraseñas, vea [Configuración de los requisitos de complejidad de contraseñas de Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuevas plantillas predeterminadas para experiencias de autenticación con marca personalizada

**Tipo:** Nueva característica  
**Categoría del servicio:** B2C: administración de identidades de consumidor  
**Funcionalidad del producto:** B2B/B2C

Puede usar nuestras nuevas plantillas predeterminadas, ubicadas en la hoja **Diseños de página** de los flujos de usuario (anteriormente conocidos como directivas integradas), para que los usuarios disfruten de una experiencia de autenticación adaptada a su marca.

Para obtener más información sobre el uso de las plantillas, vea [Azure AD B2C tiene ahora la personalización de JavaScript y muchas más características nuevas](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---
