---
title: 'RpcTimeoutException para Thrift de Apache Spark: Azure HDInsight'
description: Al procesar conjuntos de datos grandes mediante el servidor Thrift de Apache Spark verá 502 errores.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c9e71c745d62432af3c0fe035d28009e3e5be761
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241036"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Escenario: RpcTimeoutException para el servidor Thrift de Apache Spark en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar componentes de Apache Spark en clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Se produce un error `org.apache.spark.rpc.RpcTimeoutException` en la aplicación Spark con el mensaje `Futures timed out`, como en el ejemplo siguiente:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

Los errores `OutOfMemoryError` y `overhead limit exceeded` también pueden aparecer en `sparkthriftdriver.log`, como en el ejemplo siguiente:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Causa

Estos errores se deben a una falta de recursos de memoria durante el procesamiento de datos. Si se inicia el proceso de recolección de elementos no utilizados de Java, la aplicación Spark podría bloquearse. Las consultas comenzarán a agotar el tiempo de espera y el procesamiento se detendrá. El error `Futures timed out` indica que un clúster está sometido a una sobrecarga severa.

## <a name="resolution"></a>Resolución

Aumente el tamaño del clúster; para hacerlo, agregue más nodos de trabajo o incremente la capacidad de memoria en los nodos de clúster existentes. También puede ajustar la canalización de datos para reducir la cantidad de datos que se procesan de manera simultánea.

`spark.network.timeout` controla el tiempo de espera de todas las conexiones de red. El aumento del tiempo de espera de la red puede conceder más tiempo para que se completen algunas operaciones críticas, pero no resolverá el problema por completo.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
