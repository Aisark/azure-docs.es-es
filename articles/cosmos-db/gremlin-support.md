---
title: Compatibilidad de Azure Cosmos DB con Gremlin
description: Más información acerca del lenguaje de Gremlin de Apache TinkerPop. Más información acerca de las características y los pasos disponibles en Azure Cosmos DB
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: fd49cc6810f4a3a479748180ddb0c44aedf04e89
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275562"
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Compatibilidad de Azure Cosmos DB con grafos Gremlin
Azure Cosmos DB admite el lenguaje de recorrido de grafo de [Apache Tinkerpop](https://tinkerpop.apache.org) conocido como [Gremlin](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps). Puede usar el lenguaje Gremlin para crear entidades de grafo (vértices y aristas), modificar las propiedades de las entidades, realizar consultas y recorridos, y eliminar entidades. 

Azure Cosmos DB aporta características empresariales a las bases de datos de grafos, Estas características incluyen distribución global, escalado independiente del almacenamiento y el rendimiento, latencias predecibles inferiores a 10 milisegundos, indexación automática, SLA y disponibilidad de lectura para cuentas de base de datos que abarcan dos o más regiones de Azure. Dado que Azure Cosmos DB admite TinkerPop/Gremlin, puede migrar fácilmente aplicaciones escritas con otra base de datos de grafos compatible. Además, gracias a la compatibilidad con Gremlin, Azure Cosmos DB se integra perfectamente entornos de análisis habilitados para TinkerPop, tales como [Apache Spark GraphX](https://spark.apache.org/graphx/). 

En este artículo se proporciona un tutorial rápido de Gremlin y se enumeran las características de Gremlin que se admiten en Gremlin API.

## <a name="gremlin-by-example"></a>Gremlin con ejemplos
Vamos a usar un grafo de ejemplo para entender cómo se expresan las consultas en Gremlin. La siguiente ilustración muestra una aplicación empresarial que administra datos de usuarios, intereses y dispositivos en forma de grafo.  

![Base de datos de ejemplo que muestra personas, dispositivos e intereses](./media/gremlin-support/sample-graph.png) 

Este grafo tiene los siguientes tipos de vértices (llamados "etiquetas" en Gremlin):

- Personas: El grafo tiene tres personas, Robin, Thomas y Ben
- Intereses: Sus intereses; en este ejemplo, el fútbol
- Dispositivos: Los dispositivos que usan las personas
- Sistemas operativos: Los sistemas operativos que se ejecutan en los dispositivos

Las relaciones entre estas entidades se representan con las siguientes etiquetas o tipos de arista:

- Conoce a: Por ejemplo, "Thomas conoce a Robin"
- Tiene interés en: Permite representar los intereses de las personas de nuestro grafo; por ejemplo, "Ben tiene interés en Fútbol"
- EjecutaSO: Portátil ejecuta el sistema operativo Windows
- Usa: Permite representar el dispositivo que una persona usa. Por ejemplo, Robin usa un teléfono Motorola con número de serie 77.

Vamos a ejecutar algunas operaciones en este grafo mediante la [consola de Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console). También puede realizar estas operaciones mediante controladores de Gremlin en la plataforma que elija (Java, Node.js, Python o .NET).  Antes de adentrarnos en lo que se admite en Azure Cosmos DB, veamos algunos ejemplos para familiarizarnos con la sintaxis.

Primero, echemos un vistazo a CRUD. La siguiente instrucción de Gremlin inserta el vértice "Thomas" en el grafo:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

La siguiente instrucción de Gremlin inserta un arista "conoce a" entre Thomas y Robin.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

La consulta siguiente devuelve los vértices "persona" en orden descendente de sus nombres:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

En el lugar donde brillan los grafos es donde se necesita responder preguntas como "¿Qué sistemas operativos usan los amigos de Thomas?". Puede ejecutar este recorrido de Gremlin para obtener esa información del grafo:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Ahora, veamos qué proporciona Azure Cosmos DB a los desarrolladores de Gremlin.

## <a name="gremlin-features"></a>Características de Gremlin
TinkerPop es una norma que abarca una amplia gama de tecnologías de grafos. Por lo tanto, usa una terminología estándar para describir qué características proporciona un proveedor de grafos. Azure Cosmos DB proporciona una base de datos de grafos de escritura, persistente y de alta simultaneidad que se puede dividir entre varios servidores o clústeres. 

En la tabla siguiente se enumeran las características de TinkerPop implementadas por Azure Cosmos DB: 

| Categoría | Implementación de Azure Cosmos DB |  Notas | 
| --- | --- | --- |
| Características de grafos | Proporciona persistencia y acceso simultáneo. Diseñado para admitir transacciones | Los métodos de proceso se pueden implementar mediante el conector de Spark. |
| Características de variables | Admite valores Boolean, Integer, Byte, Double, Float, Integer, Long, String. | Admite tipos primitivos; es compatible con tipos complejos mediante el modelo de datos. |
| Características de vértices | Admite RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Permite crear, modificar y eliminar vértices. |
| Características de propiedades de vértices | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Permite crear, modificar y eliminar propiedades de vértices. |
| Características de aristas | AddEdges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Permite crear, modificar y eliminar aristas. |
| Características de propiedades de aristas | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Permite crear, modificar y eliminar propiedades de aristas. |

## <a name="gremlin-wire-format-graphson"></a>Formato de alambre de Gremlin: GraphSON

Azure Cosmos DB usa el [formato GraphSON](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) al devolver los resultados de las operaciones de Gremlin. GraphSON es el formato estándar de Gremlin para representar los vértices, las aristas y las propiedades (propiedades con uno o varios valores) mediante JSON. 

Por ejemplo, el fragmento de código siguiente muestra una representación de GraphSON de un vértice *devuelto al cliente* desde Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

A continuación, se describen las propiedades utilizadas por GraphSON para los vértices:

| Propiedad | DESCRIPCIÓN | 
| --- | --- | --- |
| `id` | Identificador del vértice. Debe ser único (en combinación con el valor de `_partition`, si corresponde). Si no se especifica ningún valor se suministrarán automáticamente con un GUID | 
| `label` | Etiqueta del vértice. Se utiliza para describir el tipo de entidad. |
| `type` | Se usa para distinguir los vértices de los documentos que no son grafos. |
| `properties` | Contenedor de propiedades definidas por el usuario asociadas con el vértice. Cada propiedad puede tener varios valores. |
| `_partition` | Clave de partición del vértice. Se una para la [creación de particiones de grafos](graph-partitioning.md). |
| `outE` | Esta propiedad contiene una lista de las aristas de un vértice. Almacenar la información de proximidad con los vértices permite ejecutar recorridos rápidamente. Las aristas se agrupan en función de sus etiquetas. |

La arista contiene la siguiente información para ayudar a la navegación a otras partes del grafo.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| `id` | Identificador de la arista. Debe ser único (en combinación con el valor de `_partition`, si corresponde). |
| `label` | Etiqueta de la arista. Esta propiedad es opcional y se usa para describir el tipo de relación. |
| `inV` | Esta propiedad contiene una lista de vértices para una línea. Almacenar la información de proximidad con la línea permite ejecutar recorridos rápidamente. Los vértices se agrupan en función de sus etiquetas. |
| `properties` | Contenedor de propiedades definidas por el usuario asociadas con la arista. Cada propiedad puede tener varios valores. |

Cada propiedad puede almacenar varios valores dentro de una matriz. 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| `value` | Valor de la propiedad.

## <a name="gremlin-steps"></a>Pasos de Gremlin
Ahora, echemos un vistazo a los pasos de Gremlin que Azure Cosmos DB admite. Para una referencia completa de Gremlin, consulte la [referencia de TinkerPop](https://tinkerpop.apache.org/docs/current/reference).

| paso | DESCRIPCIÓN | Documentación de TinkerPop 3.2. |
| --- | --- | --- |
| `addE` | Agrega una arista entre dos vértices. | [Paso addE](https://tinkerpop.apache.org/docs/current/reference/#addedge-step) |
| `addV` | Agrega un vértice al grafo. | [Paso addV](https://tinkerpop.apache.org/docs/current/reference/#addvertex-step) |
| `and` | Garantiza que todos los recorridos devuelven un valor. | [y un paso.](https://tinkerpop.apache.org/docs/current/reference/#and-step) |
| `as` | Modulador de pasos para asignar una variable a la salida de un paso. | [Paso as](https://tinkerpop.apache.org/docs/current/reference/#as-step) |
| `by` | Modulador de pasos que se usa con `group` y `order` | [con el paso by](https://tinkerpop.apache.org/docs/current/reference/#by-step) |
| `coalesce` | Devuelve el primer recorrido que devuelve un resultado. | [paso coalesce](https://tinkerpop.apache.org/docs/current/reference/#coalesce-step) |
| `constant` | Devuelve un valor constante. Se usa con `coalesce`| [paso constant](https://tinkerpop.apache.org/docs/current/reference/#constant-step) |
| `count` | Devuelve el número del recorrido. | [paso count](https://tinkerpop.apache.org/docs/current/reference/#count-step) |
| `dedup` | Devuelve los valores sin duplicados. | [paso dedup](https://tinkerpop.apache.org/docs/current/reference/#dedup-step) |
| `drop` | Quita los valores (vértice/arista). | [paso drop](https://tinkerpop.apache.org/docs/current/reference/#drop-step) |
| `executionProfile` | Crea una descripción de todas las operaciones generadas por el paso de Gremlin ejecutado | [paso executionProfile](graph-execution-profile.md) |
| `fold` | Actúa como una barrera que calcula el agregado de los resultados.| [paso fold](https://tinkerpop.apache.org/docs/current/reference/#fold-step) |
| `group` | Agrupa los valores en función de las etiquetas especificadas.| [paso group](https://tinkerpop.apache.org/docs/current/reference/#group-step) |
| `has` | Se utiliza para filtrar las propiedades, los vértices y las aristas. Admite las variantes `hasLabel`, `hasId`, `hasNot` y `has`. | [paso has](https://tinkerpop.apache.org/docs/current/reference/#has-step) |
| `inject` | Inserta valores en un flujo.| [paso inject](https://tinkerpop.apache.org/docs/current/reference/#inject-step) |
| `is` | Se usa para filtrar mediante una expresión booleana. | [paso is](https://tinkerpop.apache.org/docs/current/reference/#is-step) |
| `limit` | Se usa para limitar el número de elementos en el recorrido.| [paso limit](https://tinkerpop.apache.org/docs/current/reference/#limit-step) |
| `local` | Encapsula localmente una sección de un recorrido, de forma similar a una subconsulta. | [paso local](https://tinkerpop.apache.org/docs/current/reference/#local-step) |
| `not` | Se usa para generar la negación de un filtro. | [paso not](https://tinkerpop.apache.org/docs/current/reference/#not-step) |
| `optional` | Devuelve el resultado del recorrido especificado si produce un resultado; en caso contrario, devuelve el elemento que realiza la llamada. | [paso optional](https://tinkerpop.apache.org/docs/current/reference/#optional-step) |
| `or` | Garantiza que al menos uno de los recorridos devuelve un valor. | [paso or](https://tinkerpop.apache.org/docs/current/reference/#or-step) |
| `order` | Muestra los resultados con el criterio de ordenación especificado. | [paso order](https://tinkerpop.apache.org/docs/current/reference/#order-step) |
| `path` | Devuelve la ruta de acceso completa del recorrido. | [paso path](https://tinkerpop.apache.org/docs/current/reference/#path-step) |
| `project` | Proyecta las propiedades como un mapa. | [paso project](https://tinkerpop.apache.org/docs/current/reference/#project-step) |
| `properties` | Devuelve las propiedades de las etiquetas especificadas. | [paso properties](https://tinkerpop.apache.org/docs/current/reference/#properties-step) |
| `range` | Filtra el intervalo de valores especificado.| [paso range](https://tinkerpop.apache.org/docs/current/reference/#range-step) |
| `repeat` | Repite el paso el número de veces especificado. Se usa para crear bucles. | [paso repeat](https://tinkerpop.apache.org/docs/current/reference/#repeat-step) |
| `sample` | Se usa para tomar muestras de datos del recorrido. | [paso sample](https://tinkerpop.apache.org/docs/current/reference/#sample-step) |
| `select` | Se usa para proyectar los resultados del recorrido. |  [paso select](https://tinkerpop.apache.org/docs/current/reference/#select-step) |
| `store` | Se utiliza para realizar agregados sin bloqueo del recorrido. | [paso store](https://tinkerpop.apache.org/docs/current/reference/#store-step) |
| `tree` | Agrega las rutas de acceso desde un vértice en un árbol. | [paso tree](https://tinkerpop.apache.org/docs/current/reference/#tree-step) |
| `unfold` | Extrae un iterador como un paso.| [paso unfold](https://tinkerpop.apache.org/docs/current/reference/#unfold-step) |
| `union` | Combina los resultados de varios recorridos.| [paso union](https://tinkerpop.apache.org/docs/current/reference/#union-step) |
| `V` | Incluye los pasos necesarios para los recorridos entre los vértices y las aristas `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` y `otherV` | [pasos vertex](https://tinkerpop.apache.org/docs/current/reference/#vertex-steps) |
| `where` | Se usa para filtrar los resultados del recorrido. Admite los operadores `eq`, `neq`, `lt`, `lte`, `gt`, `gte` y `between`  | [paso where](https://tinkerpop.apache.org/docs/current/reference/#where-step) |

De manera predeterminada, el motor optimizado para escritura de Azure Cosmos DB admite la indexación automática de todas las propiedades dentro de los vértices y los bordes. Por lo tanto, las consultas con filtros, las consultas de intervalo, la ordenación o los agregados de cualquier propiedad se procesan a partir del índice y se sirven de forma eficiente. Para más información sobre cómo funciona la indexación en Azure Cosmos DB, consulte nuestro artículo sobre la [indexación independiente del esquema](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Pasos siguientes
* Empezar a compilar una aplicación de grafos [con nuestros SDK](create-graph-dotnet.md) 
* Más información acerca de la [compatibilidad con grafos](graph-introduction.md) en Azure Cosmos DB
