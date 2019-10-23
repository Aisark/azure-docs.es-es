---
title: Vistas de soluciones de administración | Microsoft Docs
description: 'Las soluciones de administración incluirán normalmente una o varias vistas para visualizar los datos.  En este artículo se describe cómo exportar una vista creada por el Diseñador de vistas e incluirla en una solución de administración. '
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/16/2018
ms.openlocfilehash: 473d10bbec6ca056554f7223800a32e9ca93578e
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553904"
---
# <a name="views-in-management-solutions-preview"></a>Vistas de soluciones de administración (versión preliminar)
> [!NOTE]
> Esta es la documentación preliminar para crear soluciones de administración que se encuentran actualmente en versión preliminar. Cualquier esquema descrito a continuación está sujeto a cambios.    


Las [soluciones de administración](solutions.md) incluirán normalmente una o varias vistas para visualizar los datos.  En este artículo se describe cómo exportar una vista creada por el [Diseñador de vistas](../../azure-monitor/platform/view-designer.md) e incluirla en una solución de administración.  

> [!NOTE]
> En los ejemplos de este artículo se usan parámetros y variables que son necesarios o comunes para las soluciones de administración, y se describen en [Diseño y compilación de una solución de administración en Azure](solutions-creating.md).
>
>

## <a name="prerequisites"></a>Requisitos previos
En este artículo se supone que ya está familiarizado con la manera de [crear una solución de administración](solutions-creating.md) y la estructura de un archivo de solución.

## <a name="overview"></a>Información general
Para incluir una vista en una solución de administración, se crea un **recurso** para ella en el [archivo de solución](solutions-creating.md).  Sin embargo, el JSON que describe la configuración detallada de la vista detallada suele ser complejo y no algo que el autor de una solución típica podría crear manualmente.  El método más común es crear la vista mediante el [Diseñador de vistas](../../azure-monitor/platform/view-designer.md), exportarla y luego agregar su configuración detallada a la solución.

Los pasos básicos para agregar una vista a una solución son los siguientes.  Cada uno de los pasos se describe en detalle en las secciones que aparecen a continuación.

1. Exportar la vista a un archivo.
2. Crear el recurso de la vista en la solución.
3. Agregue los detalles de la vista.

## <a name="export-the-view-to-a-file"></a>Exportar la vista a un archivo
Siga las instrucciones del [Diseñador de vistas de Log Analytics](../../azure-monitor/platform/view-designer.md) para exportar una vista a un archivo.  El archivo exportado tendrá el formato JSON con los mismo [elementos que el archivo de solución](solutions-solution-file.md).  

El elemento **resources** del archivo de vista tendrá un recurso con un tipo de **Microsoft.OperationalInsights/workspaces** que representa el área de trabajo de Log Analytics.  Este elemento tendrá un subelemento con un tipo de **vistas** que representa la vista y contiene su configuración detallada.  Copiará los detalles de este elemento y luego copiará el elemento en la solución.

## <a name="create-the-view-resource-in-the-solution"></a>Crear el recurso de la vista en la solución
Agregue el siguiente recurso de vista al elemento **resources** del archivo de solución.  Usa las variables que se describen a continuación y que también debe agregar.  Tenga en cuenta que las propiedades **Dashboard** y **OverviewTile** son marcadores de posición que sobrescribirá con las propiedades correspondientes desde el archivo de vista exportado.

    {
        "apiVersion": "[variables('LogAnalyticsApiVersion')]",
        "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
        "type": "Microsoft.OperationalInsights/workspaces/views",
        "location": "[parameters('workspaceregionId')]",
        "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
        "dependson": [
            ],
        "properties": {
            "Id": "[variables('ViewName')]",
            "Name": "[variables('ViewName')]",
            "DisplayName": "[variables('ViewName')]",
            "Description": "",
            "Author": "[variables('ViewAuthor')]",
            "Source": "Local",
            "Dashboard": ,
            "OverviewTile":
        }
    }

Agregue las siguientes variables al elemento variables del archivo de solución y reemplace los valores por los de la solución.

    "LogAnalyticsApiVersion": "<api-version>",
    "ViewAuthor": "Your name."
    "ViewDescription": "Optional description of the view."
    "ViewName": "Provide a name for the view here."

Tenga en cuenta que podría copiar todo el recurso de vista desde el archivo de vista exportado, pero necesitaría realizar los siguientes cambios para que funcionara en su solución.  

* El **tipo** del recurso de vista debe cambiar de **vistas** a **Microsoft.OperationalInsights/workspaces**.
* La propiedad **name** del recurso de vista se debe cambiar para incluir el nombre del área de trabajo.
* La dependencia en el área de trabajo debe quitarse porque el recurso de área de trabajo no está definido en la solución.
* La propiedad **DisplayName** se debe agregar a la vista.  Los valores **Id**, **Name** y **DisplayName** deben coincidir.
* Los nombres de parámetro deben cambiarse para coincidir con el conjunto de parámetros necesario.
* Las variables deben definirse en la solución y usarse en las propiedades adecuadas.

