---
title: Asignación de variables en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para la asignación de variables de T-SQL en Azure SQL Data Warehouse para desarrollar soluciones.
services: sql-data-warehouse
author: XiaoyuL-Preview
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 62c4273a02e02aff268a96e1b13483088ba33f87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65861675"
---
# <a name="assigning-variables-in-azure-sql-data-warehouse"></a>Asignación de variables en Azure SQL Data Warehouse

Sugerencias para la asignación de variables de T-SQL en Azure SQL Data Warehouse para desarrollar soluciones.

## <a name="setting-variables-with-declare"></a>Configuración de variables con DECLARE

Las variables en SQL Data Warehouse se establecen mediante las instrucciones `DECLARE` o `SET`. Inicializar variables con DECLARE es una de las maneras más flexibles de establecer el valor de una variable en SQL Data Warehouse.

```sql
DECLARE @v  int = 0
;
```

También puede utilizar DECLARE para establecer más de una variable a la vez. No se puede usar SELECT ni UPDATE para hacer lo siguiente:

```sql
DECLARE @v  INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Smith')
,       @v1 INT = (SELECT TOP 1 c_customer_sk FROM Customer where c_last_name = 'Jones')
;
```

No se puede inicializar ni utilizar una variable en la misma instrucción DECLARE. Para ilustrar esta cuestión, el ejemplo siguiente **no** está permitido, ya que @p1 se inicializa y se utiliza en la misma instrucción DECLARE. El siguiente ejemplo produce un error.

```sql
DECLARE @p1 int = 0
,       @p2 int = (SELECT COUNT (*) FROM sys.types where is_user_defined = @p1 )
;
```

## <a name="setting-values-with-set"></a>Configuración de valores con SET

SET es un método muy común para configurar una sola variable.

Las instrucciones siguientes son formas válidas de establecer una variable con SET:

```sql
SET     @v = (Select max(database_id) from sys.databases);
SET     @v = 1;
SET     @v = @v+1;
SET     @v +=1;
```

Solo puede establecer una variable al mismo tiempo con SET. Aunque los operadores compuestos están permitidos.

## <a name="limitations"></a>Limitaciones

Puede usar UPDATE para la asignación de variables.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más sugerencias sobre desarrollo, vea la [información general sobre desarrollo](sql-data-warehouse-overview-develop.md).
