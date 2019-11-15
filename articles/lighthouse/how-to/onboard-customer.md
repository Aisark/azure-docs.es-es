---
title: Incorporación de un cliente a la administración de recursos delegados de Azure
description: Obtenga información sobre cómo incorporar un cliente a la administración de recursos delegados de Azure, lo que permite administrar sus recursos y acceder a ellos desde su propio inquilino.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 11/7/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 1d5e9c44fe7669a89c52d2ac14299c2687f11dc5
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827250"
---
# <a name="onboard-a-customer-to-azure-delegated-resource-management"></a>Incorporación de un cliente a la administración de recursos delegados de Azure

En este artículo se explica cómo, como proveedor de servicios, puede incorporar un cliente a la administración de recursos delegados de Azure, lo que permite tener acceso a sus recursos delegados (suscripciones o grupos de recursos) y administrarlos a través de su propio inquilino de Azure Active Directory (Azure AD). Aunque aquí nos referiremos a los proveedores de servicios y clientes, las empresas que administren varios inquilinos pueden usar el mismo proceso para consolidar su experiencia de administración.

Puede repetir este proceso si está administrando recursos para varios clientes. A continuación, cuando un usuario autorizado inicia sesión en el inquilino, se puede autorizar al usuario en los ámbitos del inquilino del cliente para realizar operaciones de administración sin tener que iniciar sesión en todos los inquilinos de cliente individuales.

