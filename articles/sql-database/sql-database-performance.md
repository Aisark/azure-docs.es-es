---
title: Supervisión y mejora del rendimiento
description: Azure SQL Database ofrece herramientas de rendimiento para ayudarle a identificar áreas que pueden mejorar el rendimiento actual de las consultas.
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
ms.date: 01/25/2019
ms.openlocfilehash: f7e5f698a822cd68d8319102e1b8bddbbfa8ad70
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821339"
---
# <a name="monitor-and-improve-performance"></a>Supervisión y mejora del rendimiento

Azure SQL Database identifica posibles problemas en la base de datos y recomienda las acciones que pueden mejorar el rendimiento de la carga de trabajo mediante recomendaciones y acciones de optimización inteligentes.

Para revisar el rendimiento de la base de datos, use el icono **Rendimiento** de la página Información general o desplácese hacia abajo hasta la sección "Soporte técnico y solución de problemas":

   ![Vista Rendimiento](./media/sql-database-performance/entries.png)

En la sección "Soporte técnico y solución de problemas", puede usar las páginas siguientes:


1. [Información general de rendimiento](#performance-overview) para supervisar el rendimiento de la base de datos. 
2. [Recomendaciones de rendimiento](#performance-recommendations) para buscar las recomendaciones de rendimiento que pueden mejorar el rendimiento de la carga de trabajo.
3. [Información de rendimiento de consultas](#query-performance-insight) para buscar las consultas que consumen más recursos.
4. [Ajuste automático](#automatic-tuning) para permitir que Azure SQL Database optimice automáticamente la base de datos.

## <a name="performance-overview"></a>Información general de rendimiento

Esta vista ofrece un resumen del rendimiento de la base de datos y le ayuda con la optimización y solución de problemas de rendimiento. 

![Rendimiento](./media/sql-database-performance/performance.png)

* El icono de **Recomendaciones** proporciona un desglose de recomendaciones de ajuste para la base de datos (se muestran las tres primeras recomendaciones si hay más). Haga clic en este icono para acceder a **[Recomendaciones de rendimiento](#performance-recommendations)** . 
* El icono de **Tuning activity** (Actividad de optimización) proporciona un resumen de las acciones de optimización en curso y finalizadas para la base de datos, lo que le brinda una vista rápida del historial de la actividad de optimización. Si hace clic en este icono irá a la vista completa del historial de optimización de la basa de datos.
* El icono **Ajuste automático** muestra la [configuración de ajuste automático](sql-database-automatic-tuning-enable.md) de la base de datos (las opciones de ajuste que se aplican automáticamente a la base de datos). Al hacer clic en este icono se abre el cuadro de diálogo de configuración de automatización.
* El icono de **Consultas de bases de datos** muestra el resumen del rendimiento de consultas de la base de datos (uso general de DTU y consultas que consumen más recursos). Haga clic en este icono para acceder a **[Información de rendimiento de consultas](#query-performance-insight)** .

## <a name="performance-recommendations"></a>Recomendaciones de rendimiento

En esta página se proporcionan [recomendaciones de ajuste](sql-database-advisor.md) inteligentes que pueden mejorar el rendimiento de la base de datos. Los siguientes tipos de recomendaciones se muestran en esta página:

* Recomendaciones sobre qué índices se deben crear o quitar.
* Recomendaciones cuando se identifican problemas del esquema en la base de datos.
* Recomendaciones en caso de consultas que puedan beneficiarse de consultas parametrizadas.

![Rendimiento](./media/sql-database-performance/recommendations.png)

También puede encontrar el historial completo de las acciones de ajuste que se aplicaron en el pasado.

Obtenga información sobre cómo buscar y aplicar recomendaciones de rendimiento en el artículo [Búsqueda y aplicación de recomendaciones de rendimiento](sql-database-advisor-portal.md).

## <a name="automatic-tuning"></a>Ajuste automático

Las bases de datos de Azure SQL pueden optimizar automáticamente el rendimiento de base de datos mediante la aplicación de [recomendaciones de rendimiento](sql-database-advisor.md). Para obtener más información, lea el [artículo Ajuste automático](sql-database-automatic-tuning.md). Para habilitarlo, lea [cómo habilitar el ajuste automático](sql-database-automatic-tuning-enable.md).

## <a name="query-performance-insight"></a>Información de rendimiento de consultas

[Información de rendimiento de consultas](sql-database-query-performance.md) permite dedicar menos tiempo a la solución de problemas de rendimiento de bases de datos, ya que proporciona:

* Información más detallada sobre el consumo de recursos (DTU) de las bases de datos. 
* Las consultas que más CPU consumen, que potencialmente pueden ajustarse para mejorar el rendimiento. 
* La posibilidad de profundizar en los detalles de una consulta. 

  ![panel de rendimiento](./media/sql-database-query-performance/performance.png)

Obtenga más información sobre esta página en el artículo **[Cómo usar información de rendimiento de consultas](sql-database-query-performance.md)** .

## <a name="additional-resources"></a>Recursos adicionales

* [Guía de rendimiento de Azure SQL Database para bases de datos únicas](sql-database-performance-guidance.md)
* [¿Cuándo se debe utilizar un grupo elástico?](sql-database-elastic-pool-guidance.md)

