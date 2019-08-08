---
title: Uso de la fuente de cambios de Azure Cosmos DB con Azure Functions
description: Utilice la fuente de cambios de Azure Cosmos DB con Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 299799566f0d54cc3fa017b032e15b387fe8cdd5
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467920"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Arquitecturas basadas en eventos sin servidor con Azure Cosmos DB y Azure Functions

Azure Functions proporciona la manera más sencilla de conectarse a la [fuente de cambios](change-feed.md). Puede crear pequeñas funciones reactivas de Azure Functions que se desencadenarán automáticamente en cada nuevo evento en la fuente de cambios de su contenedor de Azure Cosmos.

![Funciones basadas en eventos sin servidor que funcionan con el desencadenador de Azure Functions para Cosmos DB](./media/change-feed-functions/functions.png)

Con el [desencadenador de Azure Functions para Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), puede aprovechar el escalado del [procesador de fuente de cambios](./change-feed-processor.md) y la funcionalidad de detección de eventos confiable sin necesidad de mantener ninguna [infraestructura de trabajo](./change-feed-processor.md). Céntrese en la lógica de Azure Functions sin preocuparse por el resto de la canalización de origen de eventos. Incluso puede combinar el desencadenador con cualquier otro [enlace de Azure Functions](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Actualmente, el uso del desencadenador de Azure Functions para Cosmos DB se admite solo con Core (SQL) API.

## <a name="requirements"></a>Requisitos

Para implementar un flujo basado en eventos sin servidor, necesita:

* **El contenedor supervisado**: el contenedor supervisado es el contenedor de Azure Cosmos que se está supervisando y almacena los datos a partir de los cuales se genera la fuente de cambios. Todas las inserciones y cambios (por ejemplo, CRUD) realizados en el contenedor supervisado se reflejan en la fuente de cambios del contenedor.
* **El contenedor de concesión**: el contenedor de concesión mantiene el estado entre instancias de Azure Functions sin servidor múltiples y dinámicas, y permite un escalado dinámico. Este contenedor de concesión se puede crear manual o automáticamente mediante el desencadenador de Azure Functions para Cosmos DB. Para crear automáticamente el contenedor de concesión, establezca la marca *CreateLeaseCollectionIfNotExists* en la [configuración](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Los contenedores de concesión con particiones deben tener una definición de clave de partición `/id`.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Creación del desencadenador de Azure Functions para Cosmos DB

Ahora se admite la creación de una función de Azure con un desencadenador de Azure Functions para Cosmos DB en todos los IDE de Azure Functions e integraciones de la CLI:

* [Extensión de Visual Studio](../azure-functions/functions-develop-vs.md) para usuarios de Visual Studio.
* [Extensión de núcleo de Visual Studio](https://code.visualstudio.com/tutorials/functions-extension/create-function) para usuarios de Visual Studio Code.
* Y, finalmente, [herramientas de la CLI de Core](../azure-functions/functions-run-local.md#create-func) para una experiencia independiente del IDE multiplataforma.

## <a name="run-your-trigger-locally"></a>Ejecución local del desencadenador

Puede ejecutar la [función de Azure localmente](../azure-functions/functions-develop-local.md) con el [Emulador de Azure Cosmos DB](./local-emulator.md) para crear y desarrollar flujos basados en eventos sin servidor, sin tener una suscripción de Azure ni incurrir en gastos.

Si desea probar escenarios en vivo en la nube, puede [probar Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) sin necesidad de tener tarjetas de crédito ni suscripciones de Azure.

## <a name="next-steps"></a>Pasos siguientes

Ahora, puede seguir aprendiendo acerca de las fuentes de cambios en los siguientes artículos:

* [Introducción a la fuente de cambios](change-feed.md)
* [Maneras de leer la fuente de cambios](read-change-feed.md)
* [Uso de la biblioteca de procesadores de fuente de cambios](change-feed-processor.md)
* [Trabajo con la biblioteca de procesadores de fuente de cambios](change-feed-processor.md)
* [Informática de base de datos sin servidor con Azure Cosmos DB y Azure Functions](serverless-computing-database.md)
