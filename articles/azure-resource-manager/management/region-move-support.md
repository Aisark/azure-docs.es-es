---
title: Compatibilidad con el movimiento de recursos de Azure entre regiones
description: Enumera los tipos de recursos de Azure que se pueden transferir entre regiones de Azure.
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: reference
ms.date: 01/20/2020
ms.author: raynew
ms.openlocfilehash: 9bc7dc66ccf3049ac878f7871c816e5ade1afde5
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760715"
---
# <a name="support-for-moving-azure-resources-across-regions"></a>Compatibilidad con el movimiento de recursos de Azure entre regiones

En este artículo se confirma si un tipo de recurso de Azure es compatible para el movimiento a otra región de Azure. 

Vaya a un espacio de nombres del proveedor de recursos:
> [!div class="op_single_selector"]
> - [Microsoft.AAD](#microsoftaad)
> - [microsoft.aadiam](#microsoftaadiam)
> - [Microsoft.AlertsManagement](#microsoftalertsmanagement)
> - [Microsoft.AnalysisServices](#microsoftanalysisservices)
> - [Microsoft.ApiManagement](#microsoftapimanagement)
> - [Microsoft.AppConfiguration](#microsoftappconfiguration)
> - [Microsoft.AppService](#microsoftappservice)
> - [Microsoft.Authorization](#microsoftauthorization)
> - [Microsoft.Automation](#microsoftautomation)
> - [Microsoft.AzureActiveDirectory](#microsoftazureactivedirectory)
> - [Microsoft.AzureData](#microsoftazuredata)
> - [Microsoft.AzureStack](#microsoftazurestack)
> - [Microsoft.Batch](#microsoftbatch)
> - [Microsoft.BatchAI](#microsoftbatchai)
> - [Microsoft.BingMaps](#microsoftbingmaps)
> - [Microsoft.BizTalkServices](#microsoftbiztalkservices)
> - [Microsoft.Blockchain](#microsoftblockchain)
> - [Microsoft.Blueprint](#microsoftblueprint)
> - [Microsoft.BotService](#microsoftbotservice)
> - [Microsoft.Cache](#microsoftcache)
> - [Microsoft.Cdn](#microsoftcdn)
> - [Microsoft.CertificateRegistration](#microsoftcertificateregistration)
> - [Microsoft.ClassicCompute](#microsoftclassiccompute)
> - [Microsoft.ClassicNetwork](#microsoftclassicnetwork)
> - [Microsoft.ClassicStorage](#microsoftclassicstorage)
> - [Microsoft.CognitiveServices](#microsoftcognitiveservices)
> - [Microsoft.Compute](#microsoftcompute)
> - [Microsoft.Container](#microsoftcontainer)
> - [Microsoft.ContainerInstance](#microsoftcontainerinstance)
> - [Microsoft.ContainerRegistry](#microsoftcontainerregistry)
> - [Microsoft.ContainerService](#microsoftcontainerservice)
> - [Microsoft.ContentModerator](#microsoftcontentmoderator)
> - [Microsoft.CortanaAnalytics](#microsoftcortanaanalytics)
> - [Microsoft.CostManagement](#microsoftcostmanagement)
> - [Microsoft.CustomerInsights](#microsoftcustomerinsights)
> - [Microsoft.CustomProviders](#microsoftcustomproviders)
> - [Microsoft.DataBox](#microsoftdatabox)
> - [Microsoft.DataBoxEdge](#microsoftdataboxedge)
> - [Microsoft.Databricks](#microsoftdatabricks)
> - [Microsoft.DataCatalog](#microsoftdatacatalog)
> - [Microsoft.DataConnect](#microsoftdataconnect)
> - [Microsoft.DataExchange](#microsoftdataexchange)
> - [Microsoft.DataFactory](#microsoftdatafactory)
> - [Microsoft.DataLake](#microsoftdatalake)
> - [Microsoft.DataLakeAnalytics](#microsoftdatalakeanalytics)
> - [Microsoft.DataLakeStore](#microsoftdatalakestore)
> - [Microsoft.DataMigration](#microsoftdatamigration)
> - [Microsoft.DataShare](#microsoftdatashare)
> - [Microsoft.DBforMariaDB](#microsoftdbformariadb)
> - [Microsoft.DBforMySQL](#microsoftdbformysql)
> - [Microsoft.DBforPostgreSQL](#microsoftdbforpostgresql)
> - [Microsoft.DeploymentManager](#microsoftdeploymentmanager)
> - [Microsoft.Devices](#microsoftdevices)
> - [Microsoft.DevSpaces](#microsoftdevspaces)
> - [Microsoft.DevTestLab](#microsoftdevtestlab)
> - [Microsoft.DocumentDB](#microsoftdocumentdb)
> - [Microsoft.DomainRegistration](#microsoftdomainregistration)
> - [Microsoft.EnterpriseKnowledgeGraph](#microsoftenterpriseknowledgegraph)
> - [Microsoft.EventGrid](#microsofteventgrid)
> - [Microsoft.EventHub](#microsofteventhub)
> - [Microsoft.Genomics](#microsoftgenomics)
> - [Microsoft.HanaOnAzure](#microsofthanaonazure)
> - [Microsoft.HDInsight](#microsofthdinsight)
> - [Microsoft.HealthcareApis](#microsofthealthcareapis)
> - [Microsoft.HybridCompute](#microsofthybridcompute)
> - [Microsoft.HybridData](#microsofthybriddata)
> - [Microsoft.ImportExport](#microsoftimportexport)
> - [microsoft.insights](#microsoftinsights)
> - [Microsoft.IoTCentral](#microsoftiotcentral)
> - [Microsoft.IoTSpaces](#microsoftiotspaces)
> - [Microsoft.KeyVault](#microsoftkeyvault)
> - [Microsoft.Kusto](#microsoftkusto)
> - [Microsoft.LabServices](#microsoftlabservices)
> - [Microsoft.LocationBasedServices](#microsoftlocationbasedservices)
> - [Microsoft.LocationServices](#microsoftlocationservices)
> - [Microsoft.Logic](#microsoftlogic)
> - [Microsoft.MachineLearning](#microsoftmachinelearning)
> - [Microsoft.MachineLearningCompute](#microsoftmachinelearningcompute)
> - [Microsoft.MachineLearningExperimentation](#microsoftmachinelearningexperimentation)
> - [Microsoft.MachineLearningModelManagement](#microsoftmachinelearningmodelmanagement)
> - [Microsoft.MachineLearningOperationalization](#microsoftmachinelearningoperationalization)
> - [Microsoft.MachineLearningServices](#microsoftmachinelearningservices)
> - [Microsoft.ManagedIdentity](#microsoftmanagedidentity)
> - [Microsoft.Maps](#microsoftmaps)
> - [Microsoft.MarketplaceApps](#microsoftmarketplaceapps)
> - [Microsoft.Media](#microsoftmedia)
> - [Microsoft.Microservices4Spring](#microsoftmicroservices4spring)
> - [Microsoft.Migrate](#microsoftmigrate)
> - [Microsoft.NetApp](#microsoftnetapp)
> - [Microsoft.Network](#microsoftnetwork)
> - [Microsoft.NotificationHubs](#microsoftnotificationhubs)
> - [Microsoft.OperationalInsights](#microsoftoperationalinsights)
> - [Microsoft.OperationsManagement](#microsoftoperationsmanagement)
> - [Microsoft.Peering](#microsoftpeering)
> - [Microsoft.Portal](#microsoftportal)
> - [Microsoft.PortalSdk](#microsoftportalsdk)
> - [Microsoft.PowerBI](#microsoftpowerbi)
> - [Microsoft.PowerBIDedicated](#microsoftpowerbidedicated)
> - [Microsoft.ProjectOxford](#microsoftprojectoxford)
> - [Microsoft.RecoveryServices](#microsoftrecoveryservices)
> - [Microsoft.Relay](#microsoftrelay)
> - [Microsoft.ResourceGraph](#microsoftresourcegraph)
> - [Microsoft.SaaS](#microsoftsaas)
> - [Microsoft.Scheduler](#microsoftscheduler)
> - [Microsoft.Search](#microsoftsearch)
> - [Microsoft.Security](#microsoftsecurity)
> - [Microsoft.ServerManagement](#microsoftservermanagement)
> - [Microsoft.ServiceBus](#microsoftservicebus)
> - [Microsoft.ServiceFabric](#microsoftservicefabric)
> - [Microsoft.ServiceFabricMesh](#microsoftservicefabricmesh)
> - [Microsoft.SignalRService](#microsoftsignalrservice)
> - [Microsoft.Solutions](#microsoftsolutions)
> - [Microsoft.Sql](#microsoftsql)
> - [Microsoft.SqlVirtualMachine](#microsoftsqlvirtualmachine)
> - [Microsoft.SqlVM](#microsoftsqlvm)
> - [Microsoft.Storage](#microsoftstorage)
> - [Microsoft.StorageCache](#microsoftstoragecache)
> - [Microsoft.StorageSync](#microsoftstoragesync)
> - [Microsoft.StorageSyncDev](#microsoftstoragesyncdev)
> - [Microsoft.StorageSyncInt](#microsoftstoragesyncint)
> - [Microsoft.StorSimple](#microsoftstorsimple)
> - [Microsoft.StreamAnalytics](#microsoftstreamanalytics)
> - [Microsoft.StreamAnalyticsExplorer](#microsoftstreamanalyticsexplorer)
> - [Microsoft.TerraformOSS](#microsoftterraformoss)
> - [Microsoft.TimeSeriesInsights](#microsofttimeseriesinsights)
> - [Microsoft.Token](#microsofttoken)
> - [Microsoft.VirtualMachineImages](#microsoftvirtualmachineimages)
> - [microsoft.visualstudio](#microsoftvisualstudio)
> - [Microsoft.VMwareCloudSimple](#microsoftvmwarecloudsimple)
> - [Microsoft.Web](#microsoftweb)
> - [Microsoft.WindowsIoT](#microsoftwindowsiot)
> - [Microsoft.WindowsVirtualDesktop](#microsoftwindowsvirtualdesktop)

## <a name="microsoftaad"></a>Microsoft.AAD

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- | 
> | domainservices | No | 
> | domainservices/replicasets | No | 

## <a name="microsoftaadiam"></a>microsoft.aadiam

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | tenants | No |

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | actionRules | No | 

## <a name="microsoftanalysisservices"></a>Microsoft.AnalysisServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | servers | No |

## <a name="microsoftapimanagement"></a>Microsoft.ApiManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | service |  Sí | 

## <a name="microsoftappconfiguration"></a>Microsoft.AppConfiguration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | configurationstores | No | 

## <a name="microsoftappservice"></a>Microsoft.AppService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | apiapps | No | 
> | appidentities | No | 
> | gateways | No | 


## <a name="microsoftauthorization"></a>Microsoft.Authorization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | policyassignments | No |

## <a name="microsoftautomation"></a>Microsoft.Automation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | automationaccounts | No | 
> | automationaccounts/configurations | No | 
> | automationaccounts/runbooks | No | 



## <a name="microsoftazureactivedirectory"></a>Microsoft.AzureActiveDirectory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | b2cdirectories | No | 

## <a name="microsoftazuredata"></a>Microsoft.AzureData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | sqlserverregistrations | No |

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | registrations | No | 

## <a name="microsoftbatch"></a>Microsoft.Batch

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | batchaccounts | No |

## <a name="microsoftbatchai"></a>Microsoft.BatchAI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | clusters | No | 
> | fileservers | No | 
> | jobs | No | 
> | workspaces | No | 

## <a name="microsoftbingmaps"></a>Microsoft.BingMaps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | mapapis | No | 

## <a name="microsoftbiztalkservices"></a>Microsoft.BizTalkServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | biztalk | No | 

## <a name="microsoftblockchain"></a>Microsoft.Blockchain

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | blockchainmembers | No |
> | watchers | No | 

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | blueprintassignments | No | 

## <a name="microsoftbotservice"></a>Microsoft.BotService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | botservices | No | 

## <a name="microsoftcache"></a>Microsoft.Cache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | redis | No | 


## <a name="microsoftcdn"></a>Microsoft.Cdn

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | cdnwebapplicationfirewallpolicies | No |
> | profiles | No | 
> | profiles/endpoints | No | 

## <a name="microsoftcertificateregistration"></a>Microsoft.CertificateRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | certificateorders | No | 


## <a name="microsoftclassiccompute"></a>Microsoft.ClassicCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | domainnames | No |  
> | virtualmachines | No | 



## <a name="microsoftclassicnetwork"></a>Microsoft.ClassicNetwork

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | networksecuritygroups | No |
> | reservedips | No | 
> | virtualnetworks | No | 

## <a name="microsoftclassicstorage"></a>Microsoft.ClassicStorage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | storageaccounts | Sí |  


## <a name="microsoftcognitiveservices"></a>Microsoft.CognitiveServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftcompute"></a>Microsoft.Compute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | availabilitysets | No | 
> | diskencryptionsets | No | 
> | disks | No | 
> | galleries | No | 
> | galleries/images | No | 
> | galleries/images/versions | No | 
> | hostgroups | No | 
> | hostgroups/hosts | No | 
> | images | No | 
> | proximityplacementgroups | No | 
> | restorepointcollections | No | 
> | sharedvmimages | No | 
> | sharedvmimages/versions | No | 
> | snapshots | No | 
> | virtualmachines | Sí | 
> | virtualmachines/extensions | No | 
> | virtualmachinescalesets | No | 

## <a name="microsoftcontainer"></a>Microsoft.Container

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | containergroups | No | 

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | registries | No |  
> | registries/buildtasks | No |  
> | registries/replications | No | 
> | registries/tasks | No |  
> | registries/webhooks | No | 

## <a name="microsoftcontainerservice"></a>Microsoft.ContainerService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | containerservices | No | 
> | managedclusters | No | 
> | openshiftmanagedclusters | No | 

## <a name="microsoftcontentmoderator"></a>Microsoft.ContentModerator

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | applications | No | 

## <a name="microsoftcortanaanalytics"></a>Microsoft.CortanaAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | conectores | No |  

## <a name="microsoftcustomerinsights"></a>Microsoft.CustomerInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | hubs | No |  

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | resourceproviders | No | 

## <a name="microsoftdatabox"></a>Microsoft.DataBox

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | jobs | No | 

## <a name="microsoftdataboxedge"></a>Microsoft.DataBoxEdge

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | databoxedgedevices | No | 

## <a name="microsoftdatabricks"></a>Microsoft.Databricks

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | workspaces | No | 

## <a name="microsoftdatacatalog"></a>Microsoft.DataCatalog

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | catalogs | No | 
> | datacatalogs | No | 

## <a name="microsoftdataconnect"></a>Microsoft.DataConnect

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | connectionmanagers | No | 

## <a name="microsoftdataexchange"></a>Microsoft.DataExchange

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | packages | No | 
> | plans | No | 

## <a name="microsoftdatafactory"></a>Microsoft.DataFactory

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | datafactories | No | 
> | factories | No |  

## <a name="microsoftdatalake"></a>Microsoft.DataLake

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | datalakeaccounts | No | 

## <a name="microsoftdatalakeanalytics"></a>Microsoft.DataLakeAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatalakestore"></a>Microsoft.DataLakeStore

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | services | No | 
> | services/projects | No | 
> | slots | No | 

## <a name="microsoftdatashare"></a>Microsoft.DataShare

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | servers | No |  

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | servers | No |  

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | servergroups | No | 
> | servers | No |  
> | serversv2 | No | 

## <a name="microsoftdeploymentmanager"></a>Microsoft.DeploymentManager

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | artifactsources | No | 
> | rollouts | No |  
> | servicetopologies | No | 
> | servicetopologies/services | No |  
> | servicetopologies/services/serviceunits | No | 
> | steps | No | 

## <a name="microsoftdevices"></a>Microsoft.Devices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | elasticpools | No | 
> | elasticpools/iothubtenants | No | 
> | iothubs | Sí | 
> | provisioningservices | No | 

## <a name="microsoftdevspaces"></a>Microsoft.DevSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | controllers | No | 

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | labcenters | No | 
> | labs | No | 
> | labs/environments | No |  
> | labs/servicerunners | No | 
> | labs/virtualmachines | No |  
> | schedules | No |  

## <a name="microsoftdocumentdb"></a>Microsoft.DocumentDB

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | databaseaccounts | No | 

## <a name="microsoftdomainregistration"></a>Microsoft.DomainRegistration

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | domains | No | 

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | domains | No |  
> | topics | No | 

## <a name="microsofteventhub"></a>Microsoft.EventHub

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | clusters | No |  
> | espacios de nombres | No | 

## <a name="microsoftgenomics"></a>Microsoft.Genomics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsofthanaonazure"></a>Microsoft.HanaOnAzure

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | hanainstances | No | 
> | sapmonitors | No |  

## <a name="microsofthdinsight"></a>Microsoft.HDInsight

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | clusters | No | 

## <a name="microsofthealthcareapis"></a>Microsoft.HealthcareApis

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | services | No |  

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | machines | No | 

## <a name="microsofthybriddata"></a>Microsoft.HybridData

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | datamanagers |  No | 

## <a name="microsoftimportexport"></a>Microsoft.ImportExport

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | jobs |  No | 

## <a name="microsoftinsights"></a>microsoft.insights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 
> | actiongroups |  No | 
> | activitylogalerts | No | 
> | alertrules |  No | 
> | autoscalesettings |  No | 
> | components |  No |  
> | guestdiagnosticsettings | No | 
> | metricalerts | No | 
> | notificationgroups | No | 
> | notificationrules | No | 
> | scheduledqueryrules |  No | 
> | webtests |  No | 
> | workbooks |  No |  


## <a name="microsoftiotcentral"></a>Microsoft.IoTCentral

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | iotapps |  No |  

## <a name="microsoftiotspaces"></a>Microsoft.IoTSpaces

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | checknameavailability |  No |  
> | graph |  No | 

## <a name="microsoftkeyvault"></a>Microsoft.KeyVault

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | hsmpools | No | 
> | vaults |  No | 


## <a name="microsoftkusto"></a>Microsoft.Kusto

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | clusters |  No |  

## <a name="microsoftlabservices"></a>Microsoft.LabServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | labaccounts | No | 

## <a name="microsoftlocationbasedservices"></a>Microsoft.LocationBasedServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftlocationservices"></a>Microsoft.LocationServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftlogic"></a>Microsoft.Logic

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | hostingenvironments | No | 
> | integrationaccounts |  No |  
> | integrationserviceenvironments | No | 
> | isolatedenvironments | No | 
> | workflows |  No |  

## <a name="microsoftmachinelearning"></a>Microsoft.MachineLearning

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | commitmentplans |  No | 
> | webservices |  No | 
> | workspaces |  No | 

## <a name="microsoftmachinelearningcompute"></a>Microsoft.MachineLearningCompute

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | operationalizationclusters |  No | 

## <a name="microsoftmachinelearningexperimentation"></a>Microsoft.MachineLearningExperimentation

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 
> | accounts/workspaces | No | 
> | accounts/workspaces/projects | No | 
> | teamaccounts | No | 
> | teamaccounts/workspaces | No | 
> | teamaccounts/workspaces/projects | No | 

## <a name="microsoftmachinelearningmodelmanagement"></a>Microsoft.MachineLearningModelManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftmachinelearningoperationalization"></a>Microsoft.MachineLearningOperationalization

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | hostingaccounts | No | 

## <a name="microsoftmachinelearningservices"></a>Microsoft.MachineLearningServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | workspaces | No | 

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | userassignedidentities | No | 

## <a name="microsoftmaps"></a>Microsoft.Maps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts |  No |  

## <a name="microsoftmarketplaceapps"></a>Microsoft.MarketplaceApps

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | classicdevservices | No | 

## <a name="microsoftmedia"></a>Microsoft.Media

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | mediaservices |  No | 
> | mediaservices/liveevents |  No | 
> | mediaservices/streamingendpoints |  No | 

## <a name="microsoftmicroservices4spring"></a>Microsoft.Microservices4Spring

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | appclusters | No | 

## <a name="microsoftmigrate"></a>Microsoft.Migrate

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | assessmentprojects | No | 
> | migrateprojects | No | 
> | projects | No | 

## <a name="microsoftnetapp"></a>Microsoft.NetApp

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | netappaccounts | No | 
> | netappaccounts/capacitypools | No | 
> | netappaccounts/capacitypools/volumes | No | 
> | netappaccounts/capacitypools/volumes/mounttargets | No | 
> | netappaccounts/capacitypools/volumes/snapshots | No | 

## <a name="microsoftnetwork"></a>Microsoft.Network

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | applicationgateways | No | 
> | applicationgatewaywebapplicationfirewallpolicies | No | 
> | applicationsecuritygroups |  No |  
> | azurefirewalls |  No |  
> | bastionhosts | No | 
> | connections |  No | 
> | ddoscustompolicies |  No | 
> | ddosprotectionplans | No | 
> | dnszones |  No | 
> | expressroutecircuits | No | 
> | expressroutecrossconnections | No | 
> | expressroutegateways | No | 
> | expressrouteports | No | 
> | frontdoors | No | 
> | frontdoorwebapplicationfirewallpolicies | No | 
> | loadbalancers | Sí: SKU básico<br>No: SKU estándar | Sí: SKU básico<br> Sí: SKU estándar |
> | localnetworkgateways |  No | 
> | natgateways |  No | 
> | networkintentpolicies |  No | 
> | networkinterfaces | Sí | 
> | networkprofiles | No | 
> | networksecuritygroups | Sí | 
> | networkwatchers |  No |  
> | networkwatchers/connectionmonitors |  No | 
> | networkwatchers/lenses |  No | 
> | networkwatchers/pingmeshes |  No | 
> | p2svpngateways | No | 
> | privatednszones |  No |  
> | privatednszones/virtualnetworklinks |  No |  
> | privateendpoints | No | 
> | privatelinkservices | No | 
> | publicipaddresses | Sí: SKU básico<br>No: SKU estándar | Sí: SKU básico<br>No: SKU estándar |
> | publicipprefixes | No | 
> | routefilters | No | 
> | routetables |  No | 
> | serviceendpointpolicies |  No | 
> | trafficmanagerprofiles |  No | 
> | virtualhubs | No | 
> | virtualnetworkgateways |  No |  
> | virtualnetworks |  No | 
> | virtualnetworktaps | No | 
> | virtualwans | No | 
> | vpngateways (Virtual WAN) | No | 
> | vpnsites (Virtual WAN) | No | 
> | webapplicationfirewallpolicies |  No | 


## <a name="microsoftnotificationhubs"></a>Microsoft.NotificationHubs

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | espacios de nombres |  No | 
> | namespaces/notificationhubs |  No |  

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | workspaces |  No | 



## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | managementconfigurations |  No | 
> | views |  No | 

## <a name="microsoftpeering"></a>Microsoft.Peering

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | peerings | No | 

## <a name="microsoftportal"></a>Microsoft.Portal

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | dashboards | No | 

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | rootresources | No | 

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | workspacecollections |  No | 

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | capacities |  No | 

## <a name="microsoftprojectoxford"></a>Microsoft.ProjectOxford

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | accounts | No | 

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | vaults | No. [Deshabilite el almacén y vuelva a crearlo](https://docs.microsoft.com/azure/site-recovery/move-vaults-across-regions) para Site Recovery  | 


## <a name="microsoftrelay"></a>Microsoft.Relay

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | espacios de nombres |  No | 

## <a name="microsoftresourcegraph"></a>Microsoft.ResourceGraph

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | Consultas |  No |  

## <a name="microsoftsaas"></a>Microsoft.SaaS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | applications |  No | 

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | flows |  No |  
> | jobcollections |  No | 

## <a name="microsoftsearch"></a>Microsoft.Search

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | searchservices |  No | 


## <a name="microsoftsecurity"></a>Microsoft.Security

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | iotsecuritysolutions |  No | 
> | playbookconfigurations | No | 

## <a name="microsoftservermanagement"></a>Microsoft.ServerManagement

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | gateways | No | 
> | nodes | No | 

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | espacios de nombres |  No | 

## <a name="microsoftservicefabric"></a>Microsoft.ServiceFabric

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | applications | No | 
> | clusters |  No | 
> | clusters/applications | No | 
> | containergroups | No | 
> | containergroupsets | No | 
> | edgeclusters | No | 
> | networks | No | 
> | secretstores | No | 
> | volumes | No | 

## <a name="microsoftservicefabricmesh"></a>Microsoft.ServiceFabricMesh

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | applications |  No | 
> | containergroups | No | 
> | gateways |  No | 
> | networks |  No | 
> | secrets |  No | 
> | volumes |  No |  

## <a name="microsoftsignalrservice"></a>Microsoft.SignalRService

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | signalr |  No |  

## <a name="microsoftsolutions"></a>Microsoft.Solutions

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | appliancedefinitions | No | 
> | appliances | No | 
> | applicationdefinitions | No | 
> | applications | No | 
> | jitrequests | No | 

## <a name="microsoftsql"></a>Microsoft.Sql

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | instancepools | No | 
> | managedinstances | Sí | 
> | managedinstances/databases | Sí | 
> | servers | Sí | 
> | servers/databases | Sí | 
> | servers/elasticpools | Sí | 
> | virtualclusters | Sí | 

## <a name="microsoftsqlvirtualmachine"></a>Microsoft.SqlVirtualMachine

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | sqlvirtualmachinegroups |  No |  
> | sqlvirtualmachines |  No |  

## <a name="microsoftsqlvm"></a>Microsoft.SqlVM

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | dwvm | No | 

## <a name="microsoftstorage"></a>Microsoft.Storage

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | storageaccounts | Sí | 

## <a name="microsoftstoragecache"></a>Microsoft.StorageCache

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | caches | No | 

## <a name="microsoftstoragesync"></a>Microsoft.StorageSync

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | storagesyncservices |  No | 

## <a name="microsoftstoragesyncdev"></a>Microsoft.StorageSyncDev

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstoragesyncint"></a>Microsoft.StorageSyncInt

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | storagesyncservices | No | 

## <a name="microsoftstorsimple"></a>Microsoft.StorSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | managers | No | 

## <a name="microsoftstreamanalytics"></a>Microsoft.StreamAnalytics

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | streamingjobs |  No |  


## <a name="microsoftstreamanalyticsexplorer"></a>Microsoft.StreamAnalyticsExplorer

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | environments | No | 
> | environments/eventsources | No | 
> | instances | No | 
> | instances/environments | No | 
> | instances/environments/eventsources | No | 

## <a name="microsoftterraformoss"></a>Microsoft.TerraformOSS

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | providerregistrations | No | 
> | resources | No | 

## <a name="microsofttimeseriesinsights"></a>Microsoft.TimeSeriesInsights

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | environments |  No | 
> | environments/eventsources |  No |  
> | environments/referencedatasets |  No | 

## <a name="microsofttoken"></a>Microsoft.Token

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | almacenes | No | 

## <a name="microsoftvirtualmachineimages"></a>Microsoft.VirtualMachineImages

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | imagetemplates | No | 

## <a name="microsoftvisualstudio"></a>microsoft.visualstudio

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | account |  No | 
> | account/extension |  No | 
> | account/project |  No | 



## <a name="microsoftvmwarecloudsimple"></a>Microsoft.VMwareCloudSimple

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | dedicatedcloudnodes | No | 
> | dedicatedcloudservices | No | 
> | virtualmachines | No | 

## <a name="microsoftweb"></a>Microsoft.Web

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | certificates | No | 
> | connectiongateways |  No |  
> | connections |  No |  
> | customapis |  No | 
> | hostingenvironments | No | 
> | serverfarms |  No |  
> | sites |  No | 
> | sites/premieraddons |  No |  
> | sites/slots |  No |  


## <a name="microsoftwindowsiot"></a>Microsoft.WindowsIoT

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | deviceservices | No | 

## <a name="microsoftwindowsvirtualdesktop"></a>Microsoft.WindowsVirtualDesktop

> [!div class="mx-tableFixed"]
> | Tipo de recurso | Movimiento de región | 
> | ------------- | ----------- |
> | applicationgroups | No | 
> | hostpools | No | 
> | workspaces | No | 

## <a name="third-party-services"></a>Servicios de terceros

Actualmente los servicios de terceros no son compatibles con la operación de traslado.
