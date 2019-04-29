---
title: 'Registro de un dispositivo nuevo desde Azure Portal: Azure IoT Edge | Microsoft Docs'
description: Uso de Azure Portal para registrar un dispositivo IoT Edge nuevo y recuperar la cadena de conexión
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126331"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registro de un nuevo dispositivo Azure IoT Edge desde Azure Portal

Para poder usar los dispositivos IoT con Azure IoT Edge, debe registrarlos con el centro de IoT. Una vez que registra un dispositivo, recibe una cadena de conexión que se puede usar para configurar el dispositivo para cargas de trabajo de Edge.

En este artículo se muestra cómo registrar un nuevo dispositivo IoT Edge mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) en la suscripción de Azure.

## <a name="create-a-device"></a>Creación de un dispositivo

En Azure Portal, los dispositivos IoT Edge se crean y administran por separado de los dispositivos que se conectan al centro de IoT pero que no están habilitados para Edge.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.
2. Seleccione **IoT Edge** en el menú.
3. Seleccione **Agregar un dispositivo IoT Edge**.
4. Proporcione un identificador de dispositivo descriptivo. Use la configuración predeterminada para la generación automática de claves de autenticación y para conectar el dispositivo nuevo a su centro.
5. Seleccione **Guardar**.

## <a name="view-all-devices"></a>Ver todos los dispositivos

Todos los dispositivos habilitados para Edge que se conectan al centro de IoT se enumeran en la página **IoT Edge**.

## <a name="retrieve-the-connection-string"></a>Recuperación de la cadena de conexión

Cuando esté listo para configurar el dispositivo, necesitará la cadena de conexión que vincula el dispositivo físico con su identidad en el centro de IoT.

1. En la página **IoT Edge** del portal, haga clic en el identificador de dispositivo en la lista de dispositivos de Edge.
2. Copie el valor de **Cadena de conexión (clave principal)** o **Cadena de conexión (clave secundaria)**.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [implementar módulos en un dispositivo con Azure Portal](how-to-deploy-modules-portal.md).
