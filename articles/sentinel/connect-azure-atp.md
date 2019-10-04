---
title: Conexión de datos de Azure ATP con Azure Sentinel | Microsoft Docs
description: Aprenda a conectar datos de Azure ATP a Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: rkarlin
ms.openlocfilehash: 764fb4c22bcce5fc5b045e68dc512243e783020e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261840"
---
# <a name="connect-data-from-azure-advanced-threat-protection-atp"></a>Conectar datos de Azure Advanced Threat Protection (ATP)

> [!IMPORTANT]
> El conector de datos de Azure Advanced Threat Protection en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Se pueden transmitir registros desde [Azure Advanced Threat Protection](https://docs.microsoft.com/azure-advanced-threat-protection/what-is-atp) a Azure Sentinel con tan solo un clic.

## <a name="prerequisites"></a>Requisitos previos

- Un usuario que sea administrador global o que tenga permisos de administrador de seguridad
- Debe ser un cliente de la versión preliminar de Azure ATP.

## <a name="connect-to-azure-atp"></a>Conectar a Azure ATP

Asegúrese de que la versión preliminar de Azure ATP está [habilitada en la red](https://docs.microsoft.com/azure-advanced-threat-protection/install-atp-step1).
Si Azure ATP ya está implementado e ingiriendo datos, las alertas sospechosas se pueden transmitir a Azure Sentinel muy fácilmente. Las alertas pueden tardar hasta 24 horas en empezar a transmitirse a Azure Sentinel.


1. Para conectar Azure ATP a Azure Sentinel, primero debe habilitar la integración entre Azure ATP y Microsoft Cloud App Security. Para obtener información sobre cómo hacerlo, consulte [Integración de Azure Advanced Threat Protection](https://docs.microsoft.com/cloud-app-security/aatp-integration).

1. En Azure Sentinel, seleccione **Data connectors** (Conectores de datos) y, después, haga clic en el icono de **Azure Advanced Threat Protection (versión preliminar)** .

1. Puede seleccionar si desea que las alertas de Azure ATP generen incidentes automáticamente en Azure Sentinel. En **Creación de incidentes** seleccione **Habilitar** para habilitar la regla analítica predeterminada que crea incidentes automáticamente a partir de alertas generadas en el servicio de seguridad conectado. Luego, puede editar esta regla en **Analytics** (Análisis) y **Active rules** (Reglas activas).

1. Haga clic en **Conectar**.

1. Para usar el esquema correspondiente en Log Analytics para encontrar alertas de Azure ATP, busque **SecurityAlert**.

> [!NOTE]
> Si las alertas superan los 30 KB, Azure Sentinel dejará de mostrar el campo Entidades en las alertas.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar Azure Advanced Threat Protection a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](quickstart-get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](tutorial-detect-threats-built-in.md).

