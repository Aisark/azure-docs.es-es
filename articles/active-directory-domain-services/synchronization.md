---
title: 'Azure Active Directory Domain Services: Sincronización en dominios administrados | Microsoft Docs'
description: Información sobre la sincronización en dominios administrados de Azure Active Directory Domain Services
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: iainfou
ms.openlocfilehash: 475817985885cdd6023e72f20ecf35a3ca582924
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2019
ms.locfileid: "67472438"
---
# <a name="synchronization-in-an-azure-ad-domain-services-managed-domain"></a>Sincronización en dominios administrados de Azure AD Domain Services
En el siguiente diagrama se ilustra cómo funciona la sincronización en dominios administrados de Azure AD Domain Services.

![Sincronización de Azure AD Domain Services](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-your-on-premises-directory-to-your-azure-ad-tenant"></a>Sincronización del directorio local con el inquilino de Azure AD
El servicio de sincronización de Azure AD Connect se utiliza para sincronizar cuentas de usuario, pertenencias a grupos y algoritmos hash de credenciales con el inquilino de Azure AD. Los atributos de cuentas de usuario, como el UPN y el SID (identificador de seguridad) local, se sincronizan. Si utiliza Azure AD Domain Services, los hashes heredados de credenciales necesarios para la autenticación NTLM y Kerberos también se sincronizan con el inquilino de Azure AD.

En caso de que configure la opción de reescritura, los cambios que se producen en el directorio de Azure AD se vuelven a sincronizar con la instancia de Active Directory local. Por ejemplo, si cambia la contraseña mediante la administración de autoservicio de contraseña de Azure AD, la contraseña modificada se actualizará en el dominio de AD local.

> [!NOTE]
> Utilice siempre la versión más reciente de Azure AD Connect para asegurarse de contar con correcciones para todos los errores conocidos.
>
>

## <a name="synchronization-from-your-azure-ad-tenant-to-your-managed-domain"></a>Sincronización del inquilino de Azure AD con el dominio administrado
Las cuentas de usuario, las pertenencias a grupos y los algoritmos hash de credenciales se sincronizan desde el inquilino de Azure AD con el dominio administrado de Azure AD Domain Services. Este proceso de sincronización es automático; es decir, no tiene que configurar, supervisar ni administrar este proceso de sincronización. La sincronización inicial puede tardar entre unas horas hasta un par de días, según el número de objetos del directorio de Azure AD. Una vez completada la sincronización inicial, los cambios realizados en Azure AD tardarán aproximadamente entre 20 y 30 minutos en aparecer reflejados en el dominio administrado. Este intervalo de sincronización se aplica a cambios de contraseña o a cambios de atributos efectuados en Azure AD.

Dicho proceso también es, por naturaleza, unidireccional. El dominio administrado es, en gran medida, de solo lectura, excepto para las unidades organizativas personalizadas que cree. Por lo tanto, no podrá realizar cambios en los atributos de usuario, las contraseñas de usuario o las pertenencias a grupos del dominio administrado. Como resultado, no se realiza ningún proceso de sincronización inversa de los cambios realizados en el dominio administrado con el inquilino de Azure AD.

## <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>Sincronización en un entorno local de varios bosques
Muchas organizaciones tienen una infraestructura de identidad local bastante compleja que consta de varios bosques de cuentas. Azure AD Connect permite sincronizar usuarios, grupos y hashes de credenciales de entornos de varios bosques con el inquilino de Azure AD.

En cambio, el inquilino de Azure AD es un espacio de nombres plano y mucho más sencillo. Si quiere que los usuarios puedan acceder de manera confiable a las aplicaciones protegidas mediante Azure AD, resuelva todos los conflictos de UPN que haya en las cuentas de usuario de los todos los bosques. Los dominios administrados de Azure AD Domain Services guardan un gran parecido con el inquilino de Azure AD. Observará una estructura plana de unidades organizativas en el dominio administrado. Las cuentas de usuario y los grupos se almacenarán en el contenedor "Usuarios de AADDC", aunque se sincronicen desde dominios o bosques locales diferentes. Puede que haya configurado una estructura jerárquica de unidades organizativas en un dominio local. El dominio administrado seguirá teniendo una estructura plana de unidades organizativas.

## <a name="exclusions---what-isnt-synchronized-to-your-managed-domain"></a>Exclusiones: los elementos que no se sincronizan con el dominio administrado
Los siguientes objetos o atributos no se sincronizan con el inquilino de Azure AD o el dominio administrado:

* **Atributos excluidos:** gracias a Azure AD Connect, puede elegir qué atributos no se sincronizarán con el inquilino de Azure AD en el dominio local. Estos atributos excluidos no estarán disponibles en el dominio administrado.
* **Directivas de grupo:** las directivas de grupo configuradas en el dominio local no se sincronizan con el administrado.
* **Recurso compartido Sysvol:** del mismo modo, el contenido del recurso compartido Sysvol del dominio local no se sincronizará con el administrado.
* **Objetos de equipo:** los objetos de equipo unidos al dominio local no se sincronizarán con el administrado. Estos equipos no tienen una relación de confianza con el dominio administrado y pertenecen exclusivamente al local. En el dominio administrado, solo encontrará objetos de equipos que se han unido expresamente al dominio administrado.
* **Atributos SidHistory de usuarios y grupos:** los SID de los grupos y usuarios primarios del dominio local se sincronizan con el administrado. Sin embargo, los atributos SidHistory existentes de usuarios y grupos no se sincronizan desde el dominio local al administrado.
* **Estructuras de unidades organizativas:** las unidades organizativas definidas en el dominio local no se sincronizan con el administrado. Hay dos unidades organizativas integradas en el dominio administrado. De forma predeterminada, el dominio administrado tiene una estructura plana de unidades organizativas. Sin embargo, puede optar por [crear una unidad organizativa personalizada en el dominio administrado](create-ou.md).

## <a name="how-specific-attributes-are-synchronized-to-your-managed-domain"></a>Modo de sincronización de atributos específicos con el dominio administrado
En la tabla siguiente se enumeran algunos atributos comunes y se describe cómo se sincronizan con el dominio administrado.

| Atributo del dominio administrado | Origen | Notas |
|:--- |:--- |:--- |
| UPN |Atributo UPN del usuario del inquilino de Azure AD |El atributo UPN del inquilino de Azure AD se sincroniza tal cual con el dominio administrado. Por lo tanto, la forma más confiable de iniciar sesión en el dominio administrado consiste en usar el UPN. |
| SAMAccountName |El atributo MailNickname del usuario del inquilino de Azure AD o uno generado automáticamente |El atributo SAMAccountName se obtiene del atributo mailNickname del inquilino de Azure AD. Si varias cuentas de usuario tienen el mismo atributo mailNickname, SAMAccountName se genera automáticamente. Si el prefijo de UPN o el atributo mailNickname del usuario tienen más de 20 caracteres, SAMAccountName se genera automáticamente para cumplir el límite de 20 caracteres de los atributos SAMAccountName. |
| Contraseñas |Contraseña del usuario del inquilino de Azure AD |Los hashes de credenciales necesarios para la autenticación NTLM o Kerberos (también denominados "credenciales complementarias") se sincronizan desde el inquilino de Azure AD. Si el inquilino de Azure AD es un inquilino sincronizado, estas credenciales se obtienen del dominio local. |
| SID de los grupos y usuarios primarios |Generado automáticamente |El SID primario de las cuentas de usuarios o grupos se genera automáticamente en el dominio administrado. Este atributo no coincide con el SID de los grupos o usuarios primarios del objeto del dominio de AD local. Esto se debe a que el dominio administrado tiene un espacio de nombres de SID diferente al del dominio local. |
| Historial de SID de usuarios y grupos |SID de usuarios y grupos primarios locales |El atributo SidHistory de usuarios y grupos del dominio administrado se establece para que coincida con el SID de grupos o usuarios primarios correspondiente del dominio local. Gracias a esta característica, le resultará más sencillo trasladar aplicaciones locales al dominio administrado, ya que no tendrá que volver a incluir los recursos en listas ACL. |

> [!NOTE]
> **Inicie sesión en el dominio administrado usando el formato UPN:** el atributo SAMAccountName puede generarse automáticamente en algunas cuentas de usuario del dominio administrado. Si varios usuarios tienen el mismo atributo mailNickname o prefijos UPN excesivamente largos, el atributo SAMAccountName para estos usuarios puede generarse automáticamente. Por lo tanto, el formato de SAMAccountName (por ejemplo, CONTOSO100\usuarioJuan) no siempre constituye una manera confiable de iniciar sesión en el dominio. Los atributos SAMAccountName generados automáticamente de los usuarios pueden ser distintos de sus prefijos de UPN. Use el formato UPN (por ejemplo, "joeuser@contoso100.com") para iniciar sesión en el dominio administrado de forma confiable.

### <a name="attribute-mapping-for-user-accounts"></a>Asignación de atributos para cuentas de usuario
En la tabla siguiente se muestra cómo determinados atributos de objetos de usuario del inquilino de Azure AD se sincronizan con los atributos correspondientes del dominio administrado.

| Atributo de usuario del inquilino de Azure AD | Atributo de usuario del dominio administrado |
|:--- |:--- |
| accountEnabled |userAccountControl (establece o borra el bit ACCOUNT_DISABLED) |
| city |l |
| country |co |
| department |department |
| DisplayName |DisplayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |título |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (a veces, puede generarse automáticamente) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl (establece o borra el bit DONT_EXPIRE_PASSWORD) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>Asignación de atributos de grupos
En la tabla siguiente se muestra cómo determinados atributos de objetos de grupo del inquilino de Azure AD se sincronizan con los atributos correspondientes del dominio administrado.

| Atributo de grupo del inquilino de Azure AD | Atributo de grupo del dominio administrado |
|:--- |:--- |
| DisplayName |DisplayName |
| DisplayName |SAMAccountName (a veces, puede generarse automáticamente) |
| mail |mail |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="password-hash-synchronization-and-security-considerations"></a>Consideraciones de seguridad y sincronización de hash de contraseña
Al habilitar Azure AD Domain Services, su directorio de Azure AD genera y almacena varios hash de contraseña en los formatos compatibles NTLM y Kerberos. 

En el caso de las cuentas de usuario existentes en la nube, estos hash no pueden generarse automáticamente, ya que Azure AD nunca almacena las contraseñas no cifradas. Por lo tanto, Microsoft requiere que los [usuarios en la nube restablezcan o cambien sus contraseñas](active-directory-ds-getting-started-password-sync.md) para que sus hash de contraseña se puedan generar y almacenar en Azure AD. En el caso de cualquier cuenta de usuario en la nube creada en Azure AD tras habilitar Azure AD Domain Services, los hash de contraseña se generarán y almacenarán en los formatos compatibles NTLM y Kerberos. 

En el caso de las cuentas de usuario sincronizadas desde el directorio de Azure AD local mediante la sincronización de Azure AD Connect, deberá [configurar Azure AD Connect para que sincronice los hash de contraseña en los formatos compatibles NTLM y Kerberos](active-directory-ds-getting-started-password-sync-synced-tenant.md).

Los hash de contraseña en los formatos compatibles NTLM y Kerberos siempre se almacenarán de forma cifrada en Azure AD. Estos hash se cifrarán de forma que solo Azure AD Domain Services tenga acceso a las claves de descifrado. Ningún otro servicio o componente de Azure AD tendrá acceso a ellas. Las claves de cifrado serán únicas para cada inquilino de Azure AD. Azure AD Domain Services sincronizará los hash de contraseña en los controladores de dominio de su dominio administrado. Estos hash de contraseña se almacenarán y protegerán en dichos controladores de dominio de modo similar a cómo se almacenan y protegen las contraseñas en los controladores de dominio de Windows Server AD. Los discos de estos controladores de dominio administrado se cifrarán en reposo.

## <a name="objects-that-are-not-synchronized-to-your-azure-ad-tenant-from-your-managed-domain"></a>Objetos que no se sincronizan con el inquilino de Azure AD desde el dominio administrado
Tal y como se describió en la sección anterior de este artículo, no se realiza ningún proceso de sincronización en el dominio administrado con el inquilino de Azure AD. Puede optar por [crear una unidad organizativa personalizada](create-ou.md) en el dominio administrado. Además, puede crear otros usuarios, grupos, cuentas de servicio o unidades organizativas en estas unidades organizativas personalizadas. Ninguno de los objetos creados en las unidades organizativas personalizadas se vuelven a sincronizar con el inquilino de Azure AD. Estos objetos solo están disponibles para utilizarse en el dominio administrado. Por lo tanto, estos objetos no se pueden ver utilizando los cmdlets de PowerShell de Azure AD, Graph API o la interfaz de usuario de administración de Azure AD.

## <a name="related-content"></a>Contenido relacionado
* [Características de Azure AD Domain Services](active-directory-ds-features.md)
* [Escenarios de implementación de Azure AD Domain Services](scenarios.md)
* [Consideraciones de red de Azure AD Domain Services](network-considerations.md)
* [Introducción a Azure AD Domain Services](create-instance.md)
