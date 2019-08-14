---
title: Error "watchdog BUG soft lockup - CPU" desde un clúster de Azure HDInsight
description: El error "watchdog BUG soft lockup - CPU" aparece en registros de syslog del kernel
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/05/2019
ms.openlocfilehash: 9278c174d96cb6b1823c8dbfdcba197b7a3c05cc
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828872"
---
# <a name="scenario-watchdog-bug-soft-lockup---cpu-error-from-an-azure-hdinsight-cluster"></a>Escenario: error "watchdog: BUG: soft lockup - CPU" desde un clúster de Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

Los registros de syslog del kernel contienen el mensaje de error: `watchdog: BUG: soft lockup - CPU`.

## <a name="cause"></a>Causa

Un [error](https://bugzilla.kernel.org/show_bug.cgi?id=199437) en el kernel de Linux está provocando bloqueos de software de la CPU.

## <a name="resolution"></a>Resolución

Aplique una revisión del kernel. El siguiente script actualiza el kernel de Linux y reinicia las máquinas en distintos momentos durante 24 horas. Ejecute la acción de script en dos lotes. El primer lote está en todos los nodos excepto en el nodo principal. El segundo lote está en el nodo principal. No ejecute lotes en el nodo principal y en otros nodos al mismo tiempo.

1. Vaya al clúster de HDInsight en Azure Portal.

1. Vaya a las acciones de script.

1. Seleccione **Enviar nuevo** y escriba la entrada como se indica a continuación.

    | Propiedad | Valor |
    | --- | --- |
    | Tipo de script | -Custom |
    | NOMBRE |Fix for kernel soft lock issue |
    | URI de script de Bash |`https://raw.githubusercontent.com/hdinsight/hdinsight.github.io/master/ClusterCRUD/KernelSoftLockFix/scripts/KernelSoftLockIssue_FixAndReboot.sh` |
    | Tipos de nodo |Worker, Zookeeper |
    | Parámetros |N/D |

    Seleccione **Conservar esta acción de script...** si desea que el script se ejecute cuando se agreguen nuevos nodos.

1. Seleccione **Crear**.

1. Espere a que la ejecución se realice correctamente.

1. Ejecute la acción de script en el nodo principal con los mismos datos que en el paso 3, pero esta vez especifique Head para Tipos de nodo.

1. Espere a que la ejecución se realice correctamente.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener más información, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
