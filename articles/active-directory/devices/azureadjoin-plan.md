---
title: Planeamiento de la implementación de unión a Azure Active Directory (Azure AD) | Microsoft Docs
description: Explica los pasos necesarios para implementar dispositivos unidos a Azure AD en su entorno.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e58762bd5bf4342804767a200c94b432dd152a0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562222"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Procedimientos para: Planeación de la implementación de la unión a Azure AD

Unión a Azure AD permite unir dispositivos directamente a Azure AD sin necesidad de unirlos a un entorno local de Active Directory, al tiempo que mantiene la productividad y la seguridad de los usuarios. La opción Unión a Azure AD está preparada para la empresa tanto para implementaciones de ámbito como a escala.   

Este artículo proporciona la información que necesita para planear su implementación de Unión a Azure AD.
 
## <a name="prerequisites"></a>Requisitos previos

En este artículo se da por hecho que está familiarizado con la [introducción a la administración de dispositivos en Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planeamiento de la implementación

Para planear la implementación de la unión a Azure AD, debe familiarizarse con:

|   |   |
|---|---|
|![Comprobar][1]|Revisión de los escenarios|
|![Comprobar][1]|Revisión de la infraestructura de identidad|
|![Comprobar][1]|Evaluación de la administración de dispositivos|
|![Comprobar][1]|Explicación de las consideraciones de aplicaciones y recursos|
|![Comprobar][1]|Explicación de las opciones de aprovisionamiento|
|![Comprobar][1]|Configuración de Enterprise State Roaming|
|![Comprobar][1]|Configuración del acceso condicional|

## <a name="review-your-scenarios"></a>Revisión de los escenarios 

Aunque Unión a Azure AD híbrido puede ser la opción preferida para determinados escenarios, Unión a Azure AD le permite realizar la transición a un modelo primero en la nube con Windows. Si tiene previsto modernizar la administración de dispositivos y reducir los costos de TI relacionados con el dispositivo, Unión a Azure AD proporciona una base estupenda para alcanzar estos objetivos.  
 
Debe considerar la opción Unión a Azure AD si sus objetivos se alinean con los criterios siguientes:

- Están adoptando Microsoft 365 como conjunto de aplicaciones de productividad para los usuarios.
- Quiere administrar dispositivos con una solución de administración de dispositivos en la nube.
- Quiere simplificar el aprovisionamiento de dispositivos para usuarios distribuidos geográficamente.
- Tiene previsto modernizar su infraestructura de aplicaciones.

## <a name="review-your-identity-infrastructure"></a>Revisión de la infraestructura de identidad  

Unión a Azure AD funciona con entornos tanto administrados como federados.  

### <a name="managed-environment"></a>Entorno administrado

Un entorno administrado se puede implementar mediante [Sincronización de hash de contraseña](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) o [Autenticación de paso a través](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-quick-start) con Inicio de sesión único de conexión directa.

Estos escenarios no requieren que se configure un servidor de federación para la autenticación.

### <a name="federated-environment"></a>Entorno federado

Un entorno federado debe tener un proveedor de identidades que admita los protocolos WS-Trust y WS-Fed:

- **WS-Fed:** este protocolo es necesario para unir un dispositivo a Azure AD.
- **WS-Trust:** este protocolo es necesario para iniciar sesión en un dispositivo unido a Azure AD. 

Si el proveedor de identidades no admite estos protocolos, la opción Unión a Azure AD no funcionará de forma nativa. A partir de Windows 10 1809, los usuarios pueden iniciar sesión en un dispositivo unido a Azure AD con un proveedor de identidades basado en SAML mediante el [Inicio de sesión web en Windows 10](https://docs.microsoft.com/windows/whats-new/whats-new-windows-10-version-1809#web-sign-in-to-windows-10). Actualmente, el inicio de sesión web es una característica en vista previa y no se recomienda para las implementaciones de producción.

### <a name="smartcards-and-certificate-based-authentication"></a>Tarjetas inteligentes y autenticación basada en certificados

No se puede usar las tarjetas inteligentes ni la autenticación basada en certificados para unir dispositivos a Azure AD. Sin embargo, las tarjetas inteligentes se pueden usar para iniciar sesión en dispositivos unidos a Azure AD si tiene AD FS configurado.

**Recomendación:** Implemente Windows Hello para empresas para la autenticación segura y sin contraseña en dispositivos Windows 10.

### <a name="user-configuration"></a>Configuración del usuario

Si crea usuarios en:

- **Active Directory local**, debe sincronizarlos con Azure AD mediante [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**, no se requiere ninguna configuración adicional.

Los nombres principales de usuario locales son distintos de los nombres principales de usuario de Azure AD no se admiten en los dispositivos unidos a Azure AD. Si los usuarios usan un UPN local, debe planear usar su UPN principal en Azure AD.

## <a name="assess-your-device-management"></a>Evaluación de la administración de dispositivos

### <a name="supported-devices"></a>Dispositivos compatibles

Unión a Azure AD:

- Solo es aplicable a dispositivos Windows 10. 
- No es aplicable a versiones anteriores de Windows ni a otros sistemas operativos. Si tiene dispositivos Windows 7/8.1, debe actualizar a Windows 10 para implementar Unión a Azure AD.
- No es compatible con dispositivos con TPM en modo FIPS.
 
**Recomendación:** Use siempre la versión más reciente de Windows 10 para aprovechar las características actualizadas.

### <a name="management-platform"></a>Plataforma de administración

La característica Administración de dispositivos para dispositivos unidos a Azure AD se basa en una plataforma MDM como Intune y en los CSP de MDM. Windows 10 tiene un agente de MDM integrado que funciona con todas las soluciones MDM compatibles.

> [!NOTE]
> Dado que los dispositivos unidos a Azure AD no están conectados a una instancia de Active Directory local, no se admiten las directivas de grupo. La administración de dispositivos unidos a Azure AD solo es posible mediante MDM

Existen dos enfoques para la administración de dispositivos unidos a Azure AD:

- **Solo MDM**: un dispositivo lo administra exclusivamente un proveedor de MDM como Intune. Todas las directivas se entregan como parte del proceso de inscripción de MDM. Para los clientes de Azure AD Premium o EMS, la inscripción de MDM es un paso automatizado que forma parte de una unión a Azure AD.
- **Administración conjunta**: un dispositivo lo administran un proveedor de MDM y SCCM. En este enfoque, el agente de SCCM se instala en un dispositivo administrado por MDM para administrar determinados aspectos.

Si está usando directivas de grupo, evalúe la paridad de la directiva de MDM mediante la [herramienta de análisis de migración de MDM (MMAT)](https://github.com/WindowsDeviceManagement/MMAT). 

Revise las directivas compatibles y no compatibles para determinar si puede usar una solución MDM en lugar de las directivas de grupo. Para las directivas no compatibles, tenga en cuenta lo siguiente:

- ¿Las directivas no compatibles son necesarias para los dispositivos o usuarios unidos a Azure AD?
- ¿Las directivas no compatibles son aplicables a una implementación basada en la nube?

Si la solución MDM no está disponible a través de la galería de aplicaciones de Azure AD, puede agregarla siguiendo el proceso descrito en [Azure Active Directory integration with MDM](https://docs.microsoft.com/windows/client-management/mdm/azure-active-directory-integration-with-mdm) (Integración de Azure Active Directory con MDM). 

A través de la administración conjunta, puede usar SCCM para administrar determinados aspectos de los dispositivos, mientras que las directivas se entregan a través de la plataforma MDM. Microsoft Intune permite la administración conjunta con SCCM. Para obtener más información, consulte [Administración conjunta para dispositivos de Windows 10](https://docs.microsoft.com/sccm/core/clients/manage/co-management-overview). Si usa un producto MDM distinto de Intune, consulte a su proveedor de MDM sobre los escenarios de administración conjunta aplicables.

**Recomendación:** Considere la posibilidad de usar la administración solo con MDM para los dispositivos unidos a Azure AD.

## <a name="understand-considerations-for-applications-and-resources"></a>Explicación de las consideraciones de aplicaciones y recursos

Se recomienda migrar las aplicaciones del entorno local a la nube para mejorar la experiencia de usuario y el control de acceso. Sin embargo, los dispositivos unidos a Azure AD pueden proporcionar acceso perfectamente a aplicaciones tanto locales como en la nube. Para obtener más información, consulte [How SSO to on-premises resources works on Azure AD joined devices](azuread-join-sso.md) (Funcionamiento del inicio de sesión único a recursos locales en dispositivos unidos a Azure AD).

En las secciones siguientes se indican las consideraciones para los distintos tipos de aplicaciones y recursos.

### <a name="cloud-based-applications"></a>Aplicaciones basadas en la nube

Si una aplicación se agrega a la galería de aplicaciones de Azure AD, los usuarios obtienen el inicio de sesión único a través de los dispositivos unidos a Azure AD. No se requiere ninguna configuración adicional. Los usuarios obtienen el inicio de sesión único en ambos exploradores, Microsoft Edge y Chrome. Para Chrome, debe implementar la [extensión de cuentas de Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Todas las aplicaciones Win32 que:

- Dependen del Administrador de cuentas web (WAM) para que las solicitudes de tokens también obtengan el inicio de sesión único en dispositivos unidos a Azure AD. 
- No se basan en WAM pueden solicitar a los usuarios la autenticación. 

### <a name="on-premises-web-applications"></a>Aplicaciones web locales

Si las aplicaciones se han creado de manera personalizada u hospedado localmente, deberá agregarlas a sitios de confianza del explorador para:

- Habilitar la autenticación integrada de Windows para que funcione. 
- Proporcionar una experiencia de inicio de sesión único sin aviso a los usuarios. 

Si usa AD FS, consulte [Comprobar y administrar el inicio de sesión único con AD FS](https://docs.microsoft.com/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Recomendación:** Considere la posibilidad del hospedaje en la nube (por ejemplo, Azure) y la integración con Azure AD para obtener una mejor experiencia.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Aplicaciones locales basadas en protocolos heredados

Los usuarios obtienen inicio de sesión único desde dispositivos unidos a Azure AD si el dispositivo tiene acceso a un controlador de dominio. 

**Recomendación:** Implemente el [proxy de aplicación de Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) para permitir el acceso seguro para estas aplicaciones.

### <a name="on-premises-network-shares"></a>Recursos compartidos de red local

Los usuarios disponen de inicio de sesión único desde dispositivos unidos a Azure AD cuando un dispositivo tiene acceso a un controlador de dominio local.

### <a name="printers"></a>Impresoras

Para las impresoras, deberá implementar la [impresión en la nube híbrida](https://docs.microsoft.com/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) para detectar impresoras en dispositivos unidos a Azure AD. 

Mientras que las impresoras no se pueden detectar automáticamente en un entorno solo de nube, los usuarios también pueden usar la ruta de acceso UNC de las impresoras para agregarlas directamente. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Aplicaciones locales basadas en la autenticación del equipo

Los dispositivos unidos a AD Azure no admiten las aplicaciones locales basadas en la autenticación del equipo. 

**Recomendación:** Considere la posibilidad de retirar estas aplicaciones y cambiar a sus alternativas modernas.

### <a name="remote-desktop-services"></a>Servicios de Escritorio remoto

La conexión a Escritorio remoto para dispositivos unidos a Azure AD requiere que el equipo host esté unido a Azure AD o unido a Azure AD híbrido. No se admite el escritorio remoto desde un dispositivo no unido o que no sea Windows. Para obtener más información, consulte [Conectarse a un equipo remoto unido a Azure Active Directory](https://docs.microsoft.com/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Explicación de las opciones de aprovisionamiento

Puede aprovisionar la unión a Azure AD con los siguientes enfoques:

- **Autoservicio en la configuración o en Configuración**: en el modo de autoservicio, los usuarios se someten al proceso de unión a Azure AD durante la experiencia de configuración rápida o desde la Configuración de Windows. Para obtener más información, consulte [Una su dispositivo de trabajo a la red de su organización](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot**: permite la configuración previa de dispositivos para una experiencia más fluida en la configuración rápida para realizar una unión a Azure AD. Para obtener más información, consulte [Introducción a Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Inscripción masiva**: permite la unión a Azure AD controlada por un administrador mediante una herramienta de aprovisionamiento en bloque para configurar dispositivos. Para obtener más información, consulte [Inscripción masiva para dispositivos Windows](https://docs.microsoft.com/intune/windows-bulk-enroll).
 
A continuación se incluye una comparación de estos tres enfoques 
 
|   | Configuración de autoservicio | Windows Autopilot | Inscripción masiva |
| --- | --- | --- | --- |
| Requiere la interacción del usuario para la configuración | Sí | Sí | Sin |
| Requiere un esfuerzo de TI | Sin | Sí | Sí |
| Flujos aplicables | OOBE y Configuración | Solo OOBE | Solo OOBE |
| Derechos de administrador local para el usuario principal | Sí, de forma predeterminada | Configurable | Sin |
| Requerir soporte técnico del OEM del dispositivo | Sin | Sí | Sin |
| Versiones compatibles | 1511 y posteriores | 1709 y posteriores | 1703 y posteriores |
 
Elija el enfoque o los enfoques de implementación. Para ello, revise la tabla anterior y tenga en cuenta las consideraciones siguientes a la hora de adoptar alguno:  

- ¿Sus usuarios son expertos en tecnología para realizar la instalación ellos mismos? 
   - La opción de autoservicio puede ser la mejor para estos usuarios. Considere la posibilidad de usar Windows Autopilot para mejorar la experiencia del usuario.  
- ¿Sus usuarios son remotos o están en entornos corporativos? 
   - Las opciones Autoservicio o Autopilot son ideales para que los usuarios remotos puedan realizar la configuración sin problemas. 
- ¿Prefiere una configuración controlada por el usuario o administrada por el administrador? 
   - La inscripción masiva es ideal para la implementación controlada por el administrador para configurar los dispositivos antes de entregarlos a los usuarios.     
- ¿Compra dispositivos de 1 o 2 OEM, o tiene una distribución amplia de dispositivos de OEM?  
   - Si compra a OEM limitados que también admiten Autopilot, puede beneficiarse de una integración más estrecha con Autopilot. 

## <a name="configure-your-device-settings"></a>Configuración de las opciones del dispositivo

Azure Portal permite controlar la implementación de dispositivos unidos a Azure AD en su organización. Para configurar las opciones relacionadas, en la **página Azure Active Directory**, seleccione `Devices > Device settings`.

### <a name="users-may-join-devices-to-azure-ad"></a>Los usuarios pueden unir dispositivos a Azure AD

Establezca esta opción en **Todos** o **Seleccionados** en función del ámbito de la implementación y de a quién quiere permitir la configuración de un dispositivo unido a Azure AD. 

![Los usuarios pueden unir dispositivos a Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Administradores locales adicionales en dispositivos unidos a Azure AD

Elija **Seleccionados** y elija los usuarios que quiere agregar al grupo de administradores locales en todos los dispositivos unidos a Azure AD. 

![Administradores locales adicionales en dispositivos unidos a Azure AD](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Requerir Multi-factor Authentication para unir dispositivos

Seleccione **Sí** si necesita que los usuarios ejecuten MFA al unir dispositivos a Azure AD. Para los usuarios que unen dispositivos a Azure AD con MFA, el propio dispositivo se convierte en un segundo factor.

![Requerir Multi-factor Authentication para unir dispositivos](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Configurar las opciones de movilidad

Para poder configurar las opciones de movilidad, es posible que primero tenga que agregar un proveedor de MDM.

**Para agregar un proveedor de MDM**:

1. En la página **Azure Active Directory**, en la sección **Administrar**, haga clic en `Mobility (MDM and MAM)` 
1. Haga clic en **Agregar aplicación**.
1. Seleccione su proveedor de MDM en la lista.

   ![Adición de una aplicación](./media/azureadjoin-plan/04.png)

Seleccione su proveedor de MDM para configurar las opciones relacionadas. 

### <a name="mdm-user-scope"></a>Ámbito de usuario de MDM

Seleccione **Algunos** o **Todos** en función del ámbito de la implementación. 

![Ámbito de usuario de MDM](./media/azureadjoin-plan/05.png)

En función del ámbito, se dará una de las siguientes situaciones: 

- **El usuario está en el ámbito de MDM**: si tiene una suscripción a Azure AD Premium, la inscripción de MDM se automatiza junto con la unión a Azure AD. Todos los usuarios con ámbito deben tener una licencia adecuada para su MDM. Si se produce un error en la inscripción de MDM en este escenario, la unión a Azure AD también se revertirá.
- **El usuario no está en el ámbito de MDM**: si los usuarios no están en el ámbito de MDM, la unión a Azure AD se completará sin ninguna inscripción a MDM. Esto da como resultado un dispositivo no administrado.

### <a name="mdm-urls"></a>Direcciones URL de MDM

Hay tres direcciones URL relacionadas con la configuración de MDM:

- Dirección URL de condiciones de uso de MDM
- Dirección URL de descubrimiento de MDM 
- Dirección URL de cumplimiento de MDM

![Adición de una aplicación](./media/azureadjoin-plan/06.png)

Cada dirección URL tiene un valor predeterminado predefinido. Si estos campos están vacíos, póngase en contacto con su proveedor de MDM para obtener más información.

### <a name="mam-settings"></a>Configuración de MAM

MAM no es aplicable a la unión a Azure AD. 

## <a name="configure-enterprise-state-roaming"></a>Configuración de Enterprise State Roaming

Si quiere habilitar State Roaming en Azure AD para que los usuarios puedan sincronizar su configuración entre dispositivos, consulte [Habilitación de Enterprise State Roaming en Azure Active Directory](enterprise-state-roaming-enable.md). 

**Recomendación:** Habilite esta opción incluso para los dispositivos unidos a Azure AD híbrido.

## <a name="configure-conditional-access"></a>Configuración del acceso condicional

Si tiene un proveedor de MDM configurado para sus dispositivos unidos a Azure AD, el proveedor marca el dispositivo como compatible en cuanto este pasa a administración. 

![Dispositivos compatible](./media/azureadjoin-plan/46.png)

Puede usar esta implementación para [requerir dispositivos administrados para el acceso a aplicaciones en la nube mediante el acceso condicional](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Unir un nuevo dispositivo Windows 10 con Azure AD durante la primera ejecución](azuread-joined-devices-frx.md)
> [Unir el dispositivo de trabajo a la red de la organización](https://docs.microsoft.com/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
