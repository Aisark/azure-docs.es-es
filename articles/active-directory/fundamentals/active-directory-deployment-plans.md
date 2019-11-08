---
title: Planes de implementación de Azure Active Directory | Microsoft Docs
description: Guía de un extremo a otro sobre cómo implementar muchas funcionalidades de Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 949676c622c87831d22dcfc7e1bc6d920b622738
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473282"
---
# <a name="azure-active-directory-deployment-plans"></a>Planes de implementación de Azure Active Directory
¿Busca una guía de un extremo a otro sobre cómo implementar las funcionalidades de Azure Active Directory (Azure AD)? Los planes de implementación de Azure AD le guían por el valor de negocio, las consideraciones de planeamiento y los procedimientos operativos necesarios para implementar correctamente las funcionalidades comunes de Azure AD.

Desde cualquiera de las páginas del plan, use la funcionalidad Imprimir en PDF del explorador para crear una versión sin conexión actualizada de la documentación.
## <a name="include-the-right-stakeholders"></a>Inclusión de la parte interesada correcta

Cuando comience a planear la implementación para una nueva funcionalidad, es importante incluir a las partes interesadas que son clave en su organización. Es recomendable identificar y documentar a la persona o personas que cumplen cada uno de los siguientes roles y trabajar con ellas para determinar su participación en el proyecto.  

Entre los roles, se incluyen los siguientes: 

|Role |DESCRIPCIÓN |
|-|-|
|Usuario final|Un grupo representativo de usuarios para el que se implementará la funcionalidad. A menudo previsualiza los cambios en un programa piloto.
|Administrador de soporte técnico de TI|Un representante de la organización de soporte técnico de TI que puede proporcionar información sobre la compatibilidad de este cambio desde una perspectiva del departamento de soporte técnico.  
|Arquitecto de identidades o administrador global de Azure|Un representante del equipo de administración de identidades responsable de definir cómo este cambio se alinea con la infraestructura de administración de identidades principal de su organización.|
|Propietario empresarial de la aplicación |El propietario empresarial global de las aplicaciones afectadas, que pueden incluir la administración del acceso.  También puede proporcionar información sobre la experiencia del usuario y la utilidad de este cambio desde la perspectiva del usuario final.
|Propietario de seguridad|Un representante del equipo de seguridad que puede aprobar que el plan cumplirá los requisitos de seguridad de la organización.|
|Administrador de cumplimiento|La persona de la organización responsable de garantizar el cumplimiento con requisitos corporativos, del sector o gubernamentales.|

**Los niveles de implicación podrían incluir:**

- **R**esponsible para implementar el plan del proyecto y el resultado 

- **A**probación del plan del proyecto y el resultado 

- **C**olaborador con el plan del proyecto y el resultado 

- **I**nformado del plan del proyecto y el resultado


## <a name="best-practices-for-a-pilot"></a>Procedimientos recomendados para un piloto
Un piloto le permite probar con un grupo pequeño antes de activar una funcionalidad para todos. Asegúrese de que, como parte de sus pruebas, cada caso de uso de su organización se prueba de forma exhaustiva. Es mejor dirigirse a un grupo específico de usuarios piloto antes de implementar esto en su organización como un todo.

En su primera oleada, TI de destino, facilidad de uso y otros usuarios adecuados que pueden probar y proporcionar comentarios. Estos comentarios deben usarse para desarrollar aún más las comunicaciones e instrucciones que envía a sus usuarios y proporcionarles información sobre los tipos de problemas que puede ver su personal de soporte técnico. 

La ampliación de la implementación en grupos de usuarios más grandes debe llevarse a cabo aumentando el ámbito de los grupos objetivo. Esto puede hacerse a través de la [pertenencia dinámica a grupos](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) o agregando usuarios manualmente a los grupos objetivo.


## <a name="deploy-authentication"></a>Implementación de la autenticación

