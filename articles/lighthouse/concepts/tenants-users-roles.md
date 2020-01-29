---
title: Inquilinos, roles y usuarios en escenarios de Azure Lighthouse
description: Comprenda los conceptos de los inquilinos, los usuarios y los roles de Azure Active Directory, así como la forma en que se pueden usar en escenarios de Azure Lighthouse.
ms.date: 01/16/2020
ms.topic: conceptual
ms.openlocfilehash: 344e104201a83b3589dae6dbd3b02e49e4575e00
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76156342"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Inquilinos, roles y usuarios en escenarios de Azure Lighthouse

Antes de incorporar clientes para la [administración de recursos delegados de Azure](azure-delegated-resource-management.md), es importante comprender cómo funcionan los inquilinos, los usuarios y los roles de Azure Active Directory (Azure AD), así como la forma en que se pueden usar en escenarios de Azure Lighthouse.

Un *inquilino* es una instancia dedicada y de confianza de Azure AD. Normalmente, cada inquilino representa una organización individual. La administración de recursos delegados de Azure permite la proyección lógica de recursos de un inquilino a otro. Esto permite a los usuarios del inquilino de administración (por ejemplo, uno perteneciente a un proveedor de servicios) acceder a los recursos delegados en el inquilino de un cliente, o bien permite que las [empresas con varios inquilinos centralicen sus operaciones de administración](enterprise.md).

Para lograr esta proyección lógica, una suscripción (o uno o varios grupos de recursos dentro de una suscripción) en el inquilino del cliente debe estar *incorporada* para la administración de recursos delegados de Azure. Este proceso de incorporación puede realizarse [a través de plantillas de Azure Resource Manager](../how-to/onboard-customer.md) o [mediante la publicación de una oferta pública o privada en Azure Marketplace](../how-to/publish-managed-services-offers.md).

Sea cual sea el método de incorporación que elija, tendrá que definir las *autorizaciones*. Cada autorización especifica una cuenta de usuario en el inquilino de administración que tendrá acceso a los recursos delegados y a un rol integrado que establece los permisos que cada uno de estos usuarios tendrá en relación con estos recursos.

## <a name="role-support-for-azure-delegated-resource-management"></a>Compatibilidad con los roles para la administración de recursos delegados de Azure

Al definir una autorización, cada cuenta de usuario debe tener asignado uno de los [roles integrados para control de acceso basado en roles (RBAC)](../../role-based-access-control/built-in-roles.md). No se admiten los roles personalizados ni [los roles de administrador de suscripciones clásicas](../../role-based-access-control/classic-administrators.md).

Todos los [roles integrados](../../role-based-access-control/built-in-roles.md) son compatibles actualmente con la administración de recursos delegados de Azure, con las siguientes excepciones:

- No se admite el rol de [Propietario](../../role-based-access-control/built-in-roles.md#owner).
- No se admiten los roles integrados con el permiso [DataActions](../../role-based-access-control/role-definitions.md#dataactions).
- Se admite el rol integrado [administrador de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator), pero solo con el objetivo limitado de [asignar roles a una identidad administrada en el inquilino del cliente](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant). No se aplicará ningún otro permiso que normalmente conceda este rol. Si define un usuario con este rol, también debe especificar los roles integrados que este usuario puede asignar a las identidades administradas.

> [!NOTE]
> Una vez que se agrega un rol integrado aplicable a Azure, se puede asignar al [incorporar clientes mediante las plantillas de Azure Resource Manager](../how-to/onboard-customer.md). Puede haber un retraso antes de que el rol recién agregado esté disponible en Cloud Partner Portal al [publicar una oferta de servicio administrado](../how-to/publish-managed-services-offers.md).

## <a name="best-practices-for-defining-users-and-roles"></a>Prácticas recomendadas para definir usuarios y roles

Para crear las autorizaciones, se recomiendan las siguientes prácticas recomendadas:

- En la mayoría de los casos, querrá asignar permisos a una entidad de servicio o un grupo de usuarios de Azure AD, en lugar de a una serie de cuentas de usuario individuales. Esto le permite agregar o quitar el acceso de usuarios individuales sin tener que actualizar y volver a publicar el plan cuando cambien los requisitos de acceso.
- Asegúrese de seguir el principio de privilegios mínimos para que los usuarios solo tengan los permisos necesarios para completar su trabajo, lo que ayuda a reducir la posibilidad de errores involuntarios. Para obtener más información, consulte [Recommended security practices](../concepts/recommended-security-practices.md) (Prácticas de seguridad recomendadas).
- Incluya un usuario con el [rol de eliminación de asignaciones de registro de los servicios administrados](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) para que pueda [quitar el acceso a la delegación](../how-to/onboard-customer.md#remove-access-to-a-delegation) posteriormente si es necesario. Si este rol no está asignado, solo un usuario puede quitar los recursos delegados del inquilino del cliente.
- Asegúrese de que todos los usuarios que necesiten [ver la página Mis clientes en Azure Portal](../how-to/view-manage-customers.md) tengan el rol de [Lector](../../role-based-access-control/built-in-roles.md#reader) (u otro rol integrado que incluya acceso de lectura).

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre las [prácticas de seguridad recomendadas para la de administración de recursos delegados de Azure](recommended-security-practices.md).
- Incorpore los clientes a la administración de recursos delegados de Azure, ya sea [mediante plantillas de Azure Resource Manager](../how-to/onboard-customer.md) o [publicando una oferta de servicios administrados privada o pública en Azure Marketplace](../how-to/publish-managed-services-offers.md).
