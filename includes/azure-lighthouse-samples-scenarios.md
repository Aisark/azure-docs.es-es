---
title: archivo de inclusión
description: archivo de inclusión
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: a5532b7a4574f3c2cc057255742c72bf032d180b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456730"
---
En estos ejemplos se muestran varias tareas que se pueden realizar en escenarios de administración entre inquilinos.

| **Plantilla** | **Descripción** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Implementa cuentas de almacenamiento en dos grupos de recursos diferentes.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Crea los servicios de administración de Azure, los vincula entre sí e implementa soluciones adicionales. Para una implementación completa, use la plantilla **rgWithAzureMgmt.json**. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Habilita y configura Azure Security Center dentro de la suscripción de Azure de destino. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Implementa y habilita Azure Sentinel en un área de trabajo de Log Analytics existente de una suscripción delegada. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Estas plantillas le permiten implementar extensiones de máquinas virtuales de Log Analytics en máquinas virtuales Windows y Linux mediante la conexión al área de trabajo de Log Analytics designada. |
