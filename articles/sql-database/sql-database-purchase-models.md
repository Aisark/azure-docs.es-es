---
title: Modelos de compra
description: Información acerca de los modelos de compra que están disponibles para Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 04/26/2019
ms.openlocfilehash: be223245c634b8e38dc5e4c89df4c265c22c0b57
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73821261"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Elija entre los modelos de compra de núcleo virtual y de DTU

Azure SQL Database le permite comprar fácilmente motores de base de datos de plataforma como servicio (PaaS) totalmente administrados, que se ajusten a sus necesidades de rendimiento y costos. Según el modelo de implementación que haya elegido para Azure SQL Database, puede seleccionar el modelo de compra que más se adapte a sus necesidades:

- [Modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md) (recomendado). Este modelo de compra permite elegir entre un nivel de proceso aprovisionado y un nivel de proceso sin servidor. Con el nivel de proceso aprovisionado, elige la cantidad exacta de recursos de proceso que se aprovisionan siempre para la carga de trabajo. Con el nivel de proceso sin servidor, debe especificar el escalado automático de los recursos de proceso mediante un rango de procesos configurables. Con este nivel de proceso, también puede pausar y reanudar automáticamente la base de datos en función de la actividad de carga de trabajo. El precio unitario de un núcleo virtual por unidad de tiempo es inferior en el nivel de proceso aprovisionado que en el nivel de proceso sin servidor.
- [Modelo de compra basado en unidad de transacción de base de datos (DTU)](sql-database-service-tiers-dtu.md). Este modelo de compra proporciona paquetes de proceso y almacenamiento agrupados y equilibrados para cargas de trabajo habituales.

Hay disponibles distintos modelos de compra para los diferentes modelos de implementación de Azure SQL Database:

- Las opciones de implementación de [base de datos única](sql-database-single-databases-manage.md) y [grupo elástico](sql-database-elastic-pool.md) de [Azure SQL Database](sql-database-technical-overview.md) ofrecen ambas el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md) y el [modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md).
- La opción de implementación de [instancia administrada](sql-database-managed-instance.md) de Azure SQL Database solo ofrece el [modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md).
- El [nivel de servicio Hiperescala](sql-database-service-tier-hyperscale.md) solo está disponible para bases de datos únicas que usan el [modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md).

En la tabla y el gráfico siguientes se comparan y contrastan los modelos de compra basado en núcleo virtual y basado en DTU:

