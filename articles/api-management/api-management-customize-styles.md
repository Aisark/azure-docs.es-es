---
title: Personalización del estilo de página en el portal para desarrolladores de Azure API Management | Microsoft Docs
description: Siga los pasos de esta guía de inicio rápido para personalizar el estilo de los elementos del portal para desarrolladores de Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 4ea64b16a9a581683d3b7a44b4b331af435db22c
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/12/2019
ms.locfileid: "57762728"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Personalización del estilo de las páginas del portal para desarrolladores

Existen tres maneras comunes de personalizar el portal para desarrolladores en Azure API Management:
 
* [Editar el contenido de las páginas estáticas y los elementos de diseño de página](api-management-modify-content-layout.md)
* Actualizar los estilos usados en los elementos de página en el portal para desarrolladores (que se explica en esta guía)
* [Modificar las plantillas usadas en las páginas generadas por el portal](api-management-developer-portal-templates.md) (por ejemplo, documentos de API, productos, autenticación de usuario)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Personalizar el estilo de los elementos de las páginas del portal para **desarrolladores**
> * Ver el cambio

![Personalizar el estilo](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Requisitos previos

+ Conocer la [terminología de API Management de Azure](api-management-terminology.md).
+ Complete el siguiente inicio rápido: [Creación de una instancia de Azure API Management](get-started-create-service-instance.md).
+ Además, realice el siguiente tutorial: [Importación y publicación de la primera API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="customize-the-developer-portal"></a>Personalización del portal para desarrolladores

1. Seleccione **Información general**.
2. Haga clic en el botón **Portal para desarrolladores** en la parte superior de la ventana **Información general**. También puede hacer clic en el vínculo de la **dirección URL del portal para desarrolladores**.
3. En el lado superior izquierdo de la pantalla, verá un icono formado por dos pinceles. Mantenga el puntero sobre este icono para abrir el menú de personalización del portal.

    ![Personalizar el estilo](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. Seleccione **Estilos** en el menú para abrir el panel de personalización del estilo.

    En la página aparecen todos los elementos que se pueden personalizar con **Estilos**.
5. Escriba "headings-color" en el campo **Change variable values to customize developer portal appearance:** (Cambiar valores de variables para personalizar la apariencia del portal para desarrolladores).

    El elemento **\@headings-color** aparece en la página. Esta variable controla el color del texto.

    ![Personalizar el estilo](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Haga clic en el campo correspondiente a la variable **\@headings-color**. 
    
    Se abre la lista desplegable del selector de colores.
7. En esta lista, seleccione un nuevo color.

    > [!TIP]
    > Existe una versión preliminar en tiempo real disponible de todos los cambios. Un indicador de progreso aparece en la parte superior del panel de personalización. Al cabo de unos segundos, el color del texto del encabezado cambia al que se acaba de seleccionar.

8. Seleccione **Publicar** en la parte inferior izquierda del menú del panel de personalización.
9. Seleccione **Publish customizations** (Publicar personalizaciones) para que los cambios estén disponibles públicamente.

## <a name="view-your-change"></a>Ver el cambio

1. Desplácese hasta el portal para desarrolladores.
2. Puede ver el cambio que ha realizado.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Personalizar el estilo de los elementos de las páginas del portal para **desarrolladores**
> * Ver el cambio

También podría estar interesado en aprender [cómo personalizar el portal para desarrolladores para Azure API Management mediante plantillas](api-management-developer-portal-templates.md).