| Capacidad | DESCRIPCIÓN|
| -| -|
| [Multi-Factor Authentication](https://aka.ms/deploymentplans/mfa)| Azure Multi-Factor Authentication (MFA) es la solución de Microsoft de comprobación de dos pasos. El uso de métodos de autenticación aprobados por un administrador permite a Azure MFA ayudar a proteger el acceso a sus datos y aplicaciones, además de satisfacer la exigencia de un proceso de inicio de sesión simple. |
| [Acceso condicional](https://aka.ms/deploymentplans/ca)| Con Acceso condicional, puede implementar decisiones de control de acceso automatizado sobre quién puede acceder a las aplicaciones en nube, en función de condiciones. |
| [Restablecimiento de la contraseña de autoservicio](https://aka.ms/deploymentplans/sspr)| El autoservicio de restablecimiento de contraseñas proporciona a los usuarios la capacidad de restablecer sus contraseñas, sin intervención de ningún administrador, en el momento y el lugar donde se precisa. |
| [Inicio de sesión sin contraseña](https://aka.ms/deploymentplans/passwordless) | Implementación de la autenticación sin contraseña mediante la aplicación Microsoft Authenticator o las claves de seguridad de FIDO2 de su organización |

## <a name="deploy-application-management"></a>Implementación de la administración de aplicaciones

| Capacidad | DESCRIPCIÓN|
| -| - |
| [Inicio de sesión único](https://aka.ms/deploymentplans/sso)| El inicio de sesión único ayuda a los usuarios a acceder a las aplicaciones y los recursos que necesitan para hacer negocios, iniciando sesión una sola vez. Una vez iniciada la sesión, pueden ir de Microsoft Office a SalesForce, a Box y a aplicaciones internas sin necesidad de escribir credenciales una segunda vez. |
| [Panel de acceso](https://aka.ms/deploymentplans/accesspanel)| Proporcione a sus usuarios un lugar centralizado y sencillo desde el que puedan acceder a todas las aplicaciones. Favorezca la productividad con funcionalidades de autoservicio, como la solicitud de acceso a aplicaciones y grupos, o la administración del acceso a recursos en nombre de otros usuarios. |


## <a name="deploy-hybrid-scenarios"></a>Implementación de escenarios híbridos

| Capacidad | DESCRIPCIÓN|
| -| -|
| [ADFS para sincronización de hash de contraseña](https://aka.ms/deploymentplans/adfs2phs)| Con la sincronización de hash de contraseña, los valores de hash de las contraseñas de usuario se sincronizan desde Active Directory local a Azure AD, lo que permite a Azure AD autenticar a los usuarios sin interacción con Active Directory local. |
| [Autenticación de ADFS para paso a través](https://aka.ms/deploymentplans/adfs2pta)| La autenticación de paso a través de Azure AD ayuda a los usuarios a iniciar sesión en aplicaciones basadas en la nube y locales con las mismas contraseñas. Esta característica proporciona a los usuarios una mejor experiencia (una contraseña menos que recordar) y reduce los costos del departamento de soporte técnico de TI dado que es menos probable que olviden cómo iniciar sesión. Cuando los usuarios inician sesión con Azure AD, esta característica valida sus contraseñas directamente con la instancia de Active Directory local. |
| [Azure AD Application Proxy](https://aka.ms/deploymentplans/appproxy)| Hoy en día, los empleados desean ser productivos en cualquier lugar, en cualquier momento y con cualquier dispositivo. Tienen que acceder a aplicaciones SaaS en la nube y a aplicaciones corporativas locales. El proxy de aplicación de Azure AD permite este acceso sólido sin redes privadas virtuales (VPN) costosas y complejas ni zonas desmilitarizadas (DMZ). |
| [SSO de conexión directa](https://aka.ms/SeamlessSSODPDownload)| El inicio de sesión único de conexión directa de Azure Active Directory (SSO de conexión directa de Azure AD) permite iniciar sesión automáticamente a los usuarios en dispositivos corporativos conectados a la red de la empresa. Con esta característica, los usuarios no tendrán que escribir la contraseña para iniciar sesión en Azure AD ni en general el nombre de usuario. Esta característica proporciona a los usuarios autorizados un acceso sencillo a las aplicaciones en la nube sin necesidad de componentes locales adicionales. |

## <a name="deploy-user-provisioning"></a>Implementación del aprovisionamiento de usuarios

| Capacidad | DESCRIPCIÓN|
| -| -|
| [Aprovisionamiento de usuarios](https://aka.ms/deploymentplans/userprovisioning)| Azure AD le ayuda a automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en aplicaciones en la nube (SaaS) como Dropbox, Salesforce y muchas otras. |
| [Aprovisionamiento de usuarios de entrada basado en Workday](https://aka.ms/WorkdayDeploymentPlan)| El escenario "Aprovisionamiento de usuarios de entrada basado en Workday" para Active Directory establece las bases de una gobernanza continua de identidades y mejora la calidad de los procesos de negocio que se basan en datos de identidades fidedignos. Con esta característica, puede administrar fácilmente el ciclo de vida de las identidades de los empleados y los trabajadores temporales configurando reglas que asignen procesos de tipo alta, baja o traslado (como Nueva contratación, Fin de contrato y Traslado) a las acciones de aprovisionamiento de TI (como Crear, Habilitar y Deshabilitar). |

## <a name="deploy-governance-and-reporting"></a>Implementación de la gobernanza y los informes

| Capacidad | DESCRIPCIÓN|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) le permite administrar roles con privilegios administrativos en Azure AD, los recursos de Azure y otros servicios de Microsoft Online Services. Asimismo, PIM ofrece soluciones como acceso de tipo Just-In-Time, flujos de trabajo de aprobación de solicitudes y revisiones de acceso completamente integradas para que pueda identificar, detectar y evitar en tiempo real las actividades malintencionadas de roles con privilegios. |
| [Creación de informes y supervisión](https://aka.ms/deploymentplans/reporting)| El diseño de la solución de creación de informes y supervisión de Azure AD depende de sus requisitos legales, de seguridad y operativos, así como del entorno y los procesos existentes. En este artículo se presentan las distintas opciones de diseño y se le guía para que elija la estrategia de implementación adecuada. |
