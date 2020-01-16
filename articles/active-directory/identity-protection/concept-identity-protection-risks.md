---
title: ¿Qué es el riesgo? Azure AD Identity Protection
description: Explicación del riesgo en Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c169c1eb6511cb05a7b46320b74ae293834cca86
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2020
ms.locfileid: "75609152"
---
# <a name="what-is-risk"></a>¿Qué es el riesgo?

Las detecciones de riesgo en Azure AD Identity Protection incluyen todas las acciones sospechosas identificadas, relacionadas con las cuentas de usuario en el directorio.

Identity Protection proporciona a las organizaciones el acceso a recursos eficaces, para ver y responder rápidamente a estas acciones sospechosas. 

![Información general sobre seguridad que muestra usuarios e inicios de sesión de riesgo](./media/concept-identity-protection-risks/identity-protection-security-overview.png)

## <a name="risk-types-and-detection"></a>Tipos de riesgo y detección

Hay dos tipos de riesgo, **Usuario** e **Inicio de sesión** y dos tipos de detección o cálculo **Tiempo real** y **Sin conexión**.

### <a name="user-risk"></a>Riesgo de usuario

Un riesgo de usuario representa la probabilidad de que una identidad o cuenta determinada esté en peligro. 

Estos riesgos se calculan sin conexión, usando orígenes de inteligencia de amenazas internos y externos de Microsoft, incluidos los investigadores de seguridad, los profesionales de la aplicación de la ley, los equipos de seguridad de Microsoft y otros orígenes de confianza.

| Detección de riesgos | Descripción |
| --- | --- |
| Credenciales con fugas | Este tipo de detección de riesgos indica que se han filtrado las credenciales válidas del usuario. Cuando los cibercriminales llegan a poner en peligro las contraseñas válidas de usuarios legítimos, es frecuente que las compartan. Normalmente lo hacen publicándolas en la Web oscura, los sitios de pegado, o bien mediante el intercambio o la venta de esas credenciales en el mercado negro. Cuando el servicio de credenciales filtradas de Microsoft adquiere las credenciales de usuario de la Web oscura, los sitios de pegado u otros orígenes, se comparan con las credenciales válidas actuales de los usuarios de Azure AD para encontrar coincidencias válidas. |
| Inteligencia de Azure AD sobre amenazas | Este tipo de detección de riesgos indica una actividad de usuario poco común para el usuario en cuestión o coherente con patrones de ataque conocidos basados en orígenes de información sobre amenazas internas y externas de Microsoft. |

### <a name="sign-in-risk"></a>Riesgo de inicio de sesión

Un inicio de sesión de riesgo representa la probabilidad de que el propietario de la identidad no haya autorizado una solicitud de autenticación determinada. 

Estos riesgos se pueden calcular en tiempo real o sin conexión, usando orígenes de inteligencia de amenazas internos y externos de Microsoft, incluidos los investigadores de seguridad, los profesionales de la aplicación de la ley, los equipos de seguridad de Microsoft y otros orígenes de confianza.

