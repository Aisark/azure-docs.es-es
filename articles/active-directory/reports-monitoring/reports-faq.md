---
title: Preguntas frecuentes sobre los informes de Azure Active Directory | Microsoft Docs
description: Preguntas frecuentes en torno a los informes de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: 534da0b1-7858-4167-9986-7a62fbd10439
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fc90d62f889bfd9f439a7e8955f049c6c979746
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60437046"
---
# <a name="frequently-asked-questions-around-azure-active-directory-reports"></a>Preguntas frecuentes en torno a los informes de Azure Active Directory

Este artículo incluye respuestas a preguntas más frecuentes sobre los informes de Azure Active Directory (Azure AD). Para más información, consulte [Informes de Azure Active Directory](overview-reports.md). 

## <a name="getting-started"></a>Introducción 

**P: Usan actualmente la `https://graph.windows.net/<tenant-name>/reports/` informa de las API de uso de aplicaciones integradas y auditoría de Azure AD de la incorporación de cambios en nuestros sistemas de informes mediante programación de punto de conexión. ¿A cuál debo cambiar?**

**R:** Consulte la [referencia de API](https://developer.microsoft.com/graph/) para ver cómo puede [usar las API para acceder a informes de actividad](concept-reporting-api.md). Este punto de conexión tiene dos informes (**auditoría** e **inicios de sesión**) que proporcionan todos los datos que obtuvo en el punto de conexión de la API anterior. Este nuevo punto de conexión también tiene un informe de inicios de sesión con la licencia Premium de Azure AD que puede usar para obtener información del uso de aplicaciones y de dispositivos, y de inicio de sesión de usuarios.

---

**P: Usan actualmente la `https://graph.windows.net/<tenant-name>/reports/` punto de conexión de API para extraer informes de seguridad de Azure AD (tipos específicos de detecciones, como las credenciales perdidas o inicios de sesión desde direcciones IP anónimas) en los sistemas de notificación mediante programación. ¿A cuál debo cambiar?**

**R:** Puede usar la  [API de eventos de riesgo de Identity Protection](../identity-protection/graph-get-started.md)  para acceder a las detecciones de seguridad mediante Microsoft Graph. Este nuevo formato proporciona mayor flexibilidad en el modo en que permite consultar los datos, con filtrado avanzado, selección de campos y mucho más, y normaliza los eventos de riesgo en un tipo para facilitar la integración en SIEM y otras herramientas de recolección de datos. Dado que los datos están en un formato diferente, no puede sustituir una nueva consulta para las consultas anteriores. Sin embargo, [la nueva API utiliza Microsoft Graph](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityriskevent), que es el estándar de Microsoft para estas API como Office 365 o Azure AD. Así, el trabajo necesario puede dilatar sus inversiones actuales en MS Graph o ayudarle a comenzar la transición a esta nueva plataforma estándar.

---

**P: ¿Cómo puedo obtener una licencia Premium?**

**R:** Consulte [Introducción a Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) para actualizar la edición de Azure Active Directory.

---

**P: ¿Cuándo se deberían ver los datos de actividades después de obtener una licencia Premium?**

**R:** Si ya tiene datos de actividades como una licencia gratuita, entonces puede verlos inmediatamente. Si no tiene ningún dato, tardarán uno o dos días en mostrarse en los informes.

---

**P: ¿Puedo ver los datos del último mes después de obtener una licencia de Azure AD Premium?**

**R:** Si ha cambiado recientemente a una versión Premium (incluida una versión de prueba), inicialmente, puede ver datos de hasta siete días. Cuando se acumulan datos, puede ver los datos de los últimos 30 días.

---

**P: ¿Tengo que ser administrador global para ver los inicios de sesión de actividad en Azure Portal o para obtener datos mediante la API?**

**R:** No, también puede acceder a los datos de informes mediante el portal o por medio de la API si es un **Lector de seguridad** o un **Administrador de seguridad** en el inquilino. Por supuesto, los **Administradores globales** también tendrá acceso a estos datos.

---


## <a name="activity-logs"></a>Registros de actividad


**P: ¿Cuál es la retención de datos de los registros de actividad (auditoría e inicios de sesión) en Azure Portal?** 

**R:** En la tabla siguiente se muestra el período de retención de datos de los registros de actividad. Para más información, consulte las [directivas de retención de datos para informes de Azure AD](reference-reports-data-retention.md).

| Informe                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registros de auditoría             | 7 días        | 30 días             | 30 días             |
| Inicios de sesión               | N/D           | 30 días             | 30 días             |
| Uso de Azure MFA        | 30 días       | 30 días             | 30 días             |

---

**P: ¿Cuánto tiempo debo esperar para poder ver los datos de actividad después de haber completado una tarea?**

**R:** Los registros de auditoría tienen una latencia que oscila entre 15 minutos y una hora. Los registros de actividad de inicio de sesión pueden tardar desde 15 minutos hasta 2 horas para algunos registros.

---

**P: ¿Puedo obtener información del registro de actividad de Office 365 a través de Azure Portal?**

**R:** Aunque Office 365 y los registros de actividad de Azure AD comparten mucho de los recursos del directorio, si desea que una vista completa de los registros de actividad de Office 365, debe ir a la [centro de administración de Microsoft 365](https://admin.microsoft.com) al obtener el registro de actividad de Office 365 información.

---

**P: ¿Qué API utilizo para obtener información sobre los registros de actividad de Office 365?**

**R:** Use las [API de Administración de Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview) para acceder a los registros de actividad de Office 365 a través de una API.

---

**P: ¿Cuántos registros puedo descargar de Azure Portal?**

**R:** Puede descargar hasta 5000 registros de Azure Portal. Los registros se ordenan a partir de los *más recientes* y, de forma predeterminada, se obtienen los últimos 5000 registros.

---

## <a name="risky-sign-ins"></a>Inicios de sesión no seguros

**P: Hay un evento de riesgo en Identity Protection, pero no veo el inicio de sesión correspondiente en el informe de inicios de sesión. ¿Es normal?**

**R:** Sí, Identity Protection evalúa el riesgo de todos los flujos de autenticación si es interactivo o no interactivo. Sin embargo, el informe únicamente de todos los inicios de sesión muestra los inicios de sesión interactivos.

---

**P: ¿Cómo puedo saber por qué un inicio de sesión o un usuario se marcó como peligroso en Azure Portal?**

**R:** Si tiene una suscripción a **Azure AD Premium**, puede aprender más sobre los eventos de riesgo subyacentes mediante la selección de un usuario en **Usuarios marcados en riesgo** o la selección de un registro en el informe **Inicios de sesión de riesgo**. Si tiene una suscripción **gratuita** o **básica**, puede ver los usuarios en riesgo y los informes de inicios de sesión en riesgo, pero no puede ver la información de los eventos de riesgo subyacentes.

---

**P: ¿Cómo se calculan las direcciones IP en los inicios de sesión y en el informe de inicios de sesión de riesgo?**

**R:** Las direcciones IP se emiten de forma que no haya ninguna conexión definitiva entre una dirección IP y donde se encuentre físicamente el equipo con esa dirección. La asignación de direcciones IP se complica aún más por factores tales como los proveedores de dispositivos móviles y VPN que emiten direcciones IP desde grupos centrales, a menudo muy lejos de donde se usa realmente el dispositivo del cliente. Actualmente, la conversión de la dirección IP en una ubicación física en los informes de Azure AD es un esfuerzo notable basado en seguimientos, datos del Registro, búsquedas inversas y otra información. 

---

**P: ¿Qué significa el evento de riesgo "Inicio de sesión con riesgo adicional detectado"?**

**R:** Para proporcionarle información detallada de todos los inicios de sesión en riesgo en su entorno, "Inicio de sesión con riesgo adicional detectado" funciona como marcador de posición para los inicios de sesión en detecciones exclusivas de los suscriptores de Azure AD Identity Protection.

---

## <a name="conditional-access"></a>Acceso condicional

**P: ¿Qué novedades ofrece esta característica?**

**R:** Los clientes ahora pueden solucionar problemas de directivas de acceso condicional con todos los informes de inicios de sesión. Los clientes pueden revisar el estado de acceso condicional y profundizar en los detalles de las directivas que se aplican al inicio de sesión y del resultado de cada directiva.

**P: ¿Cómo empiezo?**

**R:** Primeros pasos:

* Navegue hasta el informe de inicios de sesión en [Azure Portal](https://portal.azure.com).
* Haga clic en el inicio de sesión cuyos problemas desea solucionar.
* Vaya a la pestaña **Acceso condicional**. Aquí puede ver todas las directivas que afectaron al inicio de sesión y el resultado de cada directiva. 
    
**P: ¿Cuáles son todos los valores posibles para el estado de acceso condicional?**

**R:** El estado de acceso condicional puede tener los siguientes valores:

* **No aplicado**: significa que no existía en el ámbito ninguna directiva de acceso condicional con el usuario y la aplicación. 
* **Correcto**: significa que existía en el ámbito una directiva de acceso condicional con el usuario y la aplicación y que las directivas de acceso condicional se cumplieron correctamente. 
* **Error**: significa que existía en el ámbito una directiva de acceso condicional con el usuario y la aplicación y que las directivas de acceso condicional no se cumplieron correctamente. 
    
**P: ¿Cuáles son todos los valores posibles para el resultado de la directiva de acceso condicional?**

**R:** La directiva de acceso condicional puede ofrecer los siguientes resultados:

* **Correcto**: la directiva se cumplió correctamente.
* **Error**: no se cumplió la directiva.
* **No aplicado**: puede deberse a que las condiciones de la directiva no se cumplieron.
* **No habilitado**: puede deberse a que la directiva presenta un estado deshabilitado. 
    
**P: El nombre de la directiva del informe de todos los inicios de sesión no coincide con el nombre de la directiva de acceso condicional. ¿Por qué?**

**R:** El nombre de la directiva del informe de todos los inicios de sesión se basa en el nombre de la directiva de acceso condicional en el momento en que se inicia sesión. Puede ser incoherente con el nombre de la directiva de acceso condicional si actualizó el nombre de la directiva más tarde, es decir, después de iniciar sesión.

**P: Mi inicio de sesión se ha bloqueado debido a una directiva de acceso condicional, pero en el informe de actividad de inicio de sesión se muestra que el inicio de sesión se realizó correctamente. ¿Por qué?**

**R:** Actualmente, en el informe de inicio de sesión no se pueden mostrar resultados precisos para escenarios de Exchange ActiveSync cuando se aplica el acceso condicional. Puede haber casos en los que el resultado de inicio de sesión del informe muestra un inicio de sesión correcto, pero en realidad el inicio de sesión generó un error debido a una directiva de acceso condicional. 
