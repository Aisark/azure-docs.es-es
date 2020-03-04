---
title: Uso de Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight
description: Obtenga información sobre cómo usar Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/20/2020
ms.openlocfilehash: d711cc7e58fb055eda62cfc364a5552a7d10f7bd
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623146"
---
# <a name="use-azure-data-lake-storage-gen2-with-azure-hdinsight-clusters"></a>Uso de Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight

‎Azure Data Lake Storage Gen2 es un servicio de almacenamiento en la nube dedicado al análisis de macrodatos e integrado en Azure Blob Storage. Data Lake Storage Gen2 combina las funcionalidades de Azure Blob Storage y Azure Data Lake Storage Gen1. El servicio resultante ofrece características de Azure Data Lake Storage Gen1, como la semántica del sistema de archivos, la seguridad de los niveles de directorio y de archivo, y la escalabilidad junto con las funcionalidades de recuperación ante desastres, de alta disponibilidad y de almacenamiento por niveles de bajo costo de Azure Blob Storage.

## <a name="data-lake-storage-gen2-availability"></a>Disponibilidad de Data Lake Storage Gen2

Data Lake Storage Gen2 está disponible como opción de almacenamiento para casi todos los tipos de clúster de Azure HDInsight como una cuenta de almacenamiento predeterminada o adicional. Sin embargo, HBase solo puede tener una cuenta de Data Lake Storage Gen2.

Para ver una comparación completa de las opciones de creación de clústeres con Data Lake Storage Gen2, consulte [Comparación de opciones de almacenamiento para los clústeres de Azure HDInsight](hdinsight-hadoop-compare-storage-options.md).

> [!Note]  
> Una vez que selecciona Data Lake Storage Gen2 como el **tipo de almacenamiento principal**, no puede seleccionar una cuenta de Data Lake Storage Gen1 como almacenamiento adicional.

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-portal"></a>Creación de un clúster con Data Lake Storage Gen2 mediante Azure Portal

Para crear un clúster de HDInsight que use Data Lake Storage Gen2 para el almacenamiento, siga estos pasos para configurar una cuenta de Data Lake Storage Gen2.

### <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario

Cree una identidad administrada asignada por el usuario si todavía no tiene una.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la parte superior izquierda, haga clic en **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **usuario asignado** y haga clic en **Identidad administrada asignada por el usuario**.
1. Haga clic en **Crear**.
1. Escriba un nombre para la identidad administrada y seleccione la suscripción, el grupo de recursos y la ubicación correctos.
1. Haga clic en **Crear**.

Para obtener más información sobre cómo funcionan las identidades administradas en Azure HDInsight, vea [Identidades administradas en Azure HDInsight](hdinsight-managed-identities.md).

![Crear una identidad administrada asignada por el usuario](./media/hdinsight-hadoop-use-data-lake-storage-gen2/create-user-assigned-managed-identity-portal.png)

### <a name="create-a-data-lake-storage-gen2-account"></a>Creación de una cuenta de Data Lake Storage Gen2

