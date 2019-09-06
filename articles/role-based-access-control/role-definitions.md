---
title: Descripción de definiciones de roles en RBAC para recursos de Azure | Microsoft Docs
description: Obtenga información acerca de las definiciones de roles en el control de acceso basado en rol (RBAC) para la administración de acceso específico de recursos de Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: ''
ms.openlocfilehash: 4bf2e057f4c5dad650834f9b42c75be3aedec46e
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142852"
---
# <a name="understand-role-definitions-for-azure-resources"></a>Descripción de definiciones de roles para los recursos de Azure

Si quiere comprender el funcionamiento de un rol o si va a crear su propio [rol personalizado](custom-roles.md) para recursos de Azure, resulta útil entender cómo se definen los roles. En este artículo se describen los detalles de las definiciones de roles y se proporcionan algunos ejemplos.

## <a name="role-definition-structure"></a>Estructura de definición de roles

Una *definición de roles* es una recopilación de permisos. A veces, se denomina *rol* simplemente. Una definición de roles enumera las operaciones que se pueden realizar, por ejemplo, de lectura, escritura y eliminación. También puede enumerar las operaciones que no se pueden realizar u operaciones relacionadas con datos subyacentes. Tiene la siguiente estructura:

```
Name
Id
IsCustom
Description
Actions []
NotActions []
DataActions []
NotDataActions []
AssignableScopes []
```

Se especifican las operaciones con cadenas que tienen el formato siguiente:

- `{Company}.{ProviderName}/{resourceType}/{action}`

La parte `{action}` de una cadena de la operación especifica el tipo de operaciones que puede realizar en un tipo de recurso. Por ejemplo, verá las siguientes subcadenas en `{action}`:

| Subcadena de acción    | DESCRIPCIÓN         |
| ------------------- | ------------------- |
| `*` | El carácter comodín concede acceso a todas las operaciones que coinciden con la cadena. |
| `read` | Permite operaciones de lectura (GET). |
| `write` | Permite operaciones de escritura (PUT o PATCH). |
| `action` | Permite operaciones personalizadas, como reiniciar máquinas virtuales (POST). |
| `delete` | Permite operaciones de eliminación (DELETE). |

Esta es la definición de roles [Colaborador](built-in-roles.md#contributor) en formato JSON. La operación de carácter comodín (`*`) en `Actions` indica que la entidad de seguridad asignada a este rol puede realizar todas las acciones o, en otras palabras, administrar todo el contenido. Esto incluye las acciones definidas en el futuro, a medida que Azure agregue nuevos tipos de recursos. Las operaciones en `NotActions` se restan de `Actions`. En el caso del rol [Colaborador](built-in-roles.md#contributor), `NotActions` le quita la capacidad para administrar y asignar el acceso a los recursos.

```json
{
  "Name": "Contributor",
  "Id": "b24988ac-6180-42a0-ab88-20f7382dd24c",
  "IsCustom": false,
  "Description": "Lets you manage everything except access to resources.",
  "Actions": [
    "*"
  ],
  "NotActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

## <a name="management-and-data-operations"></a>Administración y operaciones de datos

El control de acceso basado en rol para las operaciones de administración se especifica en las propiedades `Actions` y `NotActions` de una definición de roles. A continuación, se incluyen algunos ejemplos de operaciones de administración de Azure:

- Administrar el acceso a una cuenta de almacenamiento
- Crear, actualizar o eliminar un contenedor de blobs
- Eliminar un grupo de recursos y todos sus recursos

El acceso de administración no se hereda a los datos, dado que el método de autenticación de contenedor se establece en "Cuenta de usuario de Azure AD". Esta separación impide que los roles con caracteres comodín (`*`) tengan acceso no restringido a los datos. Por ejemplo, si un usuario tiene el rol [Lector](built-in-roles.md#reader) en una suscripción, podrá ver la cuenta de almacenamiento, pero, de forma predeterminada, no podrá ver los datos subyacentes.

Anteriormente, el control de acceso basado en rol no se usaba para operaciones de datos. La autorización para las operaciones de datos variaba entre proveedores de recursos. El mismo modelo de autorización de control de acceso basado en rol que se usa para las operaciones de administración se ha ampliado a las operaciones de datos.

Para admitir las operaciones de datos, se agregaron nuevas propiedades de datos a la estructura de la definición de roles. Las operaciones de datos se especifican en las propiedades `DataActions` y `NotDataActions`. Al agregar estas propiedades de datos, se mantiene la separación entre la administración y los datos. Esto impide que las asignaciones de roles actuales con caracteres comodín (`*`) de repente tengan acceso a los datos. Estas son algunas operaciones de datos que se pueden especificar en `DataActions` y `NotDataActions`:

- Leer una lista de blobs en un contenedor
- Escribir un blob de almacenamiento en un contenedor
- Eliminar un mensaje de una cola

Esta es la definición del rol [Lector de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-reader), que incluye operaciones en las propiedades `Actions` y `DataActions`. Esta función permite leer el contenedor de blobs y también los datos de blob subyacentes.

```json
{
  "Name": "Storage Blob Data Reader",
  "Id": "2a2b9908-6ea1-4ae2-8e65-a410df84e7d1",
  "IsCustom": false,
  "Description": "Allows for read access to Azure Storage blob containers and data",
  "Actions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/read"
  ],
  "NotActions": [],
  "DataActions": [
    "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read"
  ],
  "NotDataActions": [],
  "AssignableScopes": [
    "/"
  ]
}
```

Solo se pueden agregar las operaciones de datos a las propiedades `DataActions` y `NotDataActions`. Los proveedores de recursos identifican qué operaciones son operaciones de datos, al establecer la propiedad `isDataAction` en `true`. Para ver una lista de las operaciones donde `isDataAction` es `true`, consulte [Resource provider operations](resource-provider-operations.md) (Operaciones de proveedor de recursos). Los roles que no tienen operaciones de datos no necesitan las propiedades `DataActions` y `NotDataActions` en la definición de roles.

Azure Resource Manager controla la autorización de todas las llamadas API de operación de administración. Un proveedor de recursos o Azure Resource Manager controlan la autorización de las llamadas API de operación de datos.

### <a name="data-operations-example"></a>Ejemplo de operaciones de datos

Para comprender mejor cómo funcionan las operaciones de datos y de administración, veamos un ejemplo concreto. A Alice se le ha asignado el rol [Propietario](built-in-roles.md#owner) en el ámbito de la suscripción. A Bob se le ha asignado el rol [Colaborador de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-contributor) en un ámbito de la cuenta de almacenamiento. En el siguiente diagrama se muestra este ejemplo.

![El control de acceso basado en rol se ha ampliado para admitir operaciones de datos y de administración](./media/role-definitions/rbac-management-data.png)

El rol [Propietario](built-in-roles.md#owner) de Alice y el rol [Colaborador de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-contributor) de Bob tienen las siguientes acciones:

Propietario

&nbsp;&nbsp;&nbsp;&nbsp;Acciones<br>
&nbsp;&nbsp;&nbsp;&nbsp;`*`

Colaborador de datos de blobs de almacenamiento

&nbsp;&nbsp;&nbsp;&nbsp;Acciones<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/write`<br>
&nbsp;&nbsp;&nbsp;&nbsp;DataActions<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br>
&nbsp;&nbsp;&nbsp;&nbsp;`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`

