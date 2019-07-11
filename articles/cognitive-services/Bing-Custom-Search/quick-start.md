---
title: 'Inicio rápido: Creación de la primera instancia de Bing Custom Search | Microsoft Docs'
titlesuffix: Azure Cognitive Services
description: Use este artículo para crear una instancia personalizada de Bing que pueda buscar en los dominios y las páginas web que defina.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 06/18/2019
ms.author: aahi
ms.openlocfilehash: 6949824f598194456837544526223b823dcfc3e5
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/20/2019
ms.locfileid: "67273352"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>Inicio rápido: Creación de la primera instancia de Bing Custom Search

Para usar Bing Custom Search, debe crear una instancia de búsqueda personalizada que defina la vista o el segmento de la web. La instancia contiene valores que especifican los dominios públicos, sitios web y páginas web donde desea que Bing busque, así como los ajustes en la clasificación. 

Para crear la instancia, utilice el [portal de Bing Custom Search](https://customsearch.ai). 

![Imagen del portal de Bing Custom Search](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>Creación de una instancia de búsqueda personalizada

Para crear una instancia de Bing Custom Search:

1. Haga clic en **Introducción** en el [portal de Bing Custom Search](https://customsearch.ai) e inicie sesión con su cuenta Microsoft.

2. Haga clic en **New Instance** (Nueva instancia) y escriba un nombre descriptivo. Puede cambiar el nombre en cualquier momento.
 
3. En la pestaña **Activo** de **Experiencia de búsqueda**, escriba la dirección URL de uno o varios sitios web que quiera incluir en la búsqueda. 

    > [!NOTE]
    > Las instancias de Bing Custom Search solo devolverán los resultados de los dominios y las páginas web que son públicos y Bing haya indexado.

4. Puede usar el lado derecho del portal de Bing Custom Search para escribir una consulta y examinar los resultados de búsqueda que devuelve su instancia de búsqueda. Si no se devuelve ningún resultado, pruebe a escribir una dirección URL diferente.  

5. Haga clic en **Publish** (Publicar) para publicar los cambios en el entorno de producción y actualizar los puntos de conexión de la instancia.

6.  Haga clic en la pestaña **Production** (Producción), en **Endpoints**, (Puntos de conexión) y copie el valor de **Custom Configuration ID** (Id. de configuración personalizada). Necesitará este identificador para anexarlo al parámetro de consulta `customconfig=` en las llamadas de consulta a Custom Search API.


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Inicio rápido: Llamada a un punto de conexión de Bing Custom Search](./call-endpoint-csharp.md)
