---
title: Variables de entorno de Azure Service Fabric | Microsoft Docs
description: Documentación de referencia de las variables de entorno de Service Fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: mikhegn
ms.openlocfilehash: 1c8400898dba59f312ba9d994ee711a5e241973a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60946729"
---
# <a name="service-fabric-environment-variables"></a>Variables de entorno de Service Fabric

Service Fabric tiene variables de entorno integradas que se establecen para cada instancia del servicio. A continuación se muestra la lista completa de variables de entorno:

| Variable de entorno                         | DESCRIPCIÓN                                                            | Ejemplo                                                              |
|----------------------------------------------|------------------------------------------------------------------------|----------------------------------------------------------------------|
| Fabric_ApplicationName                       | Nombre de identificador URI de Service Fabric de la aplicación                                 | fabric:/MyApplication                                                |
| Fabric_CodePackageName                       | Nombre del paquete de código al que pertenece el proceso              | Código                                                                 |
| Fabric_Endpoint\_IPOrFQDN\_*ServiceEndpointName*     | La dirección IP o FQDN del punto de conexión                                 | 10.0.0.1                                                     |
| Fabric\_Endpoint\_*ServiceEndpointName*              | Número de puerto del punto de conexión                                  | 8234                                                                 |
| Fabric_Folder_App_Log                        | Carpeta de registro                                                             | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\log      |
| Fabric_Folder_App_Temp                       | Carpeta temporal                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\temp     |
| Fabric_Folder_App_Work                       | Carpeta de trabajo                                                            | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12\\\\work     |
| Fabric_Folder_Application                    | Carpeta de inicio de las aplicaciones                                           | C:\\\\Data\\\\_App\\\\_Node_0\\\\MyApplicationType_App12             |
| Fabric_IsContainerHost                       | Un valor booleano que especifica si el proceso es un contenedor                   | false                                                                |
| Fabric_NodeId                                | El identificador de nodo del nodo que ejecuta el proceso                            | bf865279ba277deb864a976fbf4c200e                                     |
| Fabric_NodeIPOrFQDN                          | Dirección IP o FQDN del nodo, como se especifica en el archivo de manifiesto del clúster. | localhost o 10.0.0.1                                                |
| Fabric_NodeName                              | El nombre de nodo del nodo que ejecuta el proceso                          | _Node_0                                                              |
| Fabric_ServiceName                           | Nombre de la URI de tejido del servicio, si el servicio se hospeda en modo ExclusiveProcess. Este valor de la variable solo está disponible si ha creado el servicio con ServicePackageActivationMode ExclusiveProcess.  | fabric:/MyApplication/MyService                                               |
| Fabric_ServicePackageActivationId            | El identificador de activación del paquete de servicio                                         | Un identificador GUID                                                               |
| Fabric_ServicePackageName                    | Nombre del paquete del servicio del que forma parte el proceso                     | Web1Pkg                                                              |

Variables de entorno internas utilizadas por el runtime de Service Fabric:

- Fabric_ApplicationHostId
- Fabric_ApplicationHostType
- Fabric_ApplicationId
- Fabric_CodePackageInstanceId
- Fabric_CodePackageInstanceSeqNum
- Fabric_RuntimeConnectionAddress
- Fabric_ServicePackageActivationGuid
- Fabric_ServicePackageInstanceId
- Fabric_ServicePackageInstanceSeqNum
- Fabric_ServicePackageVersionInstance
- FabricActivatorAddress
- FabricPackageFileName
- HostedServiceName
