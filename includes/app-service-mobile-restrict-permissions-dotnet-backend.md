---
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 08/23/2018
ms.author: crdun
ms.openlocfilehash: b609a708a987194398c53bdf83f0d6e1f281808d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "67186808"
---
De forma predeterminada, se pueden invocar las API en un back-end de Mobile Apps de forma anónima. A continuación, deberá restringir el acceso a solo los clientes autenticados.  

* **Back-end de Node.js (a través de Azure Portal)** :  

    En la configuración de Mobile Apps, haga clic en **Tablas fáciles** y seleccione la tabla. Haga clic en **Cambiar permisos**, seleccione **Solo acceso autenticado** para todos los permisos y luego haga clic en **Guardar**.
* **Back-end de .NET (C#)** :  

    En el proyecto de servidor, vaya a **Controladores** > **TodoItemController.cs**. Agregue el atributo `[Authorize]` a la clase **TodoItemController** , como sigue. Para restringir el acceso solo a determinados métodos, también puede aplicar este atributo solo a esos métodos en lugar de la clase. Vuelva a publicar el proyecto de servidor.

        [Authorize]
        public class TodoItemController : TableController<TodoItem>

* **Back-end de Node.js (a través del código de Node.js)** :  

    Para pedir autenticación para acceder a las tablas, agregue la siguiente línea al script del servidor de Node.js:

        table.access = 'authenticated';

    Para más información, consulte [Autenticación necesaria para el acceso a las tablas](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). Para obtener información sobre cómo descargar el proyecto de código de inicio rápido desde su sitio, consulte [Cómo: descargar el proyecto de código de inicio rápido de back-end de Node.js con Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).
