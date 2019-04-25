---
title: Información sobre la nube por niveles de Azure File Sync | Microsoft Docs
description: Obtenga información sobre la característica Nube por niveles de Azure File Sync
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 871eb1663d6cba550f1403215b1d3ce5fe8278d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444946"
---
# <a name="cloud-tiering-overview"></a>Información general de nube por niveles
La nube por niveles es una característica opcional de Azure File Sync por la que los archivos a los que se tiene acceso con frecuencia se almacenan en caché localmente en el servidor mientras que todos los demás archivos se organizan en niveles en Azure Files, según la configuración de directiva. Cuando un archivo está en capas, el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) sustituye al archivo localmente por un puntero o punto de repetición de análisis. El punto de repetición de análisis representa una dirección URL del archivo en Azure Files. Un archivo con niveles tiene los atributos “sin conexión” y FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS establecidos en NTFS para que las aplicaciones de terceros puedan identificar de forma segura archivos con niveles.
 
Cuando un usuario abre un archivo con niveles, el servicio Azure File Sync recupera completamente los datos de archivo de Azure Files sin necesidad de que el usuario sepa que el archivo está almacenado en Azure. 
 
 > [!Important]  
 > La nube por niveles no se admite para los puntos de conexión de servidor en los volúmenes del sistema de Windows y solo se pueden organizar por niveles en Azure Files los archivos mayores de 64 KiB.
    
Azure File Sync no admite la organización por niveles de archivos menores de 64 KiB ya que la sobrecarga de rendimiento de organizar por niveles y recuperar estos archivos pequeños superaría con creces los ahorros de espacio.

 > [!Important]  
 > Para recuperar los archivos que se ha organizado en niveles, el ancho de banda de red debe ser al menos 1 Mbps. Si el ancho de banda de red es menor que 1 Mbps, los archivos pueden producir un error al recuperar un error de tiempo de espera.

## <a name="cloud-tiering-faq"></a>Preguntas frecuentes de la nube por niveles

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>¿Cómo funciona la nube por niveles?
El filtro del sistema de Azure File Sync se basa en un “mapa térmico” del espacio de nombres en cada punto de conexión de servidor. Supervisa los accesos (operaciones de lectura y escritura) a lo largo del tiempo y después, en función de la frecuencia y la cercanía en el tiempo del acceso, asigna una puntuación de calor a todos los archivos. Un archivo al que se accede con frecuencia que se ha abierto recientemente se considerará como caliente, mientras que se considerará frío un archivo que apenas se toca y al que no se ha accedido desde hace algún tiempo. Cuando el volumen de archivos en un servidor supera el umbral de espacio disponible del volumen que estableció, organizará por niveles los archivos más fríos en Azure Files hasta que se cumpla el porcentaje de espacio libre.

