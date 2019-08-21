---
title: 'Azure Active Directory Domain Services: Implementación de Azure AD Application Proxy | Microsoft Docs'
description: En este artículo se explica cómo usar el proxy de aplicación en dominios administrados de Azure Active Directory Domain Services.
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2019
ms.author: iainfou
ms.openlocfilehash: 35211d6f832033a2bb16c495ebab839b7f740445
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69031027"
---
# <a name="deploy-azure-ad-application-proxy-on-an-azure-ad-domain-services-managed-domain"></a>Implementación del proxy de aplicación de Azure AD en dominios administrados de Azure Active Directory Domain Services.
El proxy de aplicación de Azure Active Directory (AD) permite publicar aplicaciones locales para acceder a ellas a través de Internet y poder admitir trabajadores remotos. Con Azure AD Domain Services, ahora podrá trasladar aplicaciones heredadas que se ejecutan de forma local en los servicios de infraestructura de Azure. Después, podrá publicar estas aplicaciones mediante el proxy de aplicación de Azure AD para proporcionar acceso remoto seguro a los usuarios de su organización.

Si no está familiarizado con Azure AD Application Proxy, aprenda más sobre esta característica con el siguiente artículo: [Provisión de acceso remoto seguro a aplicaciones locales](../active-directory/manage-apps/application-proxy.md).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>Antes de empezar
Para realizar las tareas enumeradas en este artículo, necesita lo siguiente:

1. Una **suscripción de Azure**válida.
2. Un **directorio de Azure AD** : sincronizado con un directorio local o solo en la nube.
3. Se necesita una **licencia de Azure AD Premium** para usar Azure AD Application Proxy.
4. **Servicios de dominio de Azure AD** deben estar habilitado en el directorio de Azure AD. Si no lo ha hecho, siga todas las tareas descritas en [Servicios de dominio de Azure AD (vista previa): introducción](create-instance.md).

<br>

## <a name="task-1---enable-azure-ad-application-proxy-for-your-azure-ad-directory"></a>Tarea 1: Habilitar el proxy de aplicación de Azure AD en su directorio de Azure AD
Realice los pasos siguientes para habilitar el proxy de aplicación de Azure AD en su directorio Azure AD.

