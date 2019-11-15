---
title: Arquitectura de Azure FarmBeats
description: Describe la arquitectura de Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 8eaba4c2a987f47c56169af138ef380835d56740
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797325"
---
# <a name="integration-patterns"></a>Patrones de integración

Azure FarmBeats es una oferta de negocio a negocio disponible en Azure Marketplace. FarmBeats permite la agregación de conjuntos de datos de agricultura de distinto proveedores y la generación de conclusiones mediante la creación de modelos de inteligencia artificial (AI) o aprendizaje automático (ML) al fusionar los conjuntos de datos.

![Proyecto de FarmBeats](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

En las secciones siguientes se describe el patrón de integración de Azure FarmBeats.

## <a name="why-integrate-with-azure-farmbeats"></a>Motivos para la integración con Azure AD

Esta sección se centra en los partners que quieren integrar sus sistemas de datos (como sensores, drones o estaciones meteorológicas) en Azure FarmBeats.

Azure FarmBeats es una oferta extensible que permite a las empresas agrarias agregar sus diferentes conjuntos de valores históricos en tiempo real en una sola plataforma. Azure FarmBeats ayuda a las empresas agrarias a normalizar, contextualizar y agregar sus datos en el contexto de una granja.

Al convertirse en un partner de datos con Azure FarmBeats, puede abrir los sistemas para llevar a cabo una adopción más amplia y llegar a más clientes con sus ofertas de datos. Azure FarmBeats proporciona un nivel de API extensible denominado "centro de datos", que lo ayuda a ingerir datos de sus dispositivos de forma sistemática y en un esquema normalizado.

Una vez que los datos estén disponibles en la instancia de Azure FarmBeats de los clientes, los clientes pueden crear análisis y herramientas más completos sobre los datos.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre la integración de datos de sensores, consulte [Integración de datos de sensores](sensor-partner-integration.md), y para la integración de asociados de imagen, consulte [Integración de partners de imágenes](imagery-partner-integration.md).
