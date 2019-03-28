---
title: Cómo mejorar la precisión de la detección en Azure Active Directory Identity Protection (actualizado) | Microsoft Docs
description: Cómo mejorar la precisión de la detección en Azure Active Directory Identity Protection (actualizado).
services: active-directory
keywords: azure active directory identity protection, detección de aplicaciones en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: e7434eeb-4e98-4b6b-a895-b5598a6cccf1
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2019
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7724d69a9294b420ca061d5ad26ad64826372203
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517681"
---
# <a name="how-to-improve-the-detection-accuracy"></a>Instrucciones: Mejorar la precisión de detección 

Identity Protection proporciona mecanismos para enviar comentarios a Azure AD acerca de las detecciones de riesgo de su entorno. Para proporcionar comentarios, puede confirmar el estado del usuario de riesgo detectado o del evento de inicio de sesión. Microsoft usa estos comentarios para tomar medidas en función de las detecciones de riesgos y mejorar la precisión de las detecciones futuras. 


## <a name="what-is-detection"></a>¿Qué es una detección?

La detección es el proceso de identificar actividades sospechosas junto con sus cuentas de usuario. Las actividades sospechosas que Azure AD puede detectar se denominan [Evento de riesgo](../reports-monitoring/concept-risk-events.md). El proceso de detección se basa en algoritmos y heurística de aprendizaje automático adaptable, que se usan para detectar los eventos de riesgo de los usuarios.

Asimismo, los resultados de la detección se usan para determinar si los usuarios y los inicios de sesión están en riesgo. 


## <a name="how-can-i-improve-the-detection-accuracy"></a>¿Cómo puedo mejorar la precisión de la detección?

Debido a que la detección es un proceso automático, es posible que Azure AD devuelva falsos positivos. Puede mejorar la precisión de la detección si envía sus comentarios a Azure AD en referencia a los resultados de la detección.

Existen tres formas de mejorar la precisión de la detección: confirmar el inicio de sesión en peligro, confirmar el inicio de sesión seguro y descartar el riesgo para el usuario. Puede hacerlo mediante los siguientes informes:

- **Informe de inicios de sesión de riesgo:** en el informe de inicio de sesión de riesgo puede confirmar si los inicios de sesión son seguros o si están en peligro.

- **Informe de usuarios de riesgo:** en el informe de usuarios de riesgo, puede descartar el riesgo del usuario. 

Sus comentarios se procesan mediante Azure AD para mejorar la precisión de los resultados de la detección. Normalmente, se deben proporcionar comentarios como parte de una investigación de riesgo de usuario o de inicio de sesión. Para obtener más información, consulte [How to investigate risky users and sign-ins](howto-investigate-risky-users-signins.md) (Cómo investigar usuarios e inicios de sesión de riesgo).


## <a name="confirm-compromised"></a>Confirmar que se encuentra en peligro

Confirmar que un evento de inicio de sesión está peligro indica a Azure AD que el propietario de la identidad no autorizó ese inicio de sesión. Cuando se selecciona "Confirmar que se encuentra en peligro", Azure AD hará lo siguiente:

- Aumentará a un nivel alto el riesgo de usuario correspondiente al usuario afectado.

- Le ayudará a optimizar el aprendizaje automático que detecta los eventos de riesgo.
 
- Tomará medidas adicionales para proteger aún más su organización.



Para confirmar un inicio de sesión en peligro:

- **Informe de inicios de sesión de riesgo**: esta opción le permite confirmar un inicio de sesión en peligro para uno o varios eventos de inicio de sesión.

    ![Descartar el riesgo de usuario](./media/howto-improve-detection-accuracy/07.png)

- **Vista de detalles del informe de inicios de sesión de riesgo**: esta opción le permite confirmar una cuenta en peligro para el evento de inicio de sesión seleccionado en el informe de inicios de sesión de riesgo. 

    ![Descartar el riesgo de usuario](./media/howto-improve-detection-accuracy/04.png)


 
## <a name="confirm-safe"></a>Confirmar que es seguro


Confirmar que un evento de inicio de sesión es seguro indica a Azure AD que el propietario de la identidad **autorizó** ese inicio de sesión. Cuando se selecciona "Confirmar que es seguro", Azure AD hará lo siguiente:

- Revertirá la contribución del riesgo de usuario de los inicios de sesión seleccionados.

- Cerrará los eventos de riesgo subyacentes.

- Le ayudará a optimizar el aprendizaje automático que detecta los eventos de riesgo.

- Tomará medidas adicionales para proteger aún más su organización.
 

Para confirmar un inicio de sesión seguro:

- **Informe de inicios de sesión de riesgo**: esta opción le permite confirmar un inicio de sesión seguro para uno o varios eventos de inicio de sesión.

    ![Descartar el riesgo de usuario](./media/howto-improve-detection-accuracy/08.png)

- **Vista de detalles del informe de inicios de sesión de riesgo**: esta opción le permite confirmar un inicio de sesión seguro para el evento de inicio de sesión seleccionado en el informe de inicios de sesión de riesgo. 

    ![Descartar el riesgo de usuario](./media/howto-improve-detection-accuracy/05.png)




## <a name="dismiss-user-risk"></a>Descartar el riesgo de usuario

Si ya se han eliminado las acciones de corrección para un usuario de riesgo o si cree que se marcaron equivocadamente como de riesgo, puede descartar el riesgo de un usuario. Recuerde que descartar el riesgo de un usuario restaura a ese usuario a un estado sin riesgo. Todos los inicios de sesión y los eventos de riesgo anteriores del usuario se descartarán.


Puede descartar el riesgo de usuario notificado en:

- **El informe de usuarios de riesgo**: esta opción le permite descartar el riesgo de usuario de uno o más usuarios seleccionados.

    ![Descartar el riesgo de usuario](./media/howto-improve-detection-accuracy/02.png)

- **La vista de detalles**: esta opción le permite descartar el riesgo de usuario correspondiente al usuario seleccionado en el informe de riesgo del mismo. 

    ![Descartar el riesgo de usuario](./media/howto-improve-detection-accuracy/01.png)


**Qué debería saber:**

- No se puede revertir esta acción.

- Esta acción puede tardar unos minutos en completarse, por lo que no debe volver a enviar la solicitud.

- Solo puede realizar esta acción si AD administra las credenciales del usuario. 



## <a name="best-practices"></a>Procedimientos recomendados

Descartar el riesgo de un usuario es una forma de desbloquearlo si la directiva de riesgo de usuario lo bloqueó; además, esta acción no se puede corregir por sí misma debido a que no se ha habilitado el restablecimiento de la contraseña o el servicio MFA. En esta situación, es mejor asegurarse de que el usuario se registra para restablecer la contraseña y habilitar el servicio MFA, de modo que pueda remediar automáticamente cualquier evento de riesgo futuro.


## <a name="next-steps"></a>Pasos siguientes

Para obtener una visión general de Azure AD Identity Protection, consulte la [Introducción a Azure AD Identity Protection](overview-v2.md).


