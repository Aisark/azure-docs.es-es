---
title: Crear, ver y administrar las alertas del registro de actividad en Azure Monitor
description: Crear alertas del registro de actividad mediante Azure Portal, una plantilla de Azure Resource Manager y Azure PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: vinagara
ms.openlocfilehash: a7f80698791831b3d4404ea0f687a75c660c2222
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67852741"
---
# <a name="create-view-and-manage-activity-log-alerts-by-using-azure-monitor"></a>Crear, ver y administrar las alertas del registro de actividad mediante Azure Monitor  

## <a name="overview"></a>Información general
Las alertas del registro de actividad son alertas que se activan cuando un nuevo evento del registro de actividad cumple las condiciones especificadas en la alerta.

Estas alertas son recursos de Azure, por lo que pueden crearse con una plantilla de Azure Resource Manager. También se pueden crear, actualizar o eliminar en Azure Portal. Normalmente es necesario crear alertas del registro de actividad para recibir una notificación si se producen cambios específicos en los recursos de la suscripción de Azure. Las alertas a menudo se limitan a grupos de recursos o recursos determinados. Por ejemplo, es posible que quiera recibir una notificación cuando se elimine cualquier máquina virtual del grupo de recursos de ejemplo **myProductionResourceGroup**. O bien, podría querer recibir una notificación si se asigna algún rol nuevo a un usuario de la suscripción.

> [!IMPORTANT]
> No se pueden crear alertas en la notificación de Service Health a través de la interfaz para la creación de alertas del registro de actividad. Para obtener más información acerca de cómo crear y usar las notificaciones de Service Health, consulte [Receive activity log alerts on service health notifications](alerts-activity-log-service-notifications.md) (Recibir alertas del registro de actividad con las notificaciones de Service Health).

Cuando cree reglas de alertas, asegúrese de lo siguiente:

- La suscripción del ámbito no es diferente de la suscripción donde se creó la alerta.
- La alerta se configurará según los siguientes tipos de criterios: nivel, estado, autor de la llamada, grupo de recursos, id. del recurso o categoría de eventos del tipo de recurso.
- No hay condición "anyOf" o condiciones anidadas en la configuración de alertas JSON. Básicamente, solo se permite una condición "allOf" sin más condiciones "allOf" o "anyOf".
- Cuando la categoría es "administrativo", debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se active cada vez que se crea un evento en los registros de actividad.


## <a name="azure-portal"></a>Portal de Azure

Puede usar Azure Portal para crear y modificar las reglas de alertas del registro de actividad. Asimismo, se integra la experiencia con el registro de actividad de Azure para garantizar la creación de alertas sin problemas para eventos específicos de interés.

### <a name="create-with-the-azure-portal"></a>Crear con Azure Portal

Use el siguiente procedimiento.

1. En Azure Portal, seleccione **Supervisar** > **Alertas**.
2. Seleccione **Nueva regla de alertas** en la esquina superior izquierda de la ventana **Alertas**.

     ![Nueva alerta de reglas](media/alerts-activity-log/AlertsPreviewOption.png)

     Aparece la ventana **Crear regla**.

      ![Opciones de la nueva regla de alertas](media/alerts-activity-log/create-new-alert-rule-options.png)

