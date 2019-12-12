---
title: Ejemplos de inicio rápido de la CLI de Azure Monitor
description: Comandos de CLI de ejemplo para características de Azure Monitor. Azure Monitor es un servicio de Microsoft Azure que permite enviar notificaciones de alerta, llamar a direcciones URL web en función de los valores de datos de telemetría configurados y escalar automáticamente Cloud Services, Virtual Machines y Web Apps.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 05/16/2018
ms.openlocfilehash: 0def528e0c432ecbc706622f0b8c29c2d610c663
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2019
ms.locfileid: "74893542"
---
# <a name="azure-monitor-cli-quick-start-samples"></a>Ejemplos de inicio rápido de la CLI de Azure Monitor
En este artículo se muestran comandos de la interfaz de la línea de comandos (CLI) de ejemplo que lo ayudarán a acceder a las características de supervisión de Azure Monitor. Azure Monitor permite escalar automáticamente Cloud Services, Virtual Machines y Web Apps para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados.

## <a name="prerequisites"></a>Requisitos previos

Si aún no ha instalado la CLI de Azure, siga las instrucciones para [instalar la CLI de Azure](/cli/azure/install-azure-cli). También puede usar [Azure Cloud Shell](/azure/cloud-shell) para ejecutar la CLI como una experiencia interactiva en el explorador. Consulte una referencia completa de todos los comandos disponibles en la [referencia de la CLI de Azure Monitor](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest). 

## <a name="log-in-to-azure"></a>Inicio de sesión en Azure
El primer paso consiste en iniciar sesión en su cuenta de Azure.

```azurecli
az login
```

Después de ejecutar este comando, tendrá que iniciar sesión siguiendo las instrucciones de la pantalla. Todos los comandos funcionan en el contexto de la suscripción predeterminada.

Para mostrar los detalles de la suscripción actual, utilice el siguiente comando.

```azurecli
az account show
```

Para cambiar el contexto de trabajo a una suscripción diferente, utilice el siguiente comando.

```azurecli
az account set -s <Subscription ID or name>
```

Para ver una lista de todos los comandos de Azure Monitor admitidos, realice lo siguiente.

```azurecli
az monitor -h
```

## <a name="view-activity-log-for-a-subscription"></a>Visualización del registro de actividades para una suscripción

Para ver una lista de eventos del registro de actividades, realice lo siguiente.

```azurecli
az monitor activity-log list
```

Pruebe el siguiente comando para ver todas las opciones disponibles.

```azurecli
az monitor activity-log list -h
```

Se trata de un ejemplo para mostrar los registros por grupo de recursos.

```azurecli
az monitor activity-log list --resource-group <group name>
```

Ejemplo para mostrar registros por autor de llamada

```azurecli
az monitor activity-log list --caller myname@company.com
```

Ejemplo para mostrar registros por autor de llamada en un tipo de recurso, dentro de un intervalo de fechas

```azurecli
az monitor activity-log list --resource-provider Microsoft.Web \
    --caller myname@company.com \
    --start-time 2016-03-08T00:00:00Z \
    --end-time 2016-03-16T00:00:00Z
```

## <a name="work-with-alerts"></a>Uso de alertas 
> [!NOTE]
> Solo las alertas (clásicas) se admiten en la CLI en este momento. 

### <a name="get-alert-classic-rules-in-a-resource-group"></a>Obtención de reglas de alerta (clásicas) en un grupo de recursos

```azurecli
az monitor activity-log alert list --resource-group <group name>
az monitor activity-log alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-metric-alert-classic-rule"></a>Creación de una regla de alerta (clásica) de métrica

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-an-alert-classic-rule"></a>Eliminación de una regla de alerta (clásica)

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="log-profiles"></a>Perfiles de registro

Utilice la información de esta sección para usar perfiles de registro.

### <a name="get-a-log-profile"></a>Obtención de un perfil de registro

```azurecli
az monitor log-profiles list
az monitor log-profiles show --name <profile name>
```

### <a name="add-a-log-profile-with-retention"></a>Adición de un perfil de registro con retención de datos

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true \
    --storage-account-id <storage account ID to store the logs in>
```

### <a name="add-a-log-profile-with-retention-and-eventhub"></a>Adición de un perfil de registro con retención y EventHub

```azurecli
az monitor log-profiles create --name <profile name> --location <location of profile> \
    --locations <locations to monitor activity in: location1 location2 ...> \
    --categories <categoryName1 categoryName2 ...> \
    --days <# days to retain> \
    --enabled true
    --storage-account-id <storage account ID to store the logs in>
    --service-bus-rule-id <service bus rule ID to stream to>
```

### <a name="remove-a-log-profile"></a>Eliminación de un perfil de registro

```azurecli
az monitor log-profiles delete --name <profile name>
```

## <a name="diagnostics"></a>Diagnóstico

Utilice la información de esta sección para usar la configuración de diagnóstico.

### <a name="get-a-diagnostic-setting"></a>Obtención de la configuración de diagnóstico

```azurecli
az monitor diagnostic-settings list --resource <target resource ID>
```

### <a name="create-a-diagnostic-setting"></a>Creación de una configuración de diagnóstico 

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <storage account ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

### <a name="delete-a-diagnostic-setting"></a>Eliminación de la configuración de diagnóstico

```azurecli
az monitor diagnostic-settings delete --name <diagnostic name> \
    --resource <target resource ID>
```

## <a name="autoscale"></a>Escalado automático

Utilice la información de esta sección para usar la configuración de escalado automático. Tendrá que modificar estos ejemplos.

### <a name="get-autoscale-settings-for-a-resource-group"></a>Obtención de la configuración de escalado automático de un grupo de recursos

```azurecli
az monitor autoscale list --resource-group <group name>
```

### <a name="get-autoscale-settings-by-name-in-a-resource-group"></a>Obtención de valores de escalado automático por su nombre en un grupo de recursos

```azurecli
az monitor autoscale show --name <settings name> --resource-group <group name>
```

### <a name="set-autoscale-settings"></a>Configuración del escalado automático

```azurecli
az monitor autoscale create --name <settings name> --resource-group <group name> \
    --count <# instances> \
    --resource <target resource ID>
```

