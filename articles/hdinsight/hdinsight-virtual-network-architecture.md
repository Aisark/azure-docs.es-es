---
title: Arquitectura de red virtual de Azure HDInsight
description: Obtenga información sobre los recursos disponibles al crear un clúster de HDInsight en una red virtual de Azure.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: hrasheed
ms.openlocfilehash: 41420497bffd0abdc598e4c86b2dbda1466b2ce1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66252844"
---
# <a name="azure-hdinsight-virtual-network-architecture"></a>Arquitectura de red virtual de Azure HDInsight

En este artículo se explican los recursos que están presentes cuando se implementa un clúster de HDInsight en una red virtual de Azure personalizada. Esta información le resultará útil para conectar los recursos locales al clúster de HDInsight en Azure. Para más información sobre las redes virtuales de Azure, vea [¿Qué es Azure Virtual Network?](../virtual-network/virtual-networks-overview.md).

## <a name="resource-types-in-azure-hdinsight-clusters"></a>Tipos de recursos en clústeres de HDInsight de Azure

Los clústeres de Azure HDInsight tienen distintos tipos de máquinas virtuales o nodos. Cada tipo de nodo desempeña un papel en el funcionamiento del sistema. En la tabla siguiente se resumen estos tipos de nodo y sus roles en el clúster.

| Type | DESCRIPCIÓN |
| --- | --- |
| Nodo principal |  Para todos los tipos de clúster excepto Apache Storm, los nodos principales hospedan los procesos que administran la ejecución de la aplicación distribuida. El nodo principal es también el nodo al que puede acceder mediante SSH y ejecutar aplicaciones que después se coordinan para ejecutarse en los recursos de clúster. El número de nodos principales se fija en dos para los tipos de clúster. |
| Nodo de ZooKeeper | ZooKeeper coordina las tareas entre los nodos que realizan el procesamiento de datos. También realiza la elección de líder del nodo principal y realiza un seguimiento de qué nodo principal ejecuta un servicio maestro específico. El número de nodos de ZooKeeper se fija en tres. |
| Nodo de trabajo | Representa los nodos que admiten la funcionalidad de procesamiento de datos. Los nodos de trabajo se pueden agregar o quitar del clúster para escalar la funcionalidad de computación y administrar los costos. |
| Nodo perimetral de R Server | El nodo perimetral de R Server representa el nodo al que puede acceder mediante SSH y ejecutar aplicaciones que después se coordinan para ejecutarse en los recursos de clúster. El nodo perimetral no participa en el análisis de datos dentro del clúster. Este nodo también hospeda R Studio Server, lo que permite ejecutar la aplicación de R con un explorador. |
| Nodo regional | Para el tipo de clúster de HBase, el nodo regional (también denominado un nodo de datos) ejecuta el servidor de regiones. Los servidores de regiones atienden y administran una parte de los datos administrados por HBase. Los nodos regionales se pueden agregar o quitar del clúster para escalar la funcionalidad de computación y administrar los costos.|
| Nodo Nimbus | Para el tipo de clúster de Storm, el nodo Nimbus proporciona una funcionalidad similar a la del nodo principal. El nodo de Nimbus asigna tareas a otros nodos de un clúster mediante ZooKeeper, que coordina la ejecución de topologías de Storm. |
| Nodo de supervisión | Para el tipo de clúster de Storm, el nodo de supervisor ejecuta las instrucciones que el nodo Nimbus proporciona para realizar el procesamiento deseado. |

## <a name="basic-virtual-network-resources"></a>Recursos de red virtual básicos

El siguiente diagrama muestra la colocación de los nodos de HDInsight y los recursos de red en Azure.

![Diagrama de entidades de HDInsight creadas en una red virtual personalizada de Azure](./media/hdinsight-virtual-network-architecture/vnet-diagram.png)

Los recursos predeterminados presentes cuando HDInsight se implementa en una red virtual de Azure incluyen los tipos de nodo de clúster que se han mencionado en la tabla anterior, así como los dispositivos de red que admiten la comunicación entre la red virtual y las redes externas.

En la tabla siguiente se resumen los nueve nodos de clúster que se crean cuando HDInsight se implementa en una red virtual de Azure personalizada.

| Tipo de recurso | Número presente | Detalles |
| --- | --- | --- |
|Nodo principal | two |    |
|Nodo de Zookeeper | three | |
|Nodo de trabajo | two | Este número puede variar en función de la configuración del clúster y el escalado. Se necesitan tres nodos de trabajo como mínimo para Apache Kafka.  |
|Nodo de puerta de enlace | two | Los nodos de puerta de enlace son máquinas virtuales de Azure que se crean en Azure, pero no están visibles en la suscripción. Póngase en el servicio de soporte técnico si necesita reiniciar estos nodos. |

Los siguientes recursos de red presentes se crean automáticamente dentro de la red virtual usada con HDInsight:

| Recursos de redes | Número presente | Detalles |
| --- | --- | --- |
|Equilibrador de carga | three | |
|Interfaces de red | nueve | Este valor se basa en un clúster normal, donde cada nodo tiene su propia interfaz de red. Las nueve interfaces son para los dos nodos principales, tres nodos de ZooKeeper, dos nodos de trabajo y dos nodos de puerta de enlace que se mencionan en la tabla anterior. |
|Direcciones IP públicas | two |    |

## <a name="endpoints-for-connecting-to-hdinsight"></a>Puntos de conexión para conectarse a HDInsight

Puede acceder al clúster de HDInsight de tres formas:

- Un punto de conexión HTTPS fuera de la red virtual en `CLUSTERNAME.azurehdinsight.net`.
- Un punto de conexión SSH para conectarse directamente al nodo principal en `CLUSTERNAME-ssh.azurehdinsight.net`.
- Un punto de conexión HTTPS dentro de la red virtual `CLUSTERNAME-int.azurehdinsight.net`. Observe "-int" en esta dirección URL. Este punto de conexión se resolverá en una dirección IP privada en esa red virtual y será inaccesible desde Internet pública.

A estos tres puntos de conexión se les asigna un equilibrador de carga.

Las direcciones IP públicas también se proporcionan a los dos puntos de conexión que permiten la conexión desde fuera de la red virtual.

1. Se asigna una dirección IP pública al equilibrador de carga para que el nombre de dominio completo (FQDN) la use cuando se conecte al clúster desde Internet `CLUSTERNAME.azurehdinsight.net`.
1. La segunda dirección IP pública se utiliza para el nombre de dominio solo SSH `CLUSTERNAME-ssh.azurehdinsight.net`.

## <a name="next-steps"></a>Pasos siguientes

* [Protección del tráfico entrante a los clústeres de HDInsight en una red virtual con un punto de conexión privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
