---
title: Vulnerabilidades detectadas por Azure Active Directory Identity Protection | Microsoft Docs
description: Información general de las vulnerabilidades detectadas por Azure Active Directory Identity Protection.
services: active-directory
keywords: azure active directory identity protection, detección en la nube, administración de aplicaciones, seguridad, riesgo, nivel de riesgo, punto vulnerable, directiva de seguridad
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: daveba
ms.assetid: 92233a5b-cb34-4d28-88cc-d5d29c0f3256
ms.service: active-directory
ms.subservice: identity-protection
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: joflore
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fec9693641ff5918f622ecceee3fb94828b508e
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517902"
---
# <a name="vulnerabilities-detected-by-azure-active-directory-identity-protection"></a>Vulnerabilidades detectadas por Azure Active Directory Identity Protection
Los puntos vulnerables son puntos débiles de su entorno que pueden ser aprovechados por un atacante. Se recomienda solucionar estas vulnerabilidades para mejorar la posición de seguridad de su organización y evitar que los atacantes se aprovechen de ellas.


![vulnerabilidades](./media/vulnerabilities/101.png "vulnerabilidades")



En las siguientes secciones se ofrece información general de las vulnerabilidades de las que informa Identity Protection.

## <a name="multi-factor-authentication-registration-not-configured"></a>Registro de la autenticación multifactor no configurado
Este punto vulnerable ayuda a controlar la implementación de Azure Multi-Factor Authentication en su organización. 

Azure Multi-Factor Authentication proporciona una segunda capa de seguridad a la autenticación de usuario. Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones de verificación sencillas, como las llamadas telefónicas, los mensajes de texto, las notificaciones de aplicaciones móviles, los códigos de verificación y los tokens OATH de terceros.

Se recomienda pedir Azure Multi-Factor Authentication para los inicios de sesión de usuario. La autenticación multifactor desempeña un papel clave en las directivas de acceso condicional basadas en riesgos disponibles mediante Identity Protection.

Para más información, consulte [¿Qué es Azure Multi-Factor Authentication?](../authentication/multi-factor-authentication.md)

## <a name="unmanaged-cloud-apps"></a>Aplicaciones en la nube no administradas
Este punto vulnerable ayuda a identificar aplicaciones en la nube no administradas en su organización.

En las empresas modernas, los departamentos de TI a menudo no son conscientes de todas las aplicaciones en la nube que usan los miembros de su organización para realizar su trabajo. Es fácil entender por qué a los administradores les podría preocupar el acceso no autorizado a datos corporativos, la posible pérdida de datos y otros riesgos relacionados con la seguridad. 

Le recomendamos que implemente Cloud Discovery para detectar aplicaciones en la nube no administradas, así como administrar estas aplicaciones con Azure Active Directory.

Para más información, consulte [Cloud Discovery](/cloud-app-security/set-up-cloud-discovery).

## <a name="security-alerts-from-privileged-identity-management"></a>Alertas de seguridad de administración de identidades con privilegios
Este punto vulnerable ayuda a detectar y resolver alertas acerca de las identidades con privilegios en su organización.  

Para que los usuarios puedan realizar operaciones con privilegios, las organizaciones tienen que proporcionar a sus usuarios acceso con privilegios temporal o permanente en Azure AD, recursos de Azure u Office 365, y otras aplicaciones de SaaS. Cada uno de estos usuarios con privilegios aumenta la superficie de ataque de su organización. Este punto vulnerable ayuda a identificar a los usuarios con acceso con privilegios innecesarios y realizar la acción apropiada para reducir o eliminar el riesgo que suponen. 

Recomendamos que la organización utilice Privileged Identity Management de Azure AD para administrar, controlar y supervisar las identidades con privilegios y su acceso a recursos en Azure AD y en otros servicios en línea de Microsoft, como Office 365 o Microsoft Intune.

Para obtener más información, vea [Azure AD Privileged Identity Management](../privileged-identity-management/pim-configure.md). 

## <a name="see-also"></a>Vea también

[Azure Active Directory Identity Protection](../active-directory-identityprotection.md)

