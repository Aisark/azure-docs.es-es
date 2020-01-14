---
title: Administración de cuentas de desarrollador mediante grupos de Azure API Management
titleSuffix: Azure API Management
description: Obtenga información acerca de cómo administrar cuentas de desarrollador mediante los grupos en Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 182f0ea93ddfb2ab64de1c15b36958537fa5431f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430724"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Creación y uso de grupos para administrar cuentas de desarrollador en Azure API Management

En API Management, los grupos se usan para administrar la visibilidad de productos para los desarrolladores. Los productos son visibles en primer lugar para los grupos y luego los desarrolladores de dichos grupos pueden ver y suscribirse a los productos asociados a los grupos. 

API Management tiene los siguientes grupos invariables del sistema:

* **Administradores** : los administradores de la suscripción de Azure son miembros de este grupo. Los administradores controlan las instancias del servicio Administración de API y crean las API, las operaciones y los productos que usan los desarrolladores.
* **Desarrolladores** : los usuarios del portal para desarrolladores autenticados se incluyen en este grupo. Los desarrolladores son los clientes que compilan aplicaciones con sus API. Los desarrolladores, después de que se les concede acceso al portal para desarrolladores, crean aplicaciones que llaman a las operaciones de una API.
* **Invitados** : a este grupo pertenecen los usuarios del portal para desarrolladores no autenticados como, por ejemplo, clientes potenciales que visitan el portal para desarrolladores de una instancia de API Management. Se les concede determinado acceso de solo lectura, como por ejemplo la posibilidad de ver API pero no llamarlas.

Además de estos grupos del sistema, los administradores pueden crear grupos personalizados o [aprovechar los grupos externos en inquilinos de Azure Active Directory asociados][leverage external groups in associated Azure Active Directory tenants]. Los grupos personalizados y externos pueden usarse junto con grupos del sistema en la concesión a los desarrolladores de visibilidad y acceso a productos de la API. Por ejemplo, podría crear un grupo personalizado para los desarrolladores afiliados a una organización asociada específica y permitirles el acceso a las API a partir de un producto que contenga solo las API relevantes. Un usuario puede ser miembro de más de un grupo.

En esta guía se muestra cómo los administradores de una instancia de API Management pueden agregar nuevos grupos y asociarlos a productos y desarrolladores.

Además de crear y administrar grupos en el portal del editor, puede crear y administrar sus grupos mediante la entidad de [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) de la API de REST de administración.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Prerequisites

Complete las tareas de este artículo: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Creación de un grupo

En esta sección se explica cómo agregar un nuevo grupo a la cuenta de API Management.

1. Seleccione la pestaña **Grupos** a la izquierda de la pantalla.
2. Haga clic en **+Agregar**.
3. Especifique un nombre único para el grupo y una descripción opcional.
4. Pulse **Crear**.

    ![Agregar un nuevo grupo](./media/api-management-howto-create-groups/groups001.png)

Una vez creado el grupo, se agrega a la lista **Grupos**. <br/>Para editar el **Nombre** o la **Descripción** del grupo, haga clic en el nombre del grupo y en **Configuración**.<br/>Para eliminar el grupo, haga clic en el nombre del grupo y después en **Eliminar**.

Ahora que se ha creado el grupo, se puede asociar a productos y desarrolladores.

## <a name="associate-group-product"> </a>Asociación de un grupo a un producto

1. Seleccione la pestaña **Productos** a la izquierda.
2. Haga clic en el nombre del producto deseado.
3. Haga clic en **Control de acceso**.
4. Haga clic en **+ Agregar grupo**.

    ![Asociación de un grupo a un producto](./media/api-management-howto-create-groups/groups002.png)
5. Seleccione el grupo que desea agregar.

    ![Asociación de un grupo a un producto](./media/api-management-howto-create-groups/groups003.png)

    Para quitar un grupo del producto, haga clic en **Eliminar**.

    ![Eliminar un grupo](./media/api-management-howto-create-groups/groups004.png)

Una vez asociado un producto a un grupo, los desarrolladores del grupo pueden ver el producto y suscribirse a él.

> [!NOTE]
> Para agregar grupos de Azure Active Directory, consulte [Autorización de las cuentas de desarrollador con Azure Active Directory en Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Asociación de grupos a desarrolladores

En esta sección se explica cómo asociar grupos con miembros.

1. Seleccione la pestaña **Grupos** a la izquierda de la pantalla.
2. Seleccione **Miembros**.

    ![Agregar un miembro](./media/api-management-howto-create-groups/groups005.png)
3. Haga clic en **+ Agregar** y seleccione un miembro.

    ![Agregar un miembro](./media/api-management-howto-create-groups/groups006.png)
4. Haga clic en **Seleccionar**.

Una vez agregada, la asociación entre el desarrollador y el grupo se puede ver en la pestaña **Usuarios** .

## <a name="next-steps"> </a>Pasos siguientes

* Una vez agregado a un grupo, un desarrollador puede ver los productos asociados al grupo y suscribirse a ellos. Para obtener más información, consulte [Creación y publicación de un producto en Azure API Management][How create and publish a product in Azure API Management].
* Además de crear y administrar grupos en el portal del editor, puede crear y administrar sus grupos mediante la entidad de [grupo](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) de la API de REST de administración.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