3. En **Definir condición de alertas**, proporcione la siguiente información y haga clic en **Hecho**:

   - **Destino de la alerta:** Para ver y seleccionar el destino de la nueva alerta, use **Filtrar por suscripción** / **Filtrar por tipo de recurso**. Seleccione el recurso o grupo de recursos de la lista que se muestra.

     > [!NOTE]
     > 
     > Puede seleccionar solo el recurso de seguimiento de [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md), el grupo de recursos o una suscripción completa de una señal del registro de actividad. 

     **Vista de ejemplo de la alerta de destino**

     ![Seleccionar destino](media/alerts-activity-log/select-target.png)

   - En **Criterios de destino**, seleccione **Agregar criterios**. Se muestran todas las señales disponibles del destino, que incluyen las de varias categorías del **registro de actividad**. El nombre de la categoría se agrega al nombre del **servicio de supervisión**.

   - Seleccione la señal de la lista que se muestra y que corresponde a varias operaciones posibles para el tipo **Registro de actividad**.

     Puede seleccionar la escala de tiempo del historial de registro y la lógica de la alerta correspondiente a esta señal de destino:

     **Pantalla para agregar criterios**

     ![Agregar criterios](media/alerts-activity-log/add-criteria.png)

     - **Hora del historial**: los eventos disponibles para la operación seleccionada se pueden trazar en las últimas 6, 12 o 24 horas o durante la última semana.

     - **Lógica de alerta**:

       - **Nivel de evento**: Nivel de gravedad del evento: _Detallado_, _Informativo_, _Advertencia_, _Error_ o _Crítico_.
       - **Estado**: Estado del evento: _Iniciado_ o _Erróneo_ o _Correcto_.
       - **Evento iniciado por**: También se denomina "autor de la llamada". La dirección de correo electrónico o el identificador de Azure Active Directory del usuario que realizó la operación.

       Este gráfico de señal de ejemplo tiene la lógica de alerta aplicada:

       ![Criterios seleccionados](media/alerts-activity-log/criteria-selected.png)

4. En la opción que permite **definir los detalles de las alertas**, proporcione los datos siguientes:

    - **Nombre de la regla de alertas**: El nombre de la nueva regla de alertas.
    - **Descripción**: La descripción de la nueva regla de alertas.
    - **Guardar la alerta en el grupo de recursos**: Seleccione el grupo de recursos donde quiere guardar esta nueva regla.

5. En el **grupo de acciones**, en el menú desplegable, especifique el grupo de acciones que quiere asignar a esta nueva regla de alertas. O bien, [cree un nuevo grupo de acción](../../azure-monitor/platform/action-groups.md) y asígneselo a la nueva regla. Para crear un nuevo grupo, seleccione **+ Nuevo grupo**.

6. Para habilitar las reglas después de crearlas, seleccione **Sí** en la opción **Habilitar regla tras la creación**.
7. Seleccione **Crear regla de alertas**.

    Se crea la nueva regla de alertas del registro de actividad y aparece un mensaje de confirmación en la esquina superior derecha de la ventana.

    Puede habilitar, deshabilitar, editar o eliminar una regla. Obtenga más información sobre cómo administrar las reglas del registro de actividad.


