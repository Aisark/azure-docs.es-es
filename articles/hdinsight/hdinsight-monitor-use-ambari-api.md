---
title: Supervisión de clústeres de Hadoop en HDInsight con la API de Ambari (Azure)
description: Use las API de Apache Ambari para crear, administrar y supervisar clústeres de Hadoop. Las API y herramientas de operador intuitivas ocultan la complejidad de Hadoop.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/07/2017
ms.author: hrasheed
ROBOTS: NOINDEX
ms.openlocfilehash: ff6601042c82cef2b0101833117f17aca8b463dc
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/20/2019
ms.locfileid: "58223264"
---
# <a name="monitor-apache-hadoop-clusters-in-hdinsight-using-the-apache-ambari-api"></a>Supervisión de clústeres de Apache Hadoop en HDInsight con la API de Apache Ambari
Aprenda a supervisar clústeres de HDInsight con las API de Apache Ambari.

> [!NOTE]  
> La información de este artículo es principalmente para los clústeres de HDInsight basados en Windows, que proporcionan una versión de solo lectura de la API de REST de Ambari. Para los clústeres basados en Linux, consulte [Administración de clústeres de Apache Hadoop mediante Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="what-is-ambari"></a>¿Qué es Ambari?
[Apache Ambari][ambari-home] sirve para el aprovisionamiento, la administración y la supervisión de clústeres de Apache Hadoop. Incluye una recopilación intuitiva de herramientas de operador y un conjunto sólido de API que ocultan la complejidad de Hadoop y simplifican la operación de clústeres. Para obtener más información sobre las API, consulte [Referencia de API de Ambari][ambari-api-reference]. 

HDInsight actualmente solo es compatible con la característica de supervisión de Ambari. La API de Ambari v1.1.0 es compatible con los clústeres de las versiones 3.0 y 2.1 de HDInsight. Este artículo abarca el acceso a las API de Ambari en clústeres de las versiones 3.1 y 2.1 de HDInsight. La principal diferencia entre las dos es que algunos componentes han cambiado con la incorporación de nuevas funciones (como el servidor de historial de trabajos). 

**Requisitos previos**

Antes de empezar este tutorial, debe contar con lo siguiente:

* **Una estación de trabajo con Azure PowerShell**.
* (Opcional) [cURL][curl]. Para instalarlo, consulte [Descargas y versiones de cURL][curl-download].
  
  > [!NOTE]  
  > Cuando utilice el comando cURL en Windows, utilice comillas dobles en lugar de comillas simples para los valores de opción.

* **Un clúster de HDInsight de Azure**. Para obtener instrucciones sobre el aprovisionamiento del clúster, consulte [Introducción al uso de HDInsight][hdinsight-get-started] o [Aprovisionamiento de clústeres de HDInsight][hdinsight-provision]. Para completar el tutorial, necesita los datos siguientes:
  
  | Propiedad del clúster | Nombre de variable de Azure PowerShell | Valor | DESCRIPCIÓN |
  | --- | --- | --- | --- |
  |   Nombre del clúster de HDInsight |$clusterName | |El nombre del clúster de HDInsight. |
  |   Nombre de usuario del clúster |$clusterUsername | |Nombre de usuario del clúster especificado cuando se creó el clúster. |
  |   Contraseña de clúster |$clusterPassword | |Contraseña de usuario de clúster |

[!INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


## <a name="jump-start"></a>Jump-start
Existen varias maneras de usar Ambari para supervisar clústeres de HDInsight.

**Uso de Azure PowerShell**

El siguiente es un script de Azure PowerShell para obtener la información del seguimiento de trabajos de MapReduce *en un clúster de HDInsight 3.5*.   La principal diferencia aquí es que extraemos estos detalles del servicio YARN (en lugar de MapReduce).

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName/services/YARN/components/RESOURCEMANAGER"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'yarn.queueMetrics'

El siguiente es un script de PowerShell para obtener la información del seguimiento de trabajos de MapReduce *en un clúster de HDInsight 2.1*:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus

    $response.metrics.'mapred.JobTracker'

La salida es la siguiente:

![Salida de seguimiento de trabajo][img-jobtracker-output]

**Uso de cURL**

En el ejemplo siguiente se obtiene información de clúster mediante el uso de cURL:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

La salida es la siguiente:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

**Para la versión del 10/8/2014**:

Cuando se usa el extremo Ambari, "https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname}", el campo *host_name* devuelve ahora el nombre de dominio completo (FQDN) del nodo en lugar de solo el nombre del host. Antes de la versión del 8/10/2014, este ejemplo devolvía simplemente "**headnode0**". Después de la versión del 8/10/2014, se obtiene el FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**", como se muestra en el ejemplo anterior. Este cambio se solicitó para facilitar escenarios donde se pueden implementar varios tipos de clúster (como HBase y Hadoop) en una misma red virtual (VNET). Esto ocurre, por ejemplo, cuando se usa HBase como plataforma de back-end para Hadoop.

## <a name="ambari-monitoring-apis"></a>API de supervisión de Ambari
En la siguiente lista se enumeran algunas de las llamadas a API desde la supervisión de Ambari. Para más información sobre la API, consulte [Referencia de API de Apache Ambari][ambari-api-reference].

| Supervisión de la llamada a la API | URI | DESCRIPCIÓN |
| --- | --- | --- |
| Obtener clústeres |`/api/v1/clusters` | |
| Obtener información de clúster. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net` |clústeres, servicios, hosts |
| Obtener servicios |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services` |Los servicios incluyen: hdfs, mapreduce |
| Obtener información sobre los servicios. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>` | |
| Obtener componentes de servicio |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components` |HDFS: namenode, datanodeMapReduce: jobtracker; tasktracker |
| Obtener información de componente. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/services/<ServiceName>/components/<ComponentName>` |ServiceComponentInfo, componentes de host, métricas |
| Obtener hosts |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts` |headnode0, workernode0 |
| Obtener información sobre host. |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>` | |
| Obtener componentes de host |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components` |namenode, resourcemanager |
| Obtener información de componente de host |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/hosts/<HostName>/host_components/<ComponentName>` |HostRoles, componente, host, métricas |
| Obtener configuraciones |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations` |Tipos de configuración: core-site, hdfs-site, mapred-site, hive-site |
| Obtener información de configuración |`/api/v1/clusters/<ClusterName>.azurehdinsight.net/configurations?type=<ConfigType>&tag=<VersionName>` |Tipos de configuración: core-site, hdfs-site, mapred-site, hive-site |

## <a name="next-steps"></a>Pasos siguientes
Ahora sabe cómo usar las llamadas de API de supervisión de Apache Ambari. Para obtener más información, consulte:

* [Administración de clústeres de Apache Hadoop en HDInsight mediante Azure Portal](hdinsight-administer-use-portal-linux.md)
* [Administrar clústeres de HDInsight con Azure PowerShell][hdinsight-admin-powershell]
* [Administrar clústeres de HDInsight con la interfaz de la línea de comandos][hdinsight-admin-cli]
* [Documentación de HDInsight][hdinsight-documentation]
* [Introducción a HDInsight][hdinsight-get-started]

[ambari-home]: https://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: https://curl.haxx.se
[curl-download]: https://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: https://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[powershell-install]: /powershell/azureps-cmdlets-docs
[powershell-script]: https://technet.microsoft.com/library/ee176949.aspx

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-documentation]: https://docs.microsoft.com/azure/hdinsight/
[hdinsight-get-started]:hadoop/apache-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-hadoop-provision-linux-clusters.md

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
