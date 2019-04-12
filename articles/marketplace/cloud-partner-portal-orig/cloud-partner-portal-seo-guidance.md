---
title: Guía de SEO de Marketplace de Azure | Microsoft Docs
description: Se proporcionan instrucciones para maximizar la optimización de motor de búsqueda (SEO).
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pbutlerm
ms.openlocfilehash: da7b59400baa4c964dff71fa1f842fede9d68df1
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/11/2019
ms.locfileid: "59488856"
---
# <a name="azure-marketplace-seo-guidance"></a>Guía de SEO de Marketplace de Azure

Este artículo explica cómo puede maximizar la detectabilidad de su oferta a través de la funcionalidad de búsqueda en el [Azure Marketplace](https://azuremarketplace.microsoft.com) y [AppSource](https://appsource.microsoft.com). 


## <a name="general-explanation-of-algorithm"></a>Explicación general del algoritmo

Catálogos de Microsoft usan Azure Search para potenciar las capacidades de búsqueda de la carpeta del sitio. El algoritmo se basa en la frecuencia de términos o frecuencia inversa de documento ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)). Se utiliza el [analizador de Lucene](https://lucene.apache.org/core/) estándar.

En general, todos los campos de texto, categorías y sectores se incluyen en la ponderación de la relevancia. Los términos especializados que usan con poca frecuencia las aplicaciones, pero que se usan con frecuencia en su aplicación, generarán una puntuación de coincidencia superior con la búsqueda. Por tanto, incluir términos como “VM” ofrecería pocas ventajas, mientras que “Azure Search” sería mucho más especializado.
A continuación se muestran los campos más importantes que deben tenerse en cuenta.

 
|  Campo                   | importancia | Guía                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| Nombre de la oferta               |  Alto      | Una coincidencia exacta o cercana a una coincidencia completa con la consulta de búsqueda dará como resultado una clasificación alta.                       |
| Nombre de publicador           |  Alto      | Una coincidencia exacta o cercana a una coincidencia completa con la consulta de búsqueda dará como resultado una clasificación alta.                       |
| Descripción breve        |  Mediano    | El nombre de las aplicaciones o del publicador casi siempre garantizará una clasificación alta, aunque puede que no sea lo más relevante. En este caso, una descripción breve es fundamental. Mantenga el texto conciso y directo. Deben incluirse las palabras clave y los términos de búsqueda esperados para obtener los mejores resultados.  Por ejemplo “Este es el mejor Retail POS basado totalmente en Dynamics 365” es menos eficaz que “Retail POS (punto de venta) para Dynamics 365”.  | 
| Descripción larga         |  Bajo       | La descripción ofrece una manera de ahondar más. Las descripciones más eficaces tienen una longitud razonable y usan las palabras clave.  Una descripción directa con las palabras clave será más beneficiosa que un texto largo. Asegúrese de que los términos clave, como “IoT”, están presentes en la descripción.  |
| Categorías de productos       | Mediano     |  Las categorías de productos se determinan mediante una combinación de opciones de publicador y Microsoft. Seleccione adecuadamente estas categorías para que los usuarios puedan encontrar fácilmente las aplicaciones en la categoría correcta. |
|  |  |  |


## <a name="other-tips"></a>Otras sugerencias

-   Las sugerencias de búsqueda tienen una gran actividad de usuario. Da prioridad a las coincidencias con el nombre de la aplicación o del publicador. La descripción breve se convierte en el campo clave cuando el término de búsqueda no es una coincidencia exacta con el nombre del publicador o de la aplicación.
-   No se incluyen los documentos disponibles para descargar en la ponderación de búsqueda.
-   La adquisición y el uso de las aplicaciones también afectará a la puntuación de búsqueda. Por ejemplo, dadas dos aplicaciones equivalentes en la que una tiene muchos más usuarios, esta obtendrá una puntuación más alta.
