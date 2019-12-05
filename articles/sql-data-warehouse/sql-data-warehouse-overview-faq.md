---
title: Preguntas frecuentes sobre Azure Synapse Analytics (anteriormente SQL DW)
description: En este artículo se muestran las preguntas más frecuentes sobre Azure Synapse Analytics (anteriormente SQL DW) para clientes y desarrolladores
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 9e64d184ef0fd120d1bc64ce274ee882b7938df0
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708646"
---
# <a name="azure-synapse-analytics-formerly-sql-dw-frequently-asked-questions"></a>Preguntas frecuentes sobre Azure Synapse Analytics (anteriormente SQL DW)

## <a name="general"></a>General

P: ¿Qué es Azure Synapse?

A. Azure Synapse es un servicio de análisis ilimitado que reúne el almacenamiento de datos y el análisis de macrodatos. Le ofrece la libertad de consultar los datos como prefiera, ya sea a petición sin servidor o con recursos aprovisionados, a escala. Azure Synapse reúne estos dos mundos con una experiencia unificada para ingerir, preparar, administrar y servir datos para las necesidades inmediatas de inteligencia empresarial y aprendizaje automático. Para más información, consulte [¿Qué es Azure Synapse Analytics?](sql-data-warehouse-overview-what-is.md)

P: ¿Qué ha pasado con Azure SQL Data Warehouse?

A. Azure Synapse es Azure SQL Data Warehouse (SQL DW) evolucionado. Hemos llevado el mismo almacenamiento de datos líder del sector a un nuevo nivel de rendimiento y funcionalidades. Puede seguir ejecutando sus cargas de trabajo de almacenamiento de datos existentes en producción hoy mismo con Azure Synapse y beneficiarse automáticamente de las nuevas funcionalidades que se encuentran en versión preliminar. Para más información, consulte [¿Qué es Azure Synapse Analytics?](sql-data-warehouse-overview-what-is.md)

P: ¿Qué es SQL Analytics?

A. SQL Analytics hace referencia a las características de almacenamiento de datos empresariales que están disponibles con carácter general en Azure Synapse. Para más información, consulte [¿Qué es Azure Synapse Analytics?](sql-data-warehouse-overview-what-is.md)

P: ¿Cómo puedo empezar a usar Azure Synapse?

A. Puede empezar a trabajar con una [cuenta gratuita de Azure](https://azure.microsoft.com/free/sql-data-warehouse/) o [ponerse en contacto con Ventas para más información](https://info.microsoft.com/ww-landing-azure-sql-data-warehouse-contactme.html). 

P: ¿Qué ofrece Azure Synapse en cuanto a la seguridad de datos?

A. Azure Synapse ofrece varias soluciones para proteger datos, como TDE y auditorías. Para obtener más información, consulte [Seguridad].

P: ¿Dónde puedo averiguar sobre los estándares empresariales o legales que cumple Azure Synapse?

A. Visite la página [Cumplimiento de Microsoft] para consultar diversas ofertas de cumplimiento por producto, como SOC e ISO. Primero, elija por el título Cumplimiento, luego, expanda Azure en la sección de servicios en la nube de Microsoft del lado derecho de la página para ver qué servicios son compatibles con Azure Synapse.

P: ¿Puedo conectar Power BI?

A. Sí. Aunque Power BI admite la consulta directa con Azure Synapse, no se ha diseñado para administrar muchos usuarios ni datos en tiempo real. Para optimizar aún más el rendimiento de Power BI, considere la posibilidad de usar Power BI en Azure Analysis Services o la infraestructura como servicio de Analysis Services.

P: ¿Cuáles son los límites de capacidad de SQL Analytics?

A. Consulte nuestra página de [límites de capacidad] actuales. 

P: ¿Por qué tardan tanto tiempo en completarse las operaciones de escalado, pausar y reanudación?

A. Hay varios factores que puede influir en el tiempo que tardan en completarse las operaciones de administración de procesos. Un caso habitual de operaciones de ejecución prolongada es la reversión de transacciones. Cuando se inicia una operación de escalado o pausa, se bloquean todas las sesiones entrantes y se purgan las consultas. Para dejar el sistema en un estado estable, las transacciones se deben revertir antes de que pueda comenzar una operación. Cuanto mayor sea su número y el tamaño del registro de las transacciones, más tiempo estará detenida la operación al restaurar el sistema a un estado estable.

## <a name="user-support"></a>Asistencia técnica de usuario

P: Tengo una solicitud de característica, ¿dónde puede enviarla?

A. Si tiene una solicitud de característica, envíala a nuestra página [UserVoice].

P: ¿Cómo puedo hacer una determinada acción?

A. Para obtener ayuda con las tareas de desarrollo con Azure Synapse, puede hacer preguntas en nuestra página [Stack Overflow]. 

P: ¿Cómo puedo enviar una vale de asistencia técnica?

A. Los [vales de asistencia técnica] puede presentarse a través de Azure Portal.

## <a name="sql-languagefeature-support"></a>Compatibilidad con características o lenguajes de SQL 

P: ¿Qué tipos de datos se admiten?

A. Consulte [Tipos de datos].

P: ¿Qué características de tablas se admiten?

A. Se admiten muchas características, y las que no se admiten están documentadas en [Características de tablas no compatibles].

## <a name="tooling-and-administration"></a>Administración y herramientas

P: ¿Admite SQL Analytics las API de REST?

A. Sí. La mayoría de las funciones de REST que se pueden utilizar con SQL Database también están disponibles en SQL Analytics. Puede encontrar información sobre las API en las páginas de documentación de REST o [MSDN].


## <a name="loading"></a>Carga

P: ¿Qué controladores de cliente se admiten?

A. La compatibilidad de los controladores con SQL Data Warehouse puede encontrarse en la página [Cadenas de conexión].

P: ¿Qué formatos de archivo admite PolyBase?

R: Orc, RC, Parquet y texto delimitado sin formato.

P: ¿A qué orígenes de datos puedo conectarme mediante PolyBase? 

R: [Azure Data Lake Store] e instancias de [Blobs de Azure Storage].

P: ¿Se puede usar el cálculo de aplicación al conectarse a instancias de Azure Storage Blob o ADLS? 

R: No, PolyBase interactúa solo con los componentes de almacenamiento. 

P: ¿Puedo conectarlo a HDI?

R: HDI puede usar ADLS o WASB como capa HDFS. Si tiene uno de los dos como la capa HDFS, puede cargar datos en SQL Data Warehouse. Sin embargo, no se puede generar el cálculo de aplicación en la instancia HDI. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre Azure Synapse, vea nuestra página [Información general].


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Cadenas de conexión]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Vales de asistencia técnica]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Seguridad]: ./sql-data-warehouse-overview-manage-security.md
[Cumplimiento de Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[Límites de capacidad]: ./sql-data-warehouse-service-capacity-limits.md
[tipos de datos]: ./sql-data-warehouse-tables-data-types.md
[Características de tablas no compatibles]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Blobs de Azure Storage]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Database projects feature request]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/library/azure/mt163685.aspx
[Información general]: ./sql-data-warehouse-overview-faq.md
