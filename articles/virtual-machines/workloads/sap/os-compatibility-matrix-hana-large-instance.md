---
title: Matriz de compatibilidad del sistema operativo para SAP HANA (instancias grandes) | Microsoft Docs
description: La matriz de compatibilidad representa la compatibilidad de versiones diferentes del sistema operativo con distintos tipos de hardware (instancias grandes)
services: virtual-machines-linux
documentationcenter: ''
author: sasarava
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/03/2020
ms.author: sasarava
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aa19433ef6446932da3509694ccccd08538b964f
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2020
ms.locfileid: "78675640"
---
# <a name="compatible-operating-systems-for-hana-large-instances"></a>Sistemas operativos compatibles con instancias grandes de HANA

## <a name="hana-large-instance-type-i"></a>Instancia grande de HANA tipo I     
  | Sistema operativo | Disponibilidad        | SKU                                                          |
  |------------------|---------------------|---------------------------------------------------------------|
  | SLES 12 SP2      | No disponible | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP3      | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm              |
  | SLES 12 SP4      | Disponible           | S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224, S224m |
  
### <a name="persistent-memory-skus"></a>SKU de memoria persistente
  | Sistema operativo | Disponibilidad | SKU                             |
  |------------------|--------------|----------------------------------|
  | SLES 12 SP4      | Disponible    | S224oo, S224om, S224ooo, S224oom |
  
## <a name="hana-large-instance-type-ii"></a>Instancia grande de HANA tipo II     
  |  Sistema operativo       | Disponibilidad        | SKU                                                              |
  |-------------------------|---------------------|-------------------------------------------------------------------|
  | SLES 12 SP2             | No disponible | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  | SLES 12 SP3             | Disponible           | S384, S384m, S384xm, S384xxm, S576m, S576xm, S768m, S768xm, S960m |
  
## <a name="related-documents"></a>Documentos relacionados

- Más información sobre las [SKU disponibles](hana-available-skus.md).
- Más información sobre la [actualización del sistema operativo](os-upgrade-hana-large-instance.md).
  

  
  