1. Inicie sesión como administrador en [Azure Portal](https://portal.azure.com).

2. Haga clic en **Azure Active Directory** para que aparezca la información general del directorio. Haga clic en **Aplicaciones empresariales**.

3. Haga clic en **Proxy de aplicación**.

4. Para descargar el conector, haga clic en el botón **Conector**.

5. En la página de descarga, acepte el acuerdo de privacidad y los términos de licencia, y haga clic en el botón **Descargar**.

    ![Confirmación de la descarga](./media/app-proxy/app-proxy-enabled-confirm-download.png)


## <a name="task-2---provision-domain-joined-windows-servers-to-deploy-the-azure-ad-application-proxy-connector"></a>Tarea 2: Aprovisionar los servidores de Windows unidos a un dominio para implementar el conector del proxy de aplicación de Azure AD
Debe tener máquinas virtuales Windows Server unidas a un dominio donde pueda instalar el conector del proxy de aplicación de Azure AD. En el caso de algunas aplicaciones, puede aprovisionar varios servidores en los que esté instalado el conector. Esta opción de implementación ofrece mayor disponibilidad y lo ayuda a administrar cargas de autenticación más intensas.

Aprovisione los servidores del conector en la misma red virtual (o en una red virtual conectada o emparejada), en la que ha habilitado el dominio administrado de Azure AD Domain Services. De forma similar, los servidores que hospedan las aplicaciones que publique a través del proxy de aplicación deben instalarse en la misma red virtual de Azure.

Para aprovisionar servidores del conector, siga todas las tareas descritas en el artículo [Unión de una máquina virtual Windows Server a un dominio administrado](active-directory-ds-admin-guide-join-windows-vm.md).


## <a name="task-3---install-and-register-the-azure-ad-application-proxy-connector"></a>Tarea 3: Instalar y registrar el conector del proxy de aplicación de Azure AD
Anteriormente, aprovisionó una máquina virtual Windows Server y la unió al dominio administrado. En esta tarea, instalará el conector del proxy de aplicación de Azure AD en esta máquina virtual.

1. Copie el paquete de instalación del conector en la máquina virtual donde instaló el conector del proxy de aplicación web de Azure AD.

2. Ejecute **AADApplicationProxyConnectorInstaller.exe** en la máquina virtual. Acepte los términos de licencia del software.

    ![Aceptación de los términos de instalación](./media/app-proxy/app-proxy-install-connector-terms.png)
3. Durante la instalación, se le pedirá que registre el conector en el proxy de aplicación de su directorio de Azure AD.
   * Proporcione sus **credenciales de administrador de la aplicación de Azure AD**. Su inquilino de administrador de la aplicación puede ser distinto de sus credenciales de Microsoft Azure.
   * La cuenta de administrador que se usa para registrar el conector debe pertenecer al mismo directorio donde haya habilitado el servicio de proxy de aplicación. Por ejemplo, si el dominio del inquilino es contoso.com, el administrador debe ser admin@contoso.com o cualquier otro alias válido de ese dominio.
   * Si Configuración de seguridad mejorada de IE está activado en el servidor donde va a instalar el conector, la pantalla de registro podría bloquearse. Siga las instrucciones del mensaje de error para permitir el acceso. Asegúrese de que Internet Explorer Enhanced Security está desactivado.
   * Si el registro del conector no funciona, consulte [Solucionar problemas de Proxy de aplicación](../active-directory/manage-apps/application-proxy-troubleshoot.md).

     ![Conector instalado](./media/app-proxy/app-proxy-connector-installed.png)
4. Para garantizar que el conector funcione correctamente, ejecute el Solucionador de problemas del conector del proxy de aplicación de Azure AD. Después de ejecutar el Solucionador de problemas, debería ver un informe donde se indica que está todo correcto.

    ![Mensaje del Solucionador de problemas que todo está correcto](./media/app-proxy/app-proxy-connector-troubleshooter.png)
5. Debería ver el conector recién instalado que aparece en la página del proxy de aplicación del directorio de Azure AD.

    ![En Azure Portal, el conector instalado se muestra como disponible](./media/app-proxy/app-proxy-connector-page.png)

> [!NOTE]
> Puede decidir instalar conectores en varios servidores para garantizar una alta disponibilidad durante la autenticación de las aplicaciones publicadas a través del proxy de aplicación de Azure AD. Siga los mismos pasos mencionados anteriormente para instalar el conector en otros servidores unidos al dominio administrado.
>
>

## <a name="next-steps"></a>Pasos siguientes
Ha configurado el proxy de aplicación de Azure AD y lo ha integrado con el dominio administrado de Azure AD Domain Services.

* **Migrar las aplicaciones a máquinas virtuales de Azure:** puede migrar mediante lift-and-shift sus aplicaciones de servidores locales a máquinas virtuales de Azure unidas al dominio administrado. Si lo hace, lo ayudará a evitar los costos de infraestructura de los servidores que se ejecutan en un entorno local.

* **Publicar las aplicaciones mediante Azure AD Application Proxy:** publique aplicaciones que se ejecuten en máquinas virtuales de Azure mediante Azure AD Application Proxy. Para obtener más información, consulte [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](../active-directory/manage-apps/application-proxy-publish-azure-portal.md).


## <a name="deployment-note---publish-iwa-integrated-windows-authentication-applications-using-azure-ad-application-proxy"></a>Nota de implementación: publique las aplicaciones de IWA (autenticación integrada) mediante el proxy de aplicación de Azure AD.
Habilite el inicio de sesión único en sus aplicaciones mediante la autenticación de Windows integrada (IWA) dando permiso a los conectores del proxy de aplicación para suplantar a los usuarios, y enviar y recibir tokens en su nombre. Configure la delegación restringida de Kerberos (KCD) para que el conector conceda los permisos necesarios para acceder a los recursos del dominio administrado. Para aumentar la seguridad, use el mecanismo KCD basado en recursos en los dominios administrados.


### <a name="enable-resource-based-kerberos-constrained-delegation-for-the-azure-ad-application-proxy-connector"></a>Habilitación de la delegación restringida de Kerberos basada en recursos para el conector del proxy de aplicación de Azure AD
El conector del proxy de aplicación de Azure debe configurarse para la delegación restringida de Kerberos (KCD) de modo que pueda suplantar a usuarios en el dominio administrado. En un dominio administrado de Azure AD Domain Services, no tiene privilegios de administrador de dominios. Por lo tanto, **la KCD tradicional de nivel de cuenta no se puede configurar en un dominio administrado**.

En su lugar, use la que está basada en recursos, como se describe en este [artículo](deploy-kcd.md).

> [!NOTE]
> Tendrá que ser miembro del grupo Administradores del controlador de dominio de AAD para administrar el dominio administrado con los cmdlets de PowerShell de AD.
>
>

Use el cmdlet de PowerShell Get-ADComputer para recuperar la configuración del equipo donde está instalado el conector del proxy de aplicación de Azure AD.
```powershell
$ConnectorComputerAccount = Get-ADComputer -Identity contoso100-proxy.contoso100.com
```

Después, ejecute el cmdlet Set-ADComputer para configurar la KCD basada en recursos en el servidor de recursos.
```powershell
Set-ADComputer contoso100-resource.contoso100.com -PrincipalsAllowedToDelegateToAccount $ConnectorComputerAccount
```

Si ha implementado varios conectores del proxy de aplicación en el dominio administrado, debe configurar la KCD basada en recursos en cada instancia de este tipo del conector.


## <a name="related-content"></a>Contenido relacionado
* [Introducción a Azure AD Domain Services](create-instance.md)
* [Configuración de la delegación restringida de kerberos en un dominio administrado](deploy-kcd.md)
* [Introducción a la delegación limitada de kerberos](https://technet.microsoft.com/library/jj553400.aspx)
