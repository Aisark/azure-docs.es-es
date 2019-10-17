---
title: Creación de una instancia de Azure HPC Cache (versión preliminar)
description: Creación de una instancia de Azure HPC Cache
author: ekpgh
ms.service: hpc-cache
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: rohogue
ms.openlocfilehash: fa6408d33504c6ac73eacec079e9c08acd4cece2
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255269"
---
# <a name="plan-the-aggregated-namespace"></a>Planeamiento del espacio de nombres agregado

Azure HPC Cache (versión preliminar) permite que los clientes accedan a diversos sistemas de almacenamiento a través de un espacio de nombres virtual que oculta los detalles del sistema de almacenamiento back-end.

Cuando agrega un destino de almacenamiento, establece la ruta de acceso de archivo orientada al cliente. Las máquinas cliente montan esta ruta de acceso de archivo y pueden crear solicitudes de lectura de archivos en la memoria caché en lugar de montar el sistema de almacenamiento directamente.

Dado que la memoria caché de Azure HPC Cache administra este sistema de archivos virtual, puede cambiar el destino de almacenamiento sin cambiar la ruta de acceso orientada al cliente. Por ejemplo, podría reemplazar un sistema de almacenamiento en hardware por almacenamiento en la nube sin necesidad de volver a escribir los procedimientos orientados al cliente.

## <a name="aggregated-namespace-example"></a>Ejemplo de espacio de nombres agregado

Planee el espacio de nombres agregado para que los equipos cliente puedan alcanzar de forma sencilla la información que necesitan, y los administradores y los ingenieros de flujo de trabajo pueden distinguir fácilmente las rutas de acceso.

Por ejemplo, supongamos que cuenta con un sistema donde se usa una instancia de Azure HPC Cache para procesar los datos almacenados en Azure Blob Storage. El análisis necesita archivos de plantilla almacenados en un centro de datos local.

Los datos de plantilla se almacenan en un centro de datos y la información necesaria para este trabajo se almacena en estos subdirectorios:

    /goldline/templates/acme2017/sku798
    /goldline/templates/acme2017/sku980 

El sistema de almacenamiento del centro de datos expone estas exportaciones:

    /
    /goldline
    /goldline/templates

Los datos que se van a analizar se han copiado en un contenedor de Azure Blob Storage denominado "sourcecollection" mediante la [utilidad CLFSLoad](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload).

Para permitir un acceso sencillo a través de la memoria caché, puede crear destinos de almacenamiento con estas rutas de acceso de espacio de nombres virtual:

| Sistema de almacenamiento de back-end <br/> (ruta de acceso de archivos NFS o contenedor de blobs) | Ruta de acceso del espacio de nombres virtual |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| recopilaciónDeOrigen                        | /source/               |

Un destino de almacenamiento NFS puede tener varias rutas de acceso de espacio de nombres virtual, siempre y cuando cada una haga referencia a una ruta de acceso de exportación única.

Dado que las rutas de acceso de origen NFS son subdirectorios de la misma exportación, deberá definir varias rutas de acceso de espacio de nombres desde el mismo destino de almacenamiento.

| Nombre de host de destino de almacenamiento  | Ruta de exportación NFS      | Ruta de acceso del subdirectorio | Ruta de acceso del espacio de nombres    |
|--------------------------|----------------------|-------------------|-------------------|
| *Dirección IP o nombre de host* | /goldline/templates  | acme2017/sku798   | /templates/sku798 |
| *Dirección IP o nombre de host* | /goldline/templates  | acme2017/sku980   | /templates/sku980 |

Una aplicación cliente puede montar la memoria caché y acceder fácilmente a las rutas de archivo del espacio de nombres agregado ``/source``, ``/templates/sku798`` y ``/templates/sku980``.

## <a name="next-steps"></a>Pasos siguientes

Después de decidir cómo configurar el sistema de archivos virtual, [cree destinos de almacenamiento](hpc-cache-add-storage.md) para asignar el almacenamiento de back-end a las rutas de archivo virtuales orientadas al cliente.