| Detección de riesgos | Tipo de detección | Descripción |
| --- | --- | --- |
| Dirección IP anónima | Tiempo real | Este tipo de detección de riesgos indica inicios de sesión desde una dirección IP anónima (por ejemplo, el explorador Tor o redes VPN anónimas). Estas direcciones IP normalmente las usan actores que quieren ocultar su telemetría de inicio de sesión (dirección IP, ubicación, dispositivo, etc.) con fines potencialmente malintencionados. |
| Viaje atípico | Sin conexión | Este tipo de detección de riesgos identifica dos inicios de sesión procedentes de ubicaciones geográficamente distantes, donde al menos una de las ubicaciones puede también ser inusual para el usuario, según su comportamiento anterior. Entre otros factores, este algoritmo de aprendizaje automático tiene en cuenta el tiempo entre los dos inicios de sesión y el tiempo que habría necesitado el usuario para viajar de la primera ubicación a la segunda, lo que indica que otro usuario utiliza las mismas credenciales. <br><br> Este algoritmo omite "falsos positivos" obvios que contribuyen a una condición de viaje imposible, como las VPN y las ubicaciones que usan con regularidad otros usuarios de la organización. El sistema tiene un período de aprendizaje inicial de 14 días o 10 inicios de sesión, lo que ocurra primero, durante el cual aprende el comportamiento de inicio de sesión del nuevo usuario. |
| Dirección IP vinculada al malware | Sin conexión | Este tipo de detección de riesgos indica inicios de sesión desde direcciones IP infectadas con malware, que se sabe que se comunican activamente con un servidor bot. Esta detección se determina mediante la correlación de direcciones IP del dispositivo del usuario con direcciones IP que estuvieron en contacto con un servidor bot mientras este último estaba activo. |
| Propiedades de inicio de sesión desconocidas | Tiempo real | Este tipo de detección de riesgos tiene en cuenta el historial de inicio de sesión anterior (dirección IP, latitud/longitud y ASN) para determinar inicios de sesión anómalos. El sistema almacena información acerca de las ubicaciones anteriores utilizadas por un usuario y considera estas ubicaciones "conocidas". La detección de riesgos se desencadena cuando el inicio de sesión se produce desde una ubicación que no está en la lista de ubicaciones conocidas. Los usuarios recién creados estarán en "modo de aprendizaje" durante un período de tiempo, en el que las detecciones de riesgos de las propiedades de inicio de sesión desconocidas estarán desactivadas mientras nuestros algoritmos aprenden el comportamiento del usuario. La duración del modo de aprendizaje es dinámica y depende de cuánto tiempo tarde el algoritmo en recopilar suficiente información sobre los patrones de inicio de sesión del usuario. La duración mínima es de cinco días. Un usuario puede volver al modo de aprendizaje tras un largo período de inactividad. El sistema también ignora los inicios de sesión desde dispositivos conocidos y ubicaciones geográficamente cercanas a una ubicación conocida. <br><br> También se ejecuta esta detección para una autenticación básica o para protocolos heredados. Dado que estos protocolos no tienen propiedades modernas como el identificador de cliente, hay una telemetría limitada para reducir los falsos positivos. Se recomienda que los clientes realicen la migración a la autenticación moderna. |
| Vulneración de identidad de usuario confirmada por el administrador | Sin conexión | Esta detección indica que un administrador ha seleccionado "¿Quiere confirmar la vulneración de la identidad del usuario?" en la interfaz de usuario de riskyUsers o mediante riskyUsers API. Para ver qué administrador ha confirmado este usuario comprometido, compruebe el historial de riesgos del usuario (a través de la interfaz de usuario o la API). |
| Dirección IP malintencionada | Sin conexión | Esta detección indica el inicio de sesión desde una dirección IP malintencionada. Una dirección IP se considera malintencionada si se recibe una alta tasa de errores debidos a credenciales no válidas desde la dirección IP u otros orígenes de reputación de IP. |
| Reglas de manipulación sospechosa de la bandeja de entrada | Sin conexión | Esta detección se debe a [Microsoft Cloud App Security (MCAS)](https://docs.microsoft.com/cloud-app-security/anomaly-detection-policy#suspicious-inbox-manipulation-rules). Esta detección genera un perfil del entorno y activa alertas cuando se establecen reglas sospechosas que eliminan o mueven mensajes o carpetas en la bandeja de entrada de un usuario. Esto puede indicar que la cuenta del usuario está en peligro, que los mensajes se están ocultando intencionadamente y que el buzón se está usando para enviar correo no deseado y malware en su organización. |
| Viaje imposible | Sin conexión | Esta detección se debe a [Microsoft Cloud App Security (MCAS)](https://docs.microsoft.com/cloud-app-security/anomaly-detection-policy#impossible-travel). Esta detección identifica dos actividades de usuario (en una o varias sesiones) que se originan desde ubicaciones geográficamente distantes dentro de un período de tiempo menor que el tiempo que habría tardado el usuario para viajar de la primera ubicación a la segunda, lo que indica que otro usuario está usando las mismas credenciales. |

### <a name="other-risk-detections"></a>Otras detecciones de riesgo

| Detección de riesgos | Tipo de detección | Descripción |
| --- | --- | --- |
| Riesgo adicional detectado | En tiempo real o sin conexión | Esta detección indica que se descubrió una de las detecciones prémium anteriores. Dado que las detecciones premium solo son visibles para los clientes de Azure AD Premium P2, se denominan "Riesgo adicional detectado" para los clientes sin licencias de Azure AD Premium P2. |

## <a name="next-steps"></a>Pasos siguientes

- [Directivas disponibles para mitigar los riesgos](concept-identity-protection-policies.md)

- [Información general sobre seguridad](concept-identity-protection-security-overview.md)
