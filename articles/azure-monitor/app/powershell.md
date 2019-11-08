---
title: Automatización de Azure Application Insights con PowerShell | Microsoft Docs
description: Automatice la creación y administración de recursos, alertas y pruebas de disponibilidad en PowerShell mediante una plantilla de Azure Resource Manager.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/17/2019
ms.openlocfilehash: 5ae043c356559b2e675f05af3eb7eb61973eb170
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2019
ms.locfileid: "73621934"
---
#  <a name="manage-application-insights-resources-using-powershell"></a>Administración de recursos de Application Insights mediante PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Este artículo muestra cómo automatizar la creación y actualización de los recursos de [Application Insights](../../azure-monitor/app/app-insights-overview.md) automáticamente mediante Azure Resource Management. Puede hacerlo, por ejemplo, como parte de un proceso de compilación. Junto con el recurso básico de Application Insights, puede crear [pruebas web de disponibilidad](../../azure-monitor/app/monitor-web-app-availability.md), [configurar alertas](../../azure-monitor/app/alerts.md), establecer el [esquema de precios](pricing.md) y crear otros recursos de Azure.

La clave para crear estos recursos es las plantillas JSON para el [Administrador de recursos de Azure](../../azure-resource-manager/manage-resources-powershell.md). El procedimiento básico es: descargar las definiciones JSON de los recursos existentes; parametrizar determinados valores como los nombres; y luego ejecutar la plantilla siempre que se quiera crear un nuevo recurso. Puede empaquetar varios recursos juntos para crearlos todos en un solo paso, por ejemplo, un monitor de aplicaciones con pruebas de disponibilidad, alertas y almacenamiento para la exportación continua. Existen algunos matices a algunas de las parametrizaciones automáticas, que se explican aquí.

## <a name="one-time-setup"></a>Instalación única
Si no ha usado PowerShell con su suscripción de Azure antes:

Instale el módulo de Azure Powershell en la máquina donde quiere ejecutar los scripts.

