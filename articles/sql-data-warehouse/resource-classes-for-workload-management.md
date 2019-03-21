---
title: Clases de recursos para la administración de cargas de trabajo en Azure SQL Data Warehouse | Microsoft Docs
description: Instrucciones para usar las clases de recursos para administrar la simultaneidad y calcular los recursos de las consultas en Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 5b21c16a166a3a264156b7719be6a331e00e6e8e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881374"
---
# <a name="workload-management-with-resource-classes-in-azure-sql-data-warehouse"></a>Administración de carga de trabajo con clases de recursos en Azure SQL Data Warehouse

Instrucciones para usar las clases de recursos para administrar la memoria y la simultaneidad para las consultas en Azure SQL Data Warehouse.  

## <a name="what-is-workload-management"></a>¿Qué es la administración de cargas de trabajo

Administración de cargas de trabajo proporciona la capacidad de optimizar el rendimiento general de todas las consultas. Una carga de trabajo bien ajustado ejecuta consultas y operaciones de carga de forma eficaz, ya sean intensivas de E/S o de proceso intensivo. SQL Data Warehouse le proporciona funcionalidades de administración de cargas de trabajo para entornos de varios usuarios. Tenga en cuenta que el almacenamiento de datos no está diseñado para cargas de trabajo que tengan varios inquilinos.

La capacidad de rendimiento de un almacén de datos viene determinada por las [unidades de almacenamiento de datos](what-is-a-data-warehouse-unit-dwu-cdwu.md).

- Para ver los límites de memoria y simultaneidad para todos los perfiles de rendimiento, consulte [Límites de memoria y simultaneidad](memory-and-concurrency-limits.md).
- Para ajustar la capacidad de rendimiento, puede [escalarla o reducirla verticalmente](quickstart-scale-compute-portal.md).

La capacidad de rendimiento de una consulta viene determinada por la clase de recursos de la consulta. En el resto de este artículo se explica qué son las clases de recursos y cómo ajustarlas.

## <a name="what-are-resource-classes"></a>¿Cuáles son las clases de recursos

La capacidad de rendimiento de una consulta viene determinada por la clase de recursos del usuario.  Las clases de recursos son límites de recursos predeterminados en Azure SQL Data Warehouse que rigen los recursos de proceso y la simultaneidad para la ejecución de las consultas. Las clases de recursos pueden ayudarle a administrar la carga de trabajo mediante el establecimiento de límites en el número de consultas que se ejecutan simultáneamente y en los recursos de proceso asignados a cada consulta.  Hay un equilibrio entre memoria y simultaneidad.

- Las clases de recursos más pequeñas reducen la memoria máxima por cada consulta, pero aumentan la simultaneidad.
- Clases de recursos mayores aumentar la memoria máxima por consulta, pero reducen la simultaneidad.

Existen dos tipos de clases de recursos:

- Las clases de recursos estáticos, que son adecuadas para obtener una mayor simultaneidad en el tamaño de conjunto de datos que se ha fijado.
- Clases de recursos dinámicos, que se adaptan perfectamente para conjuntos de datos que crecen en tamaño y necesita un mayor rendimiento, como el nivel de servicio se escala verticalmente.

