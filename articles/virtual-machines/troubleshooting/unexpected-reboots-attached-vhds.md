---
title: Solución de problemas de reinicio inesperado de máquinas virtuales de Azure con discos duros virtuales conectados | Microsoft Docs
description: Solución de problemas de reinicio inesperado de máquinas virtuales.
keywords: conexión ssh rechazada, error de ssh azure ssh, error de conexión ssh
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.topic: article
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 6273087e28be8b784168d5808918d04d0e4cf303
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60443728"
---
# <a name="troubleshoot-unexpected-reboots-of-vms-with-attached-vhds"></a>Solución de problemas de reinicios inesperados de máquinas virtuales con discos duros virtuales asociados

Si una máquina virtual de Azure (VM) tiene una gran cantidad de discos duros virtuales de la misma cuenta de almacenamiento conectados, es posible que supere los objetivos de escalabilidad de una cuenta de almacenamiento individual, lo que provocaría un reinicio inesperado en la máquina virtual. Compruebe las métricas por minuto de la cuenta de almacenamiento (**TotalRequests**/**TotalIngress**/**TotalEgress**) para ver si hay picos que superen los objetivos de escalabilidad de una cuenta de almacenamiento. Consulte la sección [Las métricas muestran un aumento de PercentThrottlingError](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#metrics-show-an-increase-in-PercentThrottlingError) para recibir asistencia a la hora de averiguar si se produjo alguna limitación en la cuenta de almacenamiento.

En general, cada operación individual de entrada o salida de un VHD desde una máquina virtual se traduce en operaciones **Get Page** o **Put Page** en el blob en páginas subyacente. Por lo tanto, puede usar el IOPS estimado para su entorno con el fin de ajustar la cantidad de VHD que puede tener en una sola cuenta de almacenamiento, en función del comportamiento específico de la aplicación. Microsoft recomienda tener 40 o menos discos en una cuenta de almacenamiento única. Consulte [Objetivos de escalabilidad y rendimiento de Azure Storage](../../storage/common/storage-scalability-targets.md) para detalles de los objetivos de escalabilidad para las cuentas de almacenamiento, en particular, el ancho de banda total y la velocidad de solicitudes total para el tipo de cuenta de almacenamiento que usa.

Si supera los objetivos de escalabilidad para la cuenta de almacenamiento, coloque los VHD en varias cuentas de almacenamiento para reducir la actividad de cada cuenta individual.
