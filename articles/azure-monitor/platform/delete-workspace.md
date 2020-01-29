---
title: Eliminación y recuperación de un área de trabajo de Azure Log Analytics | Microsoft Docs
description: Obtenga información para eliminar el área de trabajo de Log Analytics si creó uno en una suscripción personal o reestructure el modelo de área de trabajo.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: 03be29cde42478abf32492f55a296aeee0a4a478
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76547258"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Eliminación y restauración de un área de trabajo de Azure Log Analytics

En este artículo se explica el concepto de eliminación temporal de áreas de trabajo de Azure Log Analytics y cómo recuperarlas. 

## <a name="considerations-when-deleting-a-workspace"></a>Consideraciones al eliminar áreas de trabajo

Al eliminar un área de trabajo de Log Analytics, se realiza una operación de eliminación temporal para que se pueda recuperar el área de trabajo, incluidos los datos y los agentes conectados en un plazo de 14 días, tanto si la eliminación fue accidental como intencionada. Después del período de eliminación temporal, el recurso del área de trabajo y sus datos no podrán recuperarse: los datos se pondrán en cola para su eliminación permanente en un plazo de 30 días. El nombre del área de trabajo es "released" y puede usarlo para crear una nueva área de trabajo.

> [!NOTE]
> El comportamiento de eliminación temporal no se puede desactivar. En breve, se agregará una opción para invalidar la eliminación temporal al usar una etiqueta "force" en la operación de eliminación.

Debe tener cuidado al eliminar un área de trabajo porque puede haber datos importantes y una configuración que puede afectar negativamente a las operaciones de servicio. Revise qué agentes, soluciones y otros servicios y orígenes de Azure almacenan sus datos en Log Analytics. Por ejemplo:

* Soluciones de administración
* Azure Automation
* Agentes que se ejecutan en máquinas virtuales Windows y Linux
* Agentes que se ejecutan en equipos Windows y Linux en su entorno
* System Center Operations Manager

La operación de eliminación temporal quita el recurso del área de trabajo y los permisos de los usuarios asociados. Si los usuarios están asociados a otras áreas de trabajo, podrán seguir utilizando Log Analytics con esas otras áreas de trabajo.

## <a name="soft-delete-behavior"></a>Comportamiento de eliminación temporal

La operación de eliminación del área de trabajo quita el recurso de Resource Manager del área de trabajo, pero su configuración y sus datos se conservan durante 14 días (y da la sensación de que el área de trabajo se ha eliminado). Los agentes y grupos de administración de System Center Operations Manager configurados para enviar notificaciones al área de trabajo continúan en un estado huérfano durante el período de eliminación temporal. El servicio proporciona además un mecanismo para recuperar el área de trabajo eliminada (incluidos sus datos y recursos conectados), básicamente, deshaciendo la eliminación.

> [!NOTE] 
> Tanto las soluciones instaladas como los servicios vinculados, como su cuenta de Azure Automation, se quitan permanentemente del área de trabajo en el momento de la eliminación y no se pueden recuperar. Deben volver a configurarse después de la operación de recuperación para que el área de trabajo vuelva al estado en que estaba configurado anteriormente.

Las áreas de trabajo se pueden eliminar un mediante [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [API REST](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) o en [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Portal de Azure

1. Para iniciar sesión, vaya a [Azure Portal](https://portal.azure.com). 
2. En Azure Portal, seleccione **Todos los servicios**. En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Seleccione **Áreas de trabajo de Log Analytics**.
3. En la lista de áreas de trabajo de Log Analytics, seleccione un área de trabajo y, a continuación, haga clic en **Eliminar** en la parte superior del panel intermedio.
   ![Opción Eliminar desde el panel de propiedades del área de trabajo](media/delete-workspace/log-analytics-delete-workspace.png)
4. Cuando aparezca de la ventana de mensaje de confirmación que le solicite que confirme la eliminación del área de trabajo, haga clic en **Sí**.
   ![Confirmación de la eliminación del área de trabajo](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Eliminación permanente del área de trabajo
Es posible que el método de eliminación temporal no encaje en algunos escenarios como el desarrollo y las pruebas, donde es necesario repetir una implementación con la misma configuración y el mismo nombre de área de trabajo. En tales casos, puede eliminar el área de trabajo de forma permanente e "invalidar" el período de eliminación temporal. La operación de eliminación permanente del área de trabajo libera el nombre del área de trabajo y puede crear una nueva área de trabajo con el mismo nombre.


> [!IMPORTANT]
> Tenga cuidado al eliminar permanentemente el área de trabajo, ya que la operación es irreversible y el área de trabajo y sus datos no se podrán recuperar.

La eliminación permanente del área de trabajo se puede realizar actualmente mediante la API REST.

> [!NOTE]
> Cualquier solicitud de API debe incluir un token de autorización de portador en el encabezado de la solicitud.
>
> Puede adquirir el token mediante:
> - [Registros de aplicaciones](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Vaya a Azure Portal mediante la consola del desarrollador (F12) en el explorador. Busque en una de las instancias de **batch?** la cadena de autenticación en **Encabezados de la solicitud**. Estará en el patrón *autorización: portador <token>* . Copie y agregue esto a la llamada a la API tal y como se muestra en los ejemplos.
> - Vaya al sitio de documentación de REST de Azure. Presione **Pruébelo** en cualquier API, copie el token de portador y agréguelo a la llamada a la API.
Para eliminar el área de trabajo de forma permanente, use la llamada a la API [Áreas de trabajo: eliminación REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) con una etiqueta de forzado:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```

## <a name="recover-workspace"></a>Recuperación de áreas de trabajo

Si tiene permisos de colaborador en la suscripción y en el grupo de recursos a los que estaba asociada el área de trabajo antes de la operación de eliminación temporal, puede recuperarla durante el periodo de dicha eliminación, incluidos sus datos, configuración y agentes conectados. Después del período de eliminación temporal, el área de trabajo no podrá recuperarse y se pondrá en cola para su eliminación permanente. Los nombres de las áreas de trabajo eliminadas se conservan durante el periodo de eliminación temporal y no se pueden usar al intentar crear un área de trabajo nueva.  

Para recuperar un área de trabajo, puede volver a crearla con los siguientes métodos de creación de áreas de trabajo: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) o [API REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate), siempre que se rellenen las siguientes propiedades con los detalles del área de trabajo eliminada:

* Id. de suscripción
* Nombre del grupo de recursos
* Nombre del área de trabajo
* Region

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

El área de trabajo y todos sus datos se restauran después de la operación de recuperación. Las soluciones y los servicios vinculados se quitaron permanentemente del área de trabajo cuando se eliminó y se deben volver a configurar para restaurarla a su estado configurado anterior. Puede que algunos de los datos no estén disponibles para la consulta después de la recuperación del área de trabajo hasta que se vuelvan a instalar las soluciones asociadas y se agreguen sus esquemas al área de trabajo.

> [!NOTE]
> * Desde [Azure Portal](https://portal.azure.com) no se pueden recuperar áreas de trabajo. 
> * Al volver a crear un área de trabajo durante el período de eliminación temporal, se indica que este nombre de área de trabajo ya está en uso. 
> 
