---
title: Recopilar y analizar los registros de actividad de Azure en el área de trabajo de Log Analytics | Microsoft Docs
description: Puede usar la solución Registros de actividad de Azure para analizar y buscar en el registro de actividad de Azure de todas las suscripciones de Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: dbac4c73-0058-4191-a906-e59aca8e2ee0
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/11/2019
ms.author: magoedte
ms.openlocfilehash: 4476bb0a5a343fd43ce5ed70cf0e493d0ccae0e9
ms.sourcegitcommit: f24b62e352e0512dfa2897362021b42e0cb9549d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2019
ms.locfileid: "59505641"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Recopilar y analizar los registros de actividad de Azure en el área de trabajo de Log Analytics en Azure Monitor

![Símbolo de registros de actividad de Azure](./media/collect-activity-logs/activity-log-analytics.png)

La solución Activity Log Analytics ayuda a analizar y buscar en el [registro de actividad de Azure](activity-logs-overview.md) de todas las suscripciones de Azure. El registro de actividad de Azure es un registro que proporciona información sobre las operaciones realizadas en los recursos de las suscripciones. Antes el registro de actividad se conocía como *Registros de auditoría* o *Registros operativos*, ya que notifica eventos de las suscripciones.

Con el registro de actividad se pueden determinar los interrogantes *qué*, *quién* y *cuándo* de las operaciones de escritura (PUT, POST, DELETE) realizadas para los recursos de la suscripción. También permite entender el estado de las operaciones y otras propiedades relevantes. El registro de actividad no incluye las operaciones de lectura (GET) ni las operaciones para los recursos que usan el modelo de implementación clásica.

Cuando los registros de actividad de Azure se conexión a un área de trabajo de Log Analytics, puede:

- Analizar los registros de actividad con vistas predefinidas
- Analizar y buscar registros de actividad de varias suscripciones de Azure
- Conservar registros de actividad durante más de 90 días<sup>1</sup>
- Correlacionar registros de actividad con otros datos de plataforma y aplicación de Azure
- Ver las actividades operativas agregadas por estado
- Ver las tendencias de las actividades que se producen en cada uno de los servicios de Azure
- Informar sobre los cambios de autorización en todos los recursos de Azure
- Identificar problemas de corte de suministro o estado del servicio que afecten a los recursos
- Usar la búsqueda de registros para correlacionar las actividades del usuario, las operaciones de escalado automático, los cambios de autorización y el estado del servicio con otros registros o métricas del entorno

<sup>1</sup>de forma predeterminada, Azure Monitor mantiene los registros de actividad de Azure en un área de trabajo de Log Analytics durante 90 días, incluso si se encuentra en el nivel gratis. O bien, si tiene una configuración de retención de área de trabajo de menos de 90 días. Si el área de trabajo tiene una retención de más de 90 días, los registros de actividad se conservan según el período de retención del área de trabajo.

El área de trabajo de Log Analytics recopila registros de actividad de forma gratuita y los almacena durante 90 días de forma gratuita. Si almacena registros durante más de 90 días, incurrirá en gastos de retención de datos para los datos almacenados durante más de ese periodo.

Si está en el nivel de precios Gratis, los registros de actividad no se aplican al consumo de datos diario.

## <a name="connected-sources"></a>Orígenes conectados

A diferencia de la mayoría de soluciones de Azure Monitor, datos agentes no recopilan registros de actividad. Todos los datos usados por la solución proceden directamente de Azure.

| Origen conectado | Compatible | DESCRIPCIÓN |
| --- | --- | --- |
| [Agentes de Windows](agent-windows.md) | Sin  | La solución no recopila información de los agentes de Windows. |
| [Agentes de Linux](../learn/quick-collect-linux-computer.md) | Sin  | La solución no recopila información de los agentes de Linux. |
| [Grupo de administración de System Center Operations Manager](om-agents.md) | Sin  | La solución no recopila información de agentes que informan a un grupo de administración de Operations Manager. |
| [Cuenta de almacenamiento de Azure](collect-azure-metrics-logs.md) | Sin  | La solución no recopila información de Azure Storage. |

## <a name="prerequisites"></a>Requisitos previos

Para acceder a la información del registro de actividad de Azure, debe tener una suscripción de Azure.

La solución también requiere que se registran los siguientes dos proveedores de recursos en su suscripción:

1. Microsoft.OperationalInsights
2. Microsoft.OperationsManagement

