---
title: Personalización de clústeres de HDInsight con Bootstrap (Azure)
description: Obtenga información acerca de cómo personalizar clústeres de HDInsight con bootstrap.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: a2cd6a59ffd55b826ef00f9e2b5f30808911f507
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359847"
---
# <a name="customize-hdinsight-clusters-using-bootstrap"></a>Personalización de los clústeres de HDInsight con Bootstrap

Es probable que en ocasiones desee establecer los valores de archivos de configuración, que incluyen lo siguiente:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml
* server.properties (kafka-broker configuration)

Hay tres métodos para usar bootstrap:

* Uso de Azure PowerShell
* Uso del SDK de .NET
* Usar plantillas de Azure Resource Manager

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Para obtener información sobre cómo instalar más componentes en el clúster de HDInsight durante la creación, vea:

* [Personalización de clústeres de HDInsight mediante la acción de scripts (Linux)](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="use-azure-powershell"></a>Uso de Azure PowerShell
El siguiente código de PowerShell personaliza una configuración de [Apache Hive](https://hive.apache.org/):

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

New-AzHDInsightCluster `
    -ResourceGroupName $existingResourceGroupName `
    -ClusterName $clusterName `
    -Location $location `
    -ClusterSizeInNodes $clusterSizeInNodes `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -Config $config 
```

En el [Anexo](#appendix-powershell-sample) se incluye un script de PowerShell completamente en uso.

**Para comprobar el cambio:**

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Haga clic en **Clústeres de HDInsight** en el menú de la izquierda. Si no lo ve, haga clic primero en **Todos los servicios**.
3. Haga clic en el clúster que acaba de crear mediante el script de PowerShell.
4. Haga clic en el **Panel** desde la parte superior de la hoja para abrir la IU de Ambari.
5. Haga clic en **Hive** en el menú izquierdo.
6. Haga clic en **HiveServer2** en **Summary** (Resumen).
7. Seleccione la pestaña **Configs** (Configuraciones).
8. Haga clic en **Hive** en el menú izquierdo.
9. Haga clic en la pestaña **Advanced** (Opciones avanzadas).
10. Desplácese hacia abajo y expanda **Advanced hive-site**(Sitio de Hive avanzado).
11. Busque **hive.metastore.client.socket.timeout** en la sección.

Otros ejemplos de cómo personalizar otros archivos de configuración:

```xml
# hdfs-site.xml configuration
$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

# core-site.xml configuration
$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

# mapred-site.xml configuration
$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

# oozie-site.xml configuration
$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
```
Para más información, vea el blog de Azim Uddin titulado [Customizing HDInsight Cluster creation](https://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx)(Personalización de la creación de clústeres de HDInsight).

## <a name="use-net-sdk"></a>Uso del SDK de .NET
Consulte [Crear clústeres basados en Linux en HDInsight con el SDK de .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md#use-bootstrap).

## <a name="use-resource-manager-template"></a>Uso de plantillas de Resource Manager
Puede usar Bootstrap en la plantilla de Resource Manager:

```json
"configurations": {
    �
    "hive-site": {
        "hive.metastore.client.connect.retry.delay": "5",
        "hive.execution.engine": "mr",
        "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
    }
}
```

![HDInsight Hadoop personaliza la plantilla de Azure Resource Manager de Bootstrap del clúster](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)

## <a name="see-also"></a>Vea también
* En [Creación de clústeres de Apache Hadoop en HDInsight][hdinsight-provision-cluster] se proporcionan instrucciones sobre cómo crear un clúster de HDInsight con otras opciones personalizadas.
* [Desarrollo de acciones de script con HDInsight][hdinsight-write-script]
* [Instalación y uso de Apache Spark en clústeres de HDInsight][hdinsight-install-spark]
* [Instalación y uso de Apache Giraph en clústeres de HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-hadoop-provision-linux-clusters.md
[powershell-install-configure]: /powershell/azureps-cmdlets-docs


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante la creación del clúster"

## <a name="appendix-powershell-sample"></a>Apéndice: Ejemplo de PowerShell
Este script de PowerShell crea un clúster de HDInsight y personaliza una configuración de Hive:

```powershell
####################################
# Set these variables
####################################
#region - used for creating Azure service names
$nameToken = "<ENTER AN ALIAS>" 
#endregion

#region - cluster user accounts
$httpUserName = "admin"  #HDInsight cluster username
$httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

$sshUserName = "sshuser" #HDInsight ssh user name
$sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
#endregion

####################################
# Service names and varialbes
####################################
#region - service names
$namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

$resourceGroupName = $namePrefix + "rg"
$hdinsightClusterName = $namePrefix + "hdi"
$defaultStorageAccountName = $namePrefix + "store"
$defaultBlobContainerName = $hdinsightClusterName

$location = "East US 2"
#endregion

# Treat all errors as terminating
$ErrorActionPreference = "Stop"

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzContext}
catch{Connect-AzAccount}
#endregion

#region - Create an HDInsight cluster
####################################
# Create dependent components
####################################
Write-Host "Creating a resource group ..." -ForegroundColor Green
New-AzResourceGroup `
    -Name  $resourceGroupName `
    -Location $location

Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $defaultStorageAccountName `
    -Location $location `
    -Type Standard_GRS

$defaultStorageAccountKey = (Get-AzStorageAccountKey `
                                -ResourceGroupName $resourceGroupName `
                                -Name $defaultStorageAccountName)[0].Value
$defaultStorageContext = New-AzStorageContext `
                                -StorageAccountName $defaultStorageAccountName `
                                -StorageAccountKey $defaultStorageAccountKey
New-AzStorageContainer `
    -Name $defaultBlobContainerName `
    -Context $defaultStorageContext #use the cluster name as the container name

####################################
# Create a configuration object
####################################
$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }

$config = New-AzHDInsightClusterConfig `
    | Set-AzHDInsightDefaultStorage `
        -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -StorageAccountKey $defaultStorageAccountKey `
    | Add-AzHDInsightConfigValues `
        -HiveSite $hiveConfigValues 

####################################
# Create an HDInsight cluster
####################################
$httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
$httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

$sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
$sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

New-AzHDInsightCluster `
    -ResourceGroupName $resourceGroupName `
    -ClusterName $hdinsightClusterName `
    -Location $location `
    -ClusterSizeInNodes 1 `
    -ClusterType Hadoop `
    -OSType Linux `
    -Version "3.6" `
    -HttpCredential $httpCredential `
    -SshCredential $sshCredential `
    -Config $config

####################################
# Verify the cluster
####################################
Get-AzHDInsightCluster -ClusterName $hdinsightClusterName

#endregion
```
