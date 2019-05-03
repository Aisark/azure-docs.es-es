---
title: Funcionamiento del Almacén de consultas de Azure SQL Database
description: Aprenda a utilizar el Almacén de consultas de Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60584750"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Funcionamiento del almacén de consultas de Azure SQL Database

El Almacén de consultas en Azure es una característica de base de datos completamente administrada que recopila y presenta constantemente información histórica detallada sobre todas las consultas. Es parecido a la caja negra de un avión y simplifica considerablemente la solución de problemas de rendimiento de las consultas tanto para los clientes locales como de nube. En este artículo se explican los aspectos específicos del funcionamiento del Almacén de consultas de Azure. Con estos datos de consulta previamente recopilados, puede diagnosticar y resolver rápidamente los problemas de rendimiento y así dedicar más tiempo a centrarse en sus negocios. 

El Almacén de consultas está [disponible globalmente](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) en Azure SQL Database desde noviembre de 2015. Es la base de las características de análisis y ajuste del rendimiento, como el [Asesor de SQL Database y el Panel de rendimiento](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). En el momento de publicar este artículo, el Almacén de consultas se ejecuta en más de 200 000 bases de datos de usuario de Azure y recopila información relativa a las consultas durante varios meses, sin interrupción.

> [!IMPORTANT]
> Microsoft está en proceso de activar el Almacén de consultas para todas las bases de datos de Azure SQL (nuevas y existentes). 

## <a name="optimal-query-store-configuration"></a>Configuración óptima del Almacén de consultas

En esta sección se describen los valores predeterminados de una configuración óptima que están diseñados para garantizar el funcionamiento confiable del Almacén de consultas y de las características que de él dependen, como el [Asesor de SQL Database y el Panel de rendimiento](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). La configuración predeterminada está optimizada para una recopilación continua de los datos, es decir, un tiempo mínimo en los estados OFF y READ_ONLY.

| Configuración | DESCRIPCIÓN | Valor predeterminado | Comentario |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Especifica el límite del espacio de datos que puede tomar el Almacén de consultas dentro de la base de datos de cliente. |100 |Se aplica a nuevas bases de datos. |
| INTERVAL_LENGTH_MINUTES |Define el tamaño de la ventana de tiempo durante la que se agregan y conservan las estadísticas recopiladas en tiempo de ejecución para los planes de consulta. Todos los planes de consulta activa tienen al menos una fila durante un período de tiempo definido con esta configuración. |60 |Se aplica a nuevas bases de datos. |
| STALE_QUERY_THRESHOLD_DAYS |Directiva de limpieza basada en el tiempo que controla el período de retención de las estadísticas en tiempo de ejecución guardadas y las consultas inactivas. |30 |Se aplica a nuevas bases de datos y bases de datos con la configuración predeterminada anterior (367). |
| SIZE_BASED_CLEANUP_MODE |Especifica si limpieza automática de los datos se lleva a cabo cuando el tamaño de los datos del Almacén de consultas se aproxima al límite. |AUTO |Se aplica a todas las bases de datos. |
| QUERY_CAPTURE_MODE |Especifica si se realiza el seguimiento de todas las consultas o solo de un subconjunto de estas. |AUTO |Se aplica a todas las bases de datos. |
| FLUSH_INTERVAL_SECONDS |Especifica el período máximo durante el que las estadísticas en tiempo de ejecución capturadas se conservan en memoria, antes de vaciarlas en el disco. |900 |Se aplica a nuevas bases de datos. |
|  | | | |

> [!IMPORTANT]
> Estos valores predeterminados se aplican automáticamente en la fase final de la activación del Almacén de consultas en todas las bases de datos de Azure SQL (consulte la nota importante anterior). Después de esta optimización, Azure SQL Database no cambiará los valores de configuración establecidos por los clientes, a no ser que influyan negativamente en la carga de trabajo principal o en las operaciones confiables del Almacén de consultas.

Si quiere permanecer con su configuración personalizada, utilice [ALTER DATABASE con las opciones del Almacén de consultas](https://msdn.microsoft.com/library/bb522682.aspx) para revertir la configuración al estado anterior. Consulte [Best Practices with the Query Store](https://msdn.microsoft.com/library/mt604821.aspx) (Procedimientos recomendados con el Almacén de consultas) para aprender a elegir los parámetros de configuración óptima.

## <a name="next-steps"></a>Pasos siguientes

[SQL Database Performance Insight (Información de rendimiento de Base de datos SQL)](sql-database-performance.md)

## <a name="additional-resources"></a>Recursos adicionales

Para más información, consulte los siguientes artículos:

- [A flight data recorder for your database (Una caja negra para la base de datos)](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Supervisar el rendimiento mediante el Almacén de consultas](https://msdn.microsoft.com/library/dn817826.aspx)
- [Query Store Usage Scenarios (Escenarios de uso del Almacén de consultas)](https://msdn.microsoft.com/library/mt614796.aspx)
