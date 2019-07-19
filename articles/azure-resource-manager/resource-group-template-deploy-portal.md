---
title: Uso de Azure Portal para implementar los recursos de Azure | Microsoft Docs
description: Utilice el Portal de Azure y Azure Resource Manager para implementar los recursos.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: tomfitz
ms.openlocfilehash: a171d9b4f054c942eebb08e7e11dd1164545f408
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460306"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-portal"></a>Implementación de recursos con las plantillas de Resource Manager y el Portal de Azure

Aprenda a usar [Azure Portal](https://portal.azure.com) con [Azure Resource Manager](resource-group-overview.md) para administrar los recursos de Azure. Para más información sobre cómo administrar los recursos, consulte [Administración de recursos de Azure con Azure Portal](manage-resources-portal.md).

La implementación de recursos de Azure mediante Azure Portal implica normalmente dos pasos:

- Cree un grupo de recursos.
- Implemente recursos en el grupo de recursos.

Además, también puede implementar una plantilla de Azure Resource Manager para crear recursos de Azure.

En el artículo se muestran ambos métodos.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

1. Para crear un grupo de recursos, seleccione **Grupos de recursos** en [Azure Portal](https://portal.azure.com).

   ![Seleccionar Grupos de recursos](./media/resource-group-template-deploy-portal/select-resource-groups.png)

1. En Grupo de recursos, seleccione **Agregar**.

   ![Agregar un grupo de recursos](./media/resource-group-template-deploy-portal/add-resource-group.png)

1. Seleccione o escriba los siguientes valores de propiedad:

    - **Suscripción**: Seleccione una suscripción de Azure.
    - **Grupo de recursos**: asigne un nombre al grupo de recursos.
    - **Región**: seleccione una ubicación de Azure. Aquí es donde los grupos de recursos almacenan los metadatos sobre los recursos. Por motivos de cumplimiento, debería especificar dónde se almacenan esos metadatos. Por lo general, se recomienda especificar una ubicación en la que vayan a residir la mayoría de los recursos. Si usa la misma ubicación, puede simplificar la plantilla.

   ![Establecer valores de grupo](./media/resource-group-template-deploy-portal/set-group-properties.png)

1. Seleccione **Revisar + crear**.
1. Revise los valores y, luego, seleccione **Crear**.
1. Para poder ver el nuevo grupo de recursos en la lista, seleccione **Actualizar**.

## <a name="deploy-resources-to-a-resource-group"></a>Implementación de recursos en un grupo de recursos

Una vez creado el grupo de recursos, puede implementar recursos en él desde Marketplace. Marketplace proporciona soluciones predefinidas para escenarios habituales.

1. Para iniciar una implementación, seleccione **Crear un recurso** en [Azure Portal](https://portal.azure.com).

   ![Nuevo recurso](./media/resource-group-template-deploy-portal/new-resources.png)

1. Busque el tipo de recurso que quiera implementar. Los recursos se organizan en categorías. Si no ve la solución específica que gustaría implementar, búsquela en Marketplace. En la siguiente captura de pantalla se muestra que se ha seleccionado Ubuntu Server.

   ![Selección del tipo de recurso](./media/resource-group-template-deploy-portal/select-resource-type.png)

1. Según el tipo del recurso seleccionado, tiene una colección de propiedades pertinentes que debe establecer antes de la implementación. Para todos los tipos, debe seleccionar un grupo de recursos de destino. En la imagen siguiente se muestra cómo crear una máquina virtual Linux e implementarla en el grupo de recursos que ha creado.

   ![Creación de un grupo de recursos](./media/resource-group-template-deploy-portal/select-existing-group.png)

   Como alternativa, puede decidir crear un nuevo grupo de recursos al implementar estos últimos. Seleccione **Crear nuevo** y asígnele un nombre al grupo de recursos.

1. Comenzará la implementación. Esta operación puede tardar varios minutos. Algunos recursos tardan más que otros. Cuando haya terminado la implementación, verá una notificación. Seleccione **Ir a recurso**.

   ![Ver la notificación](./media/resource-group-template-deploy-portal/view-notification.png)

1. Después de implementar los recursos, puede agregar más recursos al grupo de recursos si hace clic en **Agregar**.

   ![Agregar recurso](./media/resource-group-template-deploy-portal/add-resource.png)

## <a name="deploy-resources-from-custom-template"></a>Implementación de recursos desde plantilla personalizada

Si desea ejecutar una implementación sin usar las plantillas de Marketplace, puede crear una plantilla personalizada que defina la infraestructura para la solución. Para más información sobre cómo crear plantillas, vea [Nociones sobre la estructura y la sintaxis de las plantillas de Azure Resource Manager](resource-group-authoring-templates.md).

> [!NOTE]
> La interfaz del portal no admite referencias a un [secreto desde Key Vault](resource-manager-keyvault-parameter.md). En su lugar, use [PowerShell](resource-group-template-deploy.md) o la [CLI de Azure](resource-group-template-deploy-cli.md) para implementar la plantilla localmente o desde un URI externo.

1. Para implementar una plantilla personalizada mediante el portal, seleccione **Crear un recurso** y busque **plantilla**. Luego, seleccione **Implementación de plantillas**.

   ![Buscar la implementación de plantilla](./media/resource-group-template-deploy-portal/search-template.png)

1. Seleccione **Crear**.
1. Tiene varias opciones para crear una plantilla:

    - **Crear su propia plantilla en el editor**: cree una plantilla mediante el editor de plantillas del portal.  El editor tiene la capacidad para agregar un esquema de plantilla de recursos.
    - **Plantillas comunes**: hay cuatro plantillas comunes para crear una máquina virtual Linux, una máquina virtual Windows, una aplicación web y una base de datos de Azure SQL.
    - **Cargar una plantilla de inicio rápido de GitHub**: use una [plantilla de inicio rápido](https://azure.microsoft.com/resources/templates/) existente.

   ![Ver las opciones](./media/resource-group-template-deploy-portal/see-options.png)

    En este tutorial se proporcionan las instrucciones para cargar una plantilla de inicio rápido.

1. En **Cargar una plantilla de inicio rápido de GitHub**, escriba o seleccione **101-storage-account-create**.

    Tiene dos opciones:

    - **Seleccionar plantilla**: implemente la plantilla.
    - **Editar plantilla**: edite la plantilla de inicio rápido antes de implementarla.

1. Seleccione **Editar plantilla** para explorar el editor de plantillas del portal. La plantilla se carga en el editor. Observe que hay dos parámetros: **storageAccountType** y **location**.

   ![Creación de una plantilla](./media/resource-group-template-deploy-portal/show-json.png)

1. Realice un cambio menor en la plantilla. Por ejemplo, actualice la variable **storageAccountName** a:

    ```json
    "storageAccountName": "[concat('azstore', uniquestring(resourceGroup().id))]"
    ```

1. Seleccione **Guardar**. Ahora verá la interfaz de implementación de plantillas del portal. Observe los dos parámetros que definió en la plantilla.
1. Escriba o seleccione los valores de propiedad:

    - **Suscripción**: Seleccione una suscripción de Azure.
    - **Grupo de recursos**: seleccione **Crear nuevo** y asígnele un nombre.
    - **Ubicación**: Seleccione una ubicación de Azure.
    - **Tipo de cuenta de almacenamiento**: Use el valor predeterminado.
    - **Ubicación**: Use el valor predeterminado.
    - **Acepto los términos y condiciones indicados anteriormente**: (seleccionar)

1. Seleccione **Comprar**.

## <a name="next-steps"></a>Pasos siguientes

- Para ver los registros de auditoría, consulte [Operaciones de auditoría con Resource Manager](./resource-group-audit.md).
- Para solucionar errores de implementación, vea [View deployment operations](./resource-manager-deployment-operations.md) (Ver operaciones de implementación).
- Para exportar una plantilla de una implementación o un grupo de recursos, consulte [Exportación de plantillas de Azure Resource Manager](./manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Para la implementación segura del servicio entre varias regiones, consulte [Azure Deployment Manager](./deployment-manager-overview.md).
