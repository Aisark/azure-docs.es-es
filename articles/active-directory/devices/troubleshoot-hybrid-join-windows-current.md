---
title: Solución de problemas de dispositivos híbridos de Windows 10 y Windows Server 2016 unidos a Azure Active Directory | Microsoft Docs
description: Solución de problemas de dispositivos híbridos de Windows 10 y Windows Server 2016 unidos a Azure Active Directory
services: active-directory
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: cdc25576-37f2-4afb-a786-f59ba4c284c2
ms.service: active-directory
ms.subservice: devices
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2017
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: dcb7dc356c8101c1b0907818b45618ef6372c691
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60250892"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-windows-10-and-windows-server-2016-devices"></a>Solución de problemas de dispositivos híbridos de Windows 10 y Windows Server 2016 unidos a Azure Active Directory 

Este artículo es aplicable a los clientes siguientes:

-   Windows 10
-   Windows Server 2016

Para otros clientes Windows, consulte [Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory](troubleshoot-hybrid-join-windows-legacy.md).

En este artículo se da por supuesto que [configuró dispositivos híbridos unidos a Azure Active Directory](hybrid-azuread-join-plan.md) para que admitan los escenarios siguientes:

- Acceso condicional basado en dispositivos

- [Perfiles móviles de empresa](../active-directory-windows-enterprise-state-roaming-overview.md)

- [Windows Hello para empresas](../active-directory-azureadjoin-passport-deployment.md)


En este documento se proporcionan instrucciones sobre cómo resolver problemas potenciales. 


Para Windows 10 y Windows Server 2016, la unión híbrida a Azure Active Directory es compatible con la actualización de noviembre de 2015 de Windows 10 y versiones posteriores. Se recomienda utilizar la actualización de aniversario.

## <a name="step-1-retrieve-the-join-status"></a>Paso 1: Recuperación del estado de unión 

**Paso 1: Recuperación del estado de unión:**

1. Abra el símbolo del sistema como administrador.

2. Escriba **dsregcmd /status**



```
+----------------------------------------------------------------------+
| Device State                                                         |
+----------------------------------------------------------------------+

    AzureAdJoined: YES
 EnterpriseJoined: NO
         DeviceId: 5820fbe9-60c8-43b0-bb11-44aee233e4e7
       Thumbprint: B753A6679CE720451921302CA873794D94C6204A
   KeyContainerId: bae6a60b-1d2f-4d2a-a298-33385f6d05e9
      KeyProvider: Microsoft Platform Crypto Provider
     TpmProtected: YES
     KeySignTest: : MUST Run elevated to test.
              Idp: login.windows.net
         TenantId: 72b988bf-86f1-41af-91ab-2d7cd011db47
       TenantName: Contoso
      AuthCodeUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/authorize
   AccessTokenUrl: https://login.microsoftonline.com/msitsupp.microsoft.com/oauth2/token
           MdmUrl: https://enrollment.manage-beta.microsoft.com/EnrollmentServer/Discovery.svc
        MdmTouUrl: https://portal.manage-beta.microsoft.com/TermsOfUse.aspx
  dmComplianceUrl: https://portal.manage-beta.microsoft.com/?portalAction=Compliance
      SettingsUrl: eyJVcmlzIjpbImh0dHBzOi8va2FpbGFuaS5vbmUubWljcm9zb2Z0LmNvbS8iLCJodHRwczovL2thaWxhbmkxLm9uZS5taWNyb3NvZnQuY29tLyJdfQ==
   JoinSrvVersion: 1.0
       JoinSrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/device/
        JoinSrvId: urn:ms-drs:enterpriseregistration.windows.net
    KeySrvVersion: 1.0
        KeySrvUrl: https://enterpriseregistration.windows.net/EnrollmentServer/key/
         KeySrvId: urn:ms-drs:enterpriseregistration.windows.net
     DomainJoined: YES
       DomainName: CONTOSO

+----------------------------------------------------------------------+
| User State                                                           |
+----------------------------------------------------------------------+

             NgcSet: YES
           NgcKeyId: {C7A9AEDC-780E-4FDA-B200-1AE15561A46B}
    WorkplaceJoined: NO
      WamDefaultSet: YES
WamDefaultAuthority: organizations
       WamDefaultId: https://login.microsoft.com
     WamDefaultGUID: {B16898C6-A148-4967-9171-64D755DA8520} (AzureAd)
         AzureAdPrt: YES
```



## <a name="step-2-evaluate-the-join-status"></a>Paso 2: Evaluación del estado de unión 

Revise los siguientes campos y asegúrese de que tengan los valores esperados:

### <a name="azureadjoined--yes"></a>AzureAdJoined : SÍ  

En este campo se indica si el dispositivo está unido a Azure AD. Si el valor es **NO**, aún no ha finalizado la unión a Azure AD. 

**Causas posibles:**

- Error de autenticación del equipo para una unión.

- Hay un servidor proxy HTTP en la organización que el equipo no puede detectar.

- El equipo no puede llegar a Azure AD para la autenticación o a DRS de Azure para el registro.

- Es posible que el equipo no esté en la red interna de la organización o en una VPN con conexión directa a una implementación local del controlador de dominio de AD.

- Si el equipo tiene un TPM, su estado puede ser incorrecto.

- Puede haber un error de configuración en los servicios indicados en el documento anterior que necesita volver a verificar. Los ejemplos comunes son:

    - El servidor de federación no tiene habilitados los puntos de conexión de WS-Trust.

    - Es posible que el servidor de federación no permita la autenticación de entrada de equipos de la red mediante la autenticación integrada de Windows.

    - No hay ningún objeto de punto de conexión de servicio que haga referencia a su nombre de dominio comprobado en Azure AD en el bosque de AD al que pertenece el equipo.

---

### <a name="domainjoined--yes"></a>DomainJoined: SÍ  

Este campo indica si el dispositivo está unido o no a una implementación local de Active Directory. Si el valor es **NO**, el dispositivo no puede realizar una unión a Azure AD híbrido.  

---

### <a name="workplacejoined--no"></a>WorkplaceJoined: NO  

Este campo indica si el dispositivo está registrado con Azure AD, pero como un dispositivo personal (marcado como *Unido al área de trabajo*). Este valor debe ser **NO** para un equipo unido a un dominio que esté también unido a Azure AD híbrido. Si el valor es **SÍ**, se agrega una cuenta profesional o educativa antes de la finalización de la unión a Azure AD híbrido. En este caso, la cuenta se omite cuando se usa la versión de actualización de aniversario de Windows 10 (1607).

---

### <a name="wamdefaultset--yes-and-azureadprt--yes"></a>WamDefaultSet: YES and AzureADPrt: SÍ
  
Estos campos indican que el usuario se ha autenticado correctamente en Azure AD al iniciar sesión en el dispositivo. Si los valores son **NO**, podría ser debido:

- Clave de almacenamiento incorrecta (STK) en TPM asociada con el dispositivo tras el registro (comprobar KeySignTest mientras se ejecuta con privilegios elevados).

- Id. de inicio de sesión alternativo

- No se ha encontrado el proxy HTTP

## <a name="next-steps"></a>Pasos siguientes

Si tiene preguntas, consulte las [preguntas más frecuentes sobre la administración de dispositivos](faq.md). 
