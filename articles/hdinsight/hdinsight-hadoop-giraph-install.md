---
title: Instalación y uso de Giraph en clústeres de Hadoop en HDInsight
description: Obtenga información sobre cómo personalizar el clúster de HDInsight con Giraph y cómo usar Giraph.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/05/2016
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: 3b46acfbfd77015193135d1a8c7646c331340145
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200657"
---
# <a name="install-and-use-apache-giraph-on-windows-based-hdinsight-clusters"></a>Instalación y uso de Apache Giraph en clústeres de HDInsight basados en Linux

Obtenga información acerca de cómo personalizar un clúster de HDInsight basado en Windows con Apache Giraph mediante la acción de script, y cómo usar Giraph para procesar gráficos a gran escala. Para información sobre el uso de Giraph con un clúster basado en Linux, consulte [Instalación de Apache Giraph en clústeres Hadoop de HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md).

> [!IMPORTANT]  
> Los pasos de este tutorial solo se aplican a los clústeres de HDInsight basados en Windows. HDInsight solo está disponible en Windows en versiones inferiores a la 3.4. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Para información sobre cómo instalar Giraph con un clúster de HDInsight basado en Linux, consulte [Instalación de Apache Giraph en clústeres de Hadoop en HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md).


Puede instalar Giraph en cualquier tipo de clúster (Hadoop, Storm, HBase, Spark) en HDInsight de Azure mediante la *acción de script*. Un script de ejemplo para instalar Giraph en un clúster de HDInsight se encuentra disponible en un blob de solo lectura de Azure Storage en [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1). El script de ejemplo solo funciona con el clúster de HDInsight versión 3.1. Para obtener más información acerca de las versiones de clústeres de HDInsight, consulte las [versiones de clústeres de HDInsight](hdinsight-component-versioning.md).

**Artículos relacionados**

* [Instalación de Apache Giraph en clústeres de Hadoop en HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Creación de clústeres de Apache Hadoop en HDInsight](hdinsight-provision-clusters.md): información general acerca de la creación de clústeres de HDInsight.
* [Personalizar un clúster de HDInsight mediante acciones de Script] [hdinsight-cluster-customize]: información general sobre la personalización de clústeres de HDInsight mediante la acción de secuencia de comandos.
* [Desarrollo de acciones de script con HDInsight](hdinsight-hadoop-script-actions.md).

## <a name="what-is-giraph"></a>¿Qué es Giraph?
<a href="https://giraph.apache.org/" target="_blank">Apache Giraph</a> permite realizar un procesamiento gráfico mediante Hadoop, y se puede usar con HDInsight de Azure. Los gráficos modelan las relaciones entre los objetos, como las conexiones entre los enrutadores en una red de gran tamaño como Internet, o las relaciones entre personas de las redes sociales (conocidas en ocasiones como gráficos sociales). El procesamiento gráfico le permite razonar sobre las relaciones entre los objetos de un gráfico. En este sentido, le ayuda por ejemplo a:

* Identificar los posibles amigos según las relaciones actuales.
* Identificar la ruta más corta entre dos equipos de una red.
* Calcular la posición de las páginas web.

