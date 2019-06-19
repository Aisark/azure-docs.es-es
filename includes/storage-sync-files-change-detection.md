---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: beb08c29587e4ce522131142fd61925b5af45fa9
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67186385"
---
Los cambios realizados en el recurso compartido de archivos de Azure mediante Azure Portal o SMB no se detectan y replican de forma inmediata como cambios en el punto de conexión del servidor. Azure Files aún no dispone de registros en diario o notificaciones, por lo que no hay manera de iniciar automáticamente una sesión de sincronización cuando se cambian los archivos. En Windows Server, Azure File Sync usa el [registro en diario de USN de Windows](https://msdn.microsoft.com/library/windows/desktop/aa363798.aspx) para iniciar automáticamente una sesión de sincronización cuando cambian los archivos.<br /><br /> Para detectar cambios en el recurso compartido de archivos de Azure, Azure File Sync tiene un trabajo programado que se denomina *trabajo de detección de cambios*. Un trabajo de detección de cambios enumera todos los archivos del recurso compartido de archivos y, a continuación, los compara con la versión de sincronización correspondiente. Cuando el trabajo de detección de cambios determina qué archivos han cambiado, Azure File Sync inicia una sesión de sincronización. El trabajo de detección de cambios se inicia cada 24 horas. Dado que el trabajo de detección de cambios enumera todos los archivos del recurso compartido de archivos de Azure, la detección de cambios tarda más en los espacios de nombres más largos que los espacios de nombres más cortos. En el caso de los espacios de nombres largos, es posible que sea necesario determinar más de una vez cada 24 horas qué archivos han cambiado.<br /><br />
Tenga en cuenta que los cambios realizados en un recurso compartido de archivos de Azure con REST no actualizan la hora de la última modificación y no se verán como un cambio por la sincronización. <br /><br />
Estamos considerando agregar la detección de cambios para un recurso compartido de archivos de Azure similar al USN para volúmenes en Windows Server. Ayúdenos a priorizar el futuro desarrollo de esta característica votándola en [UserVoice de Azure Files](https://feedback.azure.com/forums/217298-storage/category/180670-files).
