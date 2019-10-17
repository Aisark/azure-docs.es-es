---
title: Administración del uso y los costos de los registros de Azure Monitor | Microsoft Docs
description: Aprenda cómo cambiar el plan de precios y cómo administrar el volumen de datos y la directiva de retención para el área de trabajo de Log Analytics en Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: e1875ebdb62cfc6d606465b863215513aaa47c02
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972902"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Administrar el uso y los costos con los registros de Azure Monitor

> [!NOTE]
> En este artículo se describe cómo entender y controlar los costos de los registros de Azure Monitor. En un artículo relacionado, [Supervisión del uso y costos estimados](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs), se describe cómo ver el uso y los costos estimados mediante varias características de supervisión de Azure para los distintos modelos de precios.

Los registros de Azure Monitor están diseñados para escalar y admitir la recopilación, indexación y almacenamiento de grandes cantidades de datos por día provenientes de cualquier origen dentro de su empresa o implementados en Azure.  Aunque esto puede ser un factor clave principal para su organización, la rentabilidad es en última instancia el factor clave subyacente. Con este fin, es importante comprender que el costo de un área de trabajo de Log Analytics no se basa simplemente en el volumen de datos recopilados; también depende del plan seleccionado y de cuánto tiempo se decida almacenar los datos generados a partir de los orígenes conectados.  

En este artículo se revisa cómo supervisar de forma activa el crecimiento del volumen y el almacenamiento de los datos ingeridos, y definir los límites para controlar esos costos asociados. 

## <a name="pricing-model"></a>Modelo de precios