Cree una cuenta de almacenamiento de Azure Data Lake Storage Gen2.

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. En la parte superior izquierda, haga clic en **Crear un recurso**.
1. En el cuadro de búsqueda, escriba **almacenamiento** y haga clic en **Cuenta de almacenamiento**.
1. Haga clic en **Crear**.
1. En la pantalla **Crear la cuenta de almacenamiento**:
    1. Seleccione la suscripción y el grupo de recursos correctos.
    1. Escriba un nombre para la cuenta de Data Lake Storage Gen2. Para obtener más información sobre las convenciones de nomenclatura de la cuenta de almacenamiento, vea [Convenciones de nomenclatura para los recursos de Azure](/azure/azure-resource-manager/management/resource-name-rules#microsoftstorage).
    1. Haga clic en la pestaña **Avanzado**.
    1. Haga clic en **Habilitado** junto a **Espacio de nombres jerárquico** en **Data Lake Storage Gen2**.
    1. Haga clic en **Revisar + crear**.
    1. Haga clic en **Crear**

Para obtener más información sobre otras opciones durante la creación de la cuenta de almacenamiento, consulte [Inicio rápido: Creación de una cuenta de almacenamiento de Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-quickstart-create-account.md).

![Captura de pantalla en la que se muestra la creación de una cuenta de almacenamiento en Azure Portal](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-data-lake-storage-account-create-advanced.png)

### <a name="set-up-permissions-for-the-managed-identity-on-the-data-lake-storage-gen2-account"></a>Configuración de permisos para la identidad administrada en la cuenta de Data Lake Storage Gen2

Asigne la identidad administrada al rol **Propietario de datos de Storage Blob** en la cuenta de almacenamiento.

1. En [Azure Portal](https://portal.azure.com), vaya a la cuenta de almacenamiento.
1. Seleccione la cuenta de almacenamiento y, después, seleccione **Control de acceso (IAM)** para mostrar la configuración del control de acceso de la cuenta. Seleccione la pestaña **Asignaciones de roles** para ver la lista de asignaciones de roles.

    ![Captura de pantalla que muestra la configuración de control de acceso del almacenamiento](./media/hdinsight-hadoop-use-data-lake-storage-gen2/portal-access-control.png)

1. Seleccione el botón **+ Agregar asignación de roles** para agregar un rol nuevo.
1. En la ventana **Agregar asignación de roles**, seleccione el rol **Propietario de datos de Storage Blob**. Luego, seleccione la suscripción que tiene la identidad administrada y la cuenta de almacenamiento. A continuación, busque la identidad administrada asignada por el usuario que creó anteriormente. Por último, seleccione la identidad administrada y esta se mostrará en **Miembros seleccionados**.

    ![Captura de pantalla que muestra cómo asignar un rol de RBAC](./media/hdinsight-hadoop-use-data-lake-storage-gen2/add-rbac-role3-window.png)

1. Seleccione **Guardar**. La identidad asignada por el usuario que seleccionó ahora aparece en el rol seleccionado.
1. Una vez completada la configuración inicial, puede crear un clúster a través del portal. El clúster debe estar en la misma región de Azure que la cuenta de almacenamiento. En la pestaña **Almacenamiento** del menú de creación del clúster, seleccione las siguientes opciones:

    * En **Tipo de almacenamiento principal**, haga clic en **Azure Data Lake Storage Gen2**.
    * En **Cuenta de almacenamiento principal**, busque y seleccione la cuenta de almacenamiento de Data Lake Storage Gen2 recién creada.

    * En **Identidad**, seleccione la identidad administrada asignada por el usuario recién creada.

        ![Configuración de almacenamiento para usar Data Lake Storage Gen2 con Azure HDInsight](./media/hdinsight-hadoop-use-data-lake-storage-gen2/azure-portal-cluster-storage-gentwo.png)

    > [!NOTE]
    > * Para agregar una cuenta secundaria de Data Lake Storage Gen2 en el nivel de cuenta de almacenamiento, basta con asignar la identidad administrada que ha creado anteriormente a la nueva cuenta de almacenamiento de Data Lake Storage Gen2 que desea agregar. Tenga en cuenta que no se admite la adición de una cuenta secundaria de Data Lake Storage Gen2 mediante la hoja "Cuentas de almacenamiento adicionales" en HDInsight.
    > * Puede habilitar RA-GRS o RA-ZRS en la cuenta de Azure Storage que usa HDInsight. Sin embargo, no se admite la creación de un clúster en el punto de conexión secundario RA-GRS o RA-ZRS.


## <a name="create-a-cluster-with-data-lake-storage-gen2-through-the-azure-cli"></a>Creación de un clúster con Data Lake Storage Gen2 mediante la CLI de Azure

Puede [descargar un archivo de plantilla de muestra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) y [descargar un archivo de parámetros de muestra](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json). Antes de usar la plantilla y el fragmento de código de la CLI de Azure que aparecen a continuación, reemplace estos marcadores de posición por sus valores correctos:

| Marcador de posición | Descripción |
|---|---|
| `<SUBSCRIPTION_ID>` | Identificador de la suscripción de Azure |
| `<RESOURCEGROUPNAME>` | El grupo de recursos donde quiere crear el nuevo clúster y la cuenta de almacenamiento. |
| `<MANAGEDIDENTITYNAME>` | El nombre de la identidad administrada a la que se concederán permisos en la cuenta de Azure Data Lake Storage Gen2. |
| `<STORAGEACCOUNTNAME>` | La nueva cuenta de Azure Data Lake Storage Gen2 que se creará. |
| `<CLUSTERNAME>` | El nombre del clúster de HDInsight. |
| `<PASSWORD>` | La contraseña elegida para iniciar sesión en el clúster mediante SSH, así como el panel de Ambari. |

El siguiente fragmento de código realiza los pasos iniciales siguientes:

1. Inicia sesión en la cuenta de Azure.
1. Establece la suscripción activa donde se realizarán las operaciones de creación.
1. Crea un nuevo grupo de recursos para las nuevas actividades de implementación.
1. Crea una identidad administrada asignada por el usuario.
1. Agrega una extensión a la CLI de Azure para usar las características para Data Lake Storage Gen2.
1. Crea una nueva cuenta de Data Lake Storage Gen2 mediante el uso de la marca `--hierarchical-namespace true`.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

Después, inicie sesión en el portal. Agregue la nueva identidad administrada asignada por el usuario al rol **Colaborador de datos de Storage Blob** en la cuenta de almacenamiento, como se describe en el paso 3 en [Uso de Azure Data Lake Storage Gen2 con clústeres de Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md).

Después de haber asignado el rol para la identidad administrada asignada por el usuario, implemente la plantilla mediante el siguiente fragmento de código.

```azurecli
az group deployment create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="create-a-cluster-with-data-lake-storage-gen2-through-azure-powershell"></a>Creación de un clúster con Data Lake Storage Gen2 mediante Azure PowerShell

El uso de PowerShell para crear un clúster de HDInsight con Azure Data Lake Storage Gen2 no se admite actualmente.

## <a name="access-control-for-data-lake-storage-gen2-in-hdinsight"></a>Control de acceso para Data Lake Storage Gen2 en HDInsight

### <a name="what-kinds-of-permissions-does-data-lake-storage-gen2-support"></a>¿Qué tipos de permisos admite Data Lake Storage Gen2?

Data Lake Storage Gen2 usa un modelo de control de acceso compatible con el control de acceso basado en rol (RBAC) y las listas de control de acceso (ACL) tipo POSIX. Data Lake Storage Gen1 admite las listas de control de acceso solo para controlar el acceso a los datos.

RBAC usa las asignaciones de roles para aplicar de forma eficaz conjuntos de permisos a usuarios, grupos y entidades de servicio para los recursos de Azure. Normalmente, los recursos de Azure están limitados a los recursos de nivel superior (por ejemplo, cuentas de Azure Storage). En el caso de Azure Storage y también de Data Lake Storage Gen2, este mecanismo se ha ampliado al recurso del sistema de archivos.

 Para más información sobre los permisos de archivo con RBAC, consulte [Control de acceso basado en rol (RBAC) de Azure](../storage/blobs/data-lake-storage-access-control.md#azure-role-based-access-control-rbac).

Para más información sobre los permisos de archivo con las listas de control de acceso, consulte [Listas de control de acceso en archivos y directorios](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories).

### <a name="how-do-i-control-access-to-my-data-in-data-lake-storage-gen2"></a>¿Cómo se controla el acceso a mis datos en Data Lake Storage Gen2?

La capacidad que el clúster de HDInsight tiene de acceder a los archivos en Data Lake Storage Gen2 se controla mediante las identidades administradas. Una identidad administrada es una identidad registrada en Azure Active Directory (Azure AD) cuyas credenciales son administradas por Azure. Con las identidades administradas, no es necesario registrar las entidades de servicio en Azure AD ni mantener credenciales, tales como los certificados.

Los servicios de Azure tienen dos tipos de identidades administradas: asignadas por el sistema y asignadas por el usuario. HDInsight usa las identidades administradas asignadas por el usuario para acceder a Data Lake Storage Gen2. Las identidades administrada asignadas por el usuario se crean como recursos de Azure independientes. Mediante un proceso de creación, Azure crea una identidad en el inquilino de Azure AD de confianza para la suscripción que se utiliza. Una vez creada la identidad, esta puede asignarse a una o varias instancias de servicio de Azure.

El ciclo de vida de una identidad asignada por el usuario no se administra junto con el ciclo de vida de las instancias de servicio de Azure a las que se asigna. Para más información sobre las identidades administradas, consulte [¿Cómo funcionan las identidades administradas para los recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work).

### <a name="how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services"></a>¿Cómo puedo establecer los permisos para que los usuarios de Azure AD consulten los datos en Data Lake Storage Gen2 mediante Hive u otros servicios?

Para establecer los permisos para que los usuarios consulten los datos, utilice grupos de seguridad de Azure AD como la entidad de seguridad asignada en las ACL. No asigne directamente permisos de acceso a archivos a usuarios individuales ni a entidades de servicio. Cuando usa los grupos de seguridad de Azure AD para controlar el flujo de los permisos, puede agregar y quitar usuarios o entidades de servicio sin volver a aplicar las ACL a la estructura de todo un directorio. Solo tiene que agregar o quitar los usuarios del grupo de seguridad de Azure AD adecuado. Las ACL no se heredan y, por ello, para volver a aplicar las ACL es necesario actualizarlas en cada archivo y subdirectorio.

## <a name="access-files-from-the-cluster"></a>Acceso a los archivos desde el clúster

Existen varias maneras de acceder a los archivos de Data Lake Storage Gen2 desde un clúster de HDInsight.

* **Con el nombre completo**. Con este enfoque, proporciona la ruta de acceso completa al archivo al que quiere acceder.

    ```
    abfs://<containername>@<accountname>.dfs.core.windows.net/<file.path>/
    ```

* **Con el formato abreviado de la ruta de acceso**. Con este enfoque, reemplazará la ruta de acceso a la raíz del clúster por:

    ```
    abfs:///<file.path>/
    ```

* **Con la ruta de acceso relativa**. Con este enfoque, solo proporciona la ruta de acceso relativa al archivo al que quiere acceder.

    ```
    /<file.path>/
    ```

### <a name="data-access-examples"></a>Ejemplos de acceso a datos

Los ejemplos se basan en una [conexión SSH](./hdinsight-hadoop-linux-use-ssh-unix.md) al nodo principal del clúster. En los ejemplos se usan los tres esquemas de URI. Reemplace `CONTAINERNAME` y `STORAGEACCOUNT` por los valores pertinentes.

#### <a name="a-few-hdfs-commands"></a>Algunos comandos hdfs

1. Crear un archivo en el almacenamiento local.

    ```bash
    touch testFile.txt
    ```

1. Crear directorios en el almacenamiento de clúster.

    ```bash
    hdfs dfs -mkdir abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -mkdir abfs:///sampledata2/
    hdfs dfs -mkdir /sampledata3/
    ```

1. Copia de datos del almacenamiento local al almacenamiento de clúster.

    ```bash
    hdfs dfs -copyFromLocal testFile.txt  abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -copyFromLocal testFile.txt  abfs:///sampledata2/
    hdfs dfs -copyFromLocal testFile.txt  /sampledata3/
    ```

1. Enumerar el contenido del directorio en el almacenamiento de clúster.

    ```bash
    hdfs dfs -ls abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/sampledata1/
    hdfs dfs -ls abfs:///sampledata2/
    hdfs dfs -ls /sampledata3/
    ```

#### <a name="creating-a-hive-table"></a>Creación de una tabla de Hive

Con fines de ilustración, se muestran tres ubicaciones de archivos. En la ejecución real, use solo una de las entradas `LOCATION`.

```hql
DROP TABLE myTable;
CREATE EXTERNAL TABLE myTable (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE
LOCATION 'abfs://CONTAINERNAME@STORAGEACCOUNT.dfs.core.windows.net/example/data/';
LOCATION 'abfs:///example/data/';
LOCATION '/example/data/';
```

## <a name="next-steps"></a>Pasos siguientes

* [Azure HDInsight integration with Data Lake Storage Gen2 preview - ACL and security update](https://azure.microsoft.com/blog/azure-hdinsight-integration-with-data-lake-storage-gen-2-preview-acl-and-security-update/) (Integración de Azure HDInsight con Data Lake Storage Gen2 [versión preliminar]: ACL y actualización de seguridad)
* [Introducción a Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Tutorial: Extracción, transformación y carga de datos mediante Interactive Query en Azure HDInsight](./interactive-query/interactive-query-tutorial-analyze-flight-data.md)
