---
title: Guía para el ajuste del rendimiento de Azure SQL Database | Microsoft Docs
description: Obtenga información acerca del uso de las recomendaciones para ajustar manualmente el rendimiento de las consultas de su base de datos de Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: juliemsft
ms.author: jrasnick
ms.reviewer: carlrab
manager: craigg
ms.date: 01/25/2019
ms.openlocfilehash: a49d30d3058a6cf3ce82d56076f348861ad631ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60585141"
---
# <a name="manual-tune-query-performance-in-azure-sql-database"></a>Ajuste manual del rendimiento de consultas en Azure SQL Database

Una vez que haya identificado un problema de rendimiento experimente con SQL Database, este artículo le será de ayuda:

- Ajuste la aplicación y aplique algunas prácticas recomendadas que puedan mejorar el rendimiento.
- Ajuste la base de datos cambiando los índices y consultas para que funcionen de manera más eficiente con los datos.

En este artículo se da por supuesto que ya ha llevado a cabo las [recomendaciones del asesor de bases de datos](sql-database-advisor.md) de Azure SQL Database y [recomendaciones de ajuste automático](sql-database-automatic-tuning.md) de Azure SQL Database. También se supone que ha revisado [Optimización de la supervisión y el rendimiento](sql-database-monitor-tune-overview.md) y sus artículos relacionados relativos a la solución de problemas de rendimiento. Además, en este artículo se supone que no tiene un problema de rendimiento de recursos de CPU relacionado con la ejecución que se pueda resolver aumentando el tamaño de proceso o el nivel de servicio para proporcionar más recursos a la base de datos.

## <a name="tune-your-application"></a>Optimización de la aplicación

En instancias de SQL Server locales tradicionales, el proceso de planeamiento inicial de la capacidad con frecuencia está separado del proceso de ejecución de una aplicación en producción. Primero se adquieren las licencias de hardware y productos y luego se ajusta el rendimiento. Cuando se usa Azure SQL Database, es una buena idea entrelazar el proceso de ejecutar una aplicación y optimizarla. Con el modelo de pago por capacidad a petición, puede ajustar su aplicación para que use el número mínimo de recursos necesarios, en lugar de aprovisionar en exceso el hardware en función de las estimaciones de planes de crecimiento futuro para una aplicación, que con frecuencia son incorrectas. Algunos clientes pueden decidir no optimizar una aplicación y, en su lugar, eligen aprovisionar en exceso los recursos de hardware. Este enfoque puede ser una buena idea si no quiere cambiar una aplicación clave durante un período de ocupación. Sin embargo, la optimización de una aplicación puede minimizar los requisitos de recursos y reducir las facturas mensuales cuando se usan los niveles de servicio en Azure SQL Database.

### <a name="application-characteristics"></a>Características de la aplicación

Aunque los niveles de servicio de Azure SQL Database están diseñados para mejorar la estabilidad del rendimiento y la previsibilidad de una aplicación, algunos procedimientos recomendados pueden ayudarle a optimizar la aplicación para un mejor aprovechamiento de los recursos en un tamaño de proceso. Aunque muchas aplicaciones tienen importantes ganancias de rendimiento con solo cambiar a un tamaño de proceso o un nivel de servicio superior, otras aplicaciones necesitan ajustes adicionales para beneficiarse de un nivel de servicio más alto. Para aumentar el rendimiento, puede realizar ajustes adicionales en las aplicaciones para que tengan estas características:

