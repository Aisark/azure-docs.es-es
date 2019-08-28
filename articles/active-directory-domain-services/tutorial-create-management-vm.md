---
title: 'Tutorial: Creación de una máquina virtual de administración para Azure Active Directory Domain Services | Microsoft Docs'
description: En este tutorial, aprenderá a crear y configurar una máquina virtual Windows que usará para administrar Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e7c3ccb553010b84a30ccdad875ea0362112d830
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69618795"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>Tutorial: Creación de una máquina virtual de administración para configurar y administrar un dominio administrado de Azure Active Directory Domain Services

Azure Active Directory Domain Services (AD DS) proporciona servicios de dominio administrados como, por ejemplo, unión a un dominio, directiva de grupo, LDAP o autenticación Kerberos/NTLM, que son totalmente compatibles con Windows Server Active Directory. Este dominio administrado se administra con las mismas Herramientas de administración remota del servidor (RSAT) que con un dominio de Active Directory Domain Services en el entorno local. Como Azure AD DS es un servicio administrado, hay algunas tareas administrativas que no se pueden realizar, como usar el protocolo de escritorio remoto (RDP) para conectarse a los controladores de dominio.

En este tutorial se muestra cómo crear una máquina virtual de Windows Server en Azure e instalar las herramientas necesarias para administrar un dominio administrado de Azure AD DS.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Usar las tareas administrativas disponibles en un dominio administrado de Azure AD DS
> * Instalación de las herramientas administrativas de Azure Active Directory en una máquina virtual de Windows Server
> * Uso del Centro de administración de Active Directory para realizar tareas comunes

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará los siguientes recursos y privilegios:

