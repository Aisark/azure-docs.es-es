---
title: LENGTH en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema de SQL LENGTH en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78303722"
---
# <a name="length-azure-cosmos-db"></a>LENGTH (Azure Cosmos DB)
 Devuelve el número de caracteres de la expresión de cadena especificada.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*str_expr*  
   La expresión de cadena que se va a evaluar.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se devuelve la longitud de una cadena.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones de cadena Azure Cosmos DB](sql-query-string-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](introduction.md)
