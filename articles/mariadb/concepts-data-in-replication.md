---
title: Replicar datos en Azure Database for MariaDB.
description: En este artículo se describe la replicación de datos internos para Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 3ceb8b4f3c2c50ac0ac3bd12831b5497f9a05afb
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993043"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replicar datos en Azure Database for MariaDB

La Replicación de datos de entrada permite sincronizar los datos de un servidor de MariaDB que se ejecuta de forma local, en máquinas virtuales o servicios de base de datos hospedados por otros proveedores de nube en el servicio de Azure Database for MariaDB. La Replicación de datos de entrada se basa en la replicación según la posición del archivo de registro binario (binlog), que es nativa de MariaDB. Para obtener más información sobre la replicación de binlog, consulte la [introducción a la replicación de binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Cuándo utilizar Data-in Replication
Los escenarios principales para considerar el uso de Data-in Replication son:

- **Sincronización de datos híbridos:** con Replicación de datos de entrada, se pueden mantener los datos sincronizados entre los servidores locales y Azure Database for MariaDB. Esta sincronización resulta útil para crear aplicaciones híbridas. Este método resulta atractivo cuando se tiene un servidor de base de datos local existente, pero quiere mover los datos a una región más cercana de los usuarios finales.
- **Sincronización de multi-nube:** para soluciones de nube complejas, use Replicación de datos de entrada para sincronizar datos entre Azure Database for MariaDB y distintos proveedores de nube, incluidas las máquinas virtuales y los servicios de base de datos hospedados en dichas nubes.

## <a name="limitations-and-considerations"></a>Limitaciones y consideraciones

### <a name="data-not-replicated"></a>Datos no replicados
La [*base de datos del sistema mysql* ](https://mariadb.com/kb/en/library/the-mysql-database-tables/) del servidor maestro no se replica. No se replican los cambios en las cuentas y permisos en el servidor maestro. Si crea una cuenta en el servidor maestro y esta cuenta debe tener acceso al servidor de réplica, cree manualmente la misma cuenta en el servidor de réplica. Para reconocer qué tablas se encuentran en la base de datos del sistema, vea la [documentación de MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Requisitos
- La versión del servidor maestro debe ser al menos la versión 10.2 de MariaDB.
- Las versiones del servidor maestro y de réplica deben ser las mismas. Por ejemplo, ambas deben ser la versión 10.2 de MariaDB.
- Cada tabla debe tener una clave principal.
- El servidor maestro debe usar el motor InnoDB.
- El usuario debe tener permisos para configurar el registro binario y crear nuevos usuarios en el servidor maestro.
- Si el servidor maestro tiene SSL habilitado, asegúrese de que el certificado de entidad de certificación de SSL proporcionado para el dominio se haya incluido en el procedimiento almacenado `mariadb.az_replication_change_master`. Consulte los [ejemplos](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) siguientes y el parámetro `master_ssl_ca`.
- Asegúrese de que la dirección IP del servidor maestro se ha agregado a las reglas de firewall del servidor de réplica de Azure Database for MariaDB. Actualice las reglas de firewall mediante [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) o la [CLI de Azure](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli).
- Asegúrese de que el equipo que hospeda el servidor maestro permite el tráfico entrante y saliente en el puerto 3306.
- Asegúrese de que el servidor maestro tiene una **dirección IP pública**, de que el DNS es accesible públicamente o de que tiene un nombre de dominio completo (FQDN).

### <a name="other"></a>Otros
- La Replicación de datos de entrada solo se admite en los planes de tarifa De uso general y Optimizada para memoria.

## <a name="next-steps"></a>Pasos siguientes
- Información sobre cómo [configurar la replicación de datos internos](howto-data-in-replication.md).
