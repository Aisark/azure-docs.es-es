---
title: Creación de un clúster y una base de datos de Azure Data Explorer mediante C#
description: Aprenda a crear un clúster y una base de datos de Azure Data Explorer mediante C#
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 2d800dc401b0d85b26a71817a1a70d66539203ae
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902129"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-c"></a>Creación de un clúster y una base de datos de Azure Data Explorer mediante C#

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
> * [Plantilla de Azure Resource Manager](create-cluster-database-resource-manager.md)

Azure Data Explorer es un servicio de análisis de datos rápido y totalmente administrado para analizar en tiempo real grandes volúmenes de datos de que se transmiten desde aplicaciones, sitios web, dispositivos IoT, etc. Para usar Azure Data Explorer, cree primero un clúster y una o varias bases de datos en ese clúster. A continuación, ingerirá (cargará) los datos en una base de datos para que pueda ejecutar consultas en ella. En este artículo, se crean un clúster y una base de datos con C#.

## <a name="prerequisites"></a>Prerequisites

* Si no tiene Visual Studio 2019 instalado, puede descargar y usar la versión **gratis** de [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Asegúrese de que habilita **Desarrollo de Azure** durante la instalación de Visual Studio.
* Si no tiene una suscripción a Azure, cree una [cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de empezar.

## <a name="install-c-nuget"></a>Instalación de NuGet C#

* Instale el [paquete NuGet de Azure Data Explorer (Kusto)](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Instale el [paquete NuGet Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) para la autenticación.

## <a name="authentication"></a>Authentication
Para ejecutar los ejemplos de este artículo, se necesita una aplicación de Azure AD y una entidad de servicio con acceso a los recursos. Consulte [Creación de una aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) para crear una aplicación de Azure AD gratuita e incorporar una asignación de roles en el ámbito de la suscripción. También se explica cómo obtener `Directory (tenant) ID`, `Application ID` y `Client Secret`.

## <a name="create-the-azure-data-explorer-cluster"></a>Creación del clúster de Azure Data Explorer

1. Cree el clúster mediante el siguiente código:

    ```csharp
    var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
    var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
    var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
    var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
    var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
    var credential = new ClientCredential(clientId, clientSecret);
    var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

    var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

    var kustoManagementClient = new KustoManagementClient(credentials)
    {
        SubscriptionId = subscriptionId
    };

    var resourceGroupName = "testrg";
    var clusterName = "mykustocluster";
    var location = "Central US";
    var skuName = "Standard_D13_v2";
    var tier = "Standard";
    var capacity = 5;
    var sku = new AzureSku(skuName, tier, capacity);
    var cluster = new Cluster(location, sku);
    await kustoManagementClient.Clusters.CreateOrUpdateAsync(resourceGroupName, clusterName, cluster);
    ```

   |**Configuración** | **Valor sugerido** | **Descripción del campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nombre que quiere para el clúster.|
   | skuName | *Standard_D13_v2* | La SKU que se usará para el clúster. |
   | Nivel: | *Estándar* | Nivel de SKU. |
   | capacity | *número* | Número de instancias del clúster. |
   | resourceGroupName | *testrg* | Nombre del grupo de recursos en el que se creará el clúster. |

    > [!NOTE]
    > La **creación de un clúster** es una operación de larga duración, por lo que se recomienda encarecidamente utilizar CreateOrUpdateAsync en lugar de CreateOrUpdate. 

1. Ejecute el siguiente comando para comprobar si el clúster se creó correctamente:

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

Si el resultado contiene `ProvisioningState` con el valor `Succeeded`, significa que el clúster se ha creado correctamente.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Creación de la base de datos en el clúster de Azure Data Explorer

1. Cree la base de datos mediante el siguiente código:

    ```csharp
    var hotCachePeriod = new TimeSpan(3650, 0, 0, 0);
    var softDeletePeriod = new TimeSpan(3650, 0, 0, 0);
    var databaseName = "mykustodatabase";
    var database = new ReadWriteDatabase(location: location, softDeletePeriod: softDeletePeriod, hotCachePeriod: hotCachePeriod);

    await kustoManagementClient.Databases.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, database);
    ```

        [!NOTE]
        If you are using C# version 2.0.0 or below, use Database instead of ReadWriteDatabase.

   |**Configuración** | **Valor sugerido** | **Descripción del campo**|
   |---|---|---|
   | clusterName | *mykustocluster* | Nombre del clúster donde se creará la base de datos.|
   | databaseName | *mykustodatabase* | Nombre de la base de datos.|
   | resourceGroupName | *testrg* | Nombre del grupo de recursos en el que se creará el clúster. |
   | softDeletePeriod | *3650:00:00:00* | Cantidad de tiempo que los datos estarán disponibles para consulta. |
   | hotCachePeriod | *3650:00:00:00* | Cantidad de tiempo que los datos se conservarán en la caché. |

2. Ejecute el siguiente comando para ver la base de datos que ha creado:

    ```csharp
    kustoManagementClient.Databases.Get(resourceGroupName, clusterName, databaseName) as ReadWriteDatabase;
    ```

Ahora cuenta con un clúster y una base de datos.

## <a name="clean-up-resources"></a>Limpieza de recursos

* Si tiene previsto seguir nuestros otros artículos, conserve los recursos que creó.
* Para limpiar los recursos, elimine el clúster. Cuando se elimina un clúster, también se eliminan todas las bases de datos en él. Use el siguiente comando para eliminar el clúster:

    ```csharp
    kustoManagementClient.Clusters.Delete(resourceGroupName, clusterName);
    ```

## <a name="next-steps"></a>Pasos siguientes

* [Ingesta de datos mediante el SDK de .NET Standard de Azure Data Explorer (versión preliminar)](net-standard-ingest-data.md)
