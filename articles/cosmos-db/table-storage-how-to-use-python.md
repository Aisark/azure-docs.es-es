---
title: Introducción a Azure Table Storage y a Table API de Azure Cosmos DB mediante Python
description: Almacene datos estructurados en la nube mediante Azure Table Storage o Table API de Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 04/05/2018
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: 0f0acc721fd8888953d80976234b431943985ebf
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356272"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Introducción a Azure Table Storage y a Table API de Azure Cosmos DB mediante Python

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Azure Table Storage y Azure Cosmos DB son servicios que almacenan datos NoSQL estructurados en la nube y proporcionan un almacén de claves y atributos con un diseño sin esquema. Dado que tanto Table Storage como Azure Cosmos DB carecen de esquema, es fácil adaptar los datos a medida que evolucionan las necesidades de la aplicación. El acceso a los datos tanto de Table Storage como de Table API es rápido y rentable para muchos tipos de aplicaciones y, por lo general, el costo es normalmente menor que con el SQL tradicional en volúmenes similares de datos.

Tanto Table Storage como Azure Cosmos DB se pueden usar para almacenar conjuntos de datos flexibles, como datos de usuarios para aplicaciones web, libretas de direcciones, información de dispositivos u otros tipos de metadatos que el servicio requiera. Una tabla puede almacenar un número cualquiera de entidades y una cuenta de almacenamiento puede incluir un número cualquiera de tablas, hasta alcanzar el límite de capacidad de este tipo de cuenta.

### <a name="about-this-sample"></a>Acerca de este ejemplo
En este ejemplo se muestra cómo usar el [SDK de Table de Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) en algunos escenarios habituales de Azure Table Storage. El nombre del SDK indica que es para su uso con Azure Cosmos DB, pero funciona con Azure Cosmos DB y con Azure Table Storage, cada servicio solo tiene un único punto de conexión. Estos escenarios se describen mediante ejemplos de Python que muestran cómo:
* Crear y eliminar tablas
* Insertar y consultar entidades
* Modificar entidades

Se recomienda que consulte la [referencia de la API del SDK de Azure Cosmos DB para Python](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python) mientras trabaja con los escenarios de este ejemplo.

## <a name="prerequisites"></a>Requisitos previos

Necesitará lo siguiente para completar este ejemplo correctamente:

