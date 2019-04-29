---
title: Apache Hive con herramientas Data Lake (Apache Hadoop) para Visual Studio en Azure HDInsight
description: Aprenda a usar las herramientas de Data Lake para Visual Studio a fin de ejecutar consultas de Apache Hive con Apache Hadoop en Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: 3a2e81234702e1fcff0349a14a4bc2852d257ad6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62095479"
---
# <a name="run-apache-hive-queries-using-the-data-lake-tools-for-visual-studio"></a>Ejecución de las consultas de Apache Hive mediante las herramientas de Data Lake para Visual Studio

Aprenda a usar las herramientas de Data Lake para Visual Studio a fin de realizar consultas en Apache Hive. Las herramientas de Data Lake permiten crear, enviar y supervisar fácilmente consultas de Hive en Apache Hadoop en Azure HDInsight.

## <a id="prereq"></a>Requisitos previos

* Un clúster de HDInsight (Apache Hadoop en HDInsight).

  > [!IMPORTANT]  
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Visual Studio (una de las siguientes versiones):

    * Visual Studio 2013 Community/Professional/Premium/Ultimate con la actualización 4

    * Visual Studio 2015 (cualquier edición)

    * Visual Studio 2017 (cualquier edición)

* Herramientas de HDInsight para Visual Studio o Azure Data Lake Tools for Visual Studio. Consulte [Introducción al uso de herramientas de Hadoop en Visual Studio para HDInsight](apache-hadoop-visual-studio-tools-get-started.md) para ejecutar una consulta de Hive para conocer los pasos que le permitirán instalar y configurar las herramientas.

## <a id="run"></a> Ejecución de las consultas de Apache Hive usando Visual Studio

1. Abra **Visual Studio** y seleccione **Nuevo** > **Proyecto** > **Azure Data Lake** > **HIVE** > **Hive Application** (Aplicación de Hive). Indique un nombre para este proyecto.

2. Abra el archivo **Script.hql** creado con este proyecto y péguelo en las siguientes instrucciones de HiveQL:

   ```hiveql
   set hive.execution.engine=tez;
   DROP TABLE log4jLogs;
   CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
   STORED AS TEXTFILE LOCATION '/example/data/';
   SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND  INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   ```

    Estas instrucciones realizan las acciones siguientes:

   * `DROP TABLE`: si la tabla existe, esta instrucción la elimina.

   * `CREATE EXTERNAL TABLE`: crea una nueva tabla "externa" en Hive. Las tablas externas solo almacenan la definición de tabla en Hive; los datos quedan en la ubicación original.

     > [!NOTE]  
     > Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un trabajo de MapReduce o un servicio de Azure.
     >
     > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

   * `ROW FORMAT`: indica cómo se da formato a los datos de Hive. En este caso, los campos de cada registro se separan mediante un espacio.

   * `STORED AS TEXTFILE LOCATION`: indica a Hive que los datos se almacenan como texto en el directorio example/data.

   * `SELECT`: seleccione un número de todas las filas donde la columna `t4` contiene el valor `[ERROR]`. Esta instrucción devuelve un valor de `3` porque hay tres filas que contienen este valor.

   * `INPUT__FILE__NAME LIKE '%.log'`: Indica a Hive que solo deberíamos devolver datos de archivos que terminan en .log. Esta cláusula limita la búsqueda al archivo sample.log que contiene los datos.

3. En la barra de herramientas, seleccione el **clúster de HDInsight** que desea usar para esta consulta. Seleccione **Enviar** para ejecutar las instrucciones como un trabajo de Hive.

   ![Barra Enviar](./media/apache-hadoop-use-hive-visual-studio/toolbar.png)

4. El **resumen del trabajo de Hive** aparecerá y mostrará información sobre el trabajo en ejecución. Use el vínculo **Actualizar** para actualizar la información del trabajo, hasta que el **estado del trabajo** cambie a **Completado**.

   ![resumen de trabajos que muestra un trabajo completado](./media/apache-hadoop-use-hive-visual-studio/jobsummary.png)

5. Utilice el vínculo **Salida de trabajo** para ver el resultado de este trabajo. Muestra `[ERROR] 3`, que es el valor que devuelve esta consulta.

6. También puede ejecutar consultas de Hive sin crear un proyecto. Con el **Explorador de servidores**, expanda **Azure** > **HDInsight**, haga clic con el botón derecho en el servidor de HDInsight y luego seleccione **Escribir una consulta de Hive**.

7. En el documento **temp.hql** que aparece, agregue las siguientes instrucciones de HiveQL.

   ```hiveql
   set hive.execution.engine=tez;
   CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
   INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   ```

    Estas instrucciones realizan las acciones siguientes:

   * `CREATE TABLE IF NOT EXISTS`: crea una tabla, si todavía no existe. Dado que no se utiliza la palabra clave `EXTERNAL`, esta instrucción crea una tabla interna. Las tablas internas se guardan en el almacenamiento de datos de Hive y Hive las administra.

     > [!NOTE]  
     > A diferencia de las tablas `EXTERNAL`, la eliminación de una tabla interna también eliminará los datos subyacentes.

   * `STORED AS ORC`: almacena los datos en el formato de columnas de filas optimizadas (ORC, Optimized Row Columnar). ORC es un formato altamente optimizado y eficiente para almacenar datos de Hive.

   * `INSERT OVERWRITE ... SELECT`: selecciona filas de la tabla `log4jLogs` que contienen `[ERROR]` y luego inserta los datos en la tabla `errorLogs`.

8. En la barra de herramientas, seleccione **Enviar** para ejecutar el trabajo. Utilice el **estado del trabajo** para determinar que el trabajo se ha completado correctamente.

9. Para comprobar que el trabajo ha creado la tabla, utilice el **Explorador de servidores** y expanda **Azure** > **HDInsight** > el clúster de HDInsight > **Bases de datos de Hive** > **predeterminado**. La tabla **errorLogs** y la tabla **log4jLogs** aparecen en la lista.

## <a id="nextsteps"></a>Pasos siguientes

Como puede ver, las herramientas de HDInsight para Visual Studio proporcionan una manera sencilla de trabajar con consultas de Hive en HDInsight.

Para obtener información general acerca de Hive en HDInsight:

* [Uso de Apache Hive con Apache Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Apache Pig con Apache Hadoop en HDInsight](hdinsight-use-pig.md)

* [Uso de MapReduce con Apache Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información acerca de las herramientas de HDInsight para Visual Studio:

* [Introducción a las herramientas de HDInsight para Visual Studio](apache-hadoop-visual-studio-tools-get-started.md)

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md



[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
