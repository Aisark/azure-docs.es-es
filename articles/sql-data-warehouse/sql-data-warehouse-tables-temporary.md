---
title: Tablas temporales en SQL Data Warehouse | Microsoft Docs
description: Directrices esenciales para el uso de tablas temporales y resalta los principios de las tablas temporales de nivel de sesión.
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 23a62e28700ad5fd733040c43ea0eec225fd286f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474902"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tablas temporales en SQL Data Warehouse
Este artículo contiene directrices esenciales para el uso de tablas temporales y resalta los principios de las tablas temporales de nivel de sesión. La información de este artículo puede ayudarle a dividir en secciones el código y así mejorar su reusabilidad y facilidad de mantenimiento.

## <a name="what-are-temporary-tables"></a>¿Qué son las tablas temporales?
Las tablas temporales son útiles al procesar datos, especialmente durante la transformación donde los resultados intermedios son transitorios. Las tablas temporales de SQL Data Warehouse existen en el nivel de sesión.  Solo son visibles para la sesión en la que se crearon y se eliminan automáticamente cuando esa sesión se cierra.  Las tablas temporales ofrecen ventajas para el rendimiento porque sus resultados se escriben en el almacenamiento local en lugar de en el remoto.

## <a name="create-a-temporary-table"></a>Creación de una tabla temporal
Las tablas temporales se crean colocando `#` delante del nombre de la tabla.  Por ejemplo: 

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

También se pueden crear tablas temporales mediante `CTAS` siguiendo exactamente el mismo método:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

> [!NOTE]
> `CTAS` es un comando eficaz y ofrece un uso eficiente del espacio del registro de transacciones. 
> 
> 

## <a name="dropping-temporary-tables"></a>Eliminación de tablas temporales
Cuando se crea una nueva sesión, no debe existir ninguna tabla temporal.  Sin embargo, si llama al mismo procedimiento almacenado, lo que crea un archivo temporal con el mismo nombre, para tener la seguridad de que las instrucciones `CREATE TABLE` se ejecutan correctamente, se puede usar una sencilla comprobación de existencia previa con `DROP`, como en el ejemplo siguiente:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Por coherencia con la codificación, se recomienda usar este patrón tanto para tablas como para tablas temporales.  También es buena idea usar `DROP TABLE` para quitar las tablas temporales cuando ya no las necesite en el código.  En el desarrollo de procedimientos almacenados, es habitual que los comandos de eliminación se empaqueten juntos al final de un procedimiento para garantizar que estos objetos se limpian.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularización de código
Como las tablas temporales se pueden ver en cualquier parte de una sesión de usuario, se puede aprovechar este hecho para ayudarle a dividir en secciones el código de aplicación.  Por ejemplo, el siguiente procedimiento almacenado genera DDL para actualizar todas las estadísticas de la base de datos por nombre de la estadística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

En esta fase, la única acción que se ha producido es la creación de un procedimiento almacenado que genera una tabla temporal, #stats_ddl, con instrucciones de DDL.  Este procedimiento almacenado quita #stats_ddl si ya existe para tener la seguridad de que no dará error si se ejecuta más de una vez dentro de una sesión.  Sin embargo, puesto que no hay ningún elemento `DROP TABLE` al final del procedimiento almacenado, cuando se complete este, saldrá de la tabla creada para que se pueda leer fuera del procedimiento almacenado.  En SQL Data Warehouse, a diferencia de otras bases de datos SQL Server, es posible usar la tabla temporal fuera del procedimiento almacenado que la ha creado.  Las tablas temporales de SQL Data Warehouse se pueden usar **en cualquier parte** dentro de la sesión. Esto puede dar lugar a código más modular y administrable como en el ejemplo siguiente:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitaciones de tablas temporales
SQL Data Warehouse impone algunas limitaciones al implementar las tablas temporales.  Actualmente, solo se admiten tablas temporales con ámbito de sesión.  No se admiten tablas temporales globales.  Además, no se pueden crear vistas en las tablas temporales.  Solo se pueden crear tablas temporales con distribución hash o round robin.  No se admite la distribución de tabla temporal replicada. 

## <a name="next-steps"></a>Pasos siguientes
Para aprender a desarrollar tablas, consulte la [Información general sobre tablas](sql-data-warehouse-tables-overview.md).

