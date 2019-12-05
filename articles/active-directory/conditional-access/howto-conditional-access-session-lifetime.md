---
title: 'Configuración de la administración de sesiones de autenticación: Azure Active Directory'
description: Personalice la configuración de la sesión de autenticación de Azure AD incluida la frecuencia de inicio de sesión de usuario y la persistencia de la sesión del explorador.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 797475bfe0f1ec077ad39c6fce1f0facdf679802
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483466"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Configuración de la administración de las sesiones de autenticación con el acceso condicional

En las implementaciones complejas, las organizaciones pueden tener la necesidad de restringir las sesiones de autenticación. Algunos escenarios podrían incluir:

* El acceso a los recursos desde un dispositivo no administrado o compartido
* El acceso a información confidencial desde una red externa
* Usuarios de gran impacto
* Aplicaciones empresariales críticas

Los controles de acceso condicional le permiten crear directivas dirigidas a casos de uso específicos dentro de la organización sin afectar a todos los usuarios.

Antes de entrar en detalles sobre cómo configurar la directiva, examinemos la configuración predeterminada.

## <a name="user-sign-in-frequency"></a>Frecuencia de inicio de sesión de usuario

La frecuencia de inicio de sesión define el período de tiempo antes de que se pida a un usuario que vuelva a iniciar sesión cuando intenta acceder a un recurso.

La configuración predeterminada de Azure Active Directory (Azure AD) para la frecuencia de inicio de sesión de usuario es período sucesivo de 90 días. Pedir credenciales a los usuarios a menudo parece algo sensato, pero puede resultar contraproducente: los usuarios que están capacitados para escribir sus credenciales sin pensarlo pueden suministrarlas sin querer a una petición de credenciales malintencionada.

Puede parecer alarmante no pedir a un usuario que vuelva a iniciar sesión; en realidad, cualquier infracción de las directivas de TI revocará la sesión. Algunos ejemplos incluyen, pero sin limitarse, un cambio de contraseña, un dispositivo que no cumple con las normas o la deshabilitación de la cuenta. También puede explícitamente [revocar sesiones de usuarios mediante PowerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0). La configuración predeterminada de Azure AD se reduce a "no pedir a los usuarios que proporcionen sus credenciales si la posición de seguridad de sus sesiones no ha cambiado".

La configuración de la frecuencia de inicio de sesión funciona con aplicaciones que han implementado los protocolos OAUTH2 o OIDC de acuerdo con los estándares. La mayoría de aplicaciones nativas de Microsoft para Windows, Mac y dispositivos móviles que incluyen las aplicaciones web siguientes cumplen con la configuración.

- Word, Excel y PowerPoint Online
- OneNote Online
- Office.com
- Portal de administración de O365
- Exchange Online
- SharePoint y OneDrive
- Cliente web de Teams
- Dynamics CRM Online
- Portal de Azure

## <a name="persistence-of-browsing-sessions"></a>Persistencia de las sesiones de exploración

Una sesión persistente del explorador permite a los usuarios permanecer conectados después de cerrar y volver a abrir la ventana del explorador.

