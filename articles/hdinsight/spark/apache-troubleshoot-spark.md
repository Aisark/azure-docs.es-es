---
title: Solución de problemas de Spark en Azure HDInsight
description: Obtenga respuestas a las preguntas comunes sobre cómo trabajar con Apache Spark y Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: a4dc7293c00097c7a5752e29bf7c9a203cbb31a5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107524"
---
# <a name="troubleshoot-apache-spark-by-using-azure-hdinsight"></a>Solución de problemas de Apache Spark mediante Azure HDInsight

Conozca los principales problemas y sus soluciones al trabajar con cargas útiles de [Apache Spark](https://spark.apache.org/) en [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-ambari-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante Apache Ambari en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

Se pueden ajustar los valores de configuración de Spark ayudar a evitar una excepción OutofMemoryError de aplicación de Apache Spark. Los pasos siguientes muestran la forma predeterminada los valores de configuración de Spark en HDInsight de Azure: 

1. En la lista de clústeres, seleccione **Spark2**.

    ![Selección del clúster de la lista](./media/apache-troubleshoot-spark/update-config-1.png)

2. Seleccione la pestaña **Configs** (Configuraciones).

    ![Selección de la pestaña Configs (Configuraciones)](./media/apache-troubleshoot-spark/update-config-2.png)

3. En la lista de configuraciones, seleccione **Custom-spark2-defaults**.

    ![Selección de custom-spark-defaults](./media/apache-troubleshoot-spark/update-config-3.png)

4. Busque el valor de configuración que tiene que ajustar, por ejemplo **spark.executor.memory**. En este caso, el valor de **4608m** es demasiado alto.

    ![Seleccionar el campo spark.executor.memory](./media/apache-troubleshoot-spark/update-config-4.png)

5. Establezca el valor en la configuración recomendada. Se recomienda el valor de **2048m** para esta configuración.

    ![Cambiar el valor a 2048m](./media/apache-troubleshoot-spark/update-config-5.png)

6. Guarde el valor y, después, guarde la configuración. En la barra de herramientas, seleccione **Guardar**.

    ![Guardar el valor y la configuración](./media/apache-troubleshoot-spark/update-config-6a.png)

    Se le notificará si hay configuraciones que necesiten atención. Tenga en cuenta los elementos y, a continuación, seleccione **Proceed Anyway** (Continuar de todos modos). 

    ![Selección de Proceed Anyway (Continuar de todos modos)](./media/apache-troubleshoot-spark/update-config-6b.png)

    Escriba una nota sobre los cambios de configuración y, después, seleccione **Guardar**.

    ![Escribir una nota sobre los cambios realizados](./media/apache-troubleshoot-spark/update-config-6c.png)

7. Cada vez que se guarda una configuración, se le solicitará que reinicie el servicio. Seleccione **Reiniciar**.

    ![Selección de Reiniciar](./media/apache-troubleshoot-spark/update-config-7a.png)

    Confirme el reinicio.

    ![Selección de Confirm Restart All (Confirmar reinicio de todo)](./media/apache-troubleshoot-spark/update-config-7b.png)

    Puede revisar los procesos en ejecución.

    ![Revisar los procesos en ejecución](./media/apache-troubleshoot-spark/update-config-7c.png)

8. Puede agregar configuraciones. En la lista de configuraciones, seleccione **Custom-spark2-defaults** y, a continuación, seleccione **Agregar propiedad**.

    ![Selección de Agregar propiedad](./media/apache-troubleshoot-spark/update-config-8.png)

9. Defina una nueva propiedad. Puede definir una propiedad única mediante un cuadro de diálogo para valores específicos como el tipo de datos. O bien, puede definir varias propiedades mediante una definición por línea. 

    En este ejemplo, se define la propiedad **spark.driver.memory** con un valor de **4g**.

    ![Definir la nueva propiedad](./media/apache-troubleshoot-spark/update-config-9.png)

10. Guarde la configuración y, a continuación, reinicie el servicio, como se describe en los pasos 6 y 7.

Estos cambios son para todo el clúster, pero se pueden invalidar al enviar el trabajo de Spark.

### <a name="additional-reading"></a>Lecturas adicionales

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envío de trabajos de Apache Spark en clústeres de HDInsight)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-a-jupyter-notebook-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante un cuaderno de Jupyter en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte ¿Qué produce una excepción OutOfMemoryError en una aplicación de Apache Spark?.

2. En la primera celda del cuaderno de Jupyter, después de la directiva **%%configure**, especifique las configuraciones de Spark en formato JSON válido. Cambie los valores reales según sea necesario:

    ![Agregar una configuración](./media/apache-troubleshoot-spark/add-configuration-cell.png)

### <a name="additional-reading"></a>Lecturas adicionales

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envío de trabajos de Apache Spark en clústeres de HDInsight)


## <a name="how-do-i-configure-an-apache-spark-application-by-using-apache-livy-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante Apache Livy en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte ¿Qué produce una excepción OutOfMemoryError en una aplicación de Apache Spark?. 

