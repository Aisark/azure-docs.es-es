---
title: 'Azure Databricks: Preguntas comunes y ayuda'
description: Obtenga respuestas a las preguntas comunes e información para solucionar problemas acerca de Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 3bcc511ec6ad8a246c2b1b3a33eb59043a45830e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60784714"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Preguntas más frecuentes sobre Azure Databricks

Este artículo muestra las principales preguntas que pueden surgir en relación con Azure Databricks. También se muestran algunos problemas comunes que puede encontrar al usar Databricks. Para más información, consulte [¿Qué es Azure Databricks?](what-is-azure-databricks.md) 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>¿Puedo usar Azure Key Vault para almacenar claves o secretos que se usarán en Azure Databricks?
Sí. Puede usar Azure Key Vault para almacenar claves o secretos que se usarán en Azure Databricks. Para más información, consulte el artículo sobre los [ámbitos respaldados por Azure Key Vault](https://docs.azuredatabricks.net/user-guide/secrets/secret-scopes.html#akv-ss).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>¿Puedo usar Azure Virtual Networks con Databricks?
Sí. Puede usar una instancia de Azure Virtual Network (red virtual) con Azure Databricks. Para más información, consulte el artículo sobre la [implementación de Azure Databricks en Azure Virtual Network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-inject.html).

## <a name="how-do-i-access-azure-data-lake-store-from-a-notebook"></a>¿Cómo accedo a Azure Data Lake Store desde un bloc de notas? 

Siga estos pasos:
1. En Azure Active Directory (Azure AD), aprovisione una entidad de servicio y registre su clave.
1. Asigne los permisos necesarios a la entidad de servicio en Data Lake Store.
1. Para acceder a un archivo en Data Lake Store, use las credenciales de la entidad de servicio en el Bloc de notas.

Para más información, vea [Use Data Lake Store with Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) (Uso de Data Lake Store con Azure Databricks).

## <a name="fix-common-problems"></a>Corrección de problemas comunes

Estos son algunos de los problemas que podría encontrar con Databricks.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problema: Esta suscripción no está registrada para usar el espacio de nombres "Microsoft.Databricks"

#### <a name="error-message"></a>Mensaje de error

"Esta suscripción no está registrada para usar el espacio de nombres "Microsoft.Databricks". Consulte https://aka.ms/rps-not-found para saber cómo registrar las suscripciones. (Código: MissingSubscriptionRegistration)"

#### <a name="solution"></a>Solución

1. Vaya a [Azure Portal](https://portal.azure.com).
1. Seleccione **Suscripciones**, la suscripción que usa y, a continuación, **Proveedores de recursos**. 
1. En la lista de proveedores de recursos, en **Microsoft.Databricks**, seleccione **Registrar**. Debe tener el rol colaborador o propietario de la suscripción para registrar el proveedor de recursos.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problema: La cuenta {correo electrónico} no tiene el rol propietario o colaborador en el recurso de área de trabajo de Databricks en Azure Portal

#### <a name="error-message"></a>Mensaje de error

"La cuenta {correo electrónico} no tiene el rol Propietario o Colaborador en el recurso de área de trabajo de Databricks en Azure Portal. Este error puede producirse también si es un usuario invitado en el inquilino. Pida al administrador que le conceda acceso o le agregue como un usuario directamente en el área de trabajo de Databricks". 

#### <a name="solution"></a>Solución

A continuación se muestra un par de soluciones para este problema:

* Para inicializar el inquilino, debe haber iniciado sesión como un usuario normal del inquilino, no como un usuario invitado. También debe tener el rol colaborador en el recurso de área de trabajo de Databricks. Puede conceder acceso de usuario desde la pestaña **Control de acceso (IAM)** en el área de trabajo de Databricks en Azure Portal.

* Este error también puede producirse si el nombre de dominio de correo electrónico se asigna a varios directorios de Azure AD. Para solucionar este problema, cree un nuevo usuario en el directorio que contiene la suscripción al área de trabajo de Databricks.

     a. En Azure Portal, vaya a Azure AD. Seleccione **Usuarios y grupos** > **Agregar un usuario**.

    b. Agregue un usuario con un correo electrónico de `@<tenant_name>.onmicrosoft.com` en lugar de `@<your_domain>`. Puede encontrar esta opción en **Dominios personalizados**, en Azure AD de Azure Portal.
    
    c. Conceda a este nuevo usuario el rol **Colaborador** en el recurso del área de trabajo de Databricks.
    
    d. Inicie sesión en Azure Portal con el nuevo usuario y busque el área de trabajo de Databricks.
    
    e. Inicie el área de trabajo de Databricks con este usuario.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problema: La cuenta {correo electrónico} no se ha registrado en Databricks 

#### <a name="solution"></a>Solución

Si no creó el área de trabajo y se le agrega como usuario, póngase en contacto con la persona que creó el área de trabajo. Pídale a esa persona que lo agregue mediante la Consola de administración de Azure Databricks. Para obtener instrucciones, vea [Adding and managing users](https://docs.azuredatabricks.net/administration-guide/admin-settings/users.html) (Adición y administración de usuarios). Si creó el área de trabajo y sigue recibiendo este error, intente volver a seleccionar **Inicializar área de trabajo** en Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problema: Error al iniciar el proveedor de nube al configurar el clúster (PublicIPCountLimitReached)

#### <a name="error-message"></a>Mensaje de error

"Error al iniciar el proveedor de nube: Se encontró un error de proveedor en la nube al configurar el clúster. Para más información, vea la guía de Databricks. Código de error de Azure: PublicIPCountLimitReached. Mensaje de error de Azure: No se puede crear más de 60 direcciones IP públicas para esta suscripción en esta región".

#### <a name="solution"></a>Solución

Los clústeres de Databricks usan una dirección IP pública por nodo. Si la suscripción ya ha utilizado todas sus direcciones IP públicas, debe [solicitar aumentar la cuota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request). Elija **cuota** como el **tipo de problema**, y **a redes: ARM** como el **tipo de cuota**. En **Details**, solicite un aumento de la cuota de la dirección IP pública. Por ejemplo, si el límite es actualmente 60 y desea crear un clúster de 100 nodos, solicite un aumento del límite hasta 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problema: Un segundo tipo de error de inicio de proveedor de nube al configurar el clúster (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Mensaje de error

"Error al iniciar el proveedor de nube: Se encontró un error de proveedor en la nube al configurar el clúster. Para más información, vea la guía de Databricks.
Código de error de Azure: Mensaje de error de MissingSubscriptionRegistration Azure: La suscripción no está registrada para usar el espacio de nombres "Microsoft.Compute". Consulte https://aka.ms/rps-not-found para saber cómo registrar las suscripciones".

#### <a name="solution"></a>Solución

1. Vaya a [Azure Portal](https://portal.azure.com).
1. Seleccione **Suscripciones**, la suscripción que usa y, a continuación, **Proveedores de recursos**. 
1. En la lista de proveedores de recursos, en **Microsoft.Compute**, seleccione **Registrar**. Debe tener el rol colaborador o propietario de la suscripción para registrar el proveedor de recursos.

Para instrucciones más detalladas, consulte [Tipos y proveedores de recursos](../azure-resource-manager/resource-manager-supported-services.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problema: Azure Databricks necesita permisos para tener acceso a recursos de la organización que sólo un administrador puede conceder.

#### <a name="background"></a>Fondo

Azure Databricks está integrado con Azure Active Directory. Se pueden establecer permisos dentro de Azure Databricks (por ejemplo, en cuadernos o clústeres) mediante la especificación de los usuarios de Azure AD. Para que Azure Databricks pueda generar una lista de los nombres de los usuarios de Azure AD, necesita permisos de lectura de dicha información, además de consentimiento. Si la autorización no está disponible aún, aparecerá el error.

#### <a name="solution"></a>Solución

Inicie sesión en Azure Portal como administrador global. En Azure Active Directory, vaya a la pestaña **Configuración de usuario** y asegúrese de que la opción **Los usuarios pueden permitir que las aplicaciones accedan a los datos de la compañía en su nombre** está establecida en **Sí**.

## <a name="next-steps"></a>Pasos siguientes

- [Inicio rápido: Introducción a Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [¿Qué es Azure Databricks?](what-is-azure-databricks.md)