El valor predeterminado de Azure AD para la persistencia de la sesión del explorador permite a los usuarios de dispositivos personales elegir si desean mantener la sesión mostrando un aviso de "¿Quiere mantener la sesión iniciada?" tras una autenticación correcta. Si se configura la persistencia del explorador en AD FS mediante las instrucciones del artículo [Configuración de inicio de sesión único de AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), cumpliremos con esa directiva y persistiremos también en la sesión de Azure AD. También, para configurar si los usuarios del inquilino van a ver el aviso "¿Quiere mantener la sesión iniciada?", cambie la configuración apropiada en el panel de la marca de la empresa en Azure Portal mediante la guía del artículo [Personalización de la página de inicio de sesión de Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Configuración de los controles de sesión de autenticación

El acceso condicional es una funcionalidad de Azure AD Premium y requiere una licencia prémium. Para más información sobre el acceso condicional, consulte [Acceso condicional de Azure Active Directory](overview.md#license-requirements).

> [!WARNING]
> Si utiliza la característica de [vigencia del token configurable](../develop/active-directory-configurable-token-lifetimes.md) actualmente en versión preliminar pública, tenga en cuenta que no se admite la creación de dos directivas diferentes para el mismo usuario o combinación de aplicaciones: una con esta característica y otra con la característica de vigencia del token configurable. Microsoft piensa retirar la característica de vigencia del token configurable el 1 de mayo de 2020 y reemplazarla por la característica de administración de sesiones de autenticación de acceso condicional.  

### <a name="policy-1-sign-in-frequency-control"></a>Directiva 1: Control de la frecuencia de inicio de sesión

1. Cree una nueva directiva.
1. Elija todas las condiciones necesarias para el entorno del cliente, incluidas las aplicaciones en la nube de destino.

   > [!NOTE]
   > Se recomienda establecer la misma frecuencia de autenticación para las aplicaciones clave de Microsoft Office, como Exchange Online y SharePoint Online, para una mejor experiencia del usuario.

1. Vaya a **Controles de acceso** > **Sesión** y haga clic en **Frecuencia de inicio de sesión**.
1. Escriba el valor requerido de los días y horas en el primer cuadro de texto.
1. Seleccione un valor para las **horas** o **días** en la lista desplegable.
1. Guarde la directiva.

![Directiva de acceso condicional configurada para la frecuencia de inicio de sesión](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

En los dispositivos de Windows registrados en Azure AD, el inicio de sesión en el dispositivo se considera un aviso. Por ejemplo, si ha configurado la frecuencia de inicio de sesión a 24 horas para aplicaciones de Office, los usuarios de dispositivos de Windows registrados en Azure AD cumplirán la directiva de frecuencia de inicio de sesión mediante el inicio de sesión en el dispositivo y no se les volverá a preguntar cuando abran las aplicaciones de Office.

Si ha configurado una frecuencia de inicio de sesión diferente para distintas aplicaciones web que se ejecutan en la misma sesión del navegador, se aplicará la directiva más estricta a ambas aplicaciones porque todas las aplicaciones que se ejecutan en la misma sesión del explorador comparten un único token de sesión.

### <a name="policy-2-persistent-browser-session"></a>Directiva 2: Sesión del explorador persistente

1. Cree una nueva directiva.
1. Elija todas las condiciones necesarias.

   > [!NOTE]
   > Tenga en cuenta que este control requiere elegir "Todas las aplicaciones en la nube" como una condición. La persistencia de la sesión del explorador se controla mediante el token de la sesión de autenticación. Todas las pestañas de una sesión del explorador comparten un único token de sesión y, por tanto, todas ellas deben compartir el estado de persistencia.

1. Vaya a **Controles de acceso** > **Sesión** y haga clic en **Sesión del explorador persistente**.
1. Seleccione un valor en la lista desplegable.
1. Guarde la directiva.

![Directiva de acceso condicional configurada para el explorador persistente](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> La configuración de la sesión del explorador persistente en el acceso condicional de Azure AD sobrescribirá la configuración "¿Quiere mantener la sesión iniciada?" en el panel de personalización de marca de la empresa en Azure Portal para el mismo usuario si ha configurado las dos directivas.

## <a name="validation"></a>Validación

Utilice la herramienta What-If para simular un inicio de sesión del usuario en la aplicación de destino y otras condiciones según la configuración de la directiva. Los controles de administración de la sesión de autenticación se muestran en el resultado de la herramienta.

![Resultados de la herramienta "What If" para el acceso condicional](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Implementación de directivas

Para asegurarse de que la directiva funciona según lo esperado, el procedimiento recomendado es probarla antes de implementarla en producción. Lo ideal es usar un inquilino de prueba para comprobar si la nueva directiva funciona según lo previsto. Para más información, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](best-practices.md).

## <a name="next-steps"></a>Pasos siguientes

* Si quiere saber cómo configurar una directiva de acceso condicional, consulte el artículo [Exigir MFA para aplicaciones específicas con acceso condicional de Azure Active Directory](app-based-mfa.md).
* Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](best-practices.md).