- [Python](https://www.python.org/downloads/) 2.7, 3.3, 3.4, 3.5 o 3.6
- [SDK de Table de Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Este SDK se conecta con Azure Table Storage y con Table API de Azure Cosmos DB.
- [Cuenta de Azure Storage](../storage/common/storage-quickstart-create-account.md) o [Cuenta de Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Creación de una cuenta de servicio de Azure
[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Creación de una cuenta de Azure Storage
[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Creación de una cuenta de Table API de Azure Cosmos DB
[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalación del SDK de Table de Azure Cosmos DB para Python

Cuando haya creado una cuenta de Storage, el paso siguiente consiste en instalar el [SDK de Table de Azure Cosmos DB para Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Para obtener más información sobre la instalación del SDK, consulte el archivo [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) del SDK de Table de Cosmos DB para el repositorio de Python en GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importación de las clases Entity y TableService

Para trabajar con entidades en Azure Table service de Python, use las clases [TableService][py_TableService] y and [Entity][py_Entity]. Agregue este código cerca de la parte superior del archivo de Python para importar ambos:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Conexión a Azure Table service

Para conectarse a Azure Storage Table service, cree un objeto [TableService][py_TableService] y pase la clave de cuenta y el nombre de la cuenta de Storage. Reemplace `myaccount` y `mykey` por el nombre y la clave de la de cuenta.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Conexión a Azure Cosmos DB

Para conectarse a Azure Cosmos DB, copie la cadena de conexión principal de Azure Portal y cree un objeto [TableService][py_TableService] con la cadena de conexión que ha copiado:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;)
```

## <a name="create-a-table"></a>Creación de una tabla

Llame a [create_table][py_create_table] para crear la tabla.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Adición de una entidad a una tabla

Para agregar una entidad, cree primero un objeto que la represente, pase el objeto al método [TableService.insert_entity][py_TableService]. The entity object can be a dictionary or an object of type [Entity][py_Entity] y defina los nombres y los valores de las propiedades de la entidad. Cada entidad debe incluir las propiedades [PartitionKey y RowKey](#partitionkey-and-rowkey) necesarias, además de cualquier otra propiedad que se defina para la entidad. En este ejemplo se crea un objeto de diccionario que representa una entidad y, a continuación, lo pasa al método [insert_entity][py_insert_entity] para agregarlo a la tabla:

En este ejemplo se crea un método [Entity][py_Entity] object, then passes it to the [insert_entity][py_insert_entity] para agregarlo a la tabla:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

PartitionKey y RowKey

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>Para cada entidad debe especificar una propiedad **PartitionKey** y **RowKey**.

Estos son los identificadores únicos de las entidades y juntos forman la clave principal de una entidad. Puede realizar consultas mediante estos valores de forma mucho más rápida que si consulta cualquier otra propiedad de la entidad ya que estas propiedades son las únicas que se indexan. Table service usa **PartitionKey** para distribuir de forma inteligente las entidades de tabla entre los nodos de almacenamiento.

Las entidades con la misma **PartitionKey** se almacenan en el mismo nodo. **RowKey** es el identificador exclusivo de la entidad de la partición a la que pertenece. Actualización de una entidad

## <a name="update-an-entity"></a>Para actualizar todos los valores de propiedad de una entidad, llame al método [update_entity][py_update_entity].

Este ejemplo muestra cómo reemplazar una entidad existente con una versión actualizada: Si la entidad que se está actualizando no existe, la operación de actualización generará un error.

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Si desea almacenar una entidad independientemente de si existe o no, use [insert_or_replace_entity][py_insert_or_replace_entity]. En el siguiente ejemplo, la primera llamada reemplazará la entidad existente. La segunda llamada insertará una nueva entidad, debido a que en la tabla no existe ninguna entidad con PartitionKey y RowKey especificados. El método [update_entity][py_update_entity] reemplaza todas las propiedades y los valores de una entidad existente y también se puede usar para quitar las propiedades de otra de ellas.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Puede usar el método [merge_entity][py_merge_entity] para actualizar una entidad existente con valores de propiedad nuevos o modificados sin reemplazar completamente la entidad. Modificación de varias entidades

## <a name="modify-multiple-entities"></a>Para garantizar el procesamiento atómico de una solicitud por parte de Table service, puede enviar varias operaciones en un lote.

En primer lugar, use la clase [TableBatch][py_TableBatch] para agregar varias operaciones a un único lote. A continuación, llame a [TableService][py_TableService].[commit_batch][py_commit_batch] para enviar las operaciones de una operación atómica. Todas las entidades que desea modificar del lote deben estar en la misma partición. En este ejemplo se agregan dos entidades juntas en un lote:

Los lotes también pueden usarse con la sintaxis de administrador de contexto:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Consulta de una entidad

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Para consultar una entidad de una tabla, pase sus propiedades PartitionKey y RowKey al método [TableService][py_TableService].[get_entity][py_get_entity].

Consulta de un conjunto de entidades

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Puede consultar un conjunto de entidades proporcionando una cadena de filtro con el parámetro **filter**.

En este ejemplo se buscan todas las tareas en Seattle mediante la aplicación de un filtro en PartitionKey: Consulta de un subconjunto de propiedades de las entidades

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>También puede restringir qué propiedades se devuelven para cada entidad en una consulta.

Esta técnica, denominada *proyección*, reduce el ancho de banda y puede mejorar el rendimiento de las consultas, en especial en el caso de entidades o conjuntos de resultados de gran tamaño. Use el parámetro **select** y pase los nombres de las propiedades que desea que se devuelvan al cliente. La consulta del código siguiente devuelve solo las descripciones de las entidades de la tabla.

El siguiente fragmento de código funciona solo con Azure Storage.

> [!NOTE]
> Esto no es compatible con el emulador de almacenamiento. Eliminación de una entidad

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Elimine una entidad pasando sus propiedades **PartitionKey** y **RowKey** al método [delete_entity][py_delete_entity].

Eliminación de una tabla

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Si ya no necesita una tabla ni ninguna de las entidades dentro de ella, llame al método [delete_table][py_delete_table] para eliminar permanentemente la tabla de Azure Storage.

Pasos siguientes

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>[Preguntas más frecuentes sobre desarrollo con Table API](https://docs.microsoft.com/azure/cosmos-db/faq)

* [Referencia de la API del SDK de Azure Cosmos DB para Python](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)
* [Centro para desarrolladores de Python](https://azure.microsoft.com/develop/python/)
* [Explorador de Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md): una aplicación gratuita y multiplataforma para trabajar visualmente con datos de Azure Storage en Windows, macOS y Linux.
* [Uso de Python en Visual Studio (Windows)](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)
* <bpt id="p1">[</bpt>Working with Python in Visual Studio (Windows)<ept id="p1">](https://docs.microsoft.com/visualstudio/python/overview-of-python-tools-for-visual-studio)</ept>


[py_commit_batch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_create_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_get_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_insert_or_replace_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_Entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?view=azure-python
[py_merge_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_update_entity]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_delete_table]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableService]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python
