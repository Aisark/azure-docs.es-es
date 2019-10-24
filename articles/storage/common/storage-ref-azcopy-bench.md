---
title: azcopy bench | Microsoft Docs
description: En este artículo se proporciona información de referencia del comando azcopy bench.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 10/16/2019
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 8570bce87aeea5473b4aadf9bd30bc0a648a6f0f
ms.sourcegitcommit: 12de9c927bc63868168056c39ccaa16d44cdc646
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72518128"
---
# <a name="azcopy-bench"></a>azcopy bench

Este comando ejecuta una prueba comparativa de rendimiento mediante la carga de datos de prueba en un destino especificado. Los datos de prueba se generan automáticamente.

El comando de prueba comparativa ejecuta el mismo proceso de carga que "copy", con las siguientes excepciones:

  - No tiene ningún parámetro de origen.  El comando solo requiere una dirección URL de destino. En la versión actual, esta dirección URL de destino debe hacer referencia a un contenedor de blobs.
  
  - La carga útil se describe mediante parámetros de la línea de comandos, que controlan el número de archivos que se generan automáticamente y su tamaño. El proceso de generación se realiza por completo en la memoria. No se utiliza el disco.
  
  - Solo se admiten unos pocos de los parámetros opcionales que están disponibles para el comando copy.
  
  - Se miden y se notifican diagnósticos adicionales.
  
  - De forma predeterminada, los datos transferidos se eliminan al final de la serie de pruebas.

El modo de prueba comparativa se ajustará automáticamente al número de conexiones TCP paralelas que proporcione el máximo rendimiento. Mostrará ese número al final. Para evitar el ajuste automático, establezca la variable de entorno AZCOPY_CONCURRENCY_VALUE en un número específico de conexiones.

Se admiten todos los tipos de autenticación habituales. Sin embargo, suele ser más conveniente para las pruebas comparativas crear un contenedor vacío con un token de SAS y usar la autenticación de SAS.

## <a name="examples"></a>Ejemplos

```azcopy
azcopy bench [destination] [flags]
```

Ejecución de una prueba comparativa con parámetros predeterminados (adecuada para las pruebas comparativas de redes de hasta 1 Gbps):'

- azcopy bench "https://[cuenta].blob.core.windows.net/[contenedor]?<SAS>"

Ejecución de una prueba comparativa que carga 100 archivos, cada uno con 2 GiB de tamaño (adecuada para las pruebas comparativas en una red rápida; por ejemplo, 10 Gbps):'

- azcopy bench "https://[cuenta].blob.core.windows.net/[contenedor]?<SAS>" --file-count 100 --size-per-file 2G

Igual que la anterior, pero usa 50 000 archivos, cada uno con 8 MiB de tamaño, y procesa sus códigos hash MD5 (de la misma forma que la marca --put-md5 lo hace en el comando copy). El propósito de --put-md5 al realizar pruebas comparativas es comprobar si el cálculo de MD5 afecta a la capacidad de proceso del número y tamaño de archivos seleccionados:

- azcopy bench "https://[cuenta].blob.core.windows.net/[contenedor]?<SAS>" --file-count 50000 --size-per-file 8M --put-md5

## <a name="options"></a>Opciones

**--blob-type** string. Define el tipo de blob del destino. Se usa para permitir las pruebas comparativas entre distintos tipos de blob. Idéntico al parámetro del mismo nombre en el comando copy (valor predeterminado "Detectar").

**--block-size-mb** float. Utiliza este tamaño de bloque (especificado en MiB). El valor predeterminado se calcula automáticamente en función del tamaño del archivo. Se permiten fracciones decimales (por ejemplo: 0,25). Idéntico al parámetro del mismo nombre del comando copy.

**--delete-test-data**. Si es true, los datos de la prueba comparativa se eliminarán al final de la ejecución de la prueba.  Establézcalo en false si desea mantener los datos en el destino; por ejemplo, para utilizarlos en pruebas manuales fuera del modo de prueba comparativa (el valor predeterminado es true).

**--file-count** uint. Número de archivos de datos generados automáticamente que se usarán (el valor predeterminado es 100).

**-h, --help**. Ayuda para la prueba.

**--log-level** string. Define el nivel de detalle para el archivo de registro; los niveles disponibles son: INFO (todas las solicitudes y respuestas), WARNING (respuestas lentas), ERROR (solo solicitudes con error) y NONE (sin registros de salida). (Valor predeterminado: "INFO").

**--put-md5**. Crea un código hash MD5 de cada archivo y lo guarda como la propiedad Content-MD5 del blob o archivo de destino. (De forma predeterminada, NO se crea el hash). Idéntico al parámetro del mismo nombre del comando copy.

**--size-per-file**  string. Tamaño de cada archivo de datos generado automáticamente. Debe ser un número inmediatamente seguido de K, M o G; por ejemplo: 12k o 200G (el valor predeterminado es "250M").

## <a name="options-inherited-from-parent-commands"></a>Opciones heredadas de comandos primarios

**--cap-mbps uint32**. Limita la velocidad de transferencia, en megabits por segundo. El rendimiento en un momento dado puede variar ligeramente del límite. Si esta opción se establece en cero o se omite, el rendimiento no se limita.

**--output-type**  string. Formato de la salida del comando. Las opciones incluyen: text, json. El valor predeterminado es "text". (Valor predeterminado: "text").

## <a name="see-also"></a>Otras referencias

- [azcopy](storage-ref-azcopy.md)
