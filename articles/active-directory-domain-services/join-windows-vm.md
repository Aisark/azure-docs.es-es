---
title: Unión de una máquina virtual Windows Server a un dominio administrado | Microsoft Docs
description: En este tutorial, aprenderá a unir una máquina virtual Windows a un dominio administrado de Azure Active Directory Domain Services.
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: iainfou
ms.openlocfilehash: 3fd2a50946f0857d527c34b62687b2dbdd71298e
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172039"
---
# <a name="tutorial-join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Tutorial: Unión de una máquina virtual de Windows Server a un dominio administrado

Azure Active Directory Domain Services (Azure AD DS) proporciona servicios de dominio administrados como, por ejemplo, unión a un dominio, directiva de grupo, LDAP o autenticación Kerberos/NTLM, que son totalmente compatibles con Windows Server Active Directory. Con un dominio administrado de Azure AD DS, puede proporcionar características de unión a un dominio y la administración para las máquinas virtuales (VM) en Azure. En este tutorial se muestra cómo crear una máquina virtual Windows Server y, a continuación, unirla a un dominio administrado de Azure AD DS.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una máquina virtual de Windows Server
> * Conectarse a la máquina virtual de Windows Server en una red virtual de Azure
> * Unión de la máquina virtual al dominio administrado de Azure AD DS

Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará los siguientes recursos:

