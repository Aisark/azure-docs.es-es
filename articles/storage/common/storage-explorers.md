---
title: Herramientas para trabajar con Azure Storage
description: Lista de herramientas que le permiten ver datos de Azure Storage e interactuar con ellos.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 08/26/2019
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 11838a50d70d1b9a0216505e9ef0958d3b18bdac
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2019
ms.locfileid: "70035261"
---
# <a name="azure-storage-client-tools"></a>Herramientas de cliente de Azure Storage
Los usuarios de Azure Storage con frecuencia desean poder ver o interactuar con sus datos mediante una herramienta de cliente de Azure Storage. En las tablas siguientes, mostramos un número de herramientas que le permiten hacer esto. Escribimos una "X" en cada bloque si permite enumerar la abstracción de los datos o tener acceso a ella. La tabla también muestra si las herramientas están disponibles. "Versión de prueba" indica que cuenta con una evaluación gratuita, pero que el producto completo no es gratuito. "S/N" indica que hay disponible una versión gratuita, mientras que la versión disponible para la compra es diferente.

Únicamente proporcionamos una instantánea de las herramientas de cliente de Azure Storage. Estas herramientas pueden seguir evolucionando y ampliando su funcionalidad. Si existen correcciones o actualizaciones, le agradeceremos que nos lo haga saber a través de un comentario. De igual manera, si conoce otras herramientas que deberíamos mostrar, estaremos encantados de agregarlas.

**Herramientas de cliente de Microsoft Azure Storage**

<table>
  <tr>
    <th rowspan="2">Herramienta de cliente de Azure Storage</th>
    <th rowspan="2">Blob en bloques</th>
    <th rowspan="2">Blob en páginas</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">Tablas</th>
    <th rowspan="2">Colas</th>
    <th rowspan="2">Archivos</th>
    <th rowspan="2">Gratuito</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://azure.microsoft.com/features/azure-portal/">Microsoft Azure Portal</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://storageexplorer.com/">Explorador de Microsoft Azure Storage</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
  </tr>
  <tr>
    <td><a href="https://www.visualstudio.com/features/azure-tools-vs.aspx">Explorador de servidores de Microsoft Visual Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>

**Herramientas de cliente de Azure Storage de terceros**

No hemos comprobado la funcionalidad ni la calidad proporcionadas por las siguientes herramientas de terceros; el hecho de que figuren en esta lista no implica su aprobación por parte de Microsoft.

<table>
  <tr>
    <th rowspan="2">Herramienta de cliente de Azure Storage</th>
    <th rowspan="2">Blob en bloques</th>
    <th rowspan="2">Blob en páginas</th>
    <th rowspan="2">Append Blob</th>
    <th rowspan="2">Tablas</th>
    <th rowspan="2">Colas</th>
    <th rowspan="2">Archivos</th>
    <th rowspan="2">Gratuito</th>
    <th colspan="4">Plataforma</th>
  </tr>
  <tr>
    <td>Web</td>
    <td>Windows</td>
    <td>OSX</td>
    <td>Linux</td>
  </tr>
  <tr>
    <td><a href="https://www.cerebrata.com/products/azure-management-studio/introduction">Cerabrata: Azure Management Studio</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Versión de prueba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://github.com/sebagomez/azurestorageexplorer">Azure Web Storage Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.cloudberrylab.com/explorer/microsoft-azure.aspx">CloudBerry Explorer</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td>X</td>
    <td>S/N</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gapotchenko.com/cloudcombine">Cloud Combine</a></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>X</td>
    <td>X</td>
    <td></td>
    <td>Versión de prueba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://clumsyleaf.com">ClumsyLeaf: AzureXplorer, CloudXplorer, TableXplorer</a></td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>X</td>
    <td>Y</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td><a href="https://www.gladinet.com/Azure-Storage/index.htm">Gladinet Cloud</a></td>
    <td>X</td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td></td>
    <td>Versión de prueba</td>
    <td></td>
    <td>X</td>
    <td></td>
    <td></td>
  </tr>
</table>
