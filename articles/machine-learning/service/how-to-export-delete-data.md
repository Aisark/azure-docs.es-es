---
title: Exportación o eliminación de los datos del área de trabajo
titleSuffix: Azure Machine Learning service
description: Aprenda a exportar o eliminar el área de trabajo con Azure Portal, la CLI, el SDK y las API REST autenticadas.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: ph-com
ms.author: pahusban
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 3f40606d5fae3b3784ac7f1fdcf4977b7fd9eb1f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819433"
---
# <a name="export-or-delete-your-machine-learning-service-workspace-data"></a>Exportación o eliminación de los datos del área de trabajo de Machine Learning Service 

En Azure Machine Learning, puede exportar o eliminar los datos del área de trabajo con la API REST autenticada. En este artículo se indica cómo hacerlo.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-workspace-data"></a>Control de los datos del área de trabajo
Los datos del producto almacenados por Azure Machine Learning Services están disponibles para su exportación y eliminación por medio de Azure Portal, la CLI, el SDK y las API REST autenticadas. Se puede acceder a los datos de telemetría mediante el portal de privacidad de Azure. 

En Azure Machine Learning Services, los datos personales constan de información del usuario de documentos de historial de ejecución y registros de telemetría de algunas interacciones del usuario con el servicio.

## <a name="delete-workspace-data-with-the-rest-api"></a>Eliminación de datos del área de trabajo con la API REST 

Para eliminar datos, se pueden realizar las siguientes llamadas API con el verbo HTTP DELETE. Estas llamadas están autorizadas al tener un encabezado `Authorization: Bearer <arm-token>` en la solicitud, donde `<arm-token>` es el token de acceso de AAD del punto de conexión `https://management.core.windows.net/`.  

Para saber cómo obtener este token y llamar a los puntos de conexión de Azure, consulte la [documentación de la API REST de Azure](https://docs.microsoft.com/rest/api/azure/).  

En los ejemplos siguientes, reemplace el texto en {} por los nombres de instancia que determinan el recurso asociado.

### <a name="delete-an-entire-workspace"></a>Eliminación de un área de trabajo completa

Use esta llamada para eliminar un área de trabajo entera.  
> [!WARNING]
> Se eliminará toda la información y el área de trabajo ya no podrá usarse.

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="delete-models"></a>Eliminación de modelos

Use esta llamada para obtener una lista de modelos y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Los modelos individuales se pueden eliminar con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="delete-assets"></a>Eliminación de recursos

Use esta llamada para obtener una lista de recursos y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Los recursos individuales se pueden eliminar con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="delete-images"></a>Eliminación de imágenes

Use esta llamada para obtener una lista de imágenes y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Las imágenes individuales se pueden eliminar con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="delete-services"></a>Eliminación de servicios

Use esta llamada para obtener una lista de servicios y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Los servicios individuales se pueden eliminar con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

## <a name="export-service-data-with-the-rest-api"></a>Exportación de datos de servicio con la API REST

Para exportar datos, se pueden realizar las siguientes llamadas API con el verbo HTTP GET. Estas llamadas están autorizadas al tener un encabezado `Authorization: Bearer <arm-token>` en la solicitud, donde `<arm-token>` es el token de acceso de AAD del punto de conexión `https://management.core.windows.net/`.  

Para saber cómo obtener este token y llamar a los puntos de conexión de Azure, consulte la [documentación de la API REST de Azure](https://docs.microsoft.com/rest/api/azure/).   

En los ejemplos siguientes, reemplace el texto en {} por los nombres de instancia que determinan el recurso asociado.

### <a name="export-workspace-information"></a>Exportación de la información del área de trabajo

Use esta llamada para obtener una lista de todas las áreas de trabajo:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces?api-version=2018-03-01-preview

Se puede obtener información sobre un área de trabajo individual mediante:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}?api-version=2018-03-01-preview

### <a name="export-compute-information"></a>Exportación de la información de proceso

Se pueden obtener todos los destinos de proceso asociados a un área de trabajo mediante:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

Se puede obtener información sobre un único destino de proceso mediante:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroup/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{computeName}?api-version=2018-03-01-preview

### <a name="export-run-history-data"></a>Exportación de datos del historial de ejecución
Use esta llamada para obtener una lista de todos los experimentos y su información:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments 

Se pueden obtener todas las ejecuciones de un experimento determinado mediante:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs 

Los elementos de historial de ejecución se pueden obtener mediante:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/runs/{runId} 

Se pueden obtener todas las métricas de ejecución de un experimento mediante:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics 

Se puede obtener una sola métrica de ejecución mediante:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/experiments/{experimentName}/metrics/{metricId}
    
### <a name="export-artifacts"></a>Exportación de artefactos

Use esta llamada para obtener una lista de artefactos y sus rutas de acceso:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/artifacts/origins/ExperimentRun/containers/{runId}
    
### <a name="export-notifications"></a>Exportación de notificaciones

Use esta llamada para obtener una lista de tareas almacenadas:     

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/tasks

Se pueden obtener las notificaciones de una sola tarea mediante:

    https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}tasks/{taskId}

### <a name="export-data-stores"></a>Exportación de almacenes de datos

Use esta llamada para obtener una lista de almacenes de datos:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores

Se pueden obtener almacenes de datos individuales mediante:

    https://{location}.experiments.azureml.net/datastore/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/datastores/{name}

### <a name="export-models"></a>Exportación de modelos

Use esta llamada para obtener una lista de modelos y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models?api-version=2018-03-01-preview

Los modelos individuales pueden obtenerse mediante:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/models/{id}?api-version=2018-03-01-preview

### <a name="export-assets"></a>Exportación de recursos

Use esta llamada para obtener una lista de recursos y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets?api-version=2018-03-01-preview

Se pueden obtener recursos individuales mediante:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/assets/{id}?api-version=2018-03-01-preview

### <a name="export-images"></a>Exportación de imágenes

Use esta llamada para obtener una lista de imágenes y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images?api-version=2018-03-01-preview

Se pueden obtener imágenes individuales mediante:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/images/{id}?api-version=2018-03-01-preview

### <a name="export-services"></a>Exportación de servicios

Use esta llamada para obtener una lista de servicios y sus identificadores:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services?api-version=2018-03-01-preview

Los servicios individuales se pueden obtener mediante:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspace}/services/{id}?api-version=2018-03-01-preview

### <a name="export-pipeline-experiments"></a>Exportación de experimentos de canalización

Se pueden obtener experimentos individuales mediante:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Experiments/{experimentId}

### <a name="export-pipeline-graphs"></a>Exportación de grafos de canalización

Se pueden obtener grafos individuales mediante:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Graphs/{graphId}

### <a name="export-pipeline-modules"></a>Exportación de módulos de canalización

Se pueden obtener módulos mediante:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Modules/{id}

### <a name="export-pipeline-templates"></a>Exportación de plantillas de canalización

Se pueden obtener plantillas mediante:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/Templates/{templateId}

### <a name="export-pipeline-data-sources"></a>Exportación de orígenes de datos de canalización

Se pueden obtener orígenes de datos mediante:

    https://{location}.aether.ms/api/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/DataSources/{id}
