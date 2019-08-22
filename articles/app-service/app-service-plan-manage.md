---
title: 'Administración de un plan de App Service: Azure | Microsoft Docs'
description: Aprenda cómo realizar distintas tareas para administrar un plan de App Service.
keywords: servicio de aplicaciones, azure app service, escalar, plan de app service, cambiar, crear, administrar, administración
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.assetid: 4859d0d5-3e3c-40cc-96eb-f318b2c51a3d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 77d5e4ace14fb0071b6e01a01edbad0128382303
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2019
ms.locfileid: "69639736"
---
# <a name="manage-an-app-service-plan-in-azure"></a>Administración de un plan de App Service en Azure

Un [plan de Azure App Service](overview-hosting-plans.md) proporciona los recursos que debe ejecutar una aplicación de App Service. Esta guía muestra cómo administrar un plan de App Service.

## <a name="create-an-app-service-plan"></a>Creación de un plan de App Service

> [!TIP]
> Si no cuenta con App Service Environment, consulte [Creación de un plan de App Service en App Service Environment](environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan).

Puede crear un plan de App Service vacío o como parte de la creación de la aplicación.

1. En [Azure Portal](https://portal.azure.com), haga clic en **Nuevo** > **Web y móvil** y, a continuación, seleccione **Aplicación web** u otro tipo de aplicación de App Service.

2. Seleccione un plan de App Service existente o cree un plan para la nueva aplicación.

   ![Cree una aplicación en Azure Portal.][createWebApp]

   Para crear un plan:

   a. Seleccione **[+] Crear nuevo**.

      ![Creación de un plan de App Service.][createASP] 

   b. En **Plan de App Service**, especifique el nombre del plan.

   c. En **Ubicación**, seleccione una ubicación adecuada.

   d. En **Plan de tarifa**, seleccione un plan de tarifa adecuado para el servicio. Seleccione **Ver todos** para ver más opciones de precios, como **Gratis** y **Compartido**. Una vez haya seleccionado el plan de tarifa, haga clic en el botón **Seleccionar** .

<a name="move"></a>

## <a name="move-an-app-to-another-app-service-plan"></a>Cambio de una aplicación a otro plan de App Service

Puede mover una aplicación a otro plan de App Service siempre que el plan de origen y el plan de destino se encuentren en el _mismo grupo de recursos y región geográfica_.

> [!NOTE]
> Azure implementa cada nuevo plan de App Service en una unidad de implementación, que internamente se denomina espacio web. Cada región puede tener muchos espacios web, pero la aplicación solo puede moverse entre planes que se hayan creado en el mismo espacio web. Una instancia de App Service Environment es un espacio web aislado, por lo que las aplicaciones se pueden mover entre planes de la misma instancia de App Service Environment, pero no entre planes de diferentes instancias de App Service Environment.
>
> No se puede especificar el espacio web que quiera al crear un plan, pero es posible asegurar que un plan se cree en el mismo espacio web que un plan existente. En resumen, todos los planes que se creen con la misma combinación de grupo de recursos y región se implementan en el mismo espacio de web. Por ejemplo, si crea un plan en un grupo de recursos A y en una región B, cualquier plan que cree posteriormente en el grupo de recursos A y en la región B se implementará en el mismo espacio web. Tenga en cuenta que los planes no pueden mover espacios web una vez que se crean, por lo que no podrá mover un plan al "mismo espacio web" como otro plan moviéndolo a otro grupo de recursos.
> 

1. En [Azure Portal](https://portal.azure.com), vaya a la aplicación que desea mover.

1. En el menú, busque la sección **Plan de App Service**.

1. Seleccione **Cambiar plan de App Service** para abrir el selector **Plan de App Service**.

   ![Selector de plan de App Service.][change] 

1. En el selector **Plan de App Service**, seleccione un plan existente al que mover esta aplicación.   

La página **Select App Service plan** (Seleccionar plan de App Service) muestra únicamente los planes que se encuentran en el mismo grupo de recursos y región geográfica que el plan de App Service de la aplicación actual.

Cada plan tiene su propio plan de tarifa. Por ejemplo, al mover un sitio de un plan **Gratis** a un plan **Estándar**, todas las aplicaciones asignadas puedan usar las características y los recursos del plan **Estándar**. Sin embargo, mover una aplicación de un plan de un nivel superior a un plan de un nivel inferior implicará que deje de tener acceso a determinadas características. Si la aplicación utiliza una característica que no está disponible en el plan de destino, obtendrá un error que muestra qué característica está en uso que no está disponible. 

Por ejemplo, si una de las aplicaciones usa certificados SSL, podría aparecer este mensaje de error:

`Cannot update the site with hostname '<app_name>' because its current SSL configuration 'SNI based SSL enabled' is not allowed in the target compute mode. Allowed SSL configuration is 'Disabled'.`

En este caso, antes de poder mover la aplicación al plan de destino, debe:
- escalar verticalmente el plan de tarifa del plan de destino a **Básico** o superior, o bien
- quitar todas las conexiones SSL a la aplicación.

## <a name="move-an-app-to-a-different-region"></a>Cambio de una aplicación a una región diferente

La región en la que se ejecuta la aplicación es la región del plan de App Service en la que se encuentra. Sin embargo, no se puede cambiar la región de un plan de App Service. Si desea ejecutar la aplicación en una región diferente, una alternativa es clonar la aplicación. La clonación hará una copia de su aplicación en un plan de App Service nuevo o existente en cualquier región.

Puede encontrar **Clonar aplicación** en la sección **Herramientas de desarrollo** del menú.

> [!IMPORTANT]
> La clonación tiene algunas limitaciones. Puede obtener información al respecto en [Clonación de aplicaciones de Azure App Service](app-service-web-app-cloning.md).

## <a name="scale-an-app-service-plan"></a>Escalación de un plan de App Service

Para escalar verticalmente un plan de tarifa del plan de App Service, consulte [Escalado vertical de aplicaciones en Azure](manage-scale-up.md).

Para escalar horizontalmente el recuento de instancias de una aplicación, consulte [Escalado del recuento de instancias de forma manual o automática](../monitoring-and-diagnostics/insights-how-to-scale.md).

<a name="delete"></a>

## <a name="delete-an-app-service-plan"></a>Eliminación de un plan de App Service

Para evitar cargos inesperados, al eliminar la última aplicación en un plan de App Service, App Service también elimina el plan de forma predeterminada. Si, en su lugar, elige mantener el plan, debe cambiar el plan a **Gratis** para que no le cobren.

> [!IMPORTANT]
> Los planes de App Service que no tienen aplicaciones asociadas a ellos seguirán generando cargos, ya que siguen reservando las instancias de máquinas virtuales configuradas.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Escalado vertical de aplicaciones en Azure](manage-scale-up.md)

[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
