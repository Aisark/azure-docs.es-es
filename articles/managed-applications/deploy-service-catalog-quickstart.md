---
title: Uso de Azure Portal para implementar aplicaciones del catálogo de servicios | Microsoft Docs
description: Muestra a los clientes de aplicaciones administradas cómo implementar una aplicación del catálogo de servicios a través de Azure Portal.
services: managed-applications
author: tfitzmac
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: 4d2e8b442f70ee791fe65a32402e5272eda3f209
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60589051"
---
# <a name="deploy-service-catalog-app-through-azure-portal"></a>Implementación de aplicaciones del catálogo de servicios mediante Azure Portal

En el [inicio rápido anterior](publish-managed-app-definition-quickstart.md), publicó una definición de aplicación administrada. En este inicio rápido, creará una aplicación del catálogo de servicios a partir de esa definición.

## <a name="create-service-catalog-app"></a>Creación de la aplicación de catálogo de servicios

En Azure Portal, use los pasos siguientes:

1. Seleccione **Crear un recurso**.

   ![Creación de un recurso](./media/deploy-service-catalog-quickstart/create-new.png)

1. Busque **Aplicación administrada del catálogo de servicios** y selecciónela entre las opciones disponibles.

   ![Búsqueda de la aplicación del catálogo de servicios](./media/deploy-service-catalog-quickstart/select-service-catalog.png)

1. Vea una descripción del servicio de aplicación administrada. Seleccione **Crear**.

   ![Haga clic en Crear](./media/deploy-service-catalog-quickstart/create-service-catalog.png)

1. El portal muestra las definiciones de aplicación administrada a las que tiene acceso. En las definiciones disponibles, seleccione la que desee implementar. En este inicio rápido, usará la definición de **Cuenta de almacenamiento administrada** que creó en el anterior. Seleccione **Crear**.

   ![Seleccionar la definición que se va a implementar](./media/deploy-service-catalog-quickstart/select-definition.png)

1. Proporcione los valores de la pestaña **Básico**. Seleccione su suscripción a Azure en la que quiere implementar la aplicación del catálogo de servicios. Cree un nuevo grupo de recursos denominado **applicationGroup**. Seleccione una ubicación para la aplicación. Cuando termine, seleccione **Aceptar**.

   ![Proporcionar los valores de Básico](./media/deploy-service-catalog-quickstart/provide-basics.png)

1. Proporcione un prefijo para el nombre de la cuenta de almacenamiento. Seleccione el tipo de cuenta de almacenamiento que quiere crear. Cuando termine, seleccione **Aceptar**.

   ![Proporcionar valores de almacenamiento](./media/deploy-service-catalog-quickstart/provide-storage.png)

1. Revise el resumen. Cuando la validación sea correcta, seleccione **Aceptar** para iniciar la implementación.

   ![Ver resumen](./media/deploy-service-catalog-quickstart/view-summary.png)

## <a name="view-results"></a>Visualización de los resultados

Una vez implementada la aplicación del catálogo de servicios, tendrá dos nuevos grupos de recursos. Un grupo de recursos contiene la aplicación del catálogo de servicios. El otro grupo de recursos contiene los recursos de la aplicación del catálogo de servicios.

1. Vea el grupo de recursos denominado **applicationGroup** para ver la aplicación del catálogo de servicios.

   ![Ver aplicación](./media/deploy-service-catalog-quickstart/view-managed-application.png)

1. Vea el grupo de recursos denominado **applicationGroup{caracteres de almohadilla}** para ver los recursos de la aplicación del catálogo de servicios.

   ![Visualización de recursos](./media/deploy-service-catalog-quickstart/view-resources.png)

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información sobre cómo crear los archivos de definición de una aplicación administrada, consulte [Creación y publicación de una definición de aplicación administrada](publish-service-catalog-app.md).
* Para la CLI de Azure, consulte [Deploy service catalog app with Azure CLI](./scripts/managed-application-cli-sample-create-application.md) (Implementar una aplicación del catálogo de servicios con la CLI de Azure).
* Para PowerShell, consulte [Deploy service catalog app with PowerShell](./scripts/managed-application-poweshell-sample-create-application.md) (Implementar una aplicación del catálogo de servicios con PowerShell).