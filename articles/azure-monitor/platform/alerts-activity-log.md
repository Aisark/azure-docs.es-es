---
title: Crear, ver y administrar las alertas del registro de actividad en Azure Monitor
description: Cómo crear alertas del registro de actividad en Azure Portal, la plantilla de recursos y PowerShell.
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/15/2018
ms.author: vinagara
ms.openlocfilehash: 2b069e55d98da824363dc480c211cde0fcc2518c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2019
ms.locfileid: "58090821"
---
# <a name="create-view-and-manage-activity-log-alerts-using-azure-monitor"></a>Crear, ver y administrar las alertas del registro de actividad mediante Azure Monitor  

## <a name="overview"></a>Información general
Las alertas del registro de actividad son alertas que se activan cuando un nuevo evento del registro de actividad cumple las condiciones especificadas en la alerta.

Estas alertas son recursos de Azure, por lo que pueden crearse con una plantilla de Azure Resource Manager. También se pueden crear, actualizar o eliminar en Azure Portal. Normalmente es necesario crear alertas del registro de actividad para recibir una notificación si se producen cambios específicos en los recursos de la suscripción de Azure, los cuales suelen abarcar grupos de recursos o algún recurso en particular. Por ejemplo, es posible que quiera recibir una notificación cuando se elimine una máquina virtual (grupo de recursos de ejemplo) en **myProductionResourceGroup**, o que quiera recibir una notificación si cualquier rol nuevo se asigna a un usuario de la suscripción.

> [!IMPORTANT]
> No se pueden crear alertas en la notificación de Service Health a través de la interfaz para la creación de alertas de registro de actividad. Para obtener más información acerca de cómo crear y usar las notificaciones de Service Health, consulte [Recibir alertas del registro de actividad con las notificaciones de Service Health](alerts-activity-log-service-notifications.md).

## <a name="azure-portal"></a>Azure Portal

> [!NOTE]
> 
>  Al crear las reglas de alertas, asegúrese de lo siguiente:
> 
> - La suscripción del ámbito no es diferente de la suscripción donde se creó la alerta.
> - La alerta se configurará según los siguientes tipos de criterios: nivel, estado, autor de la llamada, grupo de recursos, id. del recurso, tipo de recurso o categoría de eventos.
> - No hay ninguna condición o condiciones anidadas de tipo "anyOf" en la configuración de alertas JSON (básicamente, solo se permite una condición de tipo "AllOf" y que no tenga más tipos AllOf/anyOf).
> - Cuando la categoría es "administrativa". Debe especificar al menos uno de los criterios anteriores en la alerta. No puede crear una alerta que se active cada vez que se crea un evento en los registros de actividad.

### <a name="create-with-azure-portal"></a>Creación con Azure Portal

Utilice el siguiente procedimiento:

1. En Azure Portal, seleccione **Supervisar** > **Alertas**.
2. Haga clic en **Nueva regla de alertas** que se encuentra en la parte superior de la ventana **Alertas**.

     ![nueva regla de alertas](media/alerts-activity-log/AlertsPreviewOption.png)

     Aparece la ventana **Crear regla**.

      ![opciones de la nueva regla de alertas](media/alerts-activity-log/create-new-alert-rule-options.png)

3. **En la condición Definir alerta**, proporcione la siguiente información y haga clic en **Hecho**.

   - **Destino de la alerta:** para ver y seleccionar el destino de la nueva alerta, use la opción **Filtrar por suscripción** / **Filtrar por tipo de recurso** y seleccione el recurso o grupo de recursos de la lista que se muestra.

     > [!NOTE]
     > 
     > puede seleccionar un recurso, un grupo de recursos o una suscripción completa para la señal del registro de actividad.

     **Vista de destino de ejemplo de alerta**
     ![Seleccionar destino](media/alerts-activity-log/select-target.png)

   - En **Criterios de destino**, haga clic en **Agregar criterio** y verá todas las señales disponibles para el destino (incluidas las de categorías tales como **Registro de actividad**), con el nombre de la categoría agregado en el nombre de **Servicio de supervisión**.

   - Seleccione la señal de la lista que se muestra y que corresponde a varias operaciones posibles para el tipo **Registro de actividad**.

     Puede seleccionar la escala de tiempo del historial de registro y la lógica de la alerta correspondiente a esta señal de destino:

     **Pantalla para agregar criterios**

     ![agregar criterios](media/alerts-activity-log/add-criteria.png)

     **Hora del historial**: los eventos disponibles para la operación seleccionada se pueden trazar en las últimas 6/12/24 horas (o) durante la última semana.

     **Lógica de alerta**:

     - **Nivel del evento**: nivel de gravedad del evento. _Detallado_, _Informativo_, _Advertencia_, _Error_ o _Crítico_.
     - **Estado**: Estado del evento. _Iniciado_ o _Erróneo_ o _Correcto_.
     - **Evento iniciado por**: también conocido como autor de la llamada; es la dirección de correo electrónico o el identificador de Azure Active Directory del usuario que realizó la operación.

       Gráfico de señal de ejemplo con la lógica de alerta aplicada:

       ![ criterios seleccionados](media/alerts-activity-log/criteria-selected.png)

