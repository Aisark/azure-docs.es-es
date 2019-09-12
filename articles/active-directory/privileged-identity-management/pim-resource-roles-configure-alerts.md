---
title: 'Configuración de alertas de seguridad para roles de recursos de Azure en PIM: Azure Active Directory | Microsoft Docs'
description: Aprenda a configurar alertas de seguridad para los roles de recurso de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 177f61392c3e441c891ba1b531301b3dae8c0db2
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804239"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configuración de alertas de seguridad para los roles de recurso de Azure en PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) genera alertas cuando existen actividades sospechosas o no seguras en su entorno. Cuando se desencadena una alerta, se muestra en la página de alertas. 

![Recursos de Azure: página de alertas que muestra la alerta, el nivel de riesgo y el recuento](media/pim-resource-roles-configure-alerts/rbac-alerts-page.png)

## <a name="review-alerts"></a>Revisar alertas
Seleccione una alerta para ver un informe que enumera los usuarios o roles que desencadenaron la alerta, así como consejos de solución.

![Informe de alertas que muestra el último análisis de tiempo, la descripción, los pasos de mitigación, el tipo, la gravedad, el impacto de seguridad y cómo impedir que vuelva a pasar](media/pim-resource-roles-configure-alerts/rbac-alert-info.png)

## <a name="alerts"></a>Alertas
| Alerta | severity | Desencadenador | Recomendación |
| --- | --- | --- | --- |
| **Hay demasiados propietarios asignados a un recurso** |Mediano |Demasiados usuarios tienen el rol de propietario. |Revise los usuarios de la lista y vuelva a asignar algunos a roles con menos privilegios. |
| **Hay demasiados propietarios permanentes asignados a un recurso** |Mediano |Demasiados usuarios están asignados permanentemente a un rol. |Revise los usuarios de la lista y vuelva a asignar algunos para que tengan que solicitar la activación para usar el rol. |
| **Se ha creado un rol duplicado** |Mediano |Varios roles tienen los mismos criterios. |Use solo uno de estos roles. |


### <a name="severity"></a>severity
* **Alta**: requiere acción inmediata debido a la infracción de una directiva. 
* **Media**: no requiere acción inmediata, pero indica una posible infracción de una directiva.
* **Baja**: no requiere acción inmediata pero sugiere un cambio de directiva preferida.

## <a name="configure-security-alert-settings"></a>Configuración de alertas de seguridad
En la página de alertas, vaya a **Configuración**.

![Página de alertas con la opción Configuración resaltada](media/pim-resource-roles-configure-alerts/rbac-navigate-settings.png)

Personalice la configuración de las diferentes alertas para que encajen con su entorno y con sus objetivos de seguridad.

![Página de configuración de una alerta para habilitar y configurar los valores](media/pim-resource-roles-configure-alerts/rbac-alert-settings.png)

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de roles de recurso de Azure en PIM](pim-resource-roles-configure-role-settings.md)
