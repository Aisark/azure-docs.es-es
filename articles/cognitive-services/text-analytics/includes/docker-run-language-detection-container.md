---
title: Ejecución del ejemplo de contenedor del comando de ejecución de Docker
titleSuffix: Azure Cognitive Services
description: Comando docker run para el contenedor de detección de idioma
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 08/20/2019
ms.author: dapine
ms.openlocfilehash: b6aaa33667f85bedb4cc33fdb1c7b5f73a64d382
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051247"
---
### <a name="run-container-example-of-docker-run-command"></a>Ejecución del ejemplo de contenedor del comando de ejecución de Docker

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
mcr.microsoft.com/azure-cognitive-services/language \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Este comando:

* Ejecuta un contenedor de *detección de idioma* desde la imagen de contenedor.
* Asigna un núcleo de CPU y 4 gigabytes (GB) de memoria.
* Expone el puerto TCP 5000 y asigna un seudo-TTY para el contenedor.
* Una vez que se produce la salida, quita automáticamente el contenedor. La imagen del contenedor sigue estando disponible en el equipo host.