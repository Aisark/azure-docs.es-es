---
title: Introducción a las plantillas de Azure Data Factory | Microsoft Docs
description: Aprenda a usar una plantilla predefinida para empezar a trabajar rápidamente con Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/04/2019
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: 4bd38991b2452bdda65a7647f844dcc17fdfb125
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60786975"
---
# <a name="templates"></a>Plantillas

Las plantillas son canalizaciones de Azure Data Factory predefinidas que le permiten empezar a trabajar rápidamente con Data Factory. Las plantillas son útiles cuando no está familiarizado con Data Factory y desea empezar a trabajar rápidamente. Estas plantillas reducen el tiempo de desarrollo para la creación de proyectos de integración de datos, con lo que mejoran la productividad del desarrollador.

## <a name="create-data-factory-pipelines-from-templates"></a>Creación de canalizaciones de Data Factory a partir de plantillas

Puede empezar a crear una canalización de Data Factory a partir de una plantilla de las siguientes dos maneras:

1.  Seleccione **Create pipeline from template** (Creación de una canalización a partir de una plantilla) en la página de información general para abrir la galería de plantillas.

    ![Abrir la galería de plantillas desde la página de información general](media/solution-templates-introduction/templates-intro-image1.png)

1.  En la pestaña de autor en el Explorador de recursos, seleccione **+** , a continuación, **Pipeline from template** (Canalización a partir de la plantilla) para abrir la galería de plantillas.

    ![Abrir la galería de plantillas desde la pestaña de autor](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Galería de plantillas

![La galería de plantillas](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Plantillas listas para usar de Data Factory

Data Factory usa plantillas de Azure Resource Manager para guardar las plantillas de canalización de las factorías de datos. Puede ver todas las plantillas de Resource Manager, junto con el archivo de manifiesto que se usa para las plantillas listas para usar de Data Factory, en el [repositorio de GitHub de Azure Data Factory oficial](https://github.com/Azure/Azure-DataFactory/tree/master/templates). Las plantillas predefinidas proporcionadas por Microsoft incluyen, pero no se limitan, a los siguientes elementos:

-   Plantillas de copia:

    -   [Copia masiva desde base de datos](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copia de archivos nuevos por LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copiar varios contenedores de archivos entre almacenes basados en archivos](solution-template-copy-files-multiple-containers.md)

    -   [Copia diferencial desde base de datos](solution-template-delta-copy-with-control-table.md)

    -   Copiar de \<origen\> a \<destino\>

        -   De Amazon S3 a Azure Data Lake Store Gen 2

        -   De Google Big Query a Azure Data Lake Store Gen 2

        -   De HDF a Azure Data Lake Store Gen 2

        -   De Netezza a Azure Data Lake Store Gen 1

        -   De SQL Server local a Azure SQL Database

        -   De SQL Server local a Azure SQL Data Warehouse

        -   De Oracle local a Azure SQL Data Warehouse

-   Plantillas de SSIS

    -   Programación de Azure-SSIS Integration Runtime para ejecutar paquetes SSIS

-   Plantillas de transformación

    -   [ETL con Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Mis plantillas

También puede guardar una canalización como plantilla seleccionando **Guardar como plantilla** en la pestaña de la canalización.

![Guardar la canalización como plantilla](media/solution-templates-introduction/templates-intro-image4.png)

Puede ver las canalizaciones guardadas como plantillas en la sección **Mis plantillas** de la galería de plantillas. También puede verlas en la sección **Plantillas** del Explorador de recursos.

![Mis plantillas](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Para usar la característica Mis plantillas, tendrá que habilitar la integración de GIT. Se admiten Azure DevOps GIT y GitHub.
