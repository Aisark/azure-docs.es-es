---
title: Ver y administrar clientes y recursos delegados
description: Como proveedor de servicios con la administración de recursos delegados de Azure, puede ver todos los recursos y suscripciones de clientes delegados desde la sección Mis clientes de Azure Portal.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 10/23/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: d68ac0a1b6a66482a85f7ab79840099a34c731c2
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73615534"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Ver y administrar clientes y recursos delegados

Los proveedores de servicios que usan la [administración de recursos delegados de Azure](../concepts/azure-delegated-resource-management.md) pueden usar la página **Mis clientes** de [Azure Portal](https://portal.azure.com) para ver las suscripciones y los recursos de clientes delegados. Aunque aquí nos referiremos a los proveedores de servicios y clientes, las empresas que administren varios inquilinos pueden usar el mismo proceso para consolidar su experiencia de administración.

Para acceder a la página **Mis clientes** en Azure Portal, seleccione **Todos los servicios**, busque **Mis clientes** y seleccione esta opción. También puede acceder aquí escribiendo "Mis clientes" en el cuadro de búsqueda situado cerca de la parte superior de Azure Portal.

Tenga en cuenta que la sección superior de la página **Mis clientes** solo muestra información sobre los clientes que tienen suscripciones o grupos de recursos delegados. Si trabaja con otros clientes (por ejemplo, mediante el [programa Proveedor de soluciones en la nube](https://docs.microsoft.com/partner-center/csp-overview), no verá información sobre sus clientes aquí a menos que incorpore sus recursos a la administración de recursos delegados. Más abajo en la página, en la sección **Proveedor de soluciones en la nube (versión preliminar)** se muestra información de facturación y recursos para los clientes del CSP que han [firmado el Contrato de cliente de Microsoft (MCA)](https://docs.microsoft.com/partner-center/confirm-customer-agreement) y están sujetos al plan Azure, independientemente de que se hayan incorporado a la administración de recursos delegados de Azure. Para obtener más información, consulte [Introducción a la cuenta de facturación para un contrato Microsoft Partner Agreement](https://docs.microsoft.com/azure/billing/mpa-overview).

> [!NOTE]
> Los clientes pueden ver información sobre los proveedores de servicios navegando a **Proveedores de servicios** en Azure Portal. Para obtener más información, consulte [View and manage service providers](view-manage-service-providers.md) (Ver y administrar los proveedores de servicios).

## <a name="view-and-manage-customer-details"></a>Ver y administrar los detalles del usuario

Para ver los detalles del cliente, seleccione **Clientes** en el lado izquierdo de la página **Mis clientes**.

Para cada cliente, verá el nombre del cliente, el identificador de cliente (id. de inquilino) y la oferta asociada con la involucración. En la columna **Delegaciones**, verá el número de suscripciones delegadas o el número de grupos de recursos delegados.

> [!IMPORTANT]
> Para ver una delegación, a los usuarios se les debe haber concedido el rol [lector](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) (u otro rol integrado que incluya acceso de lectura) en el proceso de incorporación.

Los filtros de la parte superior de la página le permiten ordenar y agrupar la información del cliente o filtrar por clientes, ofertas o palabras clave específicos.

Puede ver la siguiente información desde esta página:

- Para ver todas las suscripciones, ofertas y delegaciones asociadas con un cliente, seleccione el nombre del cliente.
- Para ver más detalles sobre una oferta y sus delegaciones, seleccione el nombre de la oferta.
- Para ver más detalles acerca de las asignaciones de roles en las suscripciones o grupos de recursos delegados, seleccione la entrada de la columna **Delegaciones**.

## <a name="view-delegations"></a>Ver delegaciones

Las delegaciones muestran el grupo de recursos o la suscripción que se ha delegado, junto con los usuarios y permisos que tienen acceso a ella. Para ver esta información, seleccione **Delegaciones** en el lado izquierdo de la página **Mis clientes**.

Los filtros de la parte superior de la página le permiten ordenar y agrupar la información de asignación de acceso o filtrar por clientes, ofertas o palabras clave específicos.

Los usuarios y los permisos asociados con cada delegación aparecen en la columna **Asignaciones de roles**. Puede seleccionar cada entrada para ver la lista completa de usuarios, grupos y entidades de servicio a los que se ha concedido acceso a la suscripción o al grupo de recursos. Desde allí, puede seleccionar un usuario, grupo o nombre de entidad de seguridad de servicio determinado para obtener más detalles.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Trabajar en el contexto de una suscripción delegada

Puede trabajar directamente en el contexto de una suscripción delegada en Azure Portal sin cambiar el directorio en el que está trabajando. Para ello:

1. Seleccione el icono de **directorio y suscripción** situado cerca de la parte superior de Azure Portal.
2. En el filtro **Suscripción global**, asegúrese de que solo está seleccionado el cuadro de la suscripción delegada. Puede usar el cuadro **directorios actuales y delegados** para mostrar solo las suscripciones de un directorio específico. (No use la **Cambiar directorio**, ya que cambia el directorio en el que ha iniciado sesión).

Si después accede a un servicio que admite [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md), el servicio se establecerá de forma predeterminada en el contexto de la suscripción delegada que haya seleccionado. Para cambiarlo, siga los pasos anteriores y active la casilla **Seleccionar todo** (o elija una o más suscripciones para trabajar en su lugar).

> [!NOTE]
> Si se le ha concedido acceso a uno o varios grupos de recursos, en lugar de acceso a una suscripción completa, puede seleccionar la suscripción a la que pertenece ese grupo de recursos. Después, trabajará en el contexto de esa suscripción, pero solo podrá tener acceso a los grupos de recursos designados.

También puede tener acceso a la funcionalidad relacionada con las suscripciones o grupos de recursos delegados desde los servicios que admiten experiencias de administración entre inquilinos seleccionando la suscripción o el grupo de recursos desde el servicio.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre las [experiencias de administración entre inquilinos](../concepts/cross-tenant-management-experience.md).
- Aprenda cómo los clientes pueden [ver y administrar proveedores de servicios](view-manage-service-providers.md) desde **Proveedores de servicios**, en Azure Portal.
