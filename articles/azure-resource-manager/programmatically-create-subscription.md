---
title: Creación de suscripciones de Azure Enterprise mediante programación | Microsoft Docs
description: Aprenda a crear suscripciones adicionales de Azure Enterprise o de Desarrollo/pruebas - Enterprise mediante programación.
services: azure-resource-manager
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: tomfitz
ms.openlocfilehash: 93df0c196d78a4685ff82108354b82a07d67695d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/08/2019
ms.locfileid: "59256930"
---
# <a name="programmatically-create-azure-enterprise-subscriptions-preview"></a>Creación de suscripciones de Azure Enterprise mediante programación (versión preliminar)

Como cliente de Azure sujeto a [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), puede crear suscripciones de EA (MS-AZR-0017P) y de Desarrollo/pruebas de EA (MS-AZR-0148P) mediante programación. En este artículo, se ofrece información sobre cómo crear suscripciones mediante programación con Azure Resource Manager.

Al crear una suscripción a Azure a partir de esta API, dicha suscripción se rige por el contrato en cuyo marco ha obtenido los servicios de Microsoft Azure de Microsoft o de un distribuidor autorizado. Para obtener más información, vea [Información legal de Microsoft Azure](https://azure.microsoft.com/support/legal/).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Requisitos previos

Debe tener un rol de propietario en la que desea crear suscripciones en la cuenta de inscripción. Existen dos formas de obtener estos roles:

* El Administrador de inscripción puede [Asegúrese de un propietario de la cuenta](https://ea.azure.com/helpdocs/addNewAccount) (inicio de sesión requerido) que le hace propietario de la cuenta de inscripción. Siga las instrucciones del correo electrónico de invitación que reciba para crear manualmente una suscripción inicial. Confirme la propiedad de cuenta y cree manualmente una suscripción inicial de Contrato Enterprise antes de continuar con el paso siguiente. No basta con limitarse a agregar la cuenta a la inscripción.

* Un propietario existente de la cuenta de inscripción puede [concederle acceso](grant-access-to-create-subscription.md). De forma similar, si desea usar una entidad de servicio para crear la suscripción a Contrato Enterprise, debe [conceder a dicha entidad de servicio la capacidad de crear suscripciones](grant-access-to-create-subscription.md).

## <a name="find-accounts-you-have-access-to"></a>Búsqueda de cuentas a las que tiene acceso

Cuando haya sido agregado a una inscripción de Azure EA como propietario de cuenta, Azure usa la relación cuenta-inscripción para determinar dónde se cobra la suscripción. Todas las suscripciones creadas en la cuenta se facturan a la inscripción de EA en la que se encuentra la cuenta. Para crear suscripciones, debe pasar valores sobre la cuenta de inscripción y las entidades de seguridad de usuario al propietario de la suscripción. 

Para ejecutar los comandos siguientes, debe iniciar sesión en el *directorio particular* del propietario de cuenta, que es el directorio en el que las suscripciones se crean de manera predeterminada.

# [<a name="rest"></a>REST](#tab/rest)

Solicite mostrar todas las cuentas de inscripción:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Azure responde con una lista de todas las cuentas de inscripción a las que tiene acceso:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

# [<a name="powershell"></a>PowerShell](#tab/azure-powershell)

Use el cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) para mostrar todas las cuentas de inscripción a las que tiene acceso.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure responde con una lista de los identificadores de objeto con direcciones de correo electrónico de cuentas.

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

# [<a name="azure-cli"></a>Azure CLI](#tab/azure-cli)

Use el comando [az billing enrollment-account list](https://aka.ms/EASubCreationPublicPreviewCLI) para mostrar todas las cuentas de inscripción a las que tiene acceso.

```azurecli-interactive 
az billing enrollment-account list
```

Azure responde con una lista de los identificadores de objeto con direcciones de correo electrónico de cuentas.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

---

Use la propiedad `principalName` para identificar la cuenta a la que quiere que se facturen las suscripciones. Use el elemento `id` como valor de `enrollmentAccount` que se utiliza para crear la suscripción en el paso siguiente.

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Creación de suscripciones con una cuenta de inscripción concreta 

En el ejemplo siguiente se crea una solicitud de creación de una suscripción de nombre *Suscripción Dev Team* y la oferta de suscripción es *MS-AZR - 0017P* (EA normal). La cuenta de inscripción es `747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (el valor de marcador de posición, que es un GUID), que es la cuenta de inscripción de SignUpEngineering@contoso.com. Opcionalmente, también agrega dos usuarios como propietarios de RBAC a la suscripción.

# [<a name="rest"></a>REST](#tab/rest)

Para crear la suscripción, use el elemento `id` del objeto `enrollmentAccount` en la ruta de acceso de la solicitud.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Nombre del elemento  | Obligatorio | Type   | DESCRIPCIÓN                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Sin       | string | Nombre para mostrar de la suscripción. Si no se especifica, se establece en el nombre de la oferta, por ejemplo, "Microsoft Azure Enterprise".                                 |
| `offerType`   | Sí      | string | Oferta de la suscripción. Las dos opciones para EA son [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso en producción) y [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desarrollo y pruebas, tiene que [activarse mediante el portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Sin        | string | Identificador de objeto de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla.  |

En la respuesta, se recupera un objeto `subscriptionOperation` para la supervisión. Cuando haya finalizado la creación de la suscripción, el objeto `subscriptionOperation` devolvería un objeto `subscriptionLink`, que tiene el identificador de suscripción.

# [<a name="powershell"></a>PowerShell](#tab/azure-powershell)

Para utilizar este módulo de versión preliminar, instálelo ejecutando primero `Install-Module Az.Subscription -AllowPrerelease`. Para asegurarse de que `-AllowPrerelease` funciona, instale una versión reciente de PowerShellGet desde [Obtención del módulo PowerShellGet](/powershell/gallery/installing-psget).

Use [New-AzSubscription](/powershell/module/az.subscription) junto con el identificador de objeto `enrollmentAccount` como parámetro `EnrollmentAccountObjectId` para crear una nueva suscripción. 

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId 747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx -OwnerObjectId <userObjectId>,<servicePrincipalObjectId>
```

| Nombre del elemento  | Obligatorio | Type   | DESCRIPCIÓN                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `Name` | Sin       | string | Nombre para mostrar de la suscripción. Si no se especifica, se establece en el nombre de la oferta, por ejemplo, "Microsoft Azure Enterprise".                                 |
| `OfferType`   | Sí      | string | Oferta de la suscripción. Las dos opciones para EA son [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso en producción) y [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desarrollo y pruebas, tiene que [activarse mediante el portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Sí       | string | El identificador de objeto de la cuenta de inscripción con la que se crea la suscripción y contra la que se realizan los cargos. Este valor es un GUID que se obtiene de `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Sin        | string | Identificador de objeto de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla.  |
| `OwnerSignInName`    | Sin        | string | Dirección de correo electrónico de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `OwnerObjectId`.|
| `OwnerApplicationId` | Sin        | string | Identificador de aplicación de cualquier entidad de servicio que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `OwnerObjectId`. Al usar este parámetro, la entidad de servicio debe tener [acceso de lectura al directorio](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Para obtener una lista completa de todos los parámetros, consulte [New-AzSubscription](/powershell/module/az.subscription.preview).

# [<a name="azure-cli"></a>Azure CLI](#tab/azure-cli)

Para utilizar esta extensión de versión preliminar, instálela ejecutando primero `az extension add --name subscription`.

Use el comando [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create) con el identificador de objeto `enrollmentAccount` como parámetro `enrollment-account-object-id` para crear una suscripción.

```azurecli-interactive 
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Nombre del elemento  | Obligatorio | Type   | DESCRIPCIÓN                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `display-name` | Sin       | string | Nombre para mostrar de la suscripción. Si no se especifica, se establece en el nombre de la oferta, por ejemplo, "Microsoft Azure Enterprise".                                 |
| `offer-type`   | Sí      | string | Oferta de la suscripción. Las dos opciones para EA son [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (uso en producción) y [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (desarrollo y pruebas, tiene que [activarse mediante el portal de EA](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Sí       | string | El identificador de objeto de la cuenta de inscripción con la que se crea la suscripción y contra la que se realizan los cargos. Este valor es un GUID que se obtiene de `az billing enrollment-account list`. |
| `owner-object-id`      | Sin        | string | Identificador de objeto de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla.  |
| `owner-upn`    | Sin        | string | Dirección de correo electrónico de cualquier usuario que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `owner-object-id`.|
| `owner-spn` | Sin        | string | Identificador de aplicación de cualquier entidad de servicio que quiera agregarse como propietario de RBAC en la suscripción al crearla. Puede utilizar este parámetro en lugar de `owner-object-id`. Al usar este parámetro, la entidad de servicio debe tener [acceso de lectura al directorio](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole).| 

Para obtener una lista completa de todos los parámetros, vea [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create).

----

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Limitaciones de la API de creación de suscripciones de Azure Enterprise

- Con esta API solo pueden crearse suscripciones de Azure Enterprise.
- Hay un límite de 50 suscripciones por cuenta. Después, solo pueden crearse suscripciones mediante el Centro de cuentas.
- Debe haber al menos una suscripción de EA o de Desarrollo/pruebas de EA en la cuenta, lo que indica que el propietario de cuenta ha pasado al menos una vez por el registro manual.
- Los usuarios que no son propietarios de cuenta, pero se han agregado a una cuenta de inscripción a través de RBAC, no pueden crear suscripciones mediante el Centro de cuentas.
- No puede seleccionar el inquilino para la suscripción en que se va a crear. La suscripción siempre se crea en el inquilino principal del propietario de cuenta. Para mover la suscripción a otro inquilino, vea cómo [cambiar un inquilino de la suscripción](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener un ejemplo sobre cómo crear suscripciones con. NET, vea [ejemplo de código en GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Ahora que ha creado una suscripción, puede conceder dicha capacidad a otros usuarios y entidades de servicio. Para más información, vea [Concesión de acceso para crear suscripciones de EA (versión preliminar)](grant-access-to-create-subscription.md).
* Para obtener más información sobre cómo administrar grandes cantidades de suscripciones mediante grupos de administración, consulte [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md).
