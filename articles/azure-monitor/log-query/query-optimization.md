---
title: Optimización de las consultas de registro en Azure Monitor
description: Procedimientos recomendados para optimizar las consultas de registros en Azure Monitor.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/25/2019
ms.openlocfilehash: 521fd84e79196439ea220bd7ffa7cc6d0750f045
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648842"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optimización de las consultas de registro en Azure Monitor
Los registros de Azure Monitor usan [Azure Data Explorer (ADX)](/azure/data-explorer/) para almacenar los datos de registro y ejecutar consultas para analizar los datos. Crea, administra y mantiene los clústeres de ADX automáticamente y los optimiza para la carga de trabajo de análisis de registros. Al ejecutar una consulta, se optimiza y se redirige al clúster de ADX adecuado que almacena los datos del área de trabajo. Tanto los registros de Azure Monitor como Azure Data Explorer usan muchos mecanismos de optimización de consultas automática. Aunque las optimizaciones automáticas proporcionan un aumento significativo, en algunos casos se puede mejorar drásticamente el rendimiento de las consultas. En este artículo se explican las consideraciones de rendimiento y varias técnicas para corregirlas.

La mayoría de las técnicas son comunes a las consultas que se ejecutan directamente en Azure Data Explorer y registros de Azure Monitor, aunque hay varias consideraciones únicas de registros de Azure Monitor que se describen aquí. Para obtener más información sobre las sugerencias de optimización de Azure Data Explorer, consulte [Procedimientos recomendados sobre las consultas](/azure/kusto/query/best-practices).

Las consultas optimizadas:

- Se ejecutarán más rápido y reducirán la duración total de la ejecución de la consulta.
- Tendrán menos posibilidades de que se limiten o rechacen.

Debe prestar especial atención a las consultas que se usan para el uso recurrente y de ráfaga, como paneles, alertas, Logic Apps y Power BI. El impacto de una consulta ineficaz en estos casos es considerable.

## <a name="query-performance-pane"></a>Panel de rendimiento de consultas
Después de ejecutar una consulta en Log Analytics, haga clic en la flecha hacia abajo que aparece sobre los resultados de la consulta para ver el panel rendimiento de consultas que muestra los resultados de varios indicadores de rendimiento para la consulta. Estos indicadores de rendimiento se describen en la sección siguiente.

