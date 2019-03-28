---
title: Identidades administradas de recursos de Azure
description: Información general sobre Managed Identities for Azure Resources.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/23/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cbcab0d287f344d308e3ed51ae47087afae7f9e
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/26/2019
ms.locfileid: "58449264"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>¿Qué es Managed Identities for Azure Resources?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Un desafío común al compilar aplicaciones en la nube consiste en el modo de administrar las credenciales del código para autenticar los servicios en la nube. Proteger las credenciales es una tarea esencial. Lo ideal sería que nunca aparecieran en las estaciones de trabajo de los desarrolladores y que no se controlaran en el código fuente. Azure Key Vault proporciona una manera segura de almacenar credenciales, secretos y otras claves pero el código tiene que autenticarse en Key Vault para recuperarlos. 

Las identidades administradas para recursos de Azure con las que cuenta Azure Active Directory (Azure AD) resuelven este problema. Esta característica proporciona a los servicios de Azure una identidad de sistema administrada automáticamente en Azure AD. Puede usar esta identidad para autenticarse en cualquier servicio que admita la autenticación de Azure AD, incluido Key Vault, sin necesidad de credenciales en el código.

La característica Managed Identities for Azure Resources se incluye gratuitamente en Azure AD con las suscripciones de Azure. No hay ningún costo adicional.

> [!NOTE]
> Identidades administradas para recursos de Azure es el nombre con el que ahora se conoce al servicio Managed Service Identity (MSI).

## <a name="terminology"></a>Terminología

Los siguientes términos se usan en las identidades administradas para el conjunto de documentación de los recursos de Azure:

- **Id. de cliente**: un identificador único que genera Azure AD y que está asociado a una aplicación y entidad de servicio durante su aprovisionamiento inicial.
- **Id. de entidad de seguridad**: identificador de objeto del objeto de entidad de servicio de la identidad administrada que se usa para conceder acceso basado en roles a los recursos de Azure.
- **Azure Instance Metadata Service (IMDS)**: un punto de conexión de REST al que pueden acceder todas las máquinas virtuales IaaS creadas mediante Azure Resource Manager. El punto de conexión está disponible en una dirección IP no enrutable conocida (169.254.169.254) a la que se puede acceder solo desde dentro de la máquina virtual.

## ¿Cómo funcionan las identidades administradas para los recursos de Azure?<a name="how-does-it-work"></a>

Hay dos tipos de identidades administradas:

- Las **identidades administradas asignadas por el sistema** se habilitan directamente en las instancias de servicio de Azure. Cuando se habilita la identidad, Azure crea una identidad para la instancia del servicio en el inquilino de Azure AD de confianza de la suscripción de la instancia. Una vez creada la identidad, las credenciales se aprovisionan en la instancia. El ciclo de vida de una identidad administrada asignada por el sistema está vinculado directamente a la instancia de servicio de Azure en que está habilitada. Si se elimina la instancia, Azure limpia automáticamente las credenciales y la identidad en Azure AD.
- Las **identidades administrada asignadas por el usuario** se crean como recursos de Azure independientes. Mediante un proceso de creación, Azure crea una identidad en el inquilino de Azure AD de confianza para la suscripción que se utiliza. Una vez creada la identidad, esta puede asignarse a una o varias instancias de servicio de Azure. El ciclo de vida de una identidad asignada por el usuario no se administra junto con el ciclo de vida de las instancias de servicio de Azure a las que se asigna.

El código puede usar una identidad administrada para solicitar tokens de acceso de los servicios que admiten la autenticación de Azure AD. Azure se encarga de rotar las credenciales que usa la instancia del servicio.

En el diagrama siguiente se muestra cómo funcionan las identidades de servicio administradas con máquinas virtuales (VM) de Azure:

