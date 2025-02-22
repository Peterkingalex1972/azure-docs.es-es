---
title: 'Azure Security and Compliance Blueprint: almacenamiento de datos para PCI DSS'
description: 'Azure Security and Compliance Blueprint: almacenamiento de datos para PCI DSS'
services: security
author: meladie
ms.assetid: 6d29e5bf-6cd0-4cda-bbd8-59f283b0c86c
ms.service: security
ms.topic: article
ms.date: 07/03/2018
ms.author: meladie
ms.openlocfilehash: 0e7eadcb511bc55bbafcf20f44a28ffcf2f9be90
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/10/2019
ms.locfileid: "68946655"
---
# <a name="azure-security-and-compliance-blueprint-data-warehouse-for-pci-dss"></a>Plano técnico de seguridad y cumplimiento de Azure: Data Warehouse para PCI DSS

## <a name="overview"></a>Información general

Este plano técnico Azure Security and Compliance Blueprint ofrece una guía para implementar una arquitectura de almacenamiento de datos en Azure que ayuda con los requisitos de las Normas de seguridad de datos del sector de las tarjetas de pago (PCI DSS 3.2). Muestra una arquitectura de referencia común e indica cómo tratar adecuadamente los datos de una tarjeta de crédito (incluyendo el número de tarjeta, la fecha de caducidad y los datos de comprobación) en un entorno seguro, de niveles múltiples y compatible. Este plano técnico muestra las formas en que los clientes pueden cumplir con los requisitos específicos de seguridad y cumplimiento y sirve como base para que los clientes creen y configuren sus propias soluciones de almacenamiento de datos en Azure.

Esta arquitectura de referencia, guía de implementación y modelo de amenazas proporcionan una base para que los clientes cumplan con los requisitos de PCI DSS 3.2. Esta solución proporciona una base de referencia para ayudar a los clientes a implementar cargas de trabajo en Azure de manera conforme a PCI DSS 3.2, sin embargo, esta solución no se debe usar tal como está en un entorno de producción porque se requiere configuración adicional.

Para cumplir con los requisitos de PCI DSS, debe conseguir que un asesor de seguridad cualificado (QSA) y acreditado certifique una solución de cliente de producción. Los clientes tienen la responsabilidad de realizar las evaluaciones de seguridad y cumplimiento adecuadas de cualquier solución compilada con esta arquitectura, ya que los requisitos pueden variar en función de las características de implementación de cada cliente.

## <a name="architecture-diagram-and-components"></a>Componentes y diagrama de la arquitectura

