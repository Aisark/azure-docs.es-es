---
title: Opciones de transferencia de datos de Azure para pequeños conjuntos de datos con un ancho de banda de red de bajo a moderado | Microsoft Docs
description: Obtenga información sobre cómo elegir una solución de Azure para la transferencia de datos cuando tenga ancho de banda de red de bajo a moderado en su entorno y vaya a transferir pequeños conjuntos de datos.
services: storage
author: alkohli
ms.service: storage
ms.subservice: blobs
ms.topic: article
ms.date: 12/05/2018
ms.author: alkohli
ms.openlocfilehash: 3e6f4f3eb312f0d4d96a008c0944a9608d0bf4a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397284"
---
# <a name="data-transfer-for-small-datasets-with-low-to-moderate-network-bandwidth"></a>Transferencia de pequeños conjuntos de datos con ancho de banda de red de bajo a moderado
 
En este artículo encontrará información general sobre las soluciones para la transferencia de datos cuando tiene un ancho de banda de red de bajo a moderado en su entorno y vaya a transferir pequeños conjuntos de datos. En el artículo también se describen las opciones recomendadas de transferencia de datos y la matriz de funcionalidades clave para este escenario.

Para obtener una visión general de todas las opciones de transferencia de datos disponibles, vaya a [Choose an Azure data transfer solution](storage-choose-data-transfer-solution.md) (Elección de una solución de transferencia de datos de Azure).

## <a name="scenario-description"></a>Descripción del escenario

Los conjuntos de datos pequeños hacen referencia a tamaños de datos en el orden de GB a unos cuantos TB. El ancho de banda de red de bajo a moderado implica un tamaño de 45 Mbps (conexión T3 en el centro de datos) a 1 Gbps.

- Si está transfiriendo solo unos pocos archivos y no necesita automatizar la transferencia de datos, puede usar las herramientas con una interfaz gráfica.
- Si se siente cómodo con la administración del sistema, puede usar la línea de comandos o las herramientas de programación o scripting.

## <a name="recommended-options"></a>Opciones recomendadas

Las opciones recomendadas en este escenario son las siguientes:

- **Herramientas de interfaz gráfica**: como Azure Storage Explorer y Azure Storage en el Azure Portal. Estas proporcionan una manera fácil de ver sus datos y transferir rápidamente algunos archivos.

    - **Explorador de Azure Storage**: es una herramienta multiplataforma que permite administrar el contenido de las cuentas de Azure Storage. Permite cargar, descargar y administrar blogs, archivos, colas, tablas y entidades de Azure Cosmos DB. Utilícelo con Blob Storage para administrar blobs y carpetas, así como para cargar y descargar blobs entre el sistema de archivos local y Blob Storage, o entre cuentas de almacenamiento.
    - **Azure Portal**: Azure Storage en Azure Portal proporciona una interfaz basada en la web que sirve para explorar archivos y cargar nuevos archivos de uno en uno. Es una buena opción si no desea instalar herramientas ni generar comandos para explorar rápidamente los archivos, o simplemente cargar archivos nuevos.

- **Herramientas de scripting y programación**: como AzCopy, PowerShell, la CLI de Azure y las API REST de Azure Storage.

    - **AzCopy**: use esta herramienta de línea de comandos para copiar fácilmente datos desde y hacia Azure Blobs, Files y Table Storage con un rendimiento óptimo. AzCopy admite la simultaneidad y el paralelismo, y permite reanudar operaciones de copia cuando si se interrumpen.
    - **Azure PowerShell**: para usuarios que se sienten cómodos con la administración del sistema, use el módulo de Azure Storage en Azure PowerShell para transferir datos.
    - **CLI de Azure**: use esta herramienta multiplataforma para poder administrar los servicios de Azure y cargar datos en Azure Storage.
    - **API/SDK REST de Azure Storage**: al crear una aplicación, puede desarrollarla en función de las API REST y los SDK de Azure Storage y usar las bibliotecas de cliente de Azure que se ofrecen en varios lenguajes.


## <a name="comparison-of-key-capabilities"></a>Comparación de funcionalidades clave

En la tabla siguiente se resumen las diferencias de las funcionalidades clave.

| Característica | Explorador de Azure Storage | Azure Portal | AzCopy<br>Azure PowerShell<br>Azure CLI | API REST o SDK de Azure Storage |
|---------|------------------------|--------------|-----------------------------------------|---------------------------------|
| Disponibilidad | Descargar e instalar <br>Herramientas independientes | Herramientas de exploración de Azure Portal basadas en la web | Texto de línea de comandos |Interfaces programables en. NET, Java, Python, JavaScript, C++, Go, Ruby y PHP |
| Interfaz gráfica | Sí | Sí | No | Sin  |
| Plataformas compatibles | Windows, Mac, Linux | Basadas en web |Windows, Mac, Linux |Todas las plataformas |
| Operaciones de Blob Storage permitidas<br>para blobs y carpetas | Cargar<br>Descargar<br>administración | Cargar<br>Descargar<br>administración |Cargar<br>Descargar<br>administración | Sí, es personalizable |
| Almacenamiento de Data Lake Gen1 permitido<br>operaciones de archivos y carpetas | Cargar<br>Descargar<br>administración | Sin  |Cargar<br>Descargar<br>administración                   | Sin  |
| Operaciones de File Storage permitidas<br>para archivos y directorios | Cargar<br>Descargar<br>administración | Cargar<br>Descargar<br>administración   |Cargar<br>Descargar<br>administración | Sí, es personalizable |
| Operaciones de Table Storage permitidas<br>para tablas |administración | Sin  |Compatibilidad con tablas en AzCopy v7 |Sí, es personalizable|
| Instancia de Queue Storage permitida | administración | Sin   |Sin  | Sí, es personalizable|


## <a name="next-steps"></a>Pasos siguientes

- Aprenda a [transferir datos con el Explorador de Azure Storage](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-data-to-azure-blob-using-azure-storage-explorer).
- [Transferir datos con AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)

