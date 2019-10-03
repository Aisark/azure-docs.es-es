---
title: Azure SQL Database sin servidor (versión preliminar)| Microsoft Docs
description: En este artículo se describe el nuevo nivel de proceso sin servidor y se compara con el nivel de proceso aprovisionado existente.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: moslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 04e3881b553f639bb2df948b6ad1154f86f1c9da
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/19/2019
ms.locfileid: "71123097"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database sin servidor (versión preliminar)

Azure SQL Database sin servidor (versión preliminar) es un nivel de proceso para las bases de datos únicas que se escala automáticamente según la demanda de carga de trabajo y factura según la cantidad de proceso que se usa por segundo. El nivel de proceso sin servidor también detiene automáticamente las bases de datos durante períodos inactivos cuando solo se factura el almacenamiento y reanuda automáticamente las bases de datos cuando finaliza la actividad.

## <a name="serverless-compute-tier"></a>Nivel de servicio de informática sin servidor

El nivel de proceso sin servidor para una base de datos única se parametriza mediante un intervalo de escalado automático de proceso y una demora de pausa automática.  La configuración de estos parámetros da forma a la experiencia de rendimiento de la base de datos y el coste de proceso.

![facturación sin servidor](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Configuración del rendimiento

- Los valores **mínimos de núcleos virtuales** y los valores **máximos de núcleos virtuales** son parámetros configurables que definen el intervalo de capacidad de proceso disponible para la base de datos. Los límites de memoria y E/S son proporcionales al intervalo de núcleos virtuales especificado.  
- La **demora de pausa automática** es un parámetro configurable que define el período de tiempo de que la base de datos debe estar inactiva antes de pausarse automáticamente. La base de datos se reanuda automáticamente cuando se produce el siguiente inicio de sesión u otra actividad.  Como alternativa, puede la pausa automática se puede deshabilitar.

### <a name="cost"></a>Coste

- El coste de una base de datos sin servidor es la suma del coste de proceso y el coste de almacenamiento.
- Cuando el uso de proceso es entre los límites mínimos y máximos configurados, el coste de proceso se basa en el núcleo virtual y la memoria utilizada.
- Cuando el uso de proceso está por debajo de los límites mínimos configurados, el coste de proceso se basa en los núcleos virtuales mínimos y la cantidad mínima de memoria configurada.
- Cuando se pausa la base de datos, el coste de proceso es cero y solo se incurre en costes de almacenamiento.
- El coste de almacenamiento se determina de la misma manera que el nivel de proceso aprovisionado.

Para más detalles sobre costes, consulte [Facturación](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Escenarios

Este nivel de proceso sin servidor ofrece una relación entre precio y rendimiento optimizada para bases de datos únicas con patrones de uso intermitentes e impredecibles, que pueden permitirse alguna demora en el calentamiento de los recursos de proceso después de períodos de inactividad. En cambio, el nivel de proceso aprovisionado ofrece una relación entre precio y rendimiento optimizada para bases de datos únicas o varias bases de datos en grupos elásticos con mayor uso medio que no pueden permitirse ninguna demora en el calentamiento de los recursos de proceso.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Escenarios adecuados para el proceso sin servidor

- Bases de datos con patrones de uso impredecibles e intermitentes, intercalados con períodos de inactividad y menor uso promedio de proceso a lo largo del tiempo.
- Bases de datos únicas en el nivel de proceso aprovisionado con frecuentes cambios de escala y clientes que prefieren delegar el cambio de escala del proceso en el servicio.
- Nuevas bases de datos únicas sin historial de uso donde el tamaño de proceso es difícil (o incluso imposible) de estimar antes de la implementación en SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Escenarios adecuados para el proceso aprovisionado

- Bases de datos únicas con patrones de uso más regular y predecible y mayor uso promedio de proceso a lo largo del tiempo.
- Bases de datos que no pueden tolerar compensaciones de rendimiento resultantes de recortes de memoria más frecuentes o de una demora en la reanudación automática desde un estado de pausa.
- Varias bases de datos con patrones de uso impredecibles e intermitentes que se pueden consolidar en grupos elásticos para una mejor optimización de la relación precio-rendimiento.

## <a name="comparison-with-provisioned-compute-tier"></a>Comparación con el nivel de proceso aprovisionado

La tabla siguiente resume las diferencias entre el nivel de proceso sin servidor y el nivel de proceso aprovisionado:

| | **Proceso sin servidor** | **Proceso aprovisionado** |
|:---|:---|:---|
|**Patrones de uso de bases de datos**| Uso intermitente e impredecible con menor uso promedio de proceso a lo largo del tiempo. |  Patrones de uso más regulares con mayor uso promedio de proceso a lo largo del tiempo, o varias bases de datos que usen grupos elásticos.|
| **Trabajo de administración del rendimiento** |Inferior|Superior|
|**Escalado de proceso**|Automático|Manual|
|**Capacidad de respuesta del proceso**|Menor después de períodos de inactividad|Inmediata|
|**Granularidad de facturación**|Por segundo|Por hora|

## <a name="purchasing-model-and-service-tier"></a>Modelo de compra y nivel de servicio

SQL Database sin servidor solo se admite actualmente en el nivel de uso general en hardware de generación 5 en el modelo de compra de núcleos virtuales.

## <a name="autoscaling"></a>Escalado automático

### <a name="scaling-responsiveness"></a>Escalado de la capacidad de respuesta

En general, las bases de datos sin servidor se ejecutan en una máquina con capacidad suficiente para satisfacer la demanda de recursos sin interrupciones para cualquier cantidad de proceso solicitada dentro de los límites establecidos por el valor máximo de núcleos virtuales. En ocasiones, se produce automáticamente un equilibrio de carga si la máquina no puede satisfacer la demanda de recursos en cuestión de minutos. Por ejemplo, si la demanda de recursos es de 4 núcleos virtuales, pero solo hay 2 disponibles, puede que se tarde unos minutos en equilibrar la carga antes de que se proporcionen los 4 núcleos virtuales. La base de datos permanece en línea durante el equilibrio de carga excepto durante un breve período al final de la operación cuando se deshabilitan las conexiones.

### <a name="memory-management"></a>Administración de memoria

La memoria para bases de datos sin servidor se reclama con mayor frecuencia que la de las bases de datos de proceso aprovisionadas. Este comportamiento es importante para controlar costos en el nivel de proceso sin servidor y puede afectar al rendimiento.

#### <a name="cache-reclamation"></a>Reclamación de memoria caché

A diferencia de las bases de datos de proceso aprovisionadas, la memoria de la caché de SQL se reclama desde una base de datos sin servidor cuando el uso de CPU o de la memoria caché es bajo.

- La utilización de la memoria caché se considera baja cuando el tamaño total de las entradas de caché más recientes está por debajo de un determinado umbral durante un período de tiempo.
- Cuando se desencadena la reclamación de la memoria caché, el tamaño de la caché de destino se reduce de forma incremental a una fracción del tamaño anterior y la reclamación solo continúa si el uso sigue siendo bajo.
- Cuando se produce la reclamación de memoria caché, la directiva para seleccionar las entradas de esta que se van a expulsar es la misma directiva de selección que para las bases de datos de proceso aprovisionadas cuando la presión de memoria es elevada.
- El tamaño de la memoria caché no se reduce nunca por debajo del límite de memoria mínimo definido por el número mínimo de núcleos virtuales cuyo valor se puede configurar.

En el caso de las bases de datos sin servidor y las de proceso aprovisionadas, se pueden expulsar entradas de la caché si se usa toda la memoria disponible.

#### <a name="cache-hydration"></a>Hidratación de la memoria caché

La memoria caché de SQL crece a medida que se capturan datos del disco de la misma manera y a la misma velocidad que para las bases de datos aprovisionadas. Cuando la base de datos está ocupada, la memoria caché puede crecer sin restricciones hasta el límite máximo de memoria.

## <a name="autopausing-and-autoresuming"></a>Pausa y reanudación automáticas

### <a name="autopausing"></a>Pausa automática

La pausa automática se desencadena si todas las condiciones siguientes se cumplen durante la demora de pausa automática:

- Número de sesiones = 0
- CPU = 0 para la carga de trabajo de usuario en ejecución en el grupo de usuarios

Hay disponible una opción para deshabilitar la pausa automática si se desea.

Las características siguientes no admiten la pausa automática.  Es decir, si se utiliza cualquiera de las siguientes características, la base de datos permanecerá en línea, independientemente de la duración de la inactividad de la base de datos:

- Replicación geográfica (replicación geográfica activa y grupos de conmutación por error automáticos).
- Retención de copia de seguridad a largo plazo (LTR).
- La base de datos de sincronización utilizada en la sincronización de datos SQL.  A diferencia de las bases de datos de sincronización, las bases de datos centrales y miembro admiten las pausas automáticas.
- La base de datos de trabajo utilizada en trabajos elásticos.

Se impide temporalmente la pausa automática durante la implementación de algunas actualizaciones de servicio que requieren que la base de datos esté en línea.  En tales casos, se vuelve a permitir la pausa automática una vez finalizada la actualización del servicio.

### <a name="autoresuming"></a>Reanudación automática

La reanudación automática se desencadena si se cumple cualquiera de las siguientes condiciones en cualquier momento:

|Característica|Desencadenamiento de reanudación automática|
|---|---|
|Autenticación y autorización|Inicio de sesión|
|Detección de amenazas|Habilitación o deshabilitación de la configuración de detección de amenazas en el nivel de base de datos o servidor.<br>Modificación de la configuración de detección de amenazas en el nivel de base de datos o servidor.|
|Detección y clasificación de datos|Adición, modificación, eliminación o visualización de las etiquetas de confidencialidad|
|Auditoría|Visualización de registros de auditoría<br>Actualización o visualización de la directiva de auditoría.|
|Enmascaramiento de datos|Adición, modificación, eliminación o visualización de reglas de enmascaramiento de datos|
|Cifrado de datos transparente|Visualización del estado de cifrado de datos transparente|
|Almacén de datos de consulta (rendimiento)|Modificación o visualización de la configuración del almacén de consulta|
|Ajuste automático|Aplicación y comprobación de recomendaciones de ajuste automático, como la indexación automática|
|Copia de base de datos|Creación de base de datos como copia.<br>Exportación a un archivo BACPAC.|
|Sincronización de datos SQL|Sincronización entre la base de datos central y las bases de datos miembro que se ejecutan según una programación configurable o bien de forma manual|
|Modificación de algunos metadatos de base de datos|Adición de nuevas etiquetas de base de datos.<br>Cambio del máximo de núcleos virtuales, el mínimo de núcleos virtuales y la demora de pausa automática.|
|SQL Server Management Studio (SSMS)|Al usar las versiones de SSMS anteriores a 18.1 y abrir una nueva ventana de consulta para cualquier base de datos en el servidor, se reanudará cualquier base de datos en pausa automática en el mismo servidor. Este comportamiento no se produce si se usa SSMS versión 18.1 o posterior.|

También se desencadena la reanudación automática durante la implementación de algunas actualizaciones de servicio que requieren que la base de datos esté en línea.

### <a name="connectivity"></a>Conectividad

Si una base de datos sin servidor está en pausa, la primera vez que se inicie sesión se reanudará la base de datos y se devolverá un error con el código 40613 que indica que la base de datos no está disponible. Una vez que se reanude la base de datos, será necesario intentar iniciar sesión de nuevo para establecer la conectividad. No es necesario modificar los clientes de la base de datos con lógica de reintento de conexión.

### <a name="latency"></a>Latencia

La latencia de la reanudación y la pausa automáticas de una base de datos sin servidor es, por lo general, de 1 minuto para la reanudación automática y de 1 a 10 minutos para la pausa automática.

## <a name="onboarding-into-serverless-compute-tier"></a>Incorporación en un nivel de proceso sin servidor

La creación de una nueva base de datos o el cambio de una base de datos existente a un nivel de proceso sin servidor siguen el mismo patrón que la creación de una nueva base de datos en el nivel de proceso aprovisionado y constan de los dos pasos siguientes.

1. Especifique el nombre del objetivo de servicio. El objetivo de servicio preceptúa el nivel de servicio, la generación de hardware y el máximo de núcleos virtuales. La siguiente tabla muestra las opciones de objetivo de servicio:

   |Nombre del objetivo de servicio|Nivel de servicio|Generación de hardware|Máximo de núcleos virtuales|
   |---|---|---|---|
   |GP_S_Gen5_1|Uso general|Gen5|1|
   |GP_S_Gen5_2|Uso general|Gen5|2|
   |GP_S_Gen5_4|Uso general|Gen5|4|
   |GP_S_Gen5_6|Uso general|Gen5|6|
   |GP_S_Gen5_8|Uso general|Gen5|8|
   |GP_S_Gen5_10|Uso general|Gen5|10|
   |GP_S_Gen5_12|Uso general|Gen5|12|
   |GP_S_Gen5_14|Uso general|Gen5|14|
   |GP_S_Gen5_16|Uso general|Gen5|16|

2. Opcionalmente, especifique el mínimo de núcleos virtuales y la demora de pausa automática para cambiar sus valores predeterminados. En la siguiente tabla se muestran los valores disponibles para estos parámetros.

   |Parámetro|Opciones de valores|Valor predeterminado|
   |---|---|---|---|
   |Número mínimo de núcleos virtuales|Cualquier valor entre {0,5, 1, 2, 4} que no supere el máximo de núcleos virtuales|0,5 núcleos virtuales|
   |Demora de pausa automática|Mínimos: 60 minutos (1 hora)<br>Máximo: 10 080 minutos (7 días)<br>Incrementos: 60 minutos<br>Deshabilitar pausa automática: -1|60 minutos|

> [!NOTE]
> Actualmente no es posible usar T-SQL para mover una base de datos existente al nivel de proceso sin servidor o cambiar su tamaño de proceso, pero esto sí puede realizarse mediante Azure Portal o PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Creación de una nueva base de datos en el nivel de proceso sin servidor 

#### <a name="use-azure-portal"></a>Usar Azure Portal

Consulte [Quickstart: Creación de una base de datos única en Azure SQL Database con Azure Portal](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Uso de PowerShell

En el siguiente ejemplo se crea una nueva base de datos en el nivel de proceso sin servidor.  En este ejemplo se especifica explícitamente el mínimo de núcleos virtuales, el máximo de núcleos virtuales y la demora de pausa automática.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Cambio de la base de datos del nivel de proceso aprovisionado al nivel de proceso sin servidor

#### <a name="use-powershell"></a>Uso de PowerShell

En el siguiente ejemplo se mueve una base de datos desde el nivel de proceso aprovisionado al nivel de proceso sin servidor. En este ejemplo se especifica explícitamente el mínimo de núcleos virtuales, el máximo de núcleos virtuales y la demora de pausa automática.

```powershell
Set-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Cambio de la base de datos del nivel de proceso sin servidor al nivel de proceso aprovisionado

Una base de datos sin servidor se puede mover a un nivel de proceso aprovisionado de la misma manera que se mueve una base de datos de proceso aprovisionado a un nivel de proceso sin servidor.

## <a name="modifying-serverless-configuration"></a>Modificación de la configuración sin servidor

### <a name="maximum-vcores"></a>Máximo de núcleos virtuales

#### <a name="use-powershell"></a>Uso de PowerShell

La modificación del número máximo de núcleos virtuales se realiza mediante el comando [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) en PowerShell con el argumento `MaxVcore`.

### <a name="minimum-vcores"></a>Mínimo de núcleos virtuales

#### <a name="use-powershell"></a>Uso de PowerShell

La modificación del número mínimo de núcleos virtuales se realiza mediante el comando [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) en PowerShell con el argumento `MinVcore`.

### <a name="autopause-delay"></a>Demora de pausa automática

#### <a name="use-powershell"></a>Uso de PowerShell

La modificación de la demora de la pausa automática se realiza mediante el comando [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) en PowerShell con el argumento `AutoPauseDelayInMinutes`.

## <a name="monitoring"></a>Supervisión

### <a name="resources-used-and-billed"></a>Recursos utilizados y facturados

Los recursos de una base de datos sin servidor se encapsulan por paquete de la aplicación, instancia de SQL y entidades de grupo de recursos del usuario.

#### <a name="app-package"></a>Paquete de aplicaciones

El paquete de aplicaciones es el límite de administración de recursos más externo de una base de datos, independientemente de si la base de datos se encuentra en un nivel de proceso sin servidor o aprovisionado. El paquete de aplicaciones contiene la instancia de SQL y los servicios externos que en conjunto abarcan todos los recursos de usuario y del sistema que utiliza una base de datos en SQL Database. Entre los ejemplos de servicios externos se incluyen R y la búsqueda de texto completo. Generalmente, la instancia de SQL domina el uso de recursos global en el paquete de aplicaciones.

#### <a name="user-resource-pool"></a>Grupo de recursos de usuario

El grupo de recursos de usuario es el límite de administración de recursos más interno de una base de datos, independientemente de si la base de datos se encuentra en un nivel de proceso sin servidor o aprovisionado. El grupo de recursos de usuario abarca la CPU y la E/S para cargas de trabajo de usuario generadas por consultas de DDL como CREATE y ALTER, y por consultas de DML como SELECT, INSERT, UPDATE y DELETE. Por lo general, estas consultas representan la proporción de uso dentro del paquete de aplicaciones más importante.

### <a name="metrics"></a>Métricas

Las métricas para supervisar el uso de recursos del paquete de la aplicación y grupo de usuarios de una base de datos sin servidor se muestran en la tabla siguiente:

|Entidad|Métrica|DESCRIPCIÓN|Unidades|
|---|---|---|---|
|Paquete de aplicaciones|app_cpu_percent|Porcentaje de núcleos virtuales utilizado por la aplicación con respecto al máximo de núcleos virtuales permitido para la aplicación.|Porcentaje|
|Paquete de aplicaciones|app_cpu_billed|La cantidad de procesos que se facturan para la aplicación durante el período de informe. El importe pagado durante este período es el producto de esta métrica por el precio de la unidad de núcleo virtual. <br><br>Los valores de esta métrica se determinan al agregar en el tiempo el máximo de CPU utilizada y la memoria usada por segundo. Si la cantidad utilizada es menor que la cantidad mínima aprovisionada definida por el mínimo de núcleos virtuales y la memoria mínima, se factura la cantidad mínima aprovisionada. Para comparar la CPU y la memoria con fines de facturación, la memoria se normaliza en unidades de núcleos virtuales cambiando la escala de la cantidad de GB de memoria en 3 GB por núcleo virtual.|Segundos de núcleo virtual|
|Paquete de aplicaciones|app_memory_percent|Porcentaje de memoria utilizada por la aplicación con respecto a la memoria máxima permitida para la aplicación.|Porcentaje|
|Grupo de usuarios|cpu_percent|Porcentaje de núcleos virtuales utilizado por la carga de trabajo de usuario con respecto al máximo de núcleos virtuales permitido para la carga de trabajo de usuario.|Porcentaje|
|Grupo de usuarios|data_IO_percent|Porcentaje de IOPS de datos utilizado por la carga de trabajo de usuario con respecto al máximo de IOPS de datos permitido para la carga de trabajo de usuario.|Porcentaje|
|Grupo de usuarios|log_IO_percent|Porcentaje de MB/s de registro utilizado por la carga de trabajo de usuario con respecto al máximo de MB/s de registro permitido para la carga de trabajo de usuario.|Porcentaje|
|Grupo de usuarios|workers_percent|Porcentaje de trabajos utilizado por la carga de trabajo de usuario con respecto al máximo de trabajos permitido para la carga de trabajo de usuario.|Porcentaje|
|Grupo de usuarios|sessions_percent|Porcentaje de sesiones utilizado por la carga de trabajo de usuario con respecto al máximo de sesiones permitido para la carga de trabajo de usuario.|Porcentaje|

### <a name="pause-and-resume-status"></a>Estado de pausa y reanudación

En Azure Portal, se muestra el estado de la base de datos en el panel de información general del servidor que enumera las bases de datos que contiene. El estado de la base de datos también se muestra en el panel de información general de la base de datos.

Con el siguiente comando de PowerShell puede consultar el estado de pausa y reanudación de una base de datos:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Límites de recursos

Para ver los límites de recursos, consulte [Nivel de proceso sin servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).

## <a name="billing"></a>Facturación

La cantidad de proceso que se factura es el máximo de CPU y memoria usado en cada segundo. Si la cantidad de CPU y memoria usadas es inferior a la cantidad mínima aprovisionada para cada una, se factura la cantidad aprovisionada. Para comparar la CPU y la memoria con fines de facturación, la memoria se normaliza en unidades de núcleos virtuales cambiando la escala de la cantidad de GB de memoria en 3 GB por núcleo virtual.

- **Recurso facturado**: CPU y memoria
- **Importe facturado**: precio de la unidad de núcleo virtual * máx. (mínimo de núcleos virtuales, núcleos virtuales usados, GB de memoria mínima * 1/3, GB de memoria usada * 1/3) 
- **Frecuencia de facturación**: Por segundo

El precio de unidad de núcleo virtual es el costo por núcleo virtual por segundo. Consulte la [página de precios de Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) para conocer los precios de unidad específicos de una región determinada.

La cantidad de proceso facturada se expone mediante la métrica siguiente:

- **Métrica**: app_cpu_billed (segundos de núcleo virtual)
- **Definición**: máx. (mínimo de núcleos virtuales, núcleos virtuales usados, GB de memoria mínima * 1/3, GB de memoria usada * 1/3)
- **Frecuencia de informes**: Por minuto

Esta cantidad se calcula cada segundo y se agrega en un intervalo de 1 minuto.

Considere la posibilidad de una base de datos sin servidor configurada con 1 núcleo virtual como mínimo y 4 como máximo.  Esto se corresponde a aproximadamente 3 GB de memoria como mínimo y 12 GB de memoria como máximo.  Supongamos que la demora de la pausa automática se establece en 6 horas y la carga de trabajo de la base de datos está activa durante las primeras 2 horas de un período de 24 horas e inactiva el resto del tiempo.    

En este caso, la base de datos se facturará por proceso y almacenamiento durante las primeras 8 horas.  Aunque la base de datos está inactiva después de la segunda hora, se le seguirá facturando por el proceso de las 6 horas siguientes en función del proceso mínimo aprovisionado mientras la base de datos está en línea.  Solo se facturará por el almacenamiento el resto del período de 24 horas mientras la base de datos está en pausa.

Más concretamente, la facturación del proceso en este ejemplo se calcula como sigue:

|Intervalo de tiempo|Núcleos virtuales que se usan cada segundo|GB que se usan cada segundo|Dimensión del proceso facturado|Segundos de núcleo virtual facturados a lo largo de un intervalo de tiempo|
|---|---|---|---|---|
|0:00-1:00|4|9|Núcleos virtuales usados|4 núcleos virtuales * 3600 segundos = 14 400 segundos de núcleo virtual|
|1:00-2:00|1|12|Memoria usada|12 GB * 1/3 * 3600 segundos = 14 400 segundos de núcleo virtual|
|2:00-8:00|0|0|Memoria mínima aprovisionada|3 GB * 1/3 * 21 600 segundos = 21 600 segundos de núcleo virtual|
|8:00-24:00|0|0|No se factura ningún proceso mientras la base de datos está en pausa|0 segundos de núcleo virtual|
|Número total de segundos de núcleo virtual facturados durante 24 horas||||50 400 segundos de núcleo virtual|

Suponga que el precio de la unidad de proceso es 0,000073 $/núcleo virtual/segundo.  El proceso que se factura en este período de 24 horas es igual al producto del precio por unidad de proceso por los segundos de núcleo virtual facturados: $0,000073/vCore/segundo * 50 400 segundos de núcleo virtual = $3,68

## <a name="available-regions"></a>Regiones disponibles

El nivel de proceso sin servidor está disponible en todas las regiones excepto las siguientes: Centro de Australia, Este de China, Norte de China, Sur de Francia, Centro de Alemania, Nordeste de Alemania, India occidental, Sur de Corea del Sur, Oeste de Sudáfrica, Norte de Reino Unido, Sur de Reino Unido, Oeste de Reino Unido y Centro-oeste de EE. UU.

## <a name="next-steps"></a>Pasos siguientes

- Para comenzar, consulte [Inicio rápido: Creación de una base de datos única en Azure SQL Database con Azure Portal](sql-database-single-database-get-started.md).
- Para ver los límites de recursos, consulte [Límites de recursos del nivel de proceso sin servidor](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).
