---
title: 'Azure Active Directory Domain Services: Configuración de la delegación restringida de Kerberos | Microsoft Docs'
description: En este artículo se explica cómo habilitar la delegación restringida de kerberos en dominios administrados de Azure Active Directory Domain Services.
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: mstephen
ms.openlocfilehash: 287aea990664cf01caf83e1871acf69e48210226
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "66246170"
---
# <a name="configure-kerberos-constrained-delegation-kcd-on-a-managed-domain"></a>Configuración de la delegación restringida de Kerberos (KCD) en un dominio administrado
Muchas aplicaciones necesitan tener acceso a recursos en el contexto del usuario. Active Directory admite un mecanismo denominado "delegación de kerberos", que posibilita este caso de uso. Además, puede restringir la delegación para que solo se puedan acceder a recursos específicos en el contexto del usuario. Los dominios administrados de Azure AD Domain Services son diferentes de los tradicionales de Active Directory, puesto que se bloquean de forma más segura.

En este artículo se muestra cómo configurar la delegación restringida de Kerberos en un dominio administrado de Azure AD Domain Services.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="kerberos-constrained-delegation-kcd"></a>Delegación restringida de kerberos (KCD)
La delegación de kerberos permite a una cuenta suplantar a otra entidad de seguridad (por ejemplo, un usuario) para acceder a los recursos. Veamos el caso de una aplicación web que accede a una API web de back-end web en el contexto de un usuario. En este ejemplo, la aplicación web (que se ejecuta en el contexto de una cuenta de servicio o una cuenta de equipo o máquina) suplanta al usuario al acceder a los recursos (API web de back-end). La delegación de kerberos no es segura, ya que no limita los recursos a los que puede acceder la cuenta de suplantación en el contexto del usuario.

La delegación restringida de kerberos (KCD) restringe los servicios y recursos en los que el servidor especificado puede actuar en nombre de un usuario. La KCD tradicional requiere privilegios de administrador de dominio con el fin de configurar una cuenta de dominio para un servicio y restringe la cuenta en un solo dominio.

Asimismo, tiene algunos problemas inherentes. En sistemas operativos anteriores, si el administrador de dominio configuraba la KCD basada en cuentas para el servicio, el administrador de servicios no tenía ningún método eficaz de saber qué servicios de front-end se delegaban a los servicios de recurso que poseía. Además, cualquier servicio de front-end que podía delegar en un servicio de recursos representaba un punto de ataque potencial. Si un servidor que hospedaba un servicio de front-end estaba en peligro y configurado para delegar en servicios de recursos, estos también podrían estar en peligro.

> [!NOTE]
> En un dominio administrado de Azure AD Domain Services, no tiene privilegios de administrador de dominios. Por lo tanto, **la KCD tradicional basada en cuenta no se puede configurar en un dominio administrado**. Use la KCD basada en recursos, como se describe en este artículo. Este mecanismo también es más seguro.
>
>

## <a name="resource-based-kcd"></a>KCD basada en recursos
A partir de Windows Server 2012 y versiones posteriores, los administradores de servicios tienen la posibilidad de configurar la delegación restringida para su servicio. En este modelo, el administrador de servicios de back-end puede permitir o denegar servicios de front-end específicos mediante KCD. Este modelo se conoce como **KCD basada en recursos**.

La KCD basada en recursos se configura mediante PowerShell. Debe usar los cmdlets `Set-ADComputer` o `Set-ADUser`, dependiendo de si la cuenta de suplantación es una cuenta de equipo o una de servicio/usuario.

### <a name="configure-resource-based-kcd-for-a-computer-account-on-a-managed-domain"></a>Configuración de la KCD basada en recursos para una cuenta de equipo de un dominio administrado
Supongamos que tiene una aplicación web que se ejecuta en el equipo "contoso100-webapp.contoso100.com". Debe tener acceso al recurso (una API web que se ejecuta en "contoso100-api.contoso100.com") en el contexto de los usuarios del dominio. Este es el procedimiento para configurar la KCD basada en recursos para este escenario:

1. [Cree una UO personalizada](create-ou.md). Puede delegar los permisos para administrar esta UO personalizada para los usuarios dentro del dominio administrado.
2. Una ambas máquinas virtuales (la que ejecuta la aplicación web y la que ejecuta la API web) al dominio administrado. Cree estas cuentas de equipo dentro de la UO personalizada.
3. Ahora, configure la KCD basada en recursos mediante el siguiente comando de PowerShell:

```powershell
$ImpersonatingAccount = Get-ADComputer -Identity contoso100-webapp.contoso100.com
Set-ADComputer contoso100-api.contoso100.com -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Las cuentas de equipo de la aplicación web y la API web deben estar en una UO personalizada en la que tenga permisos para configurar LA KCD basada en recursos. No puede configurar LA KCD basada en recursos para una cuenta de equipo en el contenedor integrado "Equipos de controlador de dominio de AAD".
>

### <a name="configure-resource-based-kcd-for-a-user-account-on-a-managed-domain"></a>Configuración de la KCD basada en recursos para una cuenta de usuario de un dominio administrado
Supongamos que tiene una aplicación web que se ejecuta como una cuenta de servicio "appsvc" y debe acceder al recurso (una API web que se ejecuta como una cuenta de servicio "backendsvc") en el contexto de los usuarios del dominio. Este es el procedimiento para configurar la KCD basada en recursos para este escenario.

1. [Cree una UO personalizada](create-ou.md). Puede delegar los permisos para administrar esta UO personalizada para los usuarios dentro del dominio administrado.
2. Una la máquina virtual que ejecuta el recurso o la API web de back-end al dominio administrado. Cree su cuenta de equipo dentro de la UO personalizada.
3. Cree la cuenta de servicio (por ejemplo, "appsvc") utilizada para ejecutar la aplicación web dentro de la UO personalizada.
4. Ahora, configure la KCD basada en recursos mediante el siguiente comando de PowerShell:

```powershell
$ImpersonatingAccount = Get-ADUser -Identity appsvc
Set-ADUser backendsvc -PrincipalsAllowedToDelegateToAccount $ImpersonatingAccount
```

> [!NOTE]
> Tanto la cuenta de equipo de la API web de back-end como la cuenta de servicio deben estar en una UO personalizada en la que tenga permisos para configurar la KCD basada en recursos. No puede configurar LA KCD basada en recursos para una cuenta de equipo en el contenedor integrado "Equipos de controlador de dominio de AAD" o para cuentas de usuario en el contenedor integrado "Usuarios del controlador de dominio de AAD". Por lo tanto, no puede usar las cuentas de usuario sincronizadas de Azure AD para configurar la KCD basada en recursos.
>

## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](create-instance.md)
* [Introducción a la delegación limitada de kerberos](https://technet.microsoft.com/library/jj553400.aspx)
