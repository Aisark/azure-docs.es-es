---
title: Eliminación de la nube privada de una solución de Azure VMware de CloudSimple
description: Se describe cómo eliminar una nube privada de CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6bc3e7030c500ea2d6072a1cce0f0b3d9fc62801
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972803"
---
# <a name="delete-a-cloudsimple-private-cloud"></a>Eliminación de una nube privada de CloudSimple

CloudSimple ofrece la flexibilidad de eliminar una nube privada.  Una nube privada se compone de uno o varios clústeres de vSphere. Cada clúster puede tener de 3 a 16 nodos. Al eliminar una nube privada, se eliminarán todos los clústeres.

## <a name="before-you-begin"></a>Antes de empezar

Con la eliminación de una nube privada se elimina toda la nube privada.  Todos los componentes de la nube privada se eliminan.  Si quiere conservar los datos, asegúrese de que ha realizado una copia de seguridad de ellos en el almacenamiento local o en Azure Storage.

Los componentes de una nube privada incluyen:

* Nodos de CloudSimple
* Máquinas virtuales
* VLAN o subredes
* Todos los datos de usuario almacenados en la nube privada
* Todos los datos adjuntos de reglas de firewall a una VLAN o subred

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-a-private-cloud"></a>Eliminación de una nube privada

1. [Acceda al portal de CloudSimple](access-cloudsimple-portal.md).

2. Abra la página **Resources** (Recursos).

3. Haga clic en la nube privada que quiera eliminar.

4. En la página de resumen, haga clic en **Delete** (Eliminar).

    ![Eliminación de una nube privada](media/delete-private-cloud.png)

5. En la página de confirmación, escriba el nombre de la nube privada y haga clic en **Delete** (Eliminar). 

    ![Eliminación de la nube privada: confirmación](media/delete-private-cloud-confirm.png)

La nube privada está marcada para su eliminación.  El proceso de eliminación se inicia al cabo de tres horas y elimina la nube privada.

> [!CAUTION]
> Los nodos deben eliminarse después de la eliminación de la nube privada.  La medición de los nodos continuará hasta que se eliminen los nodos de la suscripción.

## <a name="next-steps"></a>Pasos siguientes

* [Eliminación de nodos](delete-nodes.md)
