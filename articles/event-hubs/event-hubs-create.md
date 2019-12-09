---
title: 'Guía de inicio rápido de Azure: Creación de un centro de eventos mediante Azure Portal'
description: En esta guía de inicio rápido, aprenderá a crear un centro de eventos de Azure desde Azure Portal y, después, enviar y recibir eventos mediante el SDK de .NET Standard.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
ms.service: event-hubs
ms.topic: quickstart
ms.custom: mvc
ms.date: 12/02/2019
ms.author: shvija
ms.openlocfilehash: a94e67af927365eda2b8b98240285e7cd3079a95
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688137"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Inicio rápido: Creación de un centro de eventos mediante Azure Portal
Azure Event Hubs es una plataforma de streaming de macrodatos y servicio de ingesta de eventos de gran escalabilidad capaz de recibir y procesar millones de eventos por segundo. Event Hubs puede procesar y almacenar eventos, datos o telemetría generados por dispositivos y software distribuido. Los datos enviados a un centro de eventos se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes y almacenamiento. Para más información sobre Event Hubs, consulte [Introducción a Event Hubs](event-hubs-about.md) y [Características de Event Hubs](event-hubs-features.md).

En esta guía de inicio rápido se crea un centro de eventos mediante [Azure Portal](https://portal.azure.com).

## <a name="prerequisites"></a>Requisitos previos

Para completar esta guía de inicio rápido, asegúrese de que tiene:

- Suscripción de Azure. Si no tiene una, [cree una cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- [Visual Studio 2019](https://www.visualstudio.com/vs) o cualquier versión posterior.
- [SDK de .NET Standard](https://www.microsoft.com/net/download/windows), versión 2.0 o posterior.

## <a name="create-a-resource-group"></a>Creación de un grupo de recursos

Un grupo de recursos es una recopilación lógica de recursos de Azure. Todos los recursos se implementan y administran en un grupo de recursos. Para crear un grupo de recursos:

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el panel de la izquierda, seleccione **Grupos de recursos**. A continuación, haga clic en **Agregar**.

   ![Grupos de recursos: botón Agregar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. En **Suscripción** seleccione el nombre de la suscripción de Azure en la que desea crear el grupo de recursos.
3. Escriba un **nombre único para el grupo de recursos**. El sistema comprueba de forma inmediata para ver si el nombre está disponible en la suscripción de Azure seleccionada actualmente.
4. Seleccione una **región** para el grupo de recursos.
5. Seleccione **Revisar + crear**.

   ![Grupo de recursos: Crear](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. En la página **Revisar + crear**, seleccione **Crear**. 

## <a name="create-an-event-hubs-namespace"></a>Creación de un espacio de nombres de Event Hubs

Un espacio de nombres de Event Hubs proporciona un contenedor con un único ámbito, al que hace referencia su nombre de dominio completo, en el que puede crear uno o varios centros de eventos. Para crear un espacio de nombres en el grupo de recursos mediante el portal, haga lo siguiente:

1. En Azure Portal, haga clic en **Crear un recurso** en la parte superior izquierda de la pantalla.
2. Seleccione **Todos los servicios** en el menú de la izquierda y seleccione el **asterisco (`*`)** junto a **Event Hubs** en la categoría **Análisis**. Confirme que **Event Hubs** se agrega a **FAVORITOS** en el menú de navegación de la izquierda. 
    
   ![Búsqueda de instancias de Event Hubs](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Seleccione **Event Hubs** en **FAVORITOS** en el menú de navegación de la izquierda y seleccione **Agregar** en la barra de herramientas.

   ![Botón Agregar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. En la página **Crear espacio de nombres**, realice los pasos siguientes:
    1. Escriba el **nombre** del espacio de nombres. El sistema realiza la comprobación automáticamente para ver si el nombre está disponible.
    2. Elija el **plan de tarifa** (Básico o Estándar).
    3. Observe que la opción **Habilitar Kafka** se habilita automáticamente. Azure Event Hubs proporciona un punto de conexión de Kafka. Este punto de conexión permite que el espacio de nombres de Event Hubs entienda el protocolo de mensajes y las API de [Apache Kafka](https://kafka.apache.org/intro). Con esta funcionalidad, puede comunicarse con las instancias de Event Hubs como lo haría con temas de Kafka sin cambiar los clientes de protocolo ni ejecutar sus propios clústeres. Admite [Apache Kafka versión 1.0.](https://kafka.apache.org/10/documentation.html) y versiones posteriores.
    4. Seleccione la **suscripción** en la que desea crear el espacio de nombres.
    5. Seleccione un **grupo de recursos** existente o cree uno nuevo. 
    4. Seleccione una **ubicación** para el espacio de nombres.
    5. Seleccione **Crear**. Puede que tenga que esperar unos minutos hasta que el sistema aprovisione totalmente los recursos.

       ![Creación de un espacio de nombres del centro de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
5. Actualice la página de **Event Hubs** para ver el espacio de nombres del centro de eventos. Puede comprobar el estado de la creación del centro de eventos en las alertas. 

    ![Creación de un espacio de nombres del centro de eventos](./media/event-hubs-quickstart-portal/event-hubs-refresh.png)
6. Seleccione el espacio de nombres. Puede ver la página principal del **espacio de nombres de Event Hubs** en el portal. 

   ![Página principal del espacio de nombres](./media/event-hubs-quickstart-portal/namespace-home-page.png)
    
## <a name="create-an-event-hub"></a>Creación de un centro de eventos

Para crear un centro de eventos en el espacio de nombres, haga lo siguiente:

1. En la página Espacio de nombres de Event Hubs, seleccione **Event Hubs** en el menú de la izquierda.
1. En la parte superior de la ventana, haga clic en **+ Centro de eventos**.
   
    ![Botón Agregar centro de eventos](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Escriba el nombre del centro de eventos y, a continuación, haga clic en **Crear**.
   
    ![Creación de un centro de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Puede comprobar el estado de la creación del centro de eventos en las alertas. Una vez creado el centro de eventos, puede verlo en la lista de centros de eventos como se muestra en la siguiente imagen:

    ![Centro de eventos creado](./media/event-hubs-quickstart-portal/event-hub-created.png)

Felicidades. Ha usado el portal para crear un espacio de nombres de Event Hubs y un centro de eventos dentro de ese espacio de nombres. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha creado un grupo de recursos, un espacio de nombres de Event Hubs y un centro de eventos. Para encontrar instrucciones paso a paso sobre cómo enviar eventos a un centro de eventos o recibirlos de este, consulte los tutoriales sobre **envío y recepción de eventos**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo recibir)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
