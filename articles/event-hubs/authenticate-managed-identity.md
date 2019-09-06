---
title: Autenticación de una identidad administrada con Azure Active Directory
description: En este artículo se proporciona información sobre cómo autenticar una identidad administrada con Azure Active Directory para acceder a recursos de Azure Event Hubs.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
manager: ''
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: cbd7de7d526e1954aaad60f7d71e5cdf202f6a29
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992460"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-event-hubs-resources"></a>Autenticación de una identidad administrada con Azure Active Directory para acceder a recursos de Event Hubs
Azure Event Hubs admite la autenticación de Azure Active Directory (Azure AD) con [identidades administradas para los recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md). Las identidades administradas para recursos de Azure pueden autorizar el acceso a los recursos de Event Hubs con credenciales de Azure AD desde aplicaciones que se ejecutan en máquinas virtuales (VM) de Azure, aplicaciones de función, conjuntos de escalado de máquinas virtuales y otros servicios. Si usa identidades administradas para recursos de Azure junto con autenticación de Azure AD, puede evitar el almacenamiento de credenciales con las aplicaciones que se ejecutan en la nube.

En este artículo se muestra cómo autorizar el acceso a un centro de eventos con una identidad administrada desde una máquina virtual de Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Habilitación de identidades administradas en una máquina virtual
Para poder usar identidades administradas para recursos de Azure a fin de autorizar los recursos de Event Hubs desde la máquina virtual, primero debe habilitar las identidades administradas para los recursos de Azure en la máquina virtual. Para aprender a habilitar las identidades administradas para los recursos de Azure, consulte uno de estos artículos:

- [Azure Portal](../active-directory/managed-service-identity/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [CLI de Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Plantilla de Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Bibliotecas cliente de Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Concesión de permisos a una identidad administrada en Azure AD
Para autorizar una solicitud al servicio Event Hubs desde una identidad administrada de la aplicación, primero configure los valores del control de acceso basado en roles (RBAC) de esa identidad administrada. Azure Event Hubs define roles RBAC que abarcan los permisos para enviar y leer desde Event Hubs. Cuando el rol RBAC se asigna a una identidad administrada, a esta se le concede acceso a los datos de Event Hubs en el ámbito adecuado.

Para más información sobre la asignación de roles RBAC, consulte [Autenticación con Azure Active Directory para el acceso a recursos de Event Hubs](authorize-access-azure-active-directory.md).

## <a name="use-event-hubs-with-managed-identities"></a>Uso de Event Hubs con identidades administradas
Para usar Event Hubs con identidades administradas, debe asignar la identidad del rol y el ámbito adecuado. En el procedimiento de esta sección se usa una aplicación sencilla que se ejecuta en una identidad administrada y accede a los recursos de Event Hubs.

A continuación, usaremos una aplicación web de ejemplo hospedada en [Azure App Service](https://azure.microsoft.com/services/app-service/). Para instrucciones paso a paso sobre cómo crear una aplicación web, consulte [Creación de una aplicación web de ASP.NET Core en Azure](../app-service/app-service-web-get-started-dotnet.md).

Una vez creada la aplicación, siga estos pasos: 

1. Vaya a **Configuración** y seleccione **Identidad**. 
1. Seleccione el **Estado** que va a estar **Activado**. 
1. Seleccione **Guardar** para guardar la configuración. 

    ![Identidad administrada para una aplicación web](./media/authenticate-managed-identity/identity-web-app.png)

Una vez habilitada esta configuración, se crea una identidad de servicio en Azure Active Directory (Azure AD) y se configura en el host de App Service.

Ahora, asigne esta identidad de servicio a un rol en el ámbito requerido en los recursos de Event Hubs.

### <a name="to-assign-rbac-roles-using-the-azure-portal"></a>Para asignar roles RBAC mediante Azure Portal
Para asignar un rol a los recursos de Event Hubs, vaya a ese recurso en Azure Portal. Acceda a la configuración Access Control (IAM) del recurso y siga estas instrucciones para administrar las asignaciones de roles:

> [!NOTE]
> En los pasos siguientes se asigna un rol de identidad de servicio a los espacios de nombres de Event Hubs. Puede seguir los mismos pasos para asignar un rol con ámbito a cualquier recurso de Event Hubs. 

1. En Azure Portal, vaya al espacio de nombres de Event Hubs y muestre la **información general** del espacio de nombres. 
1. Seleccione **Access Control (IAM)** en el menú de la izquierda para mostrar la configuración de control de acceso para el centro de eventos.
1.  Seleccione la pestaña **Asignaciones de roles** para ver la lista de asignaciones de roles.
3.  Seleccione **Agregar** para agregar un rol nuevo.
4.  En la página **Agregar asignación de roles**, seleccione los roles de Event Hubs que quiere asignar. A continuación, busque la identidad del servicio que ha registrado para asignar el rol.
    
    ![Página Agregar asignación de roles](./media/authenticate-managed-identity/add-role-assignment-page.png)
5.  Seleccione **Guardar**. La identidad a la que ha asignado el rol aparece enumerada debajo de ese rol. Por ejemplo, la siguiente imagen muestra que la identidad del servicio tiene al propietario de los datos de Event Hubs.
    
    ![Identidad asignada a un rol](./media/authenticate-managed-identity/role-assigned.png)

Una vez que haya asignado el rol, la aplicación web tendrá acceso a los recursos de Event Hubs en el ámbito definido. 

### <a name="test-the-web-application"></a>Prueba de la aplicación web
Ahora puede iniciar la aplicación web y apuntar el explorador a la página aspx de ejemplo. Puede encontrar la aplicación web de ejemplo que envía y recibe datos de los recursos de Event Hubs en el [repositorio de GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp).

Instale el paquete más reciente de [NuGet](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) y empiece a enviar y recibir datos de Event Hubs mediante EventHubClient, tal y como se muestra en el código siguiente: 

```csharp
var ehClient = EventHubClient.CreateWithManagedIdentity(new Uri($"sb://{EventHubNamespace}/"), EventHubName);
```

## <a name="next-steps"></a>Pasos siguientes
- Descargue el [ejemplo](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac/ManagedIdentityWebApp) de GitHub.
- Consulte el artículo siguiente para información sobre las identidades administradas para los recursos de Azure: [¿Qué son las identidades administradas de los recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md)
- Consulte los artículos relacionados siguientes:
    - [Autenticación de solicitudes en Azure Event Hubs desde una aplicación mediante Azure Active Directory](authenticate-application.md)
    - [Autenticación de solicitudes para Azure Event Hubs mediante firmas de acceso compartido](authenticate-shared-access-signature.md)
    - [Autorización del acceso a recursos de Event Hubs mediante Azure Active Directory](authorize-access-azure-active-directory.md)
    - [Autorización del acceso a recursos de Event Hubs mediante firmas de acceso compartido](authorize-access-shared-access-signature.md)