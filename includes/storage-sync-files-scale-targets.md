---
title: archivo de inclusión
description: archivo de inclusión
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/05/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6a053b94813145f9ccd69158d18edb728d5dad61
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74795872"
---
| Resource | Destino | Límite máximo |
|----------|--------------|------------|
| Servicios de sincronización de almacenamiento por región | 20 servicios de sincronización de almacenamiento | Sí |
| Grupos de sincronización por servicio de sincronización de almacenamiento | 100 grupos de sincronización | Sí |
| Servidores registrados por servicio de sincronización de almacenamiento | 99 servidores | Sí |
| Puntos de conexión en la nube por grupo de sincronización | 1 punto de conexión en la nube | Sí |
| Puntos de conexión del servidor por grupo de sincronización | 50 puntos de conexión del servidor | Sin |
| Puntos de conexión del servidor por servidor | 30 puntos de conexión de servidor | Sí |
| Objetos del sistema de archivos (archivos y directorios) por grupo de sincronización | 100 millones de objetos | Sin |
| Número máximo de objetos del sistema de archivos (archivos y directorios) en un directorio | 5 millones de objetos | Sí |
| Tamaño máximo del descriptor de seguridad de (archivos y directorios) del objeto | 64 KiB | Sí |
| Tamaño de archivo | 100 GiB | Sin |
| Tamaño mínimo de un archivo que se va a organizar en niveles | V9: basado en el tamaño del clúster del sistema de archivos (tamaño del clúster del sistema de archivos doble). Por ejemplo, si el tamaño del clúster del sistema de archivos es 4 Kb, el tamaño mínimo del archivo será de 8 Kb.<br> V8 y anteriores: 64 KiB  | Sí |

> [!Note]  
> Un punto de conexión de Azure File Sync puede escalar verticalmente hasta el tamaño de un recurso compartido de archivos de Azure. Si se alcanza el límite de tamaño de recurso compartido de archivos de Azure, la sincronización no podrá funcionar.