|**Modelo de compra**|**Descripción**|**Más adecuado para**|
|---|---|---|
|Modelo basado en DTU|Este modelo se basa en una medida agrupada de recursos de proceso, almacenamiento y E/S. Los tamaños de proceso se expresan como DTU para las bases de datos únicas y como unidades de transacción de base de datos elástica (eDTU) para los grupos elásticos. Para más información sobre las DTU y eDTU, consulte [¿Qué son las DTU y las eDTU?](sql-database-purchase-models.md#dtu-based-purchasing-model)|Recomendado para los clientes que desean opciones de recursos sencillas y previamente configuradas.|
|Modelo basado en núcleos virtuales|Este modelo le permite elegir los recursos de proceso y almacenamiento de manera independiente. El modelo de compra basado en núcleo virtual también le permite usar la [Ventaja híbrida de Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para ahorrar en los costos.|Recomendado para los clientes que valoran la flexibilidad, el control y la transparencia.|
||||  

![comparación de modelos de precios](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Costos de proceso

### <a name="provisioned-compute-costs"></a>Costos de proceso aprovisionado

En el nivel de proceso aprovisionado, el costo de proceso refleja la capacidad de proceso total que se aprovisiona para la aplicación.

En el nivel de servicio Crítico para la empresa, se asignan automáticamente como mínimo tres réplicas. Para reflejar esta asignación adicional de recursos de proceso, el precio del modelo de compra basado en núcleo virtual es aproximadamente 2,7 veces más elevado en el nivel de servicio Crítico para la empresa que en el nivel de servicio Uso general. De igual manera, el mayor precio de almacenamiento por GB en el nivel de servicio Crítico para la empresa refleja la E/S alta y la baja latencia del almacenamiento SSD.

El costo del almacenamiento de copia de seguridad es el mismo para el nivel de servicio Crítico para la empresa y el nivel de servicio Uso general porque ambos niveles usan un almacenamiento estándar.

### <a name="serverless-compute-costs"></a>Costos de proceso sin servidor

Para una descripción de cómo se define la capacidad de proceso y cómo se calculan los costos para el nivel de proceso sin servidor, consulte [SQL Database sin servidor](sql-database-serverless.md).

## <a name="storage-costs"></a>Costos de almacenamiento

Diferentes tipos de almacenamiento se facturan de forma diferente. En el almacenamiento de datos, se le cobra por el almacenamiento aprovisionado en función del tamaño máximo de la base de datos o del grupo que seleccione. El costo no cambia a menos que reduzca o aumente ese máximo. El almacenamiento de copia de seguridad se asocia a las copias de seguridad automatizadas de la instancia y su ubicación es dinámica. Al aumentar el período de retención de las copias de seguridad, aumenta el almacenamiento de copia de seguridad que consume la instancia.

De forma predeterminada, se realizan copias de seguridad automatizadas de las bases de datos durante siete días en una cuenta de almacenamiento de blobs estándar de almacenamiento con redundancia geográfica con acceso de lectura (RA-GRS). Este almacenamiento se usa para realizar cada cinco minutos copias de seguridad completas semanales, copias de seguridad diferenciales diarias y copias de seguridad de registros de transacciones. El tamaño de los registros de transacciones depende de la tasa de cambio de la base de datos. Se ofrece una cantidad de almacenamiento mínimo igual al 100 % del tamaño de la base de datos sin costo adicional. El consumo adicional de almacenamiento de copia de seguridad se cobra en GB/mes.

Para más información sobre los precios de almacenamiento, consulte la página de [precios](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modelo de compra basado en núcleo virtual

Un núcleo virtual representa la CPU lógica y le ofrece una opción para elegir entre varias generaciones de hardware y las características físicas de hardware (por ejemplo, el número de núcleos, la memoria y el tamaño de almacenamiento). El modelo de compra basado en núcleo virtual le ofrece flexibilidad, control, transparencia de consumo de recursos individuales y una manera sencilla de trasladar los requisitos de carga de trabajo locales a la nube. Este modelo le permite elegir los recursos de proceso, memoria y almacenamiento en función de las necesidades de la carga de trabajo.

En el modelo de compra basado en núcleo virtual, puede elegir entre los niveles de servicio [Uso general](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) y [Crítico para la empresa](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para [bases de datos únicas](sql-database-single-database-scale.md), [grupos elásticos](sql-database-elastic-pool.md) e [instancias administradas](sql-database-managed-instance.md). Para las bases de datos únicas, también puede elegir el [nivel de servicio Hiperescala](sql-database-service-tier-hyperscale.md).

El modelo de compra basado en núcleo virtual le permite elegir los recursos de proceso y de almacenamiento de manera independiente, igualar el rendimiento local y optimizar el precio. En el modelo de compra basado en núcleo virtual, paga por:

- Los recursos de proceso (nivel de servicio + número de núcleos virtuales y cantidad de memoria + generación de hardware).
- El tipo y la cantidad de almacenamiento de datos y registros.
- El almacenamiento de copia de seguridad (RA-GRS).

> [!IMPORTANT]
> Los recursos de proceso, E/S y el almacenamiento de datos y registros se cobran por base de datos o grupo elástico. El almacenamiento de copia de seguridad se cobra por cada base de datos. Para más información sobre los cargos de instancia administrada, consulte [Instancias administradas](sql-database-managed-instance.md).
> **Limitaciones regionales:** Para conocer la lista actual de regiones admitidas, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para crear una instancia administrada en una región que no se admita actualmente, [envíe una solicitud de soporte técnico a través de Azure Portal](sql-database-managed-instance-resource-limits.md#obtaining-a-larger-quota-for-sql-managed-instance).

Si la base de datos única o el grupo elástico consume más de 300 DTU, la conversión al modelo de compra basado en núcleo virtual puede reducir los costos. Puede realizar la conversión mediante la API de su elección o usar Azure Portal, sin experimentar tiempo de inactividad. Sin embargo, la conversión no es obligatoria y no se realiza automáticamente. Si el modelo de compra basado en DTU satisface sus requisitos de rendimiento y empresariales, debe seguir usándolo.

Para cambiar del modelo de compra basado en DTU al modelo de compra basado en núcleo virtual, seleccione el tamaño de proceso mediante las siguientes reglas generales:

- Por cada 100 DTU en el nivel Estándar se requiere al menos 1 núcleo virtual en el nivel de servicio Uso general.
- Por cada 125 DTU en el nivel Premium se requiere al menos 1 núcleo virtual en el nivel de servicio Crítico para la empresa.

## <a name="dtu-based-purchasing-model"></a>Modelo de compra basado en DTU

Una unidad de transacción de base de datos (DTU) representa una medida combinada de CPU, memoria, lecturas y escrituras. El modelo de compra basado en DTU ofrece un conjunto de agrupaciones preconfiguradas de recursos de proceso y almacenamiento incluido para impulsar diferentes niveles de rendimiento de la aplicación. Si prefiere la sencillez de una agrupación preconfigurada y pagos fijos cada mes, el modelo basado en DTU puede ser más adecuado para sus necesidades.

En el modelo de compra basado en DTU, puede elegir entre los niveles de servicio Básico, Estándar y Premium tanto para [bases de datos únicas](sql-database-single-database-scale.md) como para [grupos elásticos](sql-database-elastic-pool.md). El modelo de compra basado en DTU no está disponible para [instancias administradas](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>Unidades de transacción de base de datos (DTU)

Para una base de datos única en un tamaño de proceso específico dentro de un [nivel de servicio](sql-database-single-database-scale.md), Microsoft garantiza determinado nivel de recursos para esa base de datos (independiente de cualquier otra base de datos en la nube de Azure). Esta garantía ofrece un nivel de rendimiento predecible. La cantidad de recursos asignada a una base de datos se calcula como un número de DTU y es una medida combinada de recursos de proceso, almacenamiento y E/S.

La relación entre estos recursos se determina originalmente mediante una [carga de trabajo de prueba comparativa de procesamiento de transacciones en línea (OLTP)](sql-database-benchmark-overview.md) diseñada para representar las típicas cargas de trabajo OLTP reales. Cuando la carga de trabajo supera la cantidad de cualquiera de estos recursos, se limita el rendimiento, con lo que se obtiene un rendimiento y tiempos de espera más lentos.

Los recursos utilizados por la carga de trabajo no afectan a los recursos disponibles para otras bases de datos SQL en la nube de Azure. Del mismo modo, los recursos utilizados por otras cargas de trabajo no afectan a los recursos disponibles para la base de datos SQL.

![rectángulo de selección](./media/sql-database-what-is-a-dtu/bounding-box.png)

Las DTU son especialmente útiles para comprender los recursos relativos que están asignados a las bases de datos de Azure SQL en los diferentes tamaños de proceso y niveles de servicio. Por ejemplo:

- Duplicar el número de DTU al aumentar el tamaño de proceso de una base de datos equivale a duplicar el conjunto de recursos disponibles para esa base de datos.
- Una base de datos P11 de nivel de servicio Premium con 1750 DTU proporciona una potencia de proceso de DTU 350 veces mayor que una base de datos de nivel de servicio Básico con 5 DTU.  

Para más información sobre el consumo de recursos (DTU) de la carga de trabajo, use la [información de rendimiento de consultas](sql-database-query-performance.md) para:

- Identificar las consultas principales por CPU, duración y recuento de ejecuciones, que se pueden ajustar para mejorar el rendimiento. Por ejemplo, una consulta que realiza muchas operaciones de E/S puede beneficiarse de las [técnicas de optimización en memoria](sql-database-in-memory.md) para hacer un mejor uso de la memoria disponible en determinado nivel de servicio y tamaño de proceso.
- Explorar en profundidad los detalles de una consulta, ver su texto e historial de uso de recursos.
- Tener acceso a recomendaciones de ajuste del rendimiento que muestran las acciones realizadas por [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades de transacción de base de datos elástica (eDTU)

Para las bases de datos SQL que están siempre disponibles, en lugar de proporcionarles un conjunto dedicado de recursos (DTU) que no siempre pueden necesitar, puede colocar estas bases de datos en un [grupo elástico](sql-database-elastic-pool.md). Las bases de datos de un grupo elástico se encuentran en un único servidor de Azure SQL Database y comparten un grupo de recursos.

Los recursos compartidos de un grupo elástico se miden mediante unidades de transacción de base de dato elástica (eDTU). Los grupos elásticos proporcionan una solución sencilla y rentable para administrar los objetivos de rendimiento de varias bases de datos que tienen patrones de uso muy diferentes e imprevisibles. Un grupo elástico garantiza que una única base de datos del grupo no pueda consumir todos los recursos, mientras que asegura que cada base de datos del grupo tenga disponible siempre una cantidad mínima de recursos necesarios.

A un grupo se le asigna un número fijo de eDTU, por un precio fijo. En el grupo elástico, las bases de datos individuales se pueden escalar automáticamente dentro de los límites configurados. Una base de datos con cargas más elevadas consumirá más eDTU para satisfacer la demanda. Las bases de datos con cargas más bajas consumirán menos eDTU. Las bases de datos sin cargas no consumirán ningún eDTU. Como los recursos se aprovisionan para todo el grupo, en lugar de por base de datos, los grupos elásticos simplifican las tareas de administración, y proporcionan un presupuesto predecible para el grupo.

Puede agregar eDTU adicionales a un grupo existente sin que la base de datos experimente tiempo de inactividad ni que las bases de datos del grupo resulten afectadas. De forma similar, si ya no necesita eDTU adicionales, puede quitarlas de un grupo existente en cualquier momento. También puede agregar o quitar bases de datos de un grupo en cualquier momento. Para reservar eDTU para otras bases de datos, limite el número de eDTU que puede usar una base de datos con una carga elevada. Si una base de datos infrautiliza recursos de forma constante, sáquela del grupo y configúrela como una base de datos única con una cantidad predecible de recursos necesarios.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar el número de DTU necesarias para la carga de trabajo

Si desea migrar una carga de trabajo de máquina virtual existente local o de SQL Server a Azure SQL Database, utilice la [calculadora de DTU](https://dtucalculator.azurewebsites.net/) para hacer una estimación del número aproximado de DTU que se necesitan. Con cargas de trabajo existentes de Azure SQL Database, use la [información de rendimiento de consultas](sql-database-query-performance.md) para comprender el consumo de recursos de base de datos (DTU) y obtener información más detallada para optimizar la carga de trabajo. La vista de administración dinámica (DMV) [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) le permite ver el consumo de recursos de la última hora. La vista de catálogo [sys.resource_stats](https://msdn.microsoft.com/library/dn269979.aspx) muestra el consumo de recursos de los últimos 14 días, aunque con una fidelidad inferior media de cinco minutos.

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabajo que se benefician de un grupo elástico de recursos

Los grupos son adecuados para las bases de datos con un promedio de uso de recursos bajo y picos de uso relativamente poco frecuentes. SQL Database evalúa automáticamente el historial de uso de recursos de bases de datos en un servidor de SQL Database existente y recomienda la configuración de grupo apropiada en Azure Portal. Para más información, consulte [¿Cuándo se debe usar un grupo elástico de SQL Database?](sql-database-elastic-pool.md)

## <a name="frequently-asked-questions-faqs"></a>Preguntas más frecuentes (P+F)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>¿Tengo que desconectar la aplicación para realizar la conversión de un nivel de servicio basado en DTU a un nivel de servicio basado en núcleo virtual?

No. No es necesario desconectar la aplicación. Los nuevos niveles de servicio ofrecen un método sencillo de conversión en línea similar al proceso existente de actualizar las bases de datos desde el nivel de servicio Estándar a Premium, y viceversa. Puede iniciar esta conversión mediante Azure Portal, PowerShell, la CLI de Azure, T-SQL o la API REST. Consulte [Administración de bases de datos únicas](sql-database-single-database-scale.md) y [Administración de grupos elásticos](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>¿Puedo cambiar una base de datos de un nivel de servicio en el modelo de compra basado en núcleo virtual a otro nivel de servicio en el modelo de compra basado en DTU?

Sí, puede convertir fácilmente su base de datos a cualquier objetivo de rendimiento compatible mediante Azure Portal, PowerShell, la CLI de Azure, T-SQL o la API REST. Consulte [Administración de bases de datos únicas](sql-database-single-database-scale.md) y [Administración de grupos elásticos](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el modelo de compra basado en núcleo virtual, consulte [Modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md).
- Para más información sobre el modelo de compra basado en DTU, consulte [Modelo de compra basado en DTU](sql-database-service-tiers-dtu.md).
