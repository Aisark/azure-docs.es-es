---
title: ¿Qué es Azure Active Directory Identity Protection?
description: Detección, corrección, investigación y análisis de riesgos con Azure AD Identity Protection
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e928d67ba7102df3d342e77705ea895f9230ff3
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/24/2019
ms.locfileid: "72887705"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>¿Qué es Azure Active Directory Identity Protection?

Identity Protection es una herramienta que permite a las organizaciones realizar tres tareas clave:

- Automatizar la detección y corrección de riesgos basados en la identidad.
- Investigar los riesgos de usar los datos en el portal.
- Exportar los datos de detección de riesgos a utilidades de terceros para su posterior análisis.

Identity Protection usa los aprendizajes que Microsoft ha adquirido de su puesto en organizaciones con Azure AD, el espacio de consumidor con cuentas de Microsoft y juegos con Xbox para proteger a los usuarios. Microsoft analiza 6,5 billones de señales al día para identificar y proteger a los clientes frente a amenazas.

Las señales que genera y con las que se alimenta Identity Protection se pueden insertar en herramientas como Acceso condicional para tomar decisiones de acceso o alimentar una herramienta de información de seguridad y administración de eventos (SIEM) para realizar una investigación más detallada en función de las directivas aplicadas.

## <a name="why-is-automation-important"></a>¿Por qué es importante la automatización?

En su [entrada de blog de octubre de 2018](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Eight-essentials-for-hybrid-identity-3-Securing-your-identity/ba-p/275843), Alex Weinert, que dirige al equipo de protección y seguridad de la identidad de Microsoft, explica por qué la automatización es tan importante cuando se trabaja con el volumen de eventos:

> Cada día, nuestros sistemas de aprendizaje automático y heurística proporcionan puntuaciones de riesgo para 18 mil millones de intentos de inicio de sesión para más de 800 millones de cuentas distintas, 300 millones de los cuales proceden claramente de adversarios (entidades como actores criminales o hackers).
>
> El año pasado, en Ignite, hablé sobre los tres ataques principales a nuestros sistemas de identidad. Este es el volumen reciente de estos ataques
>   
>   - **Reproducción de infracciones**: 4600 millones detectados en mayo de 2018
>   - **Difusión de contraseña**: 350 000 en abril de 2018
>   - **Suplantación de identidad (phishing)** : Es difícil de cuantificar con precisión, pero en marzo de 2018, detectamos 23 millones de eventos de riesgo, muchos de los cuales estaban relacionados con la suplantación de identidad (phishing)

## <a name="risk-detection-and-remediation"></a>Detección y corrección de riesgos

Identity Protection identifica los riesgos de las siguientes clasificaciones:

| Tipo de detección de riesgo | DESCRIPCIÓN |
| --- | --- |
| Viaje atípico | Iniciar sesión desde una ubicación atípica, en función de los recientes inicios de sesión del usuario. |
| Dirección IP anónima | Iniciar sesión desde una dirección IP anónima (por ejemplo: el explorador Tor o redes VPN anonimizadoras). |
| Propiedades de inicio de sesión desconocidas | Iniciar sesión con propiedades que no hemos observado recientemente en el usuario en cuestión. |
| Dirección IP vinculada al malware | Iniciar sesión desde una dirección IP vinculada al malware. |
| Filtración de credenciales | Esta detección de riesgo indica que se han filtrado las credenciales válidas del usuario. |
| Inteligencia de Azure AD sobre amenazas | Las fuentes de inteligencia de amenazas internas y externas de Microsoft han identificado un patrón de ataque conocido |

Puede encontrar más información sobre estos riesgos y cómo/cuándo se calculan en el artículo [Qué es el riesgo](concept-identity-protection-risks.md).

Las señales de riesgo pueden desencadenar esfuerzos de corrección, como exigir a los usuarios que usen Azure Multi-Factor Authentication, que restablezcan su contraseña mediante el autoservicio de restablecimiento de contraseña o que apliquen bloqueos hasta que un administrador tome medidas.

## <a name="risk-investigation"></a>Investigación del riesgo

Los administradores pueden revisar las detecciones y realizar acciones manuales sobre ellas si es necesario. Hay tres informes clave que los administradores usan para las investigaciones en Identity Protection:

- Usuarios de riesgo
- Inicios de sesión no seguros
- Detecciones de riesgo

Puede encontrar más información en el artículo [Cómo investigar los riesgos](howto-identity-protection-investigate-risk.md).

## <a name="exporting-risk-data"></a>Exportación de datos de riesgo

Los datos de Identity Protection se pueden exportar a otras herramientas para archivarlos y seguir investigando y estableciendo correlaciones. Las API basadas en Microsoft Graph permiten a las organizaciones recopilar estos datos para su posterior procesamiento en una herramienta como su SIEM. Puede encontrar información sobre cómo acceder a la API de Identity Protection en el artículo [Introducción a Azure Active Directory Identity Protection y Microsoft Graph](howto-identity-protection-graph-api.md)

Puede encontrar información sobre la integración de información de Identity Protection con Azure Sentinel en el artículo [Conectar datos de Azure AD Identity Protection](../../sentinel/connect-azure-ad-identity-protection.md).

## <a name="permissions"></a>Permisos

Identity Protection requiere que los usuarios tengan el rol Lector de seguridad, Operador de seguridad, Administrador de seguridad, Lector global o Administrador global para poder acceder.

## <a name="license-requirements"></a>Requisitos de licencia

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

| Capacidad | Detalles | Azure AD Premium P2 | Azure AD Premium P1 | Azure AD Basic/Free |
| --- | --- | --- | --- | --- |
| Directivas de riesgo | Directiva de riesgo de usuario (mediante Identity Protection) | Sí | No | Sin |
| Directivas de riesgo | Directiva de riesgo de inicio de sesión (mediante Identity Protection o acceso condicional) | Sí | No | Sin |
| Informes de seguridad | Información general | Sí | No | Sin |
| Informes de seguridad | Usuarios de riesgo | Acceso total | Información limitada | Información limitada |
| Informes de seguridad | Inicios de sesión no seguros | Acceso total | Información limitada | Información limitada |
| Informes de seguridad | Detecciones de riesgo | Acceso total | Información limitada | Sin |
| Notificaciones | Alertas detectadas sobre usuarios en riesgo | Sí | No | Sin |
| Notificaciones | Resumen semanal | Sí | No | Sin |
| | Directiva de registro de MFA | Sí | No | Sin |

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la seguridad](concept-identity-protection-security-overview.md)

- [¿Qué es el riesgo?](concept-identity-protection-risks.md)

- [Directivas disponibles para mitigar los riesgos](concept-identity-protection-policies.md)
