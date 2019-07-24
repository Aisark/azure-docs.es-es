---
title: Adición de un origen de eventos de Event Hubs a Azure Time Series Insights | Microsoft Docs
description: En este artículo se describe cómo agregar un origen del evento que esté conectado a Azure Event Hubs a su entorno de Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: 21e054aefab0ee5535376ac86ebbaf1316e671b5
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165699"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Incorporación de un origen del evento de centro de eventos al entorno de Time Series Insights

Este artículo describe cómo usar Azure Portal para agregar un origen de eventos que lee datos de Azure Event Hubs en su entorno de Azure Time Series Insights.

> [!NOTE]
> Los pasos que se describen en este artículo son válidos tanto en entornos de Time Series Insights con disponibilidad general como en entornos en versión preliminar.

## <a name="prerequisites"></a>Requisitos previos

- Cree un entorno de Time Series Insights según lo explicado en [Creación de un entorno de Azure Time Series Insights: Creación de un entorno de Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Cree un centro de eventos. Vea [Creación de un espacio de nombres de Event Hubs y un centro de eventos con Azure Portal](../event-hubs/event-hubs-create.md).
- El centro de eventos tiene que tener eventos de mensajes activos que se le hayan enviado. Encontrará más información en [Envío de eventos a Azure Event Hubs mediante .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Cree un grupo de consumidores dedicado en el centro de eventos para que el entorno de Time Series Insights los consuma. Cada origen del evento de Time Series Insights tiene que tener su propio grupo de consumidores dedicado que no se comparte con ningún otro consumidor. Si varios lectores consumen eventos desde el mismo grupo de consumidores, es probable que todos los lectores vean errores. Hay un límite de 20 grupos de consumidores por centro de eventos. Si desea información más detallada, consulte la [Guía de programación de Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Incorporación de un grupo de consumidores al centro de eventos

Las aplicaciones usan grupos de consumidores para extraer datos de Azure Event Hubs. Para leer datos de manera confiable del centro de eventos, proporcione un grupo de consumidores dedicado, solo para su uso en este entorno de Time Series Insights.

Para agregar un nuevo grupo de consumidores al centro de eventos:

1. En Azure Portal, busque y abra el centro de eventos.

1. En **Entidades** seleccione **Grupos de consumidores** y, a continuación **Grupo de consumidores**.

   [![Centro de eventos: Incorporación de un grupo de consumidores](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. En la página **Grupos de consumidores**, proporcione un nuevo valor exclusivo para **Nombre**.  Use este mismo nombre al crear un nuevo origen del evento en el entorno de Time Series Insights.

1. Seleccione **Crear**.

## <a name="add-a-new-event-source"></a>Adición de un nuevo origen del evento

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

1. Busque su entorno de Time Series Insights existente. En el menú izquierdo, seleccione **Todos los recursos**y después el entorno de Time Series Insights.

1. En **Topología del entorno** seleccione **Orígenes de eventos**y, a continuación, seleccione **Agregar**.

   [![En Orígenes de eventos, seleccione el botón Agregar](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Especifique un valor para **Nombre de origen de evento** que sea exclusivo para este entorno de Time Series Insights, como **event-stream**.

1. En **Origen**, seleccione **Centro de eventos**.

1. Seleccione los valores apropiados para **Opción de importación**:
   - Cuando ya tenga un centro de eventos en una de sus suscripciones seleccione **Use Event Hub from available subscriptions** (Usar el centro de eventos de las suscripciones disponibles). Esta opción presenta el enfoque más sencillo.

       [![En el panel Nuevo origen del evento, escriba los valores para los tres primeros parámetros](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)


       [![Detalles del centro de eventos y la suscripción](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

     En la siguiente tabla se explican las propiedades necesarias para la opción **Use Event Hub from available subscriptions** (Usar el centro de eventos de las suscripciones disponibles):

     | Propiedad | DESCRIPCIÓN |
     | --- | --- |
     | Id. de suscripción | Seleccione la suscripción en la que se creó el centro de eventos.
     | Espacio de nombres de Service Bus | Seleccione el espacio de nombres de Azure Service Bus que contiene el centro de eventos.
     | Nombre del centro de eventos | Seleccione el nombre del centro de eventos.
     | Nombre de la directiva del centro de eventos | Seleccione la directiva de acceso compartido. Puede crear la directiva de acceso compartido en el centro de eventos en la pestaña **Configurar**. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **lectura**.
     | Clave de la directiva de centro de eventos | El valor clave puede haberse rellenado previamente.
     | Grupo de consumidores de centro de eventos | El grupo de consumidores que lee eventos del centro de eventos. Es muy recomendable usar un grupo de consumidores dedicado para el origen del evento. |
     | Formato de serialización de eventos | Actualmente, JSON es el único formato de serialización disponible. Los mensajes de eventos tienen que estar en este formato, de lo contrario no se podrá leer ningún dato. |
     | Nombre de la propiedad de marca de tiempo | Para determinar este valor, es necesario que comprenda el formato de mensaje de los datos del mensaje que se envía al centro de eventos. Este valor es el **nombre** de la propiedad específica del evento en los datos del mensaje que quiere usar como marca de tiempo del evento. El valor distingue mayúsculas de minúsculas. Si se deja en blanco, se usa la **hora de puesta en cola del evento** como marca de tiempo del evento en el origen del evento. |

    - Si el centro de eventos es externo a sus suscripciones o si quiere elegir opciones avanzadas, seleccione **Indicar manualmente la configuración del Centro de eventos**.

      En la siguiente tabla se explican las propiedades necesarias para la opción **Indicar manualmente la configuración del Centro de eventos**:
 
      | Propiedad | DESCRIPCIÓN |
      | --- | --- |
      | Id. de suscripción | La suscripción en la que se creó este centro de eventos.
      | Grupos de recursos | El grupo de recursos en el que se creó este centro de eventos.
      | Espacio de nombres de Service Bus | Un espacio de nombres de Service Bus es un contenedor para un conjunto de entidades de mensajería. Al crear un nuevo centro de eventos, también se crea un espacio de nombres de Service Bus.
      | Nombre del centro de eventos | El nombre del centro de eventos. Cuando creó el centro de eventos, también le asignó un nombre específico.
      | Nombre de la directiva del centro de eventos | La directiva de acceso compartido. Puede crear una directiva de acceso compartido en el centro de eventos en la pestaña **Configurar**. Cada directiva de acceso compartido tiene un nombre, los permisos establecidos y las claves de acceso. La directiva de acceso compartido para el origen de eventos *debe* tener permisos de **lectura**.
      | Clave de la directiva de centro de eventos | La clave de acceso compartido que se usa para autenticar el acceso al espacio de nombres de Service Bus. Especifique la clave principal o secundaria aquí.
      | Grupo de consumidores de centro de eventos | El grupo de consumidores que lee eventos del centro de eventos. Es muy recomendable usar un grupo de consumidores dedicado para el origen del evento.
      | Formato de serialización de eventos | Actualmente, JSON es el único formato de serialización disponible. Los mensajes de eventos tienen que estar en este formato, de lo contrario no se podrá leer ningún dato. |
      | Nombre de la propiedad de marca de tiempo | Para determinar este valor, es necesario que comprenda el formato de mensaje de los datos del mensaje que se envía al centro de eventos. Este valor es el **nombre** de la propiedad específica del evento en los datos del mensaje que quiere usar como marca de tiempo del evento. El valor distingue mayúsculas de minúsculas. Si se deja en blanco, se usa la **hora de puesta en cola del evento** como marca de tiempo del evento en el origen del evento. |

1. Agregue el nombre del grupo de consumidores dedicado de Time Series Insights que agregó al centro de eventos.

1. Seleccione **Crear**.

   [![Selección de la opción Crear](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   Una vez que se haya creado el origen del evento, Time Series Insights iniciará automáticamente la transmisión de datos al entorno.

## <a name="next-steps"></a>Pasos siguientes

* [Defina las directivas de acceso a datos](time-series-insights-data-access.md) para proteger los datos.

* [Envíe eventos](time-series-insights-send-events.md) al origen de eventos.

* Acceso al entorno en el [explorador de Time Series Insights](https://insights.timeseries.azure.com).