Para obtener información sobre cómo registrarse o comprobar que están registradas, consulte [tipos y proveedores de recursos de Azure](../../azure-resource-manager/resource-manager-supported-services.md)

## <a name="configuration"></a>Configuración

Realice los pasos siguientes para configurar la solución Log Analytics para las áreas de trabajo.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

2. Habilite la solución Activity Log Analytics desde [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActivityOMS?tab=Overview) o mediante el proceso descrito en [Add Log Analytics solutions from the Solutions Gallery (Agregar soluciones Log Analytics desde la Galería de soluciones)](../insights/solutions.md).

2. Configure los registros de actividad para ir al área de trabajo de Log Analytics.
    1. En Azure Portal, seleccione el área de trabajo y haga clic en **Registro de actividad de Azure**.
    2. Haga clic en el nombre de cada suscripción.  
        
        ![agregar suscripción](./media/collect-activity-logs/add-subscription.png)
    
    3. En la hoja *SubscriptionName*, haga clic en **Conectar**.  
    
        ![Conectar suscripción](./media/collect-activity-logs/subscription-connect.png)

## <a name="using-the-solution"></a>Uso de la solución

Al agregar la solución Log Analytics al área de trabajo, el icono **Registros de actividad de Azure** se agrega al panel Información general. Este icono muestra un recuento del número de registros de actividad de Azure de las suscripciones de Azure a las que tiene acceso la solución.

![Icono Registros de actividad de Azure](./media/collect-activity-logs/azure-activity-logs-tile.png)

### <a name="view-azure-activity-logs"></a>Ver los registros de actividad de Azure

Haga clic en el icono **Registros de actividad de Azure** para abrir el panel **Registros de actividad de Azure**. El panel incluye las hojas de la tabla siguiente. Cada hoja muestra hasta diez elementos que coinciden con los criterios de esa hoja para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que devuelva todos los registros si hace clic en **Ver todo** en la parte inferior de la hoja o si hace clic en el encabezado de esta.

Los datos del registro de actividad solo aparecen *después* de haber configurado los registros de actividad para ir a la solución, así que antes de eso no se pueden ver.

| Hoja | DESCRIPCIÓN |
| --- | --- |
| Entradas del registro de actividad de Azure | Muestra un gráfico de barras de los principales totales de registro de entrada de registro de actividad del intervalo de fechas seleccionado y una lista de los 10 principales llamadores de actividad. Haga clic en el gráfico de barras para ejecutar una búsqueda de registros de <code>AzureActivity</code>. Haga clic en un elemento de llamador para ejecutar una búsqueda de registros que devuelva todas las entradas de registro de actividad de ese elemento. |
| Registros de actividad por estado | Muestra un gráfico de anillos del estado de registro de actividad de Azure para el intervalo de fechas seleccionado. También muestra una lista de los diez principales registros de estado. Haga clic en el gráfico para ejecutar una búsqueda de registros de <code>AzureActivity &#124; summarize AggregatedValue = count() by ActivityStatus</code>. Haga clic en un elemento de estado para ejecutar una búsqueda de registros que devuelva todas las entradas de registro de actividad de ese registro de estado. |
| Registros de actividad por recurso | Muestra el número total de recursos con registros de actividad y enumera los diez principales recursos con recuentos de registro para cada recurso. Haga clic en el área total para ejecutar una búsqueda de registros de <code>AzureActivity &#124; summarize AggregatedValue = count() by Resource</code>, que muestra todos los recursos de Azure disponibles para la solución. Haga clic en un recurso para ejecutar una búsqueda de registros que devuelva todos los registros de actividad de ese recurso. |
| Registros de actividad por proveedor de recursos | Muestra el número total de proveedores de recursos que producen registros de actividad y enumera los diez principales. Haga clic en el área total para ejecutar una búsqueda de registros de <code>AzureActivity &#124; summarize AggregatedValue = count() by ResourceProvider</code>, que muestra todos los proveedores de recursos de Azure. Haga clic en un proveedor de recursos para ejecutar una búsqueda de registros que devuelva todos los registros de actividad para el proveedor. |

![Panel Registros de actividad de Azure](./media/collect-activity-logs/activity-log-dash.png)

## <a name="next-steps"></a>Pasos siguientes

- Cree una [alerta](../platform/alerts-metric.md) cuando se produzca una actividad determinada.
- Use [Búsqueda de registros](../log-query/log-query-overview.md) para ver información detallada de los registros de actividad.
