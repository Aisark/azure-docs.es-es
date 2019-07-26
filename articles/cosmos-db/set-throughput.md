---
title: Aprovisionamiento del rendimiento en contenedores y bases de datos de Azure Cosmos
description: Aprenda cómo establecer el rendimiento aprovisionado para las bases de datos y los contenedores de Azure Cosmos.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: 2bcd428e2de90251d4d64111b1c3e6b6f812ac4c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467622"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Aprovisionar rendimiento en contenedores y bases de datos

Una base de datos de Azure Cosmos es una unidad de administración de un conjunto de contenedores. Una base de datos consta de un conjunto de contenedores independiente del esquema. Un contenedor de Azure Cosmos es la unidad de escalabilidad de rendimiento y almacenamiento. Un contenedor se divide horizontalmente en un conjunto de máquinas dentro de una región de Azure y se distribuye en todas las regiones de Azure asociadas a su cuenta de Azure Cosmos.

En Azure Cosmos DB, puede aprovisionar el rendimiento en dos granularidades:
 
- Contenedores de Azure Cosmos
- Bases de datos de Azure Cosmos

## <a name="set-throughput-on-a-container"></a>Establecimiento del rendimiento en un contenedor  

El rendimiento aprovisionado en un contenedor de Azure Cosmos se reserva exclusivamente para ese contenedor. El contenedor recibe el rendimiento aprovisionado todo el tiempo. El rendimiento aprovisionado en un contenedor está respaldado financieramente por los contratos de nivel de servicio. Para obtener información sobre cómo configurar el rendimiento en un contenedor, consulte [Aprovisionamiento del rendimiento en un contenedor de Azure Cosmos](how-to-provision-container-throughput.md).

La configuración del rendimiento aprovisionado en un contenedor es la opción más utilizada. Puede escalar elásticamente el rendimiento de un contenedor con el aprovisionamiento de cualquier cantidad de rendimiento mediante el uso de [Unidades de solicitud (RU)](request-units.md). 

El rendimiento aprovisionado en un contenedor de Azure Cosmos se distribuye uniformemente entre todas las particiones lógicas del contenedor. No puede especificar de forma selectiva el rendimiento de las particiones lógicas. Puesto que una o varias particiones lógicas de un contenedor se hospedan en una partición física, las particiones físicas pertenecen exclusivamente al contenedor y admiten el rendimiento aprovisionado en dicho contenedor. 

Si la carga de trabajo que se ejecuta en una partición lógica consume más que el rendimiento que se ha asignado a esa partición lógica, las operaciones tienen una velocidad limitada. Cuando se produce una limitación de velocidad, puede aumentar el rendimiento aprovisionado de todo el contenedor o volver a intentar las operaciones. Para más información sobre las particiones, consulte [Particiones lógicas](partition-data.md).

Se recomienda configurar el rendimiento en la granularidad del contenedor cuando se quiere un rendimiento garantizado para dicho contenedor.

En la imagen siguiente se muestra cómo una partición física hospeda una o varias particiones lógicas de un contenedor:

