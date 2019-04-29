---
title: Visualización de datos del sensor en tiempo real desde Azure IoT Hub (Web Apps) | Microsoft Docs
description: Use la característica Web Apps de Microsoft Azure App Service para visualizar datos de temperatura y humedad que se recopilan desde el sensor y se envían a su IoT Hub.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: 070f37a969411cfc4caf5f2d2b089ccfae759ca2
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125719"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-by-using-the-web-apps-feature-of-azure-app-service"></a>Visualizar datos del sensor en tiempo real desde Azure IoT Hub mediante la característica Web Apps de Azure App Service

![Diagrama integral](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Conocimientos que adquirirá

En este tutorial, obtendrá más información sobre cómo visualizar los datos del sensor en tiempo real que recibe su IoT Hub mediante la ejecución de una aplicación web hospedada en una aplicación web. Si quiere intentar visualizar los datos en su IoT Hub con Power BI, vea [Visualización de datos del sensor en tiempo real desde Azure IoT Hub mediante Power BI](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Qué debe hacer

* Crear una aplicación web en Azure Portal.
* Prepare el IoT Hub para el acceso a datos mediante la adición de un grupo de consumidores.
* Configurar la aplicación web para leer datos del sensor desde su IoT Hub.
* Cargar una aplicación web para que se hospede en la aplicación web.
* Abrir la aplicación web para ver datos de temperatura y humedad en tiempo real desde su IoT Hub.

## <a name="what-you-need"></a>Lo que necesita

* Completar la [simulador en línea de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial o uno de los tutoriales de dispositivo; por ejemplo, [Raspberry Pi con node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Estos cubren los siguientes requisitos:

  * Una suscripción de Azure activa.
  * Un IoT Hub en su suscripción.
  * Una aplicación cliente que envíe mensajes a su IoT Hub.

* [Descargar Git](https://www.git-scm.com/downloads)

## <a name="create-a-web-app"></a>Creación de una aplicación web

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Crear un recurso** > **Web y móvil** > **Aplicación web**.

2. Escriba un nombre de trabajo único, compruebe la suscripción, especifique un grupo de recursos y una ubicación, seleccione **Anclar al panel** y luego haga clic en **Crear**.

   Se recomienda seleccionar la misma ubicación donde se encuentra el grupo de recursos. 
   
   ![Creación de una aplicación web](./media/iot-hub-live-data-visualization-in-web-apps/2_create-web-app-azure.png)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="configure-the-web-app-to-read-data-from-your-iot-hub"></a>Configuración de la aplicación web para leer datos del IoT Hub

1. Abra la aplicación web que acaba de aprovisionar.

2. Haga clic en **Configuración de la aplicación** y luego, en **Configuración de la aplicación**, agregue los siguientes pares clave-valor:

   | Clave                                   | Valor                                                        |
   |---------------------------------------|--------------------------------------------------------------|
   | Azure.IoT.IoTHub.ConnectionString     | Obtenido de la CLI de Azure                                      |
   | Azure.IoT.IoTHub.ConsumerGroup        | El nombre del grupo de consumidores que agrega a su IoT Hub.  |
   | WEBSITE_NODE_DEFAULT_VERSION          | 8.9.4                                                        |

   ![Agregar la configuración a una aplicación web con pares clave-valor](./media/iot-hub-live-data-visualization-in-web-apps/3_web-app-settings-key-value-azure.png)

3. Haga clic en **Configuración de la aplicación**, en **Configuración General**, active o desactive la opción **Web sockets** y, después, haga clic en **Guardar**.

   ![Activar o desactivar la opción Web sockets](./media/iot-hub-live-data-visualization-in-web-apps/4_toggle_web_sockets.png)

## <a name="upload-a-web-application-to-be-hosted-by-the-web-app"></a>Carga de una aplicación web para que se hospede en la aplicación web

Hemos facilitado el acceso a una aplicación web en GitHub que muestra datos del sensor en tiempo real desde su IoT Hub. Todo lo que tiene que hacer es configurar la aplicación web para que funcione con un repositorio de Git, descargarla de GitHub y cargarla en Azure para que la hospede la aplicación web.

1. En la aplicación web, haga clic en **Opciones de implementación** > **Elegir origen** > **Repositorio de Git local** y después pulse **Aceptar**.

   ![Configuración de la implementación de la aplicación web para usar el repositorio de Git local](./media/iot-hub-live-data-visualization-in-web-apps/5_configure-web-app-deployment-local-git-repository-azure.png)

2. Haga clic en **Credenciales de implementación**, cree un nombre de usuario y una contraseña que se usarán para conectarse al repositorio de Git de Azure y haga clic en **Guardar**.

3. Haga clic en **Información general** y anote el valor de la **URL de clonación de Git**.

   ![Obtener la dirección URL de clonación de Git de la aplicación web](./media/iot-hub-live-data-visualization-in-web-apps/6_web-app-git-clone-url-azure.png)

4. Abra una ventana de comando o de terminal en el equipo local.

5. Descargue la aplicación web de GitHub y cárguela en Azure para que se hospede en la aplicación web. Para ello, ejecute los siguientes comandos:

   ```bash
   git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
   cd web-apps-node-iot-hub-data-visualization
   git remote add webapp <Git clone URL>
   git push webapp master:master
   ```

   > [!NOTE]
   > La \<dirección URL de clonación de GIT\> es la dirección URL del repositorio de Git que se encuentra en la página **Overview** (Información general) de la aplicación web.

## <a name="open-the-web-app-to-see-real-time-temperature-and-humidity-data-from-your-iot-hub"></a>Abra la aplicación web para ver los datos de temperatura y humedad en tiempo real desde su IoT Hub.

En la página **Overview** (Información general) de la aplicación web, haga clic en la dirección URL para abrir la aplicación web.

![Obtención de la dirección URL de la aplicación web](./media/iot-hub-live-data-visualization-in-web-apps/7_web-app-url-azure.png)

Verá los datos de temperatura y humedad en tiempo real desde su IoT Hub.

![Página de aplicación web que muestra la temperatura y humedad en tiempo real](./media/iot-hub-live-data-visualization-in-web-apps/8_web-app-page-show-real-time-temperature-humidity-azure.png)

> [!NOTE]
> Asegúrese de que la aplicación de ejemplo se ejecuta en el dispositivo. Si no es así, obtendrá un gráfico en blanco; puede consultar los tutoriales de [configuración del dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md).

## <a name="next-steps"></a>Pasos siguientes

Ha usado correctamente una aplicación web para visualizar datos del sensor en tiempo real desde su IoT Hub.

Para obtener una forma alternativa de visualizar los datos desde Azure IoT Hub, vea [Visualización de datos del sensor en tiempo real desde Azure IoT Hub mediante Power BI](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]