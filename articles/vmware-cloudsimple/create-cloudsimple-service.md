---
title: 'Solución de VMware en Azure de CloudSimple: creación de un servicio de CloudSimple'
description: Describe cómo crear el servicio CloudSimple en Azure Portal.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f589adf5cbcd04b9642dfe1362fd13d5be1f9aa
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640634"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Creación de un servicio de la solución de VMware en Azure de CloudSimple

Para empezar a trabajar con la solución de VMware en Azure por CloudSimple, cree este servicio en Azure Portal.

> [!IMPORTANT]
> Antes de crear el servicio CloudSimple, debe registrar el proveedor de recursos Microsoft.VMwareCloudSimple en la suscripción de Azure. Siga los pasos indicados en [Registrar el proveedor de recursos Microsoft.VMwareCloudSimple en su suscripción de Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en el [Azure Portal](http://portal.azure.com).

## <a name="create-the-service"></a>Creación del servicio

1. Seleccione **Todos los servicios**.
2. Busque **Servicios de CloudSimple**.
    ![Búsqueda del servicio CloudSimple](media/create-cloudsimple-service-search.png)
3. Seleccione **Servicios CloudSimple**.
4. Haga clic en **Agregar** para crear un servicio.
    ![Adición del servicio CloudSimple](media/create-cloudsimple-service-add.png)
5. Seleccione la suscripción en la que quiera crear el servicio CloudSimple.
6. Seleccione el grupo de recursos del servicio. Para agregar un nuevo grupo de recursos, haga clic en **Crear nuevo**.
7. Escriba un nombre para identificar el servicio.
8. Escriba el CIDR de la puerta de enlace de servicio. Especifique una subred /28 que no se superponga con ninguna de sus subredes locales, las subredes de Azure ni las subredes planeadas de CloudSimple. No puede cambiar el CIDR después de crear el servicio.

    ![Creación del servicio CloudSimple](media/create-cloudsimple-service.png)
9. Haga clic en **OK**.

El servicio se crea y se agrega a la lista de servicios.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre cómo [crear una nube privada](create-private-cloud.md)
* Obtenga más información sobre cómo [configurar un entorno de nube privada](quickstart-create-private-cloud.md)
