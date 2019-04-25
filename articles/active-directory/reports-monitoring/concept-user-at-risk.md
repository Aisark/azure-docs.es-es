---
title: Informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory | Microsoft Docs
description: Aprenda sobre el informe de seguridad de usuarios marcados en riesgo en el portal de Azure Active Directory
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 463f5c2d03cd96089342aa9b22ef85ebc05aa909
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438137"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Informe de usuarios marcados en riesgo en Azure Portal

Azure Active Directory (Azure AD) detecta acciones sospechosas relacionadas con las cuentas de usuario. Para cada acción detectada, se crea un registro denominado [evento de riesgo](concept-risk-events.md).

Puede acceder a los informes de seguridad desde [Azure Portal](https://portal.azure.com); para ello, seleccione la hoja **Azure Active Directory** y, luego, vaya a la sección **Seguridad**. 

Los eventos de riesgo detectados se utilizan para calcular:

- **Inicios de sesión peligrosos**: un inicio de sesión peligroso es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario. 

- **Usuarios marcados en riesgo**: un usuario en peligro es un indicador de una cuenta de usuario que puede haber estado en peligro. 

Para aprender a configurar las directivas que desencadenan estos eventos de riesgo, consulte [Procedimiento de configuración de la directiva de riesgo del usuario](../identity-protection/howto-user-risk-policy.md). 

![Inicios de sesión no seguros](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>¿Qué licencia de Azure AD se necesita para acceder a los usuarios en el informe de riesgo?  

Todas las ediciones de Azure Active Directory le proporcionan estos informes sobre usuarios marcados en riesgo. Sin embargo, el nivel de granularidad del informe varía según la edición: 

- En las **ediciones gratuita y básica de Azure Active Directory**, puede obtener una lista de los usuarios marcados en riesgo. 

- Además, la edición **Azure Active Directory Premium 1** le permite examinar algunos de los eventos de riesgo subyacentes que se han detectado en cada informe. 

- La edición **Azure Active Directory Premium 2** le proporciona la información más detallada acerca de todos los eventos de riesgo subyacentes y también le permite configurar directivas de seguridad que responden automáticamente a los niveles de riesgo configurados.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Informe de usuarios en riesgo de las ediciones gratuita y básica de Azure AD

El informe de usuarios marcados en riesgo de las ediciones gratuita y básica de Azure AD proporciona una lista de cuentas de usuario que podrían estar en peligro. 

![Inicios de sesión no seguros](./media/concept-user-at-risk/03.png)

Al seleccionar un usuario se proporciona la información de inicio de sesión. En el caso de usuarios que están en riesgo, puede revisar el historial de inicios de sesión del usuario y restablecer la contraseña, en caso de que sea necesario.

Este cuadro de diálogo proporciona una opción para:

- Descargar el informe
- Búsqueda de usuarios

    ![Inicios de sesión no seguros](./media/concept-user-at-risk/16.png)

Para obtener información más detallada, necesita una licencia prémium.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Informe de usuarios en riesgo de las ediciones de Azure AD Premium

El informe de usuarios marcados en riesgo de las ediciones de Azure AD Premium proporciona lo siguiente:

- Una lista de cuentas de usuario que podrían estar en peligro 

- Información agregada sobre los [tipos de eventos de riesgo](concept-risk-events.md) que se han detectado

- Una opción para descargar el informe

- Una opción para configurar una [directiva de corrección de riesgos de usuario](../identity-protection/howto-user-risk-policy.md)  

![Inicios de sesión no seguros](./media/concept-user-at-risk/71.png)

Cuando selecciona un usuario, obtiene una vista detallada del informe para este usuario que le permite:

- Abrir la vista de todos los inicios de sesión

- Restablezca la contraseña del usuario.

- Descartar todos los eventos

- Investigar los eventos de riesgo notificados para el usuario. 

![Inicios de sesión no seguros](./media/concept-user-at-risk/324.png)

Para investigar un evento de riesgo, seleccione uno de la lista para abrir la hoja **Detalles** de este evento de riesgo. En la hoja **Detalles**, tiene la opción de cerrar manualmente un evento de riesgo o de volver a activar un evento de riesgo cerrado manualmente. 

![Inicios de sesión no seguros](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Pasos siguientes

- [Procedimiento de configuración de la directiva de riesgo del usuario](../identity-protection/howto-user-risk-policy.md)
- [Procedimiento de configuración de la directiva de corrección de riesgo del usuario](../identity-protection/howto-user-risk-policy.md)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

