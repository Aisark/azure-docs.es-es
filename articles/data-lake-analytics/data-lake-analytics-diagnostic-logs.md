---
title: Habilitar y ver los registros de diagnóstico de Azure Data Lake Analytics
description: Sepa cómo configurar registros de diagnóstico y tener acceso a ellos para Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: jasonwhowell
ms.author: jasonh
ms.assetid: cf5633d4-bc43-444e-90fc-f90fbd0b7935
ms.topic: conceptual
ms.date: 02/12/2018
ms.openlocfilehash: 7fd88383e909ebd6be64c22721b813946e37179e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2019
ms.locfileid: "56959143"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Acceso a los registros de diagnóstico de Azure Data Lake Analytics

Los registros de diagnóstico le permiten recopilar seguimientos de auditoría de acceso a datos. Estos registros proporcionan información como:

* Una lista de los usuarios que tienen acceso a los datos.
* Frecuencia con la que se accede a los datos.
* Cantidad de datos que se almacena en la cuenta.

## <a name="enable-logging"></a>Habilitación del registro

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Abra su cuenta de Data Lake Analytics y seleccione **Registros de diagnósticos** en la __sección Supervisión__. Después, seleccione __Activar diagnósticos__.

    ![Activar los diagnósticos para recopilar registros de solicitudes y auditoría](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. En __Configuración de diagnóstico__, escriba un __Nombre__ para esta configuración de registro y, a continuación, seleccione las opciones de registro.

    ![Activar los diagnósticos para recopilar registros de solicitudes y auditoría](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Habilitar registros de diagnósticos")

   * Puede optar por almacenar o procesar los datos de tres maneras diferentes.

     * Seleccione __Archive to a storage account__ (Archivar en cuenta de almacenamiento) para almacenar los registros en una cuenta de almacenamiento de Azure. Utilice esta opción si desea archivar los datos. Si selecciona esta opción, debe proporcionar una cuenta de almacenamiento de Azure para guardar los registros.

     * Seleccione la opción **Transmitir a un centro de eventos** para transmitir los datos de registro a un Centro de eventos de Azure. Use esta opción si tiene una canalización de procesamiento de bajada que está analizando los registros entrantes en tiempo real. Si selecciona esta opción, debe proporcionar los detalles del Centro de eventos de Azure que quiera usar.

     * Seleccione __enviar a Log Analytics__ para enviar los datos para el servicio Azure Monitor. Use esta opción si desea usar los registros de Azure Monitor para recopilar y analizar registros.
   * Indique si quiere obtener los registros de auditoría, los registros de solicitudes o ambos.  Un registro de solicitudes captura todas las solicitudes de API. Un registro de auditoría registra todas las operaciones que esa solicitud de API desencadena.

   * Para __Archivar en una cuenta de almacenamiento__, especifique el número de días que se retendrán los datos.

   * Haga clic en __Save__(Guardar).

        > [!NOTE]
        > Tiene que seleccionar __Archivar en una cuenta de almacenamiento__, __Transmitir a un centro de eventos__ o __Enviar a Log Analytics__ antes de hacer clic en el botón __Guardar__.

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Uso de la cuenta de Azure Storage que contiene los datos de registro

1. Para mostrar los contenedores de blobs que contienen datos de registro, abra la cuenta Azure Storage usada para Data Lake Analytics para el registro y, a continuación, haga clic en __Blobs__.

   * El contenedor **insights-logs-audit** contiene los registros de auditoría.
   * El contenedor **insights-logs-requests** contiene los registros de solicitudes.

2. Dentro de los contenedores, los registros se almacenan con la siguiente estructura de archivo:

        resourceId=/
          SUBSCRIPTIONS/
            <<SUBSCRIPTION_ID>>/
              RESOURCEGROUPS/
                <<RESOURCE_GRP_NAME>>/
                  PROVIDERS/
                    MICROSOFT.DATALAKEANALYTICS/
                      ACCOUNTS/
                        <DATA_LAKE_ANALYTICS_NAME>>/
                          y=####/
                            m=##/
                              d=##/
                                h=##/
                                  m=00/
                                    PT1H.json

   > [!NOTE]
   > La hoja `##` de la ruta de acceso contienen el año, mes, día y hora en que se creó el registro. Data Lake Analytics crea un archivo cada hora, por lo que `m=` siempre contiene un valor de `00`.

    Por ejemplo, la ruta de acceso completa a un registro de auditoría sería:

        https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json

    De forma similar, la ruta de acceso completa a un registro de solicitudes sería:

        https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json

## <a name="log-structure"></a>Estructura de los registros

Los registros de auditoría y de solicitud tienen un formato JSON.

### <a name="request-logs"></a>Request Logs

Este es un ejemplo de una entrada en el registro de solicitud con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-07T21:02:53.456Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
             "category": "Requests",
             "operationName": "GetAggregatedJobHistory",
             "resultType": "200",
             "callerIpAddress": "::ffff:1.1.1.1",
             "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
             "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
             "properties": {
                 "HttpMethod":"POST",
                 "Path":"/JobAggregatedHistory",
                 "RequestContentLength":122,
                 "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
                 "StartTime":"2016-07-07T21:02:52.472Z",
                 "EndTime":"2016-07-07T21:02:53.456Z"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="request-log-schema"></a>Esquema de un registro de solicitud

| NOMBRE | Type | DESCRIPCIÓN |
| --- | --- | --- |
| Twitter en tiempo |string |Marca de tiempo (en UTC) del registro. |
| ResourceId |string |Identificador del recurso en el que tuvo lugar la operación |
| categoría |string |Categoría del registro. Por ejemplo, **Requests**. |
| operationName |string |Nombre de la operación que se registra. Por ejemplo, GetAggregatedJobHistory. |
| resultType |string |Estado de la operación. Por ejemplo, 200. |
| callerIpAddress |string |Dirección IP del cliente que realiza la solicitud. |
| correlationId |string |Identificador del registro. Este valor puede utilizarse para agrupar un conjunto de entradas de registro relacionadas. |
| identidad |Objeto |Identidad que ha generado el registro. |
| propiedades |JSON |Consulte la siguiente sección (Esquema de propiedades de un registro de solicitud) para más información |

#### <a name="request-log-properties-schema"></a>Esquema de propiedades de un registro de solicitud

| NOMBRE | Type | DESCRIPCIÓN |
| --- | --- | --- |
| HttpMethod |string |Método HTTP usado en la operación. Por ejemplo, GET. |
| Ruta de acceso |string |Ruta de acceso en la que se ha realizado la operación. |
| RequestContentLength |int |Longitud del contenido de la solicitud HTTP. |
| ClientRequestId |string |El identificador que distingue de manera exclusiva esta solicitud |
| StartTime |string |Hora a la que el servidor ha recibido la solicitud. |
| EndTime |string |Hora a la que el servidor ha enviado una respuesta. |

### <a name="audit-logs"></a>Registros de auditoría

Este es un ejemplo de una entrada en el registro de auditoría con formato JSON. Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.

    {
    "records":
      [        
        . . . .
        ,
        {
             "time": "2016-07-28T19:15:16.245Z",
             "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
             "category": "Audit",
             "operationName": "JobSubmitted",
             "identity": "user@somewhere.com",
             "properties": {
                 "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
                 "JobName": "New Job",
                 "JobRuntimeName": "default",
                 "SubmitTime": "7/28/2016 7:14:57 PM"
                 }
        }
        ,
        . . . .
      ]
    }

#### <a name="audit-log-schema"></a>Esquema de un registro de auditoría

| NOMBRE | Type | DESCRIPCIÓN |
| --- | --- | --- |
| Twitter en tiempo |string |Marca de tiempo (en UTC) del registro. |
| ResourceId |string |Identificador del recurso en el que tuvo lugar la operación |
| categoría |string |Categoría del registro. Por ejemplo, **Audit**. |
| operationName |string |Nombre de la operación que se registra. Por ejemplo, JobSubmitted. |
| resultType |string |Un subestado para el estado del trabajo (operationName). |
| resultSignature |string |Detalles adicionales sobre el estado del trabajo (operationName). |
| identidad |string |El usuario que solicitó la operación. Por ejemplo, susan@contoso.com. |
| propiedades |JSON |Consulte la siguiente sección (Esquema de propiedades de un registro de auditoría) para más información |

> [!NOTE]
> **resultType** y **resultSignature** proporcionan información sobre el resultado de una operación y solo contienen un valor si se ha completado una operación. Por ejemplo, solo contienen un valor cuando **operationName** contiene un valor de **JobStarted** o **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Esquema de propiedades de un registro de auditoría

| NOMBRE | Type | DESCRIPCIÓN |
| --- | --- | --- |
| JobId |string |El identificador asignado al trabajo |
| JobName |string |El nombre que se proporcionó para el trabajo |
| JobRunTime |string |El tiempo de ejecución utilizado para procesar el trabajo |
| SubmitTime |string |La hora (en UTC) en la que se envió el trabajo |
| StartTime |string |La hora en la que el trabajo empezó a ejecutarse tras el envío (en UTC) |
| EndTime |string |La hora de finalización del trabajo |
| Paralelismo |string |El número de unidades de Data Lake Analytics solicitadas para este trabajo durante el envío |

> [!NOTE]
> **SubmitTime**, **StartTime**, **EndTime** y **Parallelism** proporcionan información sobre una operación. Estas entradas solo contienen un valor si la operación se ha iniciado o completado. Por ejemplo, **SubmitTime** contiene un valor después de que **operationName** tenga el valor **JobSubmitted**.

## <a name="process-the-log-data"></a>Procesamiento de los datos de registro

Azure Data Lake Analytics proporciona un ejemplo de cómo procesar y analizar los datos de registro. Puede encontrar el ejemplo en [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample).

## <a name="next-steps"></a>Pasos siguientes
* [Información general de Azure Data Lake Analytics](data-lake-analytics-overview.md)
