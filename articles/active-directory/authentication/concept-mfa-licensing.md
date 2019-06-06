---
title: 'Versiones de Azure MFA y planes de consumo: Azure Active Directory'
description: Información sobre el cliente de Multi-Factor Authentication y los distintos métodos y versiones disponibles.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a1ee55dd3aebca869da47bbc994f546aa4fe528
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496763"
---
# <a name="how-to-get-azure-multi-factor-authentication"></a>Cómo conseguir Azure Multi-Factor Authentication

En lo referente a la protección de las cuentas, la comprobación en dos pasos debe ser la norma en cualquier organización. Esta característica resulta especialmente importante en las cuentas que tienen acceso con privilegios a los recursos. Por eso, Microsoft ofrece a los administradores de Office 365 y Azure Active Directory (Azure AD) características básicas de verificación en dos pasos sin cargo adicional. Si quiere actualizar las características para los administradores o extender la verificación en dos pasos al resto de los usuarios, puede comprar Azure Multi-Factor Authentication de varias maneras.

> [!IMPORTANT]
> Este artículo pretende ser una guía para ayudarle a entender las diferentes maneras de comprar Azure Multi-Factor Authentication. Para información específica sobre precios y facturación, consulte siempre la [página de precios de Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).
>

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versiones disponibles de Azure Multi-Factor Authentication

En la tabla siguiente se describe las diferencias entre las tres versiones de autenticación multifactor:

