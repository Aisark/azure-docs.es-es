---
title: Introducción a la vista de grupos de seguridad de Azure Network Watcher | Microsoft Docs
description: En esta página se proporciona información general sobre las funcionalidades de la vista de seguridad de Network Watcher
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: ad27ab85-9d84-4759-b2b9-e861ef8ea8d8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/26/2017
ms.author: kumud
ms.openlocfilehash: aae57603dea9b7142956065dd65727e59014dcb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684359"
---
# <a name="introduction-to-network-security-group-view-in-azure-network-watcher"></a>Introducción a la vista de grupos de seguridad de red de Azure Network Watcher

Los grupos de seguridad de red pueden asociarse a un nivel de subred o a un nivel de NIC. Cuando se asocia a un nivel de subred, se aplica a todas las instancias de la máquina virtual en la subred. La vista de grupos de seguridad de red devuelve todos los grupos de seguridad de red así como las reglas que están asociadas a un nivel de subred y a la NIC para una máquina virtual que proporciona una perspectiva sobre la configuración. Además, se devuelven las reglas de seguridad vigentes para cada una de las NIC en una máquina virtual. Con la vista de grupos de seguridad de red, se puede evaluar una máquina virtual en busca de vulnerabilidades de red, como puertos abiertos. También se puede validar si el grupo de seguridad de red está funcionando según lo previsto en función de una [comparación entre las reglas de seguridad aprobadas y configuradas](network-watcher-nsg-auditing-powershell.md).

Un caso de uso más extendido se encuentra en el cumplimiento de seguridad y auditoría. Puede definir un conjunto prescriptivo de reglas de seguridad como modelo para la gobernanza de la seguridad de su organización. Una auditoría periódica del cumplimiento puede implementarse de forma programática al comparar las reglas preceptivas con las reglas vigentes para cada una de las máquinas virtuales en la red.

En el portal las reglas se dividen en vigente, subred, interfaz de red y predeterminada. Esto proporciona una vista sencilla de las reglas que se aplican a una máquina virtual. Se incluye un botón de descarga para descargar fácilmente todas las reglas de seguridad con independencia de la pestaña en un archivo CSV.

![vista de grupos de seguridad][1]

Se pueden seleccionar las reglas y se abre una nueva hoja para mostrar el grupo de seguridad de red y los prefijos de origen y de destino. En esta hoja puede navegar directamente al recurso del grupo de seguridad de red.

![obtención de detalles][2]

### <a name="next-steps"></a>Pasos siguientes

Aprenda a auditar la configuración del grupo de seguridad de red consultando [Auditoría de la configuración del grupo de seguridad de red con PowerShell](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-security-group-view-overview/securitygroupview.png
[2]: ./media/network-watcher-security-group-view-overview/figure1.png









