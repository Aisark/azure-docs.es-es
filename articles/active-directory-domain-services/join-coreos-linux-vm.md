---
title: Unión de una máquina virtual CoreOS a Azure AD Domain Services | Microsoft Docs
description: Aprenda a configurar y unir una máquina virtual CoreOS a un dominio administrado de Azure AD Domain Services.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: iainfou
ms.openlocfilehash: ddf6c9238cabedfbdeeb8056864072edc543c342
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712620"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>Unión de una máquina virtual CoreOS a un dominio administrado de Azure AD Domain Services

Para que los usuarios puedan iniciar sesión en máquinas virtuales (VM) en Azure con un único conjunto de credenciales, puede unir máquinas virtuales a un dominio administrado de Azure Active Directory Domain Services (AD DS). Al unir una máquina virtual a un dominio administrado de Azure AD DS, se pueden usar las cuentas de usuario y las credenciales del dominio para iniciar sesión y administrar servidores. También se aplican las pertenencias a grupos del dominio administrado de Azure AD DS para permitirle controlar el acceso a los archivos o servicios de la máquina virtual.

Este artículo muestra cómo unir una máquina virtual CoreOS a un dominio administrado de Azure AD DS.

## <a name="prerequisites"></a>Prerequisites

Para completar este tutorial, necesitará los siguientes recursos y privilegios:

