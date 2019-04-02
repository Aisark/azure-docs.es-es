---
title: Nivel de compatibilidad del modelo de datos en Azure Analysis Services | Microsoft Docs
description: Descripción del nivel de compatibilidad del modelo de datos tabulares.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 67a6c99253c549f0b8d3b55809b35b81756843eb
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/01/2019
ms.locfileid: "58803500"
---
# <a name="compatibility-level-for-analysis-services-tabular-models"></a>Nivel de compatibilidad para los modelos tabulares de Analysis Services

El *nivel de compatibilidad* hace referencia a los comportamientos específicos de la versión del motor de Analysis Services. Normalmente, los cambios en el nivel de compatibilidad coinciden con las versiones principales de SQL Server. Estos cambios también se implementan en Azure Analysis Services para mantener la paridad entre ambas plataformas. Los cambios de nivel de compatibilidad también afectan a las características disponibles en los modelos tabulares. Por ejemplo, DirectQuery y los metadatos de objetos tabulares tienen implementaciones diferentes según el nivel de compatibilidad. El nivel de compatibilidad se especifica en el proyecto de modelo tabular en Visual Studio (SSDT).

Azure Analysis Services admite modelos tabulares en los niveles de compatibilidad 1200 y 1400. El nivel de compatibilidad más reciente es el 1400. Este nivel se corresponde a SQL Server 2017 Analysis Services. Entre las características principales del nivel de compatibilidad 1400 cabe destacar:

*  Nuevas características para la conectividad de datos y la importación con compatibilidad con las API de TOM y el scripting de TMSL. 
*  Funcionalidades de transformación de datos y mashup de datos mediante el uso de expresiones Get Data y M.
*  Las medidas admiten una propiedad de filas de detalles con una expresión DAX. Esta propiedad habilita herramientas de cliente como Microsoft Excel para profundizar en los datos detallados de un informe agregado. Por ejemplo, cuando los usuarios ven las ventas totales para una región y mes, pueden ver los detalles del pedido asociados. 
*  Seguridad en el nivel de objeto para los nombres de tablas y columnas, además de para los datos dentro de ellas.
*  Compatibilidad mejorada para las jerarquías desiguales.
*  Mejoras en el rendimiento y la supervisión.

> [!NOTE]
> Azure Analysis Services es compatible con archivos de Power BI Desktop importados en el nivel de compatibilidad 1465. Sin embargo, la importación de la funcionalidad de Power BI Desktop, que siempre se había sido una característica de vista previa, se suspendido y se quitó desde el servicio de marzo de 2019. Los modelos existentes en el nivel de compatibilidad 1465 siguen siendo compatibles.  


## <a name="set-compatibility-level"></a>Establecimiento del nivel de compatibilidad

 Al crear un nuevo proyecto de modelo tabular en SSDT, puede especificar el nivel de compatibilidad en el cuadro de diálogo **Diseñador de modelos tabulares**. 
  
 ![ssas_tabularproject_compat1200](./media/analysis-services-compat-level/aas-tabularproject-compat.png)  
  
 Si selecciona la opción **No mostrar de nuevo este mensaje**, todos los proyectos posteriores usarán el nivel de compatibilidad que especificó como valor predeterminado. Puede cambiar el nivel de compatibilidad predeterminado en SSDT en **Herramientas** > **Opciones**.  
  
 Para actualizar un proyecto de modelo tabular existente en SSDT, establezca la propiedad **Compatibility Level** en la ventana de **propiedades** del modelo. Tenga en cuenta que la actualización del nivel de compatibilidad es irreversible.
  
## <a name="check-compatibility-level-for-a-tabular-model-database-in-sql-server-management-studio"></a>Compruebe el nivel de compatibilidad para una base de datos de modelo tabular en SQL Server Management Studio 

 En SSMS, haga clic con el botón derecho en el nombre de la base de datos > **Propiedades** > **Nivel de compatibilidad**.  
  
## <a name="check-supported-compatibility-level-for-a-server-in-ssms"></a>Comprobación del nivel de compatibilidad admitido para un servidor en SSMS  

 En SSMS, haga clic con el botón derecho en el nombre del servidor > **Propiedades** > **Nivel de compatibilidad admitido**.  
  
 Esta propiedad especifica el máximo nivel de compatibilidad de una base de datos que se ejecutará en el servidor (excepto la versión preliminar). El nivel de compatibilidad admitido no se puede cambiar.  

> [!NOTE]
> En SSMS, cuando se conecta a un servidor de Azure Analysis Services, el **nivel de compatibilidad admitido** propiedad mostrará **1200**. Esto es un problema conocido y se resolverá en una próxima SSMS update. Cuando se resuelva, esta propiedad mostrará el máximo nivel de compatibilidad admitido.

## <a name="next-steps"></a>Pasos siguientes

  [Administración de Analysis Services](analysis-services-manage.md)  
