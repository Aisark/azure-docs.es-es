---
title: 'Inicio rápido: Instancia administrada de Azure SQL Database | Microsoft Docs'
description: Empiece a usar rápidamente Instancia administrada de Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlr
ms.date: 07/11/2019
ms.openlocfilehash: 65b6b503d107b36813d1716348ce5f11fa840cc0
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2019
ms.locfileid: "71937211"
---
# <a name="getting-started-with-azure-sql-database-managed-instance"></a>Introducción a Instancia administrada de Azure SQL Database

La opción de implementación de una [instancia administrada](sql-database-managed-instance-index.yml) crea una base de datos que proporciona casi un 100 % de compatibilidad con el motor de base de datos local más reciente de SQL Server (Enterprise Edition), lo que proporciona una implementación nativa de [red virtual (VNet)](../virtual-network/virtual-networks-overview.md) que permite solucionar problemas de seguridad habituales, y un [modelo de negocio](https://azure.microsoft.com/pricing/details/sql-database/) favorable para los clientes locales de SQL Server. En este artículo aprenderá a crear y configurar rápidamente una instancia administrada y a migrar las bases de datos.

## <a name="quickstart-overview"></a>Introducción al artículo de inicio rápido

Los inicios rápidos siguientes le permiten crear rápidamente una instancia administrada, configurar una máquina virtual o una conexión VPN de punto a sitio para una aplicación cliente y restaurar una base de datos en la nueva instancia administrada mediante un archivo`.bak`.

### <a name="configure-environment"></a>Configuración del entorno

Como primer paso, tendrá que crear su primera instancia administrada con el entorno de red en el que se va a colocar, y habilitar la conexión desde el equipo o máquina virtual en el que se ejecutan las consultas a Instancia administrada. Puede usar las guías a continuación:

- [Cree una instancia administrada mediante Azure Portal](sql-database-managed-instance-get-started.md). En Azure Portal puede configurar los parámetros necesarios (nombre de usuario y contraseña, número de núcleos y cantidad de almacenamiento máxima) y crear automáticamente el entorno de red de Azure sin necesidad de conocer los detalles de red ni los requisitos de infraestructura. Solo debe asegurarse de que tiene un [tipo de suscripción](sql-database-managed-instance-resource-limits.md#supported-subscription-types) con un permiso actual para crear una instancia administrada. Si tiene su propia red que desea utilizar o desea personalizar la red, consulte [Configuración de una red virtual para Instancia administrada de Azure SQL Database](sql-database-managed-instance-configure-vnet-subnet.md) o [Creación de una red virtual para Instancia administrada de Azure SQL Database](sql-database-managed-instance-create-vnet-subnet.md).
- Se crea una instancia administrada en la propia red virtual sin ningún punto de conexión público. Para acceder a la aplicación cliente, puede **crear una máquina virtual en la misma red virtual (en una subred diferente)** o **crear una conexión VPN de punto a sitio a la red virtual desde el equipo cliente** mediante uno de estos inicios rápidos:
  - Habilite el [punto de conexión público](sql-database-managed-instance-public-endpoint-configure.md) en la instancia administrada para tener acceso a los datos directamente desde su entorno.
  - Cree una [máquina virtual de Azure en la red virtual de la instancia administrada](sql-database-managed-instance-configure-vm.md) para obtener conectividad con la aplicación cliente, incluido SQL Server Management Studio.
  - Configure una [conexión VPN de punto a sitio a la instancia administrada](sql-database-managed-instance-configure-p2s.md) desde el equipo cliente en el que tiene SQL Server Management Studio y otras aplicaciones de conectividad de cliente. Estas son las dos opciones de conectividad a la instancia administrada y a su red virtual.

  > [!NOTE]
  > También puede usar ExpressRoute o una conexión de sitio a sitio desde la red local, pero estos métodos están fuera del ámbito de estos inicios rápidos.

Como alternativa a la creación manual de la instancia administrada, puede usar [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md), [PowerShell con una plantilla de Resource Manager](scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md) o la [CLI de Azure](https://docs.microsoft.com/cli/azure/sql/mi#az-sql-mi-create) para crear scripts y automatizar este proceso.

### <a name="migrate-your-databases"></a>Migración de las bases de datos

Después de crear una instancia administrada y configurar el acceso, puede iniciar la migración de las bases de datos desde SQL Server local o las máquinas virtuales de Azure. La migración producirá un error si tiene características no admitidas en la base de datos de origen que desee migrar. Para evitar errores y comprobar la compatibilidad, puede instalar [Data Migration Assistant (DMA)](https://www.microsoft.com/download/details.aspx?id=53595), que analizará las bases de datos de SQL Server y detectará cualquier problema que pudiera bloquear la migración a una instancia administrada, como la existencia de una [secuencia de archivos](https://docs.microsoft.com/sql/relational-databases/blob/filestream-sql-server) o varios archivos de registro. Si resuelve estos problemas, las bases de datos estarán preparadas para la migración a una instancia administrada. El [Asistente para experimentación con bases de datos](https://blogs.msdn.microsoft.com/datamigration/2018/08/06/release-database-experimentation-assistant-dea-v2-6/) es otra herramienta útil que puede registrar la carga de trabajo de SQL Server y reproducirla en una instancia administrada para que pueda determinar si va a haber problemas de rendimiento si se realiza la migración a una instancia administrada.

Una vez que esté seguro de que puede migrar la base de datos a una instancia administrada, puede usar las funcionalidades nativas de restauración de SQL Server para restaurar una base de datos en una instancia administrada a partir de un archivo `.bak`. Puede usar este método para migrar bases de datos desde el motor de base de datos SQL Server instalado localmente o la máquina virtual de Azure. Para ver un inicio rápido, consulte [Restauración de una base de datos en una instancia administrada](sql-database-managed-instance-get-started-restore.md). En este inicio rápido, puede restaurar a partir de un archivo `.bak` almacenado en Azure Blob Storage mediante el comando `RESTORE` de Transact-SQL.

> [!TIP]
> Para usar el comando `BACKUP` de Transact-SQL para crear una copia de seguridad de la base de datos en Azure Blob Storage, consulte [Copia de seguridad en URL de SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url).

Estos inicios rápidos le permiten crear, configurar y restaurar una copia de seguridad de la base de datos en una instancia administrada. En algunos escenarios, necesitaría personalizar o automatizar la implementación de instancias administradas y el entorno de red necesario. Estos escenarios se describen a continuación.

## <a name="customize-network-environment"></a>Personalización del entorno de red

Aunque la red virtual o subred se pueden configurar automáticamente al crearse la instancia mediante [Azure Portal](sql-database-managed-instance-get-started.md), podría ser conveniente crearla antes de iniciar la creación de instancias administradas, ya que podrá configurar los parámetros de la red virtual y de la subred. La manera más fácil de crear y configurar el entorno de red es usar la plantilla de [implementación de recursos de Azure](sql-database-managed-instance-create-vnet-subnet.md), que le ayudará a crear y configurar la red y la subred donde se colocará la instancia. Basta con presionar el botón de implementación de Azure Resource Manager y rellenar el formulario con los parámetros.

Como alternativa, puede usar este [script de PowerShell](https://www.powershellmagazine.com/2018/07/23/configuring-azure-environment-to-set-up-azure-sql-database-managed-instance-preview/) para automatizar la creación de la red.

Si ya tiene una red virtual y una subred donde le gustaría implementar la instancia administrada, deberá asegurarse de que estas satisfagan los [requisitos de red](sql-database-managed-instance-connectivity-architecture.md#network-requirements). Use este [script de PowerShell para verificar que la subred está correctamente configurada](sql-database-managed-instance-configure-vnet-subnet.md). Este script valida la red y notifica cualquier problema, y le indica lo que se debe cambiar y, a continuación, le ofrece realizar los cambios necesarios en la red virtual o la subred. Ejecute este script si no desea configurar manualmente la red virtual o la subred. También puede ejecutarlo después de cualquier reconfiguración importante de la infraestructura de la red. Si quiere crear y configurar su propia red, lea acerca de la [arquitectura de conectividad](sql-database-managed-instance-connectivity-architecture.md) y esta [guía definitiva para crear y configurar un entorno de instancia administrada](https://medium.com/azure-sqldb-managed-instance/the-ultimate-guide-for-creating-and-configuring-azure-sql-managed-instance-environment-91ff58c0be01).

## <a name="migrate-to-a-managed-instance"></a>Migración a una instancia administrada

Estos artículos de inicio rápido le permiten configurar una instancia administrada y mover las bases de datos rápidamente mediante la funcionalidad `RESTORE` nativa. Este es un buen punto de partida si quiere completar pruebas de concepto y comprobar que la solución puede funcionar en la instancia administrada. 

Sin embargo, para migrar la base de datos de producción o incluso las bases de datos de desarrollo y pruebas que quiera usar en alguna prueba de rendimiento, deberá considerar la posibilidad de usar algunas técnicas adicionales, como:
- Pruebas de rendimiento: debe medir el rendimiento de línea de base en la instancia de SQL Server de origen y compararlo con el rendimiento de la instancia administrada de destino en que ha migrado la base de datos. Más información sobre los [procedimientos recomendados de comparación de rendimiento](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).
- Migración en línea: con la funcionalidad `RESTORE` nativa que se describen en este artículo, tendrá que esperar a que las bases de datos se restauren (y se copien en Azure Blob Storage si aún no están almacenadas allí). Esto produce un tiempo de inactividad de la aplicación especialmente en el caso de bases de datos grandes. Para mover la base de datos de producción, use [Data Migration Service (DMS)](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance?toc=/azure/sql-database/toc.json) para migrar la base de datos con el menor tiempo de inactividad posible. Para hacerlo, DMS toma los cambios realizados en la base de datos de origen y los inserta incrementalmente en la base de datos de instancia administrada que se está restaurando. De este modo, puede cambiar rápidamente la aplicación de la base de datos de origen a la de destino con tiempo de inactividad mínimo.

Más información acerca del [proceso de migración recomendado](sql-database-managed-instance-migrate.md).

## <a name="next-steps"></a>Pasos siguientes

- Encuentre una [lista detallada de las características compatibles en Instancia administrada aquí](sql-database-features.md), y [detalles y problemas conocidos aquí](sql-database-managed-instance-transact-sql-information.md).
- Información acerca de las [características técnicas de Instancia administrada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).
- Para conocer procedimientos más avanzados, consulte [Cómo usar una instancia administrada en Azure SQL Database](sql-database-howto-managed-instance.md).
- [Identificación de la SKU de instancia administrada o SQL Database de Azure adecuada para la base de datos local](/sql/dma/dma-sku-recommend-sql-db/).
