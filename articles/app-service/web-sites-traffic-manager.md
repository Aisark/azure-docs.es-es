---
title: 'Control del tráfico con Azure Traffic Manager: Azure App Service'
description: En este artículo se proporciona información resumida sobre Azure Traffic Manager en su relación con Azure App Service.
services: app-service\web
documentationcenter: ''
author: cephalin
writer: cephalin
manager: erikre
editor: mollybos
ms.assetid: dabda633-e72f-4dd4-bf1c-6e945da456fd
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2cd53c3c0b3e9acbb8a5a88c7696e8b3089c50c9
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483379"
---
# <a name="controlling-azure-app-service-traffic-with-azure-traffic-manager"></a>Control del tráfico de Azure App Service con Azure Traffic Manager
> [!NOTE]
> En este artículo se proporciona información resumida sobre Microsoft Azure Traffic Manager en su relación con Azure App Service. Puede encontrar más información sobre el Administrador de tráfico de Azure en los vínculos que aparecen al final de este artículo.
> 
> 

## <a name="introduction"></a>Introducción
Puede utilizar Azure Traffic Manager para controlar la manera en que se distribuyen solicitudes de clientes web a aplicaciones de Azure App Service. Cuando se agregan puntos de conexión de App Service a un perfil de Azure Traffic Manager, este hace un seguimiento del estado de las aplicaciones de App Service (en ejecución, detenidas o eliminadas) para decidir cuáles de esos puntos de conexión debe recibir tráfico.

## <a name="routing-methods"></a>Métodos de enrutamiento
Azure Traffic Manager utiliza cuatro métodos de enrutamiento distintos. Estos métodos se describen en la siguiente lista según su relación con Azure App Service.

* **[Prioridad](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method):** usa una aplicación principal para todo el tráfico y proporciona copias de seguridad en caso de que las aplicaciones principal o de copia de seguridad no estén disponibles.
* **[Ponderado](../traffic-manager/traffic-manager-routing-methods.md#weighted):** el tráfico se distribuye entre un conjunto de aplicaciones, de manera uniforme o según los pesos definidos.
* **[Rendimiento](../traffic-manager/traffic-manager-routing-methods.md#performance):** cuando haya aplicaciones en diferentes ubicaciones geográficas, use la aplicación "más cercana" en cuanto a menor latencia de red.
* **[Geográfico](../traffic-manager/traffic-manager-routing-methods.md#geographic):** dirige a los usuarios a aplicaciones específicas en función de la ubicación geográfica de origen de su consulta de DNS. 

Para más información, consulte [Métodos de enrutamiento de Traffic Manager](../traffic-manager/traffic-manager-routing-methods.md).

## <a name="app-service-and-traffic-manager-profiles"></a>App Service y perfiles de Traffic Manager
Para configurar el control del tráfico de la aplicación de App Service, puede crear un perfil en Azure Traffic Manager que utilice uno de los cuatro métodos de equilibrio de carga descritos anteriormente y, luego, agregar los puntos de conexión (en este caso, App Service) para los que desea controlar el tráfico al perfil. El estado de la aplicación (en ejecución, detenido o eliminado) se comunica con frecuencia al perfil para que Azure Traffic Manager dirija el tráfico en consecuencia.

Cuando utilice el Administrador de tráfico de Azure con Azure, tenga en cuenta los siguientes puntos:

* Para las implementaciones de solo aplicación dentro de la misma región, App Service ya proporciona la funcionalidad de conmutación por error y de round-robin independientemente del modo de la aplicación.
* En el caso de las implementaciones en la misma región que utilizan App Service en conjunto con otro servicio en la nube de Azure, puede combinar ambos tipos de extremos para habilitar escenarios híbridos.
* Solo puede especificar un punto de conexión de App Service por región en un perfil. Cuando selecciona una aplicación como un punto de conexión para una región, las aplicaciones restantes de esa región dejan de estar disponibles para su selección para ese perfil.
* Los puntos de conexión de App Service que especifica en un perfil de Azure Traffic Manager aparecen en la sección **Nombres de dominio** de la página Configuración de la aplicación del perfil, pero no se configuran ahí.
* Después de agregar una aplicación web a un perfil, la **URL del sitio** del panel de la página del portal de la aplicación web muestra la dirección URL del dominio personalizado de la aplicación web si ha configurado alguna. De lo contrario, muestra la del perfil de Traffic Manager (por ejemplo, `contoso.trafficmanager.net`). Tanto el nombre de dominio directo de la aplicación como la dirección URL de Traffic Manager se ven en la página Configuración de dicha aplicación, en la sección **Nombres de dominio**.
* Los nombres de dominio personalizado funcionan según lo previsto, pero además de agregarlos a las aplicaciones, también debe configurar la asignación de DNS para que apunte a la dirección URL de Traffic Manager. Para información sobre cómo configurar un dominio personalizado para una aplicación de App Service, consulte [Asignación de un nombre DNS personalizado a Azure App Service](app-service-web-tutorial-custom-domain.md).
* Solo puede agregar aplicaciones que estén en modo estándar o premium a un perfil de Azure Traffic Manager.

## <a name="next-steps"></a>Pasos siguientes
Si desea obtener información general de carácter técnico y conceptual del Administrador de tráfico de Azure, consulte [Información general sobre el Administrador de tráfico](../traffic-manager/traffic-manager-overview.md).


