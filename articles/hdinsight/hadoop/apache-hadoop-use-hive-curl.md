---
title: 'Uso de Apache Hadoop Hive con Curl en HDInsight: Azure'
description: Sepa cómo enviar remotamente trabajos de Apache Pig a HDInsight mediante Curl.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/23/2018
ms.author: hrasheed
ms.openlocfilehash: e020cbc9c18db2142ee7f52cdac22a3518683fce
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64695825"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Ejecución de consultas de Apache Hive con Apache Hadoop en HDInsight mediante REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Obtenga información acerca de cómo usar la API REST de WebHCat para ejecutar consultas de Apache Hive con Apache Hadoop en un clúster de Azure HDInsight.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de Hadoop en HDInsight basado en Linux versión 3.4 o posterior.

  > [!IMPORTANT]  
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* Un cliente REST. Este documento usa ejemplos de Windows PowerShell y [Curl](https://curl.haxx.se/).

    > [!NOTE]  
    > Azure PowerShell proporciona cmdlets dedicados para trabajar con Hive en HDInsight. Para más información, consulte el documento [Uso de Apache Hive con Azure PowerShell](apache-hadoop-use-hive-powershell.md).

Este documento también usa Windows PowerShell y [Jq](https://stedolan.github.io/jq/) para procesar los datos JSON que devuelven las solicitudes REST.

## <a id="curl"></a>Ejecución de una consulta de Hive

> [!NOTE]  
> Al usar Curl o cualquier otra comunicación REST con WebHCat, debe proporcionar el nombre de usuario y la contraseña del administrador del clúster de HDInsight para autenticar las solicitudes.
>
> La API de REST se protege con la [autenticación básica](https://en.wikipedia.org/wiki/Basic_access_authentication). Para garantizar que las credenciales se envían de manera segura al servidor, siempre debe crear solicitudes usando HTTP segura (HTTPS).

1. Para establecer el inicio de sesión del clúster utilizado por los scripts de este documento, use uno de los siguientes comandos:

    ```bash
    read -p "Enter your cluster login account name: " LOGIN
    ```

    ```powershell
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login name and password"
    ```

2. Para establecer el nombre del clúster, use uno de los siguientes comandos:

    ```bash
    read -p "Enter the HDInsight cluster name: " CLUSTERNAME
    ```

    ```powershell
    $clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
    ```

3. Utilice uno de los siguientes comandos para comprobar que puede conectarse al clúster de HDInsight:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/status
    ```
    
    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Debe recibir una respuesta similar al texto siguiente:

    ```json
    {"status":"ok","version":"v1"}
    ```

    Los parámetros que se utilizan en este comando son los siguientes:

    * `-u`: el nombre de usuario y la contraseña que se utilizan para autenticar la solicitud.
    * `-G`: indica que esta es una solicitud de operación GET.

   El principio de la dirección URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1`, es el mismo para todas las solicitudes. La ruta de acceso, `/status`, indica que la solicitud debe devolver un estado de WebHCat (también conocido como Templeton) al servidor. También puede solicitar la versión de Hive con el siguiente comando:

    ```bash
    curl -u $LOGIN -G https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Esta solicitud devuelve una respuesta similar al texto siguiente:

    ```json
        {"module":"hive","version":"0.13.0.2.1.6.0-2103"}
    ```

4. Use lo siguiente para crear una tabla llamada **log4jLogs**:

    ```bash
    JOBID=`curl -s -u $LOGIN -d user.name=$LOGIN -d execute="set+hive.execution.engine=tez;DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/hive | jq .id`
    echo $JOBID
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="set hive.execution.engine=tez;DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Esta solicitud utiliza el método POST, que envía los datos como parte de la solicitud a la API de REST. Los siguientes valores de datos se envían con la solicitud:

     * `user.name`: el usuario que ejecuta el comando.
     * `execute`: las instrucciones HiveQL para ejecutar.
     * `statusdir`: el directorio donde se escribe el estado de este trabajo.

   Estas instrucciones realizan las acciones siguientes:
   
   * `DROP TABLE`: si la tabla ya existe, se elimina.
   * `CREATE EXTERNAL TABLE`: crea una nueva tabla "externa" en Hive. Las tablas externas solo almacenan la definición de tabla en Hive. Los datos permanecen en la ubicación original.

     > [!NOTE]  
     > Las tablas externas se deben utilizar cuando se espera que un origen externo actualice los datos subyacentes. Por ejemplo, un proceso de carga de datos automatizado u otra operación de MapReduce.
     >
     > La eliminación de una tabla externa **no** elimina los datos, solamente la definición de tabla.

   * `ROW FORMAT`: indica el formato de los datos. Los campos de cada registro se separan mediante un espacio.
   * `STORED AS TEXTFILE LOCATION`: indica dónde se almacenan los datos (el directorio example/data) y que se almacenan como texto.
   * `SELECT`: selecciona un número de todas las filas donde la columna **t4** contiene el valor **[ERROR]**. Esta instrucción devuelve un valor de **3** porque hay tres filas que contienen este valor.

     > [!NOTE]  
     > Observe que los espacios entre las instrucciones HiveQL se reemplazan por el carácter `+` cuando se usa con Curl. Los valores entre comillas que contengan un espacio, como el delimitador, no se reemplazarán por `+`.

      Este comando devuelve un identificador de trabajo que se pueda usar para comprobar el estado del trabajo.

5. Para revisar el estado del trabajo, use el siguiente comando:

    ```bash
    curl -G -u $LOGIN -d user.name=$LOGIN https://$CLUSTERNAME.azurehdinsight.net/templeton/v1/jobs/$JOBID | jq .status.state
    ```

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing
    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

    Si se ha completado el trabajo, el estado es **SUCCEEDED**.

6. Una vez que el estado del trabajo cambió a **SUCCEEDED**, puede recuperar los resultados del trabajo desde Azure Blob Storage. El parámetro `statusdir` pasado con la consulta contiene la ubicación del archivo de salida; en este caso, `/example/rest`. Esta dirección almacena la salida en el directorio `example/curl` del almacenamiento predeterminado del clúster.

    Puede enumerar y descargar estos archivos mediante la [CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). Para más información acerca del uso de la CLI de Azure con Azure Storage, consulte el documento [Uso de la CLI de Azure con Azure Storage](https://docs.microsoft.com/azure/storage/storage-azure-cli#create-and-manage-blobs).

## <a id="nextsteps"></a>Pasos siguientes

Si desea obtener información general sobre Hive con HDInsight:

* [Uso de Apache Hive con Apache Hadoop en HDInsight](hdinsight-use-hive.md)

Para obtener información sobre otras formas en que puede trabajar con Hadoop en HDInsight:

* [Uso de Apache Pig con Apache Hadoop en HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con Apache Hadoop en HDInsight](hdinsight-use-mapreduce.md)

Para obtener más información sobre la API de REST usada en este documento, consulte el documento [WebHCat reference](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) (Referencia de WebHCat).

[azure-purchase-options]: https://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: https://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: https://azure.microsoft.com/pricing/free-trial/

[apache-tez]: https://tez.apache.org
[apache-hive]: https://hive.apache.org/
[apache-log4j]: https://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: https://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie-linux-mac.md




[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md
[hdinsight-submit-jobs]:submit-apache-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md

[powershell-here-strings]: https://technet.microsoft.com/library/ee692792.aspx