Puesto que Alice tiene una acción de carácter comodín (`*`) en un ámbito de suscripción, sus permisos se heredan para poder realizar todas las acciones de administración. Alice puede leer, escribir y eliminar contenedores. Sin embargo, Alice no puede realizar operaciones de datos sin pasos adicionales. Por ejemplo, de forma predeterminada, Alice no puede leer los blobs de un contenedor. Para leer los blobs, Alice tendría que recuperar las claves de acceso de almacenamiento y usarlas para acceder a los blobs.

Los permisos de Bob se limitan a solo los valores de `Actions` y `DataActions` especificados en el rol [Colaborador de datos de blobs de almacenamiento](built-in-roles.md#storage-blob-data-contributor). Según el rol, Bob puede realizar operaciones de datos y de administración. Por ejemplo, Bob puede leer, escribir y eliminar contenedores de la cuenta de almacenamiento especificada y también puede leer, escribir y eliminar los blobs.

Para más información acerca de la administración y la seguridad en el plano de datos, consulte la [guía de seguridad de Azure Storage](../storage/common/storage-security-guide.md).

### <a name="what-tools-support-using-rbac-for-data-operations"></a>¿Qué herramientas se admiten cuando se usa RBAC para las operaciones de datos?

Para visualizar y trabajar con operaciones de datos, debe tener las versiones correctas de las herramientas o SDK:

| Herramienta  | Versión  |
|---------|---------|
| [Azure PowerShell](/powershell/azure/install-az-ps) | 1.1.0 o posterior |
| [CLI de Azure](/cli/azure/install-azure-cli) | 2.0.30 o posterior |
| [Azure para .NET](/dotnet/azure/) | 2.8.0-versión preliminar o posterior |
| [Azure SDK para Go](/azure/go/azure-sdk-go-install) | 15.0.0 o posterior |
| [Azure para Java](/java/azure/) | 1.9.0 o posterior |
| [Azure para Python](/azure/python/) | 0.40.0 o posterior |
| [SDK de Azure para Ruby](https://rubygems.org/gems/azure_sdk) | 0.17.1 o posterior |

Para ver y usar las operaciones de datos en la API REST, el valor del parámetro **api-version** debe ser la siguiente versión o las versiones posteriores:

- 2018-07-01

## <a name="actions"></a>Acciones

El permiso `Actions` especifica las operaciones de administración que el rol permite realizar. Se trata de una colección de cadenas de operación que identifican a las operaciones protegibles de proveedores de recursos de Azure. A continuación, se incluyen algunos ejemplos de operaciones de administración que se pueden usar en `Actions`.

| Cadena de la operación    | DESCRIPCIÓN         |
| ------------------- | ------------------- |
| `*/read` | Concede acceso a las operaciones de lectura a todos los tipos de recursos de todos los proveedores de recursos de Azure.|
| `Microsoft.Compute/*` | Concede acceso a todas las operaciones a todos los tipos de recursos del proveedor de recursos Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede acceso a las operaciones de lectura a todos los tipos de recursos del proveedor de recursos Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede acceso a todas las operaciones de las máquinas virtuales y a sus tipos de recursos secundarios.|
| `microsoft.web/sites/restart/Action` | Concede acceso para reiniciar una aplicación web.|

## <a name="notactions"></a>NotActions

El permiso `NotActions` especifica las operaciones de administración que se excluyen de los `Actions` permitidos. Use el permiso `NotActions` si el conjunto de operaciones que quiere permitir se define más fácilmente mediante la exclusión de las operaciones restringidas. El acceso concedido por un rol (permisos vigentes) se calcula restando las operaciones de `NotActions` de las de `Actions`.

> [!NOTE]
> Si un usuario tiene asignado un rol que excluye una operación en `NotActions` y se le asigna un segundo rol que sí que concede acceso a esa operación, el usuario puede realizar dicha operación. `NotActions` no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de operaciones permitidas cuando es necesario excluir operaciones específicas.
>

## <a name="dataactions"></a>DataActions

El permiso `DataActions` especifica las operaciones de datos que el rol permite realizar en los datos dentro de ese objeto. Por ejemplo, si un usuario tiene acceso para leer datos de blobs de una cuenta de almacenamiento, puede leer los blobs en esa cuenta de almacenamiento. A continuación, se incluyen algunos ejemplos de operaciones de datos que se pueden usar en `DataActions`.

| Cadena de la operación    | DESCRIPCIÓN         |
| ------------------- | ------------------- |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/read` | Devuelve un blob o una lista de blobs. |
| `Microsoft.Storage/storageAccounts/ blobServices/containers/blobs/write` | Devuelve el resultado de la escritura de un blob. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/read` | Devuelve un mensaje. |
| `Microsoft.Storage/storageAccounts/ queueServices/queues/messages/*` | Devuelve un mensaje o el resultado de la escritura o eliminación de un mensaje. |

## <a name="notdataactions"></a>NotDataActions

El permiso `NotDataActions` especifica las operaciones de datos que se excluyen de los valores `DataActions` permitidos. El acceso concedido por un rol (permisos vigentes) se calcula restando las operaciones de `NotDataActions` de las de `DataActions`. Cada proveedor de recursos proporciona su conjunto respectivo de API para completar las operaciones de datos.

> [!NOTE]
> Si un usuario tiene asignado un rol que excluye una operación de datos en `NotDataActions` y se le asigna un segundo rol que sí que concede acceso a esa operación de datos, el usuario puede realizar dicha operación de datos. `NotDataActions` no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de operaciones de datos permitidas cuando es necesario excluir operaciones de datos específicas.
>

## <a name="assignablescopes"></a>Ámbitos asignables

La propiedad `AssignableScopes` especifica los ámbitos (suscripciones, grupos de recursos o recursos) en los que la definición de rol está disponible. Puede permitir que el rol esté disponible para su asignación solamente en las suscripciones o los grupos de recursos que lo requieran, sin necesidad de abarrotar la experiencia de usuario con el resto de las suscripciones o grupos de recursos. Tiene que utilizar al menos una suscripción, grupo de recursos o identificador de recurso.

Los roles integrados tienen `AssignableScopes` establecido en el ámbito raíz (`"/"`). El ámbito raíz indica que el rol está disponible para la asignación en todos los ámbitos. Ejemplos de ámbitos asignables válidos son:

| Escenario | Ejemplo |
|----------|---------|
| El rol está disponible para la asignación en una única suscripción. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| El rol está disponible para la asignación en dos suscripciones. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| El rol está disponible para la asignación solo en el grupo de recursos de red. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| El rol está disponible para la asignación en todos los ámbitos (se aplica solo a roles integrados) | `"/"` |

Para obtener información acerca de `AssignableScopes` para roles personalizados, consulte [Roles personalizados en los recursos de Azure](custom-roles.md).

## <a name="next-steps"></a>Pasos siguientes

* [Roles integrados en los recursos de Azure](built-in-roles.md)
* [Roles personalizados en los recursos de Azure](custom-roles.md)
* [Operaciones del proveedor de recursos de Azure Resource Manager](resource-provider-operations.md)
