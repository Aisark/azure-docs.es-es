---
title: Copia de datos en Azure Data Lake Storage Gen2 con DistCp | Microsoft Docs
description: Uso de la herramienta DistCp para copiar datos hacia y desde Data Lake Storage Gen2
services: storage
author: seguler
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: seguler
ms.openlocfilehash: 3b58dc8dabc55ba428ce6e35091a6947e5f4a824
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "61481809"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-azure-data-lake-storage-gen2"></a>Uso de DistCp para copiar datos entre Azure Storage Blob y Azure Data Lake Storage Gen2

Puede usar [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) para copiar datos entre una cuenta de almacenamiento de uso general V2 y una cuenta de almacenamiento de uso general V2 con el espacio de nombres jerárquico habilitado. En este artículo se proporcionan instrucciones sobre cómo usar la herramienta DistCp.

DistCp proporciona una variedad de parámetros de línea de comandos y se recomienda encarecidamente leer este artículo para optimizar el uso de la herramienta. En este artículo se explica la funcionalidad básica, al mismo tiempo que se centra en su uso para copiar datos a una cuenta con el espacio de nombres jerárquico habilitado.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* **Una cuenta existente de Azure Storage sin las funcionalidades de Data Lake Storage Gen2 habilitadas, como el espacio de nombres jerárquico**.
* **Una cuenta de Azure Storage con la característica de Data Lake Storage Gen2 habilitada**. Para obtener instrucciones para crear una, consulte [Creación de una cuenta de almacenamiento de Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Un sistema de archivos** creado en la cuenta de almacenamiento con el espacio de nombres jerárquico habilitado.
* **Clúster de Azure HDInsight** con acceso a una cuenta de almacenamiento con Data Lake Storage Gen2 habilitado. Consulte [Uso de Data Lake Storage Gen2 con clústeres de Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Asegúrese de habilitar el Escritorio remoto para el clúster.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Usar DistCp desde un clúster de HDInsight de Linux

Un clúster de HDInsight incluye la utilidad DistCp, que puede utilizarse para copiar datos de orígenes diferentes en un clúster de HDInsight. Si ha configurado el clúster de HDInsight para usar conjuntamente Azure Blob Storage y Azure Data Lake Storage, la utilidad DistCp puede utilizarse también directamente para copiar datos entre ellos también. En esta sección veremos cómo usar la utilidad DistCp.

1. Cree una sesión SSH en el clúster de HDI. Consulte [Conexión a un clúster de HDInsight basado en Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

2. Compruebe si puede acceder a la cuenta existente de uso general V2 (sin el espacio de nombres jerárquico habilitado).

        hdfs dfs –ls wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/

    La salida debería proporcionar una lista de contenido en el contenedor.

3. De forma similar, compruebe si puede acceder a la cuenta de almacenamiento con el espacio de nombres jerárquico habilitado desde el clúster. Ejecute el siguiente comando:

        hdfs dfs -ls abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/

    La salida debería proporcionar una lista de archivos o carpetas en la cuenta de Data Lake Storage.

4. Utilice DistCp para copiar datos desde WASB a una cuenta de Data Lake Storage.

        hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder

    El comando copia el contenido de la carpeta **/example/data/gutenberg/** de Blob Storage a **/myfolder** en la cuenta de Data Lake Storage.

5. Asimismo, utilice DistCp para copiar datos de la cuenta de Data Lake Storage a Blob Storage (WASB).

        hadoop distcp abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg

    El comando copia el contenido de **/myfolder** de la cuenta de Data Lake Store en la carpeta **/example/data/gutenberg/** de WASB.

## <a name="performance-considerations-while-using-distcp"></a>Consideraciones de rendimiento sobre el uso de DistCp

Dado que la granularidad más baja de DistCp es un único archivo, configurar el número máximo de copias simultáneas es el parámetro más importante para optimizar con respecto a Data Lake Storage. El número de copias simultáneas es igual al número de parámetros de asignador (**m**) en la línea de comandos. Este parámetro especifica el número máximo de mapeadores que se usan para copiar los datos. El valor predeterminado es 20.

**Ejemplo**

    hadoop distcp wasbs://<CONTAINER_NAME>@<STORAGE_ACCOUNT_NAME>.blob.core.windows.net/example/data/gutenberg abfss://<FILE_SYSTEM_NAME>@<STORAGE_ACCOUNT_NAME>.dfs.core.windows.net/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>¿Cómo se puede determinar el número de asignadores que se debe usar?

A continuación hay algunas instrucciones que puede usar.

* **Paso 1: Determinar la memoria total disponible para la cola de aplicación YARN "predeterminada"**: el primer paso consiste en determinar la memoria disponible para la cola de aplicación YARN "predeterminada". Esta información está disponible en el portal de Ambari asociado con el clúster. Vaya a YARN y haga clic en la pestaña de configuración para ver la memoria YARN disponible para la cola de aplicación "predeterminada". Se trata de la memoria total disponible para el trabajo DistCp (que es realmente un trabajo de MapReduce).

* **Paso 2: Cálculo del número de mapeadores**: el valor de **m** es igual al cociente de la memoria de YARN total dividido por el tamaño del contenedor de YARN. La información del tamaño de contenedor de YARN está también disponible en el portal del Ambari. Vaya a YARN y examine la pestaña de configuración. En esta ventana se muestra el tamaño del contenedor de YARN. La ecuación para llegar al número de asignadores (**m**) es

        m = (number of nodes * YARN memory for each node) / YARN container size

**Ejemplo**

Supongamos que tiene un clúster 4x D14v2s y que intenta transferir 10 TB de datos desde 10 carpetas diferentes. Cada una de las carpetas contiene diferentes cantidades de datos y los tamaños de archivo dentro de cada carpeta son diferentes.

* **Memoria de YARN total**: en el portal de Ambari determinará que la memoria de YARN es de 96 GB para un nodo D14. Por lo tanto, la memoria de YARN total para el clúster de cuatro nodos es: 

        YARN memory = 4 * 96GB = 384GB

* **Número de mapeadores**: en el portal de Ambari determinará que el tamaño del contenedor de YARN es 3072 MB para un nodo de clúster D14. Por lo tanto, el número de asignadores es:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Si otras aplicaciones usan memoria, solo puede elegir usar una parte de la memoria de YARN del clúster para DistCp.

### <a name="copying-large-datasets"></a>Copia de grandes conjuntos de datos

Cuando el tamaño del conjunto de datos que se va a mover es grande (por ejemplo, >1 TB) o si tiene muchas carpetas diferentes, debe considerar el uso de varios trabajos de DistCp. Aunque es probable que no haya ganancia de rendimiento, los trabajos se reparten para que si alguno da error, solo sea necesario reiniciar ese trabajo específico en lugar del trabajo entero.

### <a name="limitations"></a>Limitaciones

* DistCp intenta crear a asignadores que tengan un tamaño similar para optimizar el rendimiento. El aumento del número de asignadores no siempre aumenta el rendimiento.

* DistCp está limitado a solo un asignador por archivo. Por lo tanto, no debería tener más asignadores que archivos. Como DistCp solo puede asignar a un asignador a un archivo, esto limita la cantidad de simultaneidad que puede usarse para copiar archivos de gran tamaño.

* Si tiene un número pequeño de archivos grandes, debe dividirlos en fragmentos de archivo de 256 MB para ofrecerle una mayor simultaneidad en potencia.
