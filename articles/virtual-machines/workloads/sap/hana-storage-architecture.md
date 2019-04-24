---
title: Arquitectura de almacenamiento de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Arquitectura de almacenamiento para implementar SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 02272ee16cf3303890a8ba6d35d38676e98c788c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60478657"
---
# <a name="sap-hana-large-instances-storage-architecture"></a>Arquitectura de almacenamiento de SAP HANA (instancias grandes)

El diseño de almacenamiento para SAP HANA en Azure (instancias grandes) es configurado por SAP HANA en el modelo de implementación clásica según las instrucciones recomendadas de SAP. Las instrucciones se documentan en las notas del producto [Requisitos de almacenamiento de SAP HANA](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html).

La clase de tipo I de HANA (instancias grandes) suele tener cuatro veces el volumen de memoria como volumen de almacenamiento. Para las unidades de HANA (instancias grandes) de clase de tipo II, el almacenamiento no es cuatro veces superior. Las unidades tienen un volumen previsto para almacenar copias de seguridad del registro de transacciones de HANA. Para más información, consulte [Instalación y configuración de SAP HANA en Azure (instancias grandes)](hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Vea la tabla siguiente en relación a la asignación de almacenamiento. En la tabla se enumera la capacidad aproximada de los diferentes volúmenes que se proporcionan con las diferentes unidades de HANA (instancias grandes).

| SKU de Instancias grandes de HANA | hana/data | hana/log | hana/shared | hana/logbackups |
| --- | --- | --- | --- | --- |
| S72 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S72m | 3 328 GB | 768 GB |1 280 GB | 768 GB |
| S96 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| S192 | 4 608 GB | 1 024 GB | 1 536 GB | 1 024 GB |
| S192m | 11.520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S192xm |  11.520 GB |  1 536 GB |  1 792 GB |  1 536 GB |
| S384 | 11.520 GB | 1 536 GB | 1 792 GB | 1 536 GB |
| S384m | 12.000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xm | 16.000 GB | 2 050 GB | 2 050 GB | 2 040 GB |
| S384xxm |  20.000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576m | 20.000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S576xm | 31 744 GB | 4 096 GB | 2 048 GB | 4 096 GB |
| S768m | 28.000 GB | 3 100 GB | 2 050 GB | 3 100 GB |
| S768xm | 40 960 GB | 6 144 GB | 4 096 GB | 6 144 GB |
| S960m | 36.000 GB | 4 100 GB | 2 050 GB | 4 100 GB |


Los volúmenes implementados reales pueden variar en función de la implementación y la herramienta que se usa para mostrar el tamaño de los volúmenes.

Si subdivide una SKU de HANA (instancias grandes), algunos ejemplos de los posibles fragmentos de división serían como sigue:

| Partición de memoria en GB | hana/data | hana/log | hana/shared | hana/log/backup |
| --- | --- | --- | --- | --- |
| 256 | 400 GB | 160 GB | 304 GB | 160 GB |
| 512 | 768 GB | 384 GB | 512 GB | 384 GB |
| 768 | 1 280 GB | 512 GB | 768 GB | 512 GB |
| 1024 | 1 792 GB | 640 GB | 1 024 GB | 640 GB |
| 1536 | 3 328 GB | 768 GB | 1 280 GB | 768 GB |


Estos tamaños son números de volumen aproximados que pueden variar ligeramente en función de la implementación y las herramientas que se usan para analizar los volúmenes. También hay otros tamaños de partición, como 2,5 TB. Estos tamaños de almacenamiento se calculan con una fórmula similar a la utilizada para las particiones anteriores. El término "particiones" no indica en absoluto que el sistema operativo, la memoria o los recursos de CPU se dividan en particiones. Indica las particiones de almacenamiento para las distintas instancias de HANA que es posible que quiera implementar en una única unidad de HANA (instancias grandes). 

Podría necesitar más almacenamiento. Puede agregar almacenamiento comprando almacenamiento adicional en unidades de 1 TB. Este almacenamiento adicional se puede agregar como volumen adicional. También se puede utilizar para extender uno o varios de los volúmenes existentes. No es posible reducir el tamaño de los volúmenes que implementó originalmente y que se documentan principalmente en las tablas anteriores. Tampoco es posible cambiar los nombres de los volúmenes ni los de montaje. Los volúmenes de almacenamiento descritos anteriormente se conectan a las unidades de HANA (instancias grandes) como volúmenes NFS4.

Puede usar instantáneas de almacenamiento para la copia de seguridad y la restauración, además de para la recuperación ante desastres. Para más información, consulte [Alta disponibilidad y recuperación ante desastres de SAP HANA en Azure (instancias grandes)](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Consulte los [escenarios admitidos por HLI](hana-supported-scenario.md) para información detallada sobre la distribución de almacenamiento en su caso.

## <a name="run-multiple-sap-hana-instances-on-one-hana-large-instance-unit"></a>Ejecución de varias instancias de SAP HANA en una unidad de HANA (instancias grandes)

Se puede hospedar más de una instancia de SAP HANA activa en las unidades de HANA (instancias grandes). Para proporcionar las funcionalidades de instantáneas de almacenamiento y recuperación ante desastres, esta configuración requiere un volumen por cada instancia. Actualmente, las unidades de HANA (instancias grandes) se pueden subdividir de esta forma:

- **S72, S72m, S96, S144, S192**: En incrementos de 256 GB con la unidad más pequeña inicial de 256 GB. Se pueden combinar incrementos diferentes, como 256 GB y 512 GB, hasta el máximo de memoria de la unidad.
- **S144m y S192m**: En incrementos de 256 GB con la unidad más pequeña de 512 GB. Se pueden combinar incrementos diferentes, como 512 GB y 768 GB, hasta el máximo de memoria de la unidad.
- **Clase de tipo II**: En incrementos de 512 GB, con la unidad más pequeña inicial de 2 TB. Se pueden combinar incrementos diferentes, como 512 GB, 1 GB y 1,5 GB, hasta el máximo de memoria de la unidad.

Algunos ejemplos de la ejecución de varias instancias de SAP HANA tendrían este aspecto.

| SKU | Tamaño de memoria | Tamaño de almacenamiento | Tamaños con varias bases de datos |
| --- | --- | --- | --- |
| S72 | 768 GB | 3 TB | 1 instancia de HANA de 768 GB<br /> o 1 instancia de 512 GB + 1 instancia de 256 GB<br /> o 3 instancias de 256 GB | 
| S72m | 1,5 TB | 6 TB | 3 instancias de HANA de 512 GB<br />o 1 instancia de 512 GB + 1 instancia de 1 TB<br />o 6 instancias de 256 GB<br />o 1 instancia de 1,5 TB | 
| S192m | 4 TB | 16 TB | 8 instancias de 512 GB<br />o 4 instancias de 1 TB<br />o 4 instancias de 512 GB + 2 instancias de 1 TB<br />o 4 instancias de 768 GB + 2 instancias de 512 GB<br />o 1 instancia de 4 TB |
| S384xm | 8 TB | 22 TB | 4 instancias de 2 TB<br />o 2 instancias de 4 TB<br />o 2 instancias de 3 TB + 1 instancia de 2 TB<br />o 2 instancias de 2,5 TB + 1 instancia de 3 TB<br />o 1 instancia de 8 TB |


Hay también otras variaciones. 

## <a name="encryption-of-data-at-rest"></a>Cifrado de datos en reposo
El almacenamiento usado para HANA (instancias grandes) permite un cifrado transparente de los datos cuando se almacenan en los discos. Cuando se implementa una unidad de HANA (instancias grandes), puede habilitar a este tipo de cifrado. También puede cambiar a volúmenes cifrados después de que se complete la implementación. El movimiento de volúmenes no cifrados a cifrados es transparente y no requiere ningún tiempo de inactividad. 

Con las SKU de clase de tipo I, se cifra el volumen en el que está almacenado el LUN de arranque. En el caso de las SKU de clase de tipo II de HANA (instancias grandes), tendrá que cifrar el LUN de arranque con los métodos del sistema operativo. Para más información, póngase en contacto con el equipo de Microsoft Service Management.

## <a name="required-settings-for-larger-hana-instances-on-hana-large-instances"></a>Configuración requerida para las instancias más grandes de HANA en instancias grandes de HANA
El almacenamiento usado en instancias grandes de HANA tiene una limitación de tamaño de archivo. El [limitación de tamaño es de 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) por archivo. A diferencia de las limitaciones de tamaño de archivo en los sistemas de archivos EXT3, HANA no es consciente implícitamente de la limitación de almacenamiento exigida por el almacenamiento de instancias grandes de HANA. Como resultado HANA no creará automáticamente un nuevo archivo de datos cuando se alcanza el límite de tamaño de archivo de 16TB. Como HANA intenta aumentar el archivo más allá de 16 TB, HANA se notificará errores y el servidor de indexación se bloquearán al final.

> [!IMPORTANT]
> Con el fin de evitar intentar aumentar el límite de tamaño de archivo de 16 TB de almacenamiento de instancia grande de HANA más allá de los archivos de datos HANA, tiene que establecer los parámetros siguientes en el archivo de configuración global.ini de HANA
> 
> - datavolume_striping=true
> - datavolume_striping_size_gb = 15000
> - Vea también SAP nota [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Tenga en cuenta la nota de SAP [#2631285](https://launchpad.support.sap.com/#/notes/2631285)




**Pasos siguientes**
- Consulte [Escenarios admitidos para instancias grandes de HANA](hana-supported-scenario.md).