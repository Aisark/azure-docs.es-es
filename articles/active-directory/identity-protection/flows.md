---
title: Experiencias de inicio de sesión con Azure AD Identity Protection | Microsoft Docs
description: Proporciona información general sobre la experiencia de usuario cuando Identity Protection ha mitigado o corregido los problemas relacionados con un usuario, o cuando una directiva exige la autenticación multifactor.
services: active-directory
keywords: azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: de5bf637-75a7-4104-b6d8-03686372a319
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 449f808e98c4e0db2972071e160f5335153a88f2
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58520139"
---
# <a name="sign-in-experiences-with-azure-ad-identity-protection"></a>Experiencias de inicio de sesión con Azure AD Identity Protection
Con Azure Active Directory Identity Protection, puede:

* exigir a los usuarios que se registren en la autenticación multifactor y
* controlar inicios de sesión conflictivos y usuarios en peligro.

La respuesta del sistema a estos problemas tiene un impacto en la experiencia de inicio de sesión del usuario, puesto que ya no se podrá iniciar sesión directamente introduciendo un nombre de usuario y una contraseña. Se requieren pasos adicionales para que los usuarios vuelvan a sus actividades con seguridad.

En este artículo se ofrece información general sobre la experiencia de inicio de sesión de usuario para todos los casos posibles.

**Multi-factor Authentication**

* Registro de la autenticación multifactor

**Inicio de sesión en peligro**

* Recuperación de inicios de sesión peligrosos
* Inicios de sesión peligrosos bloqueados
* Registro de la autenticación multifactor durante un inicio de sesión peligroso

**Usuario en peligro**

* Recuperación de cuentas en peligro
* Cuenta en peligro bloqueada

## <a name="multi-factor-authentication-registration"></a>Registro de la autenticación multifactor
La mejor experiencia de usuario en ambos casos: el flujo de recuperación de la cuenta en peligro y el flujo de inicio de sesión peligroso, es cuando el usuario puede realizar su recuperación. Si los usuarios están registrados para la autenticación multifactor, ya tienen un número de teléfono asociado a sus cuentas que pueden usar para pasar comprobaciones de seguridad. No es necesaria ninguna participación del administrador o el departamento de soporte técnico para recuperar la cuenta puesta en peligro. Por lo tanto, se recomienda encarecidamente a los usuarios que se registren en la autenticación multifactor. 

Los administradores pueden establecer una directiva que requiere que los usuarios configuren sus cuentas para una verificación de seguridad adicional. Esta directiva permite a los usuarios omitir el registro de autenticación multifactor durante un máximo de 14 días. El período de gracia de 14 días no es configurable.

**El registro de la autenticación multifactor consta de tres pasos:**

1. En el primer paso, el usuario recibe una notificación sobre la necesidad de configurar la cuenta para la autenticación multifactor. 
   
    ![Corrección](./media/flows/140.png "Corrección")
2. Para configurar la autenticación multifactor, debe indicar al sistema cómo quiere que se pongan en contacto con usted.
   
    ![Corrección](./media/flows/141.png "Corrección")
3. El sistema envía una comprobación que debe responder.
   
    ![Corrección](./media/flows/142.png "Corrección")

## <a name="risky-sign-in-recovery"></a>Recuperación de inicios de sesión peligrosos
Cuando un administrador ha configurado una directiva de riesgo de inicio de sesión, se notifica a los usuarios afectados cuando intentan iniciar sesión. 

**El flujo de inicio de sesión peligroso consta de dos pasos:** 

1. Se informa al usuario de que se ha detectado algún aspecto inusual en su inicio de sesión, como que se haya realizado desde una nueva ubicación, dispositivo o aplicación. 
   
    ![Corrección](./media/flows/120.png "Corrección")
2. Para demostrar su identidad, el usuario debe resolver un desafío de seguridad. Si el usuario está registrado para la autenticación multifactor necesita devolver un código de seguridad enviado a su número de teléfono. Puesto que solo se trata de un inicio de sesión peligroso, y no de una cuenta en peligro, el usuario no tendrá que cambiar la contraseña en este flujo. 
   
    ![Corrección](./media/flows/121.png "Corrección")

## <a name="risky-sign-in-blocked"></a>Inicios de sesión peligrosos bloqueados
Los administradores también pueden elegir establecer una directiva de seguridad de riesgo de inicio de sesión para bloquear a los usuarios al iniciar sesión en función del nivel de riesgo. Para ser desbloqueados, los usuarios finales deben ponerse en contacto con un administrador o el departamento de soporte técnico, o bien pueden intentar iniciar sesión desde una ubicación o dispositivo conocidos. La recuperación automática al resolver la autenticación multifactor no es una opción en este caso.

![Corrección](./media/flows/200.png "Corrección")

## <a name="compromised-account-recovery"></a>Recuperación de cuentas en peligro
Cuando se ha configurado una directiva de seguridad de riesgo del usuario, los usuarios que cumplen el nivel especificado en dicha directiva (y, por tanto, se supone que están en peligro) deben pasar por el flujo de recuperación del peligro antes de poder iniciar sesión. 

**El flujo de recuperación de usuarios puestos en peligro consta de tres pasos:**

1. Se informa al usuario que la seguridad de su cuenta está en peligro debido a actividad sospechosa o credenciales con fugas.
   
    ![Corrección](./media/flows/101.png "Corrección")
2. Para demostrar su identidad, el usuario debe resolver un desafío de seguridad. Si el usuario está registrado para la autenticación multifactor, puede recuperarse automáticamente cuando está en peligro. Necesitará devolver un código de seguridad enviado a su número de teléfono. 
   
   ![Corrección](./media/flows/110.png "Corrección")
3. Por último, el usuario debe cambiar su contraseña, ya que es posible que otra persona haya tenido acceso a su cuenta. 
   A continuación se muestran capturas de pantalla de esta experiencia.
   
   ![Corrección](./media/flows/111.png "Corrección")

## <a name="compromised-account-blocked"></a>Cuenta en peligro bloqueada
Para desbloquear un usuario bloqueado por una directiva de seguridad de riesgo del usuario, el usuario debe ponerse en contacto con un administrador o el departamento de soporte técnico. La recuperación automática al resolver la autenticación multifactor no es una opción en este caso.

![Corrección](./media/flows/104.png "Corrección")

## <a name="reset-password"></a>Restablecimiento de contraseña
Si se bloquea el inicio de sesión de los usuarios en peligro, un administrador puede generar una contraseña temporal para ellos, quienes tendrán que cambiar las contraseñas en el siguiente inicio de sesión.

![Corrección](./media/flows/160.png "Corrección")

## <a name="see-also"></a>Vea también
* [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) 

