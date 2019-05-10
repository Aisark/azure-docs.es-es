---
title: Configuración del rendimiento de una instancia de Integration Runtime para la integración de SSIS en Azure | Microsoft Docs
description: Aprenda a configurar las propiedades de Integration Runtime de SSIS de Azure para conseguir un alto rendimiento
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 42c69653a002446552da998320a43730dfdaadf5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232552"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>Configuración de Integration Runtime de SSIS de Azure para conseguir un alto rendimiento

En este artículo se describe cómo configurar una instancia de Integration Runtime (IR) de SSIS de Azure para conseguir un alto rendimiento. IR de SSIS de Azure le permite implementar y ejecutar paquetes de SQL Server Integration Services (SSIS) en Azure. Para más información acerca de IR de SSIS de Azure, consulte el artículo [Integration Runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Para información sobre la implementación y la ejecución de paquetes SSIS en Azure, consulte [Levantar y mover las cargas de trabajo de SQL Server Integration Services a la nube](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview).

> [!IMPORTANT]
> Este artículo contiene los resultados de rendimiento y las observaciones de pruebas internas que han realizado los miembros del equipo de desarrollo de SSIS. Los resultados pueden variar. Realice sus propias pruebas antes de terminar su configuración, ya que esto afectará al costo y al rendimiento.

## <a name="properties-to-configure"></a>Propiedades para configurar

La parte siguiente de un script de configuración muestra las propiedades que puede configurar al crear una instancia de Integration Runtime de SSIS de Azure. Si quiere ver el script completo de PowerShell y una descripción, consulte [Implementación de paquetes de SSIS en Azure](tutorial-deploy-ssis-packages-azure-powershell.md).

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSISLocation
**AzureSSISLocation** es la ubicación del nodo de trabajo de Integration Runtime. El nodo de trabajo mantiene una conexión constante a la base de datos de catálogo de SSIS (SSISDB) en una base de datos de Azure SQL. Establezca **AzureSSISLocation** en la misma ubicación que el servidor de SQL Database que hospeda SSISDB, ya que esto permite que Integration Runtime funcione de la manera más eficaz posible.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, que incluye IR de SSIS de Azure, admite las siguientes opciones:
-   Estándar\_A4\_v2
-   Estándar\_A8\_v2
-   Estándar\_D1\_v2
-   Estándar\_D2\_v2
-   Estándar\_D3\_v2
-   Estándar\_D4\_v2
-   Standard\_D2\_v3
-   Standard\_D4\_v3
-   Standard\_D8\_v3
-   Estándar\_D16\_v3
-   Standard\_D32\_v3
-   Standard\_D64\_v3
-   Standard\_E2\_v3
-   Standard\_E4\_v3
-   Standard\_E8\_v3
-   Standard\_E16\_v3
-   Standard\_E32\_v3
-   Standard\_E64\_v3

En las pruebas internas no oficiales llevadas a cabo por el equipo de ingeniería de SSIS, la serie D parece ser más adecuada para la ejecución de paquetes SSIS que la serie A.

-   La relación precio/rendimiento de la serie D es mayor que la serie y la relación precio/rendimiento de la serie v3 es mayor que la serie de v2.
-   El rendimiento de la serie D es mayor que la serie al mismo precio y el rendimiento de la serie v3 es mayor que la serie de v2 al mismo precio.
-   Los nodos de la serie de v2 de Azure-SSIS IR no son adecuados para la instalación personalizada, por lo que use en su lugar, los nodos de la serie v3. Si ya usa los nodos de la serie de v2, cambie a los nodos de la serie v3 tan pronto como sea posible.
-   La serie E está con optimización para memoria tamaños de máquina virtual que proporciona una mayor proporción de memoria a CPU que otras máquinas. Si el paquete requiere una gran cantidad de memoria, puede elegir la VM de la serie E.

### <a name="configure-for-execution-speed"></a>Configuración de la velocidad de ejecución
Si no tiene que ejecutar muchos paquetes y quiere que la ejecución se realice rápidamente, use la información del gráfico siguiente para elegir un tipo de máquina virtual adecuado para su escenario.

Estos datos representan la ejecución de un único paquete en un único nodo de trabajo. El paquete de carga de 3 millones de registros con el nombre y columnas de nombre apellido desde Azure Blob Storage, genera una columna de nombre completo y escribe los registros que tienen el nombre completo de más de 20 caracteres en Azure Blob Storage.

![Velocidad de ejecución de paquetes de Integration Runtime de SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configuración del rendimiento global

Si tiene que ejecutar muchos paquetes y lo que más le preocupa es el rendimiento global, use la información del siguiente gráfico para elegir un tipo de máquina virtual adecuado para su escenario.

![Rendimiento global máximo de Integration Runtime de SSIS](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** ajusta la escalabilidad de Integration Runtime. El rendimiento de Integration Runtime es proporcional al valor de **AzureSSISNodeNumber**. Establezca **AzureSSISNodeNumber** en un valor pequeño al principio, supervise el rendimiento de Integration Runtime y, a continuación, ajuste el valor para su escenario. Para volver a configurar el recuento de nodos de trabajo, consulte [Administración de una instancia de Integration Runtime de SSIS de Azure](manage-azure-ssis-integration-runtime.md).

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Si ya está usando un nodo de trabajo de gran potencia para ejecutar los paquetes, el hecho de aumentar **AzureSSISMaxParallelExecutionsPerNode** puede incrementar el rendimiento global de Integration Runtime. En el caso de los nodos Standard_D1_v2, se admiten ejecuciones en paralelo 1-4 por nodo. Para los demás tipos de nodos, se admiten ejecuciones en paralelo 1-max(2 x number of cores, 8) por nodo. Si desea que **AzureSSISMaxParallelExecutionsPerNode** por encima del valor máximo que se admiten, puede abrir una incidencia de soporte técnico y se puede incrementar el valor máximo para usted y después de que necesite usar Azure Powershell para actualizar  **AzureSSISMaxParallelExecutionsPerNode**.
Puede calcular el valor apropiado en función del costo del paquete y de las siguientes configuraciones de los nodos de trabajo. Para más información, consulte [Tamaños de máquina virtual de uso general](../virtual-machines/windows/sizes-general.md).

| Tamaño             | vCPU | Memoria: GiB | GiB de almacenamiento temporal (SSD) | Rendimiento máximo de almacenamiento temporal: IOPS / MBps de lectura / MBps de escritura | Discos de datos máx. / rendimiento: E/S | Nº máx. NIC / rendimiento de red esperado (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Estándar\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2x500                         | 2 / 750                                        |
| Estándar\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4x500                         | 2 / 1500                                       |
| Estándar\_D3\_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 8 / 8x500                         | 4 / 3000                                       |
| Estándar\_D4\_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 16 / 16x500                       | 8 / 6000                                       |
| Estándar\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8x500                         | 4 / 1000                                       |
| Estándar\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16x500                       | 8 / 2000                                       |
| Standard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1000                                       |
| Standard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_D8\_v3 | 8    | 32          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Estándar\_D16\_v3| 16   | 64          | 400                    | 24000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Standard\_D32\_v3| 32   | 128         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30 000                                      |
| Standard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6 x 500                         | 2 / 1000                                       |
| Standard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standard\_E8\_v3 | 8    | 64          | 200                    | 12000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standard\_E16\_v3| 16   | 128         | 400                    | 24000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standard\_E32\_v3| 32   | 256         | 800                    | 48000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30 000                                      |

Estas son las instrucciones para configurar el valor correcto de la propiedad **AzureSSISMaxParallelExecutionsPerNode**: 

1. Establézcala en un valor pequeño en un primer momento.
2. Auméntelo un poco para comprobar si se ha mejorado el rendimiento global.
3. Deje de aumentar el valor cuando el rendimiento global alcance el valor máximo.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** es el plan de tarifa de la base de datos de catálogo de SSIS (SSISDB) en una base de datos SQL de Azure. Esta configuración afecta al número máximo de trabajos en la instancia de IR, a la velocidad para poner en cola una ejecución de paquetes y a la velocidad para cargar el registro de ejecución.

-   Si no le preocupa la velocidad a la que se pone en cola una ejecución de paquetes o se carga el registro de ejecución, puede elegir el plan de tarifa de base de datos más bajo. Azure SQL Database con el plan Básico admite 8 trabajos en una instancia del entorno de ejecución de integración.

-   Si el recuento de trabajos o el recuento de núcleos es superior a 8 o 50, respectivamente, elija una base de datos con una mayor potencia que la básica. En caso contrario, la base de datos se convierte en el cuello de botella de la instancia de Integration Runtime y el rendimiento global se ve afectado de forma negativa.

-   Si el nivel de registro se establece en detallado, elija una base de datos más eficaces, por ejemplo, s3. Según nuestras pruebas internas no oficiales, plan de tarifa s3 puede admitir la ejecución de paquete SSIS con 2 nodos, 128 recuentos paralelas y nivel de registro detallado.

También puede ajustar el plan de tarifa de base de datos en función de la información de uso de la [unidad de transacción de base de datos](../sql-database/sql-database-what-is-a-dtu.md) (DTU) disponible en Azure Portal.

## <a name="design-for-high-performance"></a>Diseño para alto rendimiento
Diseñar un paquete SSIS para que se ejecute en Azure no es lo mismo que diseñar un paquete para la ejecución local. En lugar de combinar varias tareas independientes en el mismo paquete, sepárelas en varios paquetes para una ejecución más eficiente en IR de SSIS de Azure. Cree una ejecución de paquete para cada paquete, así no tiene que esperar a que el otro finalice. Este enfoque se beneficia de la escalabilidad de Integration Runtime de SSIS de Azure y mejora el rendimiento global.

## <a name="next-steps"></a>Pasos siguientes
Aprenda sobre Integration Runtime de SSIS de Azure. Consulte [Integration Runtime de SSIS de Azure](concepts-integration-runtime.md#azure-ssis-integration-runtime).
