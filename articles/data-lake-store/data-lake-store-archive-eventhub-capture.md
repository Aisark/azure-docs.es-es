---
title: Capturar datos de Event Hubs en Azure Data Lake Storage Gen1 | Microsoft Docs
description: Usar Azure Data Lake Storage Gen1 para capturar datos de Event Hubs
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bb67c1769510710b368bef4dc0b501f939b3427e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "58884520"
---
# <a name="use-azure-data-lake-storage-gen1-to-capture-data-from-event-hubs"></a>Usar Azure Data Lake Storage Gen1 para capturar datos de Event Hubs

Obtenga información sobre cómo usar Azure Data Lake Storage Gen1 para capturar datos recibidos por Azure Event Hubs.

## <a name="prerequisites"></a>Requisitos previos

* **Una suscripción de Azure**. Consulte [Obtención de una versión de evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).

* **Una cuenta de Azure Data Lake Storage Gen1**. Para instrucciones sobre cómo crear una, consulte la [introducción a Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

*  **Un espacio de nombres de Event Hubs**. Para obtener instrucciones, consulte [Creación de un espacio de nombres de Event Hubs](../event-hubs/event-hubs-create.md#create-an-event-hubs-namespace). Asegúrese de que la cuenta de Data Lake Storage Gen1 y el espacio de nombres de Event Hubs se encuentran en la misma suscripción de Azure.


## <a name="assign-permissions-to-event-hubs"></a>Asignar permisos a Event Hubs

En esta sección, creará una carpeta en la cuenta en que quiere capturar los datos de Event Hubs. También asignará permisos a Event Hubs para que pueda escribir datos en una cuenta de Data Lake Storage Gen1. 

1. Abra la cuenta de Data Lake Storage Gen1 en que quiere capturar los datos de Event Hubs y después haga clic en **Explorador de datos**.

    ![Explorador de datos de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-open-data-explorer.png "Explorador de datos de Data Lake Storage Gen1")

1.  Haga clic en **Nueva carpeta** y después escriba un nombre para la carpeta en que quiere capturar los datos.

    ![Crear una carpeta en Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-new-folder.png "Crear una carpeta en Data Lake Storage Gen1")

1. Asigne permisos en la raíz de Data Lake Storage Gen1. 

     a. Haga clic en **Explorador de datos**, seleccione la raíz de la cuenta de Data Lake Storage Gen1 y después haga clic en **Acceso**.

    ![Asignar permisos a la raíz de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-root.png "Asignar permisos a la raíz de Data Lake Storage Gen1")

    b. En **Acceso**, haga clic en **Agregar**, en **Seleccionar usuario o grupo** y después busque `Microsoft.EventHubs`. 

    ![Asignar permisos a la raíz de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Asignar permisos a la raíz de Data Lake Storage Gen1")
    
    Haga clic en **Seleccionar**.

    c. En **Asignar permisos**, haga clic en **Seleccionar permisos**. Establezca **Permisos** en **Ejecutar**. Establezca **Agregar a** en **Esta carpeta y todos los elementos secundarios**. Establezca **Agregar como** en **Una entrada de permiso de acceso y una entrada de permiso predeterminado**.

    > [!IMPORTANT]
    > Al crear una nueva jerarquía de carpetas para capturar datos recibidos por Azure Event Hubs, esta es una manera sencilla de garantizar el acceso a la carpeta de destino.  Sin embargo, es posible que la adición de permisos a todos los elementos secundarios de una carpeta de nivel superior con muchos archivos y carpetas secundarios tarde mucho tiempo.  Si la carpeta raíz contiene una gran cantidad de archivos y carpetas, es posible que sea más rápido agregar permisos de **ejecución** para `Microsoft.EventHubs` individualmente a cada carpeta de la ruta a la carpeta de destino final. 

    ![Asignar permisos a la raíz de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp1.png "Asignar permisos a la raíz de Data Lake Storage Gen1")

    Haga clic en **OK**.

1. Asigne permisos a la carpeta en la cuenta de Data Lake Storage Gen1 en que quiere capturar los datos.

     a. Haga clic en **Explorador de datos**, seleccione la carpeta de la cuenta de Data Lake Storage Gen1 y después haga clic en **Acceso**.

    ![Asignar permisos a la carpeta de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-permissions-to-folder.png "Asignar permisos a la carpeta de Data Lake Storage Gen1")

    b. En **Acceso**, haga clic en **Agregar**, en **Seleccionar usuario o grupo** y después busque `Microsoft.EventHubs`. 

    ![Asignar permisos a la carpeta de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp.png "Asignar permisos a la carpeta de Data Lake Storage Gen1")
    
    Haga clic en **Seleccionar**.

    c. En **Asignar permisos**, haga clic en **Seleccionar permisos**. Establezca **Permisos** en **Leer, escribir** y **ejecutar**. Establezca **Agregar a** en **Esta carpeta y todos los elementos secundarios**. Por último, establezca **Agregar como** en **Una entrada de permiso de acceso y una entrada de permiso predeterminado**.

    ![Asignar permisos a la carpeta de Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-assign-eventhub-sp-folder.png "Asignar permisos a la carpeta de Data Lake Storage Gen1")
    
    Haga clic en **OK**. 

## <a name="configure-event-hubs-to-capture-data-to-data-lake-storage-gen1"></a>Configurar Event Hubs para capturar datos en Data Lake Storage Gen1

En esta sección, creará un centro de eventos en un espacio de nombres de Event Hubs. También configurará el Centro de eventos para capturar los datos en una cuenta de Azure Data Lake Storage Gen1. En esta sección, se da por supuesto que ya ha creado un espacio de nombres de Event Hubs.

1. En el panel **Introducción** del espacio de nombres de Event Hubs, haga clic en **+ Centro de eventos**.

    ![Crear centro de eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-create-event-hub.png "Crear centro de eventos")

1. Proporcione los siguientes valores para configurar Event Hubs para capturar datos en Data Lake Storage Gen1.

    ![Crear centro de eventos](./media/data-lake-store-archive-eventhub-capture/data-lake-store-configure-eventhub.png "Crear centro de eventos")

     a. Especifique un nombre para el centro de eventos.
    
    b. Para este tutorial, establezca **Recuento de particiones** y **Retención de mensajes** en los valores predeterminados.
    
    c. Establezca **Capture** en **Activado**. Establezca la **Ventana de tiempo** (la frecuencia de captura) y **Ajustar tamaño de la ventana** (tamaño de los datos que se capturarán). 
    
    d. En **Capture Provider** (Proveedor de Capture), seleccione **Azure Data Lake Store** y después seleccione la cuenta de Data Lake Storage Gen1 que ha creado anteriormente. En **Data Lake Path** (Ruta de acceso de Data Lake), escriba el nombre de la carpeta que ha creado en la cuenta de Data Lake Storage Gen1. Basta con proporcionar la ruta de acceso relativa a la carpeta.

    e. Deje **Formatos de nombre de archivo de Capture de ejemplo** en el valor predeterminado. Esta opción rige la estructura de carpetas que se crea en la carpeta de captura.

    f. Haga clic en **Create**(Crear).

## <a name="test-the-setup"></a>Probar la configuración

Ahora puede enviar datos a Azure Event Hubs para probar la solución. Siga las instrucciones de [Envío de eventos a Azure Event Hubs](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md). Cuando empiece a enviar los datos, los verá reflejados en Data Lake Storage Gen1 con la estructura de carpetas que ha especificado. Por ejemplo, verá una estructura de carpetas, tal y como se muestra en la siguiente captura de pantalla, en su cuenta de Data Lake Storage Gen1.

![Datos de ejemplo de EventHub en Data Lake Storage Gen1](./media/data-lake-store-archive-eventhub-capture/data-lake-store-eventhub-data-sample.png "Datos de ejemplo de EventHub en Data Lake Storage Gen1")

> [!NOTE]
> Incluso si no tiene mensajes que lleguen a Event Hubs, este escribe archivos vacíos solo con los encabezados en la cuenta de Data Lake Storage Gen1. Los archivos se escriben en el mismo intervalo de tiempo que ha proporcionado al crear Event Hubs.
> 
>

## <a name="analyze-data-in-data-lake-storage-gen1"></a>Análisis de datos en Data Lake Storage Gen1

Una vez que los datos están en Data Lake Storage Gen1, puede ejecutar trabajos analíticos para procesar y estudiar los datos. Vea [USQL Avro Example](https://github.com/Azure/usql/tree/master/Examples/AvroExamples) (Ejemplo de Avro de USQL) sobre cómo hacer esto con Azure Data Lake Analytics.
  

## <a name="see-also"></a>Vea también
* [Protección de datos en Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Copia de datos de los blobs de Azure Storage en Data Lake Storage Gen1](data-lake-store-copy-data-azure-storage-blob.md)
