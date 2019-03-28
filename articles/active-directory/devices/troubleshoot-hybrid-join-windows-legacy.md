---
title: Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory | Microsoft Docs
description: Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory
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
ms.date: 04/23/2018
ms.author: joflore
ms.reviewer: jairoc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76177972cbd002793f5d9fc4ab8bbe6ef2121e91
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58521455"
---
# <a name="troubleshooting-hybrid-azure-active-directory-joined-down-level-devices"></a>Solución de problemas de dispositivos híbridos de nivel inferior unidos a Azure Active Directory 

Este artículo solo es aplicable a los siguientes dispositivos: 

- Windows 7 
- Windows 8.1 
- Windows Server 2008 R2 
- Windows Server 2012 
- Windows Server 2012 R2 
 

Para Windows 10 o Windows Server 2016, consulte [Solución de problemas de dispositivos híbridos de Windows 10 y Windows Server 2016 unidos a Azure Active Directory](troubleshoot-hybrid-join-windows-current.md).

En este artículo se da por supuesto que [configuró dispositivos híbridos unidos a Azure Active Directory](hybrid-azuread-join-plan.md) para que admitan los escenarios siguientes:

- Acceso condicional basado en dispositivos


En este artículo se proporcionan instrucciones sobre cómo resolver problemas potenciales.  

**Qué debería saber:** 

- Unión de Azure AD híbrido para dispositivos Windows de bajo nivel funciona de forma ligeramente diferente que en Windows 10. Muchos clientes no se dan cuenta de que necesitan AD FS (para dominios federados) o SSO de conexión directa configurado (para dominios administrados).

- Para los clientes con dominios federados, si el punto de conexión de servicio (SCP) se configuró de manera que apunte al nombre de dominio administrado (por ejemplo, contoso.onmicrosoft.com, en lugar de contoso.com), Unión a Azure AD híbrido para dispositivos Windows de bajo nivel no funcionará.

- El número máximo de dispositivos por usuario también se aplica actualmente a los dispositivos unidos a Azure AD híbrido de bajo nivel. 

- El mismo dispositivo físico aparece varias veces en Azure AD cuando varios usuarios de dominio inician sesión en los dispositivos unidos a Azure AD híbrido de bajo nivel.  Por ejemplo, si *jdoe* y *jharnett* inician sesión en un dispositivo, se crea un registro independiente (identificador de dispositivo) para cada uno de estos usuarios en la pestaña de información de **USUARIO**. 

- Puede que reciba también varias entradas para un dispositivo en la pestaña de información del usuario debido a la reinstalación del sistema operativo o a un nuevo registro manual.

- El registro inicial o unión de dispositivos se ha configurado para realizar un intento de inicio de sesión o de bloqueo/desbloqueo. Podría haber un retraso de 5 minutos desencadenado por una tarea de programador de tareas. 

- Asegúrese de que [KB4284842](https://support.microsoft.com/help/4284842) está instalado, en el caso de Windows 7 SP1 o Windows Server 2008 R2 SP1. Esta actualización evita errores de autenticación futuros debido a la pérdida de acceso del cliente en el caso de claves protegidas después de cambiar la contraseña.

## <a name="step-1-retrieve-the-registration-status"></a>Paso 1: Recuperación del estado del registro 

**Para verificar el estado del registro:**  

1. Inicie sesión con la cuenta de usuario que usó para crear una combinación híbrida de Azure AD.

2. Apertura del símbolo del sistema del comando 

3. Escriba `"%programFiles%\Microsoft Workplace Join\autoworkplace.exe" /i`

Este comando muestra un cuadro de diálogo que proporciona detalles sobre el estado de la unión.

![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/01.png)


## <a name="step-2-evaluate-the-hybrid-azure-ad-join-status"></a>Paso 2: Evaluación del estado de Unión a Azure AD híbrido 

Si el dispositivo no estaba unido a Azure AD híbrido, puede intentar unirlo haciendo clic en el botón "Unirse". Si se produce un error al intentar realizar la unión a Azure AD híbrido, se mostrarán los detalles del error.


**Los problemas más comunes son:**

- Una configuración incorrecta de AD FS o Azure AD o problemas de red

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/02.png)
    
  - Autoworkplace.exe no puede autenticarse de forma silenciosa con Azure AD o AD FS. Esto puede deberse a la falta o mala configuración de AD FS (para dominios federados) o a la falta o mala configuración del inicio de sesión único de conexión directa de Azure AD (para dominios administrados) o a problemas de red. 
    
    - Podría ser que la autenticación de multifactor (MFA) esté habilitada o configurada para el usuario, y WIAORMUTLIAUTHN no esté configurado en el servidor de AD FS. 
     
    - Otra posibilidad es que la página de detección de dominio de inicio (HRD) esté esperando a la interacción del usuario, lo que impide que **autoworkplace.exe** solicite de forma silenciosa un token.
     
    - Puede ser que las direcciones URL de AD FS y Azure AD falten en la zona de intranet de Internet Explorer en el cliente.
     
    - Los problemas de conectividad de red pueden estar impidiendo que **autoworkplace.exe** llegue a AD FS o a las direcciones URL de Azure AD. 
     
    - **Autoworkplace.exe** requiere que el cliente tenga la línea de visión directa desde el cliente a local la organización controlador de dominio de AD, lo que significa que esa combinación de Azure AD híbrido se realiza correctamente solo cuando el cliente está conectado a la intranet de la organización.
     
    - Su organización usa sin problemas el inicio de sesión único de Azure AD, `https://autologon.microsoftazuread-sso.com` o `https://aadg.windows.net.nsatc.net` no está presente en la configuración de Intranet de IE del dispositivo y la opción **Allow updates to status bar via script** (Permitir actualizaciones en la barra de estado mediante el script) no está habilitada en la zona de la Intranet.

- No está registrado como un usuario de dominio

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/03.png)
    
    Existen motivos diferentes por los que esto puede ocurrir:
    
    - El usuario con la sesión iniciada no es un usuario del dominio (por ejemplo, un usuario local). La combinación híbrida de Azure AD en dispositivos de nivel inferior solo se admite para los usuarios del dominio.
    
    - El cliente no puede conectarse a un controlador de dominio.    

- Se ha alcanzado una cuota

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/04.png)

- El servicio no responde 

    ![Workplace Join for Windows](./media/troubleshoot-hybrid-join-windows-legacy/05.png)

Dicha información acerca del estado también se puede encontrar en el registro de eventos, en: **Applications and Services Log\Microsoft-Workplace Join**
  
**Las causas más comunes para una unión a Azure AD híbrido con error son:** 

- El equipo no está conectado a la red interna de la organización, ni a una VPN con conexión al controlador de dominio de AD local.

- Ha iniciado sesión en el equipo con una cuenta del equipo local. 

- Problemas de configuración de servicio: 

  - El servidor de AD FS no se ha configurado para admitir **WIAORMULTIAUTHN**. 

  - El bosque del equipo no tiene un objeto de punto de conexión de servicio que haga referencia a su nombre de dominio comprobado en Azure AD. 
  
  - O bien, si el dominio es administrado, el SSO de conexión directa no está configurado o no funciona.

  - Un usuario ha alcanzado el límite de dispositivos. 

## <a name="next-steps"></a>Pasos siguientes

Si tiene preguntas, consulte las [preguntas más frecuentes sobre la administración de dispositivos](faq.md).  
