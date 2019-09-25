---
title: Azure HDInsight para Visual Studio Code
description: Obtenga información sobre cómo usar las herramientas Spark y Hive (Azure HDInsight) para Visual Studio Code y así poder crear y enviar consultas y scripts.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/30/2019
ms.openlocfilehash: 2b4d588799d72be85030e70aed58b2c8d6b0a9a3
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091749"
---
# <a name="use-spark--hive-tools-for-visual-studio-code"></a>Usar las herramientas Spark y Hive para Visual Studio Code

Aprenda a usar las Herramientas Spark y Hive para Visual Studio Code para crear y enviar trabajos por lotes de Apache Hive, consultas de Hive interactivas y scripts de PySpark para Apache Spark. En primer lugar, vamos a describir cómo instalar Spark & Hive Tools en Visual Studio Code y luego veremos cómo enviar trabajos a Spark & Hive Tools.  

Las herramientas Spark y Hive se pueden instalar en las plataformas compatibles con Visual Studio Code, lo que incluye Windows, Linux y macOS. Observe los siguientes requisitos previos para las distintas plataformas.

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, se requieren los elementos siguientes:

- Un clúster de HDInsight de Azure. Para crear un clúster, vea la [introducción a HDInsight](hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md). O bien, use un clúster de Hive y Spark que admita un punto de conexión de Apache Livy.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Mono](https://www.mono-project.com/docs/getting-started/install/). Mono solo es obligatorio para Linux y macOS.
- [Un entorno interactivo de PySpark para Visual Studio Code](set-up-pyspark-interactive-environment.md).
- Un directorio local llamado **HDexample**.  En este artículo se usa **C:\HD\HDexample**.

## <a name="install-spark--hive-tools"></a>Instalar las herramientas Spark y Hive

Después de completar los requisitos previos, puede instalar Spark & Hive Tools para Visual Studio Code siguiendo estos pasos:

1. Abra Visual Studio Code.

2. En la barra de menús, vaya a **Vista** > **Extensiones**.

3. En el cuadro de búsqueda, escriba **Spark y Hive**.

4. Seleccione **Spark & Hive Tools** en los resultados de búsqueda y seleccione **Instalar**:

   ![Instalación de Spark y Hive para Visual Studio Code Python](./media/hdinsight-for-vscode/install-hdInsight-plugin.png)

5. Seleccione **Recargar** cuando sea necesario.

## <a name="open-a-work-folder"></a>Apertura de una carpeta de trabajo

Para abrir una carpeta de trabajo y crear un archivo en Visual Studio Code, siga estos pasos:

1. En la barra de menús, vaya a **Archivo** > **Abrir carpeta** > **C:\HD\HDexample** y, a continuación, seleccione el botón **Seleccionar carpeta**. La carpeta aparece en la vista **Explorador** a la izquierda.

2. En la vista **Explorador**, seleccione la carpeta, **HDexample** y, a continuación, el icono **Nuevo archivo** situado junto a la carpeta de trabajo:

   ![Icono de archivo nuevo de Visual Studio Code](./media/hdinsight-for-vscode/visual-studio-code-new-file.png)

3. Asigne al nuevo archivo la extensión de archivo `.hql` (consultas de Hive) o `.py` (script de Spark). En este ejemplo se utiliza **HelloWorld.hql**.

## <a name="set-the-azure-environment"></a>Establecimiento del entorno de Azure

Para usuarios de la nube nacional, siga los pasos para configurar el entorno de Azure en primer lugar y, a continuación, use el comando **Azure: Sign In** para iniciar sesión en Azure:

1. Seleccione **Archivo\Preferencias\Configuración**.
2. Busque la siguiente cadena: **Azure: Cloud** (Nube)
3. Seleccione la nube nacional de la lista:

   ![Establecer la configuración de entrada de inicio de sesión predeterminada](./media/hdinsight-for-vscode/set-default-login-entry-configuration.png)

## <a name="connect-to-an-azure-account"></a>Conexión a la cuenta de Azure

Para enviar scripts a clústeres desde Visual Studio Code, debe conectarse a su cuenta de Azure o vincular un clúster (con las credenciales de nombre de usuario y contraseña de Ambari o una cuenta unida a un dominio). Siga estos pasos para conectarse a Azure:

1. En la barra de menús, vaya a **Vista** > **Paleta de comandos** y escriba **Azure: Sign In**:

    ![Usar las herramientas Spark y Hive para el inicio de sesión de Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-extension-login.png)

2. Siga las instrucciones para iniciar sesión en Azure. Una vez establecida la conexión, el nombre de la cuenta de Azure se muestra en la barra de estado en la parte inferior de la ventana de Visual Studio Code.  

## <a name="link-a-cluster"></a>Vinculación de un clúster

### <a name="link-azure-hdinsight"></a>Vínculo: HDInsight de Azure

Puede vincular un clúster normal mediante un nombre de usuario administrado de [Apache Ambari](https://ambari.apache.org/) o vincular un clúster de Hadoop de seguridad de Enterprise Security Pack mediante un nombre de usuario de dominio (como `user1@contoso.com`).

1. En la barra de menús, vaya a **Vista** > **Paleta de comandos** y escriba **Spark / Hive: Link a Cluster** (HDInsight: vincular un clúster).

   ![Paleta de comandos, comando de vinculación de clúster](./media/hdinsight-for-vscode/link-cluster-command.png)

2. Seleccione el tipo de clúster vinculado **Azure HDInsight**.

3. Escriba la dirección URL del clúster de HDInsight.

4. Escriba el nombre de usuario de Ambari; el valor predeterminado es **admin**.

5. Escriba la contraseña de Ambari.

6. Seleccione el tipo de clúster.

7. Establezca el nombre para mostrar del clúster (opcional).

8. Revise la vista **SALIDA** para comprobarlo todo.

   > [!NOTE]  
   > Si el clúster se registró en la suscripción de Azure y se vinculó, se usan el nombre de usuario y la contraseña vinculados.  

### <a name="link-generic-livy-endpoint"></a>Vínculo: punto de conexión de Livy genérico

1. En la barra de menús, vaya a **Vista** > **Paleta de comandos** y escriba **Spark / Hive: Link a Cluster** (HDInsight: vincular un clúster).

2. Seleccione el tipo de clúster vinculado **Generic Livy Endpoint** (Punto de conexión de Livy genérico).

3. Escriba el punto de conexión de Livy genérico. Por ejemplo, http\://10.172.41.42:18080.

4. Seleccione el tipo de autorización **Básica** o **Ninguna**.  Si selecciona **Básico**:  
    &emsp;a. Escriba el nombre de usuario de Ambari; el valor predeterminado es **admin**.  
    &emsp;b. Escriba la contraseña de Ambari.

5. Revise la vista **SALIDA** para comprobarlo todo.

## <a name="list-clusters"></a>Enumeración de clústeres

1. En la barra de menús, vaya a **Vista** > **Paleta de comandos** y escriba **Spark / Hive: List Cluster** (HDInsight: enumeración de clústeres).

2. Seleccione la suscripción que desea.

3. Revise la vista **SALIDA**. Esta vista muestra el clúster vinculado (o los clústeres vinculados) y todos los clústeres de su suscripción de Azure:

    ![Establecer la configuración de un clúster predeterminado](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="set-the-default-cluster"></a>Establecimiento de clúster predeterminado

1. Vuelva a abrir la carpeta **HDexample** que se trató [anteriormente](#open-a-work-folder), en caso de que esté cerrada.  

2. Seleccione el archivo **HelloWorld.hql** que se creó [anteriormente](#open-a-work-folder). Se abre en el editor de scripts.

3. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **Spark / Hive: Set Default Cluster** (HDInsight: establecer el clúster predeterminado).  

4. [Conéctese](#connect-to-an-azure-account) a su cuenta de Azure o vincule un clúster si no lo ha hecho aún.

5. Seleccione un clúster como clúster predeterminado del archivo de script actual. Las herramientas actualizan automáticamente el archivo de configuración **.VSCode\settings.json**:

   ![Establecer la configuración de un clúster predeterminado](./media/hdinsight-for-vscode/set-default-cluster-configuration.png)

## <a name="submit-interactive-hive-queries-and-hive-batch-scripts"></a>Envío de consultas de Hive interactivas y scripts por lotes de Hive

Con Spark & Hive Tools para Visual Studio Code, puede enviar consultas de Hive interactivas y scripts por lotes de Hive a sus clústeres.

1. Vuelva a abrir la carpeta **HDexample** que se trató [anteriormente](#open-a-work-folder), en caso de que esté cerrada.  

2. Seleccione el archivo **HelloWorld.hql** que se creó [anteriormente](#open-a-work-folder). Se abre en el editor de scripts.

3. Copie y pegue el código siguiente en el archivo de Hive y guárdelo:

    ```hiveql
    SELECT * FROM hivesampletable;
    ```

4. [Conéctese](#connect-to-an-azure-account) a su cuenta de Azure o vincule un clúster si no lo ha hecho aún.

5. Haga clic con el botón derecho en el editor de scripts y seleccione **Hive: Interactive** (Hive: Interactivo) para enviar la consulta, o bien use el acceso directo Ctrl+Alt +I.  Seleccione **Hive: Batch** (Hive: Lote) para enviar la consulta, o bien use el acceso directo Ctrl+Alt+H.  

6. Seleccione el clúster si no ha especificado un clúster predeterminado. Las herramientas también permiten enviar un bloque de código en lugar del archivo de script completo mediante el menú contextual. Transcurridos unos instantes, los resultados de la consulta aparecen en una pestaña nueva:

   ![Resultado de consulta interactiva de Apache Hive](./media/hdinsight-for-vscode/interactive-hive-result.png)

    - Panel **RESULTADOS**: Puede guardar todo el resultado en forma de archivo CSV, JSON o de Excel en una ruta de acceso local, o bien seleccionar únicamente varias líneas.

    - Panel **MENSAJES**: Al seleccionar el número de **Línea**, se salta a la primera línea del script en ejecución.

## <a name="submit-interactive-pyspark-queries"></a>Envíe consultas de PySpark interactivas

Para enviar consultas de PySpark interactivas siga estos pasos:

1. Vuelva a abrir la carpeta **HDexample** que se trató [anteriormente](#open-a-work-folder), en caso de que esté cerrada.  

2. Cree un nuevo archivo **HelloWorld.py** siguiendo los pasos [anteriores](#open-a-work-folder).

3. Copie y pegue el código siguiente en el archivo de script:

   ```python
   from operator import add
   lines = spark.read.text("/HdiSamples/HdiSamples/FoodInspectionData/README").rdd.map(lambda r: r[0])
   counters = lines.flatMap(lambda x: x.split(' ')) \
                .map(lambda x: (x, 1)) \
                .reduceByKey(add)

   coll = counters.collect()
   sortedCollection = sorted(coll, key = lambda r: r[1], reverse = True)

   for i in range(0, 5):
        print(sortedCollection[i])
   ```

4. [Conéctese](#connect-to-an-azure-account) a su cuenta de Azure o vincule un clúster si no lo ha hecho aún.

5. Seleccione todo el código, haga clic con el botón derecho en el editor de scripts y seleccione **Spark: PySpark interactivo** para enviar la consulta. O bien, use el acceso directo Ctrl+Alt+I.

   ![menú contextual interactivo de pyspark](./media/hdinsight-for-vscode/pyspark-interactive-right-click.png)

6. Seleccione el clúster si no ha especificado un clúster predeterminado. Transcurridos unos instantes, los resultados **interactivos de Python** aparecen en una pestaña nueva. Las herramientas también permiten enviar un bloque de código en lugar del archivo de script completo mediante el menú contextual:

   ![Ventana interactiva de Python interactivo de PySpark](./media/hdinsight-for-vscode/pyspark-interactive-python-interactive-window.png) 

7. Escriba **%%info** y presione Mayús+Entrar para ver información sobre el trabajo (opcional):

   ![PySpark interactivo: ver información de trabajo](./media/hdinsight-for-vscode/pyspark-interactive-view-job-information.png)

8. La herramienta también admite la consulta **Spark SQL**:

   ![PySpark interactivo: visualización de resultado](./media/hdinsight-for-vscode/pyspark-ineteractive-select-result.png)

   El estado del envío aparece en la parte izquierda de la barra de estado inferior cuando se ejecutan consultas. No envíe otras consultas cuando el estado sea **PySpark Kernel (busy)** [Kernel de PySpark (ocupado)].  

   > [!NOTE]
   >
   > Si la opción **Python Extension Enabled** (Extensión de Python habilitada) está desactivada en la configuración (está activada de forma predeterminada), los resultados de la interacción de PySpark enviados usarán la ventana anterior:
   >
   > ![PySpark interactivo: extensión de Python deshabilitada](./media/hdinsight-for-vscode/pyspark-interactive-python-extension-disabled.png)

## <a name="submit-pyspark-batch-job"></a>Envío de un trabajo por lotes de PySpark

1. Vuelva a abrir la carpeta **HDexample** que se trató [anteriormente](#open-a-work-folder), en caso de que esté cerrada.  

2. Cree un nuevo archivo **BatchFile.py** siguiendo los pasos [anteriores](#open-a-work-folder).

3. Copie y pegue el código siguiente en el archivo de script:

    ```python
    from __future__ import print_function
    import sys
    from operator import add
    from pyspark.sql import SparkSession
    if __name__ == "__main__":
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
    
        lines = spark.read.text('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv').rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' '))\
                    .map(lambda x: (x, 1))\
                    .reduceByKey(add)
        output = counts.collect()
        for (word, count) in output:
            print("%s: %i" % (word, count))
        spark.stop()
    ```

4. [Conéctese](#connect-to-an-azure-account) a su cuenta de Azure o vincule un clúster si no lo ha hecho aún.

5. Haga clic con el botón derecho en el editor de scripts y, después, seleccione **Spark: PySpark Batch** (HDInsight: Lote de PySpark), o bien use el acceso directo Ctrl+Alt+H.

6. Seleccione el clúster al que va a enviar el trabajo de PySpark:

   ![Enviar la salida del resultado del trabajo de Python](./media/hdinsight-for-vscode/submit-pythonjob-result.png)

Después de enviar un trabajo de Python, los registros de envío aparecen en la ventana **SALIDA** de Visual Studio Code. También se muestran la dirección URL de interfaz de usuario de Spark y la dirección URL de interfaz de usuario de Yarn. Puede abrir la dirección URL en un explorador web para realizar el seguimiento de estado del trabajo.

## <a name="apache-livy-configuration"></a>Configuración de Apache Livy

La [configuración de Apache Livy](https://livy.incubator.apache.org/) es compatible. Puede configurarla en el archivo **.VSCode\settings.json** en la carpeta del área de trabajo. En la actualidad, la configuración de Livy solo admite el script de Python. Para obtener más información, consulte el archivo [léame de Livy](https://github.com/cloudera/livy/blob/master/README.rst ).

<a id="triggerlivyconf"></a>**Cómo activar la configuración de Livy**

Método 1  
1. En la barra de menús, vaya a **Archivo** > **Preferencias** > **Configuración**.
2. En el cuadro de texto **Configuración de la búsqueda** escriba **HDInsight Job Submission: Livy Conf**. (Envío de trabajo de HDInsight: Livy Conf).  
3. Seleccione **Edit in settings.json** (Editar en settings.json) para el resultado de búsqueda pertinente.

Método 2: envíe un archivo y observe que la carpeta .vscode se agrega automáticamente a la carpeta de trabajo. Puede encontrar la configuración de Livy si selecciona **.vscode\settings.json**.

+ Configuración de proyecto:

    ![Configuración de Apache Livy en HDInsight](./media/hdinsight-for-vscode/hdi-apache-livy-config.png)

    >[!NOTE]
    >Para la configuración de **driverMemory** y **executorMemory**, establezca el valor y la unidad. Por ejemplo:  1g o 1024m.

+ Configuraciones de Livy admitidas:

    **POST /batches** Cuerpo de la solicitud

    | Nombre | description | Tipo |
    | :- | :- | :- |
    | file | Archivo que contiene la aplicación para ejecutar | Ruta (obligatorio) |
    | proxyUser | Usuario para suplantar cuando se ejecuta el trabajo | Cadena |
    | className | Clase principal de Spark o Java de la aplicación | Cadena |
    | args | Argumentos de la línea de comandos para la aplicación | Lista de cadenas |
    | jars | Archivos JAR que se usarán en esta sesión | Lista de cadenas | 
    | pyFiles | Archivos Python que se usarán en esta sesión | Lista de cadenas |
    | files | Archivos que se usarán en esta sesión | Lista de cadenas |
    | driverMemory | Cantidad de memoria que se usará para el proceso de controlador | Cadena |
    | driverCores | Número de núcleos que se usarán para el proceso de controlador | Int |
    | executorMemory | Cantidad de memoria que se usará por proceso ejecutor | Cadena |
    | executorCores | Número de núcleos que se usarán para cada ejecutor | Int |
    | numExecutors | Número de ejecutores para iniciar en esta sesión | Int |
    | archives | Archivos que se usarán en esta sesión | Lista de cadenas |
    | queue | El nombre de la cola YARN a la que se enviará| Cadena |
    | name | Nombre de esta sesión | Cadena |
    | conf | Propiedades de configuración de Spark. | Asignación de clave = val |

    Response body   
    El objeto Batch creado.

    | name | description | Tipo |
    | :- | :- | :- | 
    | id | Identificador de la sesión | Int | 
    | appId | Identificador de la aplicación de esta sesión | Cadena |
    | appInfo | Información detallada de la aplicación | Asignación de clave = val |
    | log | Líneas de registro | Lista de cadenas |
    | state |Estado del lote | Cadena |

    >[!NOTE]
    >La configuración de Livy asignada se muestra en el panel de salida al enviar el script.

## <a name="integrate-with-azure-hdinsight-from-explorer"></a>Integración con Azure HDInsight desde el explorador

**Azure HDInsight** se ha agregado a la vista Explorador. Puede examinar y administrar los clústeres directamente mediante **Azure HDInsight**.

1. [Conéctese](#connect-to-an-azure-account) a su cuenta de Azure o vincule un clúster si no lo ha hecho aún.

2. En la barra de menús, vaya a **Vista** > **Explorador**.

3. En el panel izquierdo, expanda **AZURE HDINSIGHT**.  Se enumeran las suscripciones y los clústeres disponibles (se admiten Spark, Hadoop y HBase):

   ![Pantalla de suscripción de Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-subscription.png)

4. Expanda el clúster para ver el esquema de base de datos y tablas de metadatos de Hive:

   ![Eliminación de clúster de Azure HDInsight](./media/hdinsight-for-vscode/hdi-azure-hdinsight-cluster.png)

## <a name="preview-hive-table"></a>Vista previa de la tabla de Hive
Puede obtener una vista previa de la tabla de Hive directamente en los clústeres a través del explorador de **Azure HDInsight**:
1. [Conéctese](#connect-to-an-azure-account) a su cuenta de Azure si no lo ha hecho aún.

2. Seleccione el icono **Azure** de la columna situada en el extremo izquierdo.

3. En el panel izquierdo, expanda **AZURE HDINSIGHT**. Aparecen las suscripciones y clústeres disponibles.

4. Expanda el clúster para ver el esquema de base de datos y tablas de metadatos de Hive.

5. Haga clic con el botón derecho en la tabla de Hive. Por ejemplo: **hivesampletable**. Seleccione **Vista previa**. 

   ![Tabla de Hive con la vista previa de Hive y Spark para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-hive-table.png)

6. Se abre la ventana **Vista previa de resultados**:

   ![Ventana de resultados con la vista previa de Hive y Spark para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-results-window.png)

- Panel RESULTADOS

   Puede guardar todo el resultado en forma de archivo CSV, JSON o Excel en una ruta de acceso local, o bien seleccionar únicamente varias líneas.

- Panel MENSAJES
   1. Cuando el número de filas de la tabla es superior a 100, aparece un mensaje que informa de que se muestran las 100 primeras filas de la tabla de Hive.
   2. Cuando el número de filas de la tabla es menor o igual a 100, aparece un mensaje que informa de que se muestran 60 filas de la tabla de Hive.
   3. Cuando no haya contenido en la tabla, verá un mensaje que informa de que se muestran 0 filas de la tabla de Hive.

        >[!NOTE]
        >
        >En Linux, instale xclip para permitir la copia de datos de tabla.
        >
        >![Hive y Spark para Visual Studio Code en Linux](./media/hdinsight-for-vscode/hdinsight-for-vscode-preview-linux-install-xclip.png)
    ## <a name="additional-features"></a>Características adicionales

Hive y Spark para Visual Studio Code admite también las siguientes características:

- **Autocompletar de IntelliSense**. Aparecen sugerencias para palabras clave, métodos, variables y otros elementos de programación. Los distintos iconos representan diferentes tipos de objetos:

    ![Objetos de IntelliSense de las herramientas de Spark y Hive para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-auto-complete-objects.png)

- **Marcador de error de IntelliSense**. El servicio de lenguaje subraya los errores de edición del script de Hive.     
- **Aspectos destacados de la sintaxis**. El servicio de lenguaje usa colores diferentes para diferenciar variables, palabras clave, tipos de datos, funciones y otros elementos de programación:

    ![Aspectos destacados de la sintaxis de las herramientas de Spark y Hive para Visual Studio Code](./media/hdinsight-for-vscode/hdinsight-for-vscode-syntax-highlights.png)

## <a name="reader-only-role"></a>Rol de solo lectura

Los usuarios a los que se haya asignado el rol solo lector para el clúster ya no podrán enviar trabajos al clúster de HDInsight ni podrán ver la base de datos de Hive. Póngase en contacto con el administrador de clústeres para actualizar el rol a [**operador de clústeres de HDInsight**](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-granular-access-cluster-configurations#add-the-hdinsight-cluster-operator-role-assignment-to-a-user) en [Azure Portal](https://ms.portal.azure.com/). Si tiene credenciales de Ambari válidas, puede vincular manualmente el clúster siguiendo estas instrucciones.

### <a name="browse-the-hdinsight-cluster"></a>Examen del clúster de HDInsight  

Al seleccionar el explorador de Azure HDInsight para expandir un clúster de HDInsight, se le pedirá que vincule el clúster si tiene el rol de solo lector en el clúster. Use el método siguiente para vincular al clúster con sus credenciales de Ambari.

### <a name="submit-the-job-to-the-hdinsight-cluster"></a>Envío del trabajo al clúster de HDInsight

Al enviar un trabajo al clúster de HDInsight, se le pide que vincule el clúster si tiene el rol de solo lector en el clúster. Siga estos pasos para vincular al clúster con sus credenciales de Ambari.

### <a name="link-to-the-cluster"></a>Vínculo al clúster

1. Escriba un nombre de usuario de Ambari válido.
2. Escriba una contraseña válida.

   ![Nombre de usuario de Spark & Hive Tools para Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-username.png)

   ![Contraseña de Spark y Hive para Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-password.png)

  > [!NOTE]
  >
  >Puede usar `Spark / Hive: List Cluster` para comprobar el clúster vinculado:
  >
  >![Lector vinculado de las herramientas de Spark y Hive para Visual Studio Code](./media/hdinsight-for-vscode/list-cluster-result1.png)

## <a name="azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2

### <a name="browse-a-data-lake-storage-gen2-account"></a>Examen de una cuenta de Data Lake Storage Gen2

Al seleccionar el explorador de Azure HDInsight para expandir una cuenta de Azure Data Lake Storage Gen2, se le pedirá que escriba la clave de acceso del almacenamiento si la cuenta de Azure no tiene acceso al almacenamiento de Gen2. Una vez validada la clave de acceso, la cuenta de Data Lake Storage Gen2 se expande automáticamente.

### <a name="submit-jobs-to-an-hdinsight-cluster-with-data-lake-storage-gen2"></a>Envío de trabajos a un clúster de HDInsight con Data Lake Storage Gen2

Al enviar un trabajo a un clúster de HDInsight con Data Lake Storage Gen2, se le pedirá que escriba la clave de acceso del almacenamiento si la cuenta de Azure no tiene acceso de escritura en el almacenamiento de Gen2. El trabajo se enviará sin problemas una vez validada correctamente la clave de acceso.

![Clave de acceso de las herramientas de Spark y Hive para Visual Studio Code](./media/hdinsight-for-vscode/hdi-azure-hdinsight-azure-accesskey.png)

> [!NOTE]
>
> Puede obtener la clave de acceso de la cuenta de almacenamiento desde Azure Portal. Para más información, vea [Claves de acceso](https://docs.microsoft.com/azure/storage/common/storage-account-manage#access-keys).

## <a name="unlink-cluster"></a>Desvinculación del clúster

1. En la barra de menús, vaya a **Vista** > **Paleta de comandos** y escriba **Spark / Hive: Unlink a Cluster** (HDInsight: desvincular un clúster).  

2. Seleccione el clúster que desea desvincular.  

3. Revise la vista **SALIDA** para comprobarlo todo.  

## <a name="sign-out"></a>Cerrar sesión  

En la barra de menús, vaya a **Vista** > **Paleta de comandos** y escriba **Azure: Sign Out** (Cerrar sesión).

## <a name="next-steps"></a>Pasos siguientes

Para ver un vídeo de demostración del uso de Spark y Hive para Visual Studio Code, consulte [Spark y Hive para Visual Studio Code](https://go.microsoft.com/fwlink/?linkid=858706).
