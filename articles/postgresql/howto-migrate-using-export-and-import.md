---
title: Migración de una base de datos mediante importación y exportación en Azure Database for PostgreSQL con un único servidor
description: Describe cómo extraer una base de datos PostgreSQL en un archivo de script e importar los datos en la base de datos de destino desde ese archivo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 0803f56312ca9b650987c2203c4271cff21df9f8
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260357"
---
# <a name="migrate-your-postgresql-database-using-export-and-import"></a>Migración de una base de datos de PostgreSQL mediante exportación e importación
Puede usar [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) para extraer una base de datos PostgreSQL en un archivo de script y [psql](https://www.postgresql.org/docs/current/static/app-psql.html) para importar los datos en la base de datos de destino desde ese archivo.

## <a name="prerequisites"></a>Requisitos previos
Para seguir esta guía, necesitará:
- Un [servidor de Azure Database for PostgreSQL](quickstart-create-server-database-portal.md) con reglas de firewall para permitir el acceso a las bases de datos que hay en él.
- La utilidad de línea de comandos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) instalada.
- La utilidad de línea de comandos [psql](https://www.postgresql.org/docs/current/static/app-psql.html) instalada.

Siga estos pasos para exportar e importar la base de datos de PostgreSQL.

## <a name="create-a-script-file-using-pg_dump-that-contains-the-data-to-be-loaded"></a>Creación de un archivo de script mediante pg_dump con los datos que se van a cargar
Para exportar la base de datos de PostgreSQL existente en el entorno local o en una máquina virtual a un archivo de script de SQL, ejecute el siguiente comando en el entorno existente:
```bash
pg_dump –-host=<host> --username=<name> --dbname=<database name> --file=<database>.sql
```
Por ejemplo, si tiene un servidor local y una base de datos llamada **testdb** en él:
```bash
pg_dump --host=localhost --username=masterlogin --dbname=testdb --file=testdb.sql
```

## <a name="import-the-data-on-target-azure-database-for-postgresql"></a>Importación de los datos en la instancia de Azure Database for PostgreSQL de destino
Puede usar la utilidad de la línea de comandos psql y el parámetro --dbname (-d) para importar los datos en el servidor de Azure Database for PostrgeSQL y cargar los datos desde el archivo de SQL.
```bash
psql --file=<database>.sql --host=<server name> --port=5432 --username=<user@servername> --dbname=<target database name>
```
En este ejemplo se usa la utilidad psql y el archivo de script **testdb.sql** del paso anterior para importar los datos en la base de datos **mypgsqldb** del servidor de destino **mydemoserver.postgres.database.azure.com**.
```bash
psql --file=testdb.sql --host=mydemoserver.database.windows.net --port=5432 --username=mylogin@mydemoserver --dbname=mypgsqldb
```

## <a name="next-steps"></a>Pasos siguientes
- Para migrar una base de datos de PostgreSQL mediante volcado y restauración, vea [Migración de una base de datos de PostgreSQL mediante volcado y restauración](howto-migrate-using-dump-and-restore.md).
- Para obtener más información sobre cómo migrar bases de datos a Azure Database for PostgreSQL, vea la [Guía de migración de base de datos](https://aka.ms/datamigration). 
