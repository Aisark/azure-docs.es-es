---
title: 'Conexión a Windows Virtual Desktop en Windows 10 o 7: Azure'
description: Cómo conectarse a Windows Virtual Desktop mediante el cliente de escritorio de Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: helohr
ms.openlocfilehash: 15bda000f0297f6e8e006416672897cb15792b79
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367482"
---
# <a name="connect-with-the-windows-desktop-client"></a>Conexión con el cliente de Escritorio de Windows

> Se aplica a: Windows 7, Windows 10 y Windows 10 IoT Enterprise

Para acceder a los recursos de Windows Virtual Desktop en dispositivos con Windows 7, Windows 10 o Windows 10 IoT Enterprise use el cliente de escritorio de Windows.

> [!IMPORTANT]
> Windows Virtual Desktop no es compatible con el cliente de Conexión de RemoteApp y Escritorio (RADC) ni con el cliente de Conexión a Escritorio remoto (MSTSC).

## <a name="install-the-windows-desktop-client"></a>Instalación del cliente de escritorio de Windows

Elija el cliente que coincida con su versión de Windows:

- [Windows 64 bits](https://go.microsoft.com/fwlink/?linkid=2068602)
- [Windows 32 bits](https://go.microsoft.com/fwlink/?linkid=2098960)
- [Windows ARM64](https://go.microsoft.com/fwlink/?linkid=2098961)

Puede instalar el cliente para el usuario actual, lo que no requiere derechos de administrador, o bien el administrador puede instalar y configurar el cliente, con el fin de que todos los usuarios del dispositivo puedan acceder a él.

Una vez instalado, el cliente se puede iniciar desde el menú Inicio. Para ello, solo hay que buscar **Escritorio remoto**.

## <a name="subscribe-to-a-feed"></a>Suscripción a una fuente

Para obtener la lista de recursos administrados a su disposición, suscríbase a la fuente proporcionada por el administrador. La suscripción hace que los recursos estén disponibles en su equipo local.

Para suscribirse a una fuente:

1. Abra el cliente de escritorio de Windows.
2. Seleccione **Suscribirse** en la página principal para conectarse al servicio y recuperar los recursos.
3. Inicie sesión con su cuenta de usuario cuando se le solicite.

Después de iniciar sesión correctamente, debería ver una lista de los recursos a los que puede acceder.

Puede iniciar los recursos mediante uno de estos dos métodos.

- Desde la página principal del cliente, haga doble clic en un recurso para iniciarlo.
- Inicie un recurso como lo haría normalmente en otras aplicaciones desde el menú Inicio.
  - También puede buscar las aplicaciones en la barra de búsqueda.

Una vez que se haya suscrito a una fuente, el contenido de la fuente se actualiza automáticamente de forma periódica. Pueden agregarse, cambiarse o quitarse recursos de acuerdo con los cambios hechos por el administrador.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre cómo usar el cliente de escritorio de Windows, consulte [Introducción al cliente de escritorio de Windows](/windows-server/remote/remote-desktop-services/clients/windowsdesktop/).
