---
title: 'Depuración de Apache Hadoop: Consulta de registros e interpretación de mensajes de error en Azure HDInsight'
description: Conozca los mensajes de error que puede recibir cuando administre HDInsight con PowerShell, así como los pasos que debe seguir para la recuperación.
ms.reviewer: jasonh
author: ashishthaps
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2017
ms.author: ashishth
ms.openlocfilehash: f96171e1c75676a185edf4a1901ef65b7181135a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2019
ms.locfileid: "64720997"
---
# <a name="analyze-apache-hadoop-logs"></a>Análisis de registros de Apache Hadoop

Cada clúster de Apache Hadoop en Azure HDInsight tiene una cuenta de almacenamiento de Azure que se usa como sistema de archivos predeterminado. La cuenta de almacenamiento se conoce como la cuenta de almacenamiento predeterminada. El clúster usa Azure Table Storage y Blob Storage de la cuenta de Storage predeterminada para almacenar sus registros.  Para averiguar cuál es la cuenta de almacenamiento predeterminada de su clúster, consulte [Administración de clústeres de Apache Hadoop en HDInsight](../hdinsight-administer-use-portal-linux.md#find-the-storage-accounts). Los registros conservan la cuenta de almacenamiento incluso después de que se elimine el clúster.

## <a name="logs-written-to-azure-tables"></a>Registros escritos en tablas de Azure

Los registros que se escriben en las tablas de Azure proporcionan un nivel de información sobre lo que ocurre en un clúster de HDInsight.

Al crear un clúster de HDInsight, se crean automáticamente seis tablas para los clústeres basados en Linux en la instancia de Table Storage predeterminada:

* hdinsightagentlog
* syslog
* daemonlog
* hadoopservicelog
* ambariserverlog
* ambariagentlog

Los nombres de archivo de tabla son **u\<ClusterName > DDMonYYYYatHHMMSSsss\<TableName >**.

Estas tablas contienen los siguientes campos:

* ClusterDnsName
* ComponentName
* EventTimestamp
* Host
* MALoggingHash
* Message
* N
* PreciseTimeStamp
* Rol
* RowIndex
* Inquilino
* TIMESTAMP
* TraceLevel

### <a name="tools-for-accessing-the-logs"></a>Herramientas para acceder a los registros
Hay muchas herramientas disponibles para acceder a los datos de estas tablas:

* Visual Studio
* Explorador de Azure Storage
* Power Query para Excel

#### <a name="use-power-query-for-excel"></a>Uso de Power Query para Excel
Power Query puede instalarse desde [Microsoft Power Query para Excel](https://www.microsoft.com/en-us/download/details.aspx?id=39379). Consulte los requisitos del sistema en la página de descarga.

**Usar Power Query para abrir y analizar el registro de servicio**

1. Abra **Microsoft Excel**.
2. En el menú de **Power Query**, haga clic en **Desde Azure** y en **Desde Microsoft Azure Table Storage**.
   
    ![Power Query de Excel de Hadoop de HDInsight: abrir el almacenamiento de tablas de Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-open.png)
3. Escriba el nombre de la cuenta de almacenamiento, el nombre corto o el FQDN.
4. Escriba la clave de la cuenta de almacenamiento. Verá una lista de tablas:
   
    ![Registros de Hadoop de HDInsight almacenados en el almacenamiento de tablas de Azure](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-table-names.png)
5. Haga clic con el botón derecho en la tabla hadoopservicelog, en el panel **Navegador**, y seleccione **Editar**. Verá cuatro columnas. Opcionalmente, puede eliminar las columnas **Clave de partición**, **Clave de fila** y **Marca de tiempo** seleccionándolas y haciendo clic en **Quitar columnas** en las opciones de la cinta.
6. Haga clic en el icono Expandir, en la columna Contenido, para elegir las columnas que desea importar en la hoja de cálculo de Excel. Para esta demostración, se ha elegido TraceLevel y ComponentName: Pueden aportar información básica sobre los componentes que tenían problemas.
   
    ![Registros de Hadoop de HDInsight: elegir columnas](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-using-excel-power-query-filter.png)
7. Haga clic en **Aceptar** para importar los datos.
8. Seleccione las columnas **TraceLevel**, Rol y **ComponentName** y, luego, haga clic en el control **Agrupar por** en la cinta.
9. Haga clic en **Aceptar** en el cuadro de diálogo Agrupar por.
10. Haga clic en **Aplicar y cerrar**.

Ahora puede usar Excel para filtrar y ordenar según sea necesario. Puede interesarle incluir otras columnas (por ejemplo, Mensaje) para profundizar en los problemas cuando se produzcan, pero si selecciona y agrupa las columnas descritas anteriormente obtendrá una idea general adecuada de lo que ocurre en los servicios de Hadoop. La misma idea se puede aplicar a las tablas setuplog y hadoopinstalllog.

#### <a name="use-visual-studio"></a>Usar Visual Studio
**Usar Visual Studio**

1. Abra Visual Studio.
2. En el menú **Ver**, haga clic en **Cloud Explorer**. También puede hacer clic simplemente en **CTRL+\,, CTRL+X**.
3. En **Cloud Explorer**, seleccione **Tipos de recursos**.  La otra opción disponible es **Grupos de recursos**.
4. Expanda **Cuentas de almacenamiento**, la cuenta de almacenamiento predeterminada de su clúster y **Tablas**.
5. Haga doble clic en **hadoopservicelog**.
6. Agregue un filtro. Por ejemplo: 
   
        TraceLevel eq 'ERROR'
   
    ![Registros de Hadoop de HDInsight: elegir columnas](./media/apache-hadoop-debug-jobs/hdinsight-hadoop-analyze-logs-visual-studio-filter.png)
   
    Para más información sobre cómo construir filtros, consulte [Construcción de cadenas de filtro para el Diseñador de tablas](../../vs-azure-tools-table-designer-construct-filter-strings.md).

## <a name="logs-written-to-azure-blob-storage"></a>Registros escritos en Azure Blob Storage
Los registros que se escriben en las tablas de Azure proporcionan un nivel de información sobre lo que ocurre en un clúster de HDInsight. Sin embargo, estas tablas no proporcionan registros de nivel de tarea, que pueden ser útiles para obtener detalles sobre los problemas cuando se producen. Para proporcionar este nivel de detalle superior, los clústeres de HDInsight están configurados para escribir registros de tareas en su cuenta de Blob Storage para cualquier trabajo que se envíe a través de Templeton. En la práctica, esto hace referencia a los trabajos enviados mediante los cmdlets de Microsoft Azure PowerShell o las API de envío de trabajos de .NET, no a los trabajos enviados a través de RDP o el acceso de línea de comandos al clúster. 

Para ver los registros, consulte [Acceso a registros de aplicación de YARN de Apache Hadoop en HDInsight basado en Linux](../hdinsight-hadoop-access-yarn-app-logs-linux.md).


Para más información sobre los registros de aplicación, consulte [Simplifying user-logs management and access in YARN](https://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/) (Simplificación de la administración de registros de usuario y el acceso en YARN).


## <a name="view-cluster-health-and-job-logs"></a>Visualización de los registros de trabajo y del estado del clúster
### <a name="access-the-ambari-ui"></a>Acceder a la interfaz de usuario de Ambari
En Azure Portal, haga clic en un nombre de clúster de HDInsight para abrir el panel del clúster. En el panel del clúster, haga clic en **Panel**.

![Inicie el panel del clúster](./media/apache-hadoop-debug-jobs/hdi-debug-launch-dashboard.png)


### <a name="access-the-yarn-ui"></a>Acceder a la interfaz de usuario de Yarn
En Azure Portal, haga clic en un nombre de clúster de HDInsight para abrir el panel del clúster. En el panel del clúster, haga clic en **Panel**. Cuando se le pida, escriba las credenciales de administrador para el clúster. En Ambari, seleccione **YARN** de la lista de servicios situada a la izquierda. En la página que aparece, seleccione **Vínculos rápidos** y, después, seleccione la entrada del nodo principal activo y la interfaz de usuario de Resource Manager.

Puede utilizar la IU de YARN para hacer lo siguiente:

* **Obtener el estado del clúster**. En el panel izquierdo, expanda **Clúster** y haga clic en **Acerca de**. Esta acción presenta detalles del estado del clúster, como la memoria total asignada, los núcleos que se están usando, el estado del administrador de recursos del clúster, la versión del clúster, etc.
  
    ![Inicie el panel del clúster](./media/apache-hadoop-debug-jobs/hdi-debug-yarn-cluster-state.png)
* **Obtener el estado del nodo**. En el panel izquierdo, expanda **Clúster** y haga clic en **Nodos**. Esto enumera todos los nodos del clúster, la dirección HTTP de cada nodo, los recursos asignados a cada nodo, etc.
* **Supervisar el estado de los trabajos**. En el panel izquierdo, expanda **Clúster** y haga clic en **Aplicaciones** para enumerar todos los trabajos del clúster. Si desea ver los trabajos que se encuentran en un estado específico (como nuevo, enviado, en ejecución, etc.), haga clic en el vínculo apropiado en **Aplicaciones**. Además, puede hacer clic en el nombre del trabajo para obtener más información sobre el trabajo, incluyendo la salida, los registros, etc.

### <a name="access-the-hbase-ui"></a>Acceder a la interfaz de usuario de HBase
En Azure Portal, haga clic en un nombre de clúster de HDInsight HBase para abrir el panel del clúster. En el panel del clúster, haga clic en **Panel**. Cuando se le pida, escriba las credenciales de administrador para el clúster. En Ambari, seleccione HBase en la lista de servicios. Seleccione **Vínculos rápidos** en la parte superior de la página, seleccione el vínculo del nodo Zookeeper activo y, después, haga clic en HBase Master UI (Interfaz de usuario maestra de HBase).

## <a name="hdinsight-error-codes"></a>Códigos de error de HDInsight
Con los mensajes de error incluidos en esta sección pretendemos ayudar a los usuarios de Hadoop en Azure HDInsight a comprender algunas situaciones de error con las que se pueden encontrar al administrar el servicio con Azure PowerShell, así como asesorarlos sobre los pasos que pueden seguir para recuperarse del error.

Algunos de estos mensajes de error también podrían aparecer en el portal de Azure cuando se utiliza para administrar clústeres de HDInsight. Sin embargo, no es posible presentar de forma tan pormenorizada otros mensajes de error que pueden aparecer allí debido a las restricciones que afectan a las acciones de subsanación posibles en este contexto. Otros mensajes de error se asocian a los contextos en que la mitigación resulta obvia. 

### <a id="AtLeastOneSqlMetastoreMustBeProvided"></a>AtLeastOneSqlMetastoreMustBeProvided
* **Descripción**: proporcione los datos de la base de datos de Azure SQL de al menos un componente a fin de utilizar la configuración personalizada para las tiendas de metadatos de Hive y Oozie.
* **Mitigación**: el usuario debe facilitar una tienda de metadatos SQL de Azure válida y volver a enviar la solicitud.  

### <a id="AzureRegionNotSupported"></a>AzureRegionNotSupported
* **Descripción**: no se pudo crear un clúster en la región *nombreDeLaRegión*. Utilice una región de HDInsight válida y vuelva a enviar la solicitud.
* **Mitigación**: el cliente debe crear el clúster en una región que actualmente lo admita: Sudeste Asiático, Europa Occidental, Europa del Norte, Este de EE. UU. u Oeste de EE. UU.  

### <a id="ClusterContainerRecordNotFound"></a>ClusterContainerRecordNotFound
* **Descripción**: el servidor no encuentra el registro de clúster solicitado.  
* **Mitigación**: Vuelva a intentar la operación.

### <a id="ClusterDnsNameInvalidReservedWord"></a>ClusterDnsNameInvalidReservedWord
* **Descripción**: el nombre DNS del clúster *nombreDNS* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.  
* **Mitigación**: asegúrese de que se haya utilizado un nombre DNS válido para el clúster que empiece y acabe con un carácter alfanumérico y que no contenga ningún carácter especial, aparte del guión (-), y, a continuación, vuelva a intentarlo.

### <a id="ClusterNameUnavailable"></a>ClusterNameUnavailable
* **Descripción**: el nombre de clúster *nombreDeClúster* no está disponible. Elija otro nombre.  
* **Mitigación**: el usuario debe especificar un nombre de clúster que sea único y que no esté ya en uso, y, a continuación, volver a intentarlo. Si el usuario está usando el Portal, la IU le informará durante los pasos de creación si el nombre de clúster ya está en uso.

### <a id="ClusterPasswordInvalid"></a>ClusterPasswordInvalid
* **Descripción**: La contraseña del clúster no es válida. La contraseña debe cumplir estos requisitos: tener al menos 10 caracteres, incluir como mínimo un número, una letra mayúscula, una letra minúscula y un carácter especial, no contener espacios, y no estar formada a partir del nombre de usuario.  
* **Mitigación**: proporcione una contraseña de clúster válida y vuelva a intentarlo.

### <a id="ClusterUserNameInvalid"></a>ClusterUserNameInvalid
* **Descripción**: el nombre de usuario del clúster no es válido. Asegúrese de que no contenga caracteres especiales ni espacios.  
* **Mitigación**: proporcione un nombre de usuario de clúster válido y vuelva a intentarlo.

### <a id="ClusterUserNameInvalidReservedWord"></a>ClusterUserNameInvalidReservedWord
* **Descripción**: el nombre DNS del clúster *nombreDNSdelclúster* no es válido. Asegúrese de que empiece y acabe con un carácter alfanumérico y de que no contenga ningún carácter especial aparte de “-”.  
* **Mitigación**: proporcione un nombre de usuario de clúster DNS válido y vuelva a intentarlo.

### <a id="ContainerNameMisMatchWithDnsName"></a>ContainerNameMisMatchWithDnsName
* **Descripción**: el nombre de contenedor del URI *URIdeContenedor* y el nombre DNS *nombreDNS* del cuerpo de la solicitud deben coincidir.  
* **Mitigación**: asegúrese de que el nombre del contenedor y el nombre DNS coincidan y vuelva a intentarlo.

### <a id="DataNodeDefinitionNotFound"></a>DataNodeDefinitionNotFound
* **Descripción**: Configuración de clúster no es válida. No se puede encontrar ninguna definición de nodo de datos en el tamaño de nodo.  
* **Mitigación**: Vuelva a intentar la operación.

### <a id="DeploymentDeletionFailure"></a>DeploymentDeletionFailure
* **Descripción**: error al eliminar la implementación del clúster.  
* **Mitigación**: vuelva a intentar la operación de eliminación.

### <a id="DnsMappingNotFound"></a>DnsMappingNotFound
* **Descripción**: error de configuración de servicio. No se encuentra la información de asignación de DNS requerida.  
* **Mitigación**: elimine el clúster y cree uno nuevo.

### <a id="DuplicateClusterContainerRequest"></a>DuplicateClusterContainerRequest
* **Descripción**: intento de duplicación de un contenedor de clúster. Existe un registro con el nombre *nombredelcontenedor* , pero las propiedades Etag no coinciden.
* **Mitigación**: proporcione un nombre exclusivo para el contenedor y vuelva a intentarlo.

### <a id="DuplicateClusterInHostedService"></a>DuplicateClusterInHostedService
* **Descripción**: el servicio hospedado *nombreDelServicioHospedado* ya incluye un clúster. Los servicios hospedados no pueden contener varios clústeres.  
* **Mitigación**: hospede el clúster en otro servicio hospedado.

### <a id="FailureToUpdateDeploymentStatus"></a>FailureToUpdateDeploymentStatus
* **Descripción**: el servidor no pudo actualizar el estado de la implementación del clúster.  
* **Mitigación**: Vuelva a intentar la operación. Si esto ocurre varias veces, póngase en contacto con CSS.

### <a id="HdiRestoreClusterAltered"></a>HdiRestoreClusterAltered
* **Descripción**: el clúster *nombreDelClúster* se eliminó durante el mantenimiento. Vuelva a crearlo.
* **Mitigación**: vuelva a crear el clúster.

### <a id="HeadNodeConfigNotFound"></a>HeadNodeConfigNotFound
* **Descripción**: Configuración de clúster no es válida. Configuración de nodo principal requerida no encontrada en los tamaños de nodo.
* **Mitigación**: Vuelva a intentar la operación.

### <a id="HostedServiceCreationFailure"></a>HostedServiceCreationFailure
* **Descripción**: no se puede crear el servicio hospedado *nombreDelServicioHospedado*. Vuelva a intentarlo.  
* **Mitigación**: Vuelva a intentarlo.

### <a id="HostedServiceHasProductionDeployment"></a>HostedServiceHasProductionDeployment
* **Descripción**: el servicio hospedado *nombreDelServicioHospedado* ya incluye una implementación de producción. Los servicios hospedados no pueden contener varias implementaciones de producción. Vuelva a intentarlo con un nombre de clúster diferente.
* **Mitigación**: utilice un nombre de clúster diferente y vuelva a intentarlo.

### <a id="HostedServiceNotFound"></a>HostedServiceNotFound
* **Descripción**: no se encuentra el servicio hospedado *nombreDelServicioHospedado* del clúster.  
* **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### <a id="HostedServiceWithNoDeployment"></a>HostedServiceWithNoDeployment
* **Descripción**: el servicio hospedado *nombreDelServicioHospedado* no tiene asociada ninguna implementación.  
* **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.

### <a id="InsufficientResourcesCores"></a>InsufficientResourcesCores
* **Descripción**: al identificador de suscripción *identificadorDeLaSuscripción* no le quedan núcleos para crear el clúster *nombreDelClúster*. Necesario: *recursosrequeridos*, Disponible: *recursosdisponibles*.  
* **Mitigación**: libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### <a id="InsufficientResourcesHostedServices"></a>InsufficientResourcesHostedServices
* **Descripción**: el identificador de suscripción *identificadorDeLaSuscripción* no tiene cuota para un nuevo servicio hospedado y, por tanto, no puede crear el clúster *nombreDelClúster*.  
* **Mitigación**: libere recursos en la suscripción o aumente la cantidad de recursos disponibles para la suscripción e intente crear el clúster de nuevo.

### <a id="InternalErrorRetryRequest"></a>InternalErrorRetryRequest
* **Descripción**: Se produjo un error interno en el servidor. Vuelva a intentarlo.  
* **Mitigación**: Vuelva a intentarlo.

### <a id="InvalidAzureStorageLocation"></a>InvalidAzureStorageLocation
* **Descripción**: la ubicación de Azure Storage *nombreDeRegiónDeDatos* no es válida. Asegúrese de que la región sea correcta y vuelva a intentarlo.
* **Mitigación**: elija una ubicación de almacenamiento compatible con HDInsight, compruebe que el clúster esté colocalizado y vuelva a intentarlo.

### <a id="InvalidNodeSizeForDataNode"></a>InvalidNodeSizeForDataNode
* **Descripción**: tamaño de máquina virtual no válido para los nodos de datos. Únicamente el tamaño de máquina virtual grande es compatible con todos los nodos de datos.  
* **Mitigación**: especifique el tamaño de nodo admitido para el nodo de datos y vuelva a intentarlo.

### <a id="InvalidNodeSizeForHeadNode"></a>InvalidNodeSizeForHeadNode
* **Descripción**: tamaño de máquina virtual no válido para un nodo principal. Únicamente el tamaño de máquina virtual extragrande es compatible con los nodos principales.  
* **Mitigación**: especifique el tamaño de nodo admitido para el nodo principal y vuelva a intentarlo.

### <a id="InvalidRightsForDeploymentDeletion"></a>InvalidRightsForDeploymentDeletion
* **Descripción**: el identificador de suscripción *identificadorDeLaSuscripción* que se está utilizando no dispone de suficientes permisos para ejecutar la operación de eliminación del clúster *nombreDelClúster*.  
* **Mitigación**: si el clúster se encuentra en estado de error, elimínelo y, a continuación, vuelva a intentarlo.  

### <a id="InvalidStorageAccountBlobContainerName"></a>InvalidStorageAccountBlobContainerName
* **Descripción**: el nombre del contenedor de blobs de la cuenta de almacenamiento externa *nombreDelContenedor* no es válido. Asegúrese de que el nombre comience con una letra y solo contenga minúsculas, números y guiones.  
* **Mitigación**: especifique un nombre de contenedor de blobs de cuenta de almacenamiento válido y vuelva a intentarlo.

### <a id="InvalidStorageAccountConfigurationSecretKey"></a>InvalidStorageAccountConfigurationSecretKey
* **Descripción**: para configurar la cuenta de almacenamiento externa *nombreDeLaCuentaDeAlmacenamiento* es necesario establecer primero los datos de la clave secreta.  
* **Mitigación**: especifique una clave secreta válida para la cuenta de almacenamiento y vuelva a intentarlo.

### <a id="InvalidVersionHeaderFormat"></a>InvalidVersionHeaderFormat
* **Descripción**: el encabezado de la versión *encabezadoDeLaVersión* no tiene el formato válido de aaaa-mm-dd.  
* **Mitigación**: especifique un formato válido para el encabezado de la versión y vuelva a intentarlo.

### <a id="MoreThanOneHeadNode"></a>MoreThanOneHeadNode
* **Descripción**: Configuración de clúster no es válida. Se encontró más de una configuración de nodo principal.  
* **Mitigación**: edite la configuración para que solo se especifique un nodo principal.

### <a id="OperationTimedOutRetryRequest"></a>OperationTimedOutRetryRequest
* **Descripción**: la operación no se pudo completar en el tiempo permitido, o bien se alcanzó el número máximo de intentos. Vuelva a intentarlo.  
* **Mitigación**: Vuelva a intentarlo.

### <a id="ParameterNullOrEmpty"></a>ParameterNullOrEmpty
* **Descripción**: el parámetro *nombreDelParámetro* no puede ser nulo ni estar vacío.  
* **Mitigación**: especifique un valor válido para el parámetro.

### <a id="PreClusterCreationValidationFailure"></a>PreClusterCreationValidationFailure
* **Descripción**: al menos uno de los datos de la solicitud de creación de clúster no es válido. Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.  
* **Mitigación**: Asegúrese de que los valores escritos sean correctos y vuelva a intentarlo.

### <a id="RegionCapabilityNotAvailable"></a>RegionCapabilityNotAvailable
* **Descripción**: la funcionalidad de región no está disponible para la región *nombreDeLaRegión* y el identificador de suscripción *identificadorDeLaSuscripción*.  
* **Mitigación**: especifique una región compatible con los clústeres de HDInsight. Las regiones admitidas de manera pública son: Sudeste Asiático, Europa Occidental, Europa del Norte, Este de EE. UU. u Oeste de EE. UU.

### <a id="StorageAccountNotColocated"></a>StorageAccountNotColocated
* **Descripción**: la cuenta de almacenamiento *nombreDeLaCuentaDeAlmacenamiento* se encuentra en la región *nombreDeLaRegiónActual*. Debería coincidir con la región del clúster *nombredelaregióndelclúster*.  
* **Mitigación**: especifique una cuenta de almacenamiento situada en la misma región que el clúster, o bien, si la cuenta de almacenamiento ya contiene datos, cree un nuevo clúster en la misma región que la cuenta de almacenamiento existente. Si está usando el Portal, la IU le informará de este problema con antelación.

### <a id="SubscriptionIdNotActive"></a>SubscriptionIdNotActive
* **Descripción**: el identificador de suscripción proporcionado *identificadorDeLaSuscripción* no está activo.  
* **Mitigación**: vuelva a activar la suscripción u obtenga una nueva suscripción válida.

### <a id="SubscriptionIdNotFound"></a>SubscriptionIdNotFound
* **Descripción**: no se encuentra el identificador de suscripción *identificadorDeLaSuscripción*.  
* **Mitigación**: compruebe que el identificador de la suscripción sea válido y vuelva a intentarlo.

### <a id="UnableToResolveDNS"></a>UnableToResolveDNS
* **Descripción**: no se puede resolver el DNS *URLdelDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
* **Mitigación**: proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida y, entre otras cosas, debe empezar por *http://* y acabar en *.com*.

### <a id="UnableToVerifyLocationOfResource"></a>UnableToVerifyLocationOfResource
* **Descripción**: no se puede comprobar la ubicación del recurso *URLdeDNS*. Asegúrese de facilitar la dirección URL completa del extremo del blob.  
* **Mitigación**: proporcione una URL de blob válida. La dirección URL DEBE ser totalmente válida y, entre otras cosas, debe empezar por *http://* y acabar en *.com*.

### <a id="VersionCapabilityNotAvailable"></a>VersionCapabilityNotAvailable
* **Descripción**: la funcionalidad de versión no está disponible para la versión *versiónEspecificada* y el identificador de suscripción *identificadorDeLaSuscripción*.  
* **Mitigación**: elija una versión disponible y vuelva a intentarlo.

### <a id="VersionNotSupported"></a>VersionNotSupported
* **Descripción**: la versión *versiónEspecificada* no es compatible.
* **Mitigación**: elija una versión compatible y vuelva a intentarlo.

### <a id="VersionNotSupportedInRegion"></a>VersionNotSupportedInRegion
* **Descripción**: la versión *versiónEspecificada* no está disponible en la región de Azure *regiónEspecificada*.  
* **Mitigación**: elija una versión admitida en la región y vuelva a intentarlo.

### <a id="WasbAccountConfigNotFound"></a>WasbAccountConfigNotFound
* **Descripción**: Configuración de clúster no es válida. No se encuentra la configuración de cuenta WASB requerida en las cuentas externas.  
* **Mitigación**: compruebe que la cuenta exista y que esté bien especificada en la configuración, y luego, vuelva a intentarlo.

## <a name="next-steps"></a>Pasos siguientes

* [Habilitar los volcados de montón de los servicios de Apache Hadoop en HDInsight basado en Linux](../hdinsight-hadoop-collect-debug-heap-dump-linux.md)
* [Administración de clústeres de HDInsight con la interfaz de usuario web de Apache Ambari](../hdinsight-hadoop-manage-ambari.md)
