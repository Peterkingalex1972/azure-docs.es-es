---
title: Configuración y acceso a los registros del servidor para PostgreSQL con un único servidor con la CLI de Azure
description: En este artículo se describe cómo configurar los registros de servidor de Azure Database for PostgreSQL con un único servidor, y acceder a ellos, mediante la línea de comandos de la CLI de Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.devlang: azurecli
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4702db31ffeb15481584b9638f5be1aa640ff39e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65067205"
---
# <a name="configure-and-access-server-logs-by-using-azure-cli"></a>Configuración y acceso a los registros del servidor con la CLI de Azure
Puede descargar los registros de error del servidor de PostgreSQL mediante la interfaz de la línea de comandos (CLI de Azure). Sin embargo, no se admite el acceso a los registros de transacciones. 

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- [Un servidor de Azure Database for PostgreSQL](quickstart-create-server-database-azure-cli.md)
- Utilidad de línea de comandos [CLI de Azure](/cli/azure/install-azure-cli) o Azure Cloud Shell en el explorador

## <a name="configure-logging"></a>registro
Puede configurar el servidor para tener acceso a los registros de consulta y los registros de error. Los registros de error pueden contener información de vaciado automático, de conexión y de puntos de comprobación.
1. Active el registro.
2. Para habilitar el registro de consultas, actualice **log\_statement**  y  **log\_min\_duration\_statement**.
3. Actualice el período de retención.

Para obtener más información, consulte cómo [Personalización de los parámetros de configuración del servidor con la CLI de Azure](howto-configure-server-parameters-using-cli.md).

## <a name="list-logs"></a>Enumerar registros
Para mostrar la lista de archivos de registro disponibles para el servidor, ejecute el comando [az postgres server-logs list](/cli/azure/postgres/server-logs).

Puede enumerar archivos de registro del servidor **mydemoserver.postgres.database.azure.com** en el grupo de recursos **myresourcegroup**. A continuación, dirija la lista de archivos de registro a un archivo de texto denominado **log\_files\_list.txt**.
```azurecli-interactive
az postgres server-logs list --resource-group myresourcegroup --server mydemoserver > log_files_list.txt
```
## <a name="download-logs-locally-from-the-server"></a>Descarga de registros del servidor en el sistema local
Con el comando [az postgres server-logs download](/cli/azure/postgres/server-logs), puede descargar archivos de registro individuales para su servidor. 

Use el ejemplo siguiente para descargar el archivo de registro específico para el servidor **mydemoserver.postgres.database.azure.com** en el grupo de recursos **myresourcegroup** a su entorno local.
```azurecli-interactive
az postgres server-logs download --name 20170414-mydemoserver-postgresql.log --resource-group myresourcegroup --server mydemoserver
```
## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información acerca de los registros del servidor, consulte [Registros del servidor en Azure Database for PostgreSQL](concepts-server-logs.md).
- Para obtener más información sobre los parámetros del servidor, consulte cómo [Personalización de los parámetros de configuración del servidor con la CLI de Azure](howto-configure-server-parameters-using-cli.md).
