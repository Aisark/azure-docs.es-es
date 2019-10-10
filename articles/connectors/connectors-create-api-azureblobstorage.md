---
title: 'Conexión a Azure Blob Storage: Azure Logic Apps'
description: Creación y administración de blobs en Azure Storage con Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
manager: carmonm
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 06/20/2019
tags: connectors
ms.openlocfilehash: ce59c238e50a1be6879b07e959b236f6181a8ce4
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703253"
---
# <a name="create-and-manage-blobs-in-azure-blob-storage-with-azure-logic-apps"></a>Creación y administración de blobs en Azure Blob Storage con Azure Logic Apps

En este artículo se muestra cómo se puede acceder y administrar los archivos almacenados como blobs en una cuenta de Azure Storage desde dentro de una aplicación de lógica con el conector de Azure Blob Storage. De este modo, puede crear aplicaciones lógicas que automatizan tareas y flujos de trabajo para administrar los archivos. Por ejemplo, puede crear aplicaciones lógicas que creen, obtengan, actualicen y eliminen archivos en su cuenta de almacenamiento.

Imagine que tiene una herramienta que se actualiza en un sitio web de Azure, que actúa como desencadenador para su aplicación lógica. Cuando se produce este evento, la aplicación lógica puede actualizar algunos archivos en el contenedor de Blob Storage, que es una acción de la aplicación lógica.

