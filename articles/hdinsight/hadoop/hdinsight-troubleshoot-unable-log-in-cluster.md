---
title: No se puede registrar en el clúster de Azure HDInsight
description: Solución de los problemas por los que no se puede iniciar sesión en el clúster de Apache Hadoop en Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/31/2019
ms.openlocfilehash: c4abc8a1b8d81f4ed0ef7e1c3b37c4e0e755c1b8
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71087259"
---
# <a name="scenario-unable-to-log-into-azure-hdinsight-cluster"></a>Escenario: No se puede registrar en el clúster de Azure HDInsight

En este artículo se describen los pasos de solución de problemas y las posibles soluciones para los problemas que se producen al usar clústeres de Azure HDInsight.

## <a name="issue"></a>Problema

No se puede registrar en el clúster de Azure HDInsight.

## <a name="cause"></a>Causa

Los motivos pueden variar. Al iniciar sesión en los paneles de clúster o la aplicación, recuerde usar el "inicio de sesión de clúster" o las credenciales de HTTP. Cuando se conecte remotamente, use sus credenciales de Shell seguro (SSH) o del Escritorio remoto.

## <a name="resolution"></a>Resolución

Para resolver problemas habituales, pruebe uno o varios de los pasos siguientes.

* Intente abrir el panel del clúster en una nueva pestaña del explorador en modo de privacidad.

* Para los clústeres Linux, si no puede recuperar las credenciales de SSH, puede [restablecer las credenciales en la interfaz de usuario de Ambari](../hdinsight-administer-use-portal-linux.md#change-passwords).

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure mediante el [soporte técnico de la comunidad de Azure](https://azure.microsoft.com/support/community/).

* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport), la cuenta oficial de Microsoft Azure para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.

* Si necesita más ayuda, puede enviar una solicitud de soporte técnico desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Seleccione **Soporte técnico** en la barra de menús o abra la central **Ayuda + soporte técnico**. Para obtener información más detallada, revise [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). La suscripción a Microsoft Azure incluye acceso al soporte técnico para facturación y administración de suscripciones. El soporte técnico se proporciona a través de uno de los [planes de soporte técnico de Azure](https://azure.microsoft.com/support/plans/).