Esta solución proporciona una arquitectura de referencia que implementa un almacenamiento de datos de alto rendimiento y seguro en la nube. Hay dos niveles de datos independientes en esta arquitectura: uno donde se importan los datos y se almacenan provisional y definitivamente en un entorno de SQL en clúster y otro para Azure SQL Data Warehouse donde se cargan los datos mediante una herramienta de extracción, transformación y carga de datos (por ejemplo, consultas T-SQL de [PolyBase](https://docs.microsoft.com/azure/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase)) para su procesamiento. Una vez que los datos están almacenados en Azure SQL Data Warehouse, se pueden realizar análisis a gran escala.

Azure ofrece una variedad de servicios de informes y análisis para el cliente. Esta solución incluye SQL Server Reporting Services (SSRS) para la rápida creación de informes desde Azure SQL Data Warehouse. Todo el tráfico SQL se cifra con SSL mediante la inclusión de certificados autofirmados. Como procedimiento recomendado, Azure recomienda el uso de una entidad de certificación de confianza para mejorar la seguridad.

En Azure SQL Data Warehouse, los datos se almacenan en tablas relacionales con almacenamiento en columnas, un formato que reduce significativamente los costos de almacenamiento de datos al tiempo que mejora el rendimiento de las consultas. Según los requisitos de uso, los recursos de proceso de Azure SQL Data Warehouse se pueden escalar y reducir verticalmente o apagarse completamente si no hay procesos activos que necesiten recursos de proceso.

Un equilibrador de carga de SQL administra el tráfico SQL, lo cual garantiza un rendimiento alto. Todas las máquinas virtuales de esta arquitectura de referencia se implementan en un conjunto de disponibilidad con instancias de SQL Server configuradas en un grupo de disponibilidad Always On para las funcionalidades de alta disponibilidad y recuperación ante desastres.

Esta arquitectura de referencia de almacenamiento de datos también incluye un nivel de Active Directory para la administración de recursos dentro de la arquitectura. La subred de Active Directory permite la fácil adopción en una estructura de bosque de Active Directory mayor, lo que permite un funcionamiento continuo del entorno, aunque no esté disponible el acceso al bosque mayor. Todas las máquinas virtuales implementadas están unidas mediante dominio al nivel de Active Directory y usan directivas de grupo de Active Directory para aplicar configuraciones de seguridad y cumplimiento a nivel del sistema operativo.

La solución utiliza cuentas de Azure Storage que los clientes pueden configurar para que usen Storage Service Encryption para preservar la confidencialidad de los datos en reposo. Azure almacena tres copias de los datos en un centro de datos seleccionado de un cliente para proporcionar resistencia. El almacenamiento con redundancia geográfica garantiza que los datos se replicarán en un centro de datos secundario a cientos de kilómetros de distancia y que, de nuevo, se guardarán tres copias en ese centro de datos, lo que impide que un evento adverso que suceda en el centro de datos principal del cliente pueda resultar en una pérdida de datos.

Para mejorar la seguridad, todos los recursos de esta solución se administran como un grupo de recursos mediante Azure Resource Manager. El control de acceso basado en rol de Azure Active Directory se utiliza para controlar el acceso a los recursos implementados, incluidas las claves en Azure Key Vault. El mantenimiento del sistema se supervisa mediante Azure Security Center y Azure Monitor. Los clientes configuran ambos servicios de monitorización para capturar registros y mostrar el estado del sistema en un único panel de fácil navegación.

Una máquina virtual sirve de host de tipo bastión de administración, lo cual proporciona una conexión segura para que los administradores accedan a los recursos implementados. Los datos se cargan en el área de ensayo a través de este host de tipo bastión de administración. **Microsoft recomienda configurar una conexión VPN o de Azure ExpressRoute para la administración y la importación de datos en la subred de la arquitectura de referencia.**

![Diagrama de arquitectura de referencia de almacenamiento de datos para PCI DSS](images/pcidss-dw-architecture.png "Data Warehouse for PCI DSS reference architecture diagram")

Esta solución usa los siguientes servicios de Azure. Los detalles de la arquitectura de implementación se encuentran en la sección [Arquitectura de implementación](#deployment-architecture).

- Conjuntos de disponibilidad
    - Controladores de dominio de Active Directory
    - Testigo y nodos de clúster de SQL
- Azure Active Directory
- Azure Data Catalog
- Azure Key Vault
- Azure Monitor
- Azure Security Center
- Azure Load Balancer
- Azure Storage
- Azure Virtual Machines
    - (1) Host de tipo bastión
    - (2) Controlador de dominio de Active Directory
    - (2) Nodo de clúster de SQL Server
    - (1) Testigo de SQL Server
- Azure Virtual Network
    - (1) Red /16
    - (4) /24 redes
    - (4) Grupos de seguridad de red
- Almacén de Recovery Services
- SQL Data Warehouse
- SQL Server Reporting Services

## <a name="deployment-architecture"></a>Arquitectura de implementación

En la siguiente sección se detallan los elementos de desarrollo e implementación.

**SQL Data Warehouse**: [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-overview-what-is) es un almacenamiento de datos empresarial (EDW) en la nube empresarial que aprovecha el procesamiento paralelo masivo (MPP) para ejecutar rápidamente consultas complejas en petabytes de datos, lo que permite que los usuarios identifiquen de manera eficaz los datos financieros. Los usuarios pueden usar consultas T-SQL de PolyBase simples para importar macrodatos a SQL Data Warehouse y usar la potencia de MPP para ejecutar análisis de alto rendimiento.

**SQL Server Reporting Services (SSRS)** : [SQL Server Reporting Services](https://docs.microsoft.com/sql/reporting-services/report-data/sql-azure-connection-type-ssrs) proporciona una rápida creación de informes con tablas, gráficos, mapas, medidores, matrices y más para Azure SQL Data Warehouse.

**Data Catalog**: [Data Catalog](../../data-catalog/overview.md) facilita que los usuarios que administran los datos puedan detectar y comprender los orígenes de datos. En los orígenes de datos comunes se pueden registrar, etiquetar y buscar datos financieros. Los datos permanecen en la ubicación existente, pero se agrega una copia de sus metadatos a Data Catalog, junto con una referencia a la ubicación del origen de datos. Los metadatos también se indexan no solo para que todos los orígenes de datos se puedan detectar fácilmente a través de la búsqueda, sino también para que los usuarios que los detecten puedan comprenderlos.

**Host de tipo bastión**: el host de tipo bastión es el único punto de entrada que permite a los usuarios acceder a los recursos implementados en este entorno. El host de tipo bastión proporciona una conexión segura a los recursos implementados al permitir solo el tráfico remoto desde las direcciones IP públicas de una lista segura. Para permitir el tráfico de escritorio remoto (RDP), el origen del tráfico debe definirse en el grupo de seguridad de red.

Esta solución crea una máquina virtual como host de tipo bastión unido mediante dominio con las siguientes configuraciones:
-   [Extensión antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)
-   [Extensión de Diagnósticos de Azure](../../virtual-machines/windows/extensions-diagnostics-template.md)
-   [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) con Azure Key Vault
-   Una [directiva de apagado automático](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) para reducir el consumo de recursos de la máquina virtual cuando no esté en uso.
-   [Credential Guard de Windows Defender](https://docs.microsoft.com/windows/access-protection/credential-guard/credential-guard) habilitado para que las credenciales y otros secretos se ejecuten en un entorno protegido aislado del sistema operativo en ejecución.

### <a name="virtual-network"></a>Virtual network

La arquitectura de referencia define una red virtual privada con un espacio de direcciones de 10.0.0.0/16.

**Grupos de seguridad de red**: los [grupos de seguridad de red](../../virtual-network/virtual-network-vnet-plan-design-arm.md) contienen listas de control de acceso que permiten o deniegan el tráfico en una red virtual. Los grupos de seguridad de red pueden usarse para proteger el tráfico en una subred o un nivel de máquina virtual individual. Existen los siguientes grupos de seguridad de red:

  - Un grupo de seguridad de red para la capa de datos (instancias de SQL Server Cluster, SQL Server Witness y el equilibrador de la carga de SQL)
  - Un grupo de seguridad de red para el host de tipo bastión de administración
  - Un grupo de seguridad de red para Active Directory
  - Un grupo de seguridad de red de SQL Server Reporting Services

Cada uno de los grupos de seguridad de red tiene puertos y protocolos específicos abiertos para que la solución pueda funcionar de forma segura y correcta. Además, las siguientes opciones de configuración están habilitadas para cada grupo de seguridad de red:

- Los [eventos y registros de diagnóstico](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log) están habilitados y se almacenan en la cuenta de almacenamiento.
- Los registros de Azure Monitor están conectados a los [registros de diagnósticos del grupo de seguridad de red](https://github.com/krnese/AzureDeploy/blob/master/AzureMgmt/AzureMonitor/nsgWithDiagnostics.json).

**Subredes**: cada subred está asociada a su grupo de seguridad de red correspondiente.

### <a name="data-at-rest"></a>Datos en reposo

La arquitectura protege los datos en reposo mediante el cifrado, la auditoría de base de datos y otras medidas.

**Azure Storage**: para cumplir con los requisitos de los datos en reposo cifrados, [Azure Storage](https://azure.microsoft.com/services/storage/) usa [Storage Service Encryption](../../storage/common/storage-service-encryption.md). Esto ayuda a proteger los datos de los titulares de tarjetas en cumplimiento de los compromisos de seguridad de la organización y los requisitos de cumplimiento definidos por PCI DSS 3.2.

**Azure Disk Encryption**: [Azure Disk Encryption](../azure-security-disk-encryption-overview.md) aprovecha la función de BitLocker de Windows para proporcionar el cifrado del volumen de discos de datos y sistema operativo. La solución se integra con Azure Key Vault para ayudar a controlar y administrar las claves de cifrado del disco.

**Azure SQL Database**: La instancia de Azure SQL Database usa las siguientes medidas de seguridad de base de datos:

- La [autenticación y autorización de Active Directory](https://docs.microsoft.com/azure/sql-database/sql-database-aad-authentication) permiten la administración de identidades de usuarios de bases de datos y otros servicios de Microsoft en una ubicación central.
- La [auditoría de bases de datos SQL](../../sql-database/sql-database-auditing.md) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de una cuenta de almacenamiento de Azure.
- Azure SQL Database está configurado para usar el [cifrado de datos transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql), que realiza el cifrado y descifrado en tiempo real de la base de datos, las copias de seguridad asociadas y los archivos de registro de transacciones, para proteger la información en reposo. El cifrado de datos transparente garantiza que los datos almacenados no hayan estado sujetos a un acceso no autorizado.
- Las [reglas de firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) impiden el acceso a los servidores de bases de datos hasta que se otorgan los permisos adecuados. Asimismo, otorgan acceso a las bases de datos según la dirección IP de origen de cada solicitud.
- La [detección de amenazas de SQL](../../sql-database/sql-database-threat-detection.md) permite detectar y responder ante posibles amenazas a medida que se producen, mediante el envío de alertas de seguridad para actividades sospechosas en la base de datos, posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a la base de datos.
- Las [columnas cifradas](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault) garantizan que los datos confidenciales nunca aparezcan como texto no cifrado en el sistema de base de datos. Después de habilitar el cifrado de datos, solo las aplicaciones cliente o los servidores de aplicaciones con acceso a las claves pueden acceder a los datos de texto no cifrado.
- Las [propiedades extendidas](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addextendedproperty-transact-sql) pueden utilizarse para interrumpir el procesamiento de los titulares de los datos, ya que permiten a los usuarios agregar propiedades personalizadas a los objetos de la base de datos y etiquetar los datos como "interrumpidos" para admitir la lógica de la aplicación y evitar el procesamiento de los datos financieros asociados.
- La [seguridad de nivel de fila](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) permite a los usuarios definir directivas para restringir el acceso a los datos a fin de interrumpir su procesamiento.
- El [enmascaramiento dinámico de datos de SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started) limita la exposición de datos confidenciales al enmascarar los datos para usuarios o aplicaciones sin privilegios. El enmascaramiento dinámico de datos puede detectar de forma automática datos potencialmente confidenciales y sugerir las máscaras adecuadas que se pueden aplicar. Esto ayuda a identificar y reducir el acceso a datos de modo que no salgan de la base de datos mediante un acceso no autorizado. Los clientes son responsables de ajustar la configuración del enmascaramiento dinámico de datos para cumplir con el esquema de la base de datos.

### <a name="identity-management"></a>Administración de identidades

Las siguientes tecnologías proporcionan funcionalidades de administración del acceso a datos confidenciales en el entorno de Azure:

- [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) es el directorio multiinquilino basado en la nube y el servicio de administración de identidades de Microsoft. Todos los usuarios de la solución se crean en Azure Active Directory, incluidos los usuarios que acceden a Azure SQL Database.
- La autenticación para acceder a la aplicación se realiza con Azure Active Directory. Para más información, consulte [Integración de aplicaciones con Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md). Además, el cifrado de columnas de la base de datos usa Azure Active Directory para autenticar la aplicación en Azure SQL Database. Para más información, consulte cómo [proteger los datos confidenciales en Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-always-encrypted-azure-key-vault).
- El [control de acceso basado en rol de Azure](../../role-based-access-control/role-assignments-portal.md) permite a los administradores especificar permisos de acceso bien definidos para conceder solo el nivel de acceso que los usuarios necesitan para realizar su trabajo. En lugar de dar a cada usuario permisos ilimitados para los recursos de Azure, los administradores pueden permitir solo ciertas acciones para acceder a los datos. El acceso a la suscripción está limitado al administrador de la suscripción.
- [Azure Active Directory Privileged Identity Management](../../active-directory/privileged-identity-management/pim-getting-started.md) permite a los clientes minimizar el número de usuarios con acceso a determinada información. Los administradores pueden usar Azure Active Directory Privileged Identity Management para detectar, restringir y supervisar las identidades con privilegios y su acceso a los recursos. Esta funcionalidad también puede utilizarse para aplicar un acceso administrativo a petición, Just-in-Time, cuando sea necesario.
- [Azure Active Directory Identity Protection](../../active-directory/identity-protection/overview.md) detecta posibles puntos vulnerables que afectan a las identidades de una organización, configura respuestas automatizadas para las acciones sospechosas detectadas relacionadas con esas identidades, investiga incidentes sospechosos y toma las medidas oportunas para resolverlos.

### <a name="security"></a>Seguridad

**Administración de secretos**: la solución utiliza [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) para la administración de claves y secretos. Azure Key Vault ayuda a proteger claves criptográficas y secretos usados por servicios y aplicaciones en la nube. Las siguientes funcionalidades de Azure Key Vault ayudan a los clientes a proteger los datos y el acceso a dichos datos:

- Se configuran directivas de acceso avanzadas según las necesidades.
- Se definen directivas de acceso a Key Vault con los permisos mínimos requeridos para las claves y los secretos.
- Todas las claves y los secretos en Key Vault tienen fechas de expiración.
- Todas las claves de Key Vault están protegidas por módulos de seguridad de hardware especializados. El tipo de clave es una clave RSA de 2048 bits protegida por HSM.
- A todos los usuarios y entidades se les otorgan los permisos mínimos necesarios mediante el control de acceso basado en rol.
- Los registros de diagnóstico de Key Vault están habilitados con un período de retención de al menos 365 días.
- Las operaciones criptográficas permitidas para las claves están restringidas únicamente a las requeridas.

**Administración de revisiones**: Las máquinas virtuales Windows implementadas como parte de esta arquitectura de referencia se configuran de forma predeterminada para recibir actualizaciones automáticas desde el servicio Windows Update. Esta solución también incluye el servicio [Azure Automation](https://docs.microsoft.com/azure/automation/automation-intro), mediante el cual se pueden crear implementaciones actualizadas para aplicar revisiones a las máquinas virtuales cuando sea necesario.

**Protección contra malware**: [Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware) para máquinas virtuales proporciona una funcionalidad de protección en tiempo real que ayuda a identificar y eliminar virus, spyware y otro software malintencionado, con alertas que se pueden configurar en caso de que un software malintencionado o no deseado conocido se intente instalar o ejecutar en máquinas virtuales protegidas.

**Azure Security Center**: con [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro), los clientes pueden aplicar y administrar de forma centralizada las directivas de seguridad entre cargas de trabajo, limitar la exposición a amenazas y detectar ataques y responder a estos. Además, Azure Security Center accede a las configuraciones existentes de los servicios de Azure para proporcionar recomendaciones de configuración y servicio que ayuden a mejorar la postura de seguridad y a proteger los datos.

Azure Security Center usa una variedad de funcionalidades de detección para alertar a los clientes de posibles ataques contra sus entornos. Estas alertas contienen información útil acerca de lo que desencadenó la alerta, los recursos objetivo y el origen del ataque. Azure Security Center cuenta con un conjunto de [alertas de seguridad predefinidas](https://docs.microsoft.com/azure/security-center/security-center-alerts-type), que se desencadenan cuando tiene lugar una amenaza o actividad sospechosa. Las [reglas de alertas personalizadas](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) de Azure Security Center permiten a los clientes definir nuevas alertas de seguridad basadas en los datos ya recopilados en el entorno.

Azure Security Center proporciona alertas de seguridad e incidentes clasificados por orden de prioridad, lo que facilita a los clientes la detección y solución de posibles problemas de seguridad. Se genera un [informe de inteligencia de amenazas](https://docs.microsoft.com/azure/security-center/security-center-threat-report) por cada amenaza detectada para ayudar a los equipos de respuesta a incidentes a investigar y corregir las amenazas.

### <a name="business-continuity"></a>Continuidad del negocio

**Alta disponibilidad**: las cargas de trabajo del servidor se agrupan en un [conjunto de disponibilidad](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-manage-availability?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para ayudar a garantizar una alta disponibilidad de las máquinas virtuales de Azure. Durante un evento de mantenimiento planeado o no planeado, al menos una máquina virtual está disponible para cumplir el 99,95 % del Acuerdo de Nivel de Servicio de Azure.

**Almacén de Recovery Services**: El [almacén de Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-recovery-services-vault-overview) aloja datos de copia de seguridad y protege todas las configuraciones de máquinas virtuales de Azure en esta arquitectura. Con los almacenes de Recovery Services, los clientes pueden restaurar archivos y carpetas desde una máquina virtual de IaaS sin tener que restaurar toda la máquina virtual, lo que permite unos tiempos de restauración más rápidos.

### <a name="logging-and-auditing"></a>Registro y auditoría

Los servicios de Azure proporcionan un registro completo de la actividad de usuario y del sistema, así como de mantenimiento del sistema:
- **Registros de actividad**: los [registros de actividad](../../azure-monitor/platform/activity-logs-overview.md) proporcionan información detallada sobre las operaciones realizadas en los recursos de la suscripción. Los registros de actividad pueden ayudar a determinar el iniciador de una operación, el momento en que se produce y el estado.
- **Registros de diagnóstico**: los [registros de diagnóstico](../../azure-monitor/platform/diagnostic-logs-overview.md) son todos los registros emitidos por todos los recursos. Estos registros incluyen registros del sistema de eventos de Windows, registros de Azure Storage, registros de auditoría de Key Vault, y registros de firewall y acceso a Application Gateway. Todos los registros de diagnóstico se escriben en una cuenta de almacenamiento de Azure centralizada y cifrada para su archivado. El usuario puede configurar la retención hasta 730 días para cumplir los requisitos de retención específicos de una organización.

**Registros de Azure Monitor**: esos registros se consolidan en [registros de Azure Monitor](https://azure.microsoft.com/services/log-analytics/) para el procesamiento, el almacenamiento y la creación de informes de panel. Una vez recopilados, los datos se organizan en tablas independientes para cada tipo de datos dentro de las áreas de trabajo de Log Analytics, lo que permite que todos los datos se puedan analizar conjuntamente con independencia de su origen. Además, Azure Security Center se integra con los registros de Azure Monitor, lo que permite a los clientes usar consultas de Kusto para acceder a sus datos de eventos de seguridad y combinarlos con datos de otros servicios.

Como parte de esta arquitectura se incluyen las siguientes [soluciones de supervisión](../../monitoring/monitoring-solutions.md) de Azure:
-   [Active Directory Assessment](../../azure-monitor/insights/ad-assessment.md): la solución Active Directory Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
- [SQL Assessment](../../azure-monitor/insights/sql-assessment.md): la solución SQL Health Check evalúa el riesgo y el estado de los entornos de servidor a intervalos regulares y proporciona a los clientes una lista prioritaria de recomendaciones específicas para la infraestructura de servidor implementada.
- [Agent Health](../../monitoring/monitoring-solution-agenthealth.md): la solución Agent Health notifica el número de agentes implementados y su distribución geográfica, así como el número de agentes que no responden y el de agentes que envían datos operativos.
-   [Activity Log Analytics](../../azure-monitor/platform/collect-activity-logs.md): la solución Activity Log Analytics ayuda a los clientes a analizar los registros de actividad de todas las suscripciones de Azure para un cliente.

**Azure Automation**: [Azure Automation](https://docs.microsoft.com/azure/automation/automation-hybrid-runbook-worker) almacena, ejecuta y administra runbooks. En esta solución, los runbooks ayudan a recopilar registros de Azure SQL Database. La solución [Change Tracking](../../automation/change-tracking.md) de Automation permite a los clientes identificar fácilmente los cambios en el entorno.

**Azure Monitor**: [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/) ayuda a los usuarios a realizar un seguimiento del rendimiento, mantener la seguridad e identificar tendencias y permite a las organizaciones auditar, crear alertas y archivar datos, incluido el seguimiento de las llamadas a API en sus recursos de Azure.

## <a name="threat-model"></a>Modelo de amenazas

El diagrama de flujo de datos de esta arquitectura de referencia está disponible para su [descarga](https://aka.ms/pcidss-dw-tm) y se encuentra a continuación. El modelo puede ayudar a los clientes a comprender los puntos de riesgo potencial de la infraestructura del sistema al realizar modificaciones.

![Diagrama de arquitectura de referencia de almacenamiento de datos para PCI DSS](images/pcidss-dw-threat-model.png "Modelo de amenazas de la aplicación web PaaS para PCI DSS")

## <a name="compliance-documentation"></a>Documentación de cumplimiento

En [Azure Security and Compliance Blueprint: matriz de responsabilidades de clientes para PCI DSS](https://aka.ms/pcidss-crm) se enumeran las responsabilidades para todos los requisitos de PCI DSS 3.2.

En [Azure Security and Compliance Blueprint: matriz de implementaciones de almacenamiento de datos para PCI DSS](https://aka.ms/pcidss-dw-cim) se informa sobre qué requisitos de PCI DSS 3.2 aborda la arquitectura de almacenamiento de datos, incluida una descripción detallada de cómo la implementación cumple los requisitos de cada control cubierto.

## <a name="guidance-and-recommendations"></a>Instrucciones y recomendaciones

### <a name="vpn-and-expressroute"></a>VPN y ExpressRoute

Debe configurarse [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o un túnel VPN seguro para establecer una conexión segura a los recursos implementados como parte de esta arquitectura de referencia de almacenamiento de datos. Al configurar una VPN o ExpressRoute adecuadamente, los clientes pueden agregar una capa de protección para los datos en tránsito.

Mediante la implementación de un túnel VPN seguro con Azure, se puede crear una conexión privada virtual entre una red local y una red virtual de Azure. Esta conexión tiene lugar a través de Internet y permite a los clientes colocar con seguridad la información en un "túnel" dentro de un vínculo cifrado entre la red del cliente y Azure. La VPN de sitio a sitio es una tecnología segura y madura que empresas de todos los tamaños han implementado durante décadas. Se utiliza [el modo de túnel IPsec](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2003/cc786385(v=ws.10)) en esta opción como mecanismo de cifrado.

Dado que el tráfico dentro del túnel VPN atraviesa Internet con una VPN de sitio a sitio, Microsoft ofrece otra opción de conexión aún más segura. Azure ExpressRoute es un vínculo de WAN dedicado entre Azure y una ubicación local o un proveedor de hospedaje de Exchange. Como las conexiones de ExpressRoute no se realizan a través de una conexión a Internet, ofrecen más confiabilidad, más velocidad, menor latencia y mayor seguridad que las conexiones a Internet normales. Además, dado que se trata de una conexión directa del proveedor de telecomunicaciones del cliente, los datos no viajan a través de Internet y, por lo tanto, no están expuestos a ella.

Hay [disponibles](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/secure-vnet-hybrid) procedimientos recomendados para implementar una red híbrida segura que extienda una red local a Azure.

### <a name="extract-transform-load-process"></a>Proceso de extracción, transformación y carga

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) puede cargar datos en Azure SQL Data Warehouse sin necesidad de una herramienta independiente para extraer, transformar, cargar o importar. PolyBase permite el acceso a los datos mediante consultas T-SQL. Con PolyBase se puede usar la inteligencia empresarial y la pila de análisis de Microsoft, así como herramientas de terceros compatibles con SQL Server.

### <a name="azure-active-directory-setup"></a>Configuración de Azure Active Directory

[Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) es esencial para la administración de la implementación y la concesión de acceso a las personas que interactúan con el entorno. Se puede integrar una instancia de Windows Server Active Directory con Azure Active Directory en [cuatro clics](../../active-directory/hybrid/how-to-connect-install-express.md). Los clientes también pueden enlazar la infraestructura de Active Directory implementada (controladores de dominio) con una instancia de Azure Active Directory existente al convertir la infraestructura de Active Directory implementada en un subdominio de un bosque de Azure Active Directory.

### <a name="optional-services"></a>Servicios opcionales

Azure ofrece una variedad de servicios para ayudar con el almacenamiento y el proceso de almacenamiento provisional de datos con formato y sin formato. Los servicios siguientes se pueden agregar a esta arquitectura de referencia en función de los requisitos del cliente:
-   [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction) es un servicio en la nube administrado creado para complejos proyectos híbridos de extracción, transformación y carga e integración de datos. Azure Data Factory tiene funcionalidades para ayudar a hacer seguimiento de los datos personales y ubicarlos, incluidas herramientas de visualización y supervisión para identificar cuándo llegaron los datos y de dónde provienen. Con Azure Data Factory, los clientes pueden crear y programar flujos de trabajo basados en datos (llamados canalizaciones) que ingieren datos de distintos almacenes de datos. Estas canalizaciones permiten que los clientes ingieran datos desde orígenes internos y externos. Después, pueden procesar y transformar los datos para la salida a almacenes de datos como Azure SQL Data Warehouse.
- Los clientes pueden almacenar de manera provisional los datos no estructurados en [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview), lo que permite capturar datos de cualquier tamaño, tipo y velocidad de ingesta en un único lugar para realizar análisis exploratorios y operativos.  Azure Data Lake tiene funcionalidades que permiten la extracción y conversión de los datos. Azure Data Lake Store es compatible con la mayoría de componentes de código abierto del ecosistema Hadoop y se integra perfectamente con otros servicios de Azure, como Azure SQL Data Warehouse.

## <a name="disclaimer"></a>Renuncia de responsabilidades

 - Este documento es meramente informativo. MICROSOFT NO EFECTÚA NINGÚN TIPO DE GARANTÍA, YA SEA EXPRESA, IMPLÍCITA O ESTATUTARIA, SOBRE LA INFORMACIÓN INCLUIDA EN EL PRESENTE DOCUMENTO. Este documento se proporciona "tal cual". La información y las opiniones expresadas en este documento, como las direcciones URL y otras referencias a sitios web de Internet, pueden cambiar sin previo aviso. Los clientes que lean este documento se atienen a las consecuencias de usarlo.
 - Este documento no proporciona a los clientes ningún derecho legal sobre ninguna propiedad intelectual de ningún producto o solución de Microsoft.
 - Los clientes pueden copiar y usar este documento con fines internos y de referencia.
 - Algunas de las recomendaciones de este documento pueden provocar un aumento del uso de datos, de la red o de los recursos de procesos en Azure, lo que podría incrementar los costos de las licencias o las suscripciones de Azure.
 - Esta arquitectura está diseñada para servir como base para que los clientes puedan ajustarse a sus requisitos específicos y no debe usarse tal cual en un entorno de producción.
 - Este documento se ha desarrollado como referencia y no debe usarse para definir todos los medios por los que un cliente puede cumplir normas y requisitos de cumplimiento específicos. Los clientes deben buscar apoyo legal de su organización sobre las implementaciones de cliente aprobadas.
