---
title: 'Compatibilidad de Linux con Windows Virtual Desktop: Azure'
description: Una breve descripción general de la compatibilidad de Linux con Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 967fd1fa182b7c8e581fd74cc287c5a6ba0e4038
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/11/2020
ms.locfileid: "79127829"
---
# <a name="linux-support"></a>Compatibilidad de Linux

Puede usar el SDK de Linux para Windows Virtual Desktop para compilar un cliente independiente de Windows Virtual Desktop. También puede usarlo para habilitar la compatibilidad con Windows Virtual Desktop en la aplicación cliente. En esta guía rápida se explicará qué es el SDK de Linux y cómo puede empezar a usarlo.

## <a name="what-is-the-linux-sdk"></a>¿Qué es el SDK de Linux?

Puede usar las API del SDK para recuperar fuentes de recursos, conectarse a sesiones de escritorio o de aplicaciones remotas y usar muchos de los redireccionamientos que admiten los clientes de nuestra plataforma.

> [!NOTE]
> El SDK está actualmente en desarrollo. Este documento se actualizará con instrucciones para acceder al SDK cuando esté disponible.

### <a name="supported-linux-distributions"></a>Distribuciones de Linux compatibles

El SDK es compatible con la mayoría de los sistemas operativos basados en Ubuntu 18.04 o versiones posteriores. Si tiene una distribución de Linux diferente, podemos trabajar juntos para averiguar cómo satisfacer mejor sus necesidades.

### <a name="feature-support"></a>Compatibilidad de características

El SDK admite varias conexiones a sesiones de escritorio y aplicaciones remotas. Se admiten los siguientes redireccionamientos:

| Redireccionamiento       | Compatible |
| :---------------- | :-------: |
| Teclado          | &#10004;  |
| Mouse             | &#10004;  |
| Entrada de audio          | &#10004;  |
| Salida de audio         | &#10004;  |
| Portapapeles (texto)  | &#10004;  |
| Portapapeles (imagen) | &#10004;  |
| Portapapeles (archivo)  | &#10004;  |
| Tarjeta inteligente         | &#10004;  |
| Unidad o carpeta      | &#10004;  |

El SDK también admite configuraciones de varios monitores, siempre y cuando seleccione monitores contiguos para su sesión.

Este documento se actualizará a medida que se agregue compatibilidad con nuevas características y redireccionamientos. Si quiere sugerir nuevas características y otras mejoras, visite nuestra [página de UserVoice](https://go.microsoft.com/fwlink/?linkid=2116523).

## <a name="get-started-with-the-linux-sdk"></a>Introducción al SDK de Linux

Antes de que pueda desarrollar un cliente Linux para Windows Virtual Desktop, debe hacer lo siguiente:

1. Cree e implemente un entorno de Windows Virtual Desktop para producción o pruebas.
2. Pruebe los clientes de Microsoft disponibles para familiarizarse con la experiencia del usuario de Windows Virtual Desktop.

## <a name="next-steps"></a>Pasos siguientes

Consulte la documentación de los siguientes clientes:

- [Cliente de escritorio de Windows](connect-windows-7-and-10.md)
- [Cliente web](connect-web.md)
- [Cliente de Android](connect-android.md)
- [Cliente de macOS](connect-macos.md)
- [Cliente de iOS](connect-ios.md)
