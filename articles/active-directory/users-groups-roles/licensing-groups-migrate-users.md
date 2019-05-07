---
title: 'Agregar usuarios individualmente con licencia para las licencias basadas en grupo: Azure Active Directory | Microsoft Docs'
description: Migración de licencias de usuarios individuales a licencias basadas en grupo con Azure Active Directory
services: active-directory
keywords: Licencias de Azure AD
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: sumitp
ms.custom: seohack1;it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333f0ae0153073b57740446ecf47e36a1f9ce590
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65192450"
---
# <a name="how-to-migrate-users-with-individual-licenses-to-groups-for-licensing"></a>Cómo migrar usuarios con licencias individuales a grupos de licencias

Puede que actualmente haya licencias implementadas para usuarios de organizaciones a través de una "asignación directa"; es decir, mediante el uso de scripts de PowerShell u otras herramientas diseñadas para asignar licencias de usuarios individuales. Antes de comenzar con licencias basadas en grupo para administrar las licencias de su organización, puede usar este plan de migración para reemplazar perfectamente las soluciones existentes con las licencias basadas en grupo.

Lo más importante es tener en cuenta que hay que evitar una situación tal en que la migración a licencias basadas en grupos conlleve que los usuarios pierdan temporalmente las licencias que actualmente tienen asignadas. Se debe evitar cualquier proceso que pueda dar lugar a la eliminación de licencias a fin de evitar el riesgo de que los usuarios pierdan el acceso a los servicios y a sus datos.

## <a name="recommended-migration-process"></a>Proceso de migración recomendado

1. Cuenta actualmente con un sistema de automatización (por ejemplo, PowerShell) que administra la asignación y la retirada de licencias de los usuarios. Deje que se ejecute de la forma habitual.

2. Cree un grupo de licencias nuevo (o decida qué grupos utilizar de entre los existentes) y asegúrese de que todos los usuarios necesarios se agregan como miembros.

3. Asigne las licencias necesarias a esos grupos; el objetivo debe consistir en reflejar el mismo estado de licencia que el sistema de automatización (por ejemplo, PowerShell) aplica a dichos usuarios.

4. Verifique que dichas licencias se hayan aplicado a todos los usuarios de esos grupos. Para ello, compruebe el estado de procesamiento de cada grupo y los registros de auditoría.

   - Puede realizar una revisión rápida de cada usuario observando los detalles de sus licencias. Observará que tienen las mismas licencias asignadas "directamente" o "heredadas" de los grupos.

   - Puede ejecutar un script de PowerShell para [verificar cómo se asignan las licencias a los usuarios](licensing-group-advanced.md#use-powershell-to-see-who-has-inherited-and-direct-licenses).

   - Cuando se asigna la misma licencia de producto al usuario directamente y a través de un grupo, el usuario solo puede consumir una licencia. Por lo tanto, no se necesitan licencias adicionales para realizar la migración.

5. Compruebe si en algún grupo de usuarios aparece el estado de error, a fin de verificar que no se produzcan errores en las asignaciones de licencias. Para más información, vea [Identificación y resolución de problemas de licencias de un grupo](licensing-groups-resolve-problems.md).

6. Considere la posibilidad de quitar las asignaciones directas originales; puede que desee hacerlo gradualmente en "ondas" para supervisar primero el resultado en un subconjunto de usuarios.

   Puede dejar las asignaciones directas originales de los usuarios, pero, en ese caso, cuando el usuario deja los grupos con licencia a los que pertenecen, conserva su licencia original, y posiblemente no es esto lo que desea.

## <a name="an-example"></a>Un ejemplo

Una organización tiene 1.000 usuarios. Todos los usuarios necesitan la licencia de Enterprise Mobility + Security (EMS). 200 usuarios trabajan en el departamento financiero y necesitan licencias para Office 365 Enterprise E3. En la actualidad, la organización tiene un script de PowerShell que se ejecuta a nivel local mediante la adición y eliminación de licencias de usuarios a medida que se incorporan y se van. Pero la organización quiere reemplazar el script con licencias basadas en grupos, de forma que Azure AD administre las licencias automáticamente.

El proceso de migración podría ser similar al siguiente:

1. En Azure Portal, asigne la licencia de EMS al grupo **Todos los usuarios** de Azure AD. Asigne la licencia de E3 al grupo **Departamento financiero** que contiene todos los usuarios necesarios.

2. Confirme en cada grupo que se haya completado la asignación de licencia a todos los usuarios. Vaya a la hoja de cada grupo, seleccione **Licencias** y compruebe el estado de procesamiento en la parte superior de la hoja **Licencias**.

   - Busque "Latest license changes have been applied to all users" (Los últimos cambios de licencia se han aplicado a todos los usuarios) para confirmar que el procesamiento se ha completado.

   - Busque si hay alguna notificación en la parte superior sobre algún usuario cuya licencia no se haya podido asignar correctamente. ¿Se han agotado las licencias para algunos usuarios? ¿Algunos usuarios tienen SKU de licencias conflictivas que les impidan heredar las licencias de grupo?

3. Realice una revisión rápida de algunos usuarios para verificar que tengan aplicadas tanto licencias directas como de grupo. Vaya a la hoja de un usuario, seleccione **Licencias** y examine el estado de las licencias.

   - Este es el estado de usuario esperado durante la migración:

      ![el estado de usuario esperado durante la migración](./media/licensing-groups-migrate-users/expected-user-state.png)

   Esto confirma que el usuario tiene licencias directas y heredadas. Se observa que tiene asignadas las licencias para **EMS** y **E3**.

   - Seleccione cada licencia para ver la información sobre los servicios habilitados. Se puede usar para comprobar si las licencias directas y de grupo habilitan exactamente los mismos planes de servicio para el usuario.

      ![comprobar los planes de servicio para el usuario](./media/licensing-groups-migrate-users/check-service-plans.png)

4. Después de confirmar que las licencias directas y de grupo son equivalentes, puede empezar a quitar a los usuarios las licencias directas. Para probarlo, quítelos para usuarios individuales en el portal y luego ejecute los scripts de automatización para quitarlos en masa. Este es un ejemplo del mismo usuario con las licencias directas quitadas a través del portal. Tenga en cuenta que el estado de licencia no varía, pero aún no se ven las asignaciones directas.

   ![Confirme que se han quitado licencias directas](./media/licensing-groups-migrate-users/direct-licenses-removed.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre otros escenarios de administración de licencias a través de grupos, vea

* [¿En qué consisten las licencias basadas en grupos de Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md)
* [Asignación de licencias a un grupo en Azure Active Directory](licensing-groups-assign.md)
* [Identificación y resolución de problemas de licencias de un grupo en Azure Active Directory](licensing-groups-resolve-problems.md)
* [Cómo migrar usuarios entre diferentes licencias de productos con licencias basadas en grupos de Azure Active Directory](licensing-groups-change-licenses.md)
* [Azure Active Directory group-based licensing additional scenarios](licensing-group-advanced.md) (Escenarios adicionales de licencias basadas en grupos de Azure Active Directory)
* [Ejemplos de PowerShell para licencias basadas en grupos de Azure AD](licensing-ps-examples.md)
