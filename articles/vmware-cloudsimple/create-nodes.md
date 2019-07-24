---
title: 'Aprovisionamiento de nodos de la solución de VMware de CloudSimple: Azure'
description: Obtenga información sobre cómo agregar nodos a VMWare con la implementación de CloudSimple.
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165251"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Aprovisionamiento de nodos de la solución de VMware de CloudSimple: Azure

Aprovisione nodos en Azure Portal. Después puede configurar la capacidad de pago por uso de su entorno de nube privada de CloudSimple.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Adición de un nodo aprovisionado a la nube privada de CloudSimple

1. Seleccione **Todos los servicios**.
2. Busque **Nodos CloudSimple**.

   ![Búsqueda de los nodos CloudSimple](media/create-cloudsimple-node-search.png)

3. Seleccione **Nodos CloudSimple**.
4. Haga clic en **Agregar** para crear los nodos.

    ![Adición de los nodos CloudSimple](media/create-cloudsimple-node-add.png)

5. Seleccione la suscripción en la que quiera aprovisionar los nodos CloudSimple.
6. Seleccione el grupo de recursos de los nodos. Para agregar un nuevo grupo de recursos, haga clic en **Crear nuevo**.
7. Escriba el prefijo para identificar los nodos.
8. Seleccione la ubicación de los recursos de los nodos.
9. Seleccione la ubicación dedicada en la que hospedar los recursos de los nodos.
10. Seleccione el tipo de nodo. Puede elegir entre las [opciones CS28 o CS36](cloudsimple-node.md). Esta última opción incluye la capacidad de proceso y memoria máxima.
11. Seleccione el número de nodos que quiere aprovisionar.
12. Seleccione **Revisar + crear**.
13. Revise la configuración. Para modificar la configuración, haga clic en **Anterior**.
14. Seleccione **Crear**.

## <a name="next-steps"></a>Pasos siguientes

* [Create Private Cloud](https://docs.azure.cloudsimple.com/create-private-cloud/) (Creación de una nube privada)
