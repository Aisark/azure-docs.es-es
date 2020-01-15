---
title: 'Agente de aprovisionamiento en la nube de Azure AD Connect: Actualización automática | Microsoft Docs'
description: En este artículo se describe la característica de actualización automática integrada en el agente de aprovisionamiento en la nube de Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3786386f75f4b85fe89562254eab63471de6cb2
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/31/2019
ms.locfileid: "75549404"
---
# <a name="azure-ad-connect-cloud-provisioning-agent-automatic-upgrade"></a>Agente de aprovisionamiento en la nube de Azure AD Connect: Actualización automática

La característica de actualización automática nos proporciona la seguridad de que la instalación del agente de aprovisionamiento en la nube de Azure Active Directory (Azure AD) Connect está siempre actualizada. Esta característica está habilitada de forma predeterminada y no se puede deshabilitar.

El agente se instala aquí: "Archivos de programa\Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe"

Para comprobar su versión, haga clic con el botón derecho en el archivo ejecutable y seleccione Propiedades; a continuación, seleccione los detalles.

![Versión del archivo del agente](media/how-to-automatic-upgrade/agent1.png)

El actualizador del agente se instala aquí: "Archivos de programa\Azure AD Connect Provisioning Agent Updater\AzureADConnectAgentUpdater.exe"

Para comprobar su versión, haga clic con el botón derecho en el archivo ejecutable y seleccione Propiedades; a continuación, seleccione los detalles.

![Versión del actualizador del agente](media/how-to-automatic-upgrade/agent2.png)

## <a name="uninstall-the-agent"></a>Desinstalación del agente
Para quitar el agente, vaya a **Desinstalar o cambiar un programa** y desinstale lo siguiente:

- **Actualizador del Agente de Microsoft Azure AD Connect**
- **Agente de aprovisionamiento de Microsoft Azure AD Connect**
- **Paquete del Agente de aprovisionamiento de Microsoft Azure AD Connect**

![Eliminación del agente](media/how-to-automatic-upgrade/agent3.png)

## <a name="next-steps"></a>Pasos siguientes 

- [¿Qué es el aprovisionamiento?](what-is-provisioning.md)
- [¿Qué es el aprovisionamiento en la nube de Azure AD Connect?](what-is-cloud-provisioning.md)

