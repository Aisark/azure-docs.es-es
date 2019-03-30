---
title: Actualización de un clúster de Azure Service Fabric | Microsoft Docs
description: Actualice el código de Service Fabric o la configuración que ejecuta un clúster de Service Fabric, incluida la configuración del modo de actualización del clúster, la incorporación de puertos de aplicación, las revisiones del sistema operativo, etc. ¿Qué se puede esperar cuando se realizan actualizaciones?
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: 15190ace-31ed-491f-a54b-b5ff61e718db
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/12/2018
ms.author: aljo
ms.openlocfilehash: 234bff5049babf0c4b1d036b40201720b2736228
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661639"
---
# <a name="upgrade-the-service-fabric-version-of-a-cluster"></a>Actualización de la versión de Service Fabric de un clúster

Para cualquier sistema moderno, diseñar la capacidad de actualización es clave para lograr el éxito a largo plazo de su producto. Un clúster de Azure Service Fabric es un recurso de su propiedad que está parcialmente administrado por Microsoft. En este artículo se describe cómo actualizar la versión de Service Fabric que se ejecuta en el clúster de Azure.

Puede configurar el clúster para recibir las actualizaciones automáticas cuando Microsoft las publica o puede seleccionar la versión compatible de Service Fabric que desea que tenga el clúster.

Para ello, establezca el modo de actualización en la configuración del clúster en el portal o mediante Resource Manager en el momento de creación, o posteriormente en un clúster activo 