* Una suscripción de Azure activa.
    * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
    * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Un dominio administrado de Azure Active Directory Domain Services habilitado y configurado en su inquilino de Azure AD.
    * Si es necesario, el primer tutorial [crea y configura una instancia de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una máquina virtual Windows Server que esté unida al dominio administrado con Azure AD DS.
    * Si es necesario, el tutorial anterior [crea una máquina virtual Windows Server y la une a un dominio administrado][create-join-windows-vm].
* Una cuenta de usuario que sea miembro del grupo de *administradores de Azure AD DC* en el inquilino de Azure AD.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

En este tutorial creará y configurará una máquina virtual de administración mediante Azure Portal. Para empezar a trabajar, primero inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Tareas administrativas disponibles en Azure AD DS

Azure AD DS proporciona un dominio administrado para que lo consuman los usuarios, las aplicaciones y los servicios. Este enfoque cambia algunas de las tareas de administración disponibles que puede realizar y los privilegios que tiene dentro del dominio administrado. Estas tareas y permisos pueden ser diferentes de lo que experimenta con un entorno de Active Directory Domain Services en el entorno local normal. No puede conectarse a los controladores de dominio en el dominio administrado de Azure AD DS mediante Escritorio remoto.

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Tareas administrativas que puede realizar en un dominio administrado de Azure AD DS

A los miembros del grupo *Administradores del controlador de dominio de AAD* se les conceden privilegios en el dominio administrado de Azure AD DS que les permiten realizar tareas como:

* Unir máquinas al dominio administrado.
* Configurar el objeto de directiva de grupo (GPO) integrado para los contenedores *Equipos de AADDC* y *Usuarios de AADDC* en el dominio administrado.
* Administrar DNS en el dominio administrado.
* Crear y administrar unidades organizativas (OU) personalizadas en el dominio administrado.
* Obtener acceso administrativo a los equipos unidos al dominio administrado.

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Privilegios de administrador que no tiene en un dominio administrado de Azure AD DS

El dominio administrado de Azure AD DS se bloquea y el usuario no tiene privilegios para realizar ciertas tareas administrativas en él. Algunos de los ejemplos siguientes son tareas que no puede realizar:

* Extender el esquema del dominio administrado.
* Conectarse a los controladores de dominio para el dominio administrado mediante Escritorio remoto.
* Agregar controladores de dominio al dominio administrado.
* No tiene privilegios de *administrador de dominio* ni de *administrador de empresa* para el dominio administrado.

## <a name="sign-in-to-the-windows-server-vm"></a>Inicio de sesión en la máquina virtual de Windows Server

En el tutorial anterior, se creó una máquina virtual Windows Server y se unió al dominio administrado de Azure AD DS. Vamos a usar esa máquina virtual para instalar las herramientas de administración. Si es necesario, [siga los pasos del tutorial para crear una máquina virtual Windows Server y unirla a un dominio administrado][create-join-windows-vm].

> [!NOTE]
> En este tutorial, se usa una máquina virtual Windows Server en Azure que esté unida al dominio administrado con Azure AD DS. También puede usar un cliente de Windows, como Windows 10, que esté unido al dominio administrado.
>
> Para más información sobre cómo instalar las herramientas administrativas en un cliente Windows, consulte cómo [se instalan las herramientas de administración remota del servidor (RSAT)](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx).

Para empezar, conéctese a la máquina virtual de Windows Server tal y como se muestra a continuación:

1. En Azure Portal, seleccione **Grupos de recursos** en el lateral izquierdo. Elija el grupo de recursos donde se creó la máquina virtual, por ejemplo, *miGrupoDeRecursos*, y luego seleccione la máquina virtual, por ejemplo, *miVM*.
1. En las ventanas **Información general** de la máquina virtual, seleccione **Conectar**.

    ![Conexión a una máquina virtual de Windows en Azure Portal](./media/tutorial-create-management-vm/connect-vm.png)

    También puede [crear y usar un host de Azure Bastion (actualmente en versión preliminar)][azure-bastion] para permitir el acceso solo mediante Azure Portal a través de SSL.

1. Seleccione la opción *Descargar archivo RDP*. Guarde este archivo RDP en el explorador Web.
1. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.
1. Escriba las credenciales de un usuario que forme parte del *grupo de administradores de Azure AD DC*, por ejemplo, *contoso\dee*
1. Si ve una advertencia de certificado durante el proceso de inicio de sesión, seleccione **Sí** o **Continuar** para conectarse.

## <a name="install-active-directory-administrative-tools"></a>Instalación de las herramientas administrativas de Active Directory

Los dominios administrados de Azure AD DS se administran mediante las mismas herramientas administrativas que los entornos de AD DS en el entorno local, como el Centro de administración de Active Directory (ADAC) o PowerShell. Estas herramientas se pueden instalar como parte de la característica Herramientas de administración remota del servidor (RSAT) en Windows Server y los equipos cliente. Los miembros del grupo *Administradores del controlador de dominio de AAD* pueden administrar los dominios administrados de Azure AD DS de forma remota mediante estas herramientas administrativas de AD desde un equipo que se haya unido al dominio administrado.

Para instalar las herramientas de administración de Active Directory en la máquina virtual unida al dominio, siga los pasos a continuación:

1. **Administrador del servidor** debería abrirse de forma predeterminada al iniciar sesión en la máquina virtual. Si no es así, en el menú **Inicio**, seleccione **Administrador del servidor**.
1. En el *Panel de información* de la ventana **Administrador del servidor**, seleccione **Agregar roles y características**.
1. En la página **Antes de comenzar** del *Asistente para agregar roles y características*, seleccione **Siguiente**.
1. En *Tipo de instalación*, deje la opción **Instalación basada en características o en roles** activada y seleccione **Siguiente**.
1. En la página **Selección de servidor**, elija la máquina virtual actual del grupo de servidores, por ejemplo *mivm.contoso.com*, y seleccione **Siguiente**.
1. En la página **Roles de servidor**, haga clic en **Siguiente**.
1. En la página **Características**, expanda el nodo **Herramientas de administración remota del servidor** y el nodo **Herramientas de administración de roles**.

    Elija la característica **Herramientas de AD DS y AD LDS** en la lista de herramientas de administración de roles y, después, seleccione **Siguiente**.

    ![Instalación de las herramientas AD DS y AD LDS en la página de características](./media/tutorial-create-management-vm/install-features.png)

1. En la página **Confirmación**, seleccione **Instalar**. Puede tardar un minuto o dos en instalar las herramientas administrativas.
1. Cuando finalice la instalación de las características, seleccione **Cerrar** para salir del Asistente para **Agregar roles y características**.

## <a name="use-active-directory-administrative-tools"></a>Herramientas administrativas de Active Directory

Con las herramientas administrativas instaladas, vamos a ver cómo usarlas para administrar el dominio administrado de Azure AD DS. Asegúrese de haber iniciado sesión en la máquina virtual con una cuenta de usuario que sea miembro del grupo *Administradores del controlador de dominio de AAD*.

1. En el menú **Inicio**, seleccione **Herramientas administrativas de Windows**. Se enumeran las herramientas administrativas de AD instaladas en el paso anterior.

    ![Lista de herramientas administrativas instaladas en el servidor](./media/tutorial-create-management-vm/list-admin-tools.png)

1. Seleccione **Centro de administración de Active Directory**.
1. Para explorar el dominio administrado de Azure AD DS, elija el nombre de dominio en el panel izquierdo, por ejemplo *contoso.com*. En la parte superior de la lista hay dos contenedores denominados *Equipos de AADDC* y *Usuarios de AADDC*.

    ![Lista de parte de los contenedores disponibles del dominio administrado de Azure AD DS](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Para ver los usuarios y los grupos que pertenecen al dominio administrado de Azure AD DS, seleccione el contenedor **Usuarios de AADDC**. Las cuentas de usuario y los grupos de su inquilino de Azure AD se enumeran en este contenedor.

    En la salida de ejemplo siguiente, se muestra en este contenedor una cuenta de usuario denominada *contosoadmin* y un grupo para los *administradores de AAD DC*.

    ![Visualización de la lista de usuarios de dominio de Azure AD DS en el Centro de administración de Active Directory](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Para ver los equipos que se unen al dominio administrado de Azure AD DS, seleccione el contenedor **Equipos de AADDC**. Se muestra una entrada para la máquina virtual actual, por ejemplo *miVM*. Las cuentas de equipo de todos los equipos unidos al dominio administrado de Azure AD DS se almacenan en este contenedor *Equipos de AADDC*.

Están disponibles acciones comunes del Centro de administración de Active Directory como son el restablecimiento de la contraseña de una cuenta de usuario o la administración de la pertenencia a grupos. También puede usar el *módulo de Active Directory para Windows PowerShell*, instalado como parte de las herramientas administrativas, para administrar acciones comunes en el dominio administrado de Azure AD DS.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Usar las tareas administrativas disponibles en un dominio administrado de Azure AD DS
> * Instalación de las herramientas administrativas de Azure Active Directory en una máquina virtual de Windows Server
> * Uso del Centro de administración de Active Directory para realizar tareas comunes

Para interactuar de forma segura con su dominio administrado de Azure AD DS, habilite el Protocolo ligero de acceso a directorios seguro (LDAPS).

> [!div class="nextstepaction"]
> [Configuración de LDAP seguro para un dominio administrado](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
