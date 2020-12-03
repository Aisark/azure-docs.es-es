---
title: archivo de inclusión
description: archivo de inclusión
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/21/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 14ac8ff54f5627509fe10a90bfdd0a01c07762d9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026881"
---
Antes de crear cualquier elemento, revisemos el concepto de ámbito. Azure proporciona cuatro niveles de administración: grupo de administración, suscripción, grupo de recursos y recurso. Los [grupos de administración](../articles/governance/management-groups/overview.md) están en versión preliminar. En la imagen siguiente se muestra un ejemplo de estos niveles:

![Ámbito](./media/resource-manager-governance-scope/scope-levels.png)

Aplicará la configuración de administración en cualquiera de estos niveles de ámbito. El nivel que seleccione determina el grado de amplitud con que se aplica la configuración. Los niveles inferiores heredan la configuración de los niveles superiores. Al aplicar una configuración a la suscripción, dicha configuración se aplica a todos los grupos de recursos y recursos de la suscripción. Al aplicar una configuración al grupo de recursos, esa configuración se aplica al grupo de recursos y a todos sus recursos. Sin embargo, otro grupo de recursos no tiene esa configuración.

Normalmente, tiene sentido aplicar la configuración crítica en niveles superiores y los requisitos específicos del proyecto en niveles inferiores. Por ejemplo, quizás quiera asegurarse de que todos los recursos de su organización se implementan en determinadas regiones. Para lograr este requisito, aplique una directiva a la suscripción que especifique las ubicaciones permitidas. Cuando otros usuarios de su organización agreguen nuevos grupos de recursos y recursos, se aplicarán automáticamente las ubicaciones permitidas.