---
title: Niveles de coherencia y API de Azure Cosmos DB
description: Descripción de los niveles de coherencia entre las API de Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: c72c65c9d27810ce4ec2df0062dc41d83ed37a6b
ms.sourcegitcommit: b5d59c6710046cf105236a6bb88954033bd9111b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2019
ms.locfileid: "74559099"
---
# <a name="consistency-levels-and-azure-cosmos-db-apis"></a>Niveles de coherencia y API de Azure Cosmos DB

Azure Cosmos DB también proporciona compatibilidad nativa con las API compatibles con el protocolo de conexión para bases de datos populares. Entre ellas se incluyen MongoDB, Apache Cassandra, Gremlin y Azure Table Storage. Estas bases de datos no ofrecen modelos de coherencia definidos con precisión ni garantías con respaldo de SLA para los niveles de coherencia. Suelen proporcionar únicamente un subconjunto de los cinco modelos de coherencia que ofrece Azure Cosmos DB. 

Al usar la API de SQL, la API de Gremlin y Table API, se usa el nivel de coherencia predeterminado configurado en la cuenta de Azure Cosmos. 

Al usar Cassandra API o la API de Azure Cosmos DB para MongoDB, las aplicaciones obtienen un conjunto completo de los niveles de coherencia que ofrece Apache Cassandra y MongoDB, respectivamente, con garantías de coherencia y durabilidad más fuertes. En este documento se muestran los niveles de coherencia de Azure Cosmos DB correspondientes para los niveles de coherencia de Apache Cassandra y MongoDB.


## <a id="cassandra-mapping"></a>Asignación entre niveles de coherencia de Apache Cassandra y Azure Cosmos DB

A diferencia de Azure Cosmos DB, Apache Cassandra no proporciona de forma nativa garantías de coherencia definidas con precisión.  En su lugar, Cassandra Apache proporciona un nivel de coherencia de escritura y de lectura para permitir los inconvenientes que representan la alta disponibilidad, coherencia, disponibilidad y latencia. Al usar Cassandra API de Azure Cosmos DB: 

* El nivel de coherencia de escritura de Apache Cassandra se asigna al nivel de coherencia predeterminado configurado en la cuenta de Azure Cosmos. 

* Azure Cosmos DB asignará dinámicamente el nivel de coherencia de lectura que el controlador de cliente de Cassandra especifique en uno de los niveles de coherencia de Azure Cosmos DB configurados dinámicamente en una solicitud de lectura. 

En la tabla siguiente se muestra cómo se asignan los niveles de coherencia nativos de Cassandra para los niveles de coherencia de Azure Cosmos DB cuando se usa Cassandra API:  

[![Asignación de modelos de coherencia de Cassandra](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png)](./media/consistency-levels-across-apis/consistency-model-mapping-cassandra.png#lightbox)

## <a id="mongo-mapping"></a>Asignación entre los niveles de coherencia de MongoDB y Azure Cosmos DB

A diferencia de Azure Cosmos DB, el nivel de coherencia nativo de MongoDB no proporciona garantías de coherencia definidas con precisión. En su lugar, MongoDB nativo permite a los usuarios configurar las siguientes garantías de coherencia (una preocupación de escritura, una preocupación de lectura y la directiva isMaster) para dirigir las operaciones de lectura a las réplicas principales o secundarias y lograr el nivel de coherencia deseado. 

Cuando se usa la API de Azure Cosmos DB para MongoDB, el controlador de MongoDB trata la región de escritura como la réplica principal y todas las demás regiones son réplicas de lectura. Puede elegir qué región asociada con su cuenta de Azure Cosmos como una réplica principal. 

Al usar la API de Azure Cosmos DB para MongoDB:

* la preocupación de escritura se asigna al nivel de coherencia predeterminado configurado en la cuenta de Azure Cosmos.
 
* Azure Cosmos DB asignará dinámicamente la preocupación de lectura que el controlador de cliente de MongoDB especifique en uno de los niveles de coherencia de Azure Cosmos DB configurados dinámicamente en una solicitud de lectura. 

* Puede anotar una región específica asociada con su cuenta de Azure Cosmos como "Maestra" al establecer la región como la primera región de escritura. 

En la tabla siguiente se muestra cómo las preocupaciones de lectura o escritura nativas de MongoDB se asignan a los niveles de coherencia de Azure Cosmos cuando se usa la API de Azure Cosmos DB para MongoDB:

[![Asignación de modelos de coherencia de MongoDB](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png)](./media/consistency-levels-across-apis/consistency-model-mapping-mongodb.png#lightbox)

## <a name="next-steps"></a>Pasos siguientes

Lea más acerca de los niveles de coherencia y la compatibilidad entre las API de Azure Cosmos DB con las API de código abierto: Consulte los artículos siguientes:

* [Availability and performance tradeoffs for various consistency levels](consistency-levels-tradeoffs.md) (Compromisos entre rendimiento y disponibilidad en los distintos niveles de coherencia)
* [Características de MongoDB que admite la API de Azure Cosmos DB para MongoDB](mongodb-feature-support.md)
* [Características de Apache Cassandra admitidas por Cassandra API de Azure Cosmos DB](cassandra-support.md)
