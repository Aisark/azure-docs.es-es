---
title: Migración del almacenamiento de datos a Gen2
description: Instrucciones para migrar un almacenamiento de datos existente a Gen2 y la programación de migración por región.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anjangsh
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 01/21/2020
ms.custom: seo-lt-2019
ms.openlocfilehash: 3f793fd68c83f90b87182647eef47a07eb452f45
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314783"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Actualización del almacenamiento de datos a Gen2

Microsoft ayuda a reducir el coste del nivel de entrada de la ejecución de un almacén de datos.  Ahora, hay disponibles para Azure SQL Data Warehouse niveles inferiores de proceso capaces de manejar consultas exigentes. Lea el anuncio completo sobre la [compatibilidad de niveles inferiores de proceso para Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). La nueva oferta está disponible en las regiones que se indican en la tabla siguiente. Para las regiones admitidas, los almacenamientos de datos de Gen1 existentes se pueden actualizar a Gen2 a través de uno de los siguientes métodos:

- **El proceso de actualización automática:** las actualizaciones automáticas no se inician en cuanto el servicio está disponible en una región.  Cuando las actualizaciones automáticas se inician en una región específica, las actualizaciones individuales de almacenamiento de datos se llevarán a cabo durante la programación de mantenimiento seleccionada.
- [**Actualización manual a Gen2:** ](#self-upgrade-to-gen2) puede controlar cuándo se debe actualizar mediante una actualización manual a Gen2. Si aún no se admite su región, puede realizar una restauración desde un punto de restauración directamente a una instancia de Gen2 en una región admitida.

## <a name="automated-schedule-and-region-availability-table"></a>Programación automatizada y tabla de disponibilidad de regiones

En la tabla siguiente se resume por región cuándo estará disponible el nivel de proceso inferior Gen2 y en qué momento iniciar las actualizaciones automáticas. Las fechas están sujetos a cambios. Consúltela en otro momento para ver cuándo estará disponible su región.

\* indica que una programación específica para la región no está disponible actualmente.

| **Región** | **Proceso inferior Gen2 disponible** | **Fecha de inicio de la actualización automática** |
|:--- |:--- |:--- |
| Este de Canadá |1 de junio de 2020 |1 de julio de 2020 |
| Este de China |\* |\* |
| Norte de China |\* |\* |
| Centro de Alemania |\* |\* |
| Centro-oeste de Alemania |Disponible |1 de mayo de 2020 |
| India occidental |Disponible |1 de mayo de 2020  |

## <a name="automatic-upgrade-process"></a>Proceso de actualización automática

En función del gráfico de disponibilidad anterior, programaremos actualizaciones automatizadas para las instancias de Gen1. Con el fin de evitar interrupciones inesperadas de la disponibilidad del almacenamiento de datos, se programarán las actualizaciones automatizadas durante la programación de mantenimiento. La capacidad de crear una instancia de Gen1 se deshabilitará en las regiones en las que se está llevando a cabo la actualización automática a Gen2. Gen1 dejará de utilizarse una vez que se hayan completado las actualizaciones automáticas. Para obtener más información sobre las programaciones, consulte [Vista de una programación de mantenimiento](viewing-maintenance-schedule.md).

El proceso de actualización implicará una breve desconexión de aproximadamente 5 minutos, ya que tenemos que reiniciar el almacenamiento de datos.  Una vez que se ha reiniciado el almacenamiento de datos, estará totalmente disponible para usarse. Aun así, puede experimentar una degradación del rendimiento mientras continúa el proceso de actualización de los archivos de datos en segundo plano. El tiempo total de esta degradación del rendimiento variará en función del tamaño de los archivos de datos.

También puede acelerar el proceso de actualización de archivos de datos ejecutando [Alter Index rebuild](sql-data-warehouse-tables-index.md) en todas las tablas de almacén de columnas principales usando una clase de recurso y un SLO de mayor tamaño después del reinicio.

> [!NOTE]
> Alter Index rebuild es una operación sin conexión y las tablas no estarán disponibles hasta que se complete la recompilación.

## <a name="self-upgrade-to-gen2"></a>Actualización manual a Gen2

Puede optar por realizar la actualización manual. Para ello, siga estos pasos en un almacenamiento de datos existente de Gen1. Si elige esta opción, debe completar la actualización manual antes de que comience el proceso de actualización automática en su región. Esto asegura que se evite cualquier riesgo de que las actualizaciones automáticas causen un conflicto.

Hay dos opciones a la hora de realizar una actualización manual.  Se puede actualizar el almacenamiento de datos actual en contexto o restaurar un almacenamiento de datos de Gen1 en una instancia de Gen2.

- [Actualización en contexto](upgrade-to-latest-generation.md): esta opción actualizará el almacenamiento de datos existente de Gen1 a Gen2. El proceso de actualización implicará una breve desconexión de aproximadamente 5 minutos, ya que tenemos que reiniciar el almacenamiento de datos.  Una vez que se ha reiniciado el almacenamiento de datos, estará totalmente disponible para usarse. Si experimenta problemas durante la actualización, cree una [solicitud de soporte técnico](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) e indique que la posible causa es la "actualización a Gen2".
- [Actualización desde un punto de restauración](sql-data-warehouse-restore.md): cree un punto de restauración definido por el usuario en el almacenamiento de datos actual de Gen1 y, a continuación, restaure directamente a una instancia de Gen2. El almacenamiento de datos existente de Gen1 se mantendrá en el mismo sitio. Una vez completada la restauración, el almacenamiento de datos de Gen2 estará totalmente disponible para usarse.  Una vez que haya ejecutado todos los procesos de prueba y validación en la instancia de Gen2 restaurada, se podrá eliminar la instancia original de Gen1.

   - Paso 1: En Azure Portal, [cree un punto de restauración definido por el usuario](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal).
   - Paso 2: Al restaurar desde el punto de restauración definido por el usuario, establezca el nivel de rendimiento en el nivel de Gen2 que prefiera.

Puede experimentar un período de degradación del rendimiento mientras continúa el proceso de actualización de los archivos de datos en segundo plano. El tiempo total de esta degradación del rendimiento variará en función del tamaño de los archivos de datos.

Para acelerar el proceso en segundo plano de migración de los datos, puede forzar inmediatamente el movimiento de datos ejecutando [Alter Index rebuild](sql-data-warehouse-tables-index.md) en todas las tablas de almacén de columnas principales que consulte con una clase de recurso y un SLO de mayor tamaño.

> [!NOTE]
> Alter Index rebuild es una operación sin conexión y las tablas no estarán disponibles hasta que se complete la recompilación.

Si tiene problemas con el almacenamiento de datos, [cree una solicitud de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md) e indique que la posible causa es la "actualización a Gen2".

Para obtener más información, vea [Actualización a Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Preguntas frecuentes sobre migración

**P: ¿Gen2 cuesta lo mismo que Gen1?**

- A. Sí.

**P: ¿Cómo afectarán las actualizaciones a mis scripts de automatización?**

- A. Los scripts de automatización que hacen referencia a un objetivo de nivel de servicio deben cambiarse para que se correspondan con el equivalente de Gen2.  Consulte los detalles [aquí](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**P: ¿Cuánto suele tardar una actualización manual?**

- A. Puede actualizar en contexto o desde un punto de restauración.  
   - La actualización en contexto provocará que, momentáneamente, se pause y reanude el almacenamiento de datos.  Un proceso en segundo plano seguirá ejecutándose mientras el almacenamiento de datos está en línea.  
   - La actualización a través de un punto de restauración tarda más porque se realiza el proceso de restauración completa.

**P: ¿Cuánto tiempo tarda la actualización automática?**

- A. El tiempo de inactividad real de la actualización es solo el necesario para pausar y reanudar el servicio; es decir, entre 5 y 10 minutos. Tras este tiempo de inactividad breve, un proceso en segundo plano ejecutará una migración de almacenamiento. El período del proceso en segundo plano depende del tamaño del almacenamiento de datos.

**P: ¿Cuándo se llevará a cabo la actualización automática?**

- A. Durante una programación de mantenimiento. Aprovechar la programación de mantenimiento elegida minimizará la interrupción de su actividad empresarial.

**P: ¿Qué debo hacer si mi proceso de actualización en segundo plano parece estar bloqueado?**

 - A. Inicie una reindexación de las tablas de almacén de columnas. Tenga en cuenta que la reindexación de las tablas se realiza sin conexión durante esta operación.

**P: ¿Qué ocurre si Gen2 no tiene el objetivo de nivel de servicio necesario en Gen1?**
- A. Si usa DW600 o DW1200 en Gen1, se recomienda usar DW500c o DW1000c respectivamente, ya que Gen2 proporciona más cantidad de memoria, recursos y rendimiento que Gen1.

**P: ¿Puedo deshabilitar la copia de seguridad con redundancia geográfica?**
- A. No. La copia de seguridad con redundancia geográfica es una característica empresarial que mantiene la disponibilidad del almacenamiento de datos en caso de que una región deje de estar disponible. Abra una [solicitud de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md) si tiene más problemas.

**P: ¿Hay alguna diferencia de sintaxis de T-SQL entre Gen1 y Gen2?**

- A. No hay ningún cambio en la sintaxis del lenguaje T-SQL de Gen1 a Gen2.

**P: ¿Gen2 admite ventanas de mantenimiento?**

- A. Sí.

**P: ¿Podré crear una nueva instancia de Gen1 después de actualizar mi región?**

- A. No. Después de haber actualizado una región, se deshabilitará la creación de instancias de Gen1.

## <a name="next-steps"></a>Pasos siguientes

- [Pasos de actualización](upgrade-to-latest-generation.md)
- [Ventana de mantenimiento](maintenance-scheduling.md)
- [Supervisión del estado de los recursos](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Revisión de antes de comenzar la migración](upgrade-to-latest-generation.md#before-you-begin)
- [Actualización en contexto y desde un punto de restauración](upgrade-to-latest-generation.md)
- [Creación de un punto de restauración definido por el usuario](sql-data-warehouse-restore-points.md)
- [Información sobre cómo restaurar a Gen2](sql-data-warehouse-restore-active-paused-dw.md#restore-an-existing-data-warehouse-through-the-azure-portal)
- [Apertura de una solicitud de soporte técnico de SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
