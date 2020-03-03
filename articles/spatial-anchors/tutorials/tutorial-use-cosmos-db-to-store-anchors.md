---
title: 'Tutorial: Uso compartido de delimitadores con Azure Cosmos DB'
description: En este tutorial, aprenderá a compartir identificadores de Azure Spatial Anchors entre los dispositivos Android o iOS de Unity con un servicio de back-end y Azure Cosmos DB.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 71b3027d86400d6921895f86e257ddff2961f91f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77615149"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>Tutorial: Uso compartido de Azure Spatial Anchors entre sesiones y dispositivos con un back-end de Azure Cosmos DB

Este tutorial es una continuación del tutorial para [compartir anclajes espaciales de Azure entre sesiones y dispositivos](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md). Le guiará en el proceso de agregar algunas funcionalidades más para que Azure Cosmos DB sirva como almacenamiento de back-end al mismo tiempo que comparten los delimitadores espaciales de Azure entre sesiones y dispositivos.

![GIF que ilustra la persistencia de objetos](./media/persistence.gif)

Es importante destacar que, aunque en este tutorial se van a usar Unity y Azure Cosmos DB, es solo para proporcionar un ejemplo de cómo compartir los identificadores de Spatial Anchors entre dispositivos. Se pueden usar otros lenguajes y tecnologías de back-end para lograr el mismo objetivo. Además, la aplicación web ASP.NET Core que se usa en este tutorial requiere el SDK de .NET Core 2.2. Funciona bien en Web Apps para Windows, pero actualmente no funciona en Web Apps para Linux.

## <a name="create-a-database-account"></a>Creación de una cuenta de base de datos

Agregue una base de datos de Azure Cosmos al grupo de recursos que creó anteriormente.

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copie `Connection String`, ya que lo va a necesitar.

## <a name="make-minor-changes-to-the-sharingservice-files"></a>Realización de cambios menores en los archivos SharingService

En el **Explorador de soluciones**, abra `SharingService\Startup.cs`.

Busque `#define INMEMORY_DEMO` al principio del archivo y marque la línea como comentario. Guarde el archivo.

En el **Explorador de soluciones**, abra `SharingService\appsettings.json`.

Busque la propiedad `StorageConnectionString` y establezca que el valor sea el mismo que el de `Connection String`, que copió en el [paso de creación de la cuenta de base de datos](#create-a-database-account). Guarde el archivo.

Puede publicar el servicio de uso compartido y ejecutar la aplicación de ejemplo.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="unity-20193"></a>Unity 2019.3

Debido a cambios importantes, Unity 2019.3 no se admite actualmente. Use Unity 2019.1 o 2019.2.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha usado Azure Cosmos DB para compartir identificadores de delimitador en todos los dispositivos. Para más información sobre cómo usar Azure Spatial Anchors en una nueva aplicación de Unity HoloLens, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Inicio de una nueva aplicación de Unity HoloLens](./tutorial-new-unity-hololens-app.md)
