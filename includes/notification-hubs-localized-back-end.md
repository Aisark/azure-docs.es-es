---
title: archivo de inclusión
description: archivo de inclusión
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 04/02/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: c15d695e072e72c6e7be6dcf49f3ea049a9b70b7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66152407"
---
Cuando se envían notificaciones de plantilla, solo necesita proporcionar un conjunto de propiedades. En este escenario, el conjunto de propiedades contiene la versión localizada de las noticias actuales.

```json
{
    "News_English": "World News in English!",
    "News_French": "World News in French!",
    "News_Mandarin": "World News in Mandarin!"
}
```

### <a name="send-notifications-using-a-c-console-app"></a>Envío de notificaciones mediante una aplicación de consola de C#

En esta sección se muestra cómo enviar notificaciones con una aplicación de consola. El código difunde las notificaciones a los dispositivos iOS y a la Tienda Windows. Modifique el método `SendTemplateNotificationAsync` en la aplicación de consola que creó anteriormente con el código siguiente:

```csharp
private static async void SendTemplateNotificationAsync()
{
    // Define the notification hub.
    NotificationHubClient hub = 
        NotificationHubClient.CreateClientFromConnectionString(
            "<connection string with full access>", "<hub name>");

    // Sending the notification as a template notification. All template registrations that contain 
    // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
    // This includes APNS, GCM, WNS, and MPNS template registrations.
    Dictionary<string, string> templateParams = new Dictionary<string, string>();

    // Create an array of breaking news categories.
    var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
    var locales = new string[] { "English", "French", "Mandarin" };

    foreach (var category in categories)
    {
        templateParams["messageParam"] = "Breaking " + category + " News!";

        // Sending localized News for each tag too...
        foreach( var locale in locales)
        {
            string key = "News_" + locale;

            // Your real localized news content would go here.
            templateParams[key] = "Breaking " + category + " News in " + locale + "!";
        }

        await hub.SendTemplateNotificationAsync(templateParams, category);
    }
}
```

El método SendTemplateNotificationAsync entrega las noticias localizadas a **todos** los dispositivos, independientemente de la plataforma. El centro de notificaciones crea y entrega la carga nativa correcta a todos los dispositivos suscritos a una etiqueta específica.

### <a name="sending-notification-with-mobile-services"></a>Envío de la notificación con Mobile Services

En el programador de Mobile Services, use el script siguiente:

```csharp
var azure = require('azure');
var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
var notification = {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin", "World News in Mandarin!"
}
notificationHubService.send('World', notification, function(error) {
    if (!error) {
        console.warn("Notification successful");
    }
});
```
