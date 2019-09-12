---
title: Creación de un grupo dinámico y comprobación del estado en Azure Active Directory | Microsoft Docs
description: Cómo crear reglas de pertenencia a grupos en Azure Portal y comprobar el estado.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 08/30/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 343acce228c38e38152fc2ea9d8fe0a59d8254d4
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193933"
---
# <a name="create-a-dynamic-group-and-check-status"></a>Creación de un grupo dinámico y comprobación de su estado

En Azure Active Directory (Azure AD), puede usar reglas para determinar la pertenencia a grupos según las propiedades de usuario o dispositivo. En este artículo se explica cómo configurar una regla para un grupo dinámico en Azure Portal.
La pertenencia dinámica se admite para grupos de seguridad o grupos de Office 365. Cuando se aplica una regla de pertenencia a grupos, se evalúan los atributos de usuario y dispositivo para ver si coinciden con la regla de pertenencia. Cuando cambian los atributos de un usuario o dispositivo, se procesan todos los cambios de pertenencia de las reglas de grupo dinámico de la organización. Los usuarios y dispositivos se agregan o quitan si cumplen las condiciones de un grupo. Los grupos de seguridad se pueden usar para dispositivos o usuarios, pero los grupos de Office 365 solo pueden ser grupos de usuarios.

## <a name="rule-builder-in-the-azure-portal"></a>Generador de reglas en Azure Portal

Azure AD proporciona un generador de reglas para crear y actualizar las reglas importantes con mayor rapidez. El generador de reglas admite la construcción de hasta cinco expresiones. Facilita la creación de reglas con unas cuantas expresiones sencillas; no obstante, no se puede usar para reproducir cada regla. En caso de que no admita la regla que quiere crear, puede usar el cuadro de texto.

Estos son algunos ejemplos de reglas o sintaxis avanzadas para las que se recomienda usar el cuadro de texto:

- Regla con más de cinco expresiones
- La regla de subordinados directos
- Valor de [precedencia de operadores](groups-dynamic-membership.md#operator-precedence)
- [Reglas con expresiones complejas](groups-dynamic-membership.md#rules-with-complex-expressions); por ejemplo, `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> Es posible que el generador de reglas no pueda mostrar algunas reglas construidas en el cuadro de texto. En este caso, podría aparecer un mensaje. El generador de reglas no cambia la sintaxis admitida, la validación ni el procesamiento de reglas de grupos dinámicos de ninguna manera.

![Adición de una regla de pertenencia a un grupo dinámico](./media/groups-update-rule/update-dynamic-group-rule.png)

Para ver ejemplos de sintaxis, propiedades admitidas, operadores y valores de una regla de pertenencia, consulte [Reglas de pertenencia dinámica a grupos de Azure Active Directory](groups-dynamic-membership.md).

## <a name="to-create-a-group-membership-rule"></a>Para crear una regla de pertenencia a grupo, siga estos pasos:

1. Inicie sesión en el [Centro de administración de Azure AD](https://aad.portal.azure.com) con una cuenta de administrador global, administrador de Intune o administrador de usuarios en el inquilino.
1. Seleccione **Grupos**.
1. Seleccione **Todos los grupos** y, luego, **Nuevo grupo**.

   ![Selección del comando para agregar nuevo grupo](./media/groups-create-rule/new-group-creation.png)

1. En la página **Grupo**, escriba un nombre y una descripción para el nuevo grupo. Seleccione un **tipo de pertenencia** para los usuarios o dispositivos y, luego, seleccione **Agregar una consulta dinámica**. El generador de reglas admite hasta cinco expresiones. Para agregar más de cinco expresiones, debe usar el cuadro de texto.

   ![Adición de una regla de pertenencia a un grupo dinámico](./media/groups-create-rule/add-dynamic-group-rule.png)

1. Para ver las propiedades de extensión personalizadas disponibles para su consulta de pertenencia, siga estos pasos:
   1. Seleccione **Obtener las propiedades de extensión personalizadas**.
   1. Escriba el identificador de aplicación y, luego, seleccione **Actualizar propiedades**.
1. Después de crear la regla, seleccione **Guardar**.
1. Seleccione **Crear** en la página **Nuevo grupo** para crear el grupo.

Si la regla que escribió no es válida, aparece una explicación de por qué no se pudo procesar la regla en una notificación de Azure en el portal. Léala con cuidado para saber cómo corregir la regla.

## <a name="turn-on-or-off-welcome-email"></a>Activación o desactivación del correo electrónico de bienvenida

Cuando se crea un nuevo grupo de Office 365, se envía una notificación de bienvenida por correo a los usuarios que se agregaron al grupo. Más tarde, si cambian los atributos de un usuario o dispositivo, se procesan los cambios de pertenencia de todas las reglas de grupo dinámico de la organización. Los usuarios que se agregan también reciben la notificación de bienvenida. Este comportamiento se puede desactivar en [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/users-and-groups/Set-UnifiedGroup?view=exchange-ps).

## <a name="check-processing-status-for-a-rule"></a>Comprobación del estado de procesamiento de una regla

Puede ver el estado de procesamiento de la pertenencia y la última fecha actualizada en la página **Información general** del grupo dinámico.
  
  ![visualización del estado del grupo dinámico](./media/groups-create-rule/group-status.png)

Los mensajes de estado siguientes se pueden mostrar para el estado de **procesamiento de la pertenencia**:

- **Evaluando**:  se ha recibido el cambio de grupo y se están evaluando las actualizaciones.
- **Procesando**: las actualizaciones se están procesando.
- **Actualización completada**: se ha completado el procesamiento y se han realizado todas las actualizaciones aplicables.
- **Error de procesamiento**:  no se pudo completar el procesamiento debido a un error al evaluar la regla de pertenencia.
- **Actualización en pausa**: el administrador han pausado las actualizaciones de la regla de pertenencia dinámica. MembershipRuleProcessingState se establece en "Paused".

Los mensajes de estado siguientes se pueden mostrar para el estado **Última actualización de la pertenencia**:

- &lt;**Fecha y hora**&gt;: la última vez que se actualizó la pertenencia.
- **En curso**: las actualizaciones están actualmente en curso.
- **Desconocido**: no se puede recuperar la hora de la última actualización. El grupo podría ser nuevo.

Si se produce un error al procesar la regla de pertenencia para un grupo específico, se muestra una alerta en la parte superior de la **página de información general** del grupo. Si no se puede procesar ninguna actualización de pertenencia dinámica pendiente para todos los grupos del inquilino durante más de 24 horas, se muestra una alerta en la parte superior de **Todos los grupos**.

![alertas de mensajes de error de procesamiento](./media/groups-create-rule/processing-error.png)

En estos artículos se proporciona información adicional sobre los grupos en Azure Active Directory.

- [Consulta de los grupos existentes](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Crear un nuevo grupo y agregar miembros](../fundamentals/active-directory-groups-create-azure-portal.md)
- [Administrar la configuración de un grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Administrar la pertenencia a grupos](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Administrar reglas dinámicas de los usuarios de un grupo](groups-dynamic-membership.md)