Una simple analogía para comprender las condiciones en las que se pueden crear reglas de alertas en el registro de actividad es explorar o filtrar eventos a través del [Registro de actividad en Azure Portal](activity-log-view.md#azure-portal). En la pantalla de **Azure Monitor: registro de actividad** se puede filtrar o buscar un evento necesario y crear una alerta mediante el botón **Agregar alerta del registro de actividad**. A continuación, siga los pasos 4 a 7 tal como se mostró anteriormente.
    
 ![Agregar una alerta del registro de actividad](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-the-azure-portal"></a>Visualización y administración en Azure Portal

1. En Azure Portal, seleccione **Supervisar** > **Alertas**. Seleccione **Administrar reglas de alertas** en la esquina superior izquierda de la ventana.

    ![Administración de reglas de alerta](media/alerts-activity-log/manage-alert-rules.png)

    Aparece la lista de reglas disponibles.

2. Busque la regla del registro de actividad que quiera modificar.

    ![Buscar las reglas de alertas del registro de actividad](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Puede usar los filtros disponibles: _Suscripción_, _Grupo de recursos_, _Recursos_, _Tipo de señal_ o _Estado_ para buscar la regla de actividad que quiera editar.

   > [!NOTE]
   > 
   > Recuerde que solo puede editar las secciones **Descripción**, **Criterios de destino** y **Grupos de acción**.

3. Seleccione la regla y haga doble clic para editar las opciones de la misma. Realice los cambios necesarios y seleccione **Guardar**.

   ![Administración de reglas de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Puede habilitar, deshabilitar, editar o eliminar una regla. Seleccione la opción adecuada en la parte superior de la ventana, después de seleccionar la regla tal como se detalla en el paso 2.


## <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure
Para crear una alerta del registro de actividad mediante una plantilla de Azure Resource Manager, cree un recurso del tipo `microsoft.insights/activityLogAlerts`. A continuación, rellene todas las propiedades relacionadas. A continuación, se muestra una plantilla que crea una alerta del registro de actividad:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "activityLogAlertName": {
      "type": "string",
      "metadata": {
        "description": "Unique name (within the Resource Group) for the Activity log alert."
      }
    },
    "activityLogAlertEnabled": {
      "type": "bool",
      "defaultValue": true,
      "metadata": {
        "description": "Indicates whether or not the alert is enabled."
      }
    },
    "actionGroupResourceId": {
      "type": "string",
      "metadata": {
        "description": "Resource Id for the Action group."
      }
    }
  },
  "resources": [   
    {
      "type": "Microsoft.Insights/activityLogAlerts",
      "apiVersion": "2017-04-01",
      "name": "[parameters('activityLogAlertName')]",      
      "location": "Global",
      "properties": {
        "enabled": "[parameters('activityLogAlertEnabled')]",
        "scopes": [
            "[subscription().id]"
        ],        
        "condition": {
          "allOf": [
            {
              "field": "category",
              "equals": "Administrative"
            },
            {
              "field": "operationName",
              "equals": "Microsoft.Resources/deployments/write"
            },
            {
              "field": "resourceType",
              "equals": "Microsoft.Resources/deployments"
            }
          ]
        },
        "actions": {
          "actionGroups":
          [
            {
              "actionGroupId": "[parameters('actionGroupResourceId')]"
            }
          ]
        }
      }
    }
  ]
}
```
El ejemplo JSON anterior se puede guardar, por ejemplo, como sampleActivityLogAlert.json a los efectos de este tutorial, y puede implementarse con [Azure Resource Manager en Azure Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Una regla de alertas nueva del registro de actividad puede tardar hasta 5 minutos en activarse.

## <a name="rest-api"></a>API DE REST 
La [API de alertas del registro de actividad de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) es una API de REST. Es totalmente compatible con la API de REST de Azure Resource Manager. Asimismo, se puede usar con PowerShell mediante el cmdlet de Resource Manager o la CLI de Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-the-resource-manager-template-with-powershell"></a>Implementación de la plantilla de Resource Manager con PowerShell
Para usar PowerShell para implementar la plantilla de ejemplo de Resource Manager que se muestra en la sección anterior [Plantilla de Azure Resource Manager](#azure-resource-manager-template), use el siguiente comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

Tenga en cuenta que sampleActivityLogAlert.parameters.json tiene los valores proporcionados para los parámetros que se necesitan para crear las reglas de alertas.

### <a name="use-activity-log-powershell-cmdlets"></a>Usar los cmdlets de PowerShell del registro de actividad

Las alertas del registro de actividad tienen cmdlets dedicados de PowerShell disponibles:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert): crea una nueva alerta de registro de actividad o actualiza una alerta de registro de actividad existente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert): obtiene uno o más recursos de alerta del registro de actividad.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert): habilita una alerta de registro de actividad existente y establece sus etiquetas.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert): deshabilita una alerta de registro de actividad existente y establece sus etiquetas.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert): quita una alerta de registro de actividad.

## <a name="azure-cli"></a>CLI de Azure

Los comandos dedicados de la CLI de Azure en el conjunto de comandos [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) están disponibles para administrar las reglas de alertas del registro de actividad.

Para crear una nueva regla de alertas del registro de actividad, use los siguientes comandos en este orden:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): para crear un nuevo recurso de regla de alertas del registro de actividad.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): para agregar el ámbito de la regla de alertas creada del registro de actividad.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): para agregar un grupo de acciones a la regla de alertas del registro de actividad.

Para recuperar un recurso de regla de alertas del registro de actividad, puede usar el comando [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) de la CLI de Azure. Asimismo, para ver todos los recursos de regla de alertas del registro de actividad en un grupo de recursos, use [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Para quitar los recursos de regla de alertas del registro de actividad, use el comando [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) de la CLI de Azure.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca del [esquema de webhook para los registros de actividad](../../azure-monitor/platform/activity-log-alerts-webhook.md).
- Lea la [información general sobre los registros de actividad](../../azure-monitor/platform/activity-log-alerts.md).
- Más información sobre los [grupos de acciones](../../azure-monitor/platform/action-groups.md).  
- Más información acerca de las [Notificaciones del estado del servicio](../../azure-monitor/platform/service-notifications.md).