## <a name="install-giraph-using-portal"></a>Instalación de Giraph mediante el portal
1. Comience a crear un clúster mediante la opción **CREACIÓN PERSONALIZADA**, como se describe en [Creación de clústeres de Hadoop en HDInsight](hdinsight-provision-clusters.md).
2. En la página **Acciones de script** del asistente, haga clic en **Agregar acción de script** para proporcionar detalles acerca de la acción de script, como se muestra a continuación:

    ![Uso de la acción de script para personalizar un clúster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Uso de la acción de script para personalizar un clúster")

    |Propiedad|Valor|  
    |---|---|  
    |NOMBRE|Especifique un nombre para la acción de script. Por ejemplo, **Instalar Giraph**.|
    |Identificador URI de script|Especifique el Identificador uniforme de recursos (URI) al script que se ha invocado para personalizar el clúster. Por ejemplo: *https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1*.|
    |Tipo de nodo|Especifique los nodos en los que se ejecuta el script de personalización. Puede elegir **Todos los nodos**, **Solo nodos principales** o **Solo nodos de trabajo**.
    |Parámetros|Especifique los parámetros, si lo requiere el script. El script para instalar Giraph no requiere ningún parámetro, por lo que puede dejarlo en blanco.|  

    Puede agregar más de una acción de script para instalar varios componentes en el clúster. Después de haber agregado los scripts, haga clic en la marca de verificación para comenzar a crear el clúster.

## <a name="use-giraph"></a>Uso de Giraph
Usamos el ejemplo SimpleShortestPathsComputation para mostrar la implementación básica de <a href = "https://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> para encontrar la ruta más corta entre objetos en un gráfico. Use los siguientes pasos para cargar los datos de ejemplo y el jar de muestra, ejecutar un trabajo mediante el ejemplo SimpleShortestPathsComputation y luego ver los resultados.

1. Cargue un archivo de datos de ejemplo en el almacenamiento de blobs de Azure. En la estación de trabajo local, cree un archivo nuevo llamado **tiny_graph.txt**. Debe contener las siguientes líneas:

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Cargue el archivo tiny_graph.txt en el almacenamiento principal del clúster de HDInsight. Para obtener instrucciones sobre cómo cargar datos, consulte [Carga de datos para trabajos de Apache Hadoop en HDInsight](hdinsight-upload-data.md).

    Estos datos describen una relación entre los objetos de un gráfico dirigido, con el formato [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Cada línea representa una relación entre un objeto **source\_id** y uno más objetos **dest\_id**. El valor **edge\_value** (o peso) se puede considerar como la fuerza o la distancia de la conexión entre **source_id** y **dest\_id**.

    Extrapolados, y usando el valor (o peso) como la distancia entre los objetos, los datos anteriores podrían parecerse a estos:

    ![tiny_graph.txt drawn as circles with lines of varying distance between](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)

2. Ejecute el ejemplo SimpleShortestPathsComputation. Utilice los siguientes cmdlets de PowerShell de Azure para ejecutar el ejemplo mediante el archivo tiny_graph.txt como entrada.

    > [!IMPORTANT]  
    > La compatibilidad con Azure PowerShell para administrar recursos de HDInsight mediante Azure Service Manager está **en desuso** y dejó de estar disponible por completo el 1 de enero de 2017. En los pasos descritos en este documento, se usan los nuevos cmdlets de HDInsight que funcionan con Azure Resource Manager.
    >
    > Para instalar la versión más reciente, siga los pasos descritos en [Cómo instalar y configurar Azure PowerShell](/powershell/azureps-cmdlets-docs) . Si tiene scripts que se deben modificar para usar los nuevos cmdlets que funcionan con Azure Resource Manager, consulte [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Migración a herramientas de desarrollo basadas en Azure Resource Manager para clústeres de HDInsight) para más información.

    ```powershell
    $clusterName = "clustername"
    # Giraph examples jar
    $jarFile = "wasb:///example/jars/giraph-examples.jar"
    # Arguments for this job
    $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
                    "-ca", "mapred.job.tracker=headnodehost:9010",
                    "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
                    "-vip", "wasb:///example/data/tiny_graph.txt",
                    "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
                    "-op",  "wasb:///example/output/shortestpaths",
                    "-w", "2"
    # Create the definition
    $jobDefinition = New-AzureHDInsightMapReduceJobDefinition
        -JarFile $jarFile
        -ClassName "org.apache.giraph.GiraphRunner"
        -Arguments $jobArguments

    # Run the job, write output to the Azure PowerShell window
    $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
    Write-Host "Wait for the job to complete ..." -ForegroundColor Green
    Wait-AzureHDInsightJob -Job $job
    Write-Host "STDERR"
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput
    ```

    En el ejemplo anterior, reemplace **clustername** por el nombre del clúster de HDInsight donde está instalado Giraph.
3. Vea los resultados. Una vez finalizado el trabajo, los resultados se almacenarán en dos archivos de salida en la carpeta **wasb:///example/out/shotestpaths**. Los archivos se llaman **part-m-00001** y **part-m-00002**. Realice los pasos siguientes para descargar y ver el resultado:

    ```powershell
    $subscriptionName = "<SubscriptionName>"       # Azure subscription name
    $storageAccountName = "<StorageAccountName>"   # Azure Storage account name
    $containerName = "<ContainerName>"             # Blob storage container name

    # Select the current subscription
    Select-AzureSubscription $subscriptionName

    # Create the Storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

    # Download the job output to the workstation
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
    Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force
    ```

    Esto creará la estructura de directorios **example/output/shortestpaths** en el directorio actual de la estación de trabajo y se descargarán los dos archivos de salida a esa ubicación.

    Use el cmdlet **Cat** para mostrar el contenido de los archivos:

        Cat example/output/shortestpaths/part*

    La salida debe ser similar a la siguiente:

        0    1.0
        4    5.0
        2    2.0
        1    0.0
        3    1.0

    El ejemplo SimpleShortestPathComputation se ha codificado de forma rígida para comenzar por el identificador de objeto 1 y encontrar la ruta más corta a los demás objetos. Así que la salida se debe leer como `destination_id distance`, donde la distancia es el valor (o peso) de los bordes recorridos entre el identificador de objeto 1 y el identificador de destino.

    Visualizando esto, puede verificar los resultados recorriendo las rutas más cortas entre el Id. 1 y todos los demás objetos. Tenga en cuenta que la ruta más corta entre el Id. 1 y el Id. 4 es 5. Esta es la distancia total entre el <span style="color:orange">identificador 1 y 3</span>, y, a continuación, <span style="color:red">identificador 3 y 4</span>.

    ![Drawing of objects as circles with shortest paths drawn between](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)

## <a name="install-giraph-using-azure-powershell"></a>Instalación de Giraph con Azure PowerShell
Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md).  El ejemplo muestra cómo instalar Apache Spark mediante Azure PowerShell. Debe personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="install-giraph-using-net-sdk"></a>Instalación de Giraph mediante .NET SDK
Consulte [Personalización de clústeres de HDInsight mediante la acción de scripts](hdinsight-hadoop-customize-cluster-linux.md). El ejemplo muestra cómo instalar Spark con .NET SDK. Debe personalizar el script para usar [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1).

## <a name="see-also"></a>Vea también
* [Instalación de Apache Giraph en clústeres de Hadoop en HDInsight (Linux)](hdinsight-hadoop-giraph-install-linux.md)
* [Creación de clústeres de Apache Hadoop en HDInsight](hdinsight-provision-clusters.md): información general acerca de la creación de clústeres de HDInsight.
* [Personalizar un clúster de HDInsight mediante acciones de Script](hdinsight-hadoop-customize-cluster-linux.md): información general sobre la personalización de clústeres de HDInsight mediante la acción de secuencia de comandos.
* [Desarrollo de acciones de script con HDInsight](hdinsight-hadoop-script-actions.md).
* [Instalación y uso de Apache Spark en clústeres de HDInsight][hdinsight-install-spark]: Ejemplo de acción de script acerca de la instalación de Spark.

[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: https://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