| Version | DESCRIPCIÓN |
| --- | --- |
| Multi-Factor Authentication para Office 365 <br> Microsoft 365 Empresa | Esta versión se administra desde el portal de Office 365 o Microsoft 365. Los administradores pueden [proteger los recursos de Office 365 con la comprobación en dos pasos](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6). Esta versión es parte de una suscripción a Office 365 o a Microsoft 365 Empresa. |
| Multi-Factor Authentication para administradores de Azure AD | Los usuarios asignados al rol Administrador global de Azure AD en inquilinos de Azure AD pueden habilitar la verificación en dos pasos sin costo adicional alguno. |
| Azure Multi-Factor Authentication | Conocida con frecuencia como la versión "completa", Azure Multi-Factor Authentication ofrece el conjunto más rico de funcionalidades. Proporciona opciones de configuración adicionales a través de [Azure Portal](https://portal.azure.com), funcionalidades de generación de informes avanzadas y compatibilidad con una amplia variedad de aplicaciones locales y en la nube. Azure Multi-factor Authentication es una característica de [Azure Active Directory Premium](https://www.microsoft.com/cloud-platform/azure-active-directory-features). |

> [!NOTE]
> A partir del 1 de septiembre de 2018, los nuevos clientes ya no pueden comprar Azure Multi-factor Authentication como oferta independiente. La autenticación multifactor seguirá estando disponible como una característica en las licencias de Azure AD Premium.

## <a name="feature-comparison-of-versions"></a>Comparación de características de las versiones

En la tabla siguiente se proporciona una lista de las características que están disponibles en las distintas versiones de Azure Multi-Factor Authentication.

> [!NOTE]
> En esta tabla comparativa, se describen las características incluidas en cada versión de Multi-Factor Authentication. En caso de que tenga el servicio completo de Azure Multi-Factor Authentication, es posible que algunas características no estén disponibles en función de si usa [MFA en la nube o MFA en local](concept-mfa-whichversion.md).
>

| Característica | Multi-Factor Authentication para Office 365 | Multi-Factor Authentication para administradores de Azure AD | Azure Multi-Factor Authentication |
| --- |:---:|:---:|:---:|
| Protección de cuentas de administración de Azure AD con MFA |● |● (Solo cuentas de administrador global de Azure AD) |● |
| Aplicación móvil como segundo factor |● |● |● |
| Llamada de teléfono como segundo factor |● |● |● |
| SMS como segundo factor |● |● |● |
| Contraseñas de aplicación para clientes que no admiten MFA |● |● |● |
| Control administrativo sobre métodos de comprobación |● |● |● |
| Protección de cuentas no de administrador con MFA |● | |● |
| Modo de PIN | | |● |
| Alerta de fraude | | |● |
| Informes de MFA | | |● |
| Omisión por única vez | | |● |
| Saludos personalizados para las llamadas de teléfono | | |● |
| Identificador de llamada personalizado para llamadas telefónicas | | |● |
| IP de confianza | | |● |
| Recordar MFA para dispositivos de confianza |● |● |● |
| MFA para aplicaciones locales | | |● |

> [!IMPORTANT]
> A partir de marzo de 2019 las opciones de llamada de teléfono no estará disponible para los usuarios MFA y SSPR en inquilinos de Azure AD gratuito de prueba. Mensajes SMS no se ven afectados por este cambio. Llamada de teléfono seguirá estando disponible para los usuarios de inquilinos de Azure AD de pago. Este cambio solo afecta a los inquilinos de Azure AD gratuito de prueba.

## <a name="how-to-turn-on-azure-multi-factor-authentication-for-azure-ad-administrators"></a>Activación de Azure Multi-Factor Authentication para administradores de Azure AD

Los usuarios asignados al rol Administrador global en inquilinos de Azure AD pueden habilitar la verificación en dos pasos para sus cuentas de Administrador global de Azure AD sin costo adicional alguno. Si utiliza una cuenta de Microsoft, puede registrarse para la autenticación multifactor siguiendo las instrucciones recogidas en el artículo de soporte técnico sobre cuentas de Microsoft, [Acerca de la verificación en dos pasos](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification). Si no utiliza una cuenta de Microsoft, active la autenticación multifactor para los administradores globales siguiendo las instrucciones recogidas en el artículo [How to require two-step verification for a user or group](howto-mfa-userstates.md) (Exigencia de verificación en dos pasos para un usuario o grupo).

## <a name="how-to-purchase-azure-multi-factor-authentication"></a>Adquisición de Azure Multi-Factor Authentication

Compre licencias que incluyan Azure Multi-factor Authentication, como Azure Active Directory Premium, o un paquete de licencia que incluye Azure AD Premium o acceso condicional y asígnelas a los usuarios en Azure Active Directory.

### <a name="consumption-based-licensing"></a>Las licencias basadas en el consumo

Licencias basadas en el consumo ya no está disponible para nuevos clientes efectivos el 1 de septiembre de 2018.

Ya no se pueden crear eficaces proveedores de autenticación de nuevo el 1 de septiembre de 2018. Los proveedores de autenticación existentes se pueden seguir usando y actualizando. La autenticación multifactor seguirá siendo una característica disponible en las licencias de Azure AD Premium.

Si se utiliza un proveedor de Azure Multi-Factor Authentication, hay dos modelos de uso disponibles que se facturan a través de la suscripción a Azure:

1. **Por usuario habilitado**: para empresas que quieren habilitar la comprobación en dos pasos para un número fijo de empleados que con frecuencia necesitan autenticación. La facturación por usuario se basa en el número de usuarios habilitados para MFA en el inquilino de Azure AD y en la instancia de Servidor Azure MFA. Si los usuarios están habilitados para MFA en Azure AD y en el Servidor Azure MFA y se habilita la sincronización de dominios (Azure AD Connect), contamos el conjunto más grande de usuarios. Si la sincronización de dominios no está habilitada, contamos la suma de todos los usuarios habilitados para MFA en Azure AD y el Servidor Azure MFA. La facturación se prorratea y se notifica al sistema de comercio diariamente.

   > [!NOTE]
   > Ejemplo de facturación 1: hoy tiene 5000 usuarios habilitados para MFA. El sistema MFA divide ese número entre 31 y notifica 161,29 usuarios ese día. Mañana habilita 15 usuarios más, así que el sistema MFA notifica 161,77 usuarios ese día. Al final del ciclo de facturación, el número total de usuarios que se facturan con su suscripción de Azure asciende aproximadamente a 5000.
   >
   > Ejemplo de facturación 2: tiene una mezcla de usuarios con licencia y sin licencia, por lo que tiene un proveedor de Azure MFA por usuario para compensar la diferencia. Hay 4500 licencias de Enterprise Mobility + Security en el inquilino, pero 5000 usuarios habilitados para MFA. En su suscripción de Azure se facturan 500 usuarios, y se prorratean y notifican diariamente como 16,13 usuarios.
   >

1. **Por autenticación**: para empresas que quieren habilitar la comprobación en dos pasos para un número mayor de usuarios que no suelen necesitar frecuentemente autenticación. La facturación se basa en el número de solicitudes de verificación en dos pasos recibidas, con independencia de si esas verificaciones se han aceptado o denegado. Esta facturación aparece en el extracto de uso de Azure en paquetes de diez autenticaciones y se notifica diariamente.

   > [!NOTE]
   > Ejemplo de facturación 3: Hoy, el servicio Azure MFA recibió 3105 solicitudes de comprobación en dos pasos. En su suscripción de Azure se facturan 310,5 paquetes autenticación.
   >

Es importante tener en cuenta que aunque tenga licencias, se le puede seguir facturando según la configuración basada en el consumo. Si configura un proveedor de Azure MFA por autenticación, se le factura cada solicitud de comprobación en dos pasos, aunque las hayan realizado usuarios con licencia. Si ha configurado un proveedor de Azure MFA por usuario en un dominio que no está vinculado a su inquilino de Azure AD, se le facturará por usuario habilitado, incluso si los usuarios tienen licencias en Azure AD.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los precios, consulte [Precios de Azure MFA](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

- Elija si va a implementar Azure MFA [en la nube o de forma local](concept-mfa-whichversion.md).
