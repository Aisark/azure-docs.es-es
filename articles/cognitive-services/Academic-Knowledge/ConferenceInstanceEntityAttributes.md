---
title: 'Atributos de la entidad de instancia de conferencia: Academic Knowledge API'
titlesuffix: Azure Cognitive Services
description: Obtenga información sobre los atributos que puede usar con la entidad de instancia de conferencia en Academic Knowledge API.
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 183a307159adb5dfdb248eb0cf4862462a626db6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60498753"
---
# <a name="conference-instance-entity"></a>Entidad de instancia de conferencia

<sub> *Los atributos siguientes son específicos de la entidad de instancia de conferencia. (Ty = '4') </sub>

Name    |DESCRIPCIÓN                            |Type       | Operaciones
------- | ------------------------------------- | --------- | ----------------------------
Id      |El identificador de entidad                              |Int64      |Equals
CIN     |Nombre normalizado de instancia de conferencia ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |string     |Equals
DCN     |Nombre para mostrar de instancia de conferencia ({ConferenceSeriesName} : {ConferenceInstanceYear})       |string     |None
CIL     |Ubicación de la instancia de conferencia    |string     |Equals,<br/>StartsWith
CISD    |Fecha de inicio de la instancia de conferencia  |Date       |Equals,<br/>IsBetween
CIED    |Fecha de finalización de la instancia de conferencia    |Date       |Equals,<br/>IsBetween
CIARD   |Fecha de vencimiento del registro del resumen de la instancia de conferencia  |Date       |Equals,<br/>IsBetween
CISDD   |Fecha de vencimiento del envío de la instancia de conferencia     |Date       |Equals,<br/>IsBetween
CIFVD   |Fecha de vencimiento de la versión final de la instancia de conferencia  |Date       |Equals,<br/>IsBetween
CINDD   |Fecha de notificación de la instancia de conferencia   |Date       |Equals,<br/>IsBetween
CD.T    |Título de un evento de la instancia de conferencia   |Date       |Equals,<br/>IsBetween
CD.D    |Fecha de un evento de la instancia de conferencia    |Date       |Equals,<br/>IsBetween
PCS.CN  |Nombre de la serie de conferencias de la instancia |string     |Equals
PCS.CId |Identificador de la serie de conferencias de la instancia |Int64    |Equals
CC      |Recuento de citas total de la instancia de conferencia           |Int32      |None  
ECC     |Recuento de citas estimado total de la instancia de conferencia |Int32      |None


## <a name="extended-metadata-attributes"></a>Atributos de metadatos extendidos ##

Name    | DESCRIPCIÓN               
--------|---------------------------    
FN      | Nombre completo de la instancia de conferencia
