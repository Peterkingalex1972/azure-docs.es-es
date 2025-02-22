---
title: ¿Qué es el servicio Azure SQL Database? | Microsoft Docs
description: 'Obtenga una introducción a Azure SQL Database: detalles técnicos y funcionalidades del sistema de administración de bases de datos relacionales (RDBMS) de Microsoft en la nube.'
keywords: introducción a sql,introducción a sql,qué es base de datos sql
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/08/2019
ms.openlocfilehash: 1c4fae5c41f4f23c4a7fe3135b602133aa69aacd
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873708"
---
# <a name="what-is-azure-sql-database-service"></a>Qué es el servicio Azure SQL Database

Azure SQL Database es un servicio administrado de base de datos relacional de uso general que le permite crear una capa de almacenamiento de datos de alto rendimiento y alta disponibilidad para las aplicaciones y soluciones en la nube de Microsoft Azure. SQL Database puede ser la opción adecuada para usar una variedad de aplicaciones modernas en la nube, porque le permite usar funcionalidades eficaces para procesar tanto datos relacionales como [estructuras no relacionales](sql-database-multi-model-features.md) como, por ejemplo, gráficos, JSON, elementos espaciales y XML. Se basa en la última versión estable del [motor de base de datos de Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) y le permite usar un amplio conjunto de características avanzadas de procesamiento de consultas, como [tecnologías de memoria de alto rendimiento](sql-database-in-memory.md) y el [procesamiento inteligente de consultas](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing?toc=/azure/sql-database/toc.json).
Con la estrategia de primero en la nube de Microsoft, las funcionalidades más recientes de SQL Server se publican en primer lugar en SQL Database y, después, en el propio SQL Server. Este enfoque proporciona las funcionalidades más recientes de SQL Server sin sobrecarga alguna en la aplicación de revisiones o actualizaciones (y con estas nuevas características probadas en millones de bases de datos). SQL Database le permite definir y escalar fácilmente el rendimiento de dos modelos de compra diferentes: un [modelo de compra basado en el núcleo virtual](sql-database-service-tiers-vcore.md) y un [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md). Asimismo, SQL Database es un servicio totalmente administrado con una alta disponibilidad, copias de seguridad y otras operaciones de mantenimiento comunes totalmente integradas. Microsoft controla perfectamente toda la aplicación de revisiones y de actualizaciones del código de SQL y del sistema operativo, y hace desaparecer toda la administración de la infraestructura subyacente.

> [!NOTE]
> Para obtener un glosario de términos en Azure SQL Database, consulte el [glosario de términos para SQL Database](sql-database-glossary-terms.md).

Azure SQL Database proporciona las siguientes opciones de implementación para una base de datos de Azure SQL:

![deployment-options](./media/sql-database-technical-overview/deployment-options.png)