![Panel de rendimiento de consultas](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Indicadores de rendimiento de consultas

Los siguientes indicadores de rendimiento de consultas están disponibles para todas las consultas que se ejecutan:

- [CPU total](#total-cpu): Proceso general usado para procesar la consulta en todos los nodos de proceso. Representa el tiempo que se usa para la computación, el análisis y la captura de datos. 

- [Datos usados para la consulta procesada](#data-used-for-processed-query): Datos globales a los que se obtuvo acceso para procesar la consulta. Se ve afectado por el tamaño de la tabla de destino, el intervalo de tiempo usado, los filtros aplicados y el número de columnas a que se hace referencia.

- [Intervalo de tiempo de la consulta procesada](#time-span-of-the-processed-query): La diferencia entre los datos más recientes y los más antiguos a los que se obtuvo acceso para procesar la consulta. Se ve afectado por el intervalo de tiempo explícito especificado para la consulta.

- [Antigüedad de los datos procesados](#age-of-processed-data): La diferencia entre los datos actuales y los más antiguos a los que se obtuvo acceso para procesar la consulta. Afecta enormemente a la eficacia de la captura de datos.

- [Número de áreas de trabajo](#number-of-workspaces): Número de áreas de trabajo a las que se obtuvo acceso durante el procesamiento de la consulta debido a una selección implícita o explícita.

- [Número de regiones](#number-of-regions): Número de regiones a las que se obtuvo acceso durante el procesamiento de la consulta debido a una selección implícita o explícita de áreas de trabajo. Las consultas de varias regiones son mucho menos eficientes y los indicadores de rendimiento presentan una cobertura parcial.

- [Paralelismo](#parallelism): Indica la cantidad de tiempo que el sistema pudo ejecutar esta consulta en varios nodos. Solo es relevante para las consultas que tienen un uso elevado de la CPU. Se ve afectado por el uso de funciones y operadores específicos.


## <a name="total-cpu"></a>CPU total
La CPU de cálculo real que se ha invertido para procesar esta consulta en todos los nodos de procesamiento de consultas. Como la mayoría de las consultas se ejecutan en un gran número de nodos, normalmente será mucho mayor que la duración de la ejecución de la consulta. 

El tiempo de procesamiento de consultas se emplea en:
- Recuperación de datos: la recuperación de datos antiguos consumirá más tiempo que la recuperación de datos recientes.
- Procesamiento de datos: lógica y evaluación de los datos. 

Aparte del tiempo dedicado a los nodos de procesamiento de consultas, hay un tiempo adicional que los registros de Azure Monitor invierten en: autenticar el usuario y comprobar que tiene permiso para acceder a estos datos, buscar el almacén de datos, analizar la consulta y asignar los nodos de procesamiento de consultas. Este tiempo no se incluye en el tiempo de CPU total de la consulta.

Algunos de los comandos de consulta y las funciones realizan un gran consumo de CPU. Esto es especialmente cierto para los comandos que analizan JSON y XML o extraen expresiones regulares complejas. Este análisis puede producirse explícitamente a través de las funciones [parse_json()](/azure/kusto/query/parsejsonfunction) o [parse_xml()](/azure/kusto/query/parse-xmlfunction) o implícitamente cuando se hace referencia a columnas dinámicas.

Estas funciones consumen CPU en proporción al número de filas que están procesando. La optimización más eficaz consiste en agregar inmediatamente condiciones where en la consulta que pueden filtrar tantos registros como sea posible antes de que se ejecute la función de uso intensivo de la CPU.

Por ejemplo, las siguientes consultas producen exactamente el mismo resultado, pero la segunda es mucho más eficaz, ya que la condición [where]() antes del análisis excluye muchos registros:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
```

Las consultas que contienen cláusulas [where](/azure/kusto/query/whereoperator) en una columna evaluada en lugar de en columnas que están físicamente presentes en el conjunto de datos pierden eficacia. El filtrado de columnas evaluadas evita algunas optimizaciones del sistema cuando se administran grandes conjuntos de datos.
Por ejemplo, las siguientes consultas producen exactamente el mismo resultado, pero la segunda es más eficaz, ya que la condición [where](/azure/kusto/query/whereoperator) hace referencia a la columna integrada.

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

Aunque algunos comandos de agregación, como [max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction) y [avg()](/azure/kusto/query/avg-aggfunction), tienen un impacto bajo en la CPU debido a su lógica, otros son más complejos e incluyen heurística y estimaciones que les permiten ejecutarse de forma eficaz. Por ejemplo, [dcount()](/azure/kusto/query/dcount-aggfunction) usa el algoritmo HyperLogLog para proporcionar una estimación próxima para distinguir el recuento de grandes conjuntos de datos sin contar realmente cada valor; las funciones de percentil realizan aproximaciones similares mediante el algoritmo de percentil de clasificación más próximo. Algunos de los comandos incluyen parámetros opcionales para reducir su impacto. Por ejemplo, la función [makeset()](/azure/kusto/query/makeset-aggfunction) tiene un parámetro opcional para definir el tamaño máximo del conjunto, lo que afecta significativamente a la CPU y la memoria.

Los comandos [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) y [summarize](/azure/kusto/query/summarizeoperator) pueden provocar un uso intensivo de la CPU cuando se procesa un gran conjunto de datos. Su complejidad está directamente relacionada con el número de valores posibles, denominado *cardinalidad*, de las columnas que usan, como `by` en summarize o como los atributos join. Para obtener una explicación y una optimización de join y resume, consulte sus artículos de documentación y sugerencias de optimización.

Por ejemplo, las siguientes consultas producen exactamente el mismo resultado, porque **CounterPath** siempre tiene una asignación uno a uno para **CounterName** y **ObjectName**. La segunda es más eficaz, ya que la dimensión de agregación es más pequeña:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

El consumo de CPU también puede verse afectado por condiciones where o columnas extendidas que necesitan una computación intensiva. Todas las comparaciones triviales de cadenas, como [equal ==](/azure/kusto/query/datatypes-string-operators) y [startswith](/azure/kusto/query/datatypes-string-operators), tienen prácticamente el mismo impacto en la CPU, mientras que las coincidencias de texto avanzadas tienen más impacto. En concreto, el operador [has](/azure/kusto/query/datatypes-string-operators) es más eficaz que el operador [contains](/azure/kusto/query/datatypes-string-operators). Debido a las técnicas de control de cadenas, es más eficaz buscar cadenas que tienen más de cuatro caracteres que cadenas cortas.

Por ejemplo, las siguientes consultas generan resultados similares en función de la directiva de nomenclatura de procesos, pero la segunda es más eficaz:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Este indicador solo presenta la CPU del clúster inmediato. En una consulta de varias regiones, solo representaría una de las regiones. En una consulta de varias áreas de trabajo, es posible que no incluya todas las áreas de trabajo.


## <a name="data-used-for-processed-query"></a>Datos usados para la consulta procesada

Un factor crítico en el procesamiento de la consulta es el volumen de datos que se digitaliza y usa para el procesamiento de la consulta. Azure Data Explorer usa optimizaciones agresivas que reducen en gran medida el volumen de datos en comparación con otras plataformas de datos. Aún así, hay factores críticos en la consulta que pueden afectar al volumen de datos que se usa.
En los registros de Azure Monitor, la columna **TimeGenerated** se usa para indexar los datos. Si los valores de **TimeGenerated** se restringen tanto como sea posible, se producirá una mejora significativa en el rendimiento de la consulta mediante una limitación considerable de la cantidad de datos que se tienen que procesar.

Otro factor que aumenta los datos que se procesan es el uso de un gran número de tablas. Esto suele ocurrir cuando se usan los comandos `search *` y `union *`. Estos comandos obligan al sistema a evaluar y digitalizar los datos de todas las tablas del área de trabajo. En algunos casos, puede haber cientos de tablas en el área de trabajo. Intente evitar en la medida de lo posible el uso de "buscar *" o las buscas sin ámbito en una tabla específica.

Por ejemplo, las siguientes consultas producen exactamente el mismo resultado, pero la última es, con diferencia, la más eficaz:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

Otro método para reducir el volumen de datos es tener condiciones [where](/azure/kusto/query/whereoperator) rápidamente en la consulta. La plataforma de Azure Data Explorer incluye una memoria caché que le permite saber qué particiones incluyen datos relevantes para una condición where específica. Por ejemplo, si un consulta contiene `where EventID == 4624`, distribuirá la consulta solo en los nodos que controlan particiones con eventos coincidentes.

Las siguientes consultas de ejemplo producen exactamente el mismo resultado, pero la última es mucho más eficaz:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

Dado que Azure Data Explorer es un almacén de datos en columnas, la recuperación de cada columna es independiente de las demás. El número de columnas que se recuperan influye directamente en el volumen de datos general. Solo se deben incluir las columnas de la salida que se necesitan para [resumir](/azure/kusto/query/summarizeoperator) los resultados o [proyectar](/azure/kusto/query/projectoperator) las columnas específicas. Azure Data Explorer tiene varias optimizaciones para reducir el número de columnas recuperadas. Si se determina que una columna no es necesaria, por ejemplo, si el comando [summarize](/azure/kusto/query/summarizeoperator) no hace referencia a ella, no se recuperará.

Por ejemplo, la segunda consulta puede procesar tres veces más datos, ya que necesita capturar no una columna, sino tres:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Intervalo de tiempo de la consulta procesada

Todos los registros de Azure Monitor se dividen en particiones según la columna **TimeGenerated**. El número de particiones a las que se obtiene acceso está directamente relacionado con el intervalo de tiempo. La forma más eficaz de garantizar la ejecución de una consulta del símbolo del sistema consiste en reducir el intervalo de tiempo.

El intervalo de tiempo puede establecerse con el selector de intervalo de tiempo en la pantalla de Log Analytics tal como se describe en [Ámbito e intervalo de tiempo de una consulta de registro en Log Analytics de Azure Monitor](scope.md#time-range). Se trata del método recomendado, ya que el intervalo de tiempo seleccionado se pasa al back-end mediante los metadatos de la consulta. 

Un método alternativo consiste en incluir explícitamente una condición [where](/azure/kusto/query/whereoperator) en **TimeGenerated** en la consulta. Se debería usar este método, ya que se asegura que el intervalo de tiempo sea fijo, aunque la consulta se use desde una interfaz diferente.
Debe asegurarse de que todas las partes de la consulta tengan filtros **TimeGenerated**. Cuando una consulta tiene subconsultas que capturan datos de diferentes tablas o de la misma, cada una tiene que incluir su propia condición [where](/azure/kusto/query/whereoperator).

Por ejemplo, en la siguiente consulta, mientras que la tabla **Perf** solo se digitalizará para el último día, la tabla **Heartbeat** se digitalizará para la totalidad de su historial, que puede ser de hasta dos años:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Un caso común en el que se produce dicho error es cuando [arg_max()](/azure/kusto/query/arg-max-aggfunction) se usa para buscar la repetición más reciente. Por ejemplo:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Se puede corregir fácilmente si se agrega un filtro de tiempo en la consulta interna:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

> [!IMPORTANT]
> Este indicador no está disponible para las consultas de varias regiones.

## <a name="age-of-processed-data"></a>Antigüedad de los datos procesados
Azure Data Explorer usa varias capas de almacenamiento: en memoria, discos SSD locales y blobs de Azure mucho más lentos. Cuanto más recientes sean los datos, más alta será la posibilidad de que se almacenen en una capa más eficiente y con menos latencia, lo que reduce el uso de la CPU y la duración de la consulta. Al margen de los propios datos, el sistema también tiene una memoria caché para los metadatos. Cuanto más antiguos sean los datos, menor será la probabilidad de que sus metadatos estén en la caché.

Mientras que algunas consultas requieren el uso de datos antiguos, hay casos en los que dichos datos se usan por error. Esto sucede cuando las consultas se ejecutan sin proporcionar el intervalo de tiempo en sus metadatos y no todas las referencias de tabla incluyen filtros en la columna **TimeGenerated**. En estos casos, el sistema digitalizará todos los datos que estén almacenados en esa tabla. Cuando la retención de datos es larga, puede abarcar intervalos de tiempo largos y, por lo tanto, aquellos datos que son tan antiguos como el período de retención de datos.

A continuación, se indican algunos ejemplos de dichos casos:

- No establecer el intervalo de tiempo en Log Analytics con una subconsulta que no esté limitada. Vea el ejemplo anterior.
- Usar la API sin los parámetros opcionales del intervalo de tiempo.
- Usar un cliente que no fuerce un intervalo de tiempo, como el conector de Power BI.

Consulte los ejemplos y las notas de la sección anterior, ya que también son relevantes en este caso.

## <a name="number-of-regions"></a>Número de regiones
Hay varias situaciones en las que una única consulta puede ejecutarse en diferentes regiones:

- Cuando se incluyen explícitamente varias áreas de trabajo y se ubican en diferentes regiones.
- Cuando una consulta con ámbito de recurso está capturando los datos y estos se almacenan en varias áreas de trabajo ubicadas en diferentes regiones.

La ejecución de consultas en varias regiones requiere que el sistema efectúe la serialización y la transferencia en los fragmentos grandes del back-end de los datos intermedios que suelen ser mucho mayores que los resultados finales de la consulta. También limita la capacidad de desempeño del sistema de llevar a cabo optimizaciones, heurísticas y usar cachés.
Si no hay motivo alguno para digitalizar todas estas regiones, debe ajustar el ámbito para que cubra menos regiones. Si el ámbito del recurso se minimiza, pero aún se usan varias regiones, puede producirse debido a un error de configuración. Por ejemplo, la configuración de diagnóstico y los registros de auditoría se envía a diferentes áreas de trabajo en distintas regiones o hay varias configuraciones de diagnóstico. 

> [!IMPORTANT]
> Este indicador no está disponible para las consultas de varias regiones.

## <a name="number-of-workspaces"></a>Número de áreas de trabajo
Las áreas de trabajo son contenedores lógicos que se usan para separar y administrar los datos de registros. El back-end optimiza las selecciones de ubicación de las áreas de trabajo en clústeres físicos dentro de la región seleccionada.

El uso de varias áreas de trabajo puede deberse a alguno de los siguientes casos: 

- Cuando se enumeran explícitamente varias áreas de trabajo.
- Cuando una consulta con ámbito de recurso está capturando los datos y estos se almacenan en varias áreas de trabajo.
 
La ejecución de consultas en varios clústeres y varias regiones requiere que el sistema efectúe la serialización y la transferencia en los fragmentos grandes del back-end de los datos intermedios que suelen ser mucho mayores que los resultados finales de la consulta. También limita la capacidad de desempeño del sistema de llevar a cabo optimizaciones, heurísticas y usar cachés.

> [!IMPORTANT]
> En algunos escenarios de varias áreas de trabajo, las medidas de los datos y la CPU no serán precisas y solo representarán la medida de algunas de las áreas de trabajo.

## <a name="parallelism"></a>Paralelismo
Los registros de Azure Monitor usan clústeres grandes de Azure Data Explorer para ejecutar consultas y dichos clústeres pueden variar según la escala. El sistema escala automáticamente los clústeres en función de la capacidad y la lógica de selección de ubicación del área de trabajo.

Para ejecutar una consulta de forma eficaz, se divide en particiones y se distribuye en nodos de ejecución según los datos necesarios para su procesamiento. Hay algunas situaciones en las que el sistema no puede hacer esto de manera eficiente. Esto puede dar lugar a una larga duración de la consulta. 

Entre los comportamientos de las consultas que pueden reducir el paralelismo se incluyen los siguientes:

- Uso de las funciones de ventana y serialización, como [serialize operator](/azure/kusto/query/serializeoperator), [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction) y [row](/azure/kusto/query/rowcumsumfunction). Las funciones de análisis de usuario y serie temporal pueden usarse en algunos de estos casos. También puede producirse una serialización ineficaz si los siguientes operadores no se usan al final de la consulta: [range](/azure/kusto/query/rangeoperator), [sort](/azure/kusto/query/sortoperator), [order](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-   El uso de la función de agregación [dcount()](/azure/kusto/query/dcount-aggfunction) obliga al sistema a tener una copia central de los distintos valores. Cuando la escala de datos es alta, considere la posibilidad de usar los parámetros opcionales de la función dcount para reducir la precisión.
-   En muchos casos, el operador [join](/azure/kusto/query/joinoperator?pivots=azuremonitor) reduce el paralelismo general. Considere la combinación de orden aleatorio como alternativa cuando el rendimiento sea problemático.
-   En las consultas con ámbito de recurso, las comprobaciones de ejecución previa de RBAC pueden quedarse en situaciones en las que hay un gran número de asignaciones de RBAC. Esto puede provocar comprobaciones más largas, lo que generaría un paralelismo más bajo. Por ejemplo, una consulta se ejecuta en una suscripción en la que hay miles de recursos y cada recurso tiene varias asignaciones de roles en el nivel de recurso, no en la suscripción ni en el grupo de recursos.
-   Si una consulta está procesando fragmentos de datos pequeños, su paralelismo será inferior, ya que el sistema no se propagará en muchos nodos de ejecución.



## <a name="next-steps"></a>Pasos siguientes

- [Documentación de referencia del lenguaje de consulta de Kusto](/azure/kusto/query/).
