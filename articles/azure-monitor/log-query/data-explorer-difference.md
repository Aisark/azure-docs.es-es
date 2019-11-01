---
title: Diferencias en el lenguaje de consulta de los registros de Azure Monitor | Microsoft Docs
description: Información de referencia del lenguaje de consulta de Kusto que utiliza Azure Monitor. Incluye elementos adicionales específicos de Azure Monitor y elementos no admitidos en las consultas del registro de Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/13/2019
ms.openlocfilehash: 16a221c0530d65e11589ac272a8e032de0cd3d2b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933101"
---
# <a name="azure-monitor-log-query-language-differences"></a>Diferencias en el lenguaje de consulta de los registros de Azure Monitor

Aunque los [registros de Azure Monitor](log-query-overview.md) se basan en [Azure Data Explorer](/azure/data-explorer) y usa el mismo [lenguaje de consulta de Kusto](/azure/kusto/query), la versión del lenguaje presenta algunas diferencias. En este artículo se identifican los elementos que son diferentes entre la versión del lenguaje que se utiliza en Data Explorer y la versión que se utiliza en las consultas del registro de Azure Monitor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementos de KQL que no se admiten en Azure Monitor
En las siguientes secciones se describen los elementos del lenguaje de consulta de Kusto que no son compatibles con Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instrucciones que no se admiten en Azure Monitor

* [Alias](/azure/kusto/query/aliasstatement)
* [Parámetros de consulta](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funciones que no se admiten en Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current(), current_cursor()](/azure/kusto/query/cursorcurrent)
* [database()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operadores que no se admiten en Azure Monitor

* [Cross-Cluster Join](/azure/kusto/query/joincrosscluster)
* [externaldata operator](/azure/kusto/query/externaldata-operator)

### <a name="plugins-not-supported-in-azure-monitor"></a>Complementos que no se admiten en Azure Monitor

* [Complemento de Python](/azure/kusto/query/pythonplugin)
* [sql_request plugin](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Operadores adicionales de Azure Monitor
Los operadores siguientes admiten características específicas de Azure Monitor que no están disponibles fuera de Azure Monitor.

* [app()](app-expression.md)
* [workspace()](workspace-expression.md)

## <a name="next-steps"></a>Pasos siguientes

- Obtenga referencias a distintos [recursos para escribir consultas de Azure Monitor](query-language.md).
- Acceda a la [documentación de referencia completa del lenguaje de consulta de Kusto](/azure/kusto/query/).
