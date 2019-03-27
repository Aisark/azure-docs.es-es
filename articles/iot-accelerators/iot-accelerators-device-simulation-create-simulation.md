---
title: 'Creación de una simulación de IoT: Azure | Microsoft Docs'
description: En este tutorial, usará Simulación de dispositivo para crear y ejecutar una simulación nueva.
author: troyhopwood
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/08/2019
ms.author: troyhop
ms.openlocfilehash: 09a6920e0d3a50da1bdacbf2bc7a80396c885897
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180711"
---
# <a name="tutorial-create-and-run-an-iot-device-simulation"></a>Tutorial: Creación y ejecución de una simulación de dispositivo IoT

En este tutorial, usará Simulación de dispositivo para crear y ejecutar una nueva simulación de IoT mediante uno o varios dispositivos simulados.

En este tutorial, hizo lo siguiente:

>[!div class="checklist"]
> * Ver todas las simulaciones activas y las históricas
> * Crear y ejecutar una nueva simulación
> * Ver las métricas de una simulación
> * Detener una simulación

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Para seguir este tutorial, necesitará una instancia implementada de Simulación de dispositivo en la suscripción de Azure.

Si aún no la ha implementado, debe completar primero la guía de inicio rápido [Implementación y ejecución de simulación de dispositivo de IoT en Azure](quickstart-device-simulation-deploy.md).

## <a name="open-device-simulation"></a>Abrir Simulación de dispositivo

Para ejecutar Simulación de dispositivo en el explorador, primero vaya a [Aceleradores de soluciones de Microsoft Azure IoT](https://www.azureiotsolutions.com). 

Se le pedirá que inicie sesión con sus credenciales de suscripción de Azure.

A continuación, haga clic en **Iniciar** en el icono de Simulación de dispositivo que ha implementado en la [guía de inicio rápido](quickstart-device-simulation-deploy.md).

## <a name="view-simulations"></a>Ver simulaciones

Seleccione **Simulaciones** en la barra de menús. La página **Simulaciones** muestra información sobre todas las simulaciones disponibles. En esta página puede ver las simulaciones en ejecución actualmente y aquellas que se ejecutaron anteriormente. Para volver a ejecutar una simulación anterior, haga clic en el icono de simulación para abrir los detalles de esta:

![Simulaciones](media/iot-accelerators-device-simulation-create-simulation/dashboard.png)

## <a name="create-a-simulation"></a>Creación de una simulación

Para crear una simulación, haga clic en **+ Nueva simulación** en la esquina superior derecha.

Una simulación consta de uno o varios modelos de dispositivo. El modelo de dispositivo define el comportamiento, la telemetría y el formato del mensaje del dispositivo que se va a simular.

Para agregar un modelo de dispositivo, haga clic en **+ Agregar un tipo de dispositivo** y seleccione el modelo de dispositivo en la lista. La simulación puede tener más de un modelo de dispositivo. Cada modelo de dispositivo puede tener un recuento de dispositivos y una frecuencia de mensajes diferente.

Una vez completo el nuevo formulario de simulación, haga clic en **Iniciar simulación** para iniciarla.

Según el número de dispositivos simulados, puede que la simulación tarde varios minutos en configurarse e iniciarse:

![Nueva simulación](media/iot-accelerators-device-simulation-create-simulation/newsimulation.png)

## <a name="stop-a-simulation"></a>Detener una simulación

Al hacer clic en **Iniciar simulación**, verá la página de detalles de la simulación. Esta página de detalles muestra las estadísticas de la simulación activa y las métricas de IoT Hub. Para ir a esta página de detalles también puede hacer clic en el icono de la simulación en la página **Simulaciones**.

Para detener una simulación, haga clic en **Detener simulación** en la barra de acciones en la parte superior derecha.

![Detener simulación](media/iot-accelerators-device-simulation-create-simulation/simulationdetails.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, aprendió a crear, ejecutar y detener una simulación. También ha aprendido a ver los detalles de la simulación. Para más información sobre cómo ejecutar simulaciones, continúe con el tutorial siguiente:

> [!div class="nextstepaction"]
> [Creación de un dispositivo simulado personalizado](iot-accelerators-device-simulation-create-custom-device.md)
