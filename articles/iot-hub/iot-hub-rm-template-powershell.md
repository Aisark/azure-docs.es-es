---
title: Creación de un centro de IoT de Azure mediante una plantilla (PowerShell) | Microsoft Docs
description: Describe cómo usar una plantilla de Azure Resource Manager para crear un centro de IoT con PowerShell.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2017
ms.openlocfilehash: fcc9af9e614b0a1b7977ba18f3147fddab8b7b7d
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/05/2019
ms.locfileid: "59045062"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Creación de un centro de IoT con una plantilla de Azure Resource Manager (PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Puede usar Azure Resource Manager para crear y administrar los centros de IoT de Azure mediante programación. En este tutorial se muestra cómo usar una plantilla de Azure Resource Manager para crear un IoT Hub con PowerShell.

> [!NOTE]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Azure Resource Manager y clásico](../azure-resource-manager/resource-manager-deployment-model.md). Este artículo trata sobre el uso del modelo de implementación de Azure Resource Manager.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para completar este tutorial, necesitará lo siguiente:

* Una cuenta de Azure activa. <br/>Si no tiene ninguna, puede crear una [cuenta gratuita][lnk-free-trial] en tan solo unos minutos.
* [Azure PowerShell 1.0][lnk-powershell-install] o posterior.

> [!TIP]
> El artículo [Uso de Azure PowerShell con Azure Resource Manager][lnk-powershell-arm] contiene más información sobre cómo utilizar PowerShell y plantillas de Azure Resource Manager para crear recursos de Azure.

## <a name="connect-to-your-azure-subscription"></a>Conexión a su suscripción de Azure

En un símbolo del sistema de PowerShell, escriba el siguiente comando para iniciar sesión en su suscripción de Azure:

```powershell
Connect-AzAccount
```

Si tiene varias suscripciones de Azure, el inicio de sesión en Azure le concede acceso a todas las suscripciones de Azure asociadas a sus credenciales. Use el siguiente comando para mostrar las suscripciones de Azure que están disponibles para su uso:

```powershell
Get-AzSubscription
```

Use el siguiente comando para seleccionar la suscripción que desea usar para ejecutar los comandos que crearán la instancia de IoT Hub. Puede usar el nombre de la suscripción o el identificador de la salida del comando anterior:

```powershell
Select-AzSubscription `
    -SubscriptionName "{your subscription name}"
```

Puede usar los comandos siguientes para conocer donde puede implementar un centro de IoT y las versiones de API admitidas actualmente:

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Cree un grupo de recursos para que contenga su centro de IoT mediante el siguiente comando en una de las ubicaciones compatibles para IoT Hub. En este ejemplo se crea un grupo de recursos denominado **MyIoTRG1**:

```powershell
New-AzResourceGroup -Name MyIoTRG1 -Location "East US"
```

## <a name="submit-a-template-to-create-an-iot-hub"></a>Enviar una plantilla para crear un centro de IoT

Use una plantilla de JSON para crear un nuevo centro de IoT en el grupo de recursos. También puede usar una plantilla de Azure Resource Manager para realizar cambios en un IoT Hub existente.

1. Use un editor de texto para crear una plantilla de Azure Resource Manager llamada **template.json** , con la siguiente definición de recursos, para crear un nuevo IoT Hub estándar. Este ejemplo agrega el centro de IoT en la región **este de EE. UU.**, crea dos grupos de consumidores (**cg1** and **cg2**) en el punto de conexión compatible con centros de eventos y usa la versión de API **2016-02-03**. En esta plantilla se espera también que pase el nombre del centro de IoT como un parámetro denominado **hubName**. Para ver una lista actualizada de las ubicaciones admitidas en IoT Hub, consulte [Estado de Azure][lnk-status].

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg1')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      },
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs/eventhubEndpoints/ConsumerGroups",
        "name": "[concat(parameters('hubName'), '/events/cg2')]",
        "dependsOn": [
          "[concat('Microsoft.Devices/Iothubs/', parameters('hubName'))]"
        ]
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Guarde el archivo de plantilla de Azure Resource Manager. En este ejemplo, se da por supuesto que lo guarda en una carpeta llamada **c:\templates**.

3. Ejecute el comando siguiente para implementar el nuevo centro de IoT, pasando el nombre de su centro de IoT como un parámetro. En este ejemplo, el nombre de la instancia de IoT Hub es `abcmyiothub`. El nombre de su instancia de IoT Hub debe único globalmente:

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName abcmyiothub
    ```
   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]

4. El resultado muestra las claves para el centro de IoT que ha creado.

5. Para comprobar que la aplicación ha agregado el nuevo centro de IoT, visite [Azure Portal][lnk-azure-portal] y vea la lista de recursos. También puede usar el **Get AzResource** cmdlet de PowerShell.

> [!NOTE]
> Esta aplicación de ejemplo agrega un centro de IoT estándar S1 por el que se le cobrará. Puede eliminar el centro de IoT a través de la [portal de Azure] [ lnk-azure-portal] o mediante el **Remove-AzResource** cmdlet de PowerShell cuando haya terminado.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha implementado un IoT Hub mediante una plantilla de Azure Resource Manager con PowerShell, quizá desee seguir explorando:

* Consulte las funcionalidades de la [API de REST del proveedor de recursos de IoT Hub][lnk-rest-api].
* Para más información sobre las funcionalidades de Azure Resource Manager, consulte [Información general de Azure Resource Manager][lnk-azure-rm-overview].
* Para conocer la sintaxis y las propiedades JSON que se usan en las plantillas, consulte [Microsoft.Devices resource types](/azure/templates/microsoft.devices/iothub-allversions) (Tipos de recursos de Microsoft.Devices).

Para obtener más información sobre cómo desarrollar para IoT Hub, consulte los siguientes artículos:

* [Introducción a C SDK][lnk-c-sdk]
* [SDK de IoT de Azure][lnk-sdks]

Para explorar aún más las funcionalidades de IoT Hub, consulte:

* [Implementación de Azure IoT Edge en un dispositivo simulado en Linux: versión preliminar][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