> [!NOTE]
> Asegúrese de que tiene siempre una versión admitida de Service Fabric en ejecución en el clúster. Cuando anunciamos el lanzamiento de una nueva versión de Service Fabric, se marca la versión anterior para que finalice el soporte después de un mínimo de 60 días a partir de esa fecha. Las nuevas versiones se anuncian [en el blog del equipo de Service Fabric](https://blogs.msdn.microsoft.com/azureservicefabric/). La nueva versión estará disponible para su elección a partir de ese momento. 
> 
> 

14 días antes de la expiración de la versión que se está ejecutando en el clúster, se genera un evento de mantenimiento que coloca el clúster en un estado de mantenimiento de advertencia. El clúster permanece en un estado de advertencia hasta que actualiza a una versión compatible de Service Fabric.

## <a name="set-the-upgrade-mode-in-the-azure-portal"></a>Establecimiento del modo de actualización en Azure Portal
Puede establecer el clúster en Automático o en Manual al crearlo.

![Create_Manualmode][Create_Manualmode]

Puede establecer el clúster en Automático o en Manual en un clúster activo, mediante la experiencia de administración. 

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-portal"></a>Actualización a una nueva versión en un clúster que está establecido en modo Manual a través del portal.
Para actualizar a una nueva versión, solo tiene que seleccionar la versión disponible en la lista desplegable y guardar. La actualización de Service Fabric se pondrá en marcha automáticamente. Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el estado de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización.

Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte. Desplácese hacia abajo en este documento para más información sobre cómo configurar las directivas de mantenimiento personalizado. 

Una vez que haya solucionado los problemas que provocaron la reversión, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

![Manage_Automaticmode][Manage_Automaticmode]

## <a name="set-the-upgrade-mode-using-a-resource-manager-template"></a>Establecimiento del modo de actualización con una plantilla de Resource Manager
Agregue la configuración de "upgradeMode" a la definición de recursos de Microsoft.ServiceFabric/clusters y establezca "clusterCodeVersion" en una de las versiones compatibles de Service Fabric como se muestra a continuación y, después, implemente la plantilla. Los valores válidos para "upgradeMode" son "Manual" o "Automático"

![ARMUpgradeMode][ARMUpgradeMode]

### <a name="upgrading-to-a-new-version-on-a-cluster-that-is-set-to-manual-mode-via-a-resource-manager-template"></a>Actualización a una nueva versión en un clúster que está establecido en modo Manual a través de una plantilla de Resource Manager.
Si el clúster está en modo Manual, para actualizar a una nueva versión, cambie "clusterCodeVersion" a una versión compatible e impleméntela. La implementación de la plantilla hace que se ponga en marcha automáticamente la actualización de Service Fabric. Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el estado de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización.

Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte.  

Una vez que haya solucionado los problemas que provocaron la reversión, debe iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

## <a name="set-custom-health-polices-for-upgrades"></a>Establecimiento de las directivas de mantenimiento personalizado para las actualizaciones
Puede especificar directivas de mantenimiento personalizado para la actualización de Service Fabric. Si ha configurado el clúster para que las actualizaciones de Service Fabric se realicen automáticamente, estas directivas se aplicarán en la [fase 1 de las actualizaciones automáticas de Service Fabric.](service-fabric-cluster-upgrade.md#fabric-upgrade-behavior-during-automatic-upgrades)
Si ha configurado el clúster para que las actualizaciones de Service Fabric se realicen de forma manual, estas directivas se aplicarán cada vez que seleccione una nueva versión, lo cual hará que el sistema ponga en marcha la actualización de Service Fabric en el clúster. Si no reemplaza las directivas, se usarán los valores predeterminados.

Puede especificar las directivas de mantenimiento personalizado o revisar la configuración actual en la hoja "actualización de Service Fabric", seleccionando la configuración avanzada de la actualización. Revise la siguiente imagen sobre cómo hacerlo. 

![Administración de las directivas de mantenimiento personalizado][HealthPolices]

## <a name="list-all-available-versions-for-all-environments-for-a-given-subscription"></a>Obtención de la lista de todas las versiones disponibles para todos los entornos de una suscripción determinada
Ejecute el siguiente comando, debería obtener una salida similar a esta.

"supportExpiryUtc" le indica cuándo expira o ha expirado una determinada versión. La última versión no tiene una fecha válida: tiene un valor de "9999-12-31T23:59:59.9999999", lo que significa simplemente que la fecha de expiración no se ha establecido aún.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/clusterVersions?api-version=2016-09-01

Example: https://management.azure.com/subscriptions/1857f442-3bce-4b96-ad95-627f76437a67/providers/Microsoft.ServiceFabric/locations/eastus/clusterVersions?api-version=2016-09-01

Output:
{
                  "value": [
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/5.0.1427.9490",
                      "name": "5.0.1427.9490",
                      "type": "Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.0.1427.9490",
                        "supportExpiryUtc": "2016-11-26T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.0.1427.9490",
                      "name": "5.1.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "5.1.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Windows"
                      }
                    },
                    {
                      "id": "subscriptions/35349203-a0b3-405e-8a23-9f1450984307/providers/Microsoft.ServiceFabric/environments/Windows/clusterVersions/4.4.1427.9490",
                      "name": "4.4.1427.9490",
                      "type": " Microsoft.ServiceFabric/environments/clusterVersions",
                      "properties": {
                        "codeVersion": "4.4.1427.9490",
                        "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
                        "environment": "Linux"
                      }
                    }
                  ]
                }
```

## <a name="next-steps"></a>Pasos siguientes
* Aprender a personalizar la [configuración de Service Fabric para el clúster](service-fabric-cluster-fabric-settings.md)
* Aprenda cómo [escalar o reducir horizontalmente el clúster](service-fabric-cluster-scale-up-down.md)
* Obtenga información sobre [actualizaciones de aplicaciones](service-fabric-application-upgrade.md)

<!--Image references-->
[CertificateUpgrade]: ./media/service-fabric-cluster-upgrade/CertificateUpgrade2.png
[AddingProbes]: ./media/service-fabric-cluster-upgrade/addingProbes2.PNG
[AddingLBRules]: ./media/service-fabric-cluster-upgrade/addingLBRules.png
[HealthPolices]: ./media/service-fabric-cluster-upgrade/Manage_AutomodeWadvSettings.PNG
[ARMUpgradeMode]: ./media/service-fabric-cluster-upgrade/ARMUpgradeMode.PNG
[Create_Manualmode]: ./media/service-fabric-cluster-upgrade/Create_Manualmode.PNG
[Manage_Automaticmode]: ./media/service-fabric-cluster-upgrade/Manage_Automaticmode.PNG
