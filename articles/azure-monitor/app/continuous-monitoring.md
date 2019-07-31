---
title: Supervisión continua de la canalización de versión de DevOps con Azure Pipelines y Azure Application Insights | Microsoft Docs
description: Proporciona instrucciones para configurar rápidamente la supervisión continua con Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/16/2019
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c39a2f75fe74b61463af464078b4446bba07dec0
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2019
ms.locfileid: "68277695"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>Incorporación de supervisión continua a la canalización de versión

Azure Pipelines se integra con Azure Application Insights para permitir la supervisión continua de la canalización de versión de DevOps a lo largo del ciclo de vida de desarrollo del software. 

Con la supervisión continua, las canalizaciones de versión pueden incorporar datos de supervisión de Application Insights y otros recursos de Azure. Cuando la canalización de versión detecta una alerta de Application Insights, puede canalizar o revertir la implementación hasta que se resuelva la alerta. Si todas las comprobaciones son correctas, las implementaciones pueden continuar automáticamente de prueba a producción sin necesidad de intervención manual. 

## <a name="configure-continuous-monitoring"></a>Configuración de la supervisión continua

1. En [Azure DevOps](https://dev.azure.com) seleccione una organización y un proyecto.
   
1. En el menú de la izquierda de la página del proyecto,seleccione **Canalizaciones** > **Versiones**. 
   
1. Baje la flecha situada junto a **Nuevo** y seleccione **Nueva canalización de versión**. O bien, si aún no tiene una canalización, seleccione **Nueva canalización** en la página que aparece.
   
1. En el panel **Seleccionar una plantilla**, busque y seleccione **Implementación de Azure App Service con supervisión continua** y **Aplicar**. 

   ![Nueva canalización de versión de Azure Pipelines](media/continuous-monitoring/001.png)

1. En el cuadro **Fase 1**, seleccione el hipervínculo a **Ver tareas de la fase**.

   ![Visualización de las tareas de la fase](media/continuous-monitoring/002.png)

1. En el panel de configuración de la **Fase 1**, rellene estos campos: 

    | Parámetro        | Valor |
   | ------------- |:-----|
   | **Nombre de la fase**      | Proporcione un nombre de fase o déjelo como **Fase 1**. |
   | **Suscripción de Azure** | Desplácese hacia abajo y seleccione la suscripción de Azure vinculada que desea usar.|
   | **Tipo de aplicación** | Desplácese hacia abajo y seleccione el tipo de aplicación. |
   | **Nombre de App Service** | Escriba el nombre de la instancia de Azure App Service. |
   | **Nombre de grupo de recursos para Application Insights**    | Seleccione el grupo de recursos que quiera usar de la lista desplegable. |
   | **Nombre de recurso de Application Insights** | Seleccione de la lista desplegable el recurso de Application Insights para el grupo de recursos que seleccionó.

1. Para guardar la canalización con la configuración de la regla de alertas predeterminada, seleccione **Guardar** en la esquina superior derecha de la ventana de Azure DevOps. Escriba un comentario descriptivo y seleccione **Aceptar**.

## <a name="modify-alert-rules"></a>Modificación de las reglas de alertas

La plantilla **Implementación de Azure App Service con supervisión continua** tiene cuatro reglas de alerta listas para usar: **Disponibilidad**, **Solicitudes con errores**, **Tiempo de respuesta del servidor** y **Excepciones de servidor**. Puede agregar más reglas o cambiar la configuración de estas para satisfacer sus necesidades de nivel de servicio. 

Para modificar la configuración de las reglas de alertas:

1. En el panel izquierdo de la página canalización de versión, seleccione **Configure Application Insights Alerts** (Configurar alertas de Application Insights).

1. En el panel **Alertas de Azure Monitor**, seleccione el botón de puntos suspensivos **...** junto a **Reglas de alerta**.
   
1. En el cuadro de diálogo **Reglas de alerta**, seleccione el símbolo de lista desplegable situado junto a una regla de alerta, como **Disponibilidad**. 
   
1. Modifique el **Umbral** y otras opciones para que se ajusten a sus necesidades.
   
   ![Modificación de alerta](media/continuous-monitoring/003.png)
   
1. Seleccione **Aceptar** y **Guardar** en la esquina superior derecha de la ventana de Azure DevOps. Escriba un comentario descriptivo y seleccione **Aceptar**.

## <a name="add-deployment-conditions"></a>Incorporación de condiciones de implementación

Al agregar puertas de implementación a la canalización de versión, una alerta que supera los umbrales establecidos evita la promoción de versiones no deseadas. Una vez resuelta la alerta, la implementación puede continuar automáticamente.

Para agregar puertas de implementación:

1. En la página principal de la canalización, en **Fases**, seleccione el icono **Condiciones anteriores a la implementación** o **Condiciones posteriores a la implementación** en función de la fase donde se necesite una puerta de supervisión continua.
   
   ![Condiciones anteriores a la implementación](media/continuous-monitoring/004.png)
   
1. En el panel de configuración **Condiciones anteriores a la implementación**, establezca **Puertas** en **Habilitadas**.
   
1. Junto a **Validaciones de la implementación**, seleccione **Agregar**.
   
1. Seleccione **Consultar las alertas de Azure Monitor** en el menú desplegable. Esta opción permite acceder a las alertas de Azure Monitor y de Application Insights.
   
   ![Consultar las alertas de Azure Monitor](media/continuous-monitoring/005.png)
   
1. En **Opciones de evaluación**, escriba los valores que desee configurar, como **El tiempo entre la reevaluación de las validaciones** y **El tiempo de espera tras el cual se produce un error de las validaciones**. 

## <a name="view-release-logs"></a>Visualización de los registros de versión

En los registros de versión se puede ver el comportamiento de la puerta de implementación y otros pasos de la versión. Para abrir los registros:

1. Seleccione **Versiones** en el menú de la izquierda de la página Canalización. 
   
1. Seleccione cualquier versión. 
   
1. En **fases**, seleccione cualquier fase para ver un resumen de la versión. 
   
1. Para ver los registros, seleccione **Ver registros** en el resumen de la versión y el hipervínculo **Correcto** o **Erróneo** en cualquier fase, o mantenga el mouse sobre una fase y seleccione **Registros**. 
   
   ![Visualización de los registros de versión](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure pipelines, consulte la [documentación de Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines).
