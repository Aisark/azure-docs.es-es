---
title: Preguntas más frecuentes sobre Azure DevTest Labs | Microsoft Docs
description: Encuentre respuestas a preguntas comunes sobre Azure DevTest Labs.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: afe83109-b89f-4f18-bddd-b8b4a30f11b4
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: 3752e9c227e4db0f583b2f6b21d6c0aa3106d248
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337704"
---
# <a name="azure-devtest-labs-faq"></a>Preguntas más frecuentes sobre Azure DevTest Labs
En este artículo se ofrecen respuestas a algunas de las preguntas más frecuentes sobre Azure DevTest Labs.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

**General**

## <a name="blog-post"></a>Entrada de blog
Se ha retirado el blog de nuestro equipo de DevTest Labs a partir del 20 de marzo de 2019. 

### <a name="where-can-i-track-feature-updates-going-forward"></a>¿Dónde puedo realizar un seguimiento de las actualizaciones de características a partir de ahora?
A partir de ahora, publicaremos actualizaciones de características o entradas de blog informativo en el blog de Azure y Azure actualiza. Estas entradas de blog también se vincularán a la documentación de cada vez que necesite.

Suscribirse a la [Blog de Azure DevTest Labs](https://azure.microsoft.com/blog/tag/azure-devtest-labs/) y [DevTest Labs Azure actualiza](https://azure.microsoft.com/updates/?product=devtest-lab) para mantenerse informado sobre las nuevas características en DevTest Labs.

### <a name="what-happens-to-the-existing-blog-posts"></a>¿Qué ocurre con las entradas de blog existente?
Actualmente estamos trabajando para migrar entradas de blog existente (excepto las actualizaciones de interrupción) para nuestra [documentación de DevTest Labs](devtest-lab-overview.md). Cuando está en desuso el blog MSDN, se le redirigirá a la introducción a la documentación de DevTest Labs. Una vez que se redirige, puede buscar el artículo que está buscando en el título 'Filtrar por'. Tenga en cuenta aún no lo ha migrado todas las publicaciones, pero se debe realizar al final de este mes. 


### <a name="where-do-i-see-outage-updates"></a>¿Dónde se puede ver las actualizaciones de interrupción?
Publicaremos actualizaciones de interrupción mediante el identificador de Twitter a partir de ahora. Síganos en Twitter para obtener las actualizaciones más recientes en las interrupciones y errores conocidos.

### <a name="twitter"></a>Twitter 
El identificador de Twitter: azlabservices

## <a name="what-if-my-question-isnt-answered-here"></a>Mi pregunta no está respondida aquí. ¿Qué debo hacer?
Si su pregunta no aparece aquí, háganoslo saber y podemos ayudarle a encontrar una respuesta.

* Publique una pregunta al final de este documento. Póngase en contacto con el equipo de Azure Cache y otros miembros de la comunidad sobre este artículo.
* Para llegar a un público más amplio, publique una pregunta en el [foro de MSDN de Azure DevTest Labs](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureDevTestLabs). Póngase en contacto con el equipo de Azure DevTest Labs y otros miembros de la comunidad.
* Para solicitudes de características, envíe sus solicitudes e ideas al sitio [Azure DevTest Labs User Voice](https://feedback.azure.com/forums/320373-azure-devtest-labs).

## <a name="why-should-i-use-azure-devtest-labs"></a>¿Por qué debería usar Azure DevTest Labs?
Azure DevTest Labs puede ahorrarle a su equipo tiempo y dinero. Los desarrolladores pueden crear sus propios entornos usando varias bases de datos diferentes. También puede utilizar artefactos para implementar y configurar aplicaciones rápidamente. Mediante el uso de fórmulas e imágenes personalizadas, puede guardar máquinas virtuales (VM) como plantillas y reproducirlas fácilmente en el equipo. DevTest Labs ofrece también varias directivas configurables que permiten a los administradores de los laboratorios reducir los residuos y administrar los entornos de un equipo. Estas directivas incluyen el apagado automático, el umbral de costo, el número máximo de máquinas virtuales por usuario y el tamaño máximo de cada máquina virtual. Para ver una explicación más detallada de DevTest Labs, lea la [información general](devtest-lab-overview.md) o consulte el [vídeo de introducción](https://channel9.msdn.com/Blogs/Azure/what-is-azure-devtest-labs).

## <a name="what-does-worry-free-self-service-mean"></a>¿Qué significa "autoservicio sin preocupaciones"?
Autoservicio sin preocupaciones significa que los desarrolladores y evaluadores crean sus propios entornos según sea necesario. Los administradores tienen la seguridad de que DevTest Labs puede ayudar a minimizar los costos del control y los residuos. Los administradores pueden especificar qué tamaños de máquina virtual se permiten, el número máximo de máquinas virtuales y cuándo iniciar y apagar las máquinas virtuales. DevTest Labs también permite supervisar fácilmente los costos y definir alertas para ayudarle a estar al tanto de cómo se usan los recursos de laboratorios.

## <a name="how-can-i-use-devtest-labs"></a>¿Cómo se usa DevTest Labs?
DevTest Labs es útil siempre que necesite desarrollar o probar entornos y quiera reproducirlos rápidamente o administrarlos mediante las directivas de ahorro de costos.

Estos son algunos escenarios en los que nuestros clientes usan DevTest Labs:

* Administración de entornos de desarrollo y pruebas en un solo lugar. Uso de directivas para reducir los costos y crear imágenes personalizadas para compartir en un equipo de personas.
* Desarrollo de una aplicación con imágenes personalizadas para guardar el estado del disco en todas las etapas del desarrollo.
* Seguimiento del costo en relación con el progreso.
* Creación de entornos de prueba masivos para pruebas de garantía de calidad.
* Uso de artefactos y fórmulas para configurar y reproducir fácilmente una aplicación en diversos entornos.
* Distribución de máquinas virtuales para encuentros de programadores (hackathons) (trabajo de desarrollo o prueba en colaboración) y desaprovisionamiento fácil de estas máquinas cuando el evento finaliza.

## <a name="how-am-i-billed-for-devtest-labs"></a>¿Cómo se factura DevTest Labs?
DevTest Labs es un servicio gratuito. Crear laboratorios y configurar las directivas, las plantillas y los artefactos en DevTest Labs no tiene ningún costo. Solo se paga por los recursos de Azure usados en los laboratorios, como máquinas virtuales, cuentas de almacenamiento y redes virtuales. Para más información sobre el costo de los recursos de laboratorio, consulte los [precios de Azure DevTest Labs](https://azure.microsoft.com/pricing/details/devtest-lab/).


**Seguridad**
## <a name="what-are-the-different-security-levels-in-devtest-labs"></a>¿Cuáles son los diferentes niveles de seguridad en DevTest Labs?
La seguridad del acceso viene determinada por el [Control de acceso basado en roles (RBAC)](../role-based-access-control/built-in-roles.md). Para aprender cómo funciona el acceso, es importante comprender las diferencias entre un permiso, un rol y un ámbito según se define en RBAC.

* **Permiso**: un permiso es un acceso definido para una acción específica. Por ejemplo, un permiso podría ser el acceso de lectura a todas las máquinas virtuales.
* **Rol**: un rol es un conjunto de permisos que se pueden agrupar y asignar a un usuario. Por ejemplo, un usuario con el rol de propietario de la suscripción tiene acceso a todos los recursos dentro de una suscripción.
* **Ámbito**: un ámbito es un nivel dentro de la jerarquía de recursos de Azure. Por ejemplo, un ámbito puede ser un grupo de recursos, un único laboratorio o toda la suscripción.

Dentro del ámbito de DevTest Labs, hay dos tipos de roles para definir los permisos de usuario:

* **Propietario de laboratorio**: tiene acceso a todos los recursos del laboratorio. El propietario de un laboratorio puede modificar las directivas, leer y escribir en todas las máquinas virtuales, cambiar la red virtual, etc.
* **Usuario de laboratorio**: puede ver todos los recursos del laboratorio, como las máquinas virtuales, las directivas y las redes virtuales. Sin embargo, un usuario de laboratorio no puede modificar las directivas ni ninguna máquina virtual creada por otros usuarios. 

También puede crear roles personalizados en DevTest Labs. Para aprender a crear roles personalizados en DevTest Labs, consulte [Concesión de permisos de usuario a directivas específicas de laboratorio](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Puesto que los ámbitos son jerárquicos, cuando un usuario tiene permisos en un ámbito determinado, también se le conceden automáticamente en cada ámbito de nivel inferior. Por ejemplo, si un usuario está asignado al rol de propietario de la suscripción, tiene acceso a todos los recursos de una suscripción. Estos recursos incluyen todas las máquinas virtuales, las redes virtuales y los laboratorios. El propietario de una suscripción hereda automáticamente el rol de propietario de laboratorio. Sin embargo, lo contrario no es cierto. El propietario de un laboratorio tiene acceso a un laboratorio, que es un ámbito inferior al del nivel de suscripción. Por lo tanto, no puede ver las máquinas virtuales, las redes virtuales ni ningún otro recurso que esté fuera del laboratorio.

## <a name="how-do-i-create-a-role-to-allow-users-to-perform-a-specific-task"></a>¿Cómo se crea un rol para permitir que los usuarios realicen una tarea específica?
Para un artículo completo sobre cómo crear roles personalizados y asignar permisos a un rol, consulte [Concesión de permisos de usuario para directivas específicas de laboratorio](devtest-lab-grant-user-permissions-to-specific-lab-policies.md). Este es un ejemplo de script que crea el rol *Usuario avanzado de DevTest Labs*, que tiene permiso para iniciar y detener todas las máquinas virtuales del laboratorio:

    $policyRoleDef = Get-AzRoleDefinition "DevTest Labs User"
    $policyRoleDef.Actions.Remove('Microsoft.DevTestLab/Environments/*')
    $policyRoleDef.Id = $null
    $policyRoleDef.Name = "DevTest Labs Advanced User"
    $policyRoleDef.IsCustom = $true
    $policyRoleDef.AssignableScopes.Clear()
    $policyRoleDef.AssignableScopes.Add("subscriptions/<subscription Id>")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Start/action")
    $policyRoleDef.Actions.Add("Microsoft.DevTestLab/labs/virtualMachines/Stop/action")
    $policyRoleDef = New-AzRoleDefinition -Role $policyRoleDef  


**Automatización e integración de CI/CD**
## <a name="does-devtest-labs-integrate-with-my-cicd-toolchain"></a>¿Se integra DevTest Labs con mi cadena de herramientas de CI/CD?
Si va a utilizar Azure DevOps, puede usar una [extensión Tareas de DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) que le permite automatizar la canalización de entrega de versiones de DevTest Labs. Algunas de las tareas que puede realizar con esta extensión incluyen:

* Crear e implementar una máquina virtual automáticamente. También puede configurar la máquina virtual con la compilación mas reciente mediante las tareas de PowerShell Azure DevOps Services o de Copia de archivos de Azure.
* Capturar automáticamente el estado de una máquina virtual después de intentar reproducir un error en la misma máquina virtual para investigarlo con más detalle.
* Eliminar la máquina virtual al final de la canalización de versiones cuando ya no sea necesaria.

Las siguientes entradas de blog proporcionan orientación e información sobre el uso de la extensión Azure DevOps Services:

* [DevTest Labs y la extensión de Azure DevOps](https://blogs.msdn.microsoft.com/devtestlab/2016/06/15/azure-devtest-labs-vsts-extension/)
* [Deploy a new VM in an existing DevTest Labs lab from Azure DevOps Services](https://www.visualstudiogeeks.com/blog/DevOps/Deploy-New-VM-To-Existing-AzureDevTestLab-From-VSTS) (Implementación de una nueva máquina virtual en un laboratorio de DevTest Labs existente desde Azure DevOps Services)
* [Using Azure DevOps Services release management for continuous deployments to Dev Test Labs](https://www.visualstudiogeeks.com/blog/DevOps/Use-VSTS-ReleaseManagement-to-Deploy-and-Test-in-AzureDevTestLabs) (Uso de la administración de versiones de Azure DevOps Services para implementaciones continuas en Dev Test Labs)

Para otras cadenas de herramientas de entrega continua (CD) o integración continua (CI), puede lograr los mismos escenarios implementando [plantillas de Azure Resource Manager](https://aka.ms/dtlquickstarttemplate) mediante [cmdlets de Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md) y [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.DevTestLabs/). También puede usar las [API de REST para DevTest Labs](https://aka.ms/dtlrestapis) a fin de integrarlas con su cadena de herramientas.  


**Máquinas virtuales**
## <a name="why-cant-i-see-vms-on-the-virtual-machines-page-that-i-see-in-devtest-labs"></a>¿Por qué no puedo ver las máquinas virtuales en la página de máquinas virtuales que veo en DevTest Labs?
Cuando crea una máquina virtual en DevTest Labs, se le otorga permisos para acceder a dicha máquina virtual. Puede ver la máquina virtual en la página de laboratorios y en el **máquinas virtuales** página. Los usuarios asignados al rol de usuario de laboratorio de DevTest Labs pueden ver todas las máquinas virtuales que se crearon en el laboratorio en el laboratorio **todas las máquinas virtuales** página. No obstante, a los usuarios que tengan el rol de usuario de laboratorio de DevTest Labs no se les concede automáticamente acceso de lectura a los recursos de máquina virtual creados por otros usuarios. Por lo tanto, esas máquinas virtuales no se muestran en el **máquinas virtuales** página.

## <a name="what-is-the-difference-between-a-custom-image-and-a-formula"></a>¿Cuál es la diferencia entre una imagen personalizada y una fórmula?
Una imagen personalizada es un disco duro virtual (VHD). Una fórmula es una imagen que puede configurar con opciones adicionales y, a continuación, guardar y reproducir. Si lo que quiere es crear rápidamente varios entornos con la misma imagen básica inmutable, puede ser preferible una imagen personalizada. En cambio, si desea reproducir la configuración de la máquina virtual con los últimos bits, como parte de una red o una subred virtual, o como máquina virtual de un tamaño específico, es mejor una fórmula. Para una explicación más detallada, consulte [Comparación de imágenes personalizadas y fórmulas en DevTest Labs](devtest-lab-comparing-vm-base-image-types.md).

## <a name="how-do-i-create-multiple-vms-from-the-same-template-at-once"></a>¿Cómo se crean varias máquinas virtuales desde la misma plantilla a la vez?
Tiene dos opciones para crear al mismo tiempo varias máquinas virtuales desde la misma plantilla:
* Puede usar la [extensión Tareas de Azure DevOps](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks). 
* Puede [generar una plantilla de Resource Manager](devtest-lab-add-vm.md#save-azure-resource-manager-template) mientras crea una máquina virtual e [implementar la plantilla de Resource Manager desde Windows PowerShell](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="how-do-i-move-my-existing-azure-vms-into-my-devtest-labs-lab"></a>¿Cómo se pueden mover las máquinas virtuales de Azure existentes a mi laboratorio de DevTest Labs?
Para copiar las máquinas virtuales existentes a los laboratorios de DevTest Labs:

1. Copie el archivo VHD de la máquina virtual existente mediante un [script de Windows PowerShell](https://github.com/Azure/azure-devtestlab/blob/master/Scripts/CopyVHDFromVMToLab.ps1).
2. [Cree la imagen personalizada](devtest-lab-create-template.md) dentro del laboratorio de DevTest Labs.
3. Cree una máquina virtual en el laboratorio a partir de su imagen personalizada.

## <a name="can-i-attach-multiple-disks-to-my-vms"></a>¿Se pueden conectar varios discos a las máquinas virtuales?
Sí, puede adjuntar varios discos a sus máquinas virtuales.  

## <a name="if-i-want-to-use-a-windows-os-image-for-my-testing-do-i-have-to-purchase-an-msdn-subscription"></a>Si quiero usar una imagen de sistema operativo Windows para mi prueba, ¿tengo que comprar una suscripción a MSDN?
Para usar imágenes de sistema operativo cliente de Windows (Windows 7 una versión o posterior) para el desarrollo o las pruebas en Azure, debe elegir entre:

- [Adquirir una suscripción a MSDN](https://www.visualstudio.com/products/how-to-buy-vs).
- Si tiene un Contrato Enterprise, crear una suscripción a Azure con la [oferta Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p).

Para obtener más información acerca de los créditos de Azure para cada oferta de MSDN, consulte [Crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/).

## <a name="how-do-i-automate-the-process-of-uploading-vhd-files-to-create-custom-images"></a>¿Cómo se puede automatizar el proceso de carga de archivos VHD para crear imágenes personalizadas?
Para automatizar la carga de archivos VHD para crear imágenes personalizadas, dispone de dos opciones:

* Usar [AzCopy](../storage/common/storage-use-azcopy.md#upload-blobs-to-blob-storage) para copiar o cargar archivos VHD en la cuenta de almacenamiento asociada al laboratorio.
* Usar el [Explorador de Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md). Explorador de Azure Storage es una aplicación independiente que se ejecuta en Windows, OS X y Linux.   

Para buscar la cuenta de almacenamiento de destino asociada al laboratorio:

1. Inicie sesión en el [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
2. En el menú de la izquierda, seleccione **Grupos de recursos**.
3. Busque y seleccione el grupo de recursos asociado al laboratorio.
4. En **Información general**, seleccione una de las cuentas de almacenamiento.
5. Seleccione **Blobs**.
6. Busque cargas en la lista. Si no existe ninguna, vuelva al paso 4 y pruebe con otra cuenta de almacenamiento.
7. Use la **dirección URL** como destino del comando AzCopy.

## <a name="how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab"></a>¿Cómo se automatiza el proceso de eliminación de todas las máquinas virtuales en mi laboratorio?
Puede eliminar las máquinas virtuales en el laboratorio en Azure Portal. También puede eliminar todas las máquinas virtuales en el laboratorio mediante un script de PowerShell. En el ejemplo siguiente, en el comentario **Values to change** (Valores para cambiar), modifique los valores del parámetro. Puede recuperar los valores `subscriptionId`, `labResourceGroup` y `labName` del panel del laboratorio en Azure Portal.

    # Delete all the VMs in a lab.

    # Values to change:
    $subscriptionId = "<Enter Azure subscription ID here>"
    $labResourceGroup = "<Enter lab's resource group here>"
    $labName = "<Enter lab name here>"

    # Sign in to your Azure account.
    Connect-AzAccount

    # Select the Azure subscription that has the lab. This step is optional
    # if you have only one subscription.
    Select-AzSubscription -SubscriptionId $subscriptionId

    # Get the lab that has the VMs that you want to delete.
    $lab = Get-AzResource -ResourceId ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)

    # Get the VMs from that lab.
    $labVMs = Get-AzResource | Where-Object {
              $_.ResourceType -eq 'microsoft.devtestlab/labs/virtualmachines' -and
              $_.Name -like "$($lab.Name)/*"}

    # Delete the VMs.
    foreach($labVM in $labVMs)
    {
        Remove-AzResource -ResourceId $labVM.ResourceId -Force
    }

**Artefactos**
## <a name="what-are-artifacts"></a>¿Qué son los artefactos?
Los artefactos son elementos personalizables que puede usar para implementar los bits más recientes o herramientas de desarrollo en una máquina virtual. Asocie los artefactos a la máquina virtual al crearla. Después de aprovisionar la máquina virtual, los artefactos la implementan y configuran. Varios artefactos preexistentes están disponibles en nuestra [repositorio público de GitHub](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts). También puede [crear sus propios artefactos](devtest-lab-artifact-author.md).


**Configuración del laboratorio**
## <a name="how-do-i-create-a-lab-from-a-resource-manager-template"></a>¿Cómo se crea un laboratorio a partir de una plantilla de Resource Manager?
Hemos ofrecido un [repositorio de GitHub de plantillas de Azure Resource Manager para laboratorios](https://aka.ms/dtlquickstarttemplate) que se pueden implementar tal cual o modificar para crear plantillas personalizadas para los laboratorios. Cada plantilla tiene un vínculo para implementar el laboratorio tal cual está en su propia suscripción de Azure. O bien, puede personalizar la plantilla e [implementarla con PowerShell o la CLI de Azure](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="why-are-my-vms-created-in-different-resource-groups-with-arbitrary-names-can-i-rename-or-modify-these-resource-groups"></a>¿Por qué mis máquinas virtuales se crean en distintos grupos de recursos con nombres arbitrarios? ¿Se pueden modificar estos grupos de recursos o cambiar su nombre?
Los grupos de recursos se crean de esta manera para que DevTest Labs pueda administrar los permisos de usuario y el acceso a las máquinas virtuales. Aunque puede mover una máquina virtual a otro grupo de recursos y usar el nombre que desee, se recomienda que no haga cambios en los grupos de recursos. Estamos trabajando en mejorar esta experiencia para permitir más flexibilidad.   

## <a name="how-many-labs-can-i-create-under-the-same-subscription"></a>¿Cuántos laboratorios se pueden crear con una misma suscripción?
No hay ningún límite específico en el número de laboratorios que se pueden crear por cada suscripción. Sin embargo, la cantidad de recursos que se usan para cada suscripción está limitada. Puede leer sobre los [límites y las cuotas de las suscripciones de Azure](../azure-subscription-service-limits.md) y [cómo aumentar estos límites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-many-vms-can-i-create-per-lab"></a>¿Cuántas máquinas virtuales se pueden crear por laboratorio?
No hay ningún límite específico en el número de máquinas virtuales que se pueden crear por cada laboratorio. Sin embargo, los recursos (los núcleos de máquinas virtuales, las direcciones IP públicas, etc) están limitados para cada suscripción. Puede leer sobre los [límites y las cuotas de las suscripciones de Azure](../azure-subscription-service-limits.md) y [cómo aumentar estos límites](https://azure.microsoft.com/blog/azure-limits-quotas-increase-requests).

## <a name="how-do-i-share-a-direct-link-to-my-lab"></a>¿Cómo se puede compartir un vínculo directo a mi laboratorio?

1. En Azure Portal, vaya al laboratorio.
2. Copie la dirección URL del laboratorio desde el explorador y compártala con los usuarios del laboratorio.

> [!NOTE]
> Si un usuario del laboratorio es externo y tiene una [cuenta de Microsoft](#what-is-a-microsoft-account), pero no es un miembro de la instancia de Active Directory de su organización, podría ver un mensaje de error cuando intente tener acceso al vínculo compartido. Si un usuario externo ve un mensaje de error, pídale que seleccione primero su nombre en la esquina superior derecha de Azure Portal. A continuación, en la sección **Directorio** del menú, el usuario puede seleccionar el directorio donde se encuentra el laboratorio.
>
>

## <a name="what-is-a-microsoft-account"></a>¿Qué es una cuenta Microsoft?
Usa una cuenta de Microsoft para casi todo lo que hace con los servicios y los dispositivos de Microsoft. Es una dirección de correo electrónico y una contraseña que utiliza para iniciar sesión en Skype, Outlook.com, OneDrive, Windows Phone y Xbox Live. Tener una sola cuenta significa que puede disponer de los archivos, fotografías, contactos y configuración desde cualquier dispositivo.

> [!NOTE]
> La cuenta de Microsoft antes se llamaba *Windows Live ID*.
>
>


**Solución de problemas**
## <a name="my-artifact-failed-during-vm-creation-how-do-i-troubleshoot-it"></a>Mi artefacto produjo errores durante la creación de la máquina virtual. ¿Cómo se soluciona este problema?
Para aprender a obtener registros de los artefactos con error, consulte [How to diagnose artifact failures in DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md) (Diagnóstico de errores de artefactos en DevTest Labs).

## <a name="why-isnt-my-existing-virtual-network-saving-properly"></a>¿Por qué mi máquina virtual existente no se guarda correctamente?
Una posibilidad es que el nombre de la red virtual contenga puntos. Si es así, intente quitar los puntos o reemplazarlos con guiones. A continuación, vuelva a intentar guardar la red virtual.

## <a name="why-do-i-get-a-parent-resource-not-found-error-when-i-provision-a-vm-from-powershell"></a>¿Por qué aparece el error "No se encuentra el recurso primario" al aprovisionar una máquina virtual desde PowerShell?
Cuando un recurso es un elemento primario de otro recurso, el primario debe existir antes de crear el secundario. Si el recurso primario no existe, verá un mensaje **ParentResourceNotFound**. Si no se especifica una dependencia del recurso primario, es posible que el recurso secundario se implemente antes que el primario.

Las máquinas virtuales son recursos secundarios en un laboratorio en un grupo de recursos. Cuando se usan plantillas de Resource Manager para la implementación de máquinas virtuales con PowerShell, el nombre del grupo de recursos proporcionado en el script de PowerShell debe ser el del grupo de recursos del laboratorio. Para más información, vea, [Solución de errores comunes de implementación de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-common-deployment-errors).

## <a name="where-can-i-find-more-error-information-if-a-vm-deployment-fails"></a>¿Dónde puedo encontrar más información sobre errores relativos a la implementación de VM?
Los errores de implementación de máquinas virtuales se capturan en los registros de actividad. Puede encontrar los registros de actividad de máquina virtual en laboratorio **registros de auditoría** o **diagnósticos de máquina Virtual** en el menú de recursos en la página de máquinas virtuales del laboratorio (aparece la página después de seleccionar la máquina virtual desde el **mi virtual las máquinas** lista).

En ocasiones, el error de implementación se produce antes de que comience la implementación de la máquina virtual. Un ejemplo es cuando se supera el límite de suscripciones para un recurso que se creó con la máquina virtual. En este caso, los detalles del error se capturan en los registros de actividad de nivel de laboratorio. Los registros de actividad se encuentran en la parte inferior de las opciones de **Configuración y directivas**. Para obtener más información sobre el uso de los registros de actividad en Azure, consulte [Visualización de registros de actividad para auditar las acciones sobre los recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