* Una suscripción de Azure activa.
    * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
    * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Un dominio administrado de Azure Active Directory Domain Services habilitado y configurado en su inquilino de Azure AD.
    * Si es necesario, el primer tutorial [crea y configura una instancia de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una cuenta de usuario que sea miembro del grupo de *administradores de Azure AD DC* en el inquilino de Azure AD.

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>Creación y conexión a una máquina virtual CoreOS Linux

Si ya tiene una máquina virtual CoreOS Linux en Azure, conéctese a ella mediante SSH y luego continúe con el paso siguiente para [empezar a configurar la máquina virtual](#configure-the-hosts-file).

Si necesita crear una máquina virtual CoreOS Linux o desea crear una máquina virtual de prueba para usarla con este artículo, puede utilizar uno de los métodos siguientes:

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [CLI de Azure](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

Al crear la máquina virtual, preste atención a la configuración de la red virtual para asegurarse de que la máquina virtual puede comunicarse con el dominio administrado de Azure AD DS:

* Implemente la máquina virtual en la misma red virtual, o en otra emparejada, en la que haya habilitado Azure AD Domain Services.
* Implemente la máquina virtual en una subred diferente a la de la instancia de Azure AD Domain Services.

Una vez implementada la máquina virtual, siga los pasos para conectarse a la máquina virtual mediante SSH.

## <a name="configure-the-hosts-file"></a>Configuración del archivo hosts

Para asegurarse de que el nombre de host de la máquina virtual está configurado correctamente para el dominio administrado, edite el archivo */etc/hosts* y establezca el nombre de host:

```console
sudo vi /etc/hosts
```

En el archivo *hosts*, actualice la dirección *localhost*. En el ejemplo siguiente:

* *aadds.contoso.com* es el nombre de dominio DNS del dominio administrado de Azure AD DS.
* *coreos* es el nombre de host de la máquina virtual CoreOS que se va a unir al dominio administrado.

Actualice estos nombres con sus propios valores:

```console
127.0.0.1 coreos coreos.aadds.contoso.com
```

Cuando haya terminado, guarde y salga del archivo *hosts* mediante el comando `:wq` del editor.

## <a name="configure-the-sssd-service"></a>Configuración del servicio SSSD

Actualice la configuración de SSSD en */etc/sssd/sssd.conf*.

```console
sudo vi /etc/sssd/sssd.conf
```

Especifique su propio nombre de dominio administrado de Azure AD DS para los siguientes parámetros:

* *domains* con TODAS LAS LETRAS EN MAYÚSCULAS
* *[domain/AADDS]* donde AADDS está CON TODAS LAS LETRAS EN MAYÚSCULAS
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* con TODAS LAS LETRAS EN MAYÚSCULAS

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = AADDS.CONTOSO.COM

[domain/AADDS.CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://aadds.contoso.com
ldap_search_base = dc=aadds.contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = aadds.contoso.com
krb5_realm = AADDS.CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>Unión de la máquina virtual al dominio administrado

Con el archivo de configuración de SSSD actualizado, una la máquina virtual al dominio administrado.

1. En primer lugar, use el comando `adcli info` para comprobar que puede ver información sobre el dominio administrado de Azure AD DS. En el ejemplo siguiente se obtiene información para el dominio *AADDS.CONTOSO.COM*. Especifique su propio nombre de dominio administrado de Azure AD DS CON TODAS LAS LETRAS EN MAYÚSCULAS:

    ```console
    sudo adcli info AADDS.CONTOSO.COM
    ```

   Si el comando `adcli info` no se puede encontrar en el dominio administrado de Azure AD DS, repase los siguientes pasos de solución de problemas:

    * Asegúrese de que el dominio sea accesible desde la máquina virtual. Pruebe `ping aadds.contoso.com` para ver si se devuelve una respuesta positiva.
    * Compruebe que la máquina virtual se ha implementado en la misma red virtual (o en otra emparejada) en la que el dominio administrado de Azure AD DS está disponible.
    * Confirme que se ha actualizado la configuración del servidor DNS de la red virtual para que apunte a los controladores de dominio del dominio administrado de Azure AD DS.

1. Ahora, una la máquina virtual al dominio administrado de Azure AD DS con el comando `adcli join`. Especifique un usuario que pertenezca al grupo *Administradores del controlador de dominio de AAD*. Si es necesario, [agregue una cuenta de usuario a un grupo en Azure AD](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md).

    Una vez más, el nombre del dominio administrado de Azure AD DS se debe escribir CON TODAS LAS LETRAS EN MAYÚSCULAS. En el ejemplo siguiente, la cuenta denominada `contosoadmin@aadds.contoso.com` se usa para inicializar Kerberos. Escriba su propia cuenta de usuario que sea miembro del grupo *Administradores del controlador de dominio de AAD*.

    ```console
    sudo adcli join -D AADDS.CONTOSO.COM -U contosoadmin@AADDS.CONTOSO.COM -K /etc/krb5.keytab -H coreos.aadds.contoso.com -N coreos
    ```

    El comando `adcli join` no devuelve ninguna información cuando la máquina virtual se ha unido correctamente al dominio administrado de Azure AD DS.

1. Para aplicar la configuración de unión a dominio, inicie el servicio SSSD:
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>Inicio de sesión en la máquina virtual mediante una cuenta de dominio

Para comprobar que la máquina virtual se ha unido correctamente al dominio administrado de Azure AD DS, inicie una nueva conexión SSH con una cuenta de usuario de dominio. Confirme que se ha creado un directorio particular y que se ha aplicado la pertenencia a grupos del dominio.

1. Cree una nueva conexión SSH desde la consola. Use una cuenta de dominio que pertenezca al dominio administrado mediante el comando `ssh -l` como, por ejemplo, `contosoadmin@aadds.contoso.com` y, a continuación, escriba la dirección de la máquina virtual, por ejemplo: *coreos.aadds.contoso.com*. Si usa Azure Cloud Shell, use la dirección IP pública de la máquina virtual en lugar del nombre DNS interno.

    ```console
    ssh -l contosoadmin@AADDS.CONTOSO.com coreos.aadds.contoso.com
    ```

1. Ahora, compruebe que las pertenencias a grupos se están resolviendo correctamente:

    ```console
    id
    ```

    Debería ver las pertenencias a grupos del dominio administrado de Azure AD DS.

## <a name="next-steps"></a>Pasos siguientes

Si tiene problemas para conectar la máquina virtual al dominio administrado de Azure AD DS o al iniciar sesión con una cuenta de dominio, consulte [Solución de problemas de unión al dominio](join-windows-vm.md#troubleshoot-domain-join-issues).

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