* Una suscripción de Azure activa.
    * Si no tiene una suscripción a Azure, [cree una cuenta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Un inquilino de Azure Active Directory asociado a su suscripción, ya sea sincronizado con un directorio en el entorno local o con un directorio solo en la nube.
    * Si es necesario, [cree un inquilino de Azure Active Directory][create-azure-ad-tenant] o [asocie una suscripción a Azure con su cuenta][associate-azure-ad-tenant].
* Un dominio administrado de Azure Active Directory Domain Services habilitado y configurado en su inquilino de Azure AD.
    * Si es necesario, [cree y configure una instancia de Azure Active Directory Domain Services][create-azure-ad-ds-instance].
* Una cuenta de usuario que sea miembro del *grupo de administradores de Azure AD DC* en el inquilino de Azure AD.
    * Asegúrese de que se ha realizado el autoservicio de restablecimiento de contraseña o la sincronización de hash de contraseñas de Azure AD Connect para que la cuenta pueda iniciar sesión en el dominio administrado de Azure AD DS.

Si ya tiene una máquina virtual que quiera unir a un dominio, vaya a la sección sobre cómo [unir la máquina virtual al dominio administrado de Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

En este tutorial, creará una máquina virtual Windows Server para unirla al dominio administrado de Azure AD DS mediante Azure Portal. Para empezar a trabajar, primero inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-windows-server-virtual-machine"></a>Creación de una máquina virtual con Windows Server

Para ver cómo unir un equipo a un dominio administrado de Azure AD DS, vamos a crear una máquina virtual Windows Server. Esta máquina virtual está conectada a una red virtual de Azure que proporciona conectividad con el dominio administrado de Azure AD DS. El proceso para unirse a un dominio administrado de Azure AD DS es el mismo que para unirse a un dominio de Active Directory Domain Services en el entorno local normal.

Si ya tiene una máquina virtual que quiera unir a un dominio, vaya a la sección sobre cómo [unir la máquina virtual al dominio administrado de Azure AD DS](#join-the-vm-to-the-azure-ad-ds-managed-domain).

1. En la esquina superior izquierda de Azure Portal, seleccione **+ Crear un recurso**.
2. En **Introducción**, elija **Windows Server 2016 Datacenter**.

    ![Elija crear una máquina virtual de Windows Server 2016 Datacenter en Azure Portal.](./media/join-windows-vm/select-vm-image.png)

3. En la ventana **Datos básicos**, configure las opciones básicas para la máquina virtual. Deje la configuración predeterminada en *Opciones de disponibilidad*, *Imagen* y *Tamaño*.

    | Parámetro            | Valor sugerido   |
    |----------------------|-------------------|
    | Resource group       | Seleccione o cree un grupo de recursos, como *myResourceGroup* |
    | Nombre de la máquina virtual | Escriba un nombre para la máquina virtual, por ejemplo, *miVM*. |
    | Region               | Elija la región en la que se va a crear la máquina virtual, por ejemplo, *Este de EE. UU.* |
    | Nombre de usuario             | Escriba un nombre de usuario para la cuenta de administrador local que se va a crear en la máquina virtual, como *usuarioazure*. |
    | Contraseña             | Escriba una contraseña segura para el administrador local que se va a crear en la máquina virtual y, a continuación, confírmela. No especifique las credenciales de una cuenta de usuario del dominio. |

4. De forma predeterminada, las máquinas virtuales creadas en Azure no son accesibles desde Internet. Esta configuración ayuda a mejorar la seguridad de la máquina virtual y reduce el área de posibles ataques. En el siguiente paso de este tutorial, debe conectarse a la máquina virtual mediante el protocolo de escritorio remoto (RDP) y, a continuación, unir el servidor de Windows al dominio administrado de Azure AD DS.

    Cuando RDP está habilitado, es probable que se produzcan ataques de inicio de sesión automatizado, lo que puede deshabilitar las cuentas con nombres comunes como *admin* o *administrador* si hubo varios intentos de inicio de sesión sucesivos con error. RDP solo se debe habilitar cuando sea necesario y limitarse a un conjunto de intervalos IP autorizados. Como parte de Azure Security Center, el [acceso a máquinas virtuales Just-in-Time de Azure][jit-access] puede habilitar estas sesiones RDP limitadas y de corta duración. También puede [crear y usar un host de Azure Bastion (actualmente en versión preliminar)][azure-bastion] para permitir el acceso solo mediante Azure Portal a través de SSL.

    Para este tutorial, habilite manualmente las conexiones RDP a la máquina virtual.

    En **Reglas de puerto de entrada**, seleccione la opción **Permitir los puertos seleccionados**. En el menú desplegable **Seleccionar puertos de entrada**, elija *RDP*.

5. Cuando termine, seleccione **Siguiente: Discos**.
6. En el menú desplegable **Tipo de disco de sistema operativo**, elija *SSD estándar* y, a continuación, seleccione **Siguiente: Redes**.
7. La máquina virtual debe conectarse a una subred de red virtual de Azure que pueda comunicarse con la subred en la que está implementado el dominio administrado de Azure AD DS. Se recomienda implementar un dominio administrado de Azure AD DS en su propia subred dedicada. No implemente la máquina virtual en la misma subred que el dominio administrado de Azure AD DS.

    Hay dos formas principales de implementar la máquina virtual y conectarse a una subred de red virtual adecuada:
    
    * Cree o seleccione una subred existente en la misma red virtual en la que esté implementado el dominio administrado de Azure AD DS.
    * Seleccione una subred en una red virtual de Azure que esté conectada a ella con el [emparejamiento de redes virtuales de Azure][vnet-peering].
    
    Si selecciona una subred de red virtual que no esté conectada a la subred de su instancia de Azure AD DS, no puede unir la máquina virtual al dominio administrado. En este tutorial, vamos a crear una nueva subred en la red virtual de Azure.

    En el panel **Redes**, seleccione la red virtual en la que se implementa el dominio administrado de Azure AD DS, como *miVnet*.
8. En este ejemplo, la subred *DomainServices* existente muestra que está conectado el dominio administrado de Azure AD DS. No conecte la máquina virtual a esta subred. Para crear una subred para la máquina virtual, seleccione **Administrar configuración de subred**.

    ![Elija esta opción para administrar la configuración de la subred en Azure Portal](./media/join-windows-vm/manage-subnet.png)

9. Seleccione **+ Subred** y, a continuación, escriba un nombre para la subred, como *VMadministradas*. Proporcione un **intervalo de direcciones (bloque CIDR)** , como *10.1.1.0/24*. Asegúrese de que este intervalo de direcciones IP no se superponga con otros en el entorno local o de Azure existentes. Deje las restantes opciones con sus valores predeterminados y, después, seleccione **Aceptar**.

    ![Creación de la configuración de una subred en Azure Portal](./media/join-windows-vm/create-subnet.png)

10. La subred tarda unos segundos en crearse. Una vez creada, seleccione la *X* para cerrar la ventana de la subred.
11. De nuevo en el panel **Redes** para crear una máquina virtual, elija la subred que creó en el menú desplegable, como *VMadministradas*. De nuevo, asegúrese de elegir la subred correcta y no implemente la máquina virtual en la misma que el dominio administrado de Azure AD DS.
12. Deje las restantes opciones con sus valores predeterminados y, después, seleccione **Administración**.
13. Establezca **Diagnósticos de arranque** en *Desactivado*. Deje las restantes opciones con sus valores predeterminados y, después, seleccione **Revisar y crear**.
14. Revise la configuración de la máquina virtual y seleccione **Crear**.

La operación de creación de la máquina virtual tarda unos minutos. En Azure Portal, se muestra el estado de la implementación. Cuando la máquina virtual esté lista, seleccione **Ir al recurso**.

![Vaya al recurso de máquina virtual en Azure Portal una vez que se haya creado correctamente.](./media/join-windows-vm/vm-created.png)

## <a name="connect-to-the-windows-server-vm"></a>Conexión a la máquina virtual de Windows Server

Ahora vamos a conectarnos a la máquina virtual Windows Server recién creada mediante RDP y a unirla al dominio administrado de Azure AD DS. Use las credenciales de administrador local que especificó cuando se creó la máquina virtual en el paso anterior, no las actuales.

1. En el panel **Información general**, seleccione **Conectar**.

    ![Conexión a una máquina virtual de Windows en Azure Portal](./media/join-windows-vm/connect-to-vm.png)

1. Seleccione la opción *Descargar archivo RDP*. Guarde este archivo RDP en el explorador Web.
1. Para conectarse a la máquina virtual, abra el archivo RDP descargado. Cuando se le pida, seleccione **Conectar**.
1. Escriba las credenciales de administrador local que escribió en el paso anterior para crear la máquina virtual, como *localhost\azureuser*
1. Si ve una advertencia de certificado durante el proceso de inicio de sesión, seleccione **Sí**o **Continuar** para conectarse.

## <a name="join-the-vm-to-the-azure-ad-ds-managed-domain"></a>Unión de la máquina virtual al dominio administrado de Azure AD DS

Una vez que se ha creado la máquina virtual y se ha establecido una conexión RDP, vamos a unir la máquina virtual Windows Server al dominio administrado de Azure AD DS. Este proceso es el mismo que el de un equipo que se conecta a un dominio de Active Directory Domain Services en el entorno local normal.

1. **Administrador del servidor** debe abrirse de forma predeterminada al iniciar sesión en la máquina virtual. Si no es así, en el menú **Inicio**, seleccione **Administrador del servidor**.
1. En el panel izquierdo de la ventana **Administrador del servidor**, seleccione **Servidor local**. En **Propiedades**, en el panel derecho, elija **Grupo de trabajo**.

    ![Abra Administrador del servidor en la máquina virtual y edite la propiedad del grupo de trabajo.](./media/join-windows-vm/server-manager.png)

1. En la ventana **Propiedades del sistema**, seleccione **Cambiar** para unirse al dominio administrado de Azure AD DS.

    ![Elección de la opción para cambiar las propiedades de grupo de trabajo o dominio](./media/join-windows-vm/change-domain.png)

1. En el cuadro **Dominio**, especifique el nombre del dominio administrado con Azure AD DS, como *contoso.com* y, a continuación, seleccione **Aceptar**.

    ![Especificación del dominio administrado de Azure AD DS al que unirse](./media/join-windows-vm/join-domain.png)

1. Escriba las credenciales de dominio para unirse al dominio. Use las credenciales de un usuario que pertenezca al grupo de *administradores de Azure AD DC*. Solo los miembros de este grupo tienen privilegios para unir máquinas al dominio administrado de Azure AD DS. Las credenciales de la cuenta se pueden especificar de una de las siguientes maneras:

    * **Formato UPN**  (recomendado): escriba el sufijo del nombre principal de usuario (UPN) de la cuenta de usuario, según esté configurado en Azure AD. Por ejemplo, el sufijo UPN del usuario *contosoadmin* sería `contosoadmin@contoso.onmicrosoft.com`. Hay un par de casos de uso comunes en los que el formato UPN se puede usar de forma confiable para iniciar sesión en el dominio en lugar del formato *SAMAccountName*:
        * Si el prefijo UPN de un usuario es demasiado largo, por ejemplo *deehasareallylongname*, el valor de *SAMAccountName* (nombre de cuenta SAM) puede generarse automáticamente.
        * Si varios usuarios tienen el mismo prefijo UPN en su inquilino de Azure AD, por ejemplo, *dee*, su formato *SAMccountName* podría generarse automáticamente.
    * **Formato SAMAccountName**: escriba el nombre de la cuenta en formato *SAMAccountName*. Por ejemplo, el formato *SAMAccountName* del usuario *contosoadmin* sería `CONTOSO\contosoadmin`.

1. Se tarda unos segundos en unirse al dominio administrado de Azure AD DS. Cuando haya finalizado, el siguiente mensaje le da la bienvenida al dominio:

    ![Bienvenido al dominio](./media/join-windows-vm/join-domain-successful.png)

    Seleccione **OK** (Aceptar) para continuar.

1. Para completar el proceso de unión al dominio administrado de Azure AD DS, reinicie la máquina virtual.

> [!TIP]
> Puede unir a un dominio una máquina virtual mediante PowerShell con el cmdlet [Add-Computer][add-computer]. En el ejemplo siguiente se une el dominio *CONTOSO* y, a continuación, se reinicia la máquina virtual. Cuando se le pida, escriba las credenciales de un usuario que pertenezca al grupo de *administradores de Azure AD DC*:
>
> `Add-Computer -DomainName CONTOSO -Restart`
>
> Para unirse a un dominio de una máquina virtual sin conectarse a ella y configurar manualmente la conexión, también puede considerar el uso del cmdlet de Azure PowerShell [Set-AzVmAdDomainExtension][set-azvmaddomainextension].

Una vez que se haya reiniciado la máquina virtual de Windows Server, las directivas aplicadas en el dominio administrado de Azure AD DS se insertarán en la máquina virtual. Ahora también puede iniciar sesión en la máquina virtual de Windows Server con las credenciales de dominio apropiadas.

## <a name="clean-up-resources"></a>Limpieza de recursos

En el siguiente tutorial, utilizará esta máquina virtual de Windows Server para instalar las herramientas de administración que permitan administrar el dominio administrado de Azure AD DS. Si no desea continuar en esta serie de tutoriales, revise las siguientes etapas de limpieza para [deshabilitar RDP](#disable-rdp) o [eliminar la máquina virtual](#delete-the-vm). De lo contrario, [continúe con el tutorial siguiente](#next-steps).

### <a name="un-join-the-vm-from-azure-ad-ds-managed-domain"></a>Separación de la máquina virtual del dominio administrado de Azure AD DS

Para retirar la máquina virtual del dominio administrado de Azure AD DS, siga de nuevo los pasos para [unir la máquina virtual a un dominio](#join-the-vm-to-the-azure-ad-ds-managed-domain). En lugar de unirse al dominio administrado de Azure AD DS, elija unirse a un grupo de trabajo, como el predeterminado *WORKGROUP*. Una vez que la máquina virtual se ha reiniciado, el objeto de equipo se quita del dominio administrado de Azure AD DS.

Si [elimina la máquina virtual](#delete-the-vm) sin haberla separado del dominio, se deja un objeto de equipo huérfano en Azure AD DS.

### <a name="disable-rdp"></a>Deshabilitar RDP

Si continúa usando la máquina virtual de Windows Server creada en este tutorial para ejecutar sus propias aplicaciones o cargas de trabajo, recuerde que RDP se abrió a través de Internet. Para mejorar la seguridad y reducir el riesgo de ataque, RDP debe deshabilitarse a través de Internet. Para deshabilitar RDP para la máquina virtual de Windows Server a través de Internet, complete estos pasos:

1. En el menú izquierdo, seleccione **Grupos de recursos**.
1. Elija el grupo de recursos, como *miGrupoDeRecursos*.
1. Elija su máquina virtual, como *miVM* y, a continuación, seleccione *Redes*.
1. En **Reglas de seguridad de red de entrada** para el grupo de seguridad de red, seleccione la regla que permita RDP y, a continuación, elija **Eliminar**. La eliminación de la regla de seguridad de entrada tarda unos segundos.

### <a name="delete-the-vm"></a>Eliminación de la máquina virtual

Si no va a usar esta máquina virtual de Windows Server, siga estos pasos para eliminarla:

1. En el menú izquierdo, seleccione **Grupos de recursos**.
1. Elija el grupo de recursos, como *miGrupoDeRecursos*.
1. Elija su máquina virtual, como *miVM* y, a continuación, seleccione **Eliminar**. Seleccione **Sí** para confirmar la eliminación del recurso. La operación de eliminación de la máquina virtual tarda unos minutos.
1. Cuando se elimine la máquina virtual, seleccione el disco del sistema operativo, la tarjeta de interfaz de red y otros recursos con el prefijo *miVM* y elimínelos.

## <a name="troubleshoot-domain-join-issues"></a>Solución de problemas de unión al dominio

La máquina virtual Windows Server debe unirse correctamente al dominio administrado de Azure AD DS, de la misma manera que un equipo normal en el entorno local se uniría a un dominio de Active Directory Domain Services. Si la máquina virtual Windows Server no se puede unir al dominio administrado de Azure AD DS, indica que hay un problema relacionado con la conectividad o con las credenciales. Revise las siguientes secciones de solución de problemas para unirse correctamente al dominio administrado.

### <a name="connectivity-issues"></a>Problemas de conectividad

Si no recibe un mensaje que le pida credenciales para unirse al dominio, hay un problema de conectividad. La máquina virtual no puede tener acceso al dominio administrado de Azure AD DS en la red virtual.

Después de probar cada uno de estos pasos de solución de problemas, intente unir de nuevo la máquina virtual Windows Server al dominio administrado.

* Compruebe que la máquina virtual esté conectada a la misma red virtual en la que está habilitado Azure AD DS o que tenga una conexión de red emparejada.
* Intente hacer ping al nombre de dominio DNS del dominio administrado, por ejemplo `ping contoso.com`.
    * Si se produce un error en la solicitud ping, intente hacer ping a las direcciones IP del dominio administrado, por ejemplo `ping 10.0.0.4`. La dirección IP de su entorno se muestra en la página *Propiedades* al seleccionar el dominio administrado de Azure AD DS en la lista de recursos de Azure.
    * Si puede hacer ping a la dirección IP pero no al dominio, DNS puede estar configurado incorrectamente. Confirme que las direcciones IP del dominio administrado están configuradas como servidores DNS para la red virtual.
* Intente vaciar la memoria caché de resolución DNS en la máquina virtual con el comando `ipconfig /flushdns`.

### <a name="credentials-related-issues"></a>Problemas relacionados con las credenciales

Si recibe un mensaje que solicita credenciales para unirse al dominio, pero después hay un error tras escribir esas credenciales, la máquina virtual puede conectarse al dominio administrado de Azure AD DS. Las credenciales proporcionadas no permiten a la máquina virtual unirse al dominio administrado de Azure AD DS.

Después de probar cada uno de estos pasos de solución de problemas, intente unir de nuevo la máquina virtual Windows Server al dominio administrado.

* Asegúrese de que la cuenta de usuario que especifique pertenece al grupo *Administradores de DC de AAD*.
* Pruebe a usar el formato UPN para especificar las credenciales, por ejemplo `contosoadmin@contoso.onmicrosoft.com`. El atributo *SAMAccountName* de su cuenta se puede generar automáticamente si hay varios usuarios con el mismo prefijo UPN en el inquilino o si el prefijo UPN es demasiado largo. En estos casos, el formato *SAMAccountName* de su cuenta puede que no sea el mismo que espera o que usa en su dominio en el entorno local.
* Compruebe que [habilitó la sincronización de contraseñas][password-sync] en el dominio administrado. Sin este paso de configuración, los hashes de contraseña necesarios no estarán presentes en el dominio administrado de Azure AD DS para autenticar correctamente el intento de inicio de sesión.
* Espere a que se complete la sincronización de contraseñas. Cuando se cambia la contraseña de una cuenta de usuario, una sincronización automática en segundo plano de Azure AD actualiza la contraseña en Azure AD DS. La contraseña tarda un tiempo en estar disponible para usarla en la unión al dominio.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una máquina virtual de Windows Server
> * Conectarse a la máquina virtual de Windows Server en una red virtual de Azure
> * Unión de la máquina virtual al dominio administrado de Azure AD DS

Para administrar el dominio administrado de Azure AD DS, configure una máquina virtual de administración mediante el Centro de administración de Active Directory (ADAC).

> [!div class="nextstepaction"]
> [Instalación de herramientas de administración en una máquina virtual de administración](tutorial-create-management-vm.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[vnet-peering]: ../virtual-network/virtual-network-peering-overview.md
[password-sync]: active-directory-ds-getting-started-password-sync.md
[add-computer]: /powershell/module/microsoft.powershell.management/add-computer
[jit-access]: ../security-center/security-center-just-in-time.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
[set-azvmaddomainextension]: /powershell/module/az.compute/set-azvmaddomainextension
