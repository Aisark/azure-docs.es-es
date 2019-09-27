---
title: Actualización del agente de Azure Backup
description: Aquí se explica por qué se debe actualizar el agente de Azure Backup y dónde se descarga la actualización.
services: backup
cloud: ''
suite: ''
author: dcurwin
manager: carmonm
ms.service: backup
ms.tgt_pltfrm: <optional>
ms.devlang: <optional>
ms.topic: article
ms.date: 8/29/2018
ms.author: dacurwin
ms.openlocfilehash: ea1295b08aa77a3e1a03d944ddbcbf37b6d17702
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2019
ms.locfileid: "71251545"
---
## <a name="upgrade-the-mars-agent"></a>Actualización del agente de MARS

Las versiones del agente de Microsoft Azure Recovery Service (MARS) inferiores a la 2.0.9083.0 tienen una dependencia en Azure Access Control Service (ACS). El agente de MARS también se conoce como agente de Azure Backup. En 2018, Azure [retiró Azure Access Control Service (ACS)](../articles/active-directory/develop/active-directory-acs-migration.md). A partir del 19 de marzo de 2018, todas las versiones del agente MARS inferiores a la 2.0.9083.0 sufrirán errores de copia de seguridad. Para evitar o resolver dichos errores, [actualice el agente de MARS a la versión más reciente](https://go.microsoft.com/fwlink/?linkid=229525). Para identificar los servidores que requieren una actualización del agente de MARS, siga los pasos del [blog de Backup para la actualización de agentes de MARS](https://blogs.technet.microsoft.com/srinathv/2018/01/17/updating-azure-backup-agents/). El agente de MARS se usa para hacer copias de seguridad de archivos, carpetas y datos de estado del sistema en Azure. System Center DPM y Azure Backup Server usan al agente de MARS para hacer una copia de seguridad de los datos en Azure.
