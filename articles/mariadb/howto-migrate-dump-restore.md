---
title: Migración de la base de datos MariaDB mediante el volcado y la restauración en Azure Database for MariaDB
description: En este artículo se explican dos formas habituales de hacer una copia de seguridad de las bases de datos y restaurarlas en Azure Database for MariaDB, con herramientas como mysqldump, MySQL Workbench y PHPMyAdmin.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: bcb76fcbba02bf53b48cc462e3dad8f264db02ed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60745967"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Migración de la base de datos MariaDB a Azure Database for MariaDB mediante el volcado y la restauración
En este artículo se explican dos formas habituales de hacer una copia de seguridad y restaurar bases de datos en Azure Database for MariaDB
- Volcado y restauración desde la línea de comandos (mediante mysqldump) 
- Volcado y restauración mediante PHPMyAdmin

## <a name="before-you-begin"></a>Antes de empezar
Para seguir esta guía de procedimientos, necesita lo siguiente:
- [Creación de un servidor de Azure Database for MariaDB: Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- Utilidad de línea de comandos [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) instalada en la máquina.
- MySQL Workbench [Descargar MySQL Workbench](https://dev.mysql.com/downloads/workbench/), Toad, Navicat u otra herramienta de MySQL de terceros para ejecutar los comandos de volcado y restauración.

## <a name="use-common-tools"></a>Uso de herramientas comunes
Use herramientas y utilidades comunes como MySQL Workbench, mysqldump, Toad o Navicat para conectarse a Azure Database for MariaDB de manera remota y restaurar ahí los datos. Use estas herramientas en el equipo cliente con una conexión a Internet para conectarse a Azure Database for MariaDB. Use una conexión cifrada SSL para aprovechar los procedimientos recomendados de seguridad; vea también la información sobre [conectividad SSL en Azure Database for MariaDB](concepts-ssl-connection-security.md). No es necesario mover los archivos de volcado a ninguna ubicación en la nube especial al migrar a Azure Database for MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Usos habituales de volcado y restauración
Puede emplear utilidades de MySQL, como mysqldump y mysqlpump para realizar un volcado de las bases de datos y cargarlas en un servidor Azure Database for MariaDB en varios escenarios comunes. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Use volcados de base de datos cuando vaya a migrar la base de datos entera. Esta recomendación es adecuada si va a mover una gran cantidad de datos o si quiere reducir la interrupción del servicio en sitios o aplicaciones dinámicos. 
-  Asegúrese de que todas las tablas de la base de datos usen el motor de almacenamiento InnoDB al cargar datos en Azure Database for MariaDB. Azure Database for MariaDB solo admite el motor de almacenamiento InnoDB y, por tanto, no es compatible con otros alternativos. Si las tablas están configuradas con otros motores de almacenamiento, conviértalos al formato del motor InnoDB antes de realizar la migración a Azure Database for MariaDB.
   Por ejemplo, si tiene una aplicación WordPress o WebApp que usa las tablas MyISAM, convierta primero esas tablas; para ello, mígrelas al formato InnoDB antes de restaurarlas en Azure Database for MariaDB. Use la cláusula `ENGINE=InnoDB` para configurar el motor utilizado al crear una nueva tabla y luego transfiera los datos a la tabla compatible antes de la restauración. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Para evitar problemas de compatibilidad, asegúrese de usar la misma versión de MariaDB en los sistemas de origen y de destino al realizar el volcado de las bases de datos. Por ejemplo, si el servidor MariaDB existente tiene la versión 10.2, debe migrar a Azure Database for MariaDB configurado para ejecutar dicha versión. El comando `mysql_upgrade` no funciona en un servidor de Azure Database for MariaDB y no se admite. Si tiene que actualizar entre versiones de MariaDB, primero vuelque o exporte la base de datos con una versión menor a una versión superior de MariaDB en su propio entorno. A continuación, ejecute `mysql_upgrade` antes de intentar la migración a una instancia de Azure Database for MariaDB.

## <a name="performance-considerations"></a>Consideraciones sobre rendimiento
Para optimizar el rendimiento, tenga en cuenta estas consideraciones al volcar grandes bases de datos:
-   Use la opción `exclude-triggers` en mysqldump al volcar las bases de datos. Excluya los desencadenadores de los archivos de volcado para evitar que los comandos de desencadenamiento se disparen durante la restauración de datos. 
-   Use la opción `single-transaction` para establecer el modo de aislamiento de transacción a REPEATABLE READ y enviar una instrucción SQL START TRANSACTION al servidor antes de volcar datos. El volcado de muchas tablas en una única transacción provoca el consumo de almacenamiento adicional durante la restauración. Las opciones `single-transaction` y `lock-tables` son mutuamente excluyentes porque LOCK TABLES hace que las transacciones pendientes se confirmen implícitamente. Para volcar las tablas grandes, combine la opción `single-transaction` con la opción `quick`. 
-   Use la sintaxis de varias filas `extended-insert` que incluye varias listas VALUE. Esto da como resultado un archivo de volcado de memoria más pequeño y acelera las inserciones cuando se vuelve a cargar el archivo.
-  Use la opción `order-by-primary` de mysqldump al volcar las bases de datos, para que el script de los datos se genere en el orden de la clave principal.
-   Use la opción `disable-keys` de mysqldump al volcar los datos para deshabilitar las restricciones de clave externa antes de la carga. El hecho de deshabilitar las comprobaciones de clave externa favorece un aumento del rendimiento. Habilite las restricciones y compruebe los datos después de la carga para garantizar la integridad referencial.
-   Use tablas con particiones cuando sea necesario.
-   Cargue los datos en paralelo. Evite demasiada paralelismo que podría provocar que se alcanzara un límite de recursos, y supervise los recursos mediante las métricas disponibles en Azure Portal. 
-   Use la opción `defer-table-indexes` de mysqlpump al volcar las bases de datos, para que la creación de índices tenga lugar una vez cargados los datos de las tablas.
-   Copie los archivos de copia de seguridad en un blob o almacén de Azure y realice la restauración desde allí, lo que debería ser mucho más rápido que realizar la restauración a través de Internet.

## <a name="create-a-backup-file"></a>Creación de un archivo de copia de seguridad
Para hacer copia de seguridad de una base de datos MariaDB existente en el servidor local en el entorno local o en una máquina virtual, ejecute el siguiente comando con mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

Los parámetros que se proporcionan son los siguientes:
- [uname] El nombre de usuario de base de datos 
- [pass] La contraseña de la base de datos (observe que no hay ningún espacio entre -p y la contraseña) 
- [dbname] El nombre de la base de datos 
- [backupfile.sql] El nombre de archivo para la copia de seguridad de la base de datos 
- [--opt] La opción mysqldump 

Por ejemplo, para hacer una copia de seguridad de una base de datos llamada "testdb" en el servidor MariaDB con el nombre de usuario "testuser" y sin contraseña en archivo untestdb_backup.sql, use el siguiente comando. El comando realiza una copia de la base de datos `testdb` en un archivo denominado `testdb_backup.sql`, que contiene todas las instrucciones SQL necesarias para volver a crear la base de datos. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Si quiere incluir en la copia de seguridad solo algunas de las tablas, ordene los nombres de tabla en una lista separados por espacios y seleccione los que desee. Por ejemplo, para realizar una copia de seguridad solo de las tablas table1 y table2 de "testdb", siga este ejemplo: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Para hacer una copia de seguridad de más de una base de datos a la vez, use el conmutador --database y ordene los nombres de las bases de datos en una lista separados por espacios. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```
Para hacer una copia de seguridad de todas las bases del servidor a la vez, use la opción --all-databases.
```bash
$ mysqldump -u root -p --all-databases > alldb_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Creación de una base de datos en el servidor de destino
Cree una base de datos vacía en el servidor de destino de Azure Database for MariaDB donde se van a migrar los datos. Use una herramienta como MySQL Workbench, Toad o Navicat para crear la base de datos. La base de datos puede tener el mismo nombre que la base de datos que contiene los datos volcados, o puede crear una base de datos con un nombre diferente.

Para conectarse, busque la información de conexión en la página **Introducción** de su instancia de Azure Database for MariaDB.

![Obtención de la información de conexión en Azure Portal](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Agregue la información de conexión a MySQL Workbench.

![Cadena de conexión de MySQL Workbench](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Restauración de la base de datos de MariaDB
Una vez que haya creado la base de datos de destino, puede usar el comando mysql o MySQL Workbench para restaurar los datos en la base de datos específica recién creada desde el archivo de volcado.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
En este ejemplo, restaure los datos en la base de datos recién creada en el servidor de destino de Azure Database for MariaDB.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exportación mediante PHPMyAdmin
Para exportar, puede usar la herramienta común phpMyAdmin que puede haber instalado ya localmente en su entorno. Para exportar la base de datos MariaDB mediante PHPMyAdmin:
1. Abra phpMyAdmin.
2. Seleccione la base de datos. Haga clic en el nombre de la base de datos en la lista de la izquierda. 
3. Haga clic en el vínculo **Exportar**. Aparece una nueva página para ver el volcado de la base de datos.
4. En el área de exportación, haga clic en el vínculo **Select All** (Seleccionar todo) para seleccionar todas las tablas de la base de datos. 
5. En el área de opciones de SQL, haga clic en las opciones adecuadas. 
6. Haga clic en la opción **Save as file** (Guardar como archivo) y en la opción correspondiente de compresión y luego haga clic en el botón **Go** (Ir). Debería aparecer un cuadro de diálogo en el que se le pide que guarde el archivo localmente.

## <a name="import-using-phpmyadmin"></a>Importación mediante PHPMyAdmin
La importación de la base de datos es similar a la exportación. Haga lo siguiente:
1. Abra phpMyAdmin. 
2. En la página de instalación de phpMyAdmin, haga clic en **Add** (Agregar) para agregar el servidor de Azure Database for MariaDB. Proporcione la información de conexión e inicio de sesión.
3. Cree una base de datos con el nombre adecuado y selecciónela en la parte izquierda de la pantalla. Para volver a escribir la base de datos existente, haga clic en el nombre de la base de datos, active todas las casillas situadas al lado de los nombres de tabla y seleccione **Eliminar** para eliminar las tablas existentes. 
4. Haga clic en el vínculo **SQL** para mostrar la página donde puede escribir comandos SQL, o bien cargue su archivo SQL. 
5. Use el botón **Browse** (Examinar) para buscar el archivo de base de datos. 
6. Haga clic en **Go** (Ir) para exportar la copia de seguridad, ejecutar los comandos SQL y volver a crear la base de datos.

## <a name="next-steps"></a>Pasos siguientes
- [Conexión de aplicaciones a Azure Database for MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
