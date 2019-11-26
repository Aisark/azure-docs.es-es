---
title: Creación de informes interactivos con libros de Azure Monitor con control de acceso basado en roles | Microsoft Docs
description: Simplifique la creación de informes complejos con libros parametrizados creados previamente y personalizados con control de acceso basado en roles
services: azure-monitor
author: mrbullwinkle
manager: carmonm
ms.service: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: mbullwin
ms.openlocfilehash: e2f1388d9823744d2382f1818ecb8dcb613895bc
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164522"
---
# <a name="access-control"></a>Control de acceso

El control de acceso en los libros hace referencia a dos cosas:

* Acceso necesario para leer los datos de un libro. Este acceso está controlado por los [roles estándar de Azure](https://docs.microsoft.com/azure/role-based-access-control/overview) en los recursos usados en el libro. Los libros no especifican ni configuran el acceso a esos recursos. Normalmente, los usuarios obtendrán este acceso a esos recursos mediante el rol de [Lector de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) en esos recursos.

* Acceso necesario para guardar libros

    - Guardar los libros `("My")` privados no requiere ningún privilegio adicional. Todos los usuarios pueden guardar libros privados y solo ellos pueden verlos.
    - Guardar los libros compartidos requiere privilegios de escritura en un grupo de recursos para guardar el libro. Estos privilegios se especifican normalmente mediante el rol de [Colaborador de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor), pero también se pueden establecer mediante el rol *Colaborador de libros*.
    
## <a name="standard-roles-with-workbook-related-privileges"></a>Roles estándar con privilegios relacionados con el libro

El [lector de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-reader) incluye privilegios estándar/de lectura que se utilizarían en las herramientas de supervisión (incluidos los libros) para leer los datos de los recursos.

El [colaborador de supervisión](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor) incluye los privilegios de `/write` generales utilizados por diversas herramientas de supervisión para guardar elementos (incluido el privilegio `workbooks/write` para guardar libros compartidos).
"Colaborador de libros" agrega privilegios de "libros y escritura" a un objeto para guardar libros compartidos.
No se requiere ningún privilegio especial para que los usuarios guarden libros privados que solo ellos pueden ver.

Para un control de acceso basado en roles personalizado:

Agregue `microsoft.insights/workbooks/write` para guardar los libros compartidos. Para obtener más información, vea el rol [Colaborador de libro](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#monitoring-contributor).

## <a name="next-steps"></a>Pasos siguientes

* Vaya a [Introducción](workbooks-visualizations.md) para obtener más información acerca de las muchas opciones de visualizaciones enriquecidas para libros.