2. Envíe la aplicación Spark a Livy mediante un cliente REST como cURL. Use un comando similar al siguiente. Cambie los valores reales según sea necesario:

    ```apache
    curl -k --user 'username:password' -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasb://container@storageaccountname.blob.core.windows.net/example/jars/sparkapplication.jar", "className":"com.microsoft.spark.application", "numExecutors":4, "executorMemory":"4g", "executorCores":2, "driverMemory":"8g", "driverCores":4}'  
    ```

### <a name="additional-reading"></a>Lecturas adicionales

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envío de trabajos de Apache Spark en clústeres de HDInsight)

## <a name="how-do-i-configure-an-apache-spark-application-by-using-spark-submit-on-clusters"></a>¿Cómo se configura una aplicación de Apache Spark mediante spark-submit en clústeres?

### <a name="resolution-steps"></a>Pasos de la solución

1. Para determinar qué configuraciones de Spark deben establecerse y en qué valores, consulte ¿Qué produce una excepción OutOfMemoryError en una aplicación de Apache Spark?.

2. Inicie spark-shell mediante un comando similar al siguiente. Cambie el valor real de las configuraciones según sea necesario: 

    ```apache
    spark-submit --master yarn-cluster --class com.microsoft.spark.application --num-executors 4 --executor-memory 4g --executor-cores 2 --driver-memory 8g --driver-cores 4 /home/user/spark/sparkapplication.jar
    ```

### <a name="additional-reading"></a>Lecturas adicionales

[Apache Spark job submission on HDInsight clusters](https://web.archive.org/web/20190112152841/ https://blogs.msdn.microsoft.com/azuredatalake/2017/01/06/spark-job-submission-on-hdinsight-101/) (Envío de trabajos de Apache Spark en clústeres de HDInsight)


## <a name="what-causes-an-apache-spark-application-outofmemoryerror-exception"></a>¿Qué produce una excepción OutOfMemoryError en una aplicación de Apache Spark?

### <a name="detailed-description"></a>Descripción detallada

Se produce un error en la aplicación Spark, con los siguientes tipos de excepciones no detectadas:

```apache
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439) 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

```apache
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main] 

java.lang.OutOfMemoryError 
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.grow(Unknown Source) 
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source) 
    at java.io.ByteArrayOutputStream.write(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source) 
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject0(Unknown Source) 
    at java.io.ObjectOutputStream.writeObject(Unknown Source) 
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44) 
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101) 
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239) 
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source) 
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source) 
    at java.lang.Thread.run(Unknown Source) 
```

### <a name="probable-cause"></a>Causa probable

La causa más probable de esta excepción es que no se asigna suficiente memoria del montón a las máquinas virtuales Java (JVM). Estas JVM se inician como ejecutores o controladores como parte de la aplicación Spark. 

### <a name="resolution-steps"></a>Pasos de la solución

1. Determine el tamaño máximo de los datos que la aplicación Spark controla. Puede realizar una estimación en función del tamaño máximo de los datos de entrada, los datos intermedios que se generan al transformar los datos de entrada y los datos de salida generados cuando la aplicación transforma aún más los datos intermedios. Este proceso puede ser iterativo si no puede realizar una estimación formal inicial. 

2. Asegúrese de que el clúster de HDInsight que va a usar tiene suficientes recursos, en términos de memoria, así como núcleos para acomodar la aplicación Spark. Puede determinarlo viendo en la sección Cluster Metrics (Métricas de clústeres) de la interfaz de usuario de YARN los valores de **Memory Used** (Memoria usada) frente a **Memory Total** (Total de memoria ) y **VCores Used** (VCores usados) frente a **VCores Total** (Total de VCores).

3. Establezca las siguientes configuraciones de Spark en los valores adecuados, que no deben superar el 90 % de la memoria y los núcleos disponibles. Los valores deben cumplir los requisitos de memoria de la aplicación Spark: 

    ```apache
    spark.executor.instances (Example: 8 for 8 executor count) 
    spark.executor.memory (Example: 4g for 4 GB) 
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB) 
    spark.executor.cores (Example: 2 for 2 cores per executor) 
    spark.driver.memory (Example: 8g for 8GB) 
    spark.driver.cores (Example: 4 for 4 cores)   
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB) 
    ```

    Para calcular la memoria total usada por todos los ejecutores: 
    
    ```apache
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```
   Para calcular la memoria total usada por el controlador:
    
    ```apache
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

### <a name="additional-reading"></a>Lecturas adicionales

- [Introducción a la administración de memoria en Apache Spark](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview)
- [Depuración de una aplicación de Apache Spark en un clúster de HDInsight](https://web.archive.org/web/20190112152909/ https://blogs.msdn.microsoft.com/azuredatalake/2016/12/19/spark-debugging-101/)


### <a name="see-also"></a>Vea también
[Solución de problemas mediante Azure HDInsight](../../hdinsight/hdinsight-troubleshoot-guide.md)
