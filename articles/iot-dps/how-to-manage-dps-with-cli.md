---
title: Administración de IoT Hub Device Provisioning Service mediante la CLI de Azure y la extensión de IoT
description: Aprenda a usar la CLI de Azure y la extensión de IoT para administrar IoT Hub Device Provisioning Service (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0ba92279632a7283ea6ede423e808e3c7be82cff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975166"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Uso de la CLI de Azure y de la extensión de IoT para administrar IoT Hub Device Provisioning Service

La [CLI de Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) es una herramienta de la línea de comandos multiplataforma y de código abierto para la administración de recursos de Azure, como IoT Edge. Está disponible en Windows, Linux y MacOS. La CLI de Azure permite administrar recursos de Azure IoT Hub, instancias de servicio de aprovisionamiento de dispositivos y centros vinculados listos para usar.

La extensión de IoT enriquece la CLI de Azure con características como administración de dispositivos y funcionalidad completa de IoT Edge.

En este tutorial, primero realizará los pasos para configurar la CLI de Azure y la extensión de IoT. A continuación, aprenderá a ejecutar comandos de la CLI para realizar operaciones básicas del servicio de aprovisionamiento de dispositivos. 

## <a name="installation"></a>Instalación 

### <a name="step-1---install-python"></a>Paso 1: Instalación de Python

Se requiere [Python 2.7x o Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-the-azure-cli"></a>Paso 2: Instalación de la CLI de Azure

Siga las [instrucciones de instalación](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para instalar la CLI de Azure en su entorno. La versión mínima de la CLI de Azure es la 2.0.24. Use `az –version` para asegurarse. Esta versión admite comandos az extension e introduce la plataforma de comandos de Knack. Una manera sencilla de instalarla en Windows es descargar e instalar [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Paso 3: Instalación de la extensión de IoT

En el [archivo Léame de la extensión de IoT](https://github.com/Azure/azure-iot-cli-extension) se describen varias maneras de instalarla. La manera más sencilla es ejecutar `az extension add --name azure-cli-iot-ext`. Después de la instalación, puede usar `az extension list` para validar las extensiones instaladas actualmente o `az extension show --name azure-cli-iot-ext` para ver los detalles acerca de la extensión de IoT. Para quitar la extensión, puede usar `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Operaciones básicas del servicio de aprovisionamiento de dispositivos
En el ejemplo se muestra cómo iniciar sesión en una cuenta de Azure, crear un grupo de recursos de Azure (un contenedor que alberga los recurso relacionados de una solución de Azure), crear una instancia de IoT Hub, crear un servicio de aprovisionamiento de dispositivos, enumerar los servicios de aprovisionamiento de dispositivos existentes y crear un centro de IoT vinculado con comandos de la CLI. 

Antes de comenzar, realice los pasos de instalación descritos anteriormente. Si no tiene una cuenta de Azure aún, también puede [crear una cuenta gratuita](https://azure.microsoft.com/free/?v=17.39a). 


### <a name="1-log-in-to-the-azure-account"></a>1. Inicie sesión en la cuenta de Azure
  
    az login

![inicio de sesión][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Cree un grupo de recursos IoTHubBlogDemo en eastus

    az group create -l eastus -n IoTHubBlogDemo

![Creación de un grupo de recursos][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Creación de dos servicios de aprovisionamiento de dispositivos

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Creación de un servicio de aprovisionamiento de dispositivos][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Enumeración de todos los servicios de aprovisionamiento de dispositivos existentes en este grupo de recursos

    az iot dps list --resource-group IoTHubBlogDemo

![Lista de los servicios de aprovisionamiento de dispositivos][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Cree una instancia de IoT Hub blogDemoHub en el grupo de recursos recién creado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Crear IoT Hub][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Vinculación de una instancia de IoT Hub existente con un servicio de aprovisionamiento de dispositivos

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Vincular centro][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Inscribir el dispositivo
> * Iniciar el dispositivo
> * Comprobar que el dispositivo está registrado

Pase al siguiente tutorial para aprender a aprovisionar varios dispositivos en centros con equilibrio de carga. 

> [!div class="nextstepaction"]
> [Aprovisionamiento de dispositivos en instancias de IoT Hub con equilibrio de carga](./tutorial-provision-multiple-hubs.md)
