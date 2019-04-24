---
title: Solución alternativa para usuarios sin permiso de propietario de Avere vFXT - Azure
description: Solución alternativa para permitir que los usuarios sin permiso de propietario de la suscripción puedan implementar Avere vFXT para Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/31/2018
ms.author: v-erkell
ms.openlocfilehash: e72e6d969649de09389ee38b94e874fad98ee08f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409216"
---
# <a name="authorize-non-owners-to-deploy-avere-vfxt"></a>Autorizar a no propietarios a implementar Avere vFXT

Estas instrucciones son una solución alternativa que permitirá a un usuario sin privilegios de propietario de suscripción crear una instancia de Avere vFXT para el sistema Azure.

(La forma recomendada de implementar el sistema Avere vFXT es hacer que un usuario con privilegios de propietario realice los pasos de creación, tal como se explica en [Prepare to create the Avere vFXT](avere-vfxt-prereqs.md) [Prepararse para crear la instancia de Avere vFXT]).  

La solución alternativa consiste en crear un rol de acceso adicional que otorgue a sus usuarios permisos suficientes para instalar el clúster. Este rol lo debe crear un propietario de suscripción y un propietario debe ser el que lo asigne a los usuarios apropiados. 

Asimismo, el propietario de una suscripción debe [aceptar los términos de uso](avere-vfxt-prereqs.md) para la imagen de Marketplace de Avere vFXT. 

> [!IMPORTANT] 
> Recuerde que estos pasos debe realizarlos un usuario que tenga privilegios de propietario en la suscripción que se usará en el clúster.

1. Copie estas líneas y guárdelas en un archivo (por ejemplo, `averecreatecluster.json`). Use el identificador de suscripción de la instrucción `AssignableScopes`.

   ```json
   {
       "AssignableScopes": ["/subscriptions/<SUBSCRIPTION_ID>"],
       "Name": "avere-create-cluster",
       "IsCustom": "true"
       "Description": "Can create Avere vFXT clusters",
       "NotActions": [],
       "Actions": [
           "Microsoft.Authorization/*/read",
           "Microsoft.Authorization/roleAssignments/*",
           "Microsoft.Authorization/roleDefinitions/*",
           "Microsoft.Compute/*/read",
           "Microsoft.Compute/availabilitySets/*",
           "Microsoft.Compute/virtualMachines/*",
           "Microsoft.Network/*/read",
           "Microsoft.Network/networkInterfaces/*",
           "Microsoft.Network/routeTables/write",
           "Microsoft.Network/routeTables/delete",
           "Microsoft.Network/routeTables/routes/delete",
           "Microsoft.Network/virtualNetworks/subnets/join/action",
           "Microsoft.Network/virtualNetworks/subnets/read",
   
           "Microsoft.Resources/subscriptions/resourceGroups/read",
           "Microsoft.Resources/subscriptions/resourceGroups/resources/read",
           "Microsoft.Storage/*/read",
           "Microsoft.Storage/storageAccounts/listKeys/action"
       ],
   }
   ```

1. Use este comando para crear el rol:

   `az role definition create --role-definition <PATH_TO_FILE>`

    Ejemplo:
    ```azurecli
    az role definition create --role-definition ./averecreatecluster.json
    ```

1. Asigne este rol al usuario que creará el clúster:

   `az role assignment create --assignee <USERNAME> --scope /subscriptions/<SUBSCRIPTION_ID> --role 'avere-create-cluster'`

Después de realizar este procedimiento, cualquier usuario que se haya asignado a este rol cuenta con los siguientes permisos para la suscripción: 

* Crear y configurar la infraestructura de red.
* Crear el controlador del clúster.
* Ejecutar los scripts de creación de clústeres desde el controlador de clúster para así poder crear el clúster en cuestión.