En las versiones 4.0 y posteriores del agente de Azure File Sync, también se puede especificar una directiva de fecha en cada punto de conexión del servidor que clasificará los archivos que no se modifiquen o a los que no se acceda en un número de días especificado.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>¿Cómo funciona la directiva de organización por niveles de espacio disponible del volumen?
El espacio disponible del volumen es la cantidad de espacio libre que desea reservar en el volumen en el que reside un punto de conexión de servidor. Por ejemplo, si el espacio disponible del volumen se establece en 20 % en un volumen que tiene un punto de conexión de servidor, hasta un 80 % del espacio de volumen permanecerá ocupado por los archivos a los que se ha accedido más recientemente, mientras que los archivos restantes que no caben dentro de este espacio se organizan en niveles en Azure. El espacio disponible del volumen se aplica en el nivel de volumen en lugar de en el nivel de directorios individuales o en lo grupos de sincronización. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>¿Cómo funciona la directiva de organización en niveles del espacio disponible de volumen con respecto a los nuevos puntos de conexión de servidor?
Cuando un punto de conexión de servidor acaba de aprovisionarse y conectarse a un recurso compartido de archivos de Azure, el servidor primero extraerá el espacio de nombres y después los archivos hasta que llegue a su umbral de espacio disponible del volumen. Este proceso también se denomina restauración rápida del espacio de nombres o recuperación ante desastres rápida.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>¿Cómo se interpreta el espacio disponible del volumen cuando tengo varios puntos de conexión de servidor en un volumen?
Cuando hay más de un punto de conexión de servidor en un volumen, el umbral de espacio disponible del volumen efectivo es el mayor espacio disponible del volumen especificado en cualquier punto de conexión de servidor de ese volumen. Los archivos se pueden almacenar en capas según sus patrones de uso, independientemente de qué punto de conexión de servidor al que pertenezcan. Por ejemplo, si tiene dos puntos de conexión de servidor en un volumen, Endpoint1 y Endpoint2, donde Endpoint1 tiene un umbral de espacio disponible del volumen del 25 %, y Endpoint2 tiene un umbral de espacio disponible del volumen de 50 %, el umbral del volumen de espacio disponible para ambos puntos de conexión de servidor será el 50 %. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>¿Cómo funciona la directiva de organización por niveles basada en fechas junto con la directiva de organización por niveles del espacio disponible de volumen? 
Cuando se habilita la nube por niveles en el punto de conexión de un servidor, se establece una directiva de espacio disponible en el volumen. Esta directiva siempre tiene prioridad sobre cualquier otra, incluidas las directivas de fecha. Si quiere, puede habilitar una directiva de fechas para cada punto de conexión de servidor en ese volumen, de forma que solo los archivos a los que accede (mediante lectura o escritura) durante el intervalo de días especificados en la directiva se mantienen de forma local, y los archivos obsoletos se organizan por niveles. Tenga en cuenta que la directiva de espacio disponible en el volumen siempre tiene prioridad, y cuando no haya espacio suficiente en el volumen para conservar todos los archivos que especifica la directiva de fechas, Azure File Sync seguirá organizando por niveles los archivos más antiguos hasta alcanzar el porcentaje de espacio libre en el volumen.

Por ejemplo, supongamos que tiene una directiva de organización por niveles basada en fechas con un valor de 60 días y una directiva de espacio disponible en el volumen del 20 %. Si tras aplicar la directiva de fechas el espacio disponible en el volumen es menor al 20 %, la directiva de espacio libre se pondrá en marcha y anulará la directiva de fechas. Como resultado, se organizarán por niveles más archivos, de forma tal que la cantidad de datos almacenados en el servidor se reduzca quizá de 60 a 45 días de datos. En cambio, la directiva forzará la organización por niveles de los archivos que no coincidan con el intervalo de tiempo, incluso si no ha alcanzado el umbral de espacio libre; es decir, un archivo con una antigüedad de 61 días se organizará por niveles incluso si el volumen está vacío.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>¿Cómo se puede determinar la cantidad adecuada de espacio disponible del volumen?
La cantidad de datos que se debe mantener en el entorno local viene determinada por una serie de factores: el ancho de banda, el patrón de acceso del conjunto de datos y su presupuesto. Si tiene una conexión con un ancho de banda bajo, desea mantener más datos en su entorno local para asegurarse de que hay un retraso mínimo para los usuarios. En caso contrario, puede basarlo en la tasa de renovación durante un período determinado. Por ejemplo, si sabe que se accede activamente o cambia aproximadamente el 10 % del conjunto de datos de 1 TB cada mes, le interesa mantener en el entorno local 100 GB para no tener que recuperar archivos con frecuencia. Si el volumen es de 2 TB, desea mantener el 5 % (o 100 GB) en el entorno local, lo que significa que el 95 % restante es el porcentaje de espacio disponible del volumen. No obstante, se recomienda que agregue un búfer para tener en cuenta los períodos de mayor renovación, es decir, partir de un porcentaje de espacio disponible del volumen inferior y ajustarlo posteriormente si es necesario. 

