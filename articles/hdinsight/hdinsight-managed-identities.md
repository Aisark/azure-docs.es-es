---
title: Identidades administradas en Azure HDInsight
description: Proporciona información general de la implementación de las identidades administradas en Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 02ea164a1fa29b494801623d418be73fc47d069c
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077078"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Identidades administradas en Azure HDInsight

Una identidad administrada es una identidad registrada en Azure Active Directory (Azure AD) cuyas credenciales son administradas por Azure. Con las identidades administradas, no es necesario registrar las entidades de servicio en Azure AD ni mantener credenciales, tales como los certificados.

Las identidades administradas pueden utilizarse en Azure HDInsight para permitir que los clústeres accedan a los servicios de dominio de Azure AD, a Azure Key Vault o a archivos en Azure Data Lake Storage Gen2.

Hay dos tipos de identidades administradas: asignadas por el sistema y asignadas por el usuario. Azure HDInsight usa las identidades administradas asignadas por el usuario. Una identidad administrada asignada por el usuario se crea como un recurso independiente de Azure que, a continuación, se puede asignar a una o varias instancias de servicio de Azure. En cambio, una identidad administrada asignada por el sistema se crea en Azure AD y, a continuación, se habilita directamente en una instancia en particular de un servicio de Azure de forma automática. La vida útil de esa identidad administrada asignada por el sistema se asocia entonces a la vida de la instancia de servicio en la que se ha habilitado.

## <a name="hdinsight-managed-identity-implementation"></a>Implementación de la identidad administrada de HDInsight

En Azure HDInsight, las identidades administradas se aprovisionan en cada nodo del clúster. Estos componentes de identidad, sin embargo, solo son utilizables por el servicio HDInsight. Actualmente no hay ningún método admitido para generar tokens de acceso con las identidades administradas instaladas en los nodos de clúster de HDInsight. Para algunos servicios de Azure, las identidades administradas se implementan con un punto de conexión que puede usar para adquirir tokens de acceso para interactuar con otros servicios de Azure por su cuenta.

## <a name="create-a-managed-identity"></a>Creación de una entidad administrada

Las identidades administradas se pueden crear con cualquiera de los métodos siguientes:

* [Azure Portal](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [CLI de Azure](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

Los pasos restantes para configurar la identidad administrada dependen del escenario donde se vaya a usar.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Escenarios de identidades administradas en Azure HDInsight

Las identidades administradas se usan en Azure HDInsight en varios escenarios. Consulte en los documentos relacionados las instrucciones detalladas para la instalación y la configuración:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md#create-a-user-assigned-managed-identity)
* [Paquete de seguridad de la empresa](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Kafka Bring Your Own Key (BYOK)](kafka/apache-kafka-byok.md#get-started-with-byok)

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué son las identidades administradas de los recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md)
