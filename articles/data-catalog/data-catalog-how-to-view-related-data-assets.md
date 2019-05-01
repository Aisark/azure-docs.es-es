---
title: Visualización de los recursos de datos relacionados en Azure Data Catalog
description: En este artículo se explica cómo ver los recursos de datos relacionados de un recurso de datos seleccionado en Azure Data Catalog.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: b01c328812113ad721b7632978ad28e54a6a3ef1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61000134"
---
# <a name="how-to-view-related-data-assets-in-azure-data-catalog"></a>¿Cómo ver los recursos de datos relacionados en Azure Data Catalog?
Azure Data Catalog permite ver los recursos de datos relacionados con un recurso de datos seleccionado, así como ver las relaciones entre ellos. 

## <a name="supported-data-sources"></a>Orígenes de datos admitidos 
Al registrar los recursos de datos de los siguientes orígenes de datos, Azure Data Catalog registra automáticamente los metadatos sobre las relaciones de combinación entre los recursos de datos seleccionados. 

- SQL Server
- Azure SQL Database
- MySQL
- Oracle

> [!NOTE]
> Para que Data Catalog importe la relación entre dos recursos de datos, debe registrar ambos recursos a la vez. Si ha agregado uno de ellos por separado, vuelva a agregarlo, junto con el otro recurso de datos, para importar la relación entre ellos.

## <a name="view-related-data-assets"></a>Visualización de los recursos de datos relacionados
Para ver los recursos de datos que están relacionados con un conjunto de datos seleccionado, utilice la pestaña **Relaciones**, tal como se muestra en la imagen siguiente: 

![Azure Data Catalog: visualización de los recursos de datos relacionados](media/data-catalog-how-to-view-related-data-assets/relationships-tab.png)

En este ejemplo, hay dos relaciones para el recurso de datos **ProductSubcategory** seleccionado: 

- La columna ProductSubcategoryID de la tabla Product tiene una relación de clave externa con la columna ProductSubcategoryID de la tabla ProductSubcategory seleccionada. 
- La columna ProductCategoryID de la tabla ProductSubCategory tiene una relación de clave externa con la columna ProductCategoryID de la tabla ProductCategory seleccionada.

> [!NOTE]
> Tenga en cuenta la dirección de la flecha en la vista de árbol de relaciones.  

Para ver más detalles, como el nombre completo de la columna, mueva el mouse sobre él para ver un menú emergente similar al de la imagen siguiente: 

![Azure Data Catalog: elemento emergente Relaciones](media/data-catalog-how-to-view-related-data-assets/relationship-popup.png)

Para incluir las relaciones entre los recursos que ya se han registrado, vuelva a registrar esos recursos.

## <a name="next-steps"></a>Pasos siguientes
- [Cómo administrar recursos de datos](data-catalog-how-to-manage.md)