---
title: Ejemplos de script de la CLI de Azure para SQL Database | Microsoft Docs
description: Ejemplos de script de la CLI de Azure para crear y administrar servidores, grupos elásticos, bases de datos y firewalls de Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples, mvc
ms.devlang: azurecli
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 02/03/2019
ms.openlocfilehash: 5ecd5ee4a053d3ebb550b6f2387a0e915b3c2c23
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68569413"
---
# <a name="azure-cli-samples-for-azure-sql-database"></a>Ejemplos de la CLI de Azure para Azure SQL Database

Azure SQL Database se puede configurar con la <a href="/cli/azure">CLI de Azure</a>.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Si decide instalar y usar la CLI localmente, para este tema es preciso que ejecute la CLI de Azure versión 2.0 o posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, consulte [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

## <a name="single-database--elastic-pools"></a>Grupos elásticos y base de datos única

En la tabla siguiente se incluyen vínculos a ejemplos de script de la CLI de Azure para Azure SQL Database.

| |  |
|---|---|
|**Creación de una base de datos única y un grupo elástico**||
| [Creación de una base de datos única y configuración de una regla de firewall](scripts/sql-database-create-and-configure-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este ejemplo de la CLI crea una base de datos de Azure SQL única y configura una regla de firewall en el nivel de servidor. |
| [Creación de grupos elásticos y traslado de bases de datos agrupadas](scripts/sql-database-move-database-between-pools-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este ejemplo de script de la CLI crea grupos elásticos de SQL, traslada las bases de datos agrupadas de Azure SQL Database y cambia los tamaños de proceso.|
|**Escalado de una base de datos única y un grupo elástico**||
| [Escalado de una base de datos única](scripts/sql-database-monitor-and-scale-database-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este ejemplo de script de la CLI escala una sola base de datos de Azure SQL a un tamaño de proceso distinto después de consultar la información del tamaño de la base de datos. |
| [Escalado de un grupo elástico](scripts/sql-database-scale-pool-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Este ejemplo de script de la CLI escala un grupo elástico de SQL a un tamaño de proceso distinto.  |
|**Grupos de conmutación por error**||
| [Incorporación de una base de datos única a un grupo de conmutación por error](scripts/sql-database-add-single-db-to-failover-group-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Este script de la CLI crea una base de datos y un grupo de conmutación por error, agrega la base de datos al grupo de conmutación por error y prueba la conmutación por error en el servidor secundario.|
|||

Obtenga más información sobre la [API de la CLI de Azure de la base de datos única](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases).

## <a name="managed-instance"></a>Instancia administrada

En la tabla siguiente se incluyen vínculos a ejemplos de script de la CLI de Azure para la instancia administrada de Azure SQL Database.

| |  |
|---|---|
| [Creación de una instancia administrada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../create-azure-sql-managed-instance-using-azure-cli/) | Este script de la CLI muestra cómo crear una Instancia administrada. |
| [Actualización una Instancia administrada](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../modify-azure-sql-database-managed-instance-using-azure-cli/) | Este script de la CLI muestra cómo crear una Instancia administrada. |
| [Move a database to another Managed Instance](https://blogs.msdn.microsoft.com/sqlserverstorageengine/20../../cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/) (Traslado de una base de datos a otra Instancia administrada) | Este script de la CLI muestra cómo restaurar una copia de seguridad de una base de datos de una instancia a otra. |
|||

Obtenga más información sobre la [API de la CLI de Azure de la instancia administrada](sql-database-managed-instance-create-manage.md#azure-cli-create-and-manage-managed-instances) y encuentre [más ejemplos aquí](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44).
