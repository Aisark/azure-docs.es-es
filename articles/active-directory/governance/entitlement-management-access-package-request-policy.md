---
title: 'Cambio de la configuración de la solicitud y aprobación para un paquete de acceso en la administración de derechos de Azure AD (versión preliminar): Azure Active Directory'
description: Aprenda a modificar la configuración de la solicitud y aprobación de un paquete de acceso en la administración de derechos de Azure Active Directory (versión preliminar).
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b060a17d9e7f795b6aad7df0e3e6349bbbd81b3
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/16/2019
ms.locfileid: "72392023"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Cambio de la configuración de la solicitud y aprobación para un paquete de acceso en la administración de derechos de Azure AD (versión preliminar)

> [!IMPORTANT]
> La administración de derechos de Azure Active Directory (Azure AD) está actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Como administrador de paquetes de acceso, puede cambiar los usuarios que pueden solicitar un paquete de acceso en cualquier momento si edita la directiva o agrega una nueva. También puede cambiar la configuración de aprobación.

En este artículo, se explica cómo se puede modificar la configuración de la solicitud y aprobación de un paquete de acceso existente.

## <a name="choose-between-one-or-multiple-polices"></a>Elección entre una o varias directivas

Para especificar quién puede solicitar un paquete de acceso, debe usar una directiva. Cuando se crea un paquete de acceso, se especifica la configuración de la solicitud y aprobación que crea una directiva. La mayoría de los paquetes de acceso tendrán una sola directiva, pero un único paquete de acceso puede tener varias. Se crearían varias directivas para un único paquete de acceso si deseara permitir que se concedan asignaciones a los distintos conjuntos de usuarios con una configuración de solicitudes y aprobación diferente. Por ejemplo, no se puede usar una sola directiva para asignar usuarios internos y externos al mismo paquete de acceso. Pero puede crear dos directivas en el mismo paquete de acceso: una para los usuarios internos y otra para los usuarios externos. Si hay varias directivas que se aplican a un usuario, se le pedirá en el momento de su solicitud que seleccione la directiva que le gustaría que se le asignara. En el diagrama siguiente se muestra un paquete de acceso con dos directivas.

![Varias directivas en un paquete de acceso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Escenario | Número de directivas |
| --- | --- |
| Quiero que todos los usuarios de mi directorio tengan la misma configuración de solicitudes y aprobación para un paquete de acceso | Uno |
| Quiero que todos los usuarios de ciertas organizaciones conectadas puedan solicitar un paquete de acceso | Uno |
| Deseo permitir que los usuarios de mi directorio y también los de fuera de mi directorio soliciten un paquete de acceso | Múltiple |
| Deseo especificar una configuración de aprobación diferente para algunos usuarios | Múltiple |
| Quiero que algunos usuarios accedan a las asignaciones de paquetes que van a expirar mientras que otros usuarios puedan extender su acceso | Múltiple |

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Apertura de una directiva existente de configuración de solicitudes y aprobación

Para cambiar la configuración de solicitudes y aprobación de un paquete de acceso, debe abrir la directiva correspondiente. Siga estos pasos para abrir la configuración de solicitudes y aprobación de un paquete de acceso.

**Rol necesario:** Administrador global, administrador de usuarios, propietario del catálogo o administrador de paquetes de acceso.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. Haga clic en **Directivas** y luego en la directiva que quiere editar.

    Se abre el panel Detalles de directiva en la parte inferior de la página.

    ![Paquete de acceso - Panel Detalles de directiva](./media/entitlement-management-shared/policy-details.png)

1. Haga clic en **Editar** para editar la directiva.

    ![Paquete de acceso - Editar directiva](./media/entitlement-management-shared/policy-edit.png)

1. Haga clic en la pestaña **Solicitudes** para abrir la configuración de solicitudes y aprobación.

1. Realice los pasos en una de las siguientes secciones de la solicitud.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Incorporación de una nueva directiva de configuración de solicitudes y aprobación

Si tiene un conjunto de usuarios que deben tener una configuración de solicitudes y aprobación diferente, probablemente tendrá que crear una directiva. Siga estos pasos para empezar a agregar una nueva directiva a un paquete de acceso existente.

**Rol necesario:** Administrador global, administrador de usuarios, propietario del catálogo o administrador de paquetes de acceso.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. Haga clic en **Directivas**  y luego en **Agregar directiva**.

1. Escriba un nombre y una descripción para la directiva.

    ![Crear directiva con nombre y descripción](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Haga clic en **Siguiente** para abrir la pestaña **Solicitudes**.

1. Realice los pasos en una de las siguientes secciones de la solicitud.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Si va a editar una directiva, haga clic en **Actualizar**. Si va a agregar una directiva nueva, haga clic en **Crear**.

## <a name="next-steps"></a>Pasos siguientes

- [Cambiar la configuración del ciclo de vida de un paquete de acceso](entitlement-management-access-package-lifecycle-policy.md)
- [Ver las solicitudes de un paquete de acceso](entitlement-management-access-package-requests.md)