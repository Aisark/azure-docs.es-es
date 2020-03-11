---
title: TAN en el lenguaje de consulta de Azure Cosmos DB
description: Obtenga información sobre la función del sistema SQL TAN en Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9d7187ba116067445e835769fc33aa70677ef80b
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301988"
---
# <a name="tan-azure-cosmos-db"></a>TAN (Azure Cosmos DB)
 Devuelve la tangente del ángulo especificado, en radianes, en la expresión especificada.  
  
## <a name="syntax"></a>Sintaxis
  
```sql
TAN (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumentos
  
*numeric_expr*  
   Es una expresión numérica.  
  
## <a name="return-types"></a>Tipos de valores devueltos
  
  Devuelve una expresión numérica.  
  
## <a name="examples"></a>Ejemplos
  
  En el ejemplo siguiente se calcula la tangente de pi()/2.  
  
```sql
SELECT TAN(PI()/2) AS tan 
```  
  
 El conjunto de resultados es el siguiente:  
  
```json
[{"tan": 16331239353195370 }]  
```  

## <a name="remarks"></a>Observaciones

Esta función del sistema no usará el índice.

## <a name="next-steps"></a>Pasos siguientes

- [Funciones matemáticas (Azure Cosmos DB)](sql-query-mathematical-functions.md)
- [Funciones del sistema (Azure Cosmos DB)](sql-query-system-functions.md)
- [Introducción a Azure Cosmos DB](introduction.md)
