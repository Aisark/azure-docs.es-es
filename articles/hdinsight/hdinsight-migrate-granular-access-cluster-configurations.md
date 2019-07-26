---
title: 'Migración al acceso basado en rol detallado para configuraciones de clúster: Azure HDInsight'
description: Obtenga más información sobre los cambios necesarios como parte de la migración a un acceso granular basado en rol para las configuraciones de clústeres de HDInsight.
author: tylerfox
ms.author: tyfox
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ebb1723a9a2b2d069a1766d4f78151f2b684c5b9
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464677"
---
# <a name="migrate-to-granular-role-based-access-for-cluster-configurations"></a>Migración a acceso basado en rol detallado para configuraciones de clúster

Se van a realizar algunos cambios importantes para permitir un acceso más pormenorizado basado en rol para obtener información confidencial. Como parte de estos cambios, puede ser **necesario que tome alguna medida** si utiliza alguna de las [entidades o de los escenarios afectados](#am-i-affected-by-these-changes).

## <a name="what-is-changing"></a>¿Qué está cambiando?

Anteriormente, los usuarios de clústeres que poseían los roles de [RBAC](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) de propietario, colaborador o lector podían obtener secretos a través de la API de HDInsight, ya que estaban disponibles para cualquier persona con el permiso `*/read`.
En el futuro, el acceso a estos secretos requerirá el permiso `Microsoft.HDInsight/clusters/configurations/*`, lo que significa que los usuarios con el rol de lector ya no podrán acceder a ellos. Los secretos se definen como valores que podrían utilizarse para obtener un acceso más elevado que el que debería permitir el rol de un usuario. Estos incluyen valores como las credenciales HTTP de la puerta de enlace del clúster, las claves de la cuenta de almacenamiento y las credenciales de la base de datos.

También estamos introduciendo el nuevo rol de [operador de clústeres de HDInisght](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator), que será capaz de recuperar secretos sin que se le concedan los permisos administrativos de colaborador o propietario. Resumiendo:

| Role                                  | Anteriormente                                                                                       | A partir de ahora       |
|---------------------------------------|--------------------------------------------------------------------------------------------------|-----------|
| Lector                                | - Acceso de lectura, incluidos secretos                                                                   | - Acceso de lectura, **excluidos** secretos |           |   |   |
| Operador de clústeres de HDInsight<br>(Nuevo rol) | N/D                                                                                              | - Acceso de lectura/escritura, incluidos secretos         |   |   |
| Colaborador                           | - Acceso de lectura/escritura, incluidos secretos<br>- Creación y administración de todos los tipos de recursos de Azure.     | Sin cambios |
| Propietario                                 | - Acceso de lectura/escritura, incluidos secretos<br>- Acceso total a todos los recursos<br>- Delegación del acceso a otros usuarios | Sin cambios |

Para obtener información sobre cómo agregar la asignación del rol de operador de clústeres de HDInsight a un usuario para otorgarle acceso de lectura o escritura a los secretos del clúster, consulte la sección siguiente [Adición de la asignación del rol de operador de clústeres de HDInsight a un usuario](#add-the-hdinsight-cluster-operator-role-assignment-to-a-user).

## <a name="am-i-affected-by-these-changes"></a>¿Estos cambios me afectan?

Afecta a las entidades y los escenarios siguientes:

- [API](#api): Usuarios que utilizan los puntos de conexión `/configurations` o `/configurations/{configurationName}`.
- [Azure HDInsight Tools for Visual Studio Code](#azure-hdinsight-tools-for-visual-studio-code) 1.1.1 o anterior.
- [Azure Toolkit for IntelliJ](#azure-toolkit-for-intellij) 3.20.0 o anterior.
- [Herramientas de Azure Data Lake y de Stream Analytics para Visual Studio](#azure-data-lake-and-stream-analytics-tools-for-visual-studio), anterior a la versión 2.3.9000.1.
- [Azure Toolkit for Eclipse](#azure-toolkit-for-eclipse) versión 3.15.0 o anterior.
- [SDK para .NET](#sdk-for-net)
    - [versiones 1.x o 2.x](#versions-1x-and-2x): Usuarios que utilizan los métodos `GetClusterConfigurations`, `GetConnectivitySettings`, `ConfigureHttpSettings`, `EnableHttp` o `DisableHttp` desde la clase ConfigurationsOperationsExtensions.
    - [versiones 3.x y posteriores](#versions-3x-and-up): Usuarios que utilizan los métodos `Get`, `Update`, `EnableHttp` o `DisableHttp` desde la clase `ConfigurationsOperationsExtensions`.
- [SDK para Python](#sdk-for-python): Usuarios que utilizan los métodos `get` o `update` desde la clase `ConfigurationsOperations`.
- [SDK para Java](#sdk-for-java): Usuarios que utilizan los métodos `update` o `get` desde la clase `ConfigurationsInner`.
- [SDK para Go](#sdk-for-go): Usuarios que utilizan los métodos `Get` o `Update` desde la estructura `ConfigurationsClient`.
- [Az.HDInsight PowerShell](#azhdinsight-powershell) anterior a la versión 2.0.0.
Consulte las secciones siguientes (o use los vínculos anteriores) para ver los pasos de migración para su escenario.

### <a name="api"></a>API

Las API siguientes se puede cambiar o están en desuso:

- [**GET /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (se quita la información confidencial)
    - Utilizada anteriormente para obtener tipos de configuración individuales (incluidos secretos).
    - Esta llamada API devolverá ahora tipos de configuración individuales con secretos que se omiten. Para obtener todas las configuraciones, como los secretos, use la nueva llamada POST /configurations. Para obtener solo la configuración de la puerta de enlace, use la nueva llamada POST /getGatewaySettings.
- [**GET /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-configuration) (en desuso)
    - Se utilizaba para obtener todas las configuraciones (incluidos los secretos).
    - Ya no se admitirá esta llamada API. Para obtener todas las configuraciones futuras, use la nueva llamada POST /configurations. Para obtener las configuraciones con los parámetros confidenciales omitidos, utilice la llamada GET /configurations/{configurationName}.
- [**POST /configurations/{configurationName}** ](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings) (en desuso)
    - Anteriormente se usaba para actualizar las credenciales de puerta de enlace.
    - Esta llamada API está en desuso y ya no se admite. Use la nueva llamada POST /updateGatewaySettings en su lugar.

Se han agregado las API de reemplazo siguientes:</span>

- [**POST /configurations**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#list-configurations)
    - Utilice esta API para obtener todas las configuraciones, incluidos los secretos.
- [**POST /getGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#get-gateway-settings)
    - Utilice esta API para obtener la configuración de puerta de enlace.
- [**POST /updateGatewaySettings**](https://docs.microsoft.com/rest/api/hdinsight/hdinsight-cluster#update-gateway-settings)
    - Utilice esta API para actualizar la configuración de la puerta de enlace (nombre de usuario o contraseña).

### <a name="azure-hdinsight-tools-for-visual-studio-code"></a>Azure HDInsight Tools for Visual Studio Code

Si usa la versión 1.1.1 o anterior, actualice a la [versión más reciente de Azure HDInsight Tools for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=mshdinsight.azure-hdinsight&ssr=false) para evitar interrupciones.

### <a name="azure-toolkit-for-intellij"></a>Kit de herramientas de Azure para IntelliJ

Si usa la versión 3.20.0 o anterior, actualice a la [versión más reciente del complemento Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij) para evitar interrupciones.

### <a name="azure-data-lake-and-stream-analytics-tools-for-visual-studio"></a>Herramientas de Azure Data Lake y de Stream Analytics para Visual Studio

Actualice a la versión 2.3.9000.1 o posterior de [Azure Data Lake y Stream Analytics Tools para Visual Studio](https://marketplace.visualstudio.com/items?itemName=ADLTools.AzureDataLakeandStreamAnalyticsTools&ssr=false#overview) para evitar interrupciones.  Para obtener ayuda con la actualización, consulte la documentación [Actualización de Data Lake Tools para Visual Studio](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-visual-studio-tools-get-started#update-data-lake-tools-for-visual-studio).

### <a name="azure-toolkit-for-eclipse"></a>Kit de herramientas de Azure para Eclipse

Si usa la versión 3.15.0 o anterior, actualice a la [versión más reciente de Azure Toolkit for Eclipse](https://marketplace.eclipse.org/content/azure-toolkit-eclipse) para evitar interrupciones.

### <a name="sdk-for-net"></a>SDK para .NET

#### <a name="versions-1x-and-2x"></a>Versiones 1.x y 2.x

Actualice a la [versión 2.1.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/2.1.0) del SDK de HDInsight para .NET. Es posible que sea necesario realizar modificaciones mínimas en el código si está utilizando un método afectado por estos cambios:

- `ClusterOperationsExtensions.GetClusterConfigurations` ya **no devuelve parámetros confidenciales** como claves de almacenamiento (sitio principal) o credenciales de HTTP (puerta de enlace).
    - Para recuperar todas las configuraciones, incluidos los parámetros confidenciales, utilice `ClusterOperationsExtensions.ListConfigurations` a partir de ahora.  Tenga en cuenta que los usuarios con el rol "Lector" no podrán usar este método. Esto permite un control granular sobre el cual los usuarios pueden acceder a información confidencial para un clúster.
    - Para recuperar solo las credenciales de la puerta de enlace HTTP, utilice `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.GetConnectivitySettings` ahora está en desuso y se ha reemplazado por `ClusterOperationsExtensions.GetGatewaySettings`.

- `ClusterOperationsExtensions.ConfigureHttpSettings` ahora está en desuso y se ha reemplazado por `ClusterOperationsExtensions.UpdateGatewaySettings`.

- `ConfigurationsOperationsExtensions.EnableHttp` y `DisableHttp` ahora están en desuso. Ahora HTTP siempre está habilitado, por lo que estos métodos ya no son necesarios.

#### <a name="versions-3x-and-up"></a>Versiones 3.x y posteriores

Actualice a la [versión 5.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.HDInsight/5.0.0) o posterior del SDK de HDInsight para .NET. Es posible que sea necesario realizar modificaciones mínimas en el código si está utilizando un método afectado por estos cambios:

- [`ConfigurationOperationsExtensions.Get`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.get?view=azure-dotnet) **ya no devuelve parámetros confidenciales** como claves de almacenamiento (sitio principal) o credenciales de HTTP (puerta de enlace).
    - Para recuperar todas las configuraciones, incluidos los parámetros confidenciales, utilice [`ConfigurationOperationsExtensions.List`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.list?view=azure-dotnet) a partir de ahora.  Tenga en cuenta que los usuarios con el rol "Lector" no podrán usar este método. Esto permite un control granular sobre el cual los usuarios pueden acceder a información confidencial para un clúster. 
    - Para recuperar solo las credenciales de la puerta de enlace HTTP, utilice [`ClusterOperationsExtensions.GetGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.getgatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.Update`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.update?view=azure-dotnet) ahora está en desuso y se ha reemplazado por [`ClusterOperationsExtensions.UpdateGatewaySettings`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.clustersoperationsextensions.updategatewaysettings?view=azure-dotnet). 
- [`ConfigurationsOperationsExtensions.EnableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.enablehttp?view=azure-dotnet) y [`DisableHttp`](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.hdinsight.configurationsoperationsextensions.disablehttp?view=azure-dotnet) ahora están en desuso. Ahora HTTP siempre está habilitado, por lo que estos métodos ya no son necesarios.

### <a name="sdk-for-python"></a>SDK para Python

Actualice a la [versión 1.0.0](https://pypi.org/project/azure-mgmt-hdinsight/1.0.0/) o posterior del SDK de HDInsight para Python. Es posible que sea necesario realizar modificaciones mínimas en el código si está utilizando un método afectado por estos cambios:

- [`ConfigurationsOperations.get`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#get-resource-group-name--cluster-name--configuration-name--custom-headers-none--raw-false----operation-config-) **ya no devuelve parámetros confidenciales** como claves de almacenamiento (sitio principal) o credenciales de HTTP (puerta de enlace).
    - Para recuperar todas las configuraciones, incluidos los parámetros confidenciales, utilice [`ConfigurationsOperations.list`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#list-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-) a partir de ahora.  Tenga en cuenta que los usuarios con el rol "Lector" no podrán usar este método. Esto permite un control granular sobre el cual los usuarios pueden acceder a información confidencial para un clúster. 
    - Para recuperar solo las credenciales de la puerta de enlace HTTP, utilice [`ClusterOperations.get_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#get-gateway-settings-resource-group-name--cluster-name--custom-headers-none--raw-false----operation-config-).
- [`ConfigurationsOperations.update`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.configurationsoperations#update-resource-group-name--cluster-name--configuration-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-) ahora está en desuso y se ha reemplazado por [`ClusterOperations.update_gateway_settings`](https://docs.microsoft.com/python/api/azure-mgmt-hdinsight/azure.mgmt.hdinsight.operations.clustersoperations#update-gateway-settings-resource-group-name--cluster-name--parameters--custom-headers-none--raw-false--polling-true----operation-config-).

### <a name="sdk-for-java"></a>SDK para Java

Actualice a la [versión 1.0.0](https://search.maven.org/artifact/com.microsoft.azure.hdinsight.v2018_06_01_preview/azure-mgmt-hdinsight/) o posterior del SDK de HDInsight para Java. Es posible que sea necesario realizar modificaciones mínimas en el código si está utilizando un método afectado por estos cambios:

- [`ConfigurationsInner.get`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.get) **ya no devuelve parámetros confidenciales** como claves de almacenamiento (sitio principal) o credenciales de HTTP (puerta de enlace).
    - Para recuperar todas las configuraciones, incluidos los parámetros confidenciales, utilice [`ConfigurationsInner.list`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.configurationsinner.list?view=azure-java-stable) a partir de ahora.  Tenga en cuenta que los usuarios con el rol "Lector" no podrán usar este método. Esto permite un control granular sobre el cual los usuarios pueden acceder a información confidencial para un clúster. 
    - Para recuperar solo las credenciales de la puerta de enlace HTTP, utilice [`ClustersInner.getGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.getgatewaysettings?view=azure-java-stable).
- [`ConfigurationsInner.update`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018__06__01__preview.implementation._configurations_inner.update) ahora está en desuso y se ha reemplazado por [`ClustersInner.updateGatewaySettings`](https://docs.microsoft.com/java/api/com.microsoft.azure.management.hdinsight.v2018_06_01_preview.implementation.clustersinner.updategatewaysettings?view=azure-java-stable).

### <a name="sdk-for-go"></a>SDK para Go

Actualice a la [versión 27.1.0](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight) o posterior del SDK de HDInsight para Go. Es posible que sea necesario realizar modificaciones mínimas en el código si está utilizando un método afectado por estos cambios:

- [`ConfigurationsClient.get`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Get) **ya no devuelve parámetros confidenciales** como claves de almacenamiento (sitio principal) o credenciales de HTTP (puerta de enlace).
    - Para recuperar todas las configuraciones, incluidos los parámetros confidenciales, utilice [`ConfigurationsClient.list`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.List) a partir de ahora.  Tenga en cuenta que los usuarios con el rol "Lector" no podrán usar este método. Esto permite un control granular sobre el cual los usuarios pueden acceder a información confidencial para un clúster. 
    - Para recuperar solo las credenciales de la puerta de enlace HTTP, utilice [`ClustersClient.get_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.GetGatewaySettings).
- [`ConfigurationsClient.update`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ConfigurationsClient.Update) ahora está en desuso y se ha reemplazado por [`ClustersClient.update_gateway_settings`](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/preview/hdinsight/mgmt/2018-06-01-preview/hdinsight#ClustersClient.UpdateGatewaySettings).

### <a name="azhdinsight-powershell"></a>Az.HDInsight PowerShell
Actualice a [Az PowerShell versión 2.0.0](https://www.powershellgallery.com/packages/Az) o posterior para evitar interrupciones.  Es posible que sea necesario realizar modificaciones mínimas en el código si está utilizando un método afectado por estos cambios.
- `Grant-AzHDInsightHttpServicesAccess` ahora está en desuso y se ha reemplazado por el nuevo cmdlet `Set-AzHDInsightGatewayCredential`.
- Se ha actualizado `Get-AzHDInsightJobOutput` para admitir el acceso granular basado en roles a la clave de almacenamiento.
    - No se verán afectados los usuarios con roles de operador, colaborador o propietario de clúster de HDInsight.
    - Los usuarios con el rol de lector deben especificar el parámetro `DefaultStorageAccountKey` explícitamente.
- `Revoke-AzHDInsightHttpServicesAccess` ahora está en desuso. Ahora HTTP siempre está habilitado, por lo que este cmdlet ya no es necesario.
 Consulte la [guía de migración de az.HDInsight](https://github.com/Azure/azure-powershell/blob/master/documentation/migration-guides/Az.2.0.0-migration-guide.md#azhdinsight) para obtener más detalles.

## <a name="add-the-hdinsight-cluster-operator-role-assignment-to-a-user"></a>Adición de la asignación del rol de operador de clústeres de HDInsight a un usuario

Un usuario con el rol [Colaborador](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#contributor) o [Propietario](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) puede asignar el rol de [operador de clúster de HDInsight](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#hdinsight-cluster-operator) a los usuarios que deseen tener acceso de lectura o escritura a los valores de configuración del clúster de HDInsight (como las credenciales de la puerta de enlace del clúster y las claves de la cuenta de almacenamiento).

### <a name="using-the-azure-cli"></a>Uso de la CLI de Azure

La forma más sencilla de agregar esta asignación de roles es con el comando `az role assignment create` en la CLI de Azure.

> [!NOTE]
> Este comando debe ejecutarlo un usuario con los roles de colaborador o propietario, ya que solo ellos pueden otorgar estos permisos. `--assignee` es la dirección de correo electrónico del usuario a quien desea asignar el rol de operador de clústeres de HDInsight.

#### <a name="grant-role-at-the-resource-cluster-level"></a>Concesión del rol en el nivel del recurso (clúster)

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee <user@domain.com> --scope /subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.HDInsight/clusters/<ClusterName>
```

#### <a name="grant-role-at-the-resource-group-level"></a>Concesión del rol en el nivel del grupo de recursos

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com -g <ResourceGroupName>
```

#### <a name="grant-role-at-the-subscription-level"></a>Concesión del rol en el nivel de suscripción

```azurecli-interactive
az role assignment create --role "HDInsight Cluster Operator" --assignee user@domain.com
```

### <a name="using-the-azure-portal"></a>Uso de Azure Portal

Como alternativa, puede usar Azure Portal para agregar la asignación del rol de operador de clústeres de HDInsight a un usuario. Consulte la documentación [Administración del acceso a los recursos de Azure mediante RBAC y Azure Portal: Adición de una asignación de roles](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment).
