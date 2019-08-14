---
title: Vinculación de una cuenta de Azure a un Id. de partner | Microsoft Docs
description: Controle las interacciones con clientes de Azure vinculando un Id. de partner a la cuenta de usuario que utiliza para administrar los recursos del cliente.
services: billing
author: dhirajgandhi
manager: dhgandhi
ms.author: banders
ms.date: 03/12/2019
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: 0448ffbccddc913bd6359f5f6bbf42988239afb4
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706402"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Vinculación de un Id. de partner a cuentas de Azure

Los asociados de Microsoft proporcionan servicios que ayudan a los clientes lograr los objetivos del negocio y la misión con productos de Microsoft. Cuando actúa en nombre del cliente que administra y configura los servicios de Azure y les da soporte, los usuarios asociados deberán acceder al entorno del cliente. Mediante el vínculo de administración de socios, puede asociar su identificador de red de asociado con las credenciales usadas para la entrega del servicio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-access-from-your-customer"></a>Obtención de acceso del cliente

Antes de vincular su Id. de partner, el cliente debe concederle acceso a sus recursos de Azure mediante una de las siguientes opciones:

- **Usuario invitado**: el cliente puede agregarle como usuario invitado y asignar roles de control de acceso basado en rol (RBAC). Para más información, consulte [Adición de usuarios invitados de otro directorio](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

- **Cuenta de directorio**: el cliente puede crear una cuenta de usuario automáticamente en su propio directorio y asignarle cualquier rol RBAC.

- **Entidad de servicio**: el cliente puede añadir una aplicación o un script de su organización en el directorio del cliente y asignarle cualquier rol RBAC. La identidad de la aplicación o el script se conoce como entidad de servicio.

## <a name="link-to-a-partner-id"></a>Vinculación a un Id. de partner

Cuando acceda a los recursos del cliente, use Azure Portal, PowerShell o la CLI de Azure para vincular su identificador de Microsoft Partner Network (Id. de MPN) a su identificador de usuario o entidad de servicio. Vincule el Id. de partner en cada inquilino de cliente.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Uso de Azure Portal para vincular a un nuevo Id. de partner

1. Vaya al [vínculo a un identificador de partner](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) en Azure Portal.

2. Inicie sesión en el Portal de Azure.

3. Escriba el identificador de partner de Microsoft. El Id. de partner es el identificador de [Microsoft Partner Network](https://partner.microsoft.com/) de su organización.

   ![Captura de pantalla que muestra el vínculo a un identificador de partner](./media/billing-link-partner-id/link-partner-ID.PNG)

4. Para vincular un identificador de partner con otro cliente, cambie el directorio. En **Cambiar directorio**, seleccione su directorio.

   ![Captura de pantalla que muestra la opción Cambiar directorio](./media/billing-link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Uso de PowerShell para vincular a un nuevo Id. de partner

1. Instale el módulo de PowerShell [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/).

2. Inicie sesión en el inquilino de cliente con la cuenta de usuario o la entidad de servicio. Para obtener más información, consulte [Inicio de sesión con PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Vincule el nuevo Id. de partner. El Id. de partner es el identificador de [Microsoft Partner Network](https://partner.microsoft.com/) de su organización.

    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Obtención del Id. de partner vinculado
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Actualización del Id. de partner vinculado
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Eliminación del Id. de partner vinculado
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Uso de la CLI de Azure para vincular un nuevo Id. de partner
1. Instale la extensión de la CLI de Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Inicie sesión en el inquilino de cliente con la cuenta de usuario o la entidad de servicio. Para obtener más información, consulte [Inicio de sesión con la CLI de Azure](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Vincule el nuevo Id. de partner. El Id. de partner es el identificador de [Microsoft Partner Network](https://partner.microsoft.com/) de su organización.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Obtención del Id. de partner vinculado
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Actualización del Id. de partner vinculado
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Eliminación del Id. de partner vinculado
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Pasos siguientes

Únase al debate en la [comunidad de asociados de Microsoft ](https://aka.ms/PALdiscussion) para recibir actualizaciones o enviar comentarios.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**¿Quién puede vincular el Id. de partner?**

Cualquier usuario de la organización asociada que administre los recursos de Azure del cliente puede vincular el identificador de partner a la cuenta.

**¿Se puede cambiar un Id. de partner después de vincularlo?**

Sí. El Id. de partner vinculado se puede cambiar, agregar o quitar.

**¿Qué ocurre si un usuario tiene una cuenta en varios inquilinos de cliente?**

El vínculo entre el Id. de partner y la cuenta se realiza para cada inquilino de cliente. Vincule el Id. de partner en cada inquilino de cliente.

**¿Pueden otros partners o clientes editar o quitar el vínculo con el Id. de partner?**

El vínculo está asociado al nivel de cuenta del usuario. Solo usted puede editar o quitar el vínculo con el Id. de partner. Ni el cliente ni ningún otro partner podrán modificar el vínculo con el Id. de partner.


**¿Qué id. de MPN debo usar si mi compañía tiene varios?**

Las cuentas de ubicación de asociados y los identificadores MPN se deben usar para vincular el identificador de asociado.  Más información sobre las [cuentas de asociado](https://docs.microsoft.com/partner-center/account-structure)

**¿Dónde puedo encontrar informes de ingresos influenciados para el identificador de asociado vinculado?**

Los informes de rendimiento del producto en la nube están disponibles para los asociados en el centro de asociados en el panel [My Insights dashboard](https://partner.microsoft.com/membership/reports/myinsights) (Panel Mi información). Debe seleccionar Vínculo de administración de asociados como el tipo de asociación de asociados.

**¿Por qué no puedo ver mi cliente en los informes?**

No puede ver al cliente en los informes por los siguientes motivos

1. La cuenta de usuario vinculada no tiene el tipo de acceso [Acceso basado en rol](https://docs.microsoft.com/azure/role-based-access-control/overview) en ningún recurso o suscripción de Azure del cliente.

2. La suscripción de Azure donde el usuario tiene el tipo de acceso [Acceso basado en rol](https://docs.microsoft.com/azure/role-based-access-control/overview) no tiene ningún uso.

**¿Funciona el identificador de asociado del vínculo con Azure Stack?**

Sí, puede vincular su identificador de asociado para Azure Stack.
