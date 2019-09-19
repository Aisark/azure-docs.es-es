---
title: Cuentas de servicio administradas de grupo para Azure AD Domain Services | Microsoft Docs
description: Obtenga información sobre cómo crear una cuenta de servicio administrada de grupo (gMSA) para su uso con los dominios administrados de Azure Active Directory Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: e6faeddd-ef9e-4e23-84d6-c9b3f7d16567
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: iainfou
ms.openlocfilehash: 1cfddf14d60b7d73bae283a18732c7c99ae22b4d
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/11/2019
ms.locfileid: "70898225"
---
# <a name="create-a-group-managed-service-account-gmsa-in-azure-ad-domain-services"></a>Cree una cuenta de servicio administrada de grupo (gMSA) en Azure AD Domain Services

Las aplicaciones y servicios suelen necesitar una identidad para autenticarse con otros recursos. Por ejemplo, es posible que un servicio web tenga que autenticarse con un servicio de base de datos. Si una aplicación o servicio tiene varias instancias, como una granja de servidores web, la creación y configuración manuales de las identidades para esos recursos llevan mucho tiempo. En su lugar, se puede crear una cuenta de servicio administrada (gMSA) en el dominio administrado de Azure Active Directory Domain Services (Azure AD DS). El SO Windows administra automáticamente las credenciales para gMSA, que simplifica la administración de grupos de recursos de gran tamaño.

En este artículo se muestra cómo crear una gMSA en un dominio administrado de Azure AD DS.

## <a name="before-you-begin"></a>Antes de empezar

Para completar este artículo, necesitará los siguientes recursos y privilegios:

* Una suscripción de Azure activa.
    * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
    * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Un dominio administrado de Azure Active Directory Domain Services habilitado y configurado en su inquilino de Azure AD.
    * Si es necesario, complete el tutorial para [crear y configurar una instancia de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una máquina virtual de administración de Windows Server que esté unida al dominio administrado de Azure AD DS.
    * Si es necesario, complete el tutorial para [crear una máquina virtual de administración][tutorial-create-management-vm].

## <a name="managed-service-accounts-overview"></a>Información general sobre las cuentas de servicio administradas

Una cuenta de servicio administrada independiente (sMSA) es una cuenta de dominio cuya contraseña se administra automáticamente. Este enfoque simplifica la administración de los nombres de entidad de seguridad de servicio (SPN), y permite delegar la administración a otros administradores. No es necesario crear y girar credenciales manualmente para la cuenta.

Una cuenta de servicio administrada de grupo (gMSA) proporciona la misma simplificación de administración, pero para varios servidores del dominio. Una gMSA permite a todas las instancias de un servicio hospedado en una granja de servidores usar la misma entidad de servicio para que los protocolos de autenticación mutua funcionen. Cuando se usa una gMSA como entidad de servicio, el sistema operativo Windows vuelve a administrar la contraseña de la cuenta en lugar de recurrir al administrador.

Para más información, consulte [Introducción a las cuentas de servicio administradas de grupo (gMSA)][gmsa-overview].

## <a name="using-service-accounts-in-azure-ad-ds"></a>Uso de cuentas de servicio en Azure AD DS

Como Microsoft bloquea y administra los dominios administrados de Azure AD DS, debe tener en cuenta algunas consideraciones al usar cuentas de servicio:

* Cree cuentas de servicio en unidades organizativas (UO) personalizadas en el dominio administrado.
    * No se puede crear una cuenta de servicio en las unidades organizativas integradas *Usuarios AADDC* ni *Equipos AADDC*.
    * En su lugar, [cree una unidad organizativa personalizada][create-custom-ou] en el dominio administrado de Azure AD DS y, después, cree las cuentas de servicio en esa unidad organizativa personalizada.
* La clave raíz de servicios de distribución de claves (KDS) se ha creado previamente.
    * La clave raíz de KDS se usa para generar y recuperar contraseñas para gMSA. En Azure AD DS, la raíz de KDS se crea automáticamente.
    * No tiene privilegios para crear otra o ver el valor predeterminado, la clave raíz de KDS.

## <a name="create-a-gmsa"></a>Crear una gMSA

En primer lugar, cree una unidad organizativa personalizada mediante el cmdlet [New-ADOrganizationalUnit][New-AdOrganizationalUnit]. Para obtener más información sobre cómo crear y administrar unidades organizativas personalizadas, consulte [Creación de una unidad organizativa en un dominio administrado de Azure AD Domain Services][create-custom-ou].

En el siguiente ejemplo se crea una unidad organizativa personalizada llamada *myNewOU* en el dominio administrado de Azure AD DS denominado *contoso.com*. Use su propia unidad organizativa y nombre de dominio administrado:

```powershell
New-ADOrganizationalUnit -Name "myNewOU" -Path "DC=contoso,DC=COM"
```

Ahora, cree una gMSA mediante el cmdlet [New-ADServiceAccount][New-ADServiceAccount]. Se definen los siguientes parámetros de ejemplo:

* **-Name** se establece en *WebFarmSvc*
* El parámetro **-Path** especifica la unidad organizativa personalizada para la gMSA creada en el paso anterior.
* Las entradas de DNS y los nombres de entidad de seguridad de servicio se establecen para *WebFarmSvc.contoso.com*
* Se permiten las entidades de seguridad en *CONTOSO-SERVER$* . Para recuperar la contraseña, use la identidad.

Especifique sus propios nombres y nombres de dominio.

```powershell
New-ADServiceAccount -Name WebFarmSvc `
    -DNSHostName WebFarmSvc.contoso.com `
    -Path "OU=MYNEWOU,DC=contoso,DC=com" `
    -KerberosEncryptionType AES128, AES256 `
    -ManagedPasswordIntervalInDays 30 `
    -ServicePrincipalNames http/WebFarmSvc.contoso.com/contoso.com, `
        http/WebFarmSvc.contoso.com/contoso, `
        http/WebFarmSvc/contoso.com, `
        http/WebFarmSvc/contoso `
    -PrincipalsAllowedToRetrieveManagedPassword CONTOSO-SERVER$
```

Ahora se pueden configurar las aplicaciones y servicios para usar la gMSA según sea necesario.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las gMSA, consulte [Introducción a las cuentas de servicio administradas de grupo][gmsa-start].

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[create-custom-ou]: create-ou.md

<!-- EXTERNAL LINKS -->
[New-ADOrganizationalUnit]: /powershell/module/addsadministration/New-AdOrganizationalUnit
[New-ADServiceAccount]: /powershell/module/addsadministration/New-AdServiceAccount
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[gmsa-start]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
