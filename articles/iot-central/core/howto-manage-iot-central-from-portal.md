---
title: Administración de IoT Central desde Azure Portal | Microsoft Docs
description: En este artículo se describe cómo crear y administrar aplicaciones de IoT Central desde el Azure Portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: c86df7c50e59309f921c60738870407e74a23219
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/27/2019
ms.locfileid: "74555214"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>Administración de IoT Central desde Azure Portal

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

En lugar de crear y administrar aplicaciones de IoT Central desde el sitio web del [administrador de aplicaciones de Azure IoT Central](https://aka.ms/iotcentral), puede usar [Azure Portal](https://portal.azure.com) para administrar las aplicaciones.

## <a name="create-iot-central-applications"></a>Creación de aplicaciones de IoT Central

Para crear una aplicación, vaya a [Azure Portal](https://ms.portal.azure.com) y seleccione **Crear un recurso** en el panel de la izquierda.

![Portal de administración: menú de navegación](media/howto-manage-iot-central-from-portal/image0.png)

En la barra de búsqueda escriba el término **IoT Central**.

![Portal de administración: buscar](media/howto-manage-iot-central-from-portal/image0a1.png)

Seleccione el elemento de línea de la **aplicación de IoT Central** en los resultados de la búsqueda.

![Portal de administración: resultados de la búsqueda](media/howto-manage-iot-central-from-portal/image0b1.png)

Ahora, seleccione **Crear**.

![Portal de administración: Recurso de IoT Central](media/howto-manage-iot-central-from-portal/image0c1.png)

Rellene todos los campos en el formulario. Este formulario es similar al que se debe rellenar para crear aplicaciones en el sitio web del [administrador de aplicaciones de Azure IoT Central](https://aka.ms/iotcentral). Para más información, consulte la guía de inicio rápido para [Crear una aplicación de IoT Central](quick-deploy-iot-central.md).

Para crear la aplicación de IoT Central con las características disponibles con carácter general, seleccione **Contoso de ejemplo**, **Aplicación personalizada** y **Kits de desarrollo de ejemplo** como plantillas de aplicación; todas las demás plantillas de aplicación usan las características de la versión preliminar pública.

![Creación de formularios de IoT Central](media/howto-manage-iot-central-from-portal/image6a.png)

**Ubicación** es la [zona geográfica](https://azure.microsoft.com/global-infrastructure/geographies/) donde quiere crear la aplicación. Normalmente, se debe elegir la ubicación más cercana físicamente a los dispositivos para un rendimiento óptimo. Azure IoT Central está disponible actualmente en **Estados Unidos**, **Australia**, **Asia Pacífico** o **Europa**.  Una vez elegida una ubicación, no podrá mover la aplicación a otra más adelante.

> [!NOTE]
> Las plantillas de aplicación de versión preliminar solo están disponibles actualmente en **Europa** y **Estados Unidos**.

![Portal de administración: crear un recurso de IoT Central](media/howto-manage-iot-central-from-portal/image1a.png)  

Después de rellenar todos los campos, seleccione **Crear**.

## <a name="manage-existing-iot-central-applications"></a>Administrar las aplicaciones existentes de IoT Central

Si ya tiene una aplicación de Azure IoT Central, puede eliminarla o moverla a un grupo de recursos o suscripción diferente en Azure Portal.

> [!NOTE]
> No puede ver las aplicaciones de la versión de evaluación en Azure Portal porque no están asociadas con su suscripción.

Para empezar, seleccione **Todos los recursos** en el panel de la izquierda. En el cuadro de búsqueda, escriba el nombre de la aplicación para buscarla en la lista de recursos. Después, seleccione la aplicación de IoT Central que quiera administrar.

![Portal de administración: administración de recursos](media/howto-manage-iot-central-from-portal/image2a.png)

Para navegar a la aplicación, seleccione la URL de la aplicación de IoT Central.

![Portal de administración: administración de recursos](media/howto-manage-iot-central-from-portal/image3.png)

Para mover la aplicación a otro grupo de recursos, seleccione **Cambiar** al lado del grupo de recursos. En la página **Mover recursos**, seleccione el grupo de recursos al que le gustaría migrar esta aplicación.

![Portal de administración: administración de recursos](media/howto-manage-iot-central-from-portal/image4a.png)

Para mover la aplicación a otra suscripción, seleccione el vínculo **Cambiar** al lado de la suscripción. Seleccione la suscripción a la que le gustaría migrar esta aplicación en el cuadro de diálogo que aparece.

![Portal de administración: administración de recursos](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar aplicaciones de Azure IoT Central desde Azure Portal, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [Administrar su aplicación](howto-administer.md)