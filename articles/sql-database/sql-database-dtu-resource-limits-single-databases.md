---
title: Límites de recursos de la unidad de procesamiento de base de datos en bases de datos únicas
description: En esta página se describen algunos límites de recursos de DTU comunes para bases de datos únicas en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/20/2019
ms.openlocfilehash: 3fb021c06369d1f37dad83b3f9b27e039ddf8b5e
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811280"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-purchasing-model"></a>Límites de recursos para bases de datos únicas que usan el modelo de compra de DTU

En este artículo se proporcionan los límites de recursos detallados para bases de datos únicas de Azure SQL Database que usan el modelo de compra de DTU.

Para saber más sobre los límites de recursos del modelo de compra de DTU para grupos elásticos, consulte [Límites de recursos de DTU: grupos elásticos](sql-database-dtu-resource-limits-elastic-pools.md). En cuanto a los límites de recursos del núcleo virtual, consulte [Límites de recursos del núcleo virtual: bases de datos únicas](sql-database-vcore-resource-limits-single-databases.md) y [Límites de recursos del núcleo virtual: grupos elásticos](sql-database-vcore-resource-limits-elastic-pools.md). Para obtener más información sobre los diferentes modelos de compra, consulte los [niveles de servicio y modelos de compra](sql-database-purchase-models.md).

## <a name="single-database-storage-sizes-and-compute-sizes"></a>Base de datos única: tamaños de almacenamiento y tamaños de proceso

Las siguientes tablas muestran los recursos disponibles para una base de datos única en cada nivel de servicio y tamaño de proceso. Puede establecer el nivel de servicio, el tamaño de proceso y la cantidad de almacenamiento para una base de datos única mediante [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), la [CLI de Azure](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) o la [API REST](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases).

> [!IMPORTANT]
> Para información y consideraciones sobre el escalado, consulte [Escalado de una base de datos única](sql-database-single-database-scale.md).

### <a name="basic-service-tier"></a>Nivel de servicio Básico

| **Tamaño de proceso** | **Básico** |
| :--- | --: |
| DTU máx. | 5 |
| Almacenamiento incluido (GB) | 2 |
| Opciones de almacenamiento máximo (GB) | 2 |
| Almacenamiento máximo de OLTP en memoria (GB) |N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos | 30 |
| N.º máximo de sesiones simultáneas | 300 |
|||

### <a name="standard-service-tier"></a>Nivel de servicio Estándar

| **Tamaño de proceso** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| DTU máx. | 10 | 20 | 50 | 100 |
| Almacenamiento incluido (GB) | 250 | 250 | 250 | 250 |
| Opciones de almacenamiento máximo (GB) | 250 | 250 | 250 | 250, 500, 750, 1024 |
| Almacenamiento máximo de OLTP en memoria (GB) | N/D | N/D | N/D | N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 60 | 90 | 120 | 200 |
| N.º máximo de sesiones simultáneas |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>Nivel de servicio Estándar (continuación)

| **Tamaño de proceso** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| DTU máx. | 200 | 400 | 800 | 1600 | 3000 |
| Almacenamiento incluido (GB) | 250 | 250 | 250 | 250 | 250 |
| Opciones de almacenamiento máximo (GB) | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| Almacenamiento máximo de OLTP en memoria (GB) | N/D | N/D | N/D | N/D |N/D |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 400 | 800 | 1600 | 3200 |6000 |
| N.º máximo de sesiones simultáneas |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>Nivel de servicio Premium

| **Tamaño de proceso** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| DTU máx. | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| Almacenamiento incluido (GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| Opciones de almacenamiento máximo (GB) | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| Almacenamiento máximo de OLTP en memoria (GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| Cantidad máxima de trabajos (solicitudes) simultáneos| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| N.º máximo de sesiones simultáneas | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* Desde 1024 GB hasta 4096 GB en incrementos de 256 GB

> [!IMPORTANT]
> Existe más de 1 TB de almacenamiento en el nivel Premium actualmente disponible en todas las regiones excepto: Este de China, Norte de China, Centro de Alemania, Nordeste de Alemania, Centro-oeste de EE. UU., US regiones de US DoD y Centro de Gobierno de EE. UU. En estas regiones, el almacenamiento máximo en el nivel Prémium está limitado a 1 TB.  Para más información, consulte las [limitaciones actuales de P11 y P15](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!NOTE]
> Para obtener información sobre los límites de `tempdb`, consulte los [límites de tempdb](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database).

## <a name="next-steps"></a>Pasos siguientes

- Para conocer los límites de recursos de los núcleos virtuales de una sola base de datos, consulte los [límites de recursos para bases de datos únicas con el modelo de compra del núcleo virtual](sql-database-vcore-resource-limits-single-databases.md).
- Para conocer los límites de recursos de núcleos virtuales para grupos elásticos, consulte los [límites de recursos para grupos elásticos con el modelo de compra del núcleo virtual](sql-database-vcore-resource-limits-elastic-pools.md).
- Para conocer los límites de recursos de DTU para grupos elásticos, consulte los [límites de recursos para grupos elásticos que usan el modelo de compra de DTU](sql-database-dtu-resource-limits-elastic-pools.md).
- Para conocer los límites de recursos para instancias administradas, consulte los [límites de recursos para instancias administradas](sql-database-managed-instance-resource-limits.md).
- Para más información sobre los límites generales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-subscription-service-limits.md).
- Para más información sobre los límites de recursos en un servidor de bases de datos, consulte [Límites de recursos en un servidor de SQL Database](sql-database-resource-limits-database-server.md) para obtener información acerca de los límites en los niveles de servidor y suscripción.
