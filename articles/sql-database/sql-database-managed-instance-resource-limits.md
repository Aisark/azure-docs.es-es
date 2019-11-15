---
title: 'Límites de recursos: instancia administrada'
description: En este artículo se proporciona información general de los límites de recursos de Azure SQL Database para las instancias administradas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 10/02/2019
ms.openlocfilehash: 08e378c4b5f6d8e17ac67a80401edf69611c0a13
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823332"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Introducción a los límites de recursos de instancia administrada de Azure SQL Database

En este artículo se proporciona una introducción a las características técnicas y los límites de recursos de instancia administrada de Azure SQL Database e información sobre cómo solicitar aumentar estos límites.

> [!NOTE]
> Para conocer las diferencias en las características e instrucciones T-SQL admitidas, consulte las instrucciones [Diferencias entre las características](sql-database-features.md) y [Compatibilidad con instrucciones T-SQL](sql-database-managed-instance-transact-sql-information.md). Para ver las diferencias generales entre los niveles de servicio de una base de datos única y una instancia administrada, consulte [Comparación de niveles de servicio](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Características de la generación de hardware

Instancia administrada tiene características y límites de recursos que dependen de la infraestructura y la arquitectura subyacentes. Instancia administrada de Azure SQL Database puede implementarse en dos generaciones de hardware: Gen4 y Gen5. Las generaciones de hardware tienen diferentes características, que se describen en la tabla siguiente:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Procesadores Intel E5-2673 v3 (Haswell) de 2,4 GHz; núcleo virtual SSD conectado equivalente a 1 PP (núcleo físico) | Procesadores Intel E5-2673 v4 (Broadwell) de 2,3 GHz; SSD NVMe rápido, núcleo virtual equivalente a 1 LP (Hyper-Threading) |
| Número de núcleos virtuales | 8, 16, 24 núcleos virtuales | 4, 8, 16, 24, 32, 40, 64 y 80 núcleos virtuales |
| Memoria máxima (relación memoria/núcleo) | 7 GB por núcleo virtual<br/>Agregue más núcleos virtuales para obtener más memoria. | 5,1 GB por núcleo virtual<br/>Agregue más núcleos virtuales para obtener más memoria. |
| Memoria máxima de OLTP en memoria | Límite de instancia: 1-1,5 GB por núcleo virtual| Límite de instancia: 0,8-1,65 GB por núcleo virtual |
| Número máximo de almacenamiento reservado de instancia |  Uso general: 8 TB<br/>Crítico para la empresa: 1 TB | Uso general: 8 TB<br/> Crítico para la empresa: 1 TB, 2 TB o 4 TB, en función del número de núcleos |

> [!IMPORTANT]
> - El hardware de Gen4 está en proceso de eliminación gradual. Se recomienda implementar nuevas instancias administradas en el hardware de Gen5.
> - El hardware de Gen4 en este momento solo sigue disponible en las siguientes regiones: Norte de Europa, Europa Occidental, Este de EE. UU., Centro-sur de EE. UU., Centro-norte de EE. UU., Oeste de EE. UU. 2, Centro de EE. UU., Centro de Canadá, India del Sur, Sudeste Asiático y Centro de Corea.

### <a name="in-memory-oltp-available-space"></a>Espacio disponible de OLTP en memoria 

La cantidad de espacio OLTP en memoria en el nivel de servicio [Crítico para la empresa](sql-database-service-tier-business-critical.md) depende del número de núcleos virtuales y de la generación de hardware. En la tabla siguiente se muestran los límites de memoria que se pueden usar para los objetos OLTP en memoria.

| Espacio OLTP en memoria  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 núcleos virtuales  | 3,14 GB | |   
| 8 núcleos virtuales  | 6,28 GB | 8 GB |
| 16 núcleos virtuales | 15,77 GB | 20 GB |
| 24 núcleos virtuales | 25,25 GB | 36 GB |
| 32 núcleos virtuales | 37,94 GB | |
| 40 núcleos virtuales | 52,23 GB | |
| 64 núcleos virtuales | 99,9 GB    | |
| 80 núcleos virtuales | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Características del nivel de servicios

Instancia administrada tiene dos niveles de servicio: [De uso general](sql-database-service-tier-general-purpose.md) y [Crítico para la empresa](sql-database-service-tier-business-critical.md). Estos niveles proporcionan [funcionalidades diferentes](sql-database-service-tiers-general-purpose-business-critical.md), como se describe en la tabla siguiente.

> [!Important]
> El nivel de servicio Crítico para la empresa proporciona una copia integrada adicional de la instancia (réplica secundaria) que se puede usar para la carga de trabajo de solo lectura. Si puede separar las consultas de lectura-escritura y las consultas de solo lectura, análisis e informes, tendrá el doble de núcleos virtuales y memoria por el mismo precio. La réplica secundaria puede retrasarse unos segundos tras la instancia principal, por lo que está diseñada para descargar la carga de trabajo de informes o análisis que no necesita el estado exacto de los datos. En la tabla siguiente, las **consultas de solo lectura** son las consultas que se ejecutan en la réplica secundaria.

| **Característica** | **Uso general** | **Crítico para la empresa** |
| --- | --- | --- |
| Número de núcleos virtuales\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64 y 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64 y 80 <br/>\*Se dedica el mismo número de núcleos virtuales a consultas de solo lectura. |
| Memoria máxima | Gen4: 56 GB - 168 GB (7 GB/núcleo virtual)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/núcleo virtual)<br/>Agregue más núcleos virtuales para obtener más memoria. | Gen4: 56 GB - 168 GB (7 GB/núcleo virtual)<br/>Gen5: 20,4 GB - 408 GB (5,1 GB/núcleo virtual) para consultas de lectura-escritura<br/>+ 20,4 GB - 408 GB (5,1 GB/núcleo virtual) adicionales para consultas de solo lectura.<br/>Agregue más núcleos virtuales para obtener más memoria. |
| Tamaño máximo de almacenamiento de instancia (reservado) | - 2 TB para 4 núcleos virtuales (solo para Gen5)<br/>- 8 TB para otros tamaños | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB para 4, 8 y 16 núcleos virtuales<br/>- 2 TB para 24 núcleos virtuales<br/>- 4 TB para 32, 40, 64 y 80 núcleos virtuales |
| Tamaño máximo de base de datos | Hasta el tamaño de instancia disponible actualmente (máximo 2 TB - 8 TB, según el número de núcleos virtuales). | Hasta el tamaño de instancia disponible actualmente (máximo 1 TB - 4 TB, según el número de núcleos virtuales). |
| Tamaño máximo de tempDB | Limitado a 24 GB/núcleo virtual (96 - 1,920 GB) y el tamaño de almacenamiento de instancia disponible actualmente.<br/>Agregue más núcleos virtuales para obtener más espacio para TempDB.<br/> El tamaño del archivo de registro está limitado a 120 GB.| Hasta el tamaño de almacenamiento de instancia disponible actualmente. |
| Número máximo de bases de datos por instancia | 100, a menos que se alcance el límite del tamaño de almacenamiento de la instancia. | 100, a menos que se alcance el límite del tamaño de almacenamiento de la instancia. |
| Número máximo de archivos de base de datos por instancia | Hasta 280, a menos que se alcance el límite de tamaño de almacenamiento de instancia o [espacio de almacenamiento de Azure Premium Disk Storage](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | 32 767 archivos por base de datos, a menos que se alcance el límite del tamaño de almacenamiento de la instancia. |
| Tamaño máximo del archivo de datos | Limitado al tamaño de almacenamiento de instancia disponible actualmente (máximo 2 TB - 8 TB) y el [espacio de asignación Azure Premium Disk Storage](sql-database-managed-instance-transact-sql-information.md#exceeding-storage-space-with-small-database-files). | Limitado al tamaño de almacenamiento de instancias disponible actualmente (hasta 1 TB - 4 TB). |
| Tamaño máximo del archivo de registro | Limitado a 2 TB y el tamaño de almacenamiento de instancias disponible actualmente. | Limitado a 2 TB y el tamaño de almacenamiento de instancias disponible actualmente. |
| Datos/IOPS de registro (aproximado) | Hasta 30 000 - 40 000 IOPS por instancia*, 500 - 7500 por archivo<br/>\*[Aumentar el tamaño del archivo para obtener más IOPS](#file-io-characteristics-in-general-purpose-tier)| 5500 - 110 000 (1375 IOPS/núcleo virtual)<br/>Agregue más núcleos virtuales para obtener un mejor rendimiento de E/S. |
| Límite de rendimiento de escritura en el registro (por instancia) | 3 MB/s por núcleo virtual<br/>Máx. 22 MB/s | 4 MB/s por núcleo virtual<br/>Máx. 48 MB/s |
| Rendimiento de datos (aproximado) | 100 - 250 MB/s por archivo<br/>\*[Aumentar el tamaño del archivo para mejorar el rendimiento de E/S](#file-io-characteristics-in-general-purpose-tier) | Sin limitación. |
| Latencia de E/S de almacenamiento (aproximada) | 5-10 ms | 1-2 ms |
| OLTP en memoria (optimización en memoria | No compatible | Disponible, [el tamaño depende del número de núcleos virtuales](#in-memory-oltp-available-space) |
| Número máximo de sesiones | 30000 | 30000 |
| [Réplicas de solo lectura](sql-database-read-scale-out.md) | 0 | 1 (incluida en el precio) |

> [!NOTE]
> - El **tamaño de almacenamiento de instancias disponible actualmente** es la diferencia entre el tamaño de instancia reservada y el espacio de almacenamiento usado.
> - Tanto los datos como el tamaño de archivo de registro en las bases de datos del usuario y las del sistema se incluyen en el tamaño de almacenamiento de la instancia que se compara con el límite de tamaño de almacenamiento máximo. Utilice la vista del sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> para determinar el espacio total utilizado por las bases de datos. Los registros de errores no se mantienen y no se incluyen en el tamaño. Las copias de seguridad no se incluyen en el tamaño del almacenamiento.
> - El rendimiento e IOPS en el nivel de uso general también dependen del [tamaño de página](#file-io-characteristics-in-general-purpose-tier), que no está limitado explícitamente por la instancia administrada.
> - Puede crear otra réplica legible en diferentes regiones de Azure mediante grupos de conmutación por error automática.
> - El número máximo de IOPS por instancia depende del diseño del archivo y la distribución de la carga de trabajo. Por ejemplo, si crea siete archivos de 1 GB con un máximo de 5000 IOPS cada uno y siete archivos pequeños (menos de 128 GB) con 500 IOPS cada uno, puede obtener 38 500 IOPS por instancia (7 x 5000 + 7 x 500) si la carga de trabajo puede usar todos los archivos. Tenga en cuenta que también se usa una determinada cantidad de IOPS para las copias de seguridad automáticas.

> [!NOTE]
> Encuentre más información sobre los [límites de recursos en grupos de instancias administradas en este artículo](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Características de E/S de archivos en el nivel De uso general

En el nivel de servicio De uso general, cada archivo de base de datos obtiene IOPS y rendimiento dedicados que dependen del tamaño del archivo. Los archivos más grandes obtienen más IOPS y capacidad de rendimiento. En la tabla siguiente se muestran las características de E/S de los archivos de base de datos:

| Tamaño de archivo           | 0 - 128 GiB | 128 - 256 GiB | 256 - 512 GiB | 0,5 - 1 TiB    | 1 - 2 TiB    | 2 - 4 TiB | 4 - 8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS por archivo       | 500   | 1100 | 2300              | 5000              | 7500              | 7500              | 12 500   |
| Rendimiento por archivo | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Si observa una latencia de E/S alta en algún archivo de base de datos o que se va a alcanzar el límite de IOPS/rendimiento, puede mejorar el rendimiento si [aumenta el tamaño de archivo](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

También hay límites de nivel de instancia, como el rendimiento de escritura de registro máximo de 22 MB/s, por lo que es posible que no pueda acceder al archivo en el archivo de registro porque se está alcanzando el límite de rendimiento de la instancia.

## <a name="supported-regions"></a>Regiones admitidas

Las instancias administradas solo se pueden crear en las [regiones admitidas](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para crear una instancia administrada en una región que no se admita actualmente, puede [enviar una solicitud de soporte técnico a través de Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>Tipos de suscripciones admitidos

Actualmente, instancia administrada admite la implementación solo en los siguientes tipos de suscripciones:

- [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pago por uso](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Proveedor de nube (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Desarrollo/pruebas - Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Desarrollo/pruebas - Pago por uso](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Suscripciones con crédito mensual de Azure para suscriptores de Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitaciones de recursos regionales

Los tipos de suscripción compatibles pueden contener un número limitado de recursos por región. La instancia administrada tiene dos límites predeterminados por región de Azure (que se pueden aumentar a petición mediante la creación de una [solicitud de soporte técnico especial en Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance)), dependiendo de un tipo de suscripción:

- **Límite de subred**: el número máximo de subredes en que se implementan instancias administradas en una sola región.
- **Límite de unidades de núcleos virtuales**: el número máximo de unidades de núcleo virtual que se pueden implementar en todas las instancias en una sola región. Un núcleo virtual de GP usa una unidad de núcleo virtual y un núcleo virtual de BC usa 4 unidades de núcleo virtual. El número total de instancias no está limitado, siempre que se encuentre dentro del límite de unidades de núcleo virtual.

> [!Note]
> Estos límites son opciones de configuración predeterminadas y no limitaciones técnicas. Los límites se pueden aumentar a petición mediante la creación de una [solicitud de soporte técnico especial en Azure Portal](#obtaining-a-larger-quota-for-sql-managed-instance) si necesita más instancias administradas en la región actual. Como alternativa, puede crear nuevas instancias administradas en otra región de Azure sin necesidad de enviar solicitudes de soporte técnico.

En la tabla siguiente se muestran los **límites regionales predeterminados** de los tipos de suscripción admitidos (estos límites se pueden ampliar mediante la solicitud de soporte técnico que se describe a continuación):

|Tipo de suscripción| Número máximo de subredes de instancia administrada | Número máximo de unidades de núcleo virtual* |
| :---| :--- | :--- |
|Pago por uso|3|320|
|CSP |8 (15 en algunas regiones**)|960 (1440 en algunas regiones**)|
|Desarrollo/pruebas - Pago por uso|3|320|
|Desarrollo/pruebas - Enterprise|3|320|
|EA|8 (15 en algunas regiones**)|960 (1440 en algunas regiones**)|
|Visual Studio Enterprise|2 |64|
|Visual Studio Professional y plataformas de MSDN|2|32|

\* En el planeamiento de las implementaciones, tenga en cuenta que el nivel de servicio Crítico para la empresa (BC) requiere cuatro (4) veces más capacidad de núcleo virtual que el nivel de servicio De uso general (GP). Por ejemplo:  1 núcleo virtual de GP = 1 unidad de núcleo virtual y 1 núcleo virtual de BC = 4 unidades de núcleo virtual. Para simplificar el análisis de consumo frente a los límites predeterminados, resuma las unidades de núcleo virtual de todas las subredes de la región en la que se implementan instancias administradas y compare los resultados con los límites de la unidad de instancia del tipo de suscripción. El límite **Número máximo de unidades de núcleo virtual** se aplica a cada suscripción en una región. No hay ningún límite por subredes individuales, salvo que la suma de todos los núcleos virtuales implementados en varias subredes debe ser inferior o igual al **número máximo de unidades de núcleo virtual**.

\*\* En las regiones siguientes hay más límites de subred y núcleo virtual: Este de Australia, Este de EE. UU., Este de EE. UU. 2, Norte de Europa, Centro-sur de EE. UU., Sudeste Asiático, Sur de Reino Unido, Oeste de Europa, Oeste de EE. UU. 2.

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Obtención de una cuota mayor para instancia administrada de SQL

Si necesita más instancias administradas en sus regiones actuales, puede enviar una solicitud de soporte técnico para ampliar la cuota a través de Azure Portal.
Para iniciar el proceso de obtención de una cuota mayor:

1. Abra **Ayuda y soporte técnico** y haga clic en **Nueva solicitud de soporte técnico**.

   ![Ayuda y soporte técnico](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. En la pestaña Conceptos básicos de la nueva solicitud de soporte técnico:
   - En **Tipo de problema**, seleccione **Límites de servicio y suscripción (cuotas)** .
   - En **Suscripción**, seleccione la suscripción.
   - En **Tipo de cuota**, seleccione **Instancia administrada de SQL Database**.
   - En **Plan de soporte técnico**, seleccione un plan de soporte técnico.

     ![Cuota de tipo de problema](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Haga clic en **Next**.
4. En la **pestaña Problema** de la nueva solicitud de soporte técnico:
   - En **Gravedad**, seleccione el nivel de gravedad del problema.
   - En **Detalles**, especifique información adicional acerca del problema, lo que incluye los mensajes de error.
   - En **Carga de archivos**, adjunte un archivo con más información (hasta 4 MB).

     ![Detalles del problema](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Una solicitud válida debe incluir:
     > - Región en la que hay que aumentar el límite de la suscripción.
     > - Número requerido de núcleos virtuales, por nivel de servicio en las subredes existentes después del aumento de la cuota (si cualquiera de las subredes existentes debe expandirse).
     > - Número requerido de nuevas subredes y número total de núcleos virtuales por nivel de servicio dentro de las nuevas subredes (si tiene que implementar instancias administradas en nuevas subredes).

5. Haga clic en **Next**.
6. En la pestaña Información de contacto de la nueva solicitud de soporte técnico, especifique el método de contacto preferido (teléfono o correo electrónico) y los detalles de contacto.
7. Haga clic en **Create**(Crear).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información sobre instancia administrada, consulte [¿Qué es una instancia administrada?](sql-database-managed-instance.md)
- Para obtener información de precios, vea [Precios de Instancia administrada de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para obtener información sobre cómo crear su primera instancia administrada, consulte la [Guía de inicio rápido](sql-database-managed-instance-get-started.md).
