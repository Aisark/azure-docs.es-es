---
title: Base de datos de Apache Ambari personalizada en Azure HDInsight
description: Aprenda a crear clústeres de HDInsight con su propia base de datos de Apache Ambari personalizada.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/24/2019
ms.author: hrasheed
ms.openlocfilehash: d6dd67e9e0cd9dcd4afb8f1ae2cc32ccf30617fd
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2020
ms.locfileid: "97704952"
---
# <a name="set-up-hdinsight-clusters-with-a-custom-ambari-db"></a>Configuración de clústeres de HDInsight con una base de datos de Ambari personalizada

Apache Ambari simplifica la administración y la supervisión de un clúster de Apache Hadoop, ya que proporciona una interfaz de usuario web y una API REST fáciles de usar. Ambari se incluye en los clústeres de HDInsight y, además, se usa para supervisar el clúster y realizar cambios en la configuración.

En la creación de clústeres normal, como se describe en otros artículos como [Configuración de clústeres en HDInsight](hdinsight-hadoop-provision-linux-clusters.md), Ambari se implementa en una [instancia S0 de Azure SQL Database](../azure-sql/database/resource-limits-dtu-single-databases.md#standard-service-tier) que administra HDInsight y no es accesible para los usuarios.

La característica de bases de datos personalizadas de Ambari permite implementar un nuevo clúster y configurar Ambari en una base de datos externa que usted administra. La implementación se realiza mediante una plantilla de Azure Resource Manager. Esta característica tiene las siguientes ventajas:

- Personalización: puede elegir el tamaño y la capacidad de procesamiento de la base de datos. Si tiene clústeres grandes que procesan cargas de trabajo de uso intensivo, una base de datos de Ambari con especificaciones menores podría convertirse en un cuello de botella para las operaciones de administración.
- Flexibilidad: puede escalar la base de datos según sea necesario para satisfacer sus necesidades.
- Control: puede administrar las copias de seguridad y la seguridad de su base de datos para que se ajusten a los requisitos de su organización.

En el resto de este artículo se tratan los puntos siguientes:

- requisitos para usar la característica de base de datos personalizada de Ambari;
- los pasos necesarios para aprovisionar clústeres de HDInsight con su propia base de datos externa para Apache Ambari.

## <a name="custom-ambari-db-requirements"></a>Requisitos de la base de datos personalizada de Ambari

Puede implementar una base de datos personalizada de Ambari con todos los tipos de clúster y versiones. Varios clústeres no pueden usar la misma base de datos de Ambari.

La base de datos personalizada de Ambari tiene los siguientes requisitos:

- El nombre de la base de datos no puede contener guiones ni espacios
- Debe tener una base de datos y un servidor de Azure SQL Database existentes.
- La base de datos que se proporciona para la configuración de Ambari debe estar vacía. No debe haber ninguna tabla en el esquema DBO predeterminado.
- El usuario utilizado para conectarse a la base de datos debe tener permisos SELECT, CREATE TABLE e INSERT en la base de datos.
- Active la opción [Permitir el acceso a servicios de Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#azure-portal-steps) en el servidor donde hospedará Ambari.
- Las direcciones IP de administración del servicio HDInsight deben permitirse en la regla de firewall. Consulte [Direcciones IP de administración de HDInsight](hdinsight-management-ip-addresses.md) para obtener una lista de las direcciones IP que se deben agregar a la regla de firewall en el nivel de servidor.

Al hospedar la base de datos de Apache Ambari en una base de datos externa, recuerde los puntos siguientes:

- Los costos adicionales de la base de datos de Azure SQL que contiene Ambari son su responsabilidad.
- Realice una copia de la base de datos personalizada de Ambari periódicamente. Azure SQL Database genera las copias de seguridad automáticamente, pero el período de tiempo de retención de dichas copias de seguridad varía. Para más información, consulte [más información sobre copias de seguridad automáticas de SQL Database](../azure-sql/database/automated-backups-overview.md).

## <a name="deploy-clusters-with-a-custom-ambari-db"></a>Implementación de clústeres con una base de datos de Ambari personalizada

Para crear un clúster de HDInsight que use su propia base de datos externa de Ambari, use la [plantilla de inicio rápido de la base de datos personalizada de Ambari](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-custom-ambari-db).

Edite los parámetros en el archivo `azuredeploy.parameters.json` para especificar información sobre el nuevo clúster y la base de datos que contendrá Ambari.

Puede comenzar la implementación mediante la CLI de Azure. Reemplace `<RESOURCEGROUPNAME>` por el grupo de recursos en el que quiere implementar el clúster.

```azurecli
az deployment group create --name HDInsightAmbariDBDeployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file azuredeploy.json \
    --parameters azuredeploy.parameters.json
```

## <a name="next-steps"></a>Pasos siguientes

- [Uso de repositorios de metadatos externos en Azure HDInsight](hdinsight-use-external-metadata-stores.md)