![Identidades de Managed Service Identity y máquinas virtuales de Azure](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Funcionamiento de una identidad administrada asignada por el sistema con una máquina virtual de Azure

1. Azure Resource Manager recibe una solicitud para habilitar la identidad administrada asignada por el sistema de una máquina virtual.
2. Entonces crea una entidad de servicio en Azure AD para la identidad de la máquina virtual. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para la suscripción.
3. Azure Resource Manager configura la identidad en la máquina virtual:
    1. Actualiza el punto de conexión de la identidad de Azure Instance Metadata Service con el identificador de cliente de la entidad de servicio y el certificado.
    1. Aprovisiona la extensión de máquina virtual (plan de desuso previsto para enero de 2019), y agrega el identificador de cliente de la entidad de servicio y el certificado. (Está previsto que este paso se ponga en desuso).
4. Ahora que la máquina virtual tiene una identidad, se usa la información de la entidad de servicio para conceder a la máquina virtual acceso a los recursos de Azure. Para llamar a Azure Resource Manager, use el control de acceso basado en rol (RBAC) de Azure AD para asignar el rol apropiado a la entidad de servicio de la máquina virtual. Para llamar a Key Vault, conceda a su código acceso al secreto o a la clave específicos en Key Vault.
5. El código que se ejecuta en la máquina virtual puede solicitar un token del punto de conexión de Azure Instance Metadata Service, accesible únicamente desde dentro de la máquina virtual: `http://169.254.169.254/metadata/identity/oauth2/token`
    - El parámetro del recurso especifica el servicio al que se va a enviar el token. Para autenticarse en Azure Resource Manager, use `resource=https://management.azure.com/`.
    - El parámetro de versión de API especifica la versión de IMDS, use api-version=2018-02-01 o posterior.

> [!NOTE]
> El código también puede solicitar un token del punto de conexión de extensión de máquina virtual, pero está previsto que pronto esté en desuso. Para más información sobre la extensión de máquina virtual, consulte [Migración de la extensión de máquina virtual a IMDS de Azure para la autenticación](howto-migrate-vm-extension.md).

6. Se realiza una llamada a Azure AD para solicitar un token de acceso, tal y como se especifica en el paso 5, con el identificador de cliente y el certificado configurado en el paso 3. Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Funcionamiento de una identidad administrada asignada por el usuario con una máquina virtual de Azure

1. Azure Resource Manager recibe una solicitud para crear una identidad administrada asignada por el usuario.
2. Luego crea una entidad de servicio en Azure AD para la identidad asignada por el usuario. La entidad de servicio se crea en el inquilino de Azure AD que sea de confianza para la suscripción.
3. Azure Resource Manager recibe una solicitud para configurar la identidad administrada asignada por el usuario en una máquina virtual:
    1. Actualiza el punto de conexión de la identidad de Azure Instance Metadata Service con el identificador de cliente de la entidad de servicio administrada asignada por el usuario y el certificado.
    1. Aprovisiona la extensión de la máquina virtual y agrega el identificador de cliente de la entidad de servicio de la identidad administrada asignada por el usuario y el certificado. (Está previsto que este paso se ponga en desuso).
4. Una vez que se ha creado la identidad administrada asignada por el usuario, se usa la información de la entidad de servicio para conceder a la identidad acceso a los recursos de Azure. Para llamar a Azure Resource Manager, use el control de acceso basado en rol (RBAC) de Azure AD para asignar el rol apropiado a la entidad de servicio de la identidad asignada por el usuario. Para llamar a Key Vault, conceda a su código acceso al secreto o a la clave específicos en Key Vault.

   > [!Note]
   > También puede realizar este paso antes del paso 3.

5. El código que se ejecuta en la máquina virtual puede solicitar un token del punto de conexión de identidad de Azure Instance Metadata Service, accesible únicamente desde dentro de la máquina virtual: `http://169.254.169.254/metadata/identity/oauth2/token`.
    - El parámetro del recurso especifica el servicio al que se va a enviar el token. Para autenticarse en Azure Resource Manager, use `resource=https://management.azure.com/`.
    - El parámetro de identificador de cliente especifica la identidad para la que se solicita el token. Este valor es necesario para eliminar la ambigüedad cuando hay varias identidades asignadas por el usuario en la misma máquina virtual.
    - El parámetro de versión de API especifica la versión de Azure Instance Metadata Service. Use `api-version=2018-02-01` o superior.

> [!NOTE]
> El código también puede solicitar un token del punto de conexión de extensión de máquina virtual, pero está previsto que pronto esté en desuso. Para más información sobre la extensión de máquina virtual, consulte [Migración de la extensión de máquina virtual a IMDS de Azure para la autenticación](howto-migrate-vm-extension.md).

6. Se realiza una llamada a Azure AD para solicitar un token de acceso, tal y como se especifica en el paso 5, con el identificador de cliente y el certificado configurado en el paso 3. Azure AD devuelve un token de acceso JSON Web Token (JWT).
7. El código envía el token de acceso en una llamada a un servicio que admite la autenticación de Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>¿Cómo se usan las identidades administradas de Managed Identities for Azure Resources?

Si quiere aprender a usar las identidades administradas para acceder a diferentes recursos de Azure, pruebe estos tutoriales.

> [!NOTE]
> Consulte el curso [Implementing Managed Identities for Microsoft Azure Resources](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) (Implementación de identidades administradas para los recursos de Microsoft Azure) para más información acerca de las identidades administradas, además de tutoriales en vídeo de varios escenarios admitidos.

Aprenda a usar una identidad administrada con una máquina virtual Windows:

* [Acceso a Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Acceso a Azure Resource Manager](tutorial-windows-vm-access-arm.md)
* [Acceso a Azure SQL](tutorial-windows-vm-access-sql.md)
* [Acceso a Azure Storage mediante una clave de acceso](tutorial-windows-vm-access-storage.md)
* [Acceso a Azure Storage mediante firmas de acceso compartido](tutorial-windows-vm-access-storage-sas.md)
* [Acceso a un recurso que no es de Azure AD con Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Aprenda a usar una identidad administrada con una máquina virtual Linux:

* [Acceso a Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Acceso a Azure Resource Manager](tutorial-linux-vm-access-arm.md)
* [Acceso a Azure Storage mediante una clave de acceso](tutorial-linux-vm-access-storage.md)
* [Acceso a Azure Storage mediante firmas de acceso compartido](tutorial-linux-vm-access-storage-sas.md)
* [Acceso a un recurso que no es de Azure AD con Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Aprenda a usar una identidad administrada con otros servicios de Azure:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)

## ¿Qué servicios de Azure admiten la característica?<a name="which-azure-services-support-managed-identity"></a>

Las identidades administradas de Managed Identities for Azure Resources se pueden usar para autenticarse en servicios que admiten la autenticación de Azure AD. Para ver una lista de los servicios de Azure que admiten la característica Managed Identities for Azure Resources, consulte [Services that support managed identities for Azure resources](services-support-msi.md) (Servicios que admiten la característica Managed Identities for Azure Resources).

## <a name="next-steps"></a>Pasos siguientes

Empiece a utilizar la característica Managed Identities for Azure Resources con las guías de inicio rápido siguientes:

* [Uso de las identidades administradas asignadas por el sistema de una máquina virtual Windows para acceder a Resource Manager](tutorial-windows-vm-access-arm.md)
* [Uso de las identidades administradas asignadas por el sistema de una máquina virtual Linux para acceder a Resource Manager](tutorial-linux-vm-access-arm.md)