![Partición física](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Establecimiento del rendimiento en una base de datos

Al aprovisionar el rendimiento en una base de datos de Azure Cosmos, dicho rendimiento se comparte entre todos los contenedores de la base de datos. Una excepción es si ha especificado un rendimiento aprovisionado en determinados contenedores de la base de datos. Compartir el rendimiento aprovisionado de nivel de base de datos entre sus contenedores equivale a hospedar una base de datos en un clúster de máquinas. Dado que todos los contenedores de una base de datos comparten los recursos disponibles en una máquina, no obtendrá un rendimiento predecible en ningún contenedor específico. Para obtener información sobre cómo configurar el rendimiento aprovisionado en una base de datos, consulte [Aprovisionamiento del rendimiento en una base de datos de Azure Cosmos](how-to-provision-database-throughput.md).

La configuración del rendimiento en una base de datos de Azure Cosmos garantiza la recepción del rendimiento aprovisionado para esa base de datos todo el tiempo. Dado que todos los contenedores de la base de datos comparten el rendimiento aprovisionado, Azure Cosmos DB no proporciona ninguna garantía de rendimiento predecible para un contenedor determinado de dicha base de datos. La parte del rendimiento que puede recibir un contenedor específico depende de los siguientes factores:

* El número de contenedores.
* La elección de las claves de partición de varios contenedores.
* La distribución de la carga de trabajo en varias particiones lógicas de los contenedores. 

Se recomienda configurar el rendimiento en una base de datos si quiere compartir el rendimiento entre varios contenedores, pero no dedicarlo a ningún contenedor específico. 

En los siguientes ejemplos se muestra dónde es preferible aprovisionar el rendimiento en el nivel de base de datos:

* El uso compartido del rendimiento aprovisionado de una base de datos a través de un conjunto de contenedores es útil para una aplicación de varios inquilinos. Cada usuario puede representarse mediante un contenedor de Azure Cosmos diferente.

* El uso compartido del rendimiento aprovisionado de una base de datos a través de un conjunto de contenedores es útil al migrar una base de datos de NoSQL, como MongoDB o Cassandra, hospedada en un clúster de máquinas virtuales o desde servidores físicos locales a Azure Cosmos DB. Puede considerar el rendimiento aprovisionado configurado en la base de datos de Azure Cosmos como un equivalente lógico, pero más rentable y flexible, de la capacidad de proceso del clúster de MongoDB o Cassandra.  

Todos los contenedores creados en una base de datos con rendimiento aprovisionado deben crearse con una [clave de partición](partition-data.md). En cualquier momento determinado, el rendimiento asignado a un contenedor dentro de una base de datos se distribuye entre todas las particiones lógicas de dicho contenedor. Cuando hay contenedores que comparten el rendimiento aprovisionado configurado en una base de datos, el rendimiento no se puede aplicar de forma selectiva a un contenedor específico o a una partición lógica. 

Si la carga de trabajo en una partición lógica consume más que el rendimiento que se asignó a una partición lógica específica, las operaciones tendrán una velocidad limitada. Cuando se produce una limitación de velocidad, puede aumentar el rendimiento de toda la base de datos o volver a intentar las operaciones. Para más información sobre las particiones, consulte [Particiones lógicas](partition-data.md).

Varias particiones lógicas pertenecientes a distintos contenedores que comparten el rendimiento aprovisionado en una base de datos se pueden hospedar en una partición física única. Mientras que una única partición lógica de un contenedor siempre se encuentra dentro del ámbito de una partición física, las particiones lógicas de *"L"* en los contenedores de *"C"* que comparten el rendimiento aprovisionado de una base de datos pueden asignarse a particiones físicas de *"R"* y hospedarse en ellas. 

En la siguiente imagen se muestra cómo una partición física puede hospedar una o varias particiones lógicas que pertenecen a distintos contenedores dentro de una base de datos:

![Partición física](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Establecimiento del rendimiento en un contenedor y una base de datos

Puede combinar los dos modelos. Se permite el aprovisionamiento del rendimiento tanto en la base de datos como en el contenedor. En el ejemplo siguiente se muestra cómo aprovisionar el rendimiento en un contenedor y una base de datos de Azure Cosmos:

* Puede crear una base de datos de Azure Cosmos llamada *Z* con rendimiento aprovisionado de RU de *"K"* . 
* Después, cree cinco contenedores llamados *A*, *B*, *C*, *D* y *E* en la base de datos. Al crear el contenedor B, asegúrese de habilitar la opción **Provision dedicated throughput for this container** (Aprovisionar el rendimiento dedicado para este contenedor) y configurar de forma explícita las Unidades de solicitud *"P"* del rendimiento aprovisionado en este contenedor. Tenga en cuenta que únicamente puede configurar el rendimiento compartido y dedicado al crear la base de datos y el contenedor. 

   ![Configuración del rendimiento en el nivel de contenedor](./media/set-throughput/coll-level-throughput.png)

* El rendimiento de las Unidades de solicitud *"K"* se comparte entre los cuatro contenedores *A*, *C*, *D* y *E*. La cantidad exacta de rendimiento disponible en *A*, *C*, *D* o *E* varía. No hay ningún Acuerdo de Nivel de Servicio para el rendimiento de cada contenedor individual.
* Se garantiza que el contenedor llamado *B* obtendrá el rendimiento de las Unidades de solicitud de *"P"* todo el tiempo. Estará respaldado por los Acuerdos de Nivel de Servicio.

## <a name="update-throughput-on-a-database-or-a-container"></a>Actualización del rendimiento en un contenedor o una base de datos

Después de crear un contenedor o una base de datos de Cosmos Azure, se puede actualizar el rendimiento aprovisionado. No existen límites para el rendimiento aprovisionado máximo que se puede configurar en la base de datos o el contenedor. El rendimiento aprovisionado mínimo depende de los factores siguientes: 

* Tamaño máximo de los datos que se han almacenado en algún momento en el contenedor.
* Rendimiento máximo que se ha aprovisionado en algún momento en el contenedor.
* El número máximo de contenedores de Azure Cosmos que se han creado alguna vez en una base de datos con rendimiento compartido. 

Puede recuperar el rendimiento mínimo de un contenedor o una base de datos mediante programación con los SDK o ver el valor en Azure Portal. Al usar el SDK de .NET, el método [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) permite escalar el valor de rendimiento aprovisionado. Al usar el SDK de Java, el método [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) permite escalar el valor de rendimiento aprovisionado. 

Al usar el SDK de .NET, el método [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) permite recuperar el rendimiento mínimo de un contenedor o una base de datos. 

Puede escalar el rendimiento aprovisionado de un contenedor o una base de datos en cualquier momento. Cuando se realiza una operación de escala para aumentar el rendimiento, puede tardar más tiempo debido a las tareas del sistema destinadas a aprovisionar los recursos necesarios. Puede comprobar el estado de la operación de escalado en Azure Portal o mediante programación con el SDK. Al usar el SDK de .NET, puede obtener el estado de la operación de escalado mediante el método `DocumentClient.ReadOfferAsync`.

## <a name="comparison-of-models"></a>Comparación de modelos

|**Parámetro**  |**Rendimiento aprovisionado en una base de datos**  |**Rendimiento aprovisionado en un contenedor**|
|---------|---------|---------|
|Número mínimo de RU |400 (después de los cuatro primeros contenedores, cada contenedor adicional requiere un mínimo de 100 RU por segundo). |400|
|Número mínimo de RU por contenedor|100|400|
|Número máximo de RU|Ilimitado, en la base de datos.|Ilimitado, en el contenedor.|
|RU asignadas o disponibles para un contenedor específico|Sin garantías. Las RU asignadas a un contenedor determinado dependen de las propiedades. Las propiedades pueden ser la elección de las claves de partición de contenedores que comparten el rendimiento, la distribución de la carga de trabajo y el número de contenedores. |Todas las RU configuradas en el contenedor se reservan exclusivamente para el contenedor.|
|Almacenamiento máximo de un contenedor|Sin límite.|Sin límite.|
|Rendimiento máximo por partición lógica de un contenedor|10 000 RU|10 000 RU|
|Almacenamiento máximo (datos + índice) por partición lógica de un contenedor|10 GB|10 GB|

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre las [particiones lógicas](partition-data.md).
* Obtenga más información sobre el [aprovisionamiento del rendimiento de un contenedor de Azure Cosmos](how-to-provision-container-throughput.md).
* Obtenga más información sobre el [aprovisionamiento del rendimiento de una base de datos de Azure Cosmos](how-to-provision-database-throughput.md).

