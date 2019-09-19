---
title: Tiempos de espera con el comando "hbase hbck" en Azure HDInsight
description: Problema de tiempo de espera con el comando "hbase hbck" al corregir las asignaciones de región
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/16/2019
ms.openlocfilehash: 800182498ab77993d0861c9b5383e0d71b302b6f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091570"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>Escenario: Tiempos de espera con el comando "hbase hbck" en Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Se producen tiempos de espera con el comando `hbase hbck` al corregir las asignaciones de regiones.

## <a name="cause"></a>Causa

Una posible causa de problemas de tiempo de espera cuando se usa el comando `hbck` puede ser que varias regiones estén en el estado "en transición" durante mucho tiempo. En la interfaz de usuario maestra de HBase, dichas regiones se pueden ver como sin conexión. Dado que un elevado número de regiones intentan realizar la transición, HBase Master puede superar el tiempo de espera, lo que impediría que dichas regiones vuelvan a estar en línea.

## <a name="resolution"></a>Resolución

1. Inicie sesión en el clúster de HDInsight HBase mediante SSH.

1. Ejecute el comando `hbase zkcli` para conectarse al shell de Apache ZooKeeper.

1. Ejecute el comando `rmr /hbase/regions-in-transition` o `rmr /hbase-unsecure/regions-in-transition`.

1. Salga del shell `hbase zkcli` mediante el comando `exit`.

1. En la interfaz de usuario de Apache Ambari, reinicie el servicio Active HBase Master.

1. Ejecute el comando `hbase hbck -fixAssignments`.

1. Supervise la "región en transición" de la interfaz de usuario de HBase Master para asegurarse de que no se bloquee ninguna región.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

- Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

- Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente. Esta cuenta pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

- Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
