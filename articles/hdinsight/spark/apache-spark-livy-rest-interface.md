---
title: Uso de Livy Spark para enviar trabajos a un clúster Spark de Azure HDInsight
description: Obtenga información sobre cómo usar la API de REST de Apache Spark para enviar trabajos de Spark de forma remota a un clúster de Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: f5b3500e1e700abf894fc4e21fb540eb258d5e35
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "67066059"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Uso de la API de REST de Apache Spark para enviar trabajos remotos a un clúster Spark de HDInsight

Obtenga información sobre cómo usar [Apache Livy](https://livy.incubator.apache.org/), la API REST de [Apache Spark](https://spark.apache.org/), que se usa para enviar trabajos remotos a un clúster de Azure HDInsight Spark. Para obtener documentación detallada, consulte [https://livy.incubator.apache.org/](https://livy.incubator.apache.org/).

Puede usar Livy para ejecutar shells de Spark interactivos o enviar trabajos por lotes que se ejecutarán en Spark. Este artículo trata acerca de cómo utilizar Livy para enviar trabajos por lotes. Los fragmentos de código de este artículo usan cURL para realizar llamadas de la API de REST al punto de conexión de Livy Spark.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Apache Spark en HDInsight. Para obtener instrucciones, vea [Creación de clústeres Apache Spark en HDInsight de Azure](apache-spark-jupyter-spark-sql.md).

* [cURL](https://curl.haxx.se/). En este artículo se usa cURL para mostrar cómo realizar llamadas de la API de REST a un clúster de HDInsight Spark.

## <a name="submit-an-apache-livy-spark-batch-job"></a>Envío de un trabajo por lotes de Apache Livy Spark

Antes de enviar un trabajo por lotes, debe cargar el archivo jar de aplicación en el almacenamiento del clúster asociado al clúster. Puede usar [AzCopy](../../storage/common/storage-use-azcopy.md), una utilidad de línea de comandos, para hacerlo. Hay muchos otros clientes que se pueden usar para cargar datos. Puede encontrar más información al respecto en [Carga de datos para trabajos de Apache Hadoop en HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Ejemplos

* Si el archivo jar se encuentra en el almacenamiento de clúster (WASB)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasb://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Si quiere pasar el nombre del archivo jar y el nombre de clase como parte de un archivo de entrada (en este ejemplo, input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Obtención de información sobre los lotes de Livy Spark que se ejecutan en el clúster

Sintaxis:

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Ejemplos

* Si desea recuperar todos los lotes de Livy Spark que se ejecutan en el clúster:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches" 
    ```

* Si desea recuperar un lote específico con un determinado identificador de lote

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Eliminación de un trabajo por lotes de Livy Spark

```cmd
curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Ejemplo

Eliminación de un trabajo por lotes con el identificador de lote `5`.

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Livy Spark y alta disponibilidad

Livy proporciona alta disponibilidad para los trabajos de Spark que se ejecuten en el clúster. Estos son algunos ejemplos.

* Si el servicio Livy deja de funcionar después de haber enviado un trabajo de forma remota a un clúster Spark, dicho trabajo continúa ejecutándose en segundo plano. Cuando se restablece el funcionamiento de Livy, restaura el estado del trabajo e informa de ello.
* Los cuadernos de Jupyter Notebook para HDInsight cuentan con la tecnología de Livy en el backend. Si un cuaderno ejecuta un trabajo de Spark y se reinicia el servicio Livy, el cuaderno sigue ejecutando las celdas de código. 

## <a name="show-me-an-example"></a>Mostrar un ejemplo

En esta sección se verán ejemplos de uso de Livy Spark para enviar un trabajo por lotes, supervisar el progreso del trabajo y después eliminarlo. La aplicación que se usa en este ejemplo es la que se desarrolla en el artículo [Creación de una aplicación de Scala independiente para ejecutarla en el clúster HDInsight Spark](apache-spark-create-standalone-application.md). En los pasos siguientes se da por hecho que:

* Ya ha copiado el archivo JAR de la aplicación en la cuenta de almacenamiento asociada con el clúster.
* Tiene CuRL instalado en el equipo donde intenta seguir estos pasos.

Lleve a cabo los siguiente pasos:

1. Nos gustaría comprobar primero que Livy Spark se está ejecutando en el clúster. Podemos hacerlo mediante la obtención de una lista de lotes en ejecución. Si es la primera vez que ejecuta un trabajo con Livy, la salida debe devolver cero.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

    Debería obtener una salida similar al siguiente fragmento de código:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observe que la última línea de la salida indica **total: 0**, lo que sugiere que no hay lotes en ejecución.

2. Ahora vamos a enviar un trabajo por lotes. El fragmento de código siguiente usa un archivo de entrada (input.txt) para pasar el nombre del archivo jar y el nombre de clase como parámetros. Si ejecuta estos pasos desde un equipo Windows, el enfoque recomendado es usar un archivo de entrada.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    A continuación se definen los parámetros del archivo **input.txt** :

    ```text
    { "file":"wasb:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Debería ver una salida similar al siguiente fragmento de código:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Observe que la última línea del resultado indica **state:starting**. También indica **id:0**. Aquí, **0** es el identificador de lote.

3. Ahora puede recuperar el estado de este lote concreto con el identificador de lote.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Debería ver una salida similar al siguiente fragmento de código:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    La salida muestra ahora **state:success**, lo que sugiere que el trabajo se ha completado correctamente.

4. Si lo desea, ahora puede eliminar el lote.

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"
    ```

    Debería ver una salida similar al siguiente fragmento de código:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    La última línea de la salida indica que el lote se ha eliminado correctamente. Al eliminar un trabajo mientras se está ejecutando también se termina. Si elimina un trabajo que se ha completado correctamente o incorrectamente, elimina la información del trabajo por completo.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Actualizaciones de configuración de Livy, a partir de la versión 3.5 de HDInsight

Los clústeres de HDInsight 3.5 y versiones posteriores, de forma predeterminada, deshabilitan el uso de rutas de acceso a archivos locales para el acceso a archivos de datos de ejemplo o archivos jar. Le recomendamos que use la ruta de acceso `wasb://` en su lugar para tener acceso a archivos jar o archivos de datos de ejemplo desde el clúster.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Envío de trabajos de Livy para un clúster en una red virtual de Azure

Si se conecta a un clúster de HDInsight Spark desde una instancia de Azure Virtual Network, puede conectarse directamente a Livy en el clúster. En tal caso, la dirección URL del punto de conexión de Livy es `http://<IP address of the headnode>:8998/batches`. Aquí, **8998** es el puerto en el que se ejecuta Livy en el nodo principal del clúster. Para más información sobre el acceso a los servicios en puertos no públicos, vea [Puertos utilizados por los servicios Apache Hadoop en HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Pasos siguientes

* [Documentación de la API REST de Apache Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Administración de recursos para el clúster Apache Spark en HDInsight de Azure](apache-spark-resource-manager.md)
* [Track and debug jobs running on an Apache Spark cluster in HDInsight (Seguimiento y depuración de trabajos que se ejecutan en un clúster de Apache Spark en HDInsight)](apache-spark-job-debugging.md)