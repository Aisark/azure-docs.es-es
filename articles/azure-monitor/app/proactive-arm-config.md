---
title: Configuración de los valores de reglas de detección inteligente de Azure Application Insights con plantillas de Azure Resource Manager | Microsoft Docs
description: Automatización de la administración y configuración de reglas de detección inteligente de Azure Application Insights con plantillas de Azure Resource Manager
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 06/26/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8b55271b39bf2a65dababbef58f7389ca07d57d8
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72818834"
---
# <a name="manage-application-insights-smart-detection-rules-using-azure-resource-manager-templates"></a>Administración de reglas de detección inteligente de Application Insights con plantillas de Azure Resource Manager

Las reglas de detección inteligente de Application Insights se pueden administrar y configurar con [plantillas de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
Este método puede usarse al implementar nuevos recursos de Application Insights con la automatización de Azure Resource Manager o para modificar la configuración de los recursos existentes.

## <a name="smart-detection-rule-configuration"></a>Configuración de una regla de detección inteligente

Puede configurar los siguientes valores en una regla de detección inteligente:
- Si la regla se habilita (el valor predeterminado es **true**).
- Si no se deben enviar correos electrónicos a los usuarios asociados con los roles [Lector de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) y [Colaborador de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) cuando se encuentra una detección (el valor predeterminado es **true**).
- Los destinatarios de correo electrónico adicionales que deben recibir una notificación cuando se encuentra una detección.
    -  La configuración de correo electrónico no está disponible para las reglas de detección inteligente marcadas como _versión preliminar_.

Para permitir la configuración de los valores de regla a través de Azure Resource Manager, la configuración de reglas de detección inteligente ahora está disponible como recurso interno en el recurso de Application Insights denominado **ProactiveDetectionConfigs**.
Para obtener la máxima flexibilidad, cada regla de detección inteligente puede configurarse con valores de notificación únicos.

## 

## <a name="examples"></a>Ejemplos

A continuación se muestran algunos ejemplos que muestran cómo configurar los valores de reglas de detección inteligente mediante plantillas de Azure Resource Manager.
Todos los ejemplos hacen referencia a un recurso de Application Insights denominado _"myApplication"_ y a la "regla de detección inteligente de duración de la dependencia prolongada" que internamente se denomina _"longdependencyduration"_ .
Asegúrese de reemplazar el nombre de recurso de Application Insights y de especificar el nombre interno de la regla de detección inteligente pertinente. En la tabla siguiente, busque una lista de los nombres internos de Azure Resource Manager correspondientes a cada regla de detección inteligente.

### <a name="disable-a-smart-detection-rule"></a>Deshabilitación de una regla de detección inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": [],
            "enabled": false
          }
        }
      ]
    }
```

### <a name="disable-sending-email-notifications-for-a-smart-detection-rule"></a>Deshabilitación del envío de notificaciones por correo electrónico para una regla de detección inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": false,
            "customEmails": [],
            "enabled": true
          }
        }
      ]
    }
```

### <a name="add-additional-email-recipients-for-a-smart-detection-rule"></a>Adición de otros destinatarios de correo electrónico para una regla de detección inteligente

```json
{
      "apiVersion": "2018-05-01-preview",
      "name": "myApplication",
      "type": "Microsoft.Insights/components",
      "location": "[resourceGroup().location]",
      "properties": {
        "ApplicationId": "myApplication"
      },
      "resources": [
        {
          "apiVersion": "2018-05-01-preview",
          "name": "longdependencyduration",
          "type": "ProactiveDetectionConfigs",
          "location": "[resourceGroup().location]",
          "dependsOn": [
            "[resourceId('Microsoft.Insights/components', 'myApplication')]"
          ],
          "properties": {
            "name": "longdependencyduration",
            "sendEmailsToSubscriptionOwners": true,
            "customEmails": ['alice@contoso.com', 'bob@contoso.com'],
            "enabled": true
          }
        }
      ]
    }

```

### <a name="failure-anomalies-v2-non-classic-alert-rule"></a>Regla de alertas Anomalías en los errores v2 (no clásicas)

Esta plantilla de Azure Resource Manager muestra la configuración de una regla de alertas de Anomalías en los errores v2 con una gravedad de 2. Esta versión nueva de la regla de alertas de Anomalías en los errores es parte de la nueva plataforma de alertas de Azure y reemplaza la versión clásica que se retira como parte del [proceso de retirada de alertas clásicas](https://azure.microsoft.com/updates/classic-alerting-monitoring-retirement/).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Failure Anomalies - my-app",
            "location": "global", 
            "properties": {
                  "description": "Failure Anomalies notifies you of an unusual rise in the rate of failed HTTP requests or dependency calls.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT1M",
                  "detector": {
                  "id": "FailureAnomaliesDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```

> [!NOTE]
> Esta plantilla de Azure Resource Manager es única para la regla de alertas de Anomalías en los errores v2 y es distinta de las otras reglas clásicas de detección inteligente que se describen en este artículo.   

## <a name="smart-detection-rule-names"></a>Nombres de regla de detección inteligente

Seguidamente se muestra una tabla de nombres de reglas de detección inteligente tal y como aparecen en el portal, así como sus nombres internos, que deben utilizarse en la plantilla de Azure Resource Manager.

> [!NOTE]
> Las reglas de detección inteligente marcadas como de _versión preliminar_ no admiten notificaciones por correo electrónico. Por lo tanto, solo se puede establecer la propiedad _habilitada_ para estas reglas. 

| Nombre de regla de Azure Portal | Nombre interno
|:---|:---|
| Carga lenta de página | slowpageloadtime |
| Lentitud en el tiempo de respuesta del servidor | slowserverresponsetime |
| Duración de la dependencia prolongada | longdependencyduration |
| Degradación del tiempo de respuesta del servidor | degradationinserverresponsetime |
| Reducción de la duración de la dependencia | degradationindependencyduration |
| Degradación en la relación de gravedad de seguimiento (versión preliminar) | extension_traceseveritydetector |
| Aumento anormal del volumen de excepciones (versión preliminar) | extension_exceptionchangeextension |
| Detección de una posible fuga de memoria (versión preliminar) | extension_memoryleakextension |
| Detección de un posible problema de seguridad (versión preliminar) | extension_securityextensionspackage |
| Aumento anómalo del volumen de datos diario (versión preliminar) | extension_billingdatavolumedailyspikeextension |

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la detección automática:

- [Anomalías de error](../../azure-monitor/app/proactive-failure-diagnostics.md)
- [Fugas de memoria](../../azure-monitor/app/proactive-potential-memory-leak.md)
- [Anomalías de rendimiento](../../azure-monitor/app/proactive-performance-diagnostics.md)