> [!NOTE]
>
> Las aplicaciones lógicas no pueden acceder directamente a cuentas de almacenamiento de Azure que tengan [reglas de firewall](../storage/common/storage-network-security.md) y que existan en la misma región. Sin embargo, las aplicaciones lógicas sí pueden acceder a cuentas de almacenamiento de Azure que existen en una región distinta, ya que se usa una dirección IP pública para la comunicación entre regiones. Solo tiene que asegurarse de permitir las [direcciones IP de salida para los conectores administrados en su región](../logic-apps/logic-apps-limits-and-config.md#outbound). O bien, puede usar opciones más avanzadas aquí:
>
> * Cree un [entorno de servicio de integración](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) que pueda conectarse a los recursos de una red virtual de Azure.
>
> * Si usa un nivel dedicado para API Management, puede enfrentar la API de Azure Storage si usa API Management y permite las direcciones IP de la última a través del firewall. Básicamente, agregue la red virtual de Azure que API Management usa a la configuración del firewall de la cuenta de almacenamiento. Después, puede usar la acción API Management o la acción HTTP para llamar a las API de Azure Storage. Sin embargo, si elige esta opción, tendrá que administrar el proceso de autenticación personalmente. Para obtener más información, vea [Arquitectura de integración empresarial sencilla](https://aka.ms/aisarch).

Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md) e [Inicio rápido: Creación de la primera aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para obtener información técnica específica del conector, consulte la [referencia sobre el conector de Azure Blob Storage](/connectors/azureblobconnector/).

## <a name="limits"></a>límites

* De forma predeterminada, las acciones de Azure Blob Storage pueden leer o escribir archivos de *50 MB o más pequeños*. Para controlar los archivos mayores de 50 MB, pero hasta 1024 MB, las acciones de Azure Blob Storage admiten la [fragmentación de mensajes](../logic-apps/logic-apps-handle-large-messages.md). La acción **Obtener contenido de blog** usa implícitamente la fragmentación.

* Los desencadenadores de Azure Blob Storage no admiten la fragmentación. Cuando se solicita el contenido del archivo, los desencadenadores seleccionan solo los archivos que tienen un tamaño de 50 MB o menos. Para obtener archivos de más de 50 MB, siga este patrón:

  * Use un desencadenador de Azure Blob Storage que devuelva las propiedades de archivo, como **Cuando se agrega o modifica un blob (solo propiedades)** .

  * Siga el desencadenador con la acción **Obtener contenido de blob** de Azure Blob Storage, que lee el archivo completo y utiliza la fragmentación implícitamente.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/).

* Una [cuenta de Azure Storage y un contenedor de almacenamiento](../storage/blobs/storage-quickstart-blobs-portal.md)

* La aplicación lógica en la que necesita acceso a la cuenta de Azure Blob Storage. Para iniciar la aplicación lógica con un desencadenador de Azure Blob Storage, necesita una [aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-blob-storage-trigger"></a>Agregar un desencadenador de Blob Storage

En Azure Logic Apps, cada aplicación lógica debe comenzar con un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts), que se activa cuando sucede un evento específico o cuando se cumple una condición determinada. Cada vez que el desencadenador se activa, el motor de Logic Apps crea una instancia de aplicación lógica y empieza a ejecutar el flujo de trabajo de la aplicación.

En este ejemplo se muestra cómo se puede iniciar un flujo de trabajo de una aplicación lógica con el desencadenador **Cuando se agrega o modifica un blob (solo propiedades)** cuando las propiedades de un blob se agregan o se actualizan en un contenedor de almacenamiento.

1. En [Azure Portal](https://portal.azure.com) o Visual Studio, cree una aplicación lógica en blanco que abra el diseñador de aplicaciones lógicas. En este ejemplo se usa Azure Portal.

2. En el cuadro de búsqueda, escriba "azure blob" como filtro. En la lista de desencadenadores, seleccione el que desee.

   En este ejemplo se utiliza este desencadenador: **Cuando se agrega o modifica un blob (solo propiedades)**

   ![Seleccionar un desencadenador](./media/connectors-create-api-azureblobstorage/azure-blob-trigger.png)

3. Si se le piden los detalles de la conexión, [cree ahora su conexión de Blob Storage](#create-connection). O bien, si la conexión ya existe, especifique la información necesaria para el desencadenador.

   Para este ejemplo, seleccione el contenedor y la carpeta que desea supervisar.

   1. En el cuadro **Contenedor**, seleccione el icono de la carpeta.

   2. En la lista de carpetas, elija el corchete angular derecho ( **>** ) y, después, desplácese hasta que encuentre y seleccione la carpeta que desee.

      ![Seleccionar carpeta](./media/connectors-create-api-azureblobstorage/trigger-select-folder.png)

   3. Seleccione el intervalo y la frecuencia con la que desea que el desencadenador compruebe si hay cambios en la carpeta.

4. Cuando esté listo, elija **Guardar** en la barra de herramientas del diseñador.

5. Ahora, agregue a la aplicación lógica una o varias acciones, en función de las tareas que desea realizar con los resultados del desencadenador.

<a name="add-action"></a>

## <a name="add-blob-storage-action"></a>Agregar un acción a Blob Storage

En Azure Logic Apps, una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) es un paso del flujo de trabajo que sigue a un desencadenador u otra acción. En este ejemplo, la aplicación lógica se inicia con el [desencadenador Periodicidad](../connectors/connectors-native-recurrence.md).

1. En [Azure Portal](https://portal.azure.com) o Visual Studio, abra la aplicación lógica en el diseñador de aplicaciones lógicas. En este ejemplo se usa Azure Portal.

2. En el Diseñador de Logic Apps, en el desencadenador o la acción, elija **Nuevo paso**.

   ![Agregar una acción](./media/connectors-create-api-azureblobstorage/add-action.png) 

   Para agregar una acción entre los pasos existentes, mueva el mouse sobre la flecha de conexión. Elija el signo más ( **+** ) que aparece y seleccione **Agregar una acción**.

3. En el cuadro de búsqueda, escriba "azure blob" como filtro. En la lista de acciones, seleccione la acción que desee.

   Este ejemplo utiliza esta acción: **Obtener contenido de blog**

   ![Acción Select](./media/connectors-create-api-azureblobstorage/azure-blob-action.png)

4. Si se le piden los detalles de la conexión, [cree ahora su conexión de Azure Blob Storage](#create-connection).
O bien, si la conexión ya existe, especifique la información necesaria para la acción.

   En este ejemplo, seleccione el archivo que desee.

   1. En el cuadro **Blob**, seleccione el icono de la carpeta.
  
      ![Seleccionar carpeta](./media/connectors-create-api-azureblobstorage/action-select-folder.png)

   2. Busque el archivo que desee por el número de **identificador** del blob y selecciónelo. Dicho número de **identificador** se puede encontrar en los metadatos del blob que devuelve el desencadenador de almacenamiento del blob descrito anteriormente.

5. Cuando esté listo, elija **Guardar** en la barra de herramientas del diseñador.
Para probar la aplicación lógica, asegúrese de que la carpeta seleccionada contiene un blob.

En este ejemplo solo se obtiene el contenido de un blob. Para ver dicho contenido, agregue otra acción que cree un archivo con el blob mediante otro conector. Por ejemplo, agregue una acción de OneDrive que cree un archivo basándose en el contenido del blob.

<a name="create-connection"></a>

## <a name="connect-to-storage-account"></a>Conectar con la cuenta de almacenamiento

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to Azure blob storage](../../includes/connectors-create-api-azureblobstorage.md)]

## <a name="connector-reference"></a>Referencia de conectores

Para obtener detalles técnicos, como los desencadenadores, las acciones y los límites, tal como lo describe el archivo Open API (antes Swagger) del conector, consulte la [página de referencia del conector](/connectors/azureblobconnector/).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)
