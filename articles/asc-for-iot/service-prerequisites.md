---
title: Requisitos previos de Azure Security Center para IoT | Microsoft Docs
description: Detalles de todo lo necesario para empezar a conocer los requisitos previos del servicio Azure Security Center for IoT.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: 4440fec98d1f561da6375bcaadba4282076cc53b
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299473"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Requisitos previos de Azure Security Center for IoT

En este artículo se proporciona una explicación sobre los diferentes bloques de creación del servicio Azure Security Center para IoT, lo que se necesita para comenzar, y explica igualmente los conceptos básicos para ayudar a conocer el servicio. 

## <a name="minimum-requirements"></a>Requisitos mínimos

- Nivel estándar de IoT Hub
    - Privilegios de nivel **Propietario** del rol RBAC 
- [Área de trabajo de Log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace) 
- Azure Security Center (recomendado)
    - El uso de Azure Security Center es una recomendación, no un requisito. Sin Azure Security Center, no podrá ver los demás recursos de Azure en IoT Hub. 
 
## <a name="working-with-azure-security-center-for-iot-service"></a>Uso del servicio Azure Security Center para IoT

Las conclusiones cognitivas y los informes de Azure Security Center para IoT están disponibles mediante Azure IoT Hub y Azure Security Center. Para habilitar Azure Security Center para IoT en Azure IoT Hub, se necesita una cuenta con privilegios de nivel **Propietario**. Después de habilitar Azure Security Center para IoT en IoT Hub, las conclusiones cognitivas de Azure Security Center para IoT se muestra como la característica **Seguridad** en Azure IoT Hub y como **IoT** en Azure Security Center. 

## <a name="supported-service-regions"></a>Regiones de servicio admitidas 

Actualmente, Azure Security Center para IoT es compatible con IoT Hub en las siguientes regiones de Azure:
  - Centro de EE. UU.  
  - East US 
  - Este de EE. UU. 2
  - Centro occidental de EE.UU.
  - Oeste de EE. UU.
  - Oeste de EE. UU. 2
  - Centro-Sur de EE. UU.
  - Centro-Norte de EE. UU
  - Centro de Canadá
  - Este de Canadá 
  - Europa del Norte    
  - Sur de Brasil
  - Centro de Francia  
  - Oeste de Reino Unido 
  - Sur de Reino Unido 2
  - Europa occidental 
  - Norte de Europa 
  - Oeste de Japón  
  - Este de Japón  
  - Sudeste de Australia
  - Este de Australia
  - Asia oriental   
  - Sudeste asiático
  - Corea Central
  - Corea del Sur 
  - India Central
  - Sur de la India

Azure Security Center para IoT enruta todo el tráfico desde todas las regiones europeas hasta el centro de datos de Oeste de Europa, y desde todas las regiones restantes hasta el centro de datos regional de Centro de EE. UU.  
  
## <a name="wheres-my-iot-hub"></a>¿Dónde está mi IoT Hub?

Antes de comenzar, compruebe la ubicación del IoT Hub para confirmar la disponibilidad del servicio. 

1. Abra IoT Hub. 
2. Haga clic en **Descripción general**. 
3. Compruebe que la ubicación que aparece coincide con una de las [regiones de servicio admitidas](#supported-service-regions). 


## <a name="supported-platforms-for-agents"></a>Plataformas compatibles con los agentes 

Los agentes de Azure Security Center para IoT admiten una lista cada vez mayor de dispositivos y plataformas. Vea la [lista de plataformas admitidas](how-to-deploy-agent.md) para consultar su biblioteca de dispositivos prevista o existente.  

## <a name="next-steps"></a>Pasos siguientes
- Lea la [introducción](overview.md) a la seguridad de Azure IoT
- Aprenda a [habilitar el servicio](quickstart-onboard-iot-hub.md)
- Lea las [preguntas más frecuentes sobre Azure Security Center para IoT](resources-frequently-asked-questions.md)
- Explore la [información sobre Alertas de seguridad de Azure Security Center para IoT](concept-security-alerts.md)