Puede asociar el identificador de Microsoft Partner Network (MPN) con las suscripciones incorporadas para realizar un seguimiento de su impacto en las involucraciones de clientes y recibir el reconocimiento correspondiente. Para más información, consulte [Vinculación de un Id. de partner a cuentas de Azure](https://docs.microsoft.com/azure/billing/billing-partner-admin-link-started). Tenga en cuenta que deberá realizar esta asociación en el inquilino del proveedor de servicios.

> [!NOTE]
> Los clientes también se pueden incorporar cuando compran una oferta de servicios administrados (pública o privada) que haya publicado en Azure Marketplace. Para obtener más información, consulte [Publicar ofertas de servicios administrados en Azure Marketplace](publish-managed-services-offers.md). También puede usar el proceso de incorporación que se describe aquí con una oferta publicada en Azure Marketplace.

El proceso de incorporación requiere que se tomen medidas desde el inquilino del proveedor de servicios y del inquilino del cliente. Todos estos pasos se describen en este artículo.

> [!IMPORTANT]
> Actualmente, no se puede incorporar una suscripción (o un grupo de recursos de una suscripción) para la administración de recursos delegados de Azure si la suscripción usa Azure Databricks. Del mismo modo, si una suscripción se ha registrado para la incorporación con el proveedor de recursos **Microsoft.ManagedServices**, en este momento, no podrá crear un área de trabajo de Databricks para la suscripción.

## <a name="gather-tenant-and-subscription-details"></a>Recopilación de los detalles del inquilino y la suscripción

Para incorporar el inquilino de un cliente, este debe tener una suscripción activa a Azure. Necesitará saber:

- El identificador del inquilino del proveedor de servicios (donde va a administrar los recursos del cliente)
- El identificador del inquilino del cliente (cuyos recursos administrará el proveedor de servicios)
- Los identificadores de suscripción de cada suscripción específica del inquilino del cliente que administrará el proveedor de servicios (o que contenga los grupos de recursos que administrará el proveedor de servicios)

Si aún no tiene esta información, puede recuperarla de una de las siguientes maneras.

### <a name="azure-portal"></a>Portal de Azure

Para ver el identificador del inquilino, mantenga el mouse sobre el nombre de la cuenta en la parte superior derecha de Azure Portal o seleccione **Cambiar directorio**. Para seleccionar y copiar el identificador del inquilino, busque "Azure Active Directory" desde dentro del portal y, a continuación, seleccione **Propiedades** y copie el valor que se muestra en campo **Id. de directorio**. Para buscar el identificador de una suscripción, busque "Suscripciones" y, a continuación, seleccione el identificador de suscripción adecuado.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Select-AzSubscription <subscriptionId>
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account set --subscription <subscriptionId/name>
az account show
```

> [!NOTE]
> Al incorporar una suscripción (o uno o varios grupos de recursos dentro de una suscripción) mediante el proceso que se describe aquí, el proveedor de recursos **Microsoft.ManagedServices** se registrará para esa suscripción.

## <a name="define-roles-and-permissions"></a>Definir roles y permisos

Como proveedor de servicios, es posible que quiera realizar varias tareas para un solo cliente, lo que requiere un acceso diferente para distintos ámbitos. Puede definir tantas autorizaciones como necesite para asignar [roles integrados de control de acceso basado en rol (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) a los usuarios de su inquilino.

Para facilitar la administración, se recomienda usar grupos de usuarios de Azure AD para cada rol, lo que le permite agregar o quitar usuarios individuales en el grupo, en lugar de asignar permisos directamente a ese usuario. También es posible que quiera asignar roles a una entidad de servicio. Asegúrese de seguir el principio de privilegios mínimos para que los usuarios solo tengan los permisos necesarios para completar su trabajo. Para obtener recomendaciones e información sobre los roles admitidos, consulte [Inquilinos, usuarios y roles en escenarios de Azure Lighthouse](../concepts/tenants-users-roles.md).

Para definir las autorizaciones, debe conocer los valores de identificador de cada usuario, grupo de usuarios o entidad de servicio al que quiera conceder acceso. También necesitará el identificador de definición de roles para cada rol integrado que quiera asignar. Si aún no los tiene, puede recuperarlos de una de las siguientes maneras.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
(Get-AzADGroup -DisplayName '<yourGroupName>').id

# To retrieve the objectId for an Azure AD user
(Get-AzADUser -UserPrincipalName '<yourUPN>').id

# To retrieve the objectId for an SPN
(Get-AzADApplication -DisplayName '<appDisplayName>').objectId

# To retrieve role definition IDs
(Get-AzRoleDefinition -Name '<roleName>').id
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# To retrieve the objectId for an Azure AD group
az ad group list --query "[?displayName == '<yourGroupName>'].objectId" --output tsv

# To retrieve the objectId for an Azure AD user
az ad user show --upn-or-object-id "<yourUPN>" –-query "objectId" --output tsv

# To retrieve the objectId for an SPN
az ad sp list --query "[?displayName == '<spDisplayName>'].objectId" --output tsv

# To retrieve role definition IDs
az role definition list --name "<roleName>" | grep name
```
> [!TIP]
> Se recomienda asignar el [rol de eliminación de asignación del registro de servicios administrados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) al incorporar un cliente, de modo que los usuarios del inquilino puedan [quitar el acceso a la delegación](#remove-access-to-a-delegation) más adelante si es necesario. Si este rol no está asignado, solo un usuario puede quitar los recursos delegados del inquilino del cliente.

## <a name="create-an-azure-resource-manager-template"></a>Creación de una plantilla de Azure Resource Manager

Para incorporar el cliente, deberá crear una plantilla de [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/) para la oferta con la información siguiente. Los valores **mspOfferName** y **mspOfferDescription** son visibles para el cliente al ver los detalles de la oferta en la [página de proveedores de servicios](view-manage-service-providers.md) de Azure Portal.

|Campo  |Definición  |
|---------|---------|
|**mspOfferName**     |Nombre que describe esta definición. Este valor se muestra al cliente como el título de la oferta.         |
|**mspOfferDescription**     |Breve descripción de la oferta (por ejemplo, "oferta de administración de VM de Contoso")      |
|**managedByTenantId**     |El identificador de inquilino         |
|**authorizations**     |Los valores de **principalId** para los usuarios/grupos/SPN del inquilino, cada uno de ellos con un valor de **principalIdDisplayName** para ayudar a su cliente a entender el propósito de la autorización y asignarla a un valor de **roleDefinitionId** integrado para especificar el nivel de acceso         |

Para incorporar una suscripción de cliente, use la plantilla adecuada de Azure Resource Manager que proporcionamos en nuestro [repositorio de ejemplos](https://github.com/Azure/Azure-Lighthouse-samples/), junto con un archivo de parámetros correspondiente que modifique para que coincida con la configuración y defina su autorizaciones. Se proporcionan plantillas independientes en función de si se incorpora una suscripción completa, un grupo de recursos o varios grupos de recursos dentro de una suscripción. También proporcionamos una plantilla que se puede usar para los clientes que han adquirido una oferta de servicios administrados que ha publicado en Azure Marketplace, si prefiere incorporar sus suscripciones de esta manera.

|Para incorporar esto  |Use esta plantilla de Azure Resource Manager  |Y modifique este archivo de parámetros |
|---------|---------|---------|
|Subscription   |[delegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.json)  |[delegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management/delegatedResourceManagement.parameters.json)    |
|Resource group   |[rgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.json)  |[rgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/rgDelegatedResourceManagement.parameters.json)    |
|Varios grupos de recursos de una suscripción   |[multipleRgDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.json)  |[multipleRgDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management/multipleRgDelegatedResourceManagement.parameters.json)    |
|Suscripción (al usar una oferta publicada en Azure Marketplace)   |[marketplaceDelegatedResourceManagement.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.json)  |[marketplaceDelegatedResourceManagement.parameters.json](https://github.com/Azure/Azure-Lighthouse-samples/blob/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management/marketplaceDelegatedResourceManagement.parameters.json)    |

> [!IMPORTANT]
> El proceso que se describe aquí requiere una implementación independiente para cada suscripción que se incorpore. También se necesitan implementaciones independientes si se incorporan varios grupos de recursos en distintas suscripciones. Sin embargo, la incorporación de varios grupos de recursos dentro de una sola suscripción puede realizarse en una implementación.
>
> También se necesitan implementaciones independientes para varias ofertas que se aplican a la misma suscripción (o grupos de recursos dentro de una suscripción). Cada oferta aplicada debe usar un valor de **mspOfferName** diferente.

En el ejemplo siguiente se muestra el archivo **delegatedResourceManagement.parameters.json**, que se usará para incorporar una suscripción. Los archivos de parámetros del grupo de recursos (situados en la carpeta [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management)) son similares, pero también incluyen un parámetro **rgName** para identificar los grupos de recursos específicos que se incorporarán.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "mspOfferName": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "mspOfferDescription": {
            "value": "Fabrikam Managed Services - Interstellar"
        },
        "managedByTenantId": {
            "value": "df4602a3-920c-435f-98c4-49ff031b9ef6"
        },
        "authorizations": {
            "value": [
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "0019bcfb-6d35-48c1-a491-a701cf73b419",
                    "principalIdDisplayName": "Tier 1 Support",
                    "roleDefinitionId": "36243c78-bf99-498c-9df9-86d9f8d28608"
                },
                {
                    "principalId": "0afd8497-7bff-4873-a7ff-b19a6b7b332c",
                    "principalIdDisplayName": "Tier 2 Support",
                    "roleDefinitionId": "acdd72a7-3385-48ef-bd42-f606fba81ae7"
                },
                {
                    "principalId": "9fe47fff-5655-4779-b726-2cf02b07c7c7",
                    "principalIdDisplayName": "Service Automation Account",
                    "roleDefinitionId": "b24988ac-6180-42a0-ab88-20f7382dd24c"
                },
                {
                    "principalId": "3kl47fff-5655-4779-b726-2cf02b05c7c4",
                    "principalIdDisplayName": "Policy Automation Account",
                    "roleDefinitionId": "18d7d88d-d35e-4fb5-a5c3-7773c20a72d9",
                    "delegatedRoleDefinitionIds": [
                        "b24988ac-6180-42a0-ab88-20f7382dd24c",
                        "92aaf0da-9dab-42b6-94a3-d43ce8d16293"
                    ]
                }
            ]
        }
    }
}
```
La última autorización del ejemplo anterior agrega un valor de **principalId** con el rol de administrador de acceso de usuario (18d7d88d-d35e-4fb5-a5c3-7773c20a72d9). Al asignar este rol, debe incluir la propiedad **delegatedRoleDefinitionIds** y uno o más roles integrados. El usuario creado en esta autorización podrá asignar los roles integrados a las [identidades administradas](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview), algo necesario para [implementar directivas que pueden corregirse](deploy-policy-remediation.md). No se aplicará a este usuario ningún otro permiso asociado normalmente al rol Administrador de acceso de usuario.

## <a name="deploy-the-azure-resource-manager-templates"></a>Implementación de las plantillas de Azure Resource Manager

Una vez actualizado el archivo de parámetros, el cliente debe implementar la plantilla de Azure Resource Manager en el inquilino de su cliente como implementación de nivel de suscripción. Se necesita una implementación independiente para cada suscripción que quiera incorporar a la administración de recursos delegados de Azure (o para cada suscripción que contenga grupos de recursos que quiera incorporar).

Dado que se trata de una implementación de nivel de suscripción, no se puede iniciar en Azure Portal. La implementación puede realizarse mediante PowerShell o la CLI de Azure, como se muestra a continuación.

> [!IMPORTANT]
> La implementación debe realizarse desde una cuenta que no sea de invitado en el inquilino del cliente que tenga el [rol Propietario integrado](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner) para la suscripción que se va a incorporar (o que contiene los grupos de recursos que se están incorporando). Para ver todos los usuarios que puedan delegar la suscripción, cualquiera de los usuarios del inquilino del cliente puede seleccionar la suscripción en Azure Portal, abrir **Control de acceso (IAM)** y [ver todos los usuarios con el rol Propietario](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#view-roles-and-permissions).

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateFile <pathToTemplateFile> `
                 -TemplateParameterFile <pathToParameterFile> `
                 -Verbose

# Deploy Azure Resource Manager template that is located externally
New-AzDeployment -Name <deploymentName> `
                 -Location <AzureRegion> `
                 -TemplateUri <templateUri> `
                 -TemplateParameterUri <parameterUri> `
                 -Verbose
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Deploy Azure Resource Manager template using template and parameter file locally
az deployment create –-name <deploymentName> \
                     --location <AzureRegion> \
                     --template-file <pathToTemplateFile> \
                     --parameters <parameters/parameterFile> \
                     --verbose

# Deploy external Azure Resource Manager template, with local parameter file
az deployment create –-name <deploymentName \
                     –-location <AzureRegion> \
                     --template-uri <templateUri> \
                     --parameters <parameterFile> \
                     --verbose
```

## <a name="confirm-successful-onboarding"></a>Confirmar la incorporación correcta

Cuando una suscripción de cliente se incorpora correctamente a la administración de recursos delegados de Azure, los usuarios del inquilino del proveedor de servicios pueden ver la suscripción y sus recursos (si se les ha concedido acceso a ellos mediante el proceso anterior, ya sea individualmente o como miembro de un grupo de Azure AD con los permisos adecuados). Para confirmarlo, compruebe que la suscripción aparece de una de las siguientes maneras.  

### <a name="azure-portal"></a>Portal de Azure

En el inquilino del proveedor de servicios:

1. Vaya a la página [Mis clientes](view-manage-customers.md).
2. Seleccione **Clientes**.
3. Confirme que puede ver las suscripciones con el nombre de la oferta que proporcionó en la plantilla de Resource Manager.

> [!IMPORTANT]
> Para ver la suscripción delegada en [Mis clientes](view-manage-customers.md), los usuarios del inquilino del proveedor de servicios deben tener concedido el rol [Lector](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (u otro rol integrado que incluya acceso de lectura) al incorporar la suscripción a la administración de recursos delegados de Azure.

En el inquilino del cliente:

1. Vaya a la [página Proveedores de servicios](view-manage-service-providers.md).
2. Seleccione **Ofertas del proveedor de servicios**.
3. Confirme que puede ver las suscripciones con el nombre de la oferta que proporcionó en la plantilla de Resource Manager.

> [!NOTE]
> Una vez completada la implementación, las actualizaciones pueden tardar unos minutos en verse reflejadas en Azure Portal.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

Get-AzContext
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

az account list
```

## <a name="remove-access-to-a-delegation"></a>Quitar el acceso a una delegación

De forma predeterminada, un usuario en el inquilino del cliente que tenga los permisos adecuados puede quitar el acceso a los recursos que se han delegado a un proveedor de servicios en la [página Proveedores de servicios](view-manage-service-providers.md#add-or-remove-service-provider-offers) de Azure Portal.

Si ha incluido usuarios con el [ rol de eliminación de asignación del registros de servicios administrados](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role) al incorporar el cliente para la administración de recursos delegados de Azure, los usuarios en el inquilino también pueden quitar la delegación. Al hacerlo, ningún usuario en el inquilino del proveedor de servicios podrá tener acceso a los recursos que se habían delegado previamente.

En el ejemplo siguiente se muestra una asignación que concede el **rol de eliminación de asignación del registro de los servicios administrados** que se puede incluir en un archivo de parámetros:

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Un usuario con este permiso puede quitar una delegación de una de las siguientes maneras.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>CLI de Azure

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete –assignment <id or full resourceId>
```

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
- [Vea y administre sus clientes](view-manage-customers.md) desde **Mis clientes**, en Azure Portal.