1. Instale el [Instalador de plataforma web de Microsoft (v5 o superior)](https://www.microsoft.com/web/downloads/platform.aspx).
2. Úselo para instalar Microsoft Azure Powershell.

Además de usar plantillas de Resource Manager, existe un completo conjunto de [cmdlets de PowerShell en Application Insights ](https://docs.microsoft.com/powershell/module/az.applicationinsights) que facilitan la configuración de recursos de Application Insights mediante programación. Entre las funcionalidades que habilitan los cmdlets se incluyen las siguientes:

* Crear y eliminar recursos de Application Insights
* Obtener listas de recursos de Application Insights y sus propiedades
* Crear y administrar la exportación continua
* Crear y administrar claves de aplicación
* Establecer el límite diario
* Establecer el plan de precios

## <a name="create-application-insights-resources-using-a-powershell-cmdlet"></a>Creación de recursos de Application Insights mediante un cmdlet de PowerShell

Aquí se muestra cómo crear un nuevo recurso de Application Insights en el centro de datos Este de EE. UU. de Azure con el cmdlet [New-AzApplicationInsights](https://docs.microsoft.com/powershell/module/az.applicationinsights/New-AzApplicationInsights):

```PS
New-AzApplicationInsights -ResourceGroupName <resource group> -Name <resource name> -location eastus
```


## <a name="create-application-insights-resources-using-a-resource-manager-template"></a>Creación de recursos de Application Insights mediante una plantilla de Resource Manager

Aquí se muestra cómo crear un nuevo recurso de Application Insights mediante una plantilla de Resource Manager.

### <a name="create-the-azure-resource-manager-template"></a>Creación de la plantilla de Azure Resource Manager

Cree un nuevo archivo .json. Vamos a llamarlo `template1.json` en este ejemplo. Copie este contenido en él:

```JSON
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "appName": {
                "type": "string",
                "metadata": {
                    "description": "Enter the name of your Application Insights resource."
                }
            },
            "appType": {
                "type": "string",
                "defaultValue": "web",
                "allowedValues": [
                    "web",
                    "java",
                    "other"
                ],
                "metadata": {
                    "description": "Enter the type of the monitored application."
                }
            },
            "appLocation": {
                "type": "string",
                "defaultValue": "eastus",
                "metadata": {
                    "description": "Enter the location of your Application Insights resource."
                }
            },
            "retentionInDays": {
                "type": "int",
                "defaultValue": 90,
                "allowedValues": [
                    30,
                    60,
                    90,
                    120,
                    180,
                    270,
                    365,
                    550,
                    730
                ],
                "metadata": {
                    "description": "Data retention in days"
                }
            },
            "ImmediatePurgeDataOn30Days": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "If set to true when changing retention to 30 days, older data will be immediately deleted. Use this with extreme caution. This only applies when retention is being set to 30 days."
                }
            },
            "priceCode": {
                "type": "int",
                "defaultValue": 1,
                "allowedValues": [
                    1,
                    2
                ],
                "metadata": {
                    "description": "Pricing plan: 1 = Per GB (or legacy Basic plan), 2 = Per Node (legacy Enterprise plan)"
                }
            },
            "dailyQuota": {
                "type": "int",
                "defaultValue": 100,
                "minValue": 1,
                "metadata": {
                    "description": "Enter daily quota in GB."
                }
            },
            "dailyQuotaResetTime": {
                "type": "int",
                "defaultValue": 24,
                "metadata": {
                    "description": "Enter daily quota reset hour in UTC (0 to 23). Values outside the range will get a random reset hour."
                }
            },
            "warningThreshold": {
                "type": "int",
                "defaultValue": 90,
                "minValue": 1,
                "maxValue": 100,
                "metadata": {
                    "description": "Enter the % value of daily quota after which warning mail to be sent. "
                }
            }
        },
        "variables": {
            "priceArray": [
                "Basic",
                "Application Insights Enterprise"
            ],
            "pricePlan": "[take(variables('priceArray'),parameters('priceCode'))]",
            "billingplan": "[concat(parameters('appName'),'/', variables('pricePlan')[0])]"
        },
        "resources": [
            {
                "type": "microsoft.insights/components",
                "kind": "[parameters('appType')]",
                "name": "[parameters('appName')]",
                "apiVersion": "2014-04-01",
                "location": "[parameters('appLocation')]",
                "tags": {},
                "properties": {
                    "ApplicationId": "[parameters('appName')]"
                },
                "dependsOn": []
            },
            {
                "name": "[variables('billingplan')]",
                "type": "microsoft.insights/components/CurrentBillingFeatures",
                "location": "[parameters('appLocation')]",
                "apiVersion": "2015-05-01",
                "dependsOn": [
                    "[resourceId('microsoft.insights/components', parameters('appName'))]"
                ],
                "properties": {
                    "CurrentBillingFeatures": "[variables('pricePlan')]",
                    "retentionInDays": "[parameters('retentionInDays')]",
                    "DataVolumeCap": {
                        "Cap": "[parameters('dailyQuota')]",
                        "WarningThreshold": "[parameters('warningThreshold')]",
                        "ResetTime": "[parameters('dailyQuotaResetTime')]"
                    }
                }
            }
        ]
    }
```

### <a name="use-the-resource-manager-template-to-create-a-new-application-insights-resource"></a>Uso de la plantilla de Resource Manager para crear un nuevo recurso de Application Insights

1. En PowerShell, inicie sesión en Azure mediante `$Connect-AzAccount`.
2. Establezca el contexto en una suscripción con `Set-AzContext "<subscription ID>"`.
2. Ejecute una nueva implementación para crear un nuevo recurso de Application Insights:
   
    ```PS
        New-AzResourceGroupDeployment -ResourceGroupName Fabrikam `
               -TemplateFile .\template1.json `
               -appName myNewApp

    ``` 
   
   * `-ResourceGroupName` es el grupo donde desea crear los nuevos recursos.
   * `-TemplateFile` debe aparecer antes que los parámetros personalizados.
   * `-appName` es el nombre del recurso que se va a crear.

Puede agregar otros parámetros, cuyas descripciones se encuentran en la sección de parámetros de la plantilla.

## <a name="get-the-instrumentation-key"></a>Obtención de la clave de instrumentación

Después de crear un recurso de aplicación, querrá la clave de instrumentación: 

1. `$Connect-AzAccount`
2. `Set-AzContext "<subscription ID>"`
3. `$resource = Get-AzResource -Name "<resource name>" -ResourceType "Microsoft.Insights/components"`
4. `$details = Get-AzResource -ResourceId $resource.ResourceId`
5. `$details.Properties.InstrumentationKey`

Para ver una lista de muchas otras propiedades del recurso de Application Insights, use el siguiente comando:

```PS
Get-AzApplicationInsights -ResourceGroupName Fabrikam -Name FabrikamProd | Format-List
```

Dispone de propiedades adicionales a través de los siguientes cmdlets:
* `Set-AzApplicationInsightsDailyCap`
* `Set-AzApplicationInsightsPricingPlan`
* `Get-AzApplicationInsightsApiKey`
* `Get-AzApplicationInsightsContinuousExport`

Consulte la [documentación detallada](https://docs.microsoft.com/powershell/module/az.applicationinsights) para información sobre los parámetros de estos cmdlets.  

## <a name="set-the-data-retention"></a>Establecimiento de la retención de datos 

Para obtener la retención actual de datos del recurso de Application Insights, puede usar la herramienta OSS [ARMClient](https://github.com/projectkudu/ARMClient)  (puede obtener más información sobre ARMClient en los artículos de [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) y [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/)).  Este es un ejemplo del uso de `ARMClient` para obtener la retención actual:

```PS
armclient GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview
```

Para establecer la retención, se usa un comando PUT similar:

```PS
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/microsoft.insights/components/MyResourceName?api-version=2018-05-01-preview "{location: 'eastus', properties: {'retentionInDays': 365}}"
```

Para establecer la retención de datos en 365 días mediante la plantilla anterior, ejecute:

```PS
New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
       -TemplateFile .\template1.json `
       -retentionInDays 365 `
       -appName myApp
```

El siguiente script también se puede usar para cambiar la retención. Copie este script para guardarlo como `Set-ApplicationInsightsRetention.ps1`.

```PS
Param(
    [Parameter(Mandatory = $True)]
    [string]$SubscriptionId,

    [Parameter(Mandatory = $True)]
    [string]$ResourceGroupName,

    [Parameter(Mandatory = $True)]
    [string]$Name,

    [Parameter(Mandatory = $True)]
    [string]$RetentionInDays
)
$ErrorActionPreference = 'Stop'
if (-not (Get-Module Az.Accounts)) {
    Import-Module Az.Accounts
}
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
if (-not $azProfile.Accounts.Count) {
    Write-Error "Ensure you have logged in before calling this function."    
}
$currentAzureContext = Get-AzContext
$profileClient = New-Object Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient($azProfile)
$token = $profileClient.AcquireAccessToken($currentAzureContext.Tenant.TenantId)
$UserToken = $token.AccessToken
$RequestUri = "https://management.azure.com/subscriptions/$($SubscriptionId)/resourceGroups/$($ResourceGroupName)/providers/Microsoft.Insights/components/$($Name)?api-version=2015-05-01"
$Headers = @{
    "Authorization"         = "Bearer $UserToken"
    "x-ms-client-tenant-id" = $currentAzureContext.Tenant.TenantId
}
## Get Component object via ARM
$GetResponse = Invoke-RestMethod -Method "GET" -Uri $RequestUri -Headers $Headers 

## Update RetentionInDays property
if($($GetResponse.properties | Get-Member "RetentionInDays"))
{
    $GetResponse.properties.RetentionInDays = $RetentionInDays
}
else
{
    $GetResponse.properties | Add-Member -Type NoteProperty -Name "RetentionInDays" -Value $RetentionInDays
}
## Upsert Component object via ARM
$PutResponse = Invoke-RestMethod -Method "PUT" -Uri "$($RequestUri)" -Headers $Headers -Body $($GetResponse | ConvertTo-Json) -ContentType "application/json"
$PutResponse
```

Después, este script se puede utilizar para:

```PS
Set-ApplicationInsightsRetention `
        [-SubscriptionId] <String> `
        [-ResourceGroupName] <String> `
        [-Name] <String> `
        [-RetentionInDays <Int>]
```

## <a name="set-the-daily-cap"></a>Establecimiento del límite diario

Para obtener las propiedades de límite diario, use el cmdlet [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan): 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para establecer las propiedades de límite diario, use el mismo cmdlet. Por ejemplo, para establecer el límite en 300 GB/día: 

```PS
Set-AzApplicationInsightsDailyCap -ResourceGroupName <resource group> -Name <resource name> -DailyCapGB 300
```

<a id="price"></a>
## <a name="set-the-pricing-plan"></a>Establecimiento del plan de precios 

Para obtener el plan de precios actual, use el cmdlet [Set-AzApplicationInsightsPricingPlan](https://docs.microsoft.com/powershell/module/az.applicationinsights/Set-AzApplicationInsightsPricingPlan): 

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> | Format-List
```

Para establecer el plan de precios, use el mismo cmdlet con la especificación de `-PricingPlan`:  

```PS
Set-AzApplicationInsightsPricingPlan -ResourceGroupName <resource group> -Name <resource name> -PricingPlan Basic
```

También puede establecer el plan de precios de un recurso de Application Insights existente mediante la plantilla de Resource Manager anterior, omitiendo el recurso "microsoft.insights/components" y el nodo `dependsOn` del recurso de facturación. Por ejemplo, para establecerlo en el plan Por GB (antes plan Básico), ejecute:

```PS
        New-AzResourceGroupDeployment -ResourceGroupName "<resource group>" `
               -TemplateFile .\template1.json `
               -priceCode 1 `
               -appName myApp
```

|priceCode|plan|
|---|---|
|1|Por GB (anteriormente denominado plan Básico)|
|2|Por nodo (anteriormente denominado plan Enterprise)|

## <a name="add-a-metric-alert"></a>Agregar una alerta de métrica

Para configurar una alerta de métrica al mismo tiempo que el recurso de aplicación, combine código similar al siguiente en el archivo de plantilla:

```JSON
{
    parameters: { ... // existing parameters ...
            "responseTime": {
              "type": "int",
              "defaultValue": 3,
              "minValue": 1,
              "metadata": {
                "description": "Enter response time threshold in seconds."
              }
    },
    variables: { ... // existing variables ...
      // Alert names must be unique within resource group.
      "responseAlertName": "[concat('ResponseTime-', toLower(parameters('appName')))]"
    }, 
    resources: { ... // existing resources ...
     {
      //
      // Metric alert on response time
      //
      "name": "[variables('responseAlertName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this resource is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('responseAlertName')]",
        "description": "response time alert",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/components', parameters('appName'))]",
            "metricName": "request.duration"
          },
          "threshold": "[parameters('responseTime')]", //seconds
          "windowSize": "PT15M" // Take action if changed state for 15 minutes
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Al invocar la plantilla, tiene la opción de agregar este parámetro:

    `-responseTime 2`

También puede parametrizar otros campos. 

Para buscar los nombres de tipo y los detalles de configuración de otras reglas de alerta, cree manualmente una regla y, a continuación, inspecciónela en [Azure Resource Manager](https://resources.azure.com/). 


## <a name="add-an-availability-test"></a>Agregar una prueba de disponibilidad

Este ejemplo es para una prueba de ping (para probar una sola página).  

**Hay dos partes** en una prueba de disponibilidad: la propia prueba y la alerta que informa de errores.

Combine el código siguiente en el archivo de plantilla que crea la aplicación.

```JSON
{
    parameters: { ... // existing parameters here ...
      "pingURL": { "type": "string" },
      "pingText": { "type": "string" , defaultValue: ""}
    },
    variables: { ... // existing variables here ...
      "pingTestName":"[concat('PingTest-', toLower(parameters('appName')))]",
      "pingAlertRuleName": "[concat('PingAlert-', toLower(parameters('appName')), '-', subscription().subscriptionId)]"
    },
    resources: { ... // existing resources here ...
    { //
      // Availability test: part 1 configures the test
      //
      "name": "[variables('pingTestName')]",
      "type": "Microsoft.Insights/webtests",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]",
      // Ensure this is created after the app resource:
      "dependsOn": [
        "[resourceId('Microsoft.Insights/components', parameters('appName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource"
      },
      "properties": {
        "Name": "[variables('pingTestName')]",
        "Description": "Basic ping test",
        "Enabled": true,
        "Frequency": 900, // 15 minutes
        "Timeout": 120, // 2 minutes
        "Kind": "ping", // single URL test
        "RetryEnabled": true,
        "Locations": [
          {
            "Id": "us-va-ash-azr"
          },
          {
            "Id": "emea-nl-ams-azr"
          },
          {
            "Id": "apac-jp-kaw-edge"
          }
        ],
        "Configuration": {
          "WebTest": "[concat('<WebTest   Name=\"', variables('pingTestName'), '\"   Enabled=\"True\"         CssProjectStructure=\"\"    CssIteration=\"\"  Timeout=\"120\"  WorkItemIds=\"\"         xmlns=\"http://microsoft.com/schemas/VisualStudio/TeamTest/2010\"         Description=\"\"  CredentialUserName=\"\"  CredentialPassword=\"\"         PreAuthenticate=\"True\"  Proxy=\"default\"  StopOnError=\"False\"         RecordedResultFile=\"\"  ResultsLocale=\"\">  <Items>  <Request Method=\"GET\"    Version=\"1.1\"  Url=\"', parameters('Url'),   '\" ThinkTime=\"0\"  Timeout=\"300\" ParseDependentRequests=\"True\"         FollowRedirects=\"True\" RecordResult=\"True\" Cache=\"False\"         ResponseTimeGoal=\"0\"  Encoding=\"utf-8\"  ExpectedHttpStatusCode=\"200\"         ExpectedResponseUrl=\"\" ReportingName=\"\" IgnoreHttpStatusCode=\"False\" />        </Items>  <ValidationRules> <ValidationRule  Classname=\"Microsoft.VisualStudio.TestTools.WebTesting.Rules.ValidationRuleFindText, Microsoft.VisualStudio.QualityTools.WebTestFramework, Version=10.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a\" DisplayName=\"Find Text\"         Description=\"Verifies the existence of the specified text in the response.\"         Level=\"High\"  ExecutionOrder=\"BeforeDependents\">  <RuleParameters>        <RuleParameter Name=\"FindText\" Value=\"',   parameters('pingText'), '\" />  <RuleParameter Name=\"IgnoreCase\" Value=\"False\" />  <RuleParameter Name=\"UseRegularExpression\" Value=\"False\" />  <RuleParameter Name=\"PassIfTextFound\" Value=\"True\" />  </RuleParameters> </ValidationRule>  </ValidationRules>  </WebTest>')]"
        },
        "SyntheticMonitorId": "[variables('pingTestName')]"
      }
    },

    {
      //
      // Availability test: part 2, the alert rule
      //
      "name": "[variables('pingAlertRuleName')]",
      "type": "Microsoft.Insights/alertrules",
      "apiVersion": "2014-04-01",
      "location": "[parameters('appLocation')]", 
      "dependsOn": [
        "[resourceId('Microsoft.Insights/webtests', variables('pingTestName'))]"
      ],
      "tags": {
        "[concat('hidden-link:', resourceId('Microsoft.Insights/components', parameters('appName')))]": "Resource",
        "[concat('hidden-link:', resourceId('Microsoft.Insights/webtests', variables('pingTestName')))]": "Resource"
      },
      "properties": {
        "name": "[variables('pingAlertRuleName')]",
        "description": "alert for web test",
        "isEnabled": true,
        "condition": {
          "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.LocationThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
          "odata.type": "Microsoft.Azure.Management.Insights.Models.LocationThresholdRuleCondition",
          "dataSource": {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
            "resourceUri": "[resourceId('microsoft.insights/webtests', variables('pingTestName'))]",
            "metricName": "GSMT_AvRaW"
          },
          "windowSize": "PT15M", // Take action if changed state for 15 minutes
          "failedLocationCount": 2
        },
        "actions": [
          {
            "$type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
            "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
            "sendToServiceOwners": true,
            "customEmails": []
          }
        ]
      }
    }
}
```

Para detectar los códigos de otras ubicaciones de prueba, o para automatizar la creación de pruebas web más complejas, cree un ejemplo manualmente y luego parametrice el código en [Azure Resource Manager](https://resources.azure.com/).

## <a name="add-more-resources"></a>Agregar más recursos

Para automatizar la creación de otros recursos de cualquier variante, cree un ejemplo manualmente y luego copie y parametrice su código en [Azure Resource Manager](https://resources.azure.com/). 

1. Abra el [Administrador de recursos de Azure](https://resources.azure.com/). Desplácese hacia abajo por `subscriptions/resourceGroups/<your resource group>/providers/Microsoft.Insights/components` hasta el recurso de la aplicación. 
   
    ![Navegación en el Explorador de recursos de Azure](./media/powershell/01.png)
   
    *Componentes* son los recursos básicos de Application Insights para mostrar aplicaciones. Existen recursos distintos para las reglas de alerta asociadas y las pruebas web de disponibilidad.
2. Copie el código JSON del componente en el lugar adecuado en `template1.json`.
3. Elimine estas propiedades:
   
   * `id`
   * `InstrumentationKey`
   * `CreationDate`
   * `TenantId`
4. Abra las secciones `webtests` y `alertrules` y copie el código JSON de los elementos individuales en la plantilla. No copie de los nodos `webtests` o `alertrules`: vaya a los elementos que hay debajo de ellos.
   
    Cada prueba web tiene una regla de alerta asociada, por lo que tiene que copiar las dos.
   
    También puede incluir alertas en las métricas. [Nombres de métricas](powershell-alerts.md#metric-names).
5. Inserte esta línea en cada recurso:
   
    `"apiVersion": "2015-05-01",`

### <a name="parameterize-the-template"></a>Parametrización de la plantilla
Ahora, debe reemplazar los nombres específicos por parámetros. Para [parametrizar una plantilla](../../azure-resource-manager/resource-group-authoring-templates.md), escriba expresiones mediante un [conjunto de funciones auxiliares](../../azure-resource-manager/resource-group-template-functions.md). 

No se puede parametrizar solo una parte de una cadena, así que use `concat()` para compilar las cadenas.

Éstos son ejemplos de sustituciones que querrá realizar. Hay varias repeticiones de cada sustitución. Y puede que tenga otras en la plantilla. En estos ejemplos se usan los parámetros y las variables que se han definido en la parte superior de la plantilla.

| find | reemplazar por |
| --- | --- |
| `"hidden-link:/subscriptions/.../../components/MyAppName"` |`"[concat('hidden-link:',`<br/>`resourceId('microsoft.insights/components',` <br/> `parameters('appName')))]"` |
| `"/subscriptions/.../../alertrules/myAlertName-myAppName-subsId",` |`"[resourceId('Microsoft.Insights/alertrules', variables('alertRuleName'))]",` |
| `"/subscriptions/.../../webtests/myTestName-myAppName",` |`"[resourceId('Microsoft.Insights/webtests', parameters('webTestName'))]",` |
| `"myWebTest-myAppName"` |`"[variables(testName)]"'` |
| `"myTestName-myAppName-subsId"` |`"[variables('alertRuleName')]"` |
| `"myAppName"` |`"[parameters('appName')]"` |
| `"myappname"` (minúscula) |`"[toLower(parameters('appName'))]"` |
| `"<WebTest Name=\"myWebTest\" ...`<br/>`Url=\"http://fabrikam.com/home\" ...>"` |`[concat('<WebTest Name=\"',` <br/> `parameters('webTestName'),` <br/> `'\" ... Url=\"', parameters('Url'),` <br/> `'\"...>')]"`|

### <a name="set-dependencies-between-the-resources"></a>Establecimiento de dependencias entre los recursos
Azure debe instalar los recursos en un orden estricto. Para tener la seguridad de que una instalación finaliza antes de que comience la siguiente, agregue líneas de dependencia:

* En el recurso de la prueba de disponibilidad:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/components', parameters('appName'))]"],`
* En el recurso de la alerta para una prueba de disponibilidad:
  
    `"dependsOn": ["[resourceId('Microsoft.Insights/webtests', variables('testName'))]"],`



## <a name="next-steps"></a>Pasos siguientes
Otros artículos de automatización:

* [Script de PowerShell para crear un recurso de Application Insights](powershell-script-create-resource.md) : método rápido sin necesidad de plantilla.
* [Uso de PowerShell para configurar alertas en Application Insights](powershell-alerts.md)
* [Creación de pruebas web](https://azure.microsoft.com/blog/creating-a-web-test-alert-programmatically-with-application-insights/)
* [Envío de Azure Diagnostics a Application Insights](powershell-azure-diagnostics.md)
* [Implementación en Azure desde GitHub](https://blogs.msdn.com/b/webdev/archive/2015/09/16/deploy-to-azure-from-github-with-application-insights.aspx)
* [Creación de anotaciones de versión](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1)