4. En la opción que permite **definir los detalles de las reglas de alertas**, proporcione los datos siguientes:

    - **Nombre de regla de alertas** : el nombre de la nueva regla de alertas.
    - **Descripción**: la descripción de la nueva regla de alertas.
    - **Guardar alerta en el grupo de recursos**: seleccione el grupo de recursos donde quiera guardar esta nueva regla.

5. En el **grupo de acciones**, en el menú desplegable, especifique el grupo de acciones que quiere asignar a esta nueva regla de alertas. Asimismo, también puede [crear un nuevo grupo de acción](../../azure-monitor/platform/action-groups.md) y asignarlo a la nueva regla. Para crear un nuevo grupo, haga clic en **+ Nuevo grupo**.

6. Para habilitar las reglas una vez creado, haga clic en **Sí** en la opción **Habilitar regla tras la creación**.
7. Haga clic en **Crear regla de alertas**.

    La nueva regla de alertas del registro de actividad se crea y aparece un mensaje de confirmación en la parte superior derecha de la ventana.

    Puede habilitar, deshabilitar, editar o eliminar una regla. Obtenga más información sobre cómo administrar las reglas del registro de actividad.


Alternativamente, una simple analogía para comprender las condiciones en las que se pueden crear reglas de alertas en el registro de actividad, es explorar o filtrar eventos a través del [Registro de actividad en Azure Portal ](../../azure-monitor/platform/activity-logs-overview.md#query-the-activity-log-in-the-azure-portal). En "Azure Monitor: registro de actividad", se puede filtrar o buscar un evento necesario y crear una alerta mediante el botón **Agregar alerta de registro de actividad**; a continuación, siga del paso 4 en adelante, tal como se indica en el tutorial anterior.
    
 ![ agregar alerta del registro de actividad](media/alerts-activity-log/add-activity-log.png)
    

### <a name="view-and-manage-in-azure-portal"></a>Visualización y administración en Azure Portal

1. En Azure Portal, haga clic en **Supervisar** > **Alertas** y haga clic en **Administrar reglas** en la parte superior izquierda de la ventana.

    ![ administración de reglas de alerta](media/alerts-activity-log/manage-alert-rules.png)

    Aparece la lista de reglas disponibles.

2. Busque la regla del registro de actividad que quiera modificar.

    ![ buscar las reglas de alertas del registro de actividad](media/alerts-activity-log/searth-activity-log-rule-to-edit.png)

    Puede usar los filtros disponibles: _Suscripción_, _Grupo de recursos_, _Recursos_, _Tipo de señal_ o _Estado_ para buscar la regla de actividad que quiera editar.

   > [!NOTE]
   > 
   > Recuerde que solo puede editar las secciones **Descripción** , **Criterios de destino** y **Grupos de acción**.

3. Seleccione la regla y haga doble clic para editar las opciones. Haga los cambios que sean necesarios y haga clic en **Guardar**.

   ![ administración de reglas de alerta](media/alerts-activity-log/activity-log-rule-edit-page.png)

4. Puede deshabilitar, habilitar o eliminar una regla. Seleccione la opción adecuada en la parte superior de la ventana, después de seleccionar la regla tal como se detalla en el paso 2.


## <a name="azure-resource-template"></a>Plantilla de recursos de Azure
Para crear una alerta del registro de actividad mediante una plantilla de Resource Manager, cree un recurso del tipo `microsoft.insights/activityLogAlerts`. A continuación, rellene todas las propiedades relacionadas. A continuación, se muestra una plantilla que crea una alerta del registro de actividad.

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
El formato JSON del ejemplo anterior se puede guardar como (digamos) sampleActivityLogAlert.json a los efectos de este tutorial, y puede implementarse con [Azure Resource Manager en Azure Portal](../../azure-resource-manager/resource-group-template-deploy-portal.md).

> [!NOTE]
> Una regla de alerta nueva del registro de actividad puede tardar hasta 5 minutos en activarse.

## <a name="rest-api"></a>API DE REST 
[Azure Monitor - Activity Log Alerts API](https://docs.microsoft.com/rest/api/monitor/activitylogalerts) es una API REST que es totalmente compatible con la API REST de Azure Resource Manager. Por lo tanto, se puede utilizar a través de Powershell con el cmdlet de Resource Manager y con la CLI de Azure.

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="deploy-resource-manager-template-with-powershell"></a>Implementar plantilla de Resource Manager con PowerShell
Para usar PowerShell para implementar el ejemplo de plantilla de recursos que se muestra en una anterior [sección plantilla de recursos] (#--plantilla de resource manager, use el siguiente comando:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "myRG" -TemplateFile sampleActivityLogAlert.json -TemplateParameterFile sampleActivityLogAlert.parameters.json
```

donde el sampleActivityLogAlert.parameters.json contiene los valores proporcionados para los parámetros necesarios para crear una regla de alerta.

### <a name="use-activity-log-powershell-cmdlets"></a>Usar cmdlets de PowerShell de registro de actividad

Las alertas del registro de actividad tienen cmdlets dedicados de PowerShell disponibles:

- [Set-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Set-AzActivityLogAlert?view=azps-1.3.0) : Crea una nueva o actualizar una alerta de registro de actividad existente.
- [Get-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Get-AzActivityLogAlert?view=azps-1.3.0) : Obtiene uno o más actividad registro alertar a los recursos.
- [Enable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Enable-AzActivityLogAlert?view=azps-1.3.0) : Habilita una alerta de registro de actividad existente y establece sus etiquetas.
- [Disable-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Disable-AzActivityLogAlert?view=azps-1.3.0) : Deshabilita una alerta de registro de actividad existente y establece sus etiquetas.
- [Remove-AzActivityLogAlert](https://docs.microsoft.com/powershell/module/az.monitor/Remove-AzActivityLogAlert?view=azps-1.3.0)    : Quita una alerta de registro de actividad.

## <a name="cli"></a>CLI

Los comandos dedicados de la CLI de Azure en el conjunto de comandos [az monitor activity-log alert](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert) están disponibles para administrar las reglas de alertas del registro de actividad.

Para crear una nueva regla de alertas del registro de actividad, use en este orden:

1. [az monitor activity-log alert create](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-create): para crear un nuevo recurso de regla de alertas del registro de actividad.
1. [az monitor activity-log alert scope](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/scope): para agregar el ámbito de la regla de alertas creada del registro de actividad.
1. [az monitor activity-log alert action-group](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert/action-group): para agregar un grupo de acciones para la regla de alertas del registro de actividad.

Para recuperar un recurso de regla de alertas del registro de actividad, puede usar el comando [az monitor activity-log alert show](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-show
) de la CLI de Azure. Asimismo, para ver todos los recursos de regla de alertas del registro de actividad en un grupo de recursos, use [az monitor activity-log alert list](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list).
Para quitar los recursos de regla de alertas del registro de actividad, use el comando [az monitor activity-log alert delete](https://docs.microsoft.com/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-delete) de la CLI de Azure.

## <a name="next-steps"></a>Pasos siguientes

- [Esquema de webhook para los registros de actividad](../../azure-monitor/platform/activity-log-alerts-webhook.md)
- [Información general sobre los registros de actividad](../../azure-monitor/platform/activity-log-alerts.md) 
- Más información sobre los [grupos de acciones](../../azure-monitor/platform/action-groups.md).  
- Más información acerca de las [Notificaciones del estado del servicio](../../azure-monitor/platform/service-notifications.md).
