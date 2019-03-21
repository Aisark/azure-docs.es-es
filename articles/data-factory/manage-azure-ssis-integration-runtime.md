---
title: Reconfiguración de un entorno de ejecución para la integración de SSIS en Azure | Microsoft Docs
description: Aprenda a reconfigurar una instancia de Integration Runtime de SSIS de Azure en Azure Data Factory después de haberlo aprovisionado.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 3c1178a20debc36fbdbbd374eaf9adb6005a93a7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454951"
---
# <a name="reconfigure-the-azure-ssis-integration-runtime"></a>Reconfiguración de un entorno de ejecución para la integración de SSIS en Azure
En este artículo se describe cómo reconfigurar un entorno de ejecución existente para la integración de SSIS en Azure. Para crear una instancia de Integration Runtime (IR) para la integración de SSIS en Azure Data Factory, vea [Creación de una instancia de Integration Runtime para la integración de SSIS en Azure](create-azure-ssis-integration-runtime.md).  

## <a name="data-factory-ui"></a>Interfaz de usuario de Data Factory 
Puede usar la interfaz de usuario de Data Factory para detener, editar y volver a configurar o eliminar un entorno de ejecución de integración de SSIS de Azure. 

1. En la **interfaz de usuario de Data Factory**, cambie a la pestaña **Edit** (Editar). Para iniciar la interfaz de usuario de Data Factory, haga clic en **Author & Monitor** (Creación y supervisión) en la página principal de su factoría de datos.
2. En el panel izquierdo, haga clic en **Connections** (Conexiones).
3. En el panel derecho, cambie a **Integration Runtimes** (Entornos de ejecución de integración). 
4. Puede usar los botones de la columna Actions (Acciones) para **detener**, **editar**, o **eliminar** el entorno de ejecución de integración. El botón **Code** (Código) de la columna **Actions** (Acciones) le permite ver la definición de JSON asociada con el entorno de ejecución de integración.  
    
    ![Acciones para entornos de ejecución de integración de SSIS de Azure](./media/manage-azure-ssis-integration-runtime/actions-for-azure-ssis-ir.png)

### <a name="to-reconfigure-an-azure-ssis-ir"></a>Para reconfigurar un entorno de ejecución de integración de SSIS de Azure
1. Haga clic en **Stop** (Detener) en la columna **Actions** (Acciones) para detener el entorno de ejecución de integración. Para actualizar la vista de lista, haga clic en **Refresh** (Actualizar) en la barra de herramientas. Una vez detenida la instancia de IR, verá que la primera acción le permite iniciar el entorno de ejecución de integración. 

    ![Acciones para entornos de ejecución de integración de SSIS de Azure después de la detención](./media/manage-azure-ssis-integration-runtime/actions-after-ssis-ir-stopped.png)
2. Edite o reconfigure el entorno de ejecución de integración, para lo que debe hacer clic en el botón **Edit** (Editar) de la columna **Actions** (Acciones). En la ventana **Integration Runtime Setup** (Configuración del entorno de ejecución), cambie la configuración (por ejemplo, el tamaño del nodo, el número de nodos o el máximo de ejecuciones en paralelo por nodo). 
3. Para reiniciar el entorno de ejecución de integración, haga clic en el botón **Start** (Start) de la columna **Actions** (Acciones).     

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Después de aprovisionar e iniciar una instancia de Integration Runtime de SSIS de Azure, puede volver a configurarla mediante la ejecución de una secuencia de los cmdlets `Stop` - `Set` - `Start` de PowerShell de forma consecutiva. Por ejemplo, el siguiente script de PowerShell cambia el número de nodos asignados a la instancia de Integration Runtime de SSIS de Azure a cinco.

### <a name="reconfigure-an-azure-ssis-ir"></a>Reconfigurar una instancia de IR de SSIS de Azure

1. En primer lugar, detenga el tiempo de ejecución de integración de SSIS de Azure mediante el [Stop AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/stop-Azdatafactoryv2integrationruntime) cmdlet. Este comando libera todos sus nodos y detiene la facturación.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName 
    ```
2. A continuación, volver a configurar la integración de SSIS en Azure mediante el [conjunto AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/set-Azdatafactoryv2integrationruntime) cmdlet. El siguiente comando de ejemplo permite escalar una instancia de Integration Runtime de SSIS de Azure a cinco nodos.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -NodeCount 5
    ```  
3. A continuación, inicie el tiempo de ejecución de integración de SSIS de Azure con el [inicio AzDataFactoryV2IntegrationRuntime](/powershell/module/az.datafactory/start-Azdatafactoryv2integrationruntime) cmdlet. Este comando asigna todos sus nodos para ejecutar paquetes SSIS.   

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName
    ```

### <a name="delete-an-azure-ssis-ir"></a>Eliminar una instancia de IR de SSIS de Azure
1. En primer lugar, cree una lista de todas las instancias de IR de SSIS de Azure en la factoría de datos.

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName -Status
    ```
2. A continuación, detenga todas las instancias de IR de SSIS de Azure existentes en la factoría de datos.

    ```powershell
    Stop-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
3. A continuación, quite todas las instancias de IR de SSIS de Azure existentes en la factoría de datos de una en una.

    ```powershell
    Remove-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Force
    ```
4. Por último, quite la factoría de datos.

    ```powershell
    Remove-AzDataFactoryV2 -Name $DataFactoryName -ResourceGroupName $ResourceGroupName -Force
    ```
5. Si ha creado un nuevo grupo de recursos, quite el grupo de recursos.

    ```powershell
    Remove-AzResourceGroup -Name $ResourceGroupName -Force 
    ```

## <a name="next-steps"></a>Pasos siguientes
Consulte los siguientes temas para más información sobre Integration Runtime de SSIS de Azure: 

- [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime). En este artículo se proporciona información conceptual acerca de Integration Runtime en general, lo que incluye Integration Runtime de SSIS de Azure. 
- [Tutorial: Implementación de paquetes SSIS en Azure](tutorial-create-azure-ssis-runtime-portal.md). En este artículo se proporcionan instrucciones paso a paso para crear una instancia de IR de SSIS de Azure y se usa una base de datos de SQL Azure para hospedar el catálogo de SSIS. 
- [Instrucciones: Creación de una instancia de Integration Runtime de SSIS de Azure](create-azure-ssis-integration-runtime.md). Este artículo amplía el tutorial y proporciona instrucciones acerca del uso de Instancia administrada de Azure SQL Database y de la combinación de Integration Runtime con una red virtual. 
- [Unión de una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual](join-azure-ssis-integration-runtime-virtual-network.md). En este artículo se ofrece información conceptual sobre cómo unir una instancia de Integration Runtime para la integración de SSIS en Azure a una red virtual de Azure. También se proporcionan los pasos para configurar la red virtual mediante Azure Portal para que se una la instancia de Integration Runtime de SSIS de Azure. 
- [Monitor an Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime) (Supervisión de una instancia de Integration Runtime de SSIS de Azure). En este artículo se muestra cómo recuperar información sobre una instancia de IR de SSIS de Azure, junto con descripciones de los estados en la información devuelta. 
 