- **Aplicaciones que tienen un rendimiento lento debido a un comportamiento "comunicativo"**

  Las aplicaciones comunicativas realizan excesivas operaciones de acceso a los datos que son sensibles a la latencia de red. Para reducir el número de operaciones de acceso a datos de la base de datos SQL, puede que tenga que modificar estos tipos de aplicaciones. Por ejemplo, puede mejorar el rendimiento de la aplicación mediante técnicas como el procesamiento por lotes de consultas ad hoc o el movimiento de las consultas a procedimientos almacenados. Para más información, consulte [Consultas por lotes](#batch-queries).

- **Bases de datos con una carga de trabajo intensiva que no se admiten en una sola máquina**

   Las bases de datos que superen los recursos del tamaño de proceso Premium más podrían beneficiarse de escalar horizontalmente la carga de trabajo. Para más información, consulte [Particionamiento entre bases de datos](#cross-database-sharding) y [Creación de particiones funcional](#functional-partitioning).

- **Aplicaciones que tienen consultas poco óptimas**

  Puede que las aplicaciones, especialmente las de la capa de acceso a datos, que tengan consultas poco optimizadas no se beneficien de un mayor tamaño de proceso. Esto incluye consultas que carecen de una cláusula WHERE, con índices que faltan o tienen estadísticas anticuadas. Estas aplicaciones se benefician de las técnicas de optimización del rendimiento de consultas estándar. Para más información, consulte [Índices que faltan](#identifying-and-adding-missing-indexes) y [Optimización de consultas y sugerencias](#query-tuning-and-hinting).

- **Aplicaciones que tienen un diseño de acceso a datos poco óptimo**

   Puede que las aplicaciones que tienen problemas inherentes de simultaneidad del acceso a los datos, por ejemplo, interbloqueos, no se beneficien de un tamaño de proceso más alto. Considere la posibilidad de reducir los viajes de ida y vuelta a Azure SQL Database almacenando en caché los datos del lado cliente con el Servicio de almacenamiento en caché de Azure u otra tecnología de almacenamiento en caché. Consulte [Almacenamiento en caché de la capa de aplicación](#application-tier-caching).

## <a name="tune-your-database"></a>Ajuste de la base de datos

En esta sección, examinamos algunas técnicas que puede usar para optimizar Azure SQL Database para obtener el mejor rendimiento de la aplicación y ejecutarla con el menor tamaño de proceso posible. Algunas de estas técnicas coinciden con los procedimientos recomendados de optimización tradicionales de SQL Server, pero otras son específicas de Azure SQL Database. En algunos casos, puede examinar los recursos consumidos en una base de datos para encontrar áreas de mejora adicional y ampliar las técnicas de SQL Server tradicionales para trabajar en Azure SQL Database.

### <a name="identifying-and-adding-missing-indexes"></a>Identificación y adición de índices que faltan

Un problema común del rendimiento de las bases de datos OLTP está relacionado con el diseño físico de la base de datos. A menudo, los esquemas de base de datos se diseñan y se entregan sin realizar pruebas a escala (ya sea en la carga o en el volumen de datos). Lamentablemente, el rendimiento de un plan de consultas puede ser aceptable a pequeña escala, pero se puede degradar sustancialmente cuando se enfrenta a los volúmenes de datos del nivel de producción. El origen más común de este problema es la falta de índices adecuados para satisfacer los filtros u otras restricciones en una consulta. A menudo, la falta de índices se manifiesta como un recorrido de tabla cuando podría ser suficiente una búsqueda de índice.

En este ejemplo, el plan de consulta seleccionado usa una exploración cuando bastaría con una búsqueda:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![Plan de consulta con índices que faltan](./media/sql-database-performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database puede ayudarle a encontrar y corregir condiciones de falta de índice comunes. Las DMV que se crean en Azure SQL Database examinan compilaciones de consultas en las que un índice reduciría considerablemente el costo estimado de ejecutar una consulta. Durante la ejecución de las consultas, SQL Database hace un seguimiento de la frecuencia con que se ejecuta cada plan de consulta, así como de la diferencia estimada entre el plan de consulta en ejecución y el imaginario en el que existía ese índice. Estas DMV se pueden usar para realizar suposiciones rápidas sobre qué cambios en el diseño de la base de datos física podrían mejorar el costo de la carga de trabajo general de una base de datos y su carga de trabajo real.

Esta consulta se puede usar para evaluar los posibles índices que falten:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

En este ejemplo, la consulta ha dado como resultado esta sugerencia:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

Después de crearla, esa misma instrucción SELECT elige un plan diferente, que usa una búsqueda en lugar de una exploración y, luego, ejecuta el plan de forma más eficaz:

![Plan de consulta con índices corregidos](./media/sql-database-performance-guidance/query_plan_corrected_indexes.png)

El principal dato es que la capacidad de E/S de un sistema compartido es más limitada que la de una máquina servidor dedicada. Es de especial valor minimizar la E/S innecesaria para sacar el máximo partido del sistema dentro de la DTU de cada tamaño de proceso de los niveles de servicio en Azure SQL Database. La elección adecuada de las opciones de diseño de bases de datos físicas puede mejorar considerablemente la latencia de las consultas individuales y la capacidad de procesamiento de solicitudes simultáneas que puede realizar por unidad de escalado, así como minimizar los costos necesarios para satisfacer la consulta. Para obtener más información acerca de las DMV de índices que faltan, consulte [sys.dm_db_missing_index_details](https://msdn.microsoft.com/library/ms345434.aspx).

### <a name="query-tuning-and-hinting"></a>Optimización de consultas y sugerencias

El optimizador de consultas de Azure SQL Database es similar al optimizador de consultas tradicional de SQL Server. La mayoría de los procedimientos recomendados para optimizar consultas y entender las limitaciones del modelo de razonamiento para el optimizador de consultas se aplica también a Azure SQL Database. Si optimiza las consultas en Azure SQL Database, puede obtener el beneficio adicional de reducir las demandas de recursos agregados. La aplicación podría ejecutarse con un menor costo que una equivalente sin optimizar porque se puede ejecutar con un tamaño de proceso menor.

Un ejemplo común en SQL Server y que también se aplica a Azure SQL Database es cómo el optimizador de consultas examina los parámetros. Durante la compilación, el optimizador de consultas evalúa el valor actual de un parámetro para determinar si puede generar un plan de consulta más óptimo. Aunque con frecuencia esta estrategia puede conducir a un plan de consultas bastante más rápido que un plan compilado sin valores de parámetros conocidos, actualmente no funciona muy bien en SQL Server y en Azure SQL Database. A veces el parámetro no se examina y otras veces sí, pero el plan generado es poco óptimo para el conjunto completo de valores de parámetros de una carga de trabajo. Microsoft incluye sugerencias de consulta (directivas) para que pueda especificar más deliberadamente la intención y reemplazar el comportamiento predeterminado de examen de parámetros. A menudo, el uso de sugerencias puede corregir los casos en los que el comportamiento predeterminado de SQL Server o Azure SQL Database es imperfecto para una carga de trabajo de un cliente determinado.

En el ejemplo siguiente se muestra cómo el procesador de consultas puede generar un plan poco óptimo para los requisitos de rendimiento y recursos. Este ejemplo también muestra que si usa una sugerencia de consulta, puede reducir los requisitos de recursos y el tiempo de ejecución de consultas de su base de datos SQL:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

El código de configuración crea una tabla que contiene una distribución de datos sesgados. El plan de consulta óptimo varía en función del parámetro que seleccione. Desafortunadamente, el comportamiento de almacenar en caché el plan no siempre recompila la consulta según el valor de parámetro más común. Por tanto, se puede almacenar en caché un plan poco óptimo y usarse para muchos valores, aunque un plan diferente podría ser una mejor opción por término medio. A continuación, el plan de consulta crea dos procedimientos almacenados que son idénticos, salvo que uno tiene una sugerencia de consulta especial.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Se recomienda esperar al menos 10 minutos para empezar la parte 2 del ejemplo, con el fin de que los resultados sean distintos en los datos de telemetría resultantes.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Cada parte de este ejemplo intenta ejecutar 1000 veces una instrucción insert parametrizada (para generar una carga suficiente para usarla como un conjunto de datos de prueba). Al ejecutar los procedimientos almacenados, el procesador de consultas examina el valor de los parámetros pasados al procedimiento durante su primera compilación (lo que se conoce como examinar parámetros). El procesador almacena en caché el plan resultante y se usa para invocaciones posteriores, aunque el valor del parámetro sea diferente. El plan óptimo no podría utilizarse en todos los casos. En ocasiones, es necesario guiar al optimizador para que seleccione un plan que sea mejor para el caso medio en lugar de para el caso específico de cuando la consulta se compila por primera vez. En este ejemplo, el plan inicial genera un plan de "exploración" que lee todas las filas para encontrar todos los valores que coinciden con el parámetro.

![Optimización de consultas mediante un plan de exploración](./media/sql-database-performance-guidance/query_tuning_1.png)

Como el procedimiento se ejecutó con el valor 1, el plan resultante era óptimo para 1 pero poco óptimo para los restantes valores de la tabla. Es probable que el resultado no sea lo que esperaría si seleccionara cada plan de manera aleatoria, dado que el plan se ejecuta más despacio y usa menos recursos.

Si ejecuta la prueba con `SET STATISTICS IO` establecido en `ON`, el trabajo de exploración lógica en este ejemplo se realiza en segundo plano. Puede ver que hay 1148 lecturas que realiza el plan (lo que es ineficaz, si el caso medio es devolver solo una fila):

![Optimización de consultas mediante una exploración lógica](./media/sql-database-performance-guidance/query_tuning_2.png)

La segunda parte del ejemplo usa una sugerencia de consulta para indicar al optimizador que use un valor específico durante el proceso de compilación. En este caso, obliga al procesador de consultas a omitir el valor que se pasa como parámetro, y en su lugar a asumir `UNKNOWN`. Este valor hace referencia a un valor que tiene la frecuencia media en la tabla (omitiendo el sesgo). El plan resultante está basado en búsquedas, es más rápido y usa menos recursos, como términos medio, que el plan de la parte 1 de este ejemplo:

![Optimización de consultas mediante una sugerencia de consulta](./media/sql-database-performance-guidance/query_tuning_3.png)

Puede ver el efecto en la tabla **sys.resource_stats** (hay un retraso desde el momento en que ejecuta la prueba y cuando los datos llenan la tabla). En este ejemplo, la parte 1 se ejecutó en el período de tiempo de 22:25:00 y la parte 2 se ejecutó a las 22:35:00. El período de tiempo anterior usó más recursos en ese período que el posterior (debido a las mejoras de eficiencia del plan).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Resultados del ejemplo de optimización de consultas](./media/sql-database-performance-guidance/query_tuning_4.png)

> [!NOTE]
> Aunque en este ejemplo el volumen es deliberadamente pequeño, el efecto de los parámetros poco óptimos puede ser considerable, especialmente en bases de datos más grandes. La diferencia, en casos extremos, puede estar entre segundos, para los casos más rápidos, y horas, para los más lentos.

Puede examinar **sys.resource_stats** para determinar si el recurso de una prueba usa más o menos recursos que otra prueba. Al comparar los datos, separe el tiempo de pruebas para que no se encuentren en el mismo período de 5 minutos en la vista **sys.resource_stats**. El objetivo del ejercicio es minimizar la cantidad total de recursos usados, no minimizar los recursos máximos. Por lo general, al optimizar la latencia de un fragmento de código, también se reduce el consumo de recursos. Asegúrese de que los cambios realizados en una aplicación sean necesarios y que no afecten negativamente a la experiencia del cliente para alguien que podría estar usando sugerencias de consulta en la aplicación.

Si una carga de trabajo contiene un conjunto de consultas repetidas, con frecuencia tiene sentido capturar y validar la idoneidad de esas elecciones del plan, ya que controlarán la unidad de tamaño mínima de los recursos necesaria para hospedar la base de datos. Después de validarlo, vuelva a examinar de vez en cuando los planes para asegurarse de que no se han degradado. Puede aprender más sobre las [sugerencias de consulta (Transact-SQL)](https://msdn.microsoft.com/library/ms181714.aspx).

### <a name="cross-database-sharding"></a>Particionamiento entre bases de datos

Como Azure SQL Database se ejecuta en hardware estándar, los límites de capacidad para una base de datos individual son inferiores a los de una instalación local de SQL Server tradicional. Algunos clientes usan técnicas de particionamiento para repartir las operaciones de base de datos entre varias bases de datos cuando las operaciones no entran en los límites de una base de datos individual en Azure SQL Database. La mayoría de los clientes que usan técnicas de particionamiento en Azure SQL Database divide sus datos en una única dimensión entre varias bases de datos. En este enfoque, debe comprender que las aplicaciones OLTP a menudo realizan transacciones que se aplican a una sola fila o a un pequeño grupo de filas del esquema.

> [!NOTE]
> Tenga en cuenta que SQL Database ahora proporciona una biblioteca para ayudar con el particionamiento. Para obtener más información, consulte [Información general de la biblioteca de cliente de Elastic Database](sql-database-elastic-database-client-library.md).

Por ejemplo, si una base de datos tiene el nombre del cliente, el pedido y los detalles del pedido (como la base de datos tradicional de ejemplo Northwind que se incluye con SQL Server), podría dividir estos datos en varias bases de datos mediante la agrupación de un cliente con el pedido relacionado y la información detallada del pedido. Puede garantizar que los datos del cliente permanezcan en una base de datos individual. La aplicación dividiría los distintos clientes entre las bases de datos, repartiendo la carga eficazmente entre varias bases de datos. El particionamiento no solo permite que los clientes eviten el límite de tamaño máximo de la base de datos, sino también que Azure SQL Database procese cargas de trabajo que sean mucho mayores que los límites de los distintos tamaños de proceso, siempre y cuando cada base de datos se ajuste a sus DTU.

Aunque el particionamiento de base de datos no reduce la capacidad de recursos agregados para una solución, es muy eficaz a la hora de admitir soluciones muy grandes que se distribuyen entre varias bases de datos. Cada base de datos se puede ejecutar con un tamaño de proceso diferente para admitir bases de datos "eficaces" muy grandes con requisitos elevados de recursos.

### <a name="functional-partitioning"></a>Creación de particiones funcional

Los usuarios de SQL Server suelen combinar varias funciones en una base de datos individual. Por ejemplo, si una aplicación contiene lógica para administrar el inventario de un almacén, esa base de datos podría contener lógica asociada con el inventario, el seguimiento de los pedidos de compra, los procedimientos almacenados y las vistas indizadas o materializadas que administran los informes de fin de me. Esta técnica facilita la administración de la base de datos para operaciones como la copia de seguridad, pero también requiere ajustar el tamaño del hardware para administrar la carga máxima en todas las funciones de una aplicación.

Si usa una arquitectura de escalado horizontal en Azure SQL Database, es una buena idea repartir las distintas funciones de una aplicación en diferentes bases de datos. Mediante esta técnica, cada aplicación se escala de forma independiente. A medida que una aplicación realiza una actividad mayor (y aumenta la carga en la base de datos), el administrador puede elegir tamaños de proceso independientes para cada función de la aplicación. En el límite, una aplicación con esta arquitectura puede ser más grande de lo que puede controlar una única máquina estándar por lo que la carga se reparte entre varias máquinas.

### <a name="batch-queries"></a>Consultas por lotes

Para las aplicaciones que tienen acceso a datos mediante consultas ad hoc frecuentes de gran volumen, una parte sustancial del tiempo de respuesta se dedica a la comunicación de red entre la capa de aplicación y la de Azure SQL Database. Incluso si la aplicación y Azure SQL Database residen en el mismo centro de datos, la latencia de red entre ambas podría verse aumentada por un número elevado de operaciones de acceso a datos. Para reducir los recorridos de ida y vuelta de red en las operaciones de acceso a datos, puede usar la opción para procesar por lotes todas las consultas ad hoc o para compilarlas como procedimientos almacenados. Si procesa por lotes las consultas ad hoc, puede enviar varias consultas como un lote grande en un solo recorrido a Azure SQL Database. Si compila las consultas ad hoc en un procedimiento almacenado, podría lograr el mismo resultado que si las procesa por lotes. El uso de un procedimiento almacenado también ofrece la ventaja de aumentar la probabilidad de almacenar en caché los planes de consulta en Azure SQL Database de modo que pueda usar de nuevo el procedimiento almacenado.

Algunas aplicaciones requieren operaciones de escritura intensivas. En ocasiones se puede reducir la carga total de E/S en una base de datos si se considera cómo procesar por lotes las escrituras juntas. Con frecuencia es tan sencillo como usar transacciones explícitas en lugar de transacciones de confirmación automática dentro de los procedimientos almacenados y lotes ad hoc. Para ver una evaluación de las distintas técnicas que se pueden usar, consulte [Técnicas de procesamiento por lotes para aplicaciones de SQL Database en Azure](https://msdn.microsoft.com/library/windowsazure/dn132615.aspx). Experimente con su propia carga de trabajo para encontrar el modelo adecuado de procesamiento por lotes. Asegúrese de entender que un modelo puede tener garantías de coherencia transaccional ligeramente diferentes. Para encontrar la carga de trabajo adecuada que minimiza el uso de recursos, es necesario encontrar la combinación correcta de coherencia y rendimiento.

### <a name="application-tier-caching"></a>Almacenamiento en caché de la capa de aplicación

Algunas aplicaciones de base de datos tienen cargas de trabajo con operaciones de lectura intensivas. El almacenamiento en caché de las capas podría reducir la carga en la base de datos y también, posiblemente, el tamaño de proceso necesario para admitir una base de datos con Azure SQL Database. Con [Azure Cache for Redis](https://azure.microsoft.com/services/cache/), si tiene una carga de trabajo con muchas operaciones de lectura, puede leer los datos una vez (o quizás una vez por máquina de nivel de aplicación, según cómo esté configurada) y luego almacenar esos datos fuera de la base de datos SQL. Se trata de una forma de reducir la carga de la base de datos (CPU y E/S de lectura), pero tiene efectos sobre la coherencia transaccional porque los datos que se leen de la caché podrían estar desincronizados con respectos a los datos de la base de datos. Aunque en muchas aplicaciones algún nivel de incoherencia es aceptable, esto no se aplica a todas las cargas de trabajo. Debería comprender totalmente los requisitos de una aplicación antes de emplear una estrategia de almacenamiento en caché de la capa de aplicación.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los niveles de servicio basados en DTU, consulte el [modelo de compra basado en DTU](sql-database-service-tiers-dtu.md).
- Para obtener más información sobre los niveles de servicio basados en núcleos virtuales, consulte [Modelo de compra basado en núcleo virtual](sql-database-service-tiers-vcore.md).
- Para obtener más información sobre los grupos elásticos, consulte [¿Qué es un grupo elástico de Azure?](sql-database-elastic-pool.md)
- Para información sobre el rendimiento y los grupos elásticos, consulte [¿Cuándo se debe utilizar un grupo elástico?](sql-database-elastic-pool-guidance.md)