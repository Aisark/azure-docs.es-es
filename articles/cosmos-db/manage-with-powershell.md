---
title: Creación y administración de Azure Cosmos DB mediante PowerShell
description: Use Azure Powershell para administrar las cuentas de Azure Cosmos DB, bases de datos, contenedores y rendimiento.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/05/2019
ms.author: mjbrown
ms.custom: seodec18
ms.openlocfilehash: e8f943ebaa5dfc06e0bfb04dc1097d6794ec6d05
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616826"
---
# <a name="manage-azure-cosmos-db-sql-api-resources-using-powershell"></a>Administración de recursos de SQL API de Azure Cosmos DB mediante PowerShell

En la guía siguiente se describe cómo usar PowerShell para crear scripts y automatizar la administración de los recursos de Azure Cosmos DB, incluida la cuenta, la base de datos, el contenedor y el rendimiento. La administración de Azure Cosmos DB se realiza mediante el cmdlet AzResource directamente en el proveedor de recursos de Azure Cosmos DB. Para ver todas las propiedades que se pueden administrar con PowerShell para el proveedor de recursos de Azure Cosmos DB, consulte [Esquema del proveedor de recursos de Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions).

Para la administración entre plataformas de Azure Cosmos DB, puede usar la [CLI de Azure](manage-with-cli.md), la [API REST][rp-rest-api] o [Azure Portal](create-sql-api-dotnet.md#create-account).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="getting-started"></a>Introducción

Siga las instrucciones de [Instalación y configuración de Azure PowerShell][powershell-install-configure] para instalar e iniciar sesión en su cuenta de Azure en Powershell.

* Si desea ejecutar los siguientes comandos sin necesidad de confirmación del usuario, anexe la marca `-Force` al comando.
* Todos los siguientes comandos son sincrónicos.

## <a name="azure-cosmos-accounts"></a>Cuentas de Azure Cosmos

En las secciones siguientes se muestra cómo administrar la cuenta de Azure Cosmos, entre ellas:

* [Creación de una cuenta de Azure Cosmos](#create-account)
* [Actualización de una cuenta de Azure Cosmos](#update-account)
* [Enumeración de todas las cuentas de Azure Cosmos DB de una suscripción](#list-accounts)
* [Obtención de una cuenta de Azure Cosmos](#get-account)
* [Eliminación de una cuenta de Azure Cosmos](#delete-account)
* [Actualización de las etiquetas de una cuenta de Azure Cosmos](#update-tags)
* [Enumeración de las claves para una cuenta de Azure Cosmos](#list-keys)
* [Regeneración de las claves para una cuenta de Azure Cosmos](#regenerate-keys)
* [Enumeración de las cadenas de conexión para una cuenta de Azure Cosmos](#list-connection-strings)
* [Modificación de la prioridad de conmutación por error de una cuenta de Azure Cosmos](#modify-failover-priority)

### <a id="create-account"></a> Creación de una cuenta de Azure Cosmos

Este comando permite crear una cuenta de base de datos de Azure Cosmos con una [directiva de coherencia](consistency-levels.md) con obsolescencia limitada en [varias regiones][distribute-data-globally].

```azurepowershell-interactive
# Create an Azure Cosmos Account for Core (SQL) API
$resourceGroupName = "myResourceGroup"
$location = "West US 2"
$accountName = "mycosmosaccount" # must be lowercase and < 31 characters .

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 }
)

$consistencyPolicy = @{
    "defaultConsistencyLevel"="BoundedStaleness";
    "maxIntervalInSeconds"=300;
    "maxStalenessPrefix"=100000
}

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$locations;
    "consistencyPolicy"=$consistencyPolicy;
    "enableMultipleWriteLocations"="false"
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Location $location `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

* `$accountName` El nombre de la cuenta de Azure Cosmos. Debe estar en minúsculas, acepta caracteres alfanuméricos y el carácter"-" y debe tener entre 3 y 31 caracteres.
* `$location` La ubicación del recurso de la cuenta de Azure Cosmos.
* `$locations` Las regiones de réplica para la cuenta de base de datos. Debe haber una región de escritura por cuenta de base de datos con un valor de prioridad de conmutación por error de 0.
* `$consistencyPolicy` El nivel de coherencia predeterminado de la cuenta de Azure Cosmos. Para más información, consulte [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md).
* `$CosmosDBProperties` Los valores de propiedad pasados al proveedor de Azure Resource Manager de Cosmos DB para aprovisionar la cuenta.

Las cuentas de Azure Cosmos se pueden configurar con firewall de IP, así como con puntos de conexión de servicio de red virtual. Para más información sobre cómo configurar el firewall de IP para Azure Cosmos DB, consulte [Configuración del firewall de IP](how-to-configure-firewall.md).  Para más información sobre cómo habilitar los puntos de conexión de servicio para Azure Cosmos DB, consulte [Configuración del acceso desde redes virtuales (VNET)](how-to-configure-vnet-service-endpoint.md).

### <a id="list-accounts"></a> Enumeración de todas las cuentas de Azure Cosmos DB de una suscripción

Este comando permite mostrar todas las cuentas de Azure Cosmos DB de una suscripción.

```azurepowershell-interactive
# List Azure Cosmos Accounts

Get-AzResource -ResourceType Microsoft.DocumentDb/databaseAccounts | ft
```

### <a id="get-account"></a> Obtención de las propiedades de una cuenta de Azure Cosmos

Este comando le permite obtener las propiedades de una cuenta de base de datos de Azure Cosmos.

```azurepowershell-interactive
# Get the properties of an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName | Select-Object Properties
```

### <a id="update-account"></a> Actualización de una cuenta de Azure Cosmos

Este comando permite actualizar las propiedades de la cuenta de base de datos de Azure Cosmos. Las propiedades que se pueden actualizar incluyen las siguientes:

* Adición o eliminación de registros
* Cambio de la directiva de coherencia predeterminada
* Cambio del filtro del intervalo de direcciones IP
* Cambio de las configuraciones de la red virtual
* Habilitación de la arquitectura multimaestro

> [!NOTE]
> Este comando le permite agregar y quitar regiones, pero no le permite modificar las prioridades de conmutación por error ni cambiar la región con `failoverPriority=0`. Para modificar la prioridad de la conmutación por error, consulte [Modificación de la prioridad de conmutación por error de una cuenta de Azure Cosmos](#modify-failover-priority).

```azurepowershell-interactive
# Get an Azure Cosmos Account (assume it has two regions currently West US 2 and East US 2) and add a third region

$resourceGroupName = "myResourceGroup"
$accountName = "myaccountname"

$account = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $accountName

$locations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 },
    @{ "locationName"="East US 2"; "failoverPriority"=1 },
    @{ "locationName"="South Central US"; "failoverPriority"=2 }
)

$account.Properties.locations = $locations
$CosmosDBProperties = $account.Properties

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -PropertyObject $CosmosDBProperties
```

### <a id="delete-account"></a> Eliminación de una cuenta de Azure Cosmos

Este comando le permite eliminar una cuenta de Azure Cosmos existente.

```azurepowershell-interactive
# Delete an Azure Cosmos Account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName
```

### <a id="update-tags"></a> Actualización de las etiquetas de una cuenta de Azure Cosmos

En el ejemplo siguiente, se describe cómo establecer [etiquetas de recursos de Azure][azure-resource-tags] para una cuenta de Azure Cosmos.

> [!NOTE]
> Este comando se puede combinar con los comandos de creación o actualización anexando la marca `-Tags` con el parámetro correspondiente.

```azurepowershell-interactive
# Update tags for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$tags = @{
    "dept" = "Finance";
    "environment" = "Production"
}

Set-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $accountName -Tags $tags
```

### <a id="list-keys"></a> Enumerar claves de cuenta

Cuando se crea una cuenta de Azure Cosmos DB, el servicio genera dos claves de acceso maestras que se pueden usar para la autenticación cuando se tiene acceso a la cuenta de Azure Cosmos DB. Al proporcionar dos claves de acceso, Azure Cosmos DB permite regenerar las claves sin interrupción en la cuenta de Azure Cosmos DB. También están disponibles las claves de solo lectura para autenticar las operaciones de solo lectura. Hay dos claves de lectura y escritura (principal y secundaria) y dos claves de solo lectura (principal y secundaria).

```azurepowershell-interactive
# List keys for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listKeys `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="list-connection-strings"></a> Enumeración de cadenas de conexión

En el caso de las cuentas de MongoDB, se puede recuperar la cadena de conexión para conectar la aplicación de MongoDB a la cuenta de la base de datos con el comando siguiente.

```azurepowershell-interactive
# List connection strings for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keys = Invoke-AzResourceAction -Action listConnectionStrings `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName

Select-Object $keys
```

### <a id="regenerate-keys"></a> Regeneración de claves de cuenta

Se deben regenerar periódicamente las claves de acceso a una cuenta de Azure Cosmos para ayudar a mantener las conexiones más seguras. Una clave de acceso principal y otra secundaria se asignan a la cuenta. Esto permite a los clientes mantener el acceso mientras se regenera la otra. Hay cuatro tipos de claves para una cuenta de Azure Cosmos (primaria, secundaria, primera de solo lectura y secundaria de solo lectura)

```azurepowershell-interactive
# Regenerate the primary key for an Azure Cosmos Account

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$keyKind = @{ "keyKind"="Primary" }

$keys = Invoke-AzResourceAction -Action regenerateKey `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $keyKind

Select-Object $keys
```

### <a id="modify-failover-priority"></a> Modificación de la prioridad de conmutación por error

En el caso de cuentas de bases de datos de varias regiones, puede cambiar el orden en que una cuenta de Cosmos promoverá las réplicas de lectura secundarias en caso de que se produzca una conmutación por error regional en la réplica de escritura principal. La modificación de `failoverPriority=0` también puede servir para iniciar una exploración de recuperación ante desastres para probar la planeación de esta última.

En el ejemplo siguiente, suponga que la cuenta tiene una prioridad de conmutación por error actual de `West US 2 = 0` y `East US 2 = 1`, y que se invierten las regiones.

> [!CAUTION]
> Cambiar `locationName` por `failoverPriority=0` desencadenará una conmutación por error manual de una cuenta de Azure cosmos. Cualquier otro cambio de prioridad no desencadenará ninguna conmutación por error.

```azurepowershell-interactive
# Change the failover priority for an Azure Cosmos Account
# Assume existing priority is "West US 2" = 0 and "East US 2" = 1

$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"

$failoverRegions = @(
    @{ "locationName"="East US 2"; "failoverPriority"=0 },
    @{ "locationName"="West US 2"; "failoverPriority"=1 }
)

$failoverPolicies = @{
    "failoverPolicies"= $failoverRegions
}

Invoke-AzResourceAction -Action failoverPriorityChange `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" `
    -ResourceGroupName $resourceGroupName -Name $accountName -Parameters $failoverPolicies
```

## <a name="azure-cosmos-database"></a>Base de datos de Azure Cosmos

Las siguientes secciones muestran cómo administrar la base de datos de Azure Cosmos, entre ellas:

* [Creación de una base de datos de Azure Cosmos](#create-db)
* [Creación de una base de datos de Azure Cosmos con rendimiento compartido](#create-db-ru)
* [Consiga el rendimiento de una base de datos de Azure Cosmos](#get-db-ru)
* [Enumeración de todas las bases de datos de Cosmos en una cuenta](#list-db)
* [Obtención de una sola base de datos de Azure Cosmos](#get-db)
* [Eliminación de una cuenta de Azure Cosmos](#delete-db)

### <a id="create-db"></a>Creación de una base de datos de Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{"id"=$databaseName}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="create-db-ru"></a>Creación de una base de datos de Azure Cosmos con rendimiento compartido

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database2"
$resourceName = $accountName + "/sql/" + $databaseName

$DataBaseProperties = @{
    "resource"=@{ "id"=$databaseName };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $DataBaseProperties
```

### <a id="get-db-ru"></a>Consiga el rendimiento de una base de datos de Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$databaseThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/settings"
$databaseThroughputResourceName = $accountName + "/sql/" + $databaseName + "/throughput"

Get-AzResource -ResourceType $databaseThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $databaseThroughputResourceName  | Select-Object Properties
```

### <a id="list-db"></a>Obtención de todas las bases de datos de Azure Cosmos en una cuenta

```azurepowershell-interactive
# Get all databases in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$resourceName = $accountName + "/sql/"

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName  | Select-Object Properties
```

### <a id="get-db"></a>Obtención de una sola base de datos de Azure Cosmos

```azurepowershell-interactive
# Get a single database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-db"></a>Eliminación de una base de datos de Azure Cosmos

```azurepowershell-interactive
# Delete a database in an Azure Cosmos account
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName
Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="azure-cosmos-container"></a>Contenedor de Azure Cosmos

Las siguientes secciones muestran cómo administrar el contenedor de Azure Cosmos, entre ellas:

* [Creación de un contenedor de Azure Cosmos](#create-container)
* [Creación de un contenedor de Azure Cosmos con una clave de partición de gran tamaño](#create-container-big-pk)
* [Consiga el rendimiento de un contenedor de Azure Cosmos](#get-container-ru)
* [Creación de un contenedor de Azure Cosmos con rendimiento compartido](#create-container-ru)
* [Creación de un contenedor de Azure Cosmos con indexación personalizada](#create-container-custom-index)
* [Creación de un contenedor de Azure Cosmos con la indexación desactivada](#create-container-no-index)
* [Creación de un contenedor de Azure Cosmos con una clave única y TTL](#create-container-unique-key-ttl)
* [Creación de un contenedor de Azure Cosmos con resolución de conflictos](#create-container-lww)
* [Enumeración de todos los contenedores de Azure Cosmos en una base de datos](#list-containers)
* [Obtención de un único contenedor de Azure Cosmos en una base de datos](#get-container)
* [Eliminación de un contenedor de Azure Cosmos](#delete-container)

### <a id="create-container"></a>Creación de un contenedor de Azure Cosmos

```azurepowershell-interactive
# Create an Azure Cosmos container with default indexes and throughput at 400 RU
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-big-pk"></a>Creación de un contenedor de Azure Cosmos con una clave de partición de gran tamaño

```azurepowershell-interactive
# Create an Azure Cosmos container with a large partition key value (version = 2)
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash";
            "version" = 2
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="get-container-ru"></a>Consiga el rendimiento de un contenedor de Azure Cosmos

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$containerThroughputResourceType = "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers/settings"
$containerThroughputResourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName + "/throughput"

Get-AzResource -ResourceType $containerThroughputResourceType `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $containerThroughputResourceName  | Select-Object Properties
```

### <a id="create-container-ru"></a>Creación de un contenedor de Azure Cosmos con rendimiento compartido

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container2"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }
    };
    "options"=@{}
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties 
```

### <a id="create-container-custom-index"></a>Creación de un contenedor de Azure Cosmos con una directiva de indexación personalizada

```azurepowershell-interactive
# Create a container with a custom indexing policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container3"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey"); 
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @(@{
                "path"="/myPathToNotIndex/*"
            })
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-no-index"></a>Creación de un contenedor de Azure Cosmos con la indexación desactivada

```azurepowershell-interactive
# Create an Azure Cosmos container with no indexing
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container4"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName; 
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "indexingPolicy"=@{
            "indexingMode"="none"
        }
    };
    "options"=@{ "Throughput"="400" }
} 

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-unique-key-ttl"></a>Creación de un contenedor de Azure Cosmos con una directiva de clave única y TTL

```azurepowershell-interactive
# Create a container with a unique key policy and TTL
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        }; 
        "indexingPolicy"=@{
            "indexingMode"="Consistent";
            "includedPaths"= @(@{
                "path"="/*";
                "indexes"= @()
            });
            "excludedPaths"= @()
        };
        "uniqueKeyPolicy"= @{
            "uniqueKeys"= @(@{
                "paths"= @(
                    "/myUniqueKey1";
                    "/myUniqueKey2"
                )
            })
        };
        "defaultTtl"= 100;
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="create-container-lww"></a>Creación de un contenedor de Azure Cosmos con resolución de conflictos

Para crear una directiva de resolución de conflictos para utilizar un procedimiento almacenado, establezca `"mode"="custom"` y además la ruta de acceso de resolución como el nombre del procedimiento almacenado, `"conflictResolutionPath"="myResolverStoredProcedure"`. Para escribir todos los conflictos en ConflictsFeed y controlarlos de manera independiente, establezca `"mode"="custom"` y `"conflictResolutionPath"=""`.

```azurepowershell-interactive
# Create container with last-writer-wins conflict resolution policy
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container6"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

$ContainerProperties = @{
    "resource"=@{
        "id"=$containerName;
        "partitionKey"=@{
            "paths"=@("/myPartitionKey");
            "kind"="Hash"
        };
        "conflictResolutionPolicy"=@{
            "mode"="lastWriterWins";
            "conflictResolutionPath"="/myResolutionPath"
        }
    };
    "options"=@{ "Throughput"="400" }
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName -PropertyObject $ContainerProperties
```

### <a id="list-containers"></a>Enumeración de todos los contenedores de Azure Cosmos en una base de datos

```azurepowershell-interactive
# List all Azure Cosmos containers in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$resourceName = $accountName + "/sql/" + $databaseName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="get-container"></a>Obtención de un único contenedor de Azure Cosmos en una base de datos

```azurepowershell-interactive
# Get a single Azure Cosmos container in a database
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container5"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName `
    -Name $resourceName | Select-Object Properties
```

### <a id="delete-container"></a>Eliminación de un contenedor de Azure Cosmos

```azurepowershell-interactive
# Delete an Azure Cosmos container
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$databaseName = "database1"
$containerName = "container1"
$resourceName = $accountName + "/sql/" + $databaseName + "/" + $containerName

Remove-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts/apis/databases/containers" `
    -ApiVersion "2015-04-08" -ResourceGroupName $resourceGroupName -Name $resourceName
```

## <a name="next-steps"></a>Pasos siguientes

* [Todos los ejemplos de PowerShell](powershell-samples.md)
* [Administración de la cuenta de Azure Cosmos](how-to-manage-database-account.md)
* [Creación de un contenedor de Azure Cosmos](how-to-create-container.md)
* [Configuración del período de vida en Azure Cosmos DB](how-to-time-to-live.md)

<!--Reference style links - using these makes the source content way more readable than using inline links-->

[powershell-install-configure]: https://docs.microsoft.com/azure/powershell-install-configure
[scaling-globally]: distribute-data-globally.md#EnableGlobalDistribution
[distribute-data-globally]: distribute-data-globally.md
[azure-resource-groups]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview#resource-groups
[azure-resource-tags]: https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags
[rp-rest-api]: https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/
