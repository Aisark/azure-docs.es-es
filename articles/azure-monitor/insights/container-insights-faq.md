---
title: Preguntas más frecuentes sobre Azure Monitor para contenedores | Microsoft Docs
description: Azure Monitor para contenedores es una solución que supervisa el estado de los clústeres de AKS y Container Instances en Azure. En este artículo se responden preguntas comunes.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960522"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>Preguntas más frecuentes sobre Azure Monitor para contenedores
En este artículo de preguntas frecuentes de Microsoft, se presenta una lista con las preguntas frecuentes sobre Azure Monitor para contenedores. Si tiene alguna otra pregunta sobre esta solución, vaya al [foro de discusión](https://feedback.azure.com/forums/34192--general-feedback) y publíquela. Si una pregunta es frecuente, se agrega a este artículo para que se pueda encontrar de forma rápida y sencilla.

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>No puedo ver ningún dato en el área de trabajo de Log Analytics en un momento determinado todos los días. ¿Cómo se resuelve este problema? 

Podría haber alcanzado el límite de 500 MB de forma predeterminada o el límite diario especificado para controlar la cantidad de datos para recopilar diariamente. Cuando se alcanza el límite para el día, la recopilación de datos se detiene y se reanuda sólo en el día siguiente. Para revisar el uso de datos y actualizar a un plan de tarifa diferente en función de los patrones de uso previsto, consulte [uso de datos y el costo de registro](../platform/manage-cost-storage.md). 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>¿Cuáles son los estados de los contenedores especificados en la tabla ContainerInventory?
La tabla ContainerInventory contiene información sobre los contenedores detenidos y en ejecución. La tabla se rellena con un flujo de trabajo dentro del agente que consulta el docker de todos los contenedores (en ejecución y detenidos) y reenvía dichos datos al área de trabajo de Log Analytics.
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>¿Cómo puedo resolver los errores relacionados con el **registro de suscripción que falta en Microsoft.OperationsManagement**?
Para resolver el error, registre el proveedor de recursos **Microsoft.OperationsManagement** en la suscripción en la que está definida el área de trabajo. La documentación sobre cómo hacer esto se puede encontrar [aquí](../../azure-resource-manager/resource-manager-register-provider-errors.md).

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>¿Azure Monitor para contenedores incluye compatibilidad para clústeres de AKS con RBAC habilitado?
La solución de supervisión de contenedores no es compatible con RBAC, pero es compatible con Azure Monitor para contenedores. La página de detalles de la solución no puede mostrar la información correcta en las hojas que muestran datos para estos clústeres.

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>¿Cómo habilitar la recopilación de registros para contenedores en el espacio de nombres kube-system mediante Helm?
La recopilación de registros de contenedores en el espacio de nombres kube-system está deshabilitada de forma predeterminada. La recopilación de registros puede habilitarse mediante la configuración de una variable de entorno en omsagent. Para obtener más información, consulte el [Azure Monitor para contenedores](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers) página de GitHub. 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>¿Cómo se puede actualizar el complemento omsagent a la última versión?
Para obtener información sobre cómo actualizar el agente, vea la información sobre la [administración del agente](container-insights-manage-agent.md).

## <a name="how-do-i-enable-multi-line-logging"></a>¿Cómo se puede habilitar el registro de varias líneas?
Actualmente, Azure Monitor para contenedores no admite el registro de varias líneas, pero hay soluciones alternativas disponibles. Puede configurar todos los servicios para que escriban en formato JSON y, a continuación, Docker/Moby los escribirán como una sola línea.

Por ejemplo, puede ajustar el registro como un objeto JSON como se muestra en el ejemplo siguiente para una aplicación de Node.js de ejemplo:

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

Estos datos tendrá un aspecto similar al ejemplo siguiente en Azure Monitor para los registros al realizar una consulta para él:

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

Para obtener una visión detallada del problema, visite el siguiente [vínculo de GitHub](https://github.com/moby/moby/issues/22920).

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>¿Cómo puedo resolver errores de Azure Active Directory al habilitar los registros dinámicos? 
Puede ver el siguiente error: **La respuesta a la dirección url especificada en la solicitud no coincide con las direcciones URL de respuesta configuradas para la aplicación: ' < Id. de aplicación\>'**. La solución para solucionar el problema puede encontrarse en el artículo [cómo ver los tiempo de real de registros de contenedor con Azure Monitor para contenedores](container-insights-live-logs.md#configure-aks-with-azure-active-directory). 

## <a name="next-steps"></a>Pasos siguientes
Para comenzar a supervisar el clúster de AKS, revise [Cómo incorporar Azure Monitor para contenedores](container-insights-onboard.md) para conocer los requisitos y los métodos disponibles para habilitar la supervisión. 