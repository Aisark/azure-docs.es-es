---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73484085"
---
Este contenedor tiene las siguientes opciones de configuración:

|Obligatorio|Configuración|Propósito|
|--|--|--|
|Sí|[ApiKey](#apikey-configuration-setting)|Realiza el seguimiento de la información de facturación.|
|Sin|[Application Insights](#applicationinsights-setting)|Permite agregar compatibilidad con los datos de telemetría de [Azure Application Insights](https://docs.microsoft.com/azure/application-insights) al contenedor.|
|Sí|[Facturación](#billing-configuration-setting)|Especifica el URI del punto de conexión del recurso de servicio en Azure.|
|Sí|[Eula](#eula-setting)| Indica que ha aceptado la licencia del contenedor.|
|Sin|[Fluentd](#fluentd-settings)|Escribe el registro y, opcionalmente, los datos de métricas en un servidor de Fluentd.|
|Sin|Proxy HTTP|Configura un proxy HTTP para realizar solicitudes de salida.|
|Sin|[Logging](#logging-settings)|Proporciona compatibilidad con el registro de ASP.NET Core al contenedor. |
|Sin|[Mounts](#mount-settings)|Lee y escribe los datos desde el equipo host al contenedor y del contenedor de nuevo al equipo host.|
