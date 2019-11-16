---
title: Actualización de una asignación existente desde el portal
description: Obtenga información acerca del mecanismo para actualizar una asignación existente desde el portal en Azure Blueprints.
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: f48f8cfb33a05e2bf8dcbe097d3a9eb3a5ebb9db
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960353"
---
# <a name="how-to-update-an-existing-blueprint-assignment"></a>Procedimiento para actualizar una asignación de plano técnico

Cuando se asigna un plano técnico, se puede actualizar la asignación. Hay varias razones para actualizar una asignación, entre las que se incluyen:

- Agregar o quitar un [bloqueo de recursos](../concepts/resource-locking.md)
- Cambiar el valor de [parámetros dinámicos](../concepts/parameters.md#dynamic-parameters)
- Actualizar la asignación por una versión **publicada** más reciente del plano técnico

## <a name="updating-assignments"></a>Actualización de asignaciones

1. Seleccione **Todos los servicios** en el panel izquierdo. Busque y seleccione **Planos técnicos**.

1. Seleccione **Planos técnicos asignados** en la página de la izquierda.

1. En la lista de planos técnicos, haga clic con el botón izquierdo en la asignación del plano técnico. Después, haga clic en el botón **Actualizar asignación** o haga clic con el botón derecho en la asignación del plano técnico y seleccione **Actualizar asignación**.

   ![Actualización de una asignación de plano técnico existente](../media/update-existing-assignments/update-assignment.png)

1. La página **Asignar plano técnico** se cargará con todos los valores rellenados de la asignación original. Puede cambiar la **versión de la definición de plano técnico**, el estado de **Asignación de bloqueo** y cualquiera de los parámetros dinámicos que existan en la definición del plano técnico. Haga clic en **Asignar** cuando haya terminado de realizar los cambios.

1. En la página de detalles de la asignación actualizada, consulte el nuevo estado. En este ejemplo, hemos agregado el **bloqueo** a la asignación.

   ![Se ha actualizado una asignación de plano técnico existente: modo de bloqueo cambiado](../media/update-existing-assignments/updated-assignment.png)

1. Explore los detalles acerca de otras **operaciones de asignación** con la lista desplegable. La tabla de **recursos administrados** se actualiza mediante la operación de asignación seleccionada.

   ![Operaciones de asignación de una asignación de plano técnico](../media/update-existing-assignments/assignment-operations.png)

## <a name="rules-for-updating-assignments"></a>Reglas de la actualización de asignaciones

La implementación de las asignaciones actualizadas sigue unas reglas importantes. Estas reglas determinan lo que sucede en los recursos ya implementados. El cambio solicitado y el tipo de recurso del artefacto que se va a implementar o actualizar determinan las acciones que se realizan.

- Asignaciones de roles
  - Si cambia el rol o el encargado de rol (usuario, grupo o aplicación), se crea una nueva asignación de roles. Las asignaciones de roles implementadas previamente se dejan en su lugar.
- Asignaciones de directiva
  - Si cambian los parámetros de la asignación de directiva, se actualiza la asignación existente.
  - Si la definición de la asignación de directiva se cambia, se crea una asignación de directiva nueva. Las asignaciones de directivas implementadas previamente se dejan en su lugar.
  - Si se quita el artefacto de asignación de directiva del plano técnico, las asignaciones de directivas implementadas anteriormente se dejan en su lugar.
- Plantillas del Administrador de recursos de Azure
  - La plantilla se procesa mediante Resource Manager como **PUT**. Como cada tipo de recurso trata esta acción de forma distinta, revise la documentación de cada recurso incluido para determinar el efecto de esta acción cuando la ejecuta Blueprints.

## <a name="possible-errors-on-updating-assignments"></a>Posibles errores en la actualización de asignaciones

Al actualizar las asignaciones, es posible realizar cambios que se interrumpan cuando se ejecutan. Un ejemplo se da al cambiar la ubicación de un grupo de recursos una vez que se haya implementado. Puede realizarse cualquier cambio que [Azure Resource Manager](../../../azure-resource-manager/resource-group-overview.md) admita, pero, si este produjera un error con Azure Resource Manager, también lo generaría en la asignación.

No hay ningún límite en el número de veces que una asignación se puede actualizar. Si se produce un error, determínelo y realice otra actualización en la asignación.  Escenarios de error de ejemplo:

- Un parámetro incorrecto
- Un objeto ya existente
- Un cambio no admitido por Azure Resource Manager

## <a name="next-steps"></a>Pasos siguientes

- Información acerca del [ciclo de vida del plano técnico](../concepts/lifecycle.md).
- Descubra cómo utilizar [parámetros estáticos y dinámicos](../concepts/parameters.md).
- Aprenda a personalizar el [orden de secuenciación de planos técnicos](../concepts/sequencing-order.md).
- Averigüe cómo usar el [bloqueo de recursos de planos técnicos](../concepts/resource-locking.md).
- Puede consultar la información de [solución de problemas generales](../troubleshoot/general.md) para resolver los problemas durante la asignación de un plano técnico.