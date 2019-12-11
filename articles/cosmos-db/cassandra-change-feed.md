---
title: Fuente de cambios de la API de Azure Cosmos DB para Cassandra
description: Aprenda a usar la fuente de cambios en la API de Azure Cosmos DB para MongoDB con el fin de obtener los cambios realizados en sus datos.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694612"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Fuente de cambios de la API de Azure Cosmos DB para Cassandra

La [compatibilidad de la fuente de cambios](change-feed.md) de la API de Azure Cosmos DB con Cassandra está disponible mediante los predicados de consulta del lenguaje de consulta de Cassandra (CQL). Con estas condiciones de predicado, puede consultar la API de la fuente de cambios. Las aplicaciones pueden obtener los cambios realizados en una tabla mediante la clave principal (también conocida como clave de partición), tal y como se requiere en CQL. Más adelante, puede realizar otras acciones en función de los resultados. Los cambios en las filas de la tabla se capturan por orden según su hora de modificación y el criterio de ordenación se garantiza por clave de partición.

En el ejemplo siguiente se muestra cómo obtener una fuente de cambios en todas las filas de una tabla de espacio de claves de Cassandra API mediante .NET. El predicado COSMOS_CHANGEFEED_START_TIME() se utiliza directamente en CQL para consultar elementos en la fuente de cambios a partir de una hora de inicio especificada (en este caso la fecha y hora actuales). Puede descargar desde [aquí](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/) el ejemplo completo.

En cada iteración, la consulta se reanuda en el último punto en el que se leyeron los cambios mediante el estado de paginación. Se observa un flujo continuo de nuevos cambios en la tabla del espacio de claves. Se pueden ver cambios en las filas que se han insertado o actualizado. Actualmente no se admite la inspección de operaciones de eliminación mediante la fuente de cambios de Cassandra API. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Para obtener los cambios realizados en una sola fila por clave principal, puede agregar esa clave a la consulta. En el ejemplo siguiente se muestra cómo realizar un seguimiento de los cambios realizados en la fila en la que "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Limitaciones actuales

Se aplican las siguientes limitaciones al usar la fuente de cambios con Cassandra API:

* Actualmente, se admiten las inserciones y las actualizaciones. Aún no se admite la operación de eliminación. Como solución alternativa, puede agregar un marcador flexible en las filas que se van a eliminar. Por ejemplo, agregue un campo en la fila denominada "deleted" y establézcalo en "true".
* Se conserva la última actualización como en la API principal de SQL y las actualizaciones intermedias de la entidad no están disponibles.


## <a name="error-handling"></a>Control de errores

Se admiten los siguientes mensajes y códigos de error cuando se utiliza la fuente de cambios de Cassandra API:

* **Código de error HTTP 429**: cuando se limita la frecuencia de la fuente de cambios, devuelve una página vacía.

## <a name="next-steps"></a>Pasos siguientes

* [Administración de recursos de Cassandra API de Azure Cosmos DB con plantillas de Azure Resource Manager](manage-cassandra-with-resource-manager.md)
