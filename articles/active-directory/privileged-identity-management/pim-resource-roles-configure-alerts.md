---
title: Configurar alertas de seguridad para los roles de recursos de Azure en PIM - Azure Active Directory | Microsoft Docs
description: Aprenda a configurar alertas de seguridad para los roles de recurso de Azure en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 04/02/2018
ms.author: rolyon
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 09e04e6b61d3387cb8c50c2af4eef2cfb4bec196
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437836"
---
# <a name="configure-security-alerts-for-azure-resource-roles-in-pim"></a>Configuración de alertas de seguridad para los roles de recurso de Azure en PIM
Azure Active Directory (Azure AD) Privileged Identity Management (PIM) genera alertas cuando no hay actividades sospechosas o no seguras en su entorno. Cuando se desencadena una alerta, se muestra en la página de alertas. 

![Página de alertas](media/azure-pim-resource-rbac/RBAC-alerts-home.png)

## <a name="review-alerts"></a>Revisar alertas
Seleccione una alerta para ver un informe que enumera los usuarios o roles que desencadenaron la alerta, así como consejos de solución.

![Informe de alertas](media/azure-pim-resource-rbac/rbac-alert-info.png)

## <a name="alerts"></a>Alertas
| Alerta | Gravedad | Desencadenador | Recomendación |
| --- | --- | --- | --- |
| **Hay demasiados propietarios asignados a un recurso** |Mediano |Demasiados usuarios tienen el rol de propietario. |Revise los usuarios de la lista y vuelva a asignar algunos a roles con menos privilegios. |
| **Hay demasiados propietarios permanentes asignados a un recurso** |Mediano |Demasiados usuarios están asignados permanentemente a un rol. |Revise los usuarios de la lista y vuelva a asignar algunos para que tengan que solicitar la activación para usar el rol. |
| **Se ha creado un rol duplicado** |Mediano |Varios roles tienen los mismos criterios. |Use solo uno de estos roles. |


### <a name="severity"></a>Gravedad
* **Alta**: requiere acción inmediata debido a la infracción de una directiva. 
* **Media**: no requiere acción inmediata, pero indica una posible infracción de una directiva.
* **Baja**: no requiere acción inmediata pero sugiere un cambio de directiva preferida.

## <a name="configure-security-alert-settings"></a>Configuración de alertas de seguridad
En la página de alertas, vaya a **Configuración**.
![Configuración](media/azure-pim-resource-rbac/rbac-navigate-settings.png)

Personalice la configuración de las diferentes alertas para que encajen con su entorno y con sus objetivos de seguridad.
![Personalizar la configuración](media/azure-pim-resource-rbac/rbac-alert-settings.png)

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de alertas de seguridad para los roles de recurso de Azure en PIM](pim-resource-roles-configure-alerts.md)
