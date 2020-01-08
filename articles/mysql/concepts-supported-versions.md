---
title: Versiones admitidas de Azure Database for MySQL
description: Obtenga información sobre las versiones del servidor de MySQL que se admiten en el servicio Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 05d4ecd58f6febff75212f1ad88b60be4f23c2a7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454331"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Versiones admitidas de servidores de Azure Database for MySQL

Azure Database for MySQL se ha desarrollado a partir de [MySQL Community Edition](https://www.mysql.com/products/community/), con el motor InnoDB.

MySQL usa el esquema de nomenclatura X.Y.Z. X es la versión principal, Y es la versión secundaria y Z es la versión de corrección de errores. Para más información sobre el esquema, consulte la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> En el servicio, se usa una puerta de enlace para redirigir las conexiones a las instancias de servidor. Una vez establecida la conexión, el cliente de MySQL muestra la versión de MySQL establecida en la puerta de enlace, no la versión real que se ejecuta en la instancia del servidor MySQL. Para determinar la versión de la instancia del servidor MySQL, use el comando `SELECT VERSION();` en el símbolo del sistema de MySQL.

Azure Database for MySQL actualmente admite las siguientes versiones:

## <a name="mysql-version-56"></a>MySQL versión 5.6

Versión de corrección de errores: 5.6.45

Consulte las [notas de la versión](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) de MySQL para más información sobre mejoras y correcciones de esta versión.

## <a name="mysql-version-57"></a>MySQL versión 5.7

Versión de corrección de errores: 5.7.27

Consulte las [notas de la versión](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) de MySQL para más información sobre mejoras y correcciones de esta versión.

## <a name="mysql-version-80"></a>MySQL versión 8.0

Versión de corrección de errores: 8.0.15

Consulte las [notas de la versión](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) de MySQL para más información sobre mejoras y correcciones de esta versión.

## <a name="managing-updates-and-upgrades"></a>Administración de actualizaciones
El servicio administra automáticamente la aplicación de partes para las actualizaciones de las versiones de corrección de errores. Por ejemplo, 5.7.20 a 5.7.21.  

Actualmente, no se admiten las actualizaciones de versiones principales y secundarias. Por ejemplo, no se admite la actualización de MySQL 5.6 a MySQL 5.7. Si quiere actualizar de la versión 5.6 a la 5.7, realice un [volcado y restáurelo](./concepts-migrate-dump-restore.md) en un servidor que se haya creado con la nueva versión del motor.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre las cuotas y limitaciones aplicables a recursos específicos en función de su **nivel de servicio**, consulte [Niveles de servicio](./concepts-pricing-tiers.md).
