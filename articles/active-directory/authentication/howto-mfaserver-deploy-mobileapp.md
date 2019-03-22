---
title: Servicio de Web de aplicación móvil de Azure MFA Server - Azure Active Directory
description: Configure el servidor MFA para enviar notificaciones de inserción a los usuarios con la aplicación Microsoft Authenticator.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ac59b68f1ddda695fba8f1a4ceacb90bfa4102a2
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58313709"
---
# <a name="enable-mobile-app-authentication-with-azure-multi-factor-authentication-server"></a>Habilitación de la autenticación de aplicación móvil con Servidor Azure Multi-Factor Authentication

La aplicación Microsoft Authenticator ofrece una opción de comprobación fuera de banda adicional. En lugar de realizar una llamada de teléfono automatizada o SMS al usuario durante el inicio de sesión, Azure Multi-Factor Authentication inserta una notificación en la aplicación Microsoft Authenticator en el smartphone o en una tableta del usuario. El usuario simplemente toca **Comprobar** (o escribe un PIN y toca "Autenticar") en la aplicación para completar el inicio de sesión.

Se recomienda usar una aplicación móvil para la verificación en dos pasos cuando la recepción telefónica es poco confiable. Si usa la aplicación como generador del token OATH, no se requiere ninguna conexión a Internet o una red.

> [!IMPORTANT]
> Si ha instalado el Servidor Azure Multi-Factor Authentication v8.x o posterior, la mayoría de los pasos a continuación no son necesarios. La autenticación de la aplicación móvil puede configurarse siguiendo los pasos descritos en [Configuración de la aplicación móvil](#configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server).

## <a name="requirements"></a>Requisitos

Para usar la aplicación Microsoft Authenticator, debe ejecutar el Servidor Azure Multi-Factor Authentication v8.x o posterior

## <a name="configure-the-mobile-app-settings-in-the-azure-multi-factor-authentication-server"></a>Establecimiento de la configuración del servicio web de aplicación móvil en el Servidor Azure Multi-Factor Authentication

1. En la consola del Servidor Multi-Factor Authentication, haga clic en el icono Portal de usuarios. Si los usuarios pueden controlar sus métodos de autenticación, active **Aplicación móvil** en la pestaña Configuración, en **Permitir a los usuarios seleccionar el método**. Sin esta característica habilitada, los usuarios finales deberán ponerse en contacto con el departamento de soporte técnico para completar la activación de la aplicación móvil.
2. Active la casilla **Permitir a los usuarios activar la aplicación móvil**.
3. Active la casilla **Permitir inscripción de usuario**.
4. Haga clic en el icono de la **aplicación móvil**.
5. Rellene el campo **Nombre de cuenta** con el nombre de la empresa u organización que aparecerá en la aplicación móvil para esta cuenta.
   ![Valores de la aplicación móvil para la configuración del servidor MFA](./media/howto-mfaserver-deploy-mobileapp/mobile.png)

## <a name="next-steps"></a>Pasos siguientes

- [Escenarios avanzados con Azure Multi-Factor Authentication y VPN de terceros](howto-mfaserver-nps-vpn.md).
