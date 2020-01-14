---
title: Límites de recursos de Azure SQL Database | Microsoft Docs
description: En este artículo se proporciona información general sobre los límites de recursos de Azure SQL Database para bases de datos únicas y grupos elásticos. También se proporciona información acerca de lo qué ocurre cuando se alcanzan o superan dichos límites.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan,moslake,josack
ms.date: 11/19/2019
ms.openlocfilehash: da8c194b7911d2eeda8e0c903cb7412186aacfcb
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638262"
---
# <a name="sql-database-resource-limits-and-resource-governance"></a>Límites y regulación de recursos de SQL Database

En este artículo se proporciona información general acerca de los límites de recursos de SQL Database para un servidor de SQL Database que administra bases de datos únicas y grupos elásticos. Se incluye información sobre lo que ocurre cuando esos límites de recursos se alcanzan o se superan y se describen los mecanismos de regulación de recursos usados para aplicar estos límites.

> [!NOTE]
> Para conocer los límites de Instancia administrada, consulte [Límites de recursos de SQL Database para instancias administradas](sql-database-managed-instance-resource-limits.md).

## <a name="maximum-resource-limits"></a>Límites de recursos máximos

| Resource | Límite |
| :--- | :--- |
| Bases de datos por servidor | 5000 |
| Número predeterminado de servidores por suscripción en cualquier región | 20 |
| Número máximo de servidores por suscripción en cualquier región | 200 |  
| Cuota de DTU o eDTU por servidor | 54 000 |  
| Cuota de núcleo virtual por servidor/instancia | 540 |
| Número máximo de grupos por servidor | Limitado por el número de DTU o núcleos virtuales. Por ejemplo, si cada grupo tiene 1000 DTU, un servidor puede admitir 54 grupos.|
|||

> [!NOTE]
> Para obtener más cuota de DTU o eDTU, cuota de núcleos virtuales o más servidores que la cantidad predeterminada, se puede enviar una nueva solicitud de soporte técnico para la suscripción en Azure Portal con el tipo de problema "Cuota". La cuota de DTU o eDTU y el límite de base de datos por servidor restringe el número de grupos elásticos por servidor.

> [!IMPORTANT]
> A medida que el número de bases de datos se aproxima al límite por servidor de SQL Database, puede ocurrir lo siguiente:
>
> - Aumento de la latencia de las consultas en ejecución en la base de datos maestra.  Incluye vistas de las estadísticas del uso de los recursos como sys.resource_stats.
> - Aumento de la latencia en las operaciones de administración y presentación de las perspectivas del portal que implican enumerar las bases de datos del servidor.

### <a name="storage-size"></a>Tamaño de almacenamiento

Para información sobre los tamaños de almacenamiento de recursos de bases de datos únicas, consulte los [límites de recursos basados en DTU](sql-database-dtu-resource-limits-single-databases.md) o los [límites de recursos basados en núcleo virtual](sql-database-vcore-resource-limits-single-databases.md) para conocer los límites de tamaño de almacenamiento por plan de tarifa.

## <a name="what-happens-when-database-resource-limits-are-reached"></a>¿Qué ocurre cuando se alcanzan los límites de recursos de base de datos?

### <a name="compute-dtus-and-edtus--vcores"></a>Proceso (DTU y eDTU o núcleos virtuales)

Cuando el uso del proceso de base de datos (medido por las DTU y eDTU o los núcleos virtuales) es elevado, la latencia de las consultas aumenta e incluso puede agotar el tiempo de espera. En estas condiciones, el servicio puede poner en cola las consultas y se proporcionan recursos para la ejecución a medida que el recurso esté disponible.
Al encontrar un uso de proceso elevado, las opciones de mitigación incluyen:

