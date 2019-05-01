---
title: Lectura de datos capturados de la aplicación de Python en Azure Event Hubs | Microsoft Docs
description: Ejemplo que usa el SDK de Azure para Python a fin de demostrar el uso de la característica Event Hubs Capture.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 639bc4ff9c69bca3d5f8bca6967bfc3e8e6a13d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60822407"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Tutorial de Event Hubs Capture: Python

Capture es una característica de Azure Event Hubs. Puede usarla para enviar automáticamente los datos de streaming que hay en un centro de eventos a la cuenta de Azure Blob Storage que prefiera. Esto facilita el procesamiento por lotes en datos de streaming en tiempo real. En este artículo se describe cómo utilizar Event Hubs Capture con Python. Para más información acerca de Event Hubs Capture, consulte este [artículo con información general al respecto](event-hubs-capture-overview.md).

En este ejemplo se usa el [SDK de Azure para Python](https://azure.microsoft.com/develop/python/) a fin de demostrar la característica Capture. El programa sender.py envía datos telemétricos del entorno simulados a Event Hubs en formato JSON. El centro de eventos está configurado para usar la característica Capture para escribir estos datos en Blob Storage en lotes. La aplicación capturereader.py lee estos blobs y crea un archivo de anexos por dispositivo. Luego, la aplicación escribe los datos en archivos .csv.

## <a name="what-youll-accomplish"></a>Podrá:

1. Crear una cuenta de Azure Blob Storage y un contenedor de blobs en ella, para lo que se debe usar Azure Portal.
2. Crear un espacio de nombres de Event Hubs desde Azure Portal.
3. Crear un centro de eventos con la característica Capture habilitada desde Azure Portal.
4. Enviar datos al centro de eventos con un script de Python.
5. Leer los archivos de la captura y procesarlos con otro script de Python.

## <a name="prerequisites"></a>Requisitos previos

- Python 2.7.x
- Una suscripción de Azure. Si no tiene una, [cree una cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
- Un [espacio de nombres de Event Hubs y un centro de eventos](event-hubs-create.md) activos. 
- Habilitar la característica **Capturar** en el centro de eventos siguiendo las instrucciones de: [Habilitación de Event Hubs Capture mediante Azure Portal](event-hubs-capture-enable-through-portal.md)

## <a name="create-an-azure-blob-storage-account"></a>Creación de una cuenta de Almacenamiento de blobs de Azure
1. Inicie sesión en [Azure Portal][Azure portal].
2. En el panel izquierdo del portal, seleccione **Nuevo** > **Storage** > **Cuenta de almacenamiento**.
3. Complete las selecciones del panel **Crear cuenta de almacenamiento** y, a continuación, seleccione **Crear**.
   
   ![Panel "Crear cuenta de almacenamiento"][1]
4. Una vez que aparezca el mensaje **Implementaciones correctas**, haga clic en el nombre de la nueva cuenta de almacenamiento y, en el panel **Essentials**, haga clic en **Blobs**. Cuando se abra el panel **Blob service**, haga clic en **+ Contenedor** en la parte superior. Asigne al contenedor el nombre **capture** y cierre el panel **Blob service**.
5. Haga clic en **Claves de acceso** en el panel izquierdo y copie el nombre de la cuenta de almacenamiento y el valor de **key1**. Guarde estos valores en el Bloc de notas, o en cualquier otra ubicación temporal.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Creación de un script de Python para enviar eventos a un centro de eventos
1. Abra el editor de Python que prefiera, como [Visual Studio Code][Visual Studio Code].
2. Crear un script denominado **sender.py**. Este script le envía 200 eventos a un centro de eventos. Son lecturas simples del entorno enviadas en JSON.
3. Pegue el código siguiente en sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Actualice el código anterior para que use el nombre del espacio de nombres, el valor de clave y el nombre del centro de eventos que obtuviera cuando se creó el espacio de nombres de Event Hubs.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Creación de un script de Python que lea archivos de Capture

1. Rellene el panel y seleccione **Crear**.
2. Cree un script denominado **capturereader.py**. Este script lee los archivos capturados y crea un archivo por dispositivo para escribir los datos solo para dicho dispositivo.
3. Pegue el código siguiente en capturereader.py:
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not dict.has_key(parsed_json['id']):
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print 'Processor started using path: ' + os.getcwd()
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = string.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')
   ```
4. Pegue los valores adecuados del nombre y la clave de su cuenta de almacenamiento en la llamada a `startProcessing`.

## <a name="run-the-scripts"></a>Ejecución de los scripts
1. Abra un símbolo del sistema que tiene Python en su ruta de acceso y, después, ejecute dichos comandos para instalar los paquetes de requisitos previos de Python:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Si tiene una versión anterior de **azure-storage** o **azure**, puede que tenga que utilizar la opción **--upgrade**.
   
   Es posible que también deba ejecutar el siguiente comando (no es necesario en la mayoría de los sistemas):
   
   ```
   pip install cryptography
   ```
2. Cambie el directorio a la ubicación en que guardó sender.py y capturereader.py, y ejecute este comando:
   
   ```
   start python sender.py
   ```
   
   Este comando inicia un nuevo proceso de Python para ejecutar el remitente.
3. Espere unos minutos para que se ejecute la captura. Después, escriba el siguiente comando en la ventana de comandos original:
   
   ```
   python capturereader.py
   ```

   Este procesador de captura usa el directorio local para descargar todos los blobs del contenedor o de la cuenta de captura. Procesa los que no estén vacíos y escribe los resultados en forma de archivos .csv en el directorio local.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs Capture][Overview of Event Hubs Capture]
* [Aplicaciones de ejemplo que usan Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Información general de Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