Los precios predeterminados de Log Analytics son de un modelo de **Pago por uso**, que se basa en el volumen de datos ingeridos y, opcionalmente, permite obtener una mayor retención de datos. Cada área de trabajo de Log Analytics se cobra como un servicio independiente y contribuye a la factura de la suscripción a Azure. La cantidad de ingesta de datos puede ser considerable dependiendo de los factores siguientes: 

  - Número de soluciones de administración habilitadas y su configuración (por ejemplo, 
  - Número de máquinas virtuales supervisadas
  - Tipo de datos recopilados de cada máquina virtual supervisada 
  
Además del modelo de pago por uso, se han incorporado **Reservas de capacidad** para Log Analytics que le permiten ahorrar hasta un 25 % en comparación con el precio de pago por uso. Los precios de la reserva de capacidad permiten comprar una reserva a partir de 100 GB/día. Cualquier uso por encima del nivel de reserva se facturará según la tarifa de pago por uso. [Más información](https://azure.microsoft.com/pricing/details/monitor/) sobre los precios de Pago por uso y de Reserva de capacidad de Log Analytics. 

Tenga en cuenta que algunas soluciones, como [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) y [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), tienen su propio modelo de precios. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Estimación de los costos de administración del entorno 

Si aún no usa los registros de Azure Monitor, puede usar la [calculadora de precios de Azure Monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) para calcular el costo del uso de Log Analytics. Comience por escribir "Azure Monitor" en el cuadro de búsqueda y haga clic en el icono de Azure Monitor resultante. Desplácese hacia abajo en la página hasta Azure Monitor y seleccione Log Analytics en la lista desplegable Tipo.  Aquí puede especificar el número de máquinas virtuales y los GB de datos que espera recopilar de cada máquina virtual. Normalmente se ingieren de 1 a 3 GB de datos al mes en una máquina virtual típica de Azure. Si ya está evaluando los registros de Azure Monitor, puede usar las estadísticas de datos de su propio entorno. Consulte a continuación cómo determinar el [número de máquinas virtuales supervisadas](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) y el [volumen de los datos que ingiere el área de trabajo](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Información útil del uso y los costos estimados

Si ya usa los registros de Azure Monitor ahora, es fácil comprender cuáles serán probablemente los costos según los patrones de uso recientes. Para ello, utilice **Uso y costos estimados de Log Analytics** a fin de revisar y analizar el uso de datos. Muestra la cantidad de datos que recopila cada solución, la cantidad de datos que se retienen y una estimación de los costos según la cantidad de datos ingeridos y cualquier retención adicional más allá de la cantidad incluida.

![Uso y costos estimados](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Para explorar los datos más detalladamente, haga clic en el icono situado en el extremo superior derecho de cualquiera de los gráficos de la página **Uso y costos estimados**. Ahora puede trabajar con esta consulta para explorar más detalles sobre su uso.  

![Visualización de registros](media/manage-cost-storage/logs.png)

En la página **Uso y costos estimados** puede revisar el volumen de datos del mes. Esto incluye todos los datos recibidos y retenidos en el área de trabajo de Log Analytics.  Haga clic en **Detalles de uso** en la parte superior de la página para ver el panel de uso con información sobre las tendencias de volumen de datos por origen, equipos y oferta. Para ver y establecer un límite diario o para modificar el período de retención, haga clic en **Administración del volumen de datos**.
 
Los cargos de Log Analytics se agregarán a la factura de Azure. Puede consultar los detalles de su factura de Azure en la sección de facturación de Azure Portal o en el [Portal de facturación de Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualización del uso de Log Analytics en la factura de Azure 

Azure proporciona una gran cantidad de funcionalidades útiles en el centro [Azure Cost Management + Facturación](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json). Por ejemplo, la funcionalidad de "Análisis de costos" le permite ver los gastos de los recursos de Azure. La adición de un filtro por tipo de recurso (a microsoft.operationalinsights/área de trabajo de Log Analytics) le permitirá realizar un seguimiento de los gastos.

Puede obtener información sobre el uso mediante la [descarga del uso desde Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). En la hoja de cálculo descargada puede ver el uso por recurso de Azure (por ejemplo, área de trabajo de Log Analytics) al día. En esta hoja de cálculo de Excel, el uso de las áreas de trabajo de Log Analytics se puede encontrar filtrando, en primer lugar, la columna "Categoría de medición" para mostrar "Insights y Analytics" (utilizado por algunos planes de tarifa heredados) y "Log Analytics" y, a continuación, agregando un filtro en la columna "Id. de instancia", que es "contiene área de trabajo". El uso se muestra en la columna "Cantidad consumida" y la unidad de cada entrada se muestra en la columna "Unidad de medida".  Hay más detalles disponibles para ayudarle a [entender la factura de Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="manage-your-maximum-daily-data-volume"></a>Administración del volumen de datos diario máximo

Puede configurar un límite diario y limitar la ingesta diaria para el área de trabajo, pero tenga cuidado de establecer el límite diario como el objetivo al que llegar.  En caso contrario, perderá los datos para el resto del día, lo que puede afectar a otros servicios y soluciones de Azure cuya funcionalidad puede depender de la disponibilidad de datos actualizados en el área de trabajo.  Como resultado, esto afecta a la capacidad de observar y recibir alertas cuando cambian las condiciones de mantenimiento de los recursos que respaldan los servicios de TI.  El límite diario está pensado para usarse como una manera de administrar el aumento inesperado del volumen de datos de los recursos administrados y permanecer dentro de su límite, o simplemente cuando desee limitar cargos no planeados para el área de trabajo.  

Cuando se alcanza el límite diario, la recopilación de tipos de datos facturables se detiene durante el resto del día. Un mensaje emergente de advertencia aparece en la parte superior de la página del área de trabajo de Log Analytics seleccionada, y se envía un evento de operación para la tabla *Operación* en la categoría **LogManagement**. La recopilación de datos se reanuda después de que se restablezca el tiempo definido en *Daily limit will be set at* (El límite diario se establecerá en). Se recomienda definir una regla de alerta en función de este evento de operación que esté configurada para notificar cuando se ha alcanzado el límite diario de datos. 

> [!NOTE]
> El límite diario no detiene la recopilación de datos de Azure Security Center, excepto en el caso de las áreas de trabajo en las que Azure Security Center se instaló antes del 19 de junio de 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificación del límite diario de datos para definir

Revise el [uso de Log Analytics y costos estimados](usage-estimated-costs.md) para comprender la tendencia de ingesta de datos y cuál es el límite de volumen diario para definir. Se debe considerar con cuidado, ya que no podrá supervisar los recursos una vez que se alcance el límite. 

### <a name="set-the-daily-cap"></a>Establecer el límite diario

Los pasos siguientes describen cómo configurar un límite para administrar el volumen de datos que el área de trabajo de Log Analytics ingiere por día.  

1. En el área de trabajo seleccione **Usage and estimated costs** (Uso y costos estimados) en el panel izquierdo.
2. En la página **Usage and estimated costs** (Uso y costos estimados) del área de trabajo seleccionada, haga clic en **Administración del volumen de datos** en la parte superior de la página. 
3. El límite diario está en **OFF** (Desactivado) de manera predeterminada, haga clic en **ON** (Activado) para habilitarlo y luego establezca el límite de volumen de datos en GB/día.

    ![Configuración del límite de datos con Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alerta cuando se alcanza el límite diario

Aunque Azure Portal presenta una indicación visual cuando se alcanza el umbral de límite de datos, este comportamiento no tiene que alinearse necesariamente con la forma en la que el usuario administra los problemas de funcionamiento que requieren atención inmediata.  Para recibir una notificación de alerta, puede crear una nueva regla de alerta en Azure Monitor.  Para obtener más información, consulte [Cómo crear, ver y administrar alertas](alerts-metric.md).

Para comenzar, esta es la configuración recomendada para la alerta:

- Destino: seleccione el recurso de Log Analytics
- Criterios: 
   - Nombre de señal: Búsqueda de registros personalizada
   - Consulta de búsqueda: Operación | donde Detalle tiene "OverQuota"
   - Basado en: Número de resultados
   - Condición: Mayor que
   - Umbral: 0
   - Período: 5 (minutos)
   - Frecuencia: 5 (minutos)
- Nombre de regla de alertas: Límite de datos diario alcanzado
- Gravedad: advertencia (gravedad 1)

Una vez que se define la alerta y se alcanza el límite, se desencadena una alerta que realiza la respuesta que se define en el grupo de acciones. Puede notificar a su equipo a través de mensajes de correo electrónico y de texto, o automatizar acciones mediante webhooks, runbooks de Automation o [integrar con una solución de ITSM externa](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Cambio del período de retención de datos

Los pasos siguientes describen cómo configurar cuánto tiempo se conservan los datos de registro en el área de trabajo.

### <a name="default-retention"></a>Retención predeterminada

Para establecer la retención predeterminada del área de trabajo, 
 
1. En Azure Portal, en el área de trabajo, seleccione **Uso y costos estimados** en el panel izquierdo.
2. En la página **Usage and estimated costs** (Uso y costos estimados), haga clic en **Administración del volumen de datos** en la parte superior de la página.
3. En el panel, mueva el control deslizante para aumentar o disminuir el número de días y, luego, haga clic en **Aceptar**.  Si el nivel es *gratuito*, no podrá modificar el período de retención de datos y tendrá que actualizar al nivel de pago para controlar esta configuración.

    ![Cambio de la configuración de retención de datos del área de trabajo](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
La retención también se puede configurar [a través de ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) mediante el parámetro `retentionInDays`. Además, si configura la retención de datos en 30 días, puede desencadenar una purga inmediata de los datos más antiguos mediante el parámetro `immediatePurgeDataOn30Days`, que puede serle útil en los escenarios relacionados con el cumplimiento. Esta funcionalidad solo está expuesta vía ARM. 

Dos tipos de datos (`Usage` y `AzureActivity`) se conservan durante 90 días de forma predeterminada y no se aplica ningún cargo por esta retención de 90 días. Estos tipos de datos también están libres de los cargos de ingesta de datos. 

### <a name="retention-by-data-type"></a>Retención por tipo de datos

También es posible especificar diferentes valores de retención para tipos de datos individuales. Cada tipo de datos es un subrecurso del área de trabajo. Por ejemplo, la tabla SecurityEvent se puede tratar en [Azure Resource Manager (ARM) ](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) como:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Tenga en cuenta que el tipo de datos (tabla) distingue mayúsculas de minúsculas.  Para obtener la configuración actual de retención por tipo de datos de un tipo de datos determinado (en este ejemplo, SecurityEvent), use:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Para obtener la configuración actual de retención por tipo de datos para todos los tipos de datos del área de trabajo, solo tiene que omitir el tipo de datos específico, por ejemplo:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Para establecer la retención de un tipo de datos determinado (en este ejemplo, SecurityEvent) en 730 días, haga

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Los tipos de datos `Usage` y `AzureActivity` no se pueden establecer con retención personalizada. Adoptarán el máximo de la retención predeterminada del área de trabajo o 90 días. 

Una herramienta excelente para conectarse directamente a ARM para establecer la retención por tipo de datos es la herramienta OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Más información sobre ARMclient en los artículos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) y [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  A continuación se muestra un ejemplo de uso de ARMClient, el establecimiento de los datos de SecurityEvent con una retención de 730 días:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> El establecimiento de la retención de tipos de datos individuales se puede usar para reducir los costos de la retención de datos.  Para los datos recopilados a partir de octubre de 2019 (fecha de publicación de esta característica), la reducción de la retención para algunos tipos de datos puede reducir el costo de retención con el tiempo.  Para los datos recopilados anteriormente, el establecimiento de una retención más baja para un tipo individual no afectará a los costos de retención.  

## <a name="legacy-pricing-tiers"></a>Planes de tarifa heredados

Las suscripciones que contenían un área de trabajo de Log Analytics o un recurso de Application Insights antes del 2 de abril de 2018, o que están vinculadas a un Contrato Enterprise que comenzó antes del 1 de febrero de 2019, continuarán teniendo acceso a los planes de tarifa heredados: **Gratuito**,**Independiente (por GB)** y **Por nodo (OMS)** .  Las áreas de trabajo en el plan de tarifa gratuito tendrán una ingesta diaria de datos limitada a 500 MB (excepto los tipos de datos de seguridad que recopile Azure Security Center) y la retención de datos se limitará a 7 días. El plan de tarifa gratuito está destinado solo para fines de evaluación. Las áreas de trabajo en los planes de tarifa Independientes o Por Nodo tienen una retención configurable para el usuario de hasta 2 años. 

Las áreas de trabajo creadas antes de abril de 2016 también tienen acceso a los planes de tarifa **Estándar** y **Premium** originales, que tienen una retención de datos fija de 30 y 365 días, respectivamente. No se pueden crear áreas de trabajo en los planes de tarifa **Estándar** o **Premium**, y si un área de trabajo se saca de estos niveles, no puede regresar a ellos. 

Puede obtener más detalles sobre las limitaciones del plan de tarifa [aquí](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Para usar los derechos que proceden de la adquisición de OMS E1 Suite, OMS E2 Suite o un complemento de OMS para System Center, elija el plan de tarifa *Por nodo* de Log Analytics.


## <a name="changing-pricing-tier"></a>Actualización del plan de tarifa

Si el área de trabajo de Log Analytics tiene acceso a los planes de tarifa heredados, siga los pasos a continuación para cambiar entre ellos:

1. En Azure Portal, en el panel de suscripciones de Log Analytics, seleccione un área de trabajo.

2. En el panel de área de trabajo, en **General**, seleccione **Plan de tarifa**.  

3. En **Plan de tarifa**, seleccione un plan y haga clic en **Seleccionar**.  
    ![Plan de precios seleccionado](media/manage-cost-storage/workspace-pricing-tier-info.png)

También puede [establecer el plan de tarifa mediante ARM](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) con el parámetro `sku` (`pricingTier` en la plantilla de ARM). 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Solucionar que Log Analytics ya no recopile datos

Si tiene el plan de tarifa gratuito heredado y ha enviado más de 500 MB de datos en un día, la recopilación de datos se detiene durante el resto del día. Alcanzar el límite diario es un motivo frecuente de que Log Analytics deje de recopilar datos o de que parezca que faltan datos.  Log Analytics crea un evento de tipo Operación cuando la recopilación de datos se inicia y se detiene. Ejecute la siguiente consulta en la búsqueda para comprobar si ha alcanzado el límite diario y faltan datos: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Cuando se detiene la recopilación de datos, el valor de OperationStatus es **Advertencia**. Cuando se inicia la recopilación de datos, el valor de OperationStatus es **Correcto**. La tabla siguiente describe los motivos por los que se detiene la recopilación de datos y una acción recomendada para reanudarla:  

|Motivo por el que se detiene la recopilación| Solución| 
|-----------------------|---------|
|Se alcanzó el límite diario del plan de tarifa Gratis heredado |Espere hasta el día siguiente para que la recopilación se reinicie automáticamente, o cambie a un plan de tarifa de pago.|
|Se alcanzó el límite diario del área de trabajo|Espere para que se reinicie automáticamente la recopilación o aumente el límite diario de volumen de datos que se describe en Administración del volumen de datos diario máximo El tiempo de restablecimiento de límite diario se muestra en la página **Administración del volumen de datos**. |
|La suscripción de Azure está en estado suspendido debido a:<br> Prueba gratuita finalizada<br> Pase para Azure expirado<br> Se ha alcanzado el límite de gasto mensual (por ejemplo, en una suscripción de MSDN o Visual Studio)|Cambie a una suscripción de pago<br> Quite el límite o espere a que se restablezca|

Para recibir una notificación cuando se detenga la recopilación de datos, siga los pasos descritos en la alerta *Crear límite de datos diario* para recibir una notificación cuando se detenga la recopilación de datos. Siga los pasos descritos en [Crear un grupo de acciones](action-groups.md) para configurar una acción de correo electrónico, de webhook o de runbook para la regla de alerta. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Solución del problema de un uso mayor de lo esperado

Un mayor uso está provocado por una de estas causas o por ambas:
- Más nodos de lo previsto que envían datos al área de trabajo de Log Analytics.
- Más datos de lo esperado enviados al área de trabajo de Log Analytics.

## <a name="understanding-nodes-sending-data"></a>Descripción de nodos que envían datos

Para comprender el número de equipos que notificaron latidos todos los días del último mes, use lo siguiente:

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Para obtener una lista de equipos que se facturarán como nodos si el área de trabajo se encuentra en el plan de tarifa heredado de cada nodo, busque los nodos que envían **tipos de datos facturados** (algunos tipos de datos son gratuitos). Para hacer esto, use la [propiedad](log-standard-properties.md#_isbillable) `_IsBillable` y el campo a la izquierda del nombre de dominio completo. Esto le devolverá la lista de equipos con datos facturados:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

El recuento de nodos facturables vistos puede estimarse como: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Use estas consultas `union withsource = tt *` con moderación, ya que la ejecución de exámenes entre tipos de datos es costosa. Esta consulta reemplaza la forma antigua de consultar información en función del equipo con el tipo de datos de uso.  

Un cálculo más preciso de lo que realmente se facturará es obtener el número de equipos por hora que envían los tipos de datos facturados. (Para las áreas de trabajo del plan de tarifa heredado Por nodo, Log Analytics calcula el número de nodos que deben facturarse por hora). 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Descripción del volumen de datos ingeridos

En la página **Uso y costos estimados**, el gráfico *Ingesta de datos por solución*  muestra el volumen total de los datos enviados y la cantidad que envía cada solución. Esto le permite determinar tendencias tales como si el uso global de los datos (o el uso de una solución en particular) crece, permanece constante o disminuye. La consulta que se utiliza para generar esto es:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

Tenga en cuenta que la cláusula "where IsBillable = true" filtra los tipos de datos de determinadas soluciones para las que no hay ningún cargo de ingesta. 

Puede profundizar más para ver las tendencias de datos para tipos de datos específicos; por ejemplo, si desea estudiar los datos debidos a los registros de IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Volumen de datos por equipo

Para ver el **tamaño** de los eventos facturables que ingirió el equipo, use la [propiedad](log-standard-properties.md#_billedsize) `_BilledSize`, que proporciona el tamaño en bytes:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

La [propiedad](log-standard-properties.md#_isbillable) `_IsBillable` especifica si los datos ingeridos incurrirán en cargos.

Para ver el recuento de eventos **facturables** que ingirió equipo, use: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Si quiere ver recuentos de tipos de datos facturables que envían datos a un equipo específico, use:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volumen de datos por recurso de Azure, grupo de recursos o suscripción

Para los datos de los nodos hospedados en Azure, puede obtener el **tamaño** de los eventos facturables que haya ingerido __el equipo__; para ello, use la [propiedad](log-standard-properties.md#_resourceid) _ResourceId, que proporciona la ruta completa al recurso:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Para los datos de los nodos hospedados en Azure, puede obtener el **tamaño** de los eventos facturables que haya ingerido __la suscripción de Azure__; para ello, analice la propiedad `_ResourceId` como:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

El cambio de `subscriptionId` a `resourceGroup` mostrará el volumen de datos ingeridos facturable en función del grupo de recursos de Azure. 


> [!NOTE]
> Algunos de los campos del tipo de datos de uso, aunque siguen en el esquema, han quedado en desuso y ya no se rellenarán sus valores. Estos son **Computer**, además de los campos relacionados con la ingesta (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** y **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Consulta de los tipos de datos comunes

Para profundizar en el origen de datos de un tipo de datos concreto, estas son algunas consultas de ejemplo útiles:

+ Solución **Security**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Solución **Log Management**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo de datos **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo de datos **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo de datos **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo de datos de **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Sugerencias para reducir el volumen de datos

Algunas sugerencias para reducir el volumen de registros recopilados incluyen:

| Origen del mayor volumen de datos | Cómo reducir el volumen de datos |
| -------------------------- | ------------------------- |
| Eventos de seguridad            | Seleccione los [eventos de seguridad común o mínima](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Cambie la directiva de auditoría de seguridad para recopilar únicamente los eventos necesarios. En particular, revise la necesidad de recopilar eventos para <br> - [auditar plataforma de filtrado](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [auditar registro](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [auditar sistema de archivos](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [auditar objeto de kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [auditar manipulación de identificadores](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - auditar almacenamiento extraíble |
| contadores de rendimiento       | Cambie la [configuración de los contadores de rendimiento](data-sources-performance-counters.md) para: <br> - Reducir la frecuencia de la colección <br> - Reducir el número de contadores de rendimiento |
| Registros de eventos                 | Cambie la [configuración del registro de eventos](data-sources-windows-events.md) para: <br> - Reducir el número de registros de eventos recopilados <br> - Recopilar solo los niveles de eventos necesarios Por ejemplo, no recopile eventos de nivel de *información*. |
| syslog                     | Cambie la [configuración de syslog](data-sources-syslog.md) para: <br> - Reducir el número de instalaciones recopiladas <br> - Recopilar solo los niveles de eventos necesarios Por ejemplo, no recopile eventos de nivel de *información* y *depuración*. |
| AzureDiagnostics           | Cambie la colección de registros de recursos para: <br> - Reducir el número de registros de recursos enviados a Log Analytics <br> - Recopilar solo los registros necesarios |
| Datos de la solución procedentes de equipos que no necesitan la solución | Use la [selección de destino de solución](../insights/solution-targeting.md) para recopilar datos solo de los grupos de equipos necesarios. |

### <a name="getting-security-and-automation-node-counts"></a>Obtener recuentos de nodos de seguridad y automatización

Si se encuentra en el plan de tarifa "Por nodo (OMS)", se le cobrará en función del número de nodos y soluciones que utilice y el número de nodos de Insight and Analytics por los que se le factura se mostrará en la tabla de la página **Uso y costos estimados**.  

Para ver el número de nodos de seguridad distintos, puede utilizar la consulta:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Para ver el número de nodos de Automation distintos, utilice la consulta:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="create-an-alert-when-data-collection-is-high"></a>Creación de una alerta cuando la colección de datos es mayor

En esta sección se describe cómo crear una alerta si:
- El volumen de datos supera una cantidad especificada.
- Se prevé que el volumen de datos supere un importe especificado.

Alertas de Azure admite [alertas de registro](alerts-unified-log.md) que usen consultas de búsqueda. 

La consulta siguiente produce un resultado cuando hay más de 100 GB de datos recopilados en las últimas 24 horas:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type 
| where DataGB > 100
```

La consulta siguiente utiliza una fórmula simple para predecir cuándo se enviarán más de 100 GB de datos en un día: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type 
| where EstimatedGB > 100
```

Para generar una alerta en un volumen de datos diferente, cambie el 100 de las consultas por el número de GB sobre el que desea alertar.

Siga los pasos explicados en [crear una nueva regla de alerta](alerts-metric.md) para recibir una notificación cuando la colección de datos sea mayor de lo previsto.

Al crear la alerta en la primera consulta; cuando hay más de 100 GB de datos en 24 horas, establezca los siguientes valores:  

- **Definición de la condición de alerta**: especifique el área de trabajo de Log Analytics como el destino del recurso.
- **Criterios de alerta** especifique lo siguiente:
   - **Nombre de señal**: seleccione **Custom log search** (Búsqueda de registros personalizada)
   - **Consulta de búsqueda** en `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Lógica de alerta** está **basada en** *número de resultados* y **Condición** es *Mayor que* un **umbral**  de *0*
   - **Período de tiempo** de *1440* minutos y **Frecuencia de la alerta** cada *60* minutos ya que los datos de uso solo se actualizan una vez por hora.
- **Definición de los detalles de la alerta**: especifique lo siguiente:
   - **Nombre** en *Volumen de datos mayor que 100 GB en 24 horas*
   - **Gravedad** en *Advertencia*

Especifique un [grupo de acciones](action-groups.md) existente o cree uno nuevo para que cuando la alerta de registro coincida con criterios, se le notifique.

Al crear la alerta para la segunda consulta; cuando se prevé que va a haber más de 100 GB de datos en 24 horas, establezca los siguientes valores:

- **Definición de la condición de alerta**: especifique el área de trabajo de Log Analytics como el destino del recurso.
- **Criterios de alerta** especifique lo siguiente:
   - **Nombre de señal**: seleccione **Custom log search** (Búsqueda de registros personalizada)
   - **Consulta de búsqueda** en `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Lógica de alerta** está **basada en** *número de resultados* y **Condición** es *Mayor que* un **umbral**  de *0*
   - **Período de tiempo** de *180* minutos y **Frecuencia de la alerta** cada *60* minutos ya que los datos de uso solo se actualizan una vez por hora.
- **Definición de los detalles de la alerta**: especifique lo siguiente:
   - **Nombre** en *Volumen de datos que se espera que sea mayor que 100 GB en 24 horas*
   - **Gravedad** en *Advertencia*

Especifique un [grupo de acciones](action-groups.md) existente o cree uno nuevo para que cuando la alerta de registro coincida con criterios, se le notifique.

Cuando se recibe una alerta, siga los pasos de la sección siguiente para solucionar el problema del uso mayor de lo esperado.

## <a name="data-transfer-charges-using-log-analytics"></a>Cargos por transferencia de datos mediante Log Analytics

Al enviar datos a Log Analytics se pueden aplicar ciertos cargos debido al ancho de banda de datos. Tal como se describe en la [página de precios de Azure Bandwidth](https://azure.microsoft.com/pricing/details/bandwidth/), la transferencia de datos entre los servicios de Azure ubicados en dos regiones se cobra como transferencia de datos salientes a precio normal. La transferencia de datos entrantes es gratuita. Sin embargo, este cargo es muy pequeño (un tanto por ciento mínimo) en comparación con los costos de la ingesta de datos de Log Analytics. En consecuencia, para controlar los costos de Log Analytics, debe centrarse en el volumen de datos ingeridos; para ello, le ofrecemos información [ aquí ](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   

## <a name="limits-summary"></a>Resumen de límites

Existen algunas limitaciones adicionales de Log Analytics, algunas de los cuales dependen del plan de tarifa de Log Analytics. Están documentadas [aquí](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Pasos siguientes

- Consulte [Log searches in Azure Monitor Logs](../log-query/log-query-overview.md) (Búsquedas de registros en el registro de Azure Monitor) para obtener información sobre cómo usar el lenguaje de búsqueda. Puede utilizar las consultas de búsqueda para realizar análisis adicionales sobre los datos de uso.
- Siga los pasos explicados en [Crear una nueva alerta de registro](alerts-metric.md) para recibir una notificación cuando se cumplan los criterios de búsqueda.
- Use la [selección de destino de solución](../insights/solution-targeting.md) para recopilar datos solo de los grupos de equipos necesarios.
- Para configurar una directiva eficaz de recopilación de eventos, revise la [Directiva de filtrado de Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Cambie la [configuración de los contadores de rendimiento](data-sources-performance-counters.md).
- Para modificar la configuración de recopilación de eventos, revise la [configuración de registros de eventos](data-sources-windows-events.md).
- Para modificar la configuración de la recopilación de syslog, revise la [configuración de syslog](data-sources-syslog.md).