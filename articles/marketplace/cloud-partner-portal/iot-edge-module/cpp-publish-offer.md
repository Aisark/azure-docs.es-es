---
title: Publicar una oferta de módulo Azure IoT Edge | Azure Marketplace
description: Cómo publicar una oferta de módulo IoT Edge.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: pabutler
ms.openlocfilehash: 2310d7a7cad16009bbb58469190a77eedb0619f8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73813832"
---
# <a name="publish-iot-edge-module-offer"></a>Publicar una oferta de módulo IoT Edge

 Después de crear una nueva oferta al proporcionar la información de la página **Nueva oferta**, puede publicar la oferta. Seleccione **Publicar** para iniciar el proceso de publicación.

El diagrama siguiente muestra los pasos principales del proceso de publicación de una oferta para "lanzarla".

![Pasos de publicación de una oferta de módulo IoT Edge](./media/iot-edge-module-publishing-steps.png)

## <a name="detailed-description-of-publishing-steps"></a>Descripción detallada de los pasos de publicación

La tabla siguiente describe cada paso de publicación, con una estimación de tiempo (máxima) para completar cada paso.
<!-- P2: we need to tell them that if an offer seems stuck in a step, to know that they should file a support ticket (link to support ticket doc) -->


|  **Paso de publicación**           | **Hora**    | **Descripción**                                                            |
|  -------------------           | --------    | ---------------                                                            |
| Validar requisitos previos         | 15 minutos   | Se validan la información y la configuración de la oferta.                        |
| Certificación                  | Dos semanas | El equipo de certificación de Azure analiza la oferta. En este paso se llevan a cabo exámenes de virus, malware, cumplimiento de seguridad y problemas de seguridad. También se comprueba que esta oferta de módulo IoT Edge cumpla todos los criterios de elegibilidad (vea [Requisitos previos](./cpp-prerequisites.md) y [Preparar los recursos técnicos](./cpp-create-technical-assets.md)). Si se detecta un problema, se proporcionan comentarios. |
| Packaging | 1 hora  | Los recursos técnicos de la oferta se empaquetan para uso del cliente y se configuran los sistemas del cliente potencial. |
|  Aprobación del publicador             |  -        | Revisión final del publicador y confirmación antes del lanzamiento de la oferta. Puede implementar la oferta en las suscripciones seleccionadas (en los pasos de información de la oferta) para comprobar que cumple todos los requisitos.  Seleccione **Transmitir** para que la oferta pueda pasar al paso siguiente. |
| Packaging                 | 1 hora | La oferta terminada se replica en las regiones y los sistemas de producción de Marketplace. | 
| En vivo                           | 4 días |La oferta se lanza, se replica en las regiones necesarias y se pone a disposición del público. |

Deje pasar hasta diez días laborables para que finalice el proceso de publicación y la oferta se publique. Cuando termina el proceso de publicación, la oferta de módulo IoT Edge aparece en [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

## <a name="next-steps"></a>Pasos siguientes

- [Actualizar una oferta existente de módulo IoT Edge en Azure Marketplace](./cpp-update-existing-offer.md)
