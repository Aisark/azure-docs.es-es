---
title: archivo de inclusión
description: archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 03/29/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 060bc1039982cc0a77214d5dbe2a08de7a839c84
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850249"
---
Para que una sesión de SSH directa sea abierta con el contenedor, la aplicación debe estar en ejecución.

Pegue la siguiente dirección URL en el explorador y reemplace \<app_name> por el nombre de la aplicación:

```
https://<app-name>.scm.azurewebsites.net/webssh/host
```

Si aún no está autenticado, será preciso que se autentique con su suscripción a Azure para conectarse. Una vez autenticado, verá un shell del explorador en el que puede ejecutar comandos dentro del contenedor.

![Conexión SSH](./media/app-service-web-ssh-connect-no-h/app-service-linux-ssh-connection.png)