Mantener más datos en el entorno local implica menores costos de salida ya que se recuperarán menos archivos de Azure, pero también es necesario mantener una mayor cantidad de almacenamiento local, lo que tiene su propio costo. Una vez que tenga una instancia de Azure File Sync implementada, puede mirar la salida de su cuenta de almacenamiento para valorar aproximadamente si la configuración de espacio disponible del volumen es adecuada para su uso. Suponiendo que la cuenta de almacenamiento contiene solo el archivo punto de conexión de nube de Azure File Sync (es decir, el recurso compartido de sincronización), una salida alta significa que mucho archivos se recuperan desde la nube y debe plantearse aumentar la memoria caché local.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>He agregado un nuevo punto de conexión de servidor. ¿Cuánto tiempo transcurrirá para que los archivos de este servidor se organicen por niveles?
En las versiones 4.0 y posteriores del agente de Azure File Sync, una vez que los archivos se han cargado al recurso compartido de archivos de Azure, dichos archivos se organizan por niveles según lo especificado en las directivas tan pronto como se ejecuta la siguiente sesión de organización, que se ejecuta una vez cada hora. En los agentes anteriores, la organización por niveles puede tardar hasta 24 horas en ocurrir.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>¿Cómo se puede saber si un archivo se ha organizado en niveles?
Hay varias maneras de comprobar si un archivo se ha organizado en niveles en el recurso compartido de archivos de Azure:
    
   *  **Compruebe los atributos de archivo en el archivo.**
     Haga clic con el botón derecho en un archivo, vaya a **Detalles** y desplácese hacia abajo a la propiedad **Atributos**. Un archivo en niveles tendrá los siguientes atributos establecidos:     
        
        | Letra del atributo | Atributo | Definición |
        |:----------------:|-----------|------------|
        | Una  | Archivar | Indica que se debe realizar la copia de seguridad del archivo con un software de copia de seguridad. Este atributo siempre se establece, independientemente de si el archivo está organizado en niveles o está completamente almacenado en el disco. |
        | P | Archivos dispersos | Indica que el archivo es un archivo disperso. Un archivo disperso es un tipo especializado de archivo que ofrece NTFS para un uso eficaz cuando el archivo en el flujo del disco está en gran parte vacío. Azure File Sync usa archivos dispersos porque un archivo o está completamente organizado en niveles o parcialmente recuperado. En un archivo completamente organizado en niveles, su flujo de archivos se almacena en la nube. En un archivo parcialmente recuperado, esa parte del archivo ya está en el disco. Si un archivo está totalmente recuperado en el disco, Azure File Sync lo convierte de un archivo disperso a un archivo normal. |
        | L | Punto de repetición de análisis | Indica que el archivo tiene un punto de repetición de análisis. Un punto de repetición de análisis es un puntero especial para su uso en un filtro del sistema de archivos. Azure File Sync usa puntos de repetición de análisis a fin de definir para el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) la ubicación en la nube donde está almacenado el archivo. Esto permite el acceso sin problemas. Los usuarios no necesitan saber que se está usando Azure File Sync ni cómo obtener acceso al archivo en el recurso compartido de archivos de Azure. Cuando un archivo completo se recupera, Azure File Sync quita el punto de repetición de análisis del archivo. |
        | O | Sin conexión | Indica que parte del contenido del archivo, o la totalidad, no se ha almacenado en el disco. Cuando un archivo completo se recupera, Azure File Sync quita este atributo. |

        ![Cuadro de diálogo Propiedades de un archivo con la pestaña Detalles seleccionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Puede ver los atributos de todos los archivos en una carpeta mediante la adición del campo **Atributos** a la presentación de la tabla del Explorador de archivos. Para ello, haga clic con el botón derecho en una columna existente (por ejemplo, **Tamaño**), seleccione **Más** y, después, **Atributos** en la lista desplegable.
        
   * **Use `fsutil` para buscar puntos de repetición de análisis en un archivo.**
       Como se indica en la opción anterior, un archivo organizado en niveles siempre tiene establecido un punto de repetición de análisis. Un puntero de repetición de análisis es un puntero especial para el filtro del sistema de archivos de Azure File Sync (StorageSync.sys). Para comprobar si un archivo tiene un punto de repetición de análisis, en un símbolo del sistema con privilegios elevados o en una ventana de PowerShell, ejecute la utilidad `fsutil`:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Si el archivo tiene un punto de repetición de análisis, debería ver **Valor de la etiqueta de análisis: 0x8000001e**. Este valor hexadecimal es el valor del punto de repetición de análisis que pertenece a Azure File Sync. La salida también contendrá los datos de repetición de análisis que representan la ruta de acceso al archivo en el recurso compartido de archivos de Azure.

        > [!WARNING]  
        > El comando de la utilidad `fsutil reparsepoint` también puede eliminar un punto de repetición de análisis. No ejecute este comando a menos que el equipo de ingeniería de Azure File Sync se lo indique. Si lo ejecuta, podría producirse una pérdida de datos. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Uno de los archivos que quiero usar se ha organizado en niveles. ¿Cómo puedo recuperarlo en el disco para usarlo de forma local?
La manera más fácil de recuperar un archivo en el disco es abrirlo. El filtro del sistema de archivos de Azure File Sync (StorageSync.sys) descarga sin problemas el archivo desde el recurso compartido de archivos de Azure de forma automática. Para los tipos de archivo que se pueden leer parcialmente, como los archivos multimedia o los archivos .zip, si se abre un archivo, este no se descargará entero.

También puede usar PowerShell para forzar que se recupere un archivo. Esta opción puede resultar útil cuando se quiere recuperar muchos archivos a la vez, como todos los archivos de una carpeta. Abra una sesión de PowerShell en el nodo del servidor donde está instalado Azure File Sync y ejecute los siguientes comandos de PowerShell:
    
    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>¿Por qué la propiedad *Tamaño en disco* de un archivo no coincide con la propiedad *Tamaño* después de usar Azure File Sync? 
El Explorador de archivos de Windows expone dos propiedades para representar el tamaño de un archivo: **Tamaño** y **Tamaño en disco**. Estas propiedades difieren ligeramente en significado. **Tamaño** representa el tamaño completo del archivo. **Tamaño en disco** representa el tamaño del flujo de archivos que se almacena en el disco. Los valores de estas propiedades pueden diferir por diversos motivos, como la compresión, el uso de la desduplicación de datos o los niveles en la nube con Azure File Sync. Si un archivo está organizado en niveles en un recurso compartido de archivos de Azure, el tamaño en disco será cero, ya que la secuencia de archivos se almacena en el recurso compartido de archivos de Azure, no en el disco. También es posible que un archivo se encuentre parcialmente organizado en niveles (o parcialmente recuperado). En el caso de un archivo parcialmente organizado en niveles, parte del archivo está en el disco. Esto puede ocurrir cuando aplicaciones como reproductores multimedia o utilidades zip leen parcialmente los archivos. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>¿Cómo puedo forzar la organización en niveles de un archivo o directorio?
Cuando se habilita la característica de organización en niveles en la nube, se crean automáticamente niveles en los archivos según la última hora de acceso y de modificación a fin de conseguir el porcentaje de espacio libre en el volumen especificado en el punto de conexión en la nube. Aun así, en ocasiones podría querer forzar un archivo para organizarse en niveles de forma manual. Esto puede resultar útil cuando se guarda un archivo grande que no piensa volver a usar durante mucho tiempo y quiere espacio libre en el volumen ahora para usarlo para otros archivos o carpetas. Puede forzar la organización en niveles con los siguientes comandos de PowerShell:

    ```powershell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Pasos siguientes
* [Planeamiento de una implementación de Azure File Sync](storage-sync-files-planning.md)
