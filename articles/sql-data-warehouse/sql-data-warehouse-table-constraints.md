---
title: Claves principales, externas y únicas
description: Compatibilidad con las restricciones de tabla en Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 8f3102425c6f984df0f50bc05eeb6f9a5e66d3dd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685481"
---
# <a name="primary-key-foreign-key-and-unique-key-in-azure-sql-data-warehouse"></a>Clave principal, clave externa y clave única en Azure SQL Data Warehouse

Conozca las restricciones de tabla de Azure SQL Data Warehouse, incluida la clave principal, la clave externa y la clave única.

## <a name="table-constraints"></a>Restricciones de tabla 
Azure SQL Data Warehouse admite estas restricciones de tabla: 
- PRIMARY KEY solo se admite cuando se usan NONCLUSTERED y NOT ENFORCED.    
- Solo se admite la restricción UNIQUE cuando se usa NOT ENFORCED.   

No se admite la restricción FOREIGN KEY en Azure SQL Data Warehouse.  

## <a name="remarks"></a>Comentarios
Tener una clave principal o una clave única permite al motor de almacenamiento de datos generar un plan de ejecución óptimo para una consulta.  Todos los valores de una columna de clave principal o de una columna de restricción única deben ser únicos. 

Después de crear una tabla con una clave principal o una restricción única en Azure Data Warehouse, los usuarios deben asegurarse de que todos los valores de esas columnas sean únicos.  En caso contrario, la consulta podría devolver resultados inexactos.  En este ejemplo se muestra cómo una consulta puede devolver resultados inexactos si la columna de restricción única o de clave principal incluye valores duplicados.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Ejemplos
Creación de una tabla de almacenamiento de datos con una clave principal: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Creación de una tabla de almacenamiento de datos con una restricción única:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Pasos siguientes

Después de crear las tablas para el almacenamiento de datos, el paso siguiente es cargar datos en la tabla. Para obtener un tutorial de carga, consulte [Carga de datos en Azure SQL Data Warehouse](load-data-wideworldimportersdw.md).