- La [base de datos única](sql-database-single-database.md) representa una base de datos aislada y totalmente administrada que es la elección perfecta para las aplicaciones modernas en la nube y los microservicios que necesitan un único origen de datos confiable. Una base de datos única es similar a una [base de datos independiente](https://docs.microsoft.com/sql/relational-databases/databases/contained-databases?toc=/azure/sql-database/toc.json) del [motor de base de datos de Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json).
- La [instancia administrada](sql-database-managed-instance.md) es una instancia completamente administrada del [motor de base de datos de Microsoft SQL Server](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database/toc.json) que contiene un conjunto de bases de datos que se pueden usar juntas. Es una opción perfecta para realizar una migración fácil de bases de datos locales de SQL Server a la nube de Azure y para aplicaciones que necesitan aprovechar las potentes características que proporciona el motor de base de datos de SQL Server.
- El [grupo elástico](sql-database-elastic-pool.md) es una colección de [bases de datos únicas](sql-database-single-database.md) con un conjunto compartido de recursos como la CPU o la memoria. Las bases de datos únicas se pueden mover dentro y fuera de un grupo elástico.

> [!IMPORTANT]
> Para entender las diferencias de las características entre SQL Database y SQL Server, así como las diferencias entre las distintas opciones de implementación de Azure SQL Database, consulte las [características de SQL](sql-database-features.md).

SQL Database ofrece un rendimiento predecible con varios tipos de recursos, niveles de servicio y tamaños de proceso que proporciona escalabilidad dinámica sin tiempo de inactividad, optimización inteligente integrada, escalabilidad y disponibilidad globales, y opciones de seguridad avanzadas (todo ello casi sin necesidad de administración). Estas funcionalidades permiten centrarse en el desarrollo rápido de aplicaciones y en reducir el plazo de acceso al mercado, en lugar de tener que dedicar tiempo y recursos a la administración tanto de máquinas virtuales como de la infraestructura. El servicio SQL Database está actualmente en 38 centros de datos de todo el mundo, y constantemente se incorporan más, lo que le permite ejecutar la base de datos en un centro de datos próximo.

## <a name="scalable-performance-and-pools"></a>Grupos y rendimiento escalable

Todas las versiones de SQL Database permiten definir la cantidad de recursos que se asignarán. 
- Con bases de datos únicas, las bases de datos están aisladas entre sí, son portátiles y cada una tiene su propia cantidad de recursos de proceso, memoria y almacenamiento garantizados. La cantidad de recursos asignados a la base de datos está dedicada a esa base de datos y no se compartirá con otras bases de datos en la nube de Azure. También ofrece la posibilidad de [escalar los recursos de una base de datos única](sql-database-single-database-scale.md). La base de datos única proporciona diferentes recursos de proceso, memoria y almacenamiento para diferentes necesidades que varían de 1 a 80 núcleos virtuales, de 32 a 4 TB, etc. El [nivel de servicio de hiperescala](sql-database-service-tier-hyperscale.md) para la base de datos única le permite escalar a 100 TB, con funcionalidades rápidas de copia de seguridad y restauración.
- Gracias a los grupos elásticos, puede asignar recursos que compartirán todas las bases de datos del grupo. Asimismo, puede crear nuevas bases de datos o mover bases de datos únicas y a existentes en un grupo de recursos para maximizar el uso de los recursos y ahorrar dinero; además, tendrá la capacidad de [aumentar y reducir de manera dinámica los recursos del grupo elástico](sql-database-elastic-pool-scale.md).
- Con las instancias administradas, cada instancia administrada está aislada de otras instancias con recursos garantizados. Dentro de una instancia administrada, las bases de datos de instancia comparten un conjunto de recursos y la capacidad de [aumentar y reducir de manera dinámica los recursos de la instancia administrada](sql-database-managed-instance-resource-limits.md).

La primera aplicación se puede compilar en una base de datos pequeña con un costo muy pequeño al mes en el nivel de servicio de uso general y, después, cambiar el nivel de servicio manualmente o mediante programación en cualquier momento al nivel de servicio crítico para la empresa para adecuarlo a las necesidades de su solución. El rendimiento se puede ajustar sin que la aplicación o los clientes sufran ningún tipo de inactividad. La escalabilidad dinámica permite que una base de datos responda transparentemente a los requisitos de recursos, que cambian con rapidez, y le permite pagar solo por los recursos que necesite cuando los necesite.

La escalabilidad dinámica es diferente del escalado automático. El escalado automático se produce al escalarse un servicio automáticamente en función de determinados criterios, mientras la escalabilidad dinámica permite el escalado manual sin tiempo de inactividad. Una base de datos única admite la escalabilidad dinámica manual, pero no la escalabilidad automática. Para ganar experiencia con el uso *automático*, considere los grupos elásticos, que permiten que las bases de datos compartan recursos en un grupo en función de las necesidades individuales de las bases de datos. Pero hay scripts que pueden ayudar a automatizar la escalabilidad en una base de datos única. Consulte [Uso de PowerShell para supervisar y escalar una sola base de datos SQL](scripts/sql-database-monitor-and-scale-database-powershell.md) encontrará un ejemplo.

### <a name="purchasing-models-service-tiers-compute-sizes-and-storage-amounts"></a>Modelos de compra, niveles de servicio, tamaños de proceso y cantidades de almacenamiento

SQL Database ofrece dos modelos de compra:
- El [modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md) le permite elegir el número de núcleos virtuales, la cantidad de memoria y la cantidad y velocidad del almacenamiento. El modelo de compra basado en núcleo virtual también le permite usar la [Ventaja híbrida de Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para ahorrar en los costos. Para más información sobre la Ventaja híbrida de Azure, consulte las [preguntas frecuentes](#sql-database-frequently-asked-questions-faq).
- El [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) ofrece una combinación de recursos de proceso, memoria y E/S en tres niveles de servicio para admitir cargas de trabajo de base de datos de ligeras a pesadas. Los tamaños de proceso de cada nivel ofrecen una combinación diferente de estos recursos, a los que puede agregar recursos de almacenamiento adicionales.

### <a name="elastic-pools-to-maximize-resource-utilization"></a>Grupos elásticos para maximizar la utilización de los recursos

Para muchas empresas y aplicaciones, poder crear bases de datos individuales y aumentar o reducir el rendimiento a petición es suficiente, especialmente si los patrones de uso son relativamente predecibles. Pero si dichos patrones son impredecibles, pueden dificultar la administración de los costos y del modelo de negocio. Los [grupos elásticos](sql-database-elastic-pool.md) están diseñadas para solucionar este problema. El concepto es sencillo. Se asignan los recursos de rendimiento a un grupo, en lugar a una base de datos individual y se paga por los recursos de rendimiento colectivos del grupo, no por el rendimiento de la base de datos única.

   ![grupos elásticos](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

Con los grupos elásticos no es preciso centrarse en marcar el ascenso y la bajada de rendimiento de la base de datos a medida que varía la demanda de recursos. Las bases de datos agrupadas consumen los recursos de rendimiento del grupo elástico a medida que se necesiten. Las bases de datos agrupadas consumen los recursos, pero nunca superan los límites del grupo, por lo que el costo es en todo momento predecible, aunque el uso de las bases de datos individuales no lo sea. Es más, puede [agregar bases de datos al grupo y quitarlas del mismo](sql-database-elastic-pool-manage-portal.md), lo que escala la aplicación de un puñado de bases de datos a miles, y todo sin perder el control del presupuesto. También puede controlar el número mínimo y máximo de recursos disponibles para las bases de datos agrupadas, con el fin de asegurarse de que ninguna de ellas utiliza todos los recursos del grupo y que todas las bases de datos agrupadas tienen un número mínimo garantizado de recursos. Para más información acerca de los modelos de diseño de las aplicaciones SaaS que usan grupos elásticos, consulte [Modelos de diseño para las aplicaciones SaaS multiinquilino y SQL Database de Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

Los scripts pueden ayudarle con la supervisión y el escalado de grupos elásticos. En [Uso de PowerShell para supervisar y escalar un grupo elástico de SQL en Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md) encontrará un ejemplo

> [!IMPORTANT]
> Una instancia administrada no admite grupos elásticos. En su lugar, una instancia administrada es una colección de bases de datos de instancia que comparten los recursos de la instancia administrada.

### <a name="blend-single-databases-with-pooled-databases"></a>Fusión de bases de datos únicas con bases de datos agrupadas

Puede fusionar bases de datos únicas con grupos elásticos y cambiar los niveles de servicio tanto de las primeras como de los segundos de forma rápida y sencilla para adaptarlos a su situación. Con la potencia y el alcance de Azure, puede combinar otros servicios de Azure con SQL Database para satisfacer sus necesidades únicas de diseño de aplicaciones, impulsar las eficiencias de costos y recursos, y acceder a nuevas oportunidades de negocio.

## <a name="extensive-monitoring-and-alerting-capabilities"></a>Extensas funcionalidades de supervisión y alerta

Azure SQL Database proporciona un conjunto de características avanzadas de supervisión y solución de problemas que pueden ayudarle a obtener todos los detalles referentes a las características de la carga de trabajo. Las características y herramientas pueden clasificarse como:
 - Funcionalidades de supervisión integradas que proporciona la última versión del motor de base de datos de SQL Server, que le permitirán buscar información detallada del rendimiento en tiempo real. 
 - Funcionalidades de supervisión de PaaS que proporciona la plataforma Azure y que le permiten supervisar fácilmente una gran cantidad de instancias de bases de datos; también le proporcionan consejos para la solución de problemas que pueden ayudarle a solucionar problemas de rendimiento.

La característica de supervisión del motor de base de datos integrada más importante que debe aprovechar, es el componente [Almacén de datos de consultas](sql-database-operate-query-store.md) que registra el rendimiento de sus consultas en tiempo real y le permite identificar los posibles problemas de rendimiento y a los principales consumidores de recursos. El ajuste automático y las recomendaciones le proporcionarán consejos relativos a las consultas acerca del rendimiento limitado y a los índices que faltan o que están duplicados. El ajuste automático en Azure SQL Database le permite aplicar manualmente los scripts que pueden solucionar los problemas o puede permitir que Azure SQL Database aplique la solución y pruebe y compruebe si esta es ventajosa y si debe guardar o revertir el cambio según el resultado. Además de las capacidades del Almacén de datos de consultas y de ajuste automático, también puede usar los elementos [DMV y XEvent](sql-database-monitoring-with-dmvs.md) estándar para supervisar el rendimiento de la carga de trabajo.

La plataforma Azure proporciona herramientas de [supervisión](sql-database-performance.md) y [alertas](sql-database-insights-alerts-portal.md) de rendimiento integradas, combinadas con las clasificaciones de rendimiento, que le permiten supervisar fácilmente el estado de miles de bases de datos. Uso de estas herramientas, puede evaluar rápidamente el impacto de escalar verticalmente en función de su suscripción actual o se proyecta necesidades de rendimiento. Además, SQL Database puede [emitir métricas y registros de diagnóstico](sql-database-metrics-diag-logging.md) para facilitar la supervisión. SQL Database se puede configurar para que almacene el uso de recursos, los trabajadores y sesiones, y la conectividad en uno de estos recursos de Azure:

- **Azure Storage**: Para archivar grandes cantidades de telemetría a un pequeño precio
- **Azure Event Hub**: Para integrar la telemetría de SQL Database con una solución de supervisión personalizada o canalizaciones activas
- **Registros de Azure Monitor**: Para la solución de supervisión integrada con funcionalidades de generación de informes, alertas y mitigación.

    ![arquitectura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="availability-capabilities"></a>Funcionalidades de disponibilidad

En un entorno tradicional de SQL Server, en general tendría (al menos) 2 máquinas configuradas localmente con copias exactas (mantenidas de forma sincrónica) de los datos (con características como grupos de disponibilidad AlwaysOn o instancias de clúster de conmutación por error) para protegerse frente a un error de una sola máquina o un solo componente. Esto proporciona alta disponibilidad, pero no protege de la destrucción del centro de datos por un desastre natural.

La recuperación ante desastres da por supuesto que la localización geográfica de un evento catastrófico será lo suficientemente precisa para tener otra máquina u otro conjunto de máquinas con una copia lejana de los datos.  En SQL Server, puede usar grupos de disponibilidad AlwaysOn que se ejecuten en modo asincrónico para obtener esta funcionalidad.  Los problemas de gran velocidad suelen traducirse en que la gente no quiere esperar a que se produzca la replicación tan lejana para confirmar una transacción, por lo que se pueden perder datos al producirse conmutaciones por error no planeadas.

Las bases de datos de los niveles de servicio prémium y crítico para la empresa ya [hacen algo muy parecido](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) a la sincronización de un grupo de disponibilidad. Las bases de datos de los niveles de servicio menores proporcionan redundancia mediante almacenamiento con un [mecanismo distinto pero equivalente](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability). Hay lógica que protege contra los errores de máquinas individuales.  La característica de replicación geográfica activa proporciona la capacidad de protegerse frente a desastres cuando se destruye toda una región.

Azure Availability Zones tiene un papel en el problema de la alta disponibilidad.  Intenta proteger contra la interrupción de un edificio con un solo centro de datos dentro de una región.  Por lo tanto, está diseñado para la protección ante la interrupción de alimentación eléctrica o de red en un edificio. En SQL Azure, esto funciona mediante la colocación de las diferentes réplicas en zonas de disponibilidad diferentes (eficazmente en distintos edificios) que, por lo demás, funcionan como antes.

De hecho, el Acuerdo de Nivel de Servicio [(SLA)](https://azure.microsoft.com/support/legal/sla/) de Azure, con una disponibilidad del 99,99 % líder del sector y con la tecnología de una red global de centros de datos administrados por Microsoft, ayuda a mantener las aplicaciones en funcionamiento de forma ininterrumpida. La plataforma de Azure administra completamente cada base de datos y garantiza un alto porcentaje de disponibilidad de los datos sin pérdida de datos. Azure controla automáticamente la aplicación de revisiones, las copias de seguridad, la replicación, la detección de errores, los posibles errores de hardware, de software o de red subyacentes, la implementación de correcciones de errores, las conmutaciones por error, las actualizaciones de base de datos y otras tareas de mantenimiento. La disponibilidad Estándar se consigue mediante una separación de las capas de proceso y de almacenamiento. La disponibilidad Premium se logra mediante la integración de los recursos de proceso y almacenamiento en un único nodo para obtener un buen rendimiento y, después, mediante la implementación de tecnología similar para los grupos de disponibilidad AlwaysOn en segundo plano. Para obtener una explicación completa de las funcionalidades de alta disponibilidad de Azure SQL Database, consulte [Disponibilidad de SQL Database](sql-database-high-availability.md). Además, SQL Database proporciona características de [continuidad empresarial y escalabilidad global](sql-database-business-continuity.md) integradas, entre las que se incluyen:

- **[Copias de seguridad automáticas](sql-database-automated-backups.md)** :

  SQL Database realiza automáticamente copias de seguridad de registros de transacciones completas y diferenciales de las bases de datos de Azure SQL para permitirle hacer una restauración a cualquier momento dado. En el caso de las bases de datos únicas y las bases de datos agrupadas, puede configurar SQL Database para almacenar copias de seguridad de bases de datos completas en el almacenamiento de Azure para la retención de copias de seguridad a largo plazo. Para las instancias administradas, también puede realizar copias de seguridad solo de copia para la retención de copias de seguridad a largo plazo.

- **[Restauraciones a un momento dado](sql-database-recovery-using-backups.md)** :

  Todas las opciones de implementación de SQL Database admite la recuperación a un momento dado dentro del período de retención de copias de seguridad automáticas de cualquier base de datos de Azure SQL.
- **[Replicación geográfica activa](sql-database-active-geo-replication.md)** :

  La base de datos única y las bases de datos agrupadas permiten configurar hasta cuatro bases de datos secundarias legibles en los mismos centros de datos de Azure o en centros de datos distribuidos globalmente.  Por ejemplo, si tiene una aplicación SaaS con una base de datos de catálogos tiene un alto volumen de transacciones simultáneas de solo lectura, utilice la replicación geográfica activa para habilitar la escala de lectura global y quitar cuellos de botella en el servidor principal debidos a las cargas de trabajo de lectura. En el caso de las instancias administradas, use grupos de conmutación por error automática.
- **[Grupos de conmutación por error automática](sql-database-auto-failover-group.md)** :

  Todas las opciones de implementación de SQL Database permiten usar los grupos de conmutación por error para habilitar la alta disponibilidad y el equilibrio de carga a escala global, lo que incluye la replicación geográfica transparente y la conmutación por error de grandes conjuntos de bases de datos, grupos elásticos e instancias administradas. Los grupos de conmutación por error permiten la creación de aplicaciones SaaS distribuidas globalmente con una sobrecarga de administración mínima, lo que deja la supervisión compleja, el enrutamiento y la orquestación de la conmutación por error a SQL Database.
- **[Bases de datos con redundancia de zona](sql-database-high-availability.md)** :

  SQL Database le permite aprovisionar bases de datos de nivel Premium o Crítico para la empresa o grupos elásticos a través de varias zonas de disponibilidad. Dado que tanto estas bases de datos como los grupos elásticos tienen varias réplicas redundantes para lograr la alta disponibilidad, la colocación de estas réplicas en varias zonas de disponibilidad proporciona mayor resistencia, lo que incluye la capacidad de recuperarse automáticamente de errores de escala de centro de datos sin pérdida de datos.

## <a name="built-in-intelligence"></a>Inteligencia integrada

Con SQL Database, obtendrá la inteligencia integrada que le ayudará a reducir drásticamente los costos de ejecutar y administrar bases de datos y maximiza el rendimiento y seguridad de la aplicación. Mediante la ejecución de millones de cargas de trabajo de clientes las 24 horas del día, SQL Database recopila y procesa una cantidad masiva de datos de telemetría y, al mismo tiempo, respeta totalmente la privacidad de los clientes. Varios algoritmos evalúan continuamente los datos de telemetría para que el servicio pueda obtener información de la aplicación y adaptarse a ella. En función de este análisis, el servicio proporciona recomendaciones para mejorar el rendimiento que se adaptan a su carga de trabajo concreta.

### <a name="automatic-performance-monitoring-and-tuning"></a>Supervisión y ajuste del rendimiento automático

SQL Database proporciona información detallada de las consultas que necesita supervisar. SQL Database aprende sus patrones de base de datos y permite adaptar el esquema de la base de datos a su carga de trabajo. SQL Database proporciona [recomendaciones para el ajuste del rendimiento](sql-database-advisor.md), donde puede consultar las acciones de ajuste y aplicarlas.

Sin embargo, supervisar constantemente una base de datos es una tarea ardua y tediosa, sobre todo cuando se trabaja con muchas bases de datos. [Intelligent Insights](sql-database-intelligent-insights.md) realiza este trabajo automáticamente mediante la supervisión del rendimiento de SQL Database a escala y le informa de los problemas de degradación del rendimiento, identifica la causa principal del problema y proporciona recomendaciones para la mejora del rendimiento cuando es posible.

La administración de un número ingente de bases de datos podría ser imposible de realizar eficazmente, ni siquiera con todas las herramientas e informes que proporcionan SQL Database y Azure Portal. En lugar de supervisar y ajustar la base de datos manualmente, puede considerar la posibilidad de delegar algunas de las acciones de supervisión y ajuste en SQL Database con el [ajuste automático](sql-database-automatic-tuning.md). SQL Database aplica automáticamente las recomendaciones y pruebas, y comprueba cada una de sus acciones de ajuste para garantizar que el rendimiento no deje de mejorar. De esta forma, SQL Database se adapta automáticamente a su carga de trabajo de una forma controlada y segura. El ajuste automático significa que el rendimiento de la base de datos se supervisa y compara meticulosamente antes y después de cada acción de ajuste, y si el rendimiento no mejora, la acción se revierte.

En la actualidad, muchos de nuestros asociados que ejecutan [aplicaciones SaaS multiinquilino](sql-database-design-patterns-multi-tenancy-saas-applications.md) sobre SQL Database confían en el ajuste automático del rendimiento para asegurarse de que sus aplicaciones siempre tienen un rendimiento estable y predecible. Para ellos, esta característica reduce enormemente el riesgo de que se produzca un incidente de rendimiento durante la noche. Además, puesto que una parte de su base de clientes también utiliza SQL Server, usan las mismas recomendaciones de indización que proporciona SQL Database para ayudar a sus clientes de SQL Server.

Hay dos aspectos del ajuste automático [disponibles en SQL Database](sql-database-automatic-tuning.md):

- **Administración automática de índices**: Identifica tanto los índices que se deben agregar a la base de datos como los que se deben quitar.
- **Corrección automática de planes**: Identifica planes problemáticos y corrige los problemas de rendimiento de los planes de SQL (próximamente, ya disponible en SQL Server 2017).

### <a name="adaptive-query-processing"></a>Procesamiento adaptable de consultas

También vamos a agregar la familia de características de [procesamiento adaptable de consultas](/sql/relational-databases/performance/intelligent-query-processing) a SQL Database, lo que incluye la ejecución intercalada de funciones con valores de tabla de varias instrucciones, comentarios de concesión de memoria del modo por lotes y combinaciones adaptables del modo por lotes. Cada una de estas características del procesamiento adaptable de consultas aplica técnicas de "aprendizaje y adaptación" similares, lo que ayuda a solucionar los problemas de rendimiento relacionados con problemas de optimización de consultas históricamente intrincados.

## <a name="advanced-security-and-compliance"></a>Conformidad y seguridad avanzada

SQL Database proporciona varias [características integradas de seguridad y cumplimiento](sql-database-security-overview.md) que facilitan que su aplicación cumpla los distintos requisitos de seguridad y cumplimiento normativo.

> [!IMPORTANT]
> Azure SQL Database (todas las opciones de implementación) ha obtenido la certificación de diversas normas de cumplimiento. Para obtener más información, vea el [Centro de confianza de Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), donde encontrará la lista más reciente de certificaciones de cumplimiento de SQL Database.

### <a name="advance-threat-protection"></a>Protección contra amenazas avanzada

Advanced Data Security es un paquete unificado de funcionalidades avanzadas de seguridad de SQL. Incluye una funcionalidad para detectar y clasificar datos confidenciales, administrar los puntos vulnerables de una base de datos y detectar actividades anómalas que puedan indicar una amenaza para dicha base de datos. Proporciona una ubicación única para habilitar y administrar estas funcionalidades.

- [Clasificación y detección de datos](sql-database-data-discovery-and-classification.md):

  Esta característica (actualmente en versión preliminar) proporciona funcionalidades integradas en Azure SQL Database para detectar, clasificar, etiquetar y proteger la información confidencial de las bases de datos. Se puede utilizar para proporcionar visibilidad del estado de clasificación de una base de datos y para realizar un seguimiento del acceso a información confidencial dentro de la base de datos y más allá de sus límites.
- [Evaluación de vulnerabilidades](sql-vulnerability-assessment.md):

  Este servicio puede detectar y realizar un seguimiento de posibles vulnerabilidades de la base de datos, así como ayudarle a corregirlas. Permite ver el estado de la seguridad e incluye los pasos necesarios para resolver problemas de seguridad y mejorar las defensas de cualquier base de datos.
- [Detección de amenazas](sql-database-threat-detection.md):

  Esta característica detecta actividades anómalas que indiquen intentos inusuales y potencialmente perjudiciales de acceder a una base de datos o de aprovechar sus vulnerabilidades. Supervisa constantemente una base de datos para detectar actividades sospechosas y proporciona de forma inmediata alertas de seguridad de posibles puntos vulnerables, ataques por inyección de código SQL y patrones anómalos de acceso a las bases de datos. Las alertas de detección de amenazas proporcionan detalles de la actividad sospechosa y recomiendan acciones para investigar y mitigar la amenaza.

### <a name="auditing-for-compliance-and-security"></a>Auditoría de seguridad y cumplimiento

La [auditoría](sql-database-auditing.md) realiza un seguimiento de eventos de bases de datos y los escribe en un registro de auditoría de su cuenta de Azure Storage. La auditoría puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias y anomalías que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

### <a name="data-encryption"></a>Cifrado de datos

Para proteger los datos, SQL Database cifra los datos en movimiento a través del protocolo de [Seguridad de la capa de transporte](https://support.microsoft.com/kb/3135244), los datos en reposo a través del [Cifrado de datos transparente](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql) y los datos en uso a través de [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine).

### <a name="azure-active-directory-integration-and-multi-factor-authentication"></a>Integración de Azure Active Directory y autenticación multifactor

SQL Database permite administrar centralmente las identidades de usuario de base de datos y otros servicios de Microsoft con la [integración de Azure Active Directory](sql-database-aad-authentication.md). Esta funcionalidad simplifica la administración de permisos y mejora la seguridad. Azure Active Directory admite la [autenticación multifactor](sql-database-ssms-mfa-authentication.md) (MFA) para aumentar la seguridad tanto de los datos como de las aplicaciones y admite un proceso de inicio de sesión único.

### <a name="compliance-certification"></a>Certificación de cumplimiento

SQL Database participa en auditorías periódicas y se ha certificado con varios estándares de cumplimiento. Para obtener más información, vea el [Centro de confianza de Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942), donde encontrará la lista más reciente de certificaciones de cumplimiento de SQL Database.

## <a name="easy-to-use-tools"></a>Herramientas fáciles de usar

SQL Database facilita la creación y el mantenimiento de aplicaciones y aumenta su productividad. SQL Database le permite centrarse en lo que mejor hace: crear magníficas aplicaciones. En SQL Database, puede realizar labores de administración y desarrollo mediante las herramientas y los conocimientos que ya posee.

- **[Azure Portal](https://portal.azure.com/)** :

  Aplicación web para administrar todos los servicios de Azure.
- **[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)** :

  Aplicación cliente gratuita que se puede descargar para administrar cualquier infraestructura de SQL, desde SQL Server hasta SQL Database.
- **[SQL Server Data Tools en Visual Studio](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt)** :

  Aplicación cliente gratuita que se puede descargar para desarrollar bases de datos relacionales de SQL Server, bases de datos de Azure SQL, paquetes de Integration Services, modelos de datos de Analysis Services e informes de Reporting Services.
- **[Visual Studio Code](https://code.visualstudio.com/docs)** :

  Editor de código abierto, gratuito y que se puede descargar para Windows, macOS y Linux que admite extensiones, entre las que se incluye la [extensión mssql](https://aka.ms/mssql-marketplace), para realizar consultas en Microsoft SQL Server, Azure SQL Database y SQL Data Warehouse.

SQL Database admite la compilación de aplicaciones con Python, Java, Node.js, PHP, Ruby y .NET en MacOS, Linux y Windows. SQL Database admite las mismas [bibliotecas de conexiones](sql-database-libraries.md) como SQL Server.

[!INCLUDE [sql-database-create-manage-portal](includes/sql-database-create-manage-portal.md)]

## <a name="sql-database-frequently-asked-questions-faq"></a>Preguntas más frecuentes sobre SQL Database

### <a name="what-is-the-current-version-of-sql-database"></a>¿Cuál es la versión actual de SQL Database?

La versión actual de SQL Database es V12. Se ha retirado la versión V11.

### <a name="can-i-control-when-patching-downtime-occurs"></a>¿Puedo controlar cuando se produce el tiempo de inactividad de la aplicación de revisiones?

No. El impacto de la aplicación de revisiones no suele ser perceptible si [usa una lógica de reintento](sql-database-develop-overview.md#resiliency) en la aplicación. Para más información sobre cómo prepararse para los eventos de mantenimiento planeado en su base de datos de Azure SQL, consulte [Planeación de los eventos de mantenimiento en Azure SQL Database](sql-database-planned-maintenance.md).

### <a name="azure-hybrid-benefit-questions"></a>Preguntas de la Ventaja híbrida de Azure

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>¿Hay derechos de doble uso con Ventaja híbrida de Azure para SQL Server?

Dispone de 180 días de derechos de doble uso de la licencia para asegurarse de que las migraciones se ejecutan sin problemas. Transcurrido dicho período, la licencia de SQL Server solo puede usarse en la nube en SQL Database, y carece de derechos de doble uso en el entorno local y en la nube.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>¿En qué se diferencia la Ventaja híbrida de Azure para SQL Server de Movilidad de licencias?

En la actualidad, ofrecemos las ventajas de la movilidad de licencias a los clientes de SQL Server con Software Assurance, lo que permite la reasignación de sus licencias a servidores compartidos de terceros. Esta ventaja puede usarse en IaaS de Azure y AWS EC2.
La Ventaja híbrida de Azure para SQL Server se diferencia de la movilidad de licencias en dos áreas principales:

- Proporciona ventajas económicas para mover cargas de trabajo muy virtualizadas a Azure. Los clientes de SQL EE pueden obtener 4 núcleos en Azure en la SKU de uso general por cada núcleo que posean en el entorno local para aplicaciones muy virtualizadas. La movilidad de licencias no ofrece ninguna ventaja especial sobre los costos de mover cargas de trabajo virtualizadas a la nube.
- Se proporciona para destinos PaaS en Azure (Instancia administrada de SQL Database) que son muy compatibles con SQL Server local.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>¿Cuáles son los derechos específicos de la Ventaja híbrida de Azure para SQL Server?

Los clientes de SQL Database tendrán asociados los siguientes derechos con la Ventaja híbrida de Azure para SQL Server:

|Superficie de licencia|¿Qué le permite obtener la Ventaja híbrida de Azure para SQL Server?|
|---|---|
|Clientes de núcleo de SQL Server Enterprise Edition con SA|<li>Puede pagar la tasa base sobre la SKU De uso general o Crítico para la empresa</li><br><li>1 núcleo local = 4 núcleos en la SKU De uso general</li><br><li>1 núcleo local = 1 núcleo en SKU Crítico para la empresa</li>|
|Clientes de núcleo de SQL Server Standard Edition con SA|<li>Puede pagar la tasa base solo sobre la SKU De uso general</li><br><li>1 núcleo local = 1 núcleo en la SKU De uso general</li>|
|||

## <a name="engage-with-the-sql-server-engineering-team"></a>Contactar con el equipo de ingeniería de SQL Server

- [DBA Stack Exchange](https://dba.stackexchange.com/questions/tagged/sql-server): formule preguntas sobre administración de base de datos
- [Stack Overflow](https://stackoverflow.com/questions/tagged/sql-server): formule preguntas sobre desarrollo
- [Foros de MSDN](https://social.msdn.microsoft.com/Forums/home?category=sqlserver): realice preguntas técnicas
- [Comentarios](https://aka.ms/sqlfeedback): informe de errores y solicitud de características
- [Reddit](https://www.reddit.com/r/SQLServer/): analice SQL Server

## <a name="next-steps"></a>Pasos siguientes

- Consulte la [página de precios](https://azure.microsoft.com/pricing/details/sql-database/), para ver comparativas y calculadoras de los costos tanto de las bases de datos únicas como de los grupos elásticos.
- Consulte estas guías de inicio rápido para comenzar:

  - [Creación de una base de datos SQL en Azure Portal](sql-database-single-database-get-started.md)  
  - [Creación de una base de datos SQL con la CLI de Azure](sql-database-get-started-cli.md)
  - [Creación de una base de datos SQL con PowerShell](sql-database-get-started-powershell.md)

- Para obtener ejemplos de la CLI de Azure y de PowerShell, consulte:
  - [Ejemplos de la CLI de Azure para SQL Database](sql-database-cli-samples.md)
  - [Ejemplos de Azure PowerShell para SQL Database](sql-database-powershell-samples.md)

 - Para obtener información acerca de las nuevas funcionalidades, consulte 
   - **[Azure Roadmap para SQL Database](https://azure.microsoft.com/roadmap/?category=databases)** : el lugar idóneo donde puede encontrar las novedades y lo que vendrá después.
  - **[Blog de Azure SQL Database](https://azure.microsoft.com/blog/topics/database)** : el lugar en el que los miembros del equipo de producto de SQL Server escriben entradas acerca de las noticias y características de SQL Database.