- Aumentar el tamaño de proceso de la base de datos o del grupo elástico para proporcionar a la base de datos más recursos de proceso. Consulte [Scale single database resources](sql-database-single-database-scale.md) (Escala de recursos de bases de datos únicas) y [Scale elastic pool resources](sql-database-elastic-pool-scale.md) (Escala de recursos de grupos elásticos).
- Optimizar las consultas para reducir el uso de recursos de cada consulta. Para más información, consulte [Optimización y sugerencias de consultas](sql-database-performance-guidance.md#query-tuning-and-hinting).

### <a name="storage"></a>Storage

Cuando el espacio de la base de datos alcanza el límite de tamaño máximo, las inserciones y actualizaciones de la base de datos que aumentan el tamaño de los datos producen un error y los clientes reciben un [mensaje de error](troubleshoot-connectivity-issues-microsoft-azure-sql-database.md). Las instrucciones SELECT y DELETE se siguen ejecutando correctamente.

Al encontrar un uso elevado de espacio, las opciones de mitigación incluyen:

- Aumentar el tamaño máximo de la base de datos o del grupo elástico, o agregar más almacenamiento. Consulte [Scale single database resources](sql-database-single-database-scale.md) (Escala de recursos de bases de datos únicas) y [Scale elastic pool resources](sql-database-elastic-pool-scale.md) (Escala de recursos de grupos elásticos).
- Si la base de datos está en un grupo elástico, se puede mover fuera del grupo para que su espacio de almacenamiento no se comparta con otras bases de datos.
- Reduzca una base de datos para reclamar el espacio no utilizado. Para obtener más información, consulte [Manage file space in Azure SQL Database](sql-database-file-space-management.md) (Administración de espacio de archivos en Azure SQL Database).

### <a name="sessions-and-workers-requests"></a>Sesiones y trabajos (solicitudes)

El número máximo de sesiones y trabajos se determina por el nivel de servicio y el tamaño de proceso (DTU y eDTU o núcleos virtuales). Las nuevas solicitudes se rechazan cuando se alcanzan los límites de sesión o de trabajo y los clientes reciben un mensaje de error. Aunque el número de conexiones disponibles se pueden controlar por la aplicación, el número de trabajos simultáneos suele ser más difícil de calcular y controlar. Esto es especialmente cierto durante los períodos de carga máxima, cuando se alcanzan los límites de recursos de base de datos y los trabajos se apilan debido a consultas de larga duración, cadenas de bloqueo de gran tamaño o excesivo paralelismo de las consultas.

Al encontrar un uso elevado de sesión o de trabajo, las opciones de mitigación incluyen:

- Aumentar el nivel de servicio o el tamaño de proceso de la base de datos o del grupo elástico. Consulte [Scale single database resources](sql-database-single-database-scale.md) (Escala de recursos de bases de datos únicas) y [Scale elastic pool resources](sql-database-elastic-pool-scale.md) (Escala de recursos de grupos elásticos).
- Optimizar las consultas para reducir el uso de recursos de cada consulta si la causa del mayor uso de trabajo es debida a la contención de los recursos de proceso. Para más información, consulte [Optimización y sugerencias de consultas](sql-database-performance-guidance.md#query-tuning-and-hinting).

## <a name="resource-governance"></a>Regulación de recursos

Para aplicar límites de recursos, Azure SQL Database usa una implementación de regulación de recursos basada en [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) de SQL Server, modificada y extendida para ejecutar un servicio de base de datos de SQL Server en Azure. En cada instancia de SQL Server del servicio, hay varios [grupos de recursos](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-resource-pool) y [grupos de cargas de trabajo](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor-workload-group), con límites de recursos que se establecen en el nivel de grupo para proporcionar una [base de datos como servicio equilibrada](https://azure.microsoft.com/blog/resource-governance-in-azure-sql-database/). La carga de trabajo de usuario y las cargas de trabajo internas se clasifican en grupos de recursos y grupos de cargas de trabajo distintos. La carga de trabajo de usuario de las réplicas primarias y secundarias legibles, incluidas las réplicas geográficas, se clasifica en el grupo de recursos `SloSharedPool1` y en el grupo de cargas de trabajo `UserPrimaryGroup.DBId[N]`, donde `N` representa el valor del identificador de base de datos. Además, hay varios grupos de recursos y grupos de cargas de trabajo para varias cargas de trabajo internas.

Además de usar Resource Governor para regular los recursos dentro del proceso de SQL Server, Azure SQL Database también emplea [objetos de trabajo](https://docs.microsoft.com/windows/win32/procthread/job-objects) de Windows para la regulación de los recursos de nivel de proceso y el [Administrador de recursos del servidor de archivos](https://docs.microsoft.com/windows-server/storage/fsrm/fsrm-overview) de Windows para la administración de la cuota de almacenamiento.

La regulación de recursos de Azure SQL Database es jerárquica por naturaleza. De arriba a abajo, los límites se aplican en el nivel de sistema operativo y en el nivel de volumen de almacenamiento mediante mecanismos de regulación de recursos del sistema operativo y Resource Governor; en el nivel de grupo de recursos mediante Resource Governor; y en el nivel de grupo de cargas de trabajo mediante Resource Governor. Los límites de regulación de recursos en vigor para la base de datos o el grupo elástico actuales se exponen en la vista [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database). 

### <a name="data-io-governance"></a>Regulación de E/S de los datos

La regulación de E/S de los datos es un proceso de Azure SQL Database que se usa para limitar la E/S física de lectura y escritura en los archivos de datos de una base de datos. Para cada nivel de servicio se establecen límites de IOPS con el fin de minimizar el efecto "vecino ruidoso", para proporcionar igualdad en la asignación de recursos en el servicio multiinquilino y para permanecer dentro de las capacidades del hardware y el almacenamiento subyacentes.

En el caso de bases de datos únicas, los límites del grupo de cargas de trabajo se aplican a toda la E/S de almacenamiento de la base de datos, mientras que los límites del grupo de recursos se aplican a toda la E/S de almacenamiento de todas las bases de datos de la misma instancia de SQL Server, incluida la base de datos `tempdb`. En el caso de grupos elásticos, los límites del grupo de cargas de trabajo se aplican a cada base de datos del grupo, mientras que el límite del grupos de recursos se aplica a todo el grupo elástico, incluida la base de datos `tempdb`, que se comparte entre todas las bases de datos del grupo. En general, es posible que la carga de trabajo no pueda lograr los límites del grupo de recursos de una base de datos (sola o agrupada), porque los límites del grupo de cargas de trabajo son inferiores a los límites del grupo de recursos y limitan antes el número de IOPS y el rendimiento. Sin embargo, la carga de trabajo combinada puede alcanzar los límites del grupo en varias bases de datos de la misma instancia de SQL Server.

Por ejemplo, si una consulta genera 1000 IOPS sin regulación de recursos de E/S, pero el límite máximo de IOPS del grupo de cargas de trabajo se establece en 900 IOPS, la consulta no podrá generar más de 900 IOPS. No obstante, si el límite máximo de IOPS del grupo de recursos se establece en 1500 IOPS y la E/S total de todos los grupos de cargas de trabajo asociados con el grupo de recursos supera los 1500 IOPS, la E/S de la misma consulta puede reducirse por debajo del límite del grupo de trabajo de 900 IOPS.

El número de IOPS y los valores mínimo y máximo de rendimiento que devuelve la vista [sys.dm_user_db_resource_governance](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-user-db-resource-governor-azure-sql-database) funcionan como límites, no como garantías. Además, la regulación de recursos no garantiza ninguna latencia de almacenamiento específica. Los mejores logros en cuanto a latencia, IOPS y rendimiento para una carga de trabajo de usuario determinada no dependen solo de los límites de regulación de los recursos, sino también de la mezcla de tamaños de E/S usados y de las capacidades del almacenamiento subyacente. SQL Server usa E/S con un tamaño variable de entre 512 KB y 4 MB. Con el fin de aplicar los límites de IOPS, cada E/S se cuenta con independencia de su tamaño, a excepción de las bases de datos con archivos de datos de Azure Storage. En ese caso, las E/S de más de 256 KB se cuentan como varias E/S de 256 KB, para alinearse con la cuenta de E/S de Azure Storage.

En las bases de datos Básica, Estándar y De uso general, que usan archivos de datos de Azure Storage, es posible que el valor de `primary_group_max_io` no sea factible si una base de datos no tiene suficientes archivos de datos para proporcionar de forma acumulativa este número de IOPS; si los datos no se distribuyen uniformemente entre los archivos; o si el nivel de rendimiento de los blobs subyacentes limita el número de IOPS o el rendimiento por debajo del límite de regulación de los recursos. Igualmente, con E/S de registros pequeños generadas por la confirmación frecuente de transacciones, puede que el valor `primary_max_log_rate` no sea factible para una carga de trabajo debido al límite de IOPS en el blob de almacenamiento de Azure subyacente.

Los valores de uso de recursos, como `avg_data_io_percent` y `avg_log_write_percent`, notificados en las vistas [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database),  [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)y [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database), se calculan como porcentajes de los límites máximos de regulación de recursos. Por lo tanto, cuando otros factores que no son la regulación de recursos limitan el número de IOPS o el rendimiento, es posible que se reduzcan estos y que aumenten las latencias a medida que se incrementa la carga de trabajo, incluso aunque la utilización de los recursos notificados permanezca por debajo del 100 %. 

Para ver el número de IOPS, el rendimiento y la latencia de lectura y escritura por archivo de base de datos, utilice la función [sys.dm_io_virtual_file_stats()](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql). Esta función muestra toda la E/S en la base de datos, incluida la que tiene lugar en segundo plano que no se tiene en cuenta en `avg_data_io_percent`, pero usa el número de IOPS y el rendimiento del almacenamiento subyacente y puede afectar a la latencia de almacenamiento observada. La función también muestra una latencia adicional que puede ser introducida por el gobierno de recursos de e/s para lecturas y escrituras, en las columnas `io_stall_queued_read_ms` y `io_stall_queued_write_ms`, respectivamente.

### <a name="transaction-log-rate-governance"></a>Regulación de la tasa de registro de transacciones

La gobernanza de las tasas de registros de transacciones es un proceso en Azure SQL Database que se usa para limitar las altas tasas de ingesta de cargas de trabajo como la inserción masiva, SELECT INTO y compilaciones de índice. Se realiza un seguimiento de estos límites y se aplican en el nivel de fracciones de segundo a la tasa de generación de registros, lo que limita el rendimiento sin importar cuántas E/S se pueden emitir respecto de los archivos de datos.  Las velocidades de generación de registros de transacciones se escalan linealmente hasta un punto que depende del hardware, con la tasa de registro máxima permitida de 96 MB/s con el modelo de compra de núcleo virtual. 

> [!NOTE]
> No se controlan ni limitan las E/S físicas reales para los archivos de registro de transacciones.

Las tasas de registro se establecen de manera que se puedan alcanzar y sostener en diversos escenarios, mientras que el sistema global puede mantener su funcionalidad con un mínimo de impacto en la carga del usuario. La gobernanza de las tasas de registro garantiza que las copias de seguridad del registro de transacciones se mantendrán dentro de los contratos de nivel de servicio de la capacidad de recuperación publicados.  Esta gobernanza también evita un trabajo pendiente excesivo en las réplicas secundarias.

A medida que se generan los registros, se evalúa cada operación para ver si se debe retrasar a fin de mantener una tasa máxima de registro deseada (MB/s por segundo). No se agregan retrasos cuando los registros se vacían en el almacenamiento. En lugar de eso, la gobernanza de tasas de registro se aplica durante la generación de registros misma.

Las tasas reales de generación de registros impuestas en tiempo de ejecución también podrían verse influenciadas por los mecanismos de comentarios, lo que reducirá de manera temporal las tasas de registros permitidas para que el sistema se pueda estabilizar. La administración del espacio de los archivos de registro, para evitar la falta de espacio para registros, y los mecanismos de replicación del grupo de disponibilidad pueden disminuir temporalmente los límites del sistema global.

El modelado de tráfico del regulador de la tasas de registros se expone a través de los siguientes tipos de espera (expuesto en la DMV [sys.dm_db_wait_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-wait-stats-azure-sql-database)):

| Tipo de espera | Notas |
| :--- | :--- |
| LOG_RATE_GOVERNOR | Limitación de la base de datos |
| POOL_LOG_RATE_GOVERNOR | Limitación del grupo |
| INSTANCE_LOG_RATE_GOVERNOR | Limitación del nivel de instancia |  
| HADR_THROTTLE_LOG_RATE_SEND_RECV_QUEUE_SIZE | Control de comentarios, la replicación física del grupo de disponibilidad en el nivel de servicio Crítico para la empresa/Premium no está al día |  
| HADR_THROTTLE_LOG_RATE_LOG_SIZE | Control de comentarios, limitación de las tasas para evitar quedarse sin espacio para registros |
|||

Cuando encuentre un límite para las tasas de registros que dificulte la alcanzar la escalabilidad deseada, considere estas opciones:
- Escale verticalmente hasta un nivel de servicio superior para obtener la velocidad máxima de registro de 96 MB/s. 
- Si los datos que se cargan son transitorios, como los datos de ensayo de un proceso de ETL, se pueden cargar en tempdb (que genera un mínimo de registros). 
- En los escenarios de análisis, cárguelos en una tabla cubierta de almacén de columnas en clúster. Esto disminuye la tasa de registros necesario debido a la compresión. Esta técnica sí aumenta la utilización de la CPU y solo se aplica a los conjuntos de datos que se benefician de los índices de almacén de columnas en clúster. 

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los límites generales de Azure, consulte [Límites, cuotas y restricciones de suscripción y servicios de Microsoft Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para más información sobre las DTU y eDTU, consulte [DTU y eDTU](sql-database-purchase-models.md#dtu-based-purchasing-model).
- Para más información sobre los límites de tamaño de tempdb, consulte [Base de datos tempdb en Azure SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).