### <a name="log-analytics-api-version"></a>Versión de la API de Log Analytics
Todos los recursos de Log Analytics definidos en una plantilla de Resource Manager tienen una propiedad **apiVersion** que define la versión de la API que el recurso debe usar.  Esta versión es diferente para las vistas con consultas que usan el [lenguaje de consulta heredado y actualizado](../../azure-monitor/log-query/log-query-overview.md).  

 En la tabla siguiente se especifican las versiones de API de Log Analytics para las vistas en los espacios de trabajo heredados y actualizados: 

| Versión del área de trabajo | Versión de API | Consultar |
|:---|:---|:---|
| v1 (heredado)   | 2015-11-01-preview | Formato heredado.<br> Ejemplo: Type=Event EventLevelName = Error  |
| v2 (actualizado) | 2015-11-01-preview | Formato heredado.  Convertido al formato actualizado en la instalación.<br> Ejemplo: Type=Event EventLevelName = Error<br>Convertido a: Event &#124; where EventLevelName == "Error"  |
| v2 (actualizado) | 2017-03-03-preview | Formato de actualización. <br>Ejemplo: Event &#124; where EventLevelName == "Error"  |


## <a name="add-the-view-details"></a>Agregar los detalles de la vista
El recurso de vista del archivo de vista exportado contendrá dos elementos en el elemento **properties** denominado **Dashboard** y **OverviewTile** que contienen la configuración detallada de la vista.  Copie los dos elementos y su contenido en el elemento **propiedades** del recurso de vista del archivo de solución.

## <a name="example"></a>Ejemplo
Por ejemplo, en el ejemplo siguiente se muestra un archivo de solución simple con una vista.  Los puntos suspensivos (...) se muestran para el contenido **Dashboard** y **OverviewTile** por motivos de espacio.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "workspaceName": {
                "type": "string"
            },
            "accountName": {
                "type": "string"
            },
            "workspaceRegionId": {
                "type": "string"
            },
            "regionId": {
                "type": "string"
            },
            "pricingTier": {
                "type": "string"
            }
        },
        "variables": {
            "SolutionVersion": "1.1",
            "SolutionPublisher": "Contoso",
            "SolutionName": "Contoso Solution",
            "LogAnalyticsApiVersion": "2015-11-01-preview",
            "ViewAuthor":  "user@contoso.com",
            "ViewDescription":  "This is a sample view.",
            "ViewName":  "Contoso View"
        },
        "resources": [
            {
                "name": "[concat(variables('SolutionName'), '(' ,parameters('workspacename'), ')')]",
                "location": "[parameters('workspaceRegionId')]",
                "tags": { },
                "type": "Microsoft.OperationsManagement/solutions",
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspacename'), '/views/', variables('ViewName'))]"
                ],
                "properties": {
                    "workspaceResourceId": "[concat(resourceGroup().id, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspacename'))]",
                    "referencedResources": [
                    ],
                    "containedResources": [
                        "[concat('Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'), '/views/', variables('ViewName'))]"
                    ]
                },
                "plan": {
                    "name": "[concat(variables('SolutionName'), '(' ,parameters('workspaceName'), ')')]",
                    "Version": "[variables('SolutionVersion')]",
                    "product": "ContosoSolution",
                    "publisher": "[variables('SolutionPublisher')]",
                    "promotionCode": ""
                }
            },
            {
                "apiVersion": "[variables('LogAnalyticsApiVersion')]",
                "name": "[concat(parameters('workspaceName'), '/', variables('ViewName'))]",
                "type": "Microsoft.OperationalInsights/workspaces/views",
                "location": "[parameters('workspaceregionId')]",
                "id": "[Concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/', resourceGroup().name, '/providers/Microsoft.OperationalInsights/workspaces/', parameters('workspaceName'),'/views/', variables('ViewName'))]",
                "dependson": [
                ],
                "properties": {
                    "Id": "[variables('ViewName')]",
                    "Name": "[variables('ViewName')]",
                    "DisplayName": "[variables('ViewName')]",
                    "Description": "[variables('ViewDescription')]",
                    "Author": "[variables('ViewAuthor')]",
                    "Source": "Local",
                    "Dashboard": ...,
                    "OverviewTile": ...
                }
            }
          ]
    }




## <a name="next-steps"></a>Pasos siguientes
* Obtener información detallada sobre cómo crear [soluciones de administración](solutions-creating.md).
* Incluir [runbooks de Automation en la solución de administración](solutions-resources-automation.md).
