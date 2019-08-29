---
title: archivo de inclusión
description: archivo de inclusión
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ddab6927044ce638e50ff3ad79aa3c35d046c820
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69520881"
---
La siguiente configuración se usó para los pasos siguientes:

  | | |
  |---|---|
  |Computer| Ubuntu Server 18.04|
  |Dependencias| strongSwan |


Ejecute los comandos siguientes para instalar la configuración necesaria de strongSwan:

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

Use el siguiente comando para instalar la interfaz de la línea de comandos de Azure:

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[Instrucciones adicionales sobre cómo instalar la CLI de Azure](https://docs.microsoft.com/cli/azure/install-azure-cli-apt?view=azure-cli-latest)