Las clases de recursos utilizan espacios de simultaneidad para medir el consumo de recursos.  Los [espacios de simultaneidad](#concurrency-slots) se explican posteriormente en este artículo.

- Para ver el uso de recursos para las clases de recursos, consulte [Límites de memoria y simultaneidad](memory-and-concurrency-limits.md#concurrency-maximums).
- Para ajustar la clase de recurso, puede ejecutar la consulta bajo un usuario diferente o [cambiar la pertenencia de la clase de recurso del usuario actual](#change-a-users-resource-class).

### <a name="static-resource-classes"></a>Clases de recursos estáticos

Las clases de recursos estáticos asignan la misma cantidad de memoria independientemente del nivel de rendimiento actual, que se mide en [unidades de almacenamiento de datos](what-is-a-data-warehouse-unit-dwu-cdwu.md). Puesto que obtienen la misma asignación de memoria independientemente del nivel de rendimiento, el [escalado horizontal del almacenamiento de datos](quickstart-scale-compute-portal.md) permite la ejecución de más consultas dentro de una clase de recursos.  Las clases de recursos estáticos son ideales si se conoce el volumen de datos y este es constante.

Las clases de recursos estáticos se implementan con estos roles de base de datos predefinidos:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Clases de recursos dinámicos

Las clases de recursos dinámicos asignan una cantidad variable de memoria en función del nivel de servicio actual. Si bien las clases de recursos estáticos son beneficiosas para una mayor simultaneidad y volúmenes de datos estáticos, las clases de recursos dinámicos son más adecuadas para trabajar con una cantidad creciente o variable de datos.  Esto significa que, al escalar a un nivel de servicio mayor, las consultas obtienen más memoria automáticamente.  

Las clases de recursos dinámicos se implementan con estos roles de base de datos predefinidos:

- smallrc
- mediumrc
- largerc
- xlargerc

### <a name="gen2-dynamic-resource-classes-are-truly-dynamic"></a>Las clases de recursos dinámicos Gen2 son realmente dinámicas

Al profundizar en los detalles de las clases de recursos dinámicos en Gen1, hay algunos detalles que agregan complejidad adicional a la hora de comprender su comportamiento:

- La clase de recursos smallrc funciona con un modelo de memoria fija, parecido a una clase de recurso estático.  Las consultas Smallrc no obtienen dinámicamente más memoria a medida que aumenta el nivel de servicio.
- A medida que los niveles de servicio cambian, la simultaneidad de la consulta disponible puede subir o bajar.
- Los niveles de servicios de escala no proporcionan un cambio proporcional en la memoria asignada a las mismas clases de recursos.

Solo en **Gen2**, las clases de recursos dinámicos son verdaderamente dinámicas, ya que abordan los puntos mencionados anteriormente.  La nueva regla es 3-10-22-70 para las asignaciones de porcentaje de memoria de clases de recursos pequeñas, medianas, grandes y extra grandes, **independientemente del nivel de servicio**.  En la siguiente tabla encontrará los detalles consolidados de los porcentajes de asignación de memoria y el número mínimo de consultas simultáneas que se ejecutan, independientemente del nivel de servicio.

| Clase de recursos | Porcentaje de memoria | Mínimo de consultas simultáneas |
|:--------------:|:-----------------:|:----------------------:|
| smallrc        | 3 %                | 32                     |
| mediumrc       | 10%               | 10                     |
| largerc        | 22 %               | 4                      |
| xlargerc       | 70%               | 1                      |

### <a name="default-resource-class"></a>Clase de recursos predeterminada

De forma predeterminada, cada usuario es miembro de la clase de recursos dinámicos **smallrc**.

La clase de recursos del administrador de servicios está fijada en smallrc y no se puede cambiar.  El administrador de servicios es el usuario que se creó durante el proceso de aprovisionamiento.  En este contexto, el administrador de servicios es el inicio de sesión especificado como inicio de sesión del administrador de servidores al crear una nueva instancia de SQL Data Warehouse con un nuevo servidor.

> [!NOTE]
> Los usuarios o grupos definidos como administrador de Active Directory también son administradores de servicio.
>
>

## <a name="resource-class-operations"></a>Operaciones de clases de recursos

Las clases de recursos están diseñadas para mejorar el rendimiento de las actividades de administración y manipulación de datos. Las consultas más complejas también pueden beneficiarse de la ejecución en una clase de recursos más grande. Por ejemplo, el rendimiento de las consultas para criterios de combinación y ordenación de gran tamaño puede mejorar cuando la clase de recursos es lo suficientemente grande como para permitir que la consulta se ejecute en la memoria.

### <a name="operations-governed-by-resource-classes"></a>Operaciones regidas por clases de recursos

Estas operaciones están regidas por clases de recursos:

- INSERT-SELECT, UPDATE, DELETE
- SELECT (al consultar las tablas de usuario)
- ALTER INDEX - REBUILD o REORGANIZE
- ALTER TABLE REBUILD
- CREATE INDEX
- CREATE CLUSTERED COLUMNSTORE INDEX
- CREATE TABLE AS SELECT (CTAS)
- Carga de datos
- Operaciones de movimiento de datos llevadas a cabo por el servicio de movimiento de datos (DMS)

> [!NOTE]  
> Ninguno de los límites de simultaneidad se encarga de regular las instrucciones SELECT de las vistas de administración dinámicas (DMV) o de otras vistas del sistema. Puede supervisar el sistema independientemente del número de consultas que se ejecutan en él.

### <a name="operations-not-governed-by-resource-classes"></a>Operaciones no regidas por clases de recursos

Algunas consultas siempre se ejecutan en la clase de recursos smallrc incluso si el usuario es miembro de una clase de recursos más grande. Estas consultas exentas no se cuentan en el límite de simultaneidad. Por ejemplo, si el límite de simultaneidad es de 16, muchos usuarios pueden realizar selecciones desde las vistas del sistema, sin que esto afecte a las ranuras de simultaneidad disponibles.

Las instrucciones siguientes están exentas de las clases de recursos y siempre se ejecutan en smallrc:

-CREAR o quitar tabla - ALTER TABLE... SWITCH, SPLIT o MERGE PARTITION-ALTER INDEX DISABLE - DROP INDEX-crear, actualizar o DROP STATISTICS-TRUNCATE TABLE-ALTER AUTHORIZATION-CREATE LOGIN-crear, ALTER o DROP USER-crear, ALTER o DROP PROCEDURE-crear o quitar vista - insertar valores - seleccione desde las vistas del sistema y DMV-EXPLICAN - DBCC

<!--
Removed as these two are not confirmed / supported under SQL DW
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Espacios de simultaneidad

Los espacios de simultaneidad son una manera cómoda de realizar un seguimiento de los recursos disponibles para la ejecución de la consulta. Son como las entradas que compra para reservar un asiento en un concierto porque estos son limitados. El número total de espacios de simultaneidad por almacenamiento de datos viene determinado por el nivel de servicio. Para que una consulta se pueda ejecutar, debe poder reservar suficientes espacios de simultaneidad. Cuando una consulta finaliza, libera sus espacios de simultaneidad.  

- Una consulta que se ejecute con 10 espacios de simultaneidad puede tener acceso a 5 veces más recursos de proceso que una consulta que se ejecute con 2 espacios de simultaneidad.
- Si cada consulta requiere 10 espacios de simultaneidad y hay 40, solo se pueden ejecutar 4 consultas simultáneamente.

Solo las consultas regidas por recursos consumen espacios de simultaneidad. Las consultas del sistema y algunas consultas triviales no consumen ningún espacio. El número exacto de espacios de simultaneidad consumidos viene determinada por la clase de recursos de la consulta.

## <a name="view-the-resource-classes"></a>Visualización de las clases de recursos

Las clases de recursos se implementan como roles de base de datos predefinidos. Existen dos tipos de clases de recursos: estáticas y dinámicas. Para ver una lista de las clases de recursos, utilice la siguiente consulta:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Cambio de una clase de recursos de usuario

Para implementar clases de recursos, debe asignar usuarios a los roles de base de datos. Cuando un usuario ejecuta una consulta, la consulta se ejecuta con la clase de recurso del usuario. Por ejemplo, si un usuario es miembro del rol de base de datos staticrc10, las consultas se ejecutan con pequeñas cantidades de memoria. Si un usuario de base de datos es un miembro de los roles de base de datos "xlargerc" o staticrc80, las consultas se ejecutan con grandes cantidades de memoria.

Para aumentar la clase de recursos de un usuario, use [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) para agregar el usuario a un rol de base de datos de una clase de recursos grande.  El siguiente código agrega un usuario al rol de base de datos largerc.  Cada solicitud obtiene 22% de la memoria del sistema.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Para reducir la clase de recursos, use [sp_droprolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql).  Si 'loaduser' no es un miembro o cualquier otra clase de recurso, entran en la clase de recurso smallrc predeterminada con una concesión de memoria de % 3.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Prioridad de la clase de recursos

Los usuarios pueden ser miembros de varias clases de recursos. Cuando un usuario pertenece a más de una clase de recursos:

- Las clases de recursos dinámicas tienen prioridad sobre las clases de recursos estáticas. Por ejemplo, si un usuario es miembro de mediumrc (dinámico) y staticrc80 (estático), las consultas se ejecutarán con mediumrc.
- Las clases de recursos más grandes tienen prioridad sobre las clases de recursos más pequeñas. Por ejemplo, si un usuario es miembro de mediumrc y largerc, las consultas se ejecutarán con largerc. Del mismo modo, si un usuario es miembro de staticrc20 y statirc80, las consultas se ejecutarán con las asignaciones de recursos de staticrc80.

## <a name="recommendations"></a>Recomendaciones

Se recomienda la creación de un usuario que se dedica a ejecutar un tipo específico de la consulta u operación de carga. Conceda a ese usuario de una clase de recursos permanente en lugar de cambiar la clase de recursos con frecuencia. Clases de recursos estáticos permiten general un mayor control sobre la carga de trabajo, por lo que se recomienda usar clases de recursos estáticos antes de considerar las clases de recursos dinámicos.

### <a name="resource-classes-for-load-users"></a>Clases de recursos para los usuarios de carga

`CREATE TABLE` usa índices de almacén de columnas en clúster de manera predeterminada. Comprimir datos en un índice de almacén de columnas es una operación que utiliza mucha memoria y, debido a ello, la presión en la memoria puede reducir la calidad del índice. Presión de memoria puede conducir a la necesidad de una clase de recurso superior al cargar los datos. Para asegurarse de que las cargas tienen suficiente memoria, puede crear un usuario designado para ejecutar cargas y asignarlo a una clase de recursos más alta.

La memoria necesaria para procesar las cargas eficazmente depende de la naturaleza de la tabla cargada y del tamaño de los datos. Para obtener más información sobre los requisitos de memoria, consulte [Maximizing rowgroup quality (Maximizar la calidad de un grupo de filas)](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

Una vez que haya determinado los requisitos de la memoria, elija si quiere asignar el usuario de carga a una clase de recursos estática o dinámica.

- Use una clase de recursos estática cuando los requisitos de memoria de la tabla se encuentren dentro de un intervalo específico. Las cargas se deben ejecutar con la memoria adecuada. Asimismo, al escalar el almacenamiento de datos, las cargas no necesitan más memoria. Mediante el uso de una clase de recursos estática, las asignaciones de memoria permanecen constantes. Esta coherencia ahorra memoria y permite que se ejecuten más consultas simultáneamente. Le recomendamos que use las clases de recursos estáticas con las nuevas soluciones, ya que estas proporcionan un mayor control.
- Puede usar una clase de recursos dinámica si los requisitos de memoria de tabla varían considerablemente. Es posible que las cargas necesiten más memoria que la que proporciona el nivel DWU o cDWU actual. Escalar el almacenamiento de datos agrega más memoria a las operaciones de carga, lo que permite la carga realizar más rápido.

### <a name="resource-classes-for-queries"></a>Clases de recursos para consultas

Algunas consultas son procesos intensivos y otros no.  

- Seleccione una clase de recurso dinámico cuando las consultas sean complejas, pero no es necesario alta simultaneidad.  Por ejemplo, el proceso de generar informes diarios o semanales es una necesidad ocasional de recursos. Si los informes procesan grandes cantidades de datos, al escalar el almacenamiento de datos se proporcionará más memoria a la clase de recursos existente del usuario.
- Seleccione una clase de recursos estática cuando varíen las expectativas de recursos a lo largo del día. Por ejemplo, una clase de recursos estática funciona bien cuando varios usuarios consultan el almacenamiento de datos. Al escalar el almacenamiento de datos, no cambia la cantidad de memoria asignada al usuario. Por lo tanto, se pueden ejecutar más consultas en paralelo en el sistema.

Concesiones de memoria adecuada dependen de muchos factores, como la cantidad de datos consultados, la naturaleza de los esquemas de tabla y seleccione varias combinaciones y predicados de grupo. Desde un punto de vista general, si asigna más memoria permitirá que las consultas se completen más rápido, pero podría reducir la simultaneidad global. Si la simultaneidad no es un problema, asignar una cantidad de memoria mayor de la necesaria no resulta perjudicial para el rendimiento.

Para optimizar el rendimiento, utilice las diferentes clases de recursos. En la siguiente sección se proporciona un procedimiento almacenado que le ayudará a averiguar cuál es la mejor clase de recursos.

## <a name="example-code-for-finding-the-best-resource-class"></a>Código de ejemplo para encontrar la mejor clase de recursos

Puede usar el siguiente procedimiento almacenado especificado para [Gen1](#stored-procedure-definition-for-gen1) o [Gen2](#stored-procedure-definition-for-gen2)-averiguar simultaneidad y concesión de memoria por clase de recurso en un SLO determinado y la mejor clase de recursos de memoria intensiva CCI operaciones en la tabla CCI sin particiones en una clase de recurso determinado:

Este es el propósito de este procedimiento almacenado:

1. Para ver la simultaneidad y la concesión de memoria por clase de recursos en un SLO determinado. El usuario tiene que proporcionar un valor NULL tanto para el esquema como para el nombre de tabla, tal como se muestra en este ejemplo.  
2. Para ver la mejor clase de recursos para las operaciones de CCI gran cantidad de memoria (carga, tabla de copia, regeneración de índice, etc.) en la tabla CCI sin particiones en una clase de recurso determinado. En este caso, el procedimiento almacenado usa el esquema de tabla para averiguar la concesión de memoria necesaria.

### <a name="dependencies--restrictions"></a>Dependencias y restricciones

- Este procedimiento almacenado no está diseñado para calcular los requisitos de memoria para una tabla cci con particiones.
- Este procedimiento almacenado no tiene requisitos de memoria en cuenta la parte SELECT de CTAS/INSERT-SELECT y se considera que es una instrucción SELECT.
- Este procedimiento almacenado utiliza una tabla temporal que está disponible en la sesión donde el procedimiento almacenado se creó.
- Este procedimiento almacenado depende de las ofertas actuales (por ejemplo, configuración de hardware, configuración DMS) y, si cualquiera de los cambia, a continuación, este procedimiento almacenado no funcionará correctamente.  
- Este procedimiento almacenado depende de las ofertas existentes de límite de simultaneidad y si estos cambian, a continuación, este procedimiento almacenado no funcionará correctamente.  
- Este procedimiento almacenado depende de las ofertas de clase de recursos existente y si estos cambian, a continuación, este procedimiento almacenado no funcionará correctamente.  

>[!NOTE]  
>Si no se obtienen resultados después de ejecutar el procedimiento almacenado con los parámetros proporcionados, podrían darse dos circunstancias.
>
>1. Que algún parámetro del almacenamiento de datos contenga un valor de SLO no válido.
>2. O bien, que no haya ninguna clase de recursos coincidente de la operación CCI en la tabla.
>
>Por ejemplo, en DW100, la concesión de memoria máxima disponible es de 400 MB y el esquema de tabla es lo suficientemente ancho como para superar el requisito de 400 MB.

### <a name="usage-example"></a>Ejemplo de uso

Sintaxis:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: proporcione un parámetro NULL para extraer la unidad de almacenamiento de datos actual de la base de datos de almacenamiento de datos o proporcione una unidad admitida con el formato 'DW100'
2. @SCHEMA_NAME: proporcione un nombre de esquema de la tabla
3. @TABLE_NAME: proporcione un nombre de tabla del interés

Ejemplos de ejecución de este procedimiento almacenado:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

La siguiente instrucción crea la tabla "Table1" que se usa en los ejemplos anteriores.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition-for-gen1"></a>Definición del procedimiento almacenado para Gen1

```sql  
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT -FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS
IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
SET @DWU = (
  SELECT 'DW'+CAST(COUNT(*)*100 AS VARCHAR(10))
  FROM sys.dm_pdw_nodes
  WHERE type = 'COMPUTE')
END

DECLARE @DWU_NUM INT
SET @DWU_NUM = CAST (SUBSTRING(@DWU, 3, LEN(@DWU)-2) AS INT)

-- Raise error if either schema name or table name is supplied but not both them supplied
--IF ((@SCHEMA_NAME IS NOT NULL AND @TABLE_NAME IS NULL) OR (@TABLE_NAME IS NULL AND @SCHEMA_NAME IS NOT NULL))
--     RAISEERROR('User need to supply either both Schema Name and Table Name or none of them')

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END

-- Creating ref. temp table (CTAS) to hold mapping info.
-- CREATE TABLE #ref
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT 'SloDWGroupC00' AS wg_name,1 AS slots_used,100 AS tgt_mem_grant_MB
  UNION ALL
    SELECT 'SloDWGroupC01',2,200
  UNION ALL
    SELECT 'SloDWGroupC02',4,400
  UNION ALL
    SELECT 'SloDWGroupC03',8,800
  UNION ALL
    SELECT 'SloDWGroupC04',16,1600
  UNION ALL
    SELECT 'SloDWGroupC05',32,3200
  UNION ALL
    SELECT 'SloDWGroupC06',64,6400
  UNION ALL
    SELECT 'SloDWGroupC07',128,12800
)
-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.tgt_mem_grant_MB AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.tgt_mem_grant_MB AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.tgt_mem_grant_MB AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.tgt_mem_grant_MB AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.tgt_mem_grant_MB AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used
-- WHERE   a1.DWU = @DWU
  WHERE   a1.DWU = UPPER(@DWU)
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;
-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
                     WHEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000)) > 0 THEN FLOOR(8 -(CAST (@DWU_NUM AS FLOAT)/6000))
                     ELSE 1
              END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size, load_multiplier, #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

### <a name="stored-procedure-definition-for-gen2"></a>Definición del procedimiento almacenado para Gen2

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(7),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.
  SELECT @DWU = 'DW'+CAST(Nodes*CASE WHEN CPUVer>6 THEN 500 ELSE 100 END AS VARCHAR(10))+CASE WHEN CPUVer>6 THEN 'c' ELSE '' END
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), CPUVer=max(i.cpu_count)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE'
         )A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
  SELECT 'DW100' AS DWU, 4 AS max_queries, 4 AS max_slots, 1 AS slots_used_smallrc, 1 AS slots_used_mediumrc,
        2 AS slots_used_largerc, 4 AS slots_used_xlargerc, 1 AS slots_used_staticrc10, 2 AS slots_used_staticrc20,
        4 AS slots_used_staticrc30, 4 AS slots_used_staticrc40, 4 AS slots_used_staticrc50,
        4 AS slots_used_staticrc60, 4 AS slots_used_staticrc70, 4 AS slots_used_staticrc80
  UNION ALL
    SELECT 'DW200', 8, 8, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW300', 12, 12, 1, 2, 4, 8, 1, 2, 4, 8, 8, 8, 8, 8
  UNION ALL
    SELECT 'DW400', 16, 16, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW500', 20, 20, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW600', 24, 24, 1, 4, 8, 16, 1, 2, 4, 8, 16, 16, 16, 16
  UNION ALL
    SELECT 'DW1000', 32, 40, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1200', 32, 48, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500', 32, 60, 1, 8, 16, 32, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000', 32, 80, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000', 32, 120, 1, 16, 32, 64, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW6000', 32, 240, 1, 32, 64, 128, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW1000c', 32, 40, 1, 4, 8, 28, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW1500c', 32, 60, 1, 6, 13, 42, 1, 2, 4, 8, 16, 32, 32, 32
  UNION ALL
    SELECT 'DW2000c', 48, 80, 2, 8, 17, 56, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW2500c', 48, 100, 3, 10, 22, 70, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW3000c', 64, 120, 3, 12, 26, 84, 1, 2, 4, 8, 16, 32, 64, 64
  UNION ALL
    SELECT 'DW5000c', 64, 200, 6, 20, 44, 140, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW6000c', 128, 240, 7, 24, 52, 168, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW7500c', 128, 300, 9, 30, 66, 210, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW10000c', 128, 400, 12, 40, 88, 280, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW15000c', 128, 600, 18, 60, 132, 420, 1, 2, 4, 8, 16, 32, 64, 128
  UNION ALL
    SELECT 'DW30000c', 128, 1200, 36, 120, 264, 840, 1, 2, 4, 8, 16, 32, 64, 128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * CASE WHEN RIGHT(@DWU,1)='c' THEN 250 ELSE 200 END AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-step"></a>Paso siguiente

Para obtener más información sobre cómo administrar los usuarios y la seguridad de la base de datos, consulte [Proteger una base de datos en SQL Data Warehouse][Secure a database in SQL Data Warehouse]. Para obtener más información sobre la manera en que las clases de recursos más grandes pueden mejorar la calidad de los índices de almacén de columnas en clúster, consulte [Memory optimizations for columnstore compression (Optimizaciones de memoria para la compresión del almacén de columnas)](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

<!--Image references-->

<!--Article references-->
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md
[Rebuilding indexes to improve segment quality]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Secure a database in SQL Data Warehouse]: ./sql-data-warehouse-overview-manage-security.md

<!--MSDN references-->
[Managing Databases and Logins in Azure SQL Database]:https://msdn.microsoft.com/library/azure/ee336235.aspx

<!--Other Web references-->
