---
title: Apertura de los puertos de firewall necesarios para una aplicación de proxy de aplicación
description: Averigüe qué puertos se deben abrir para que el proxy de aplicación de Azure AD funcione correctamente
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275546"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Apertura de los puertos de firewall necesarios para una aplicación de proxy de aplicación

Para ver una lista completa de los puertos necesarios y la función de cada uno, consulte la sección Requisitos previos de la [documentación del proxy de aplicación](application-proxy-add-on-premises-application.md). Tenga en cuenta que el proxy de aplicación solo usa puertos de salida.

También puede comprobar si tiene todos los puertos necesarios abiertos si abre la [herramienta de prueba de puertos de conector](https://aadap-portcheck.connectorporttest.msappproxy.net/) desde la red local. Cuanto mayor sea el número de marcas de verificación verdes, mayor será la resistencia. 

## <a name="app-proxy-regions"></a>Regiones de proxy de aplicación

Se está trabajando en una forma de hacerle saber cuál de estas regiones debe estar verde para su caso. Por ahora, asegúrese de que todas ellas lo estén. También se necesita Centro de EE. UU., independientemente de en qué región se encuentre.

Para asegurarse de que la herramienta ofrezca los resultados correctos, debe:

-   Abrir la herramienta en un explorador desde el servidor donde esté instalado el conector.

-   Asegurarse de que los servidores proxy o firewalls aplicables a su conector también estén aplicados a esta página. Para hacerlo en Internet Explorer, vaya a **Configuración** -&gt; **Opciones de Internet** -&gt; **Conexiones** -&gt; **Configuración LAN**. En esta página, verá el campo "Usar un servidor proxy para la LAN". Active esta casilla y escriba la dirección del proxy en el campo "Dirección".

## <a name="next-steps"></a>Pasos siguientes
[Descripción de los conectores del Proxy de aplicación de Azure AD](application-proxy-connectors.md)
