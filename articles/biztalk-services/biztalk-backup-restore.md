---
title: Creación y restauración de una copia de seguridad en BizTalk Services | Microsoft Docs
description: Entre BizTalk Services se incluye Copias de seguridad y restauración. Obtenga información acerca de cómo crear y restaurar una copia de seguridad y aprenda a determinar el contenido del que se realizan dichas copias. MABS, WABS
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: ee86b9aa2d920668cf036f3e8f8634e9289e8913
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916876"
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Copia de seguridad y restauración

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk Services incluye las capacidades de copia de seguridad y restauración. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> NO se realiza ninguna copia de seguridad de las conexiones híbridas, independientemente de la edición. Debe volver a crear las conexiones híbridas.


## <a name="before-you-begin"></a>Introducción
* Puede que las copias de seguridad y restauración no estén disponible para todas las ediciones. Consulte [BizTalk Services: Gráfico de ediciones](biztalk-editions-feature-chart.md).
* El contenido de las copias de seguridad se puede restaurar en el mismo servicio de BizTalk o en uno nuevo. Para restaurar el servicio de BizTalk con el mismo nombre, es preciso eliminar el servicio de BizTalk existente y el nombre debe estar disponible. Después de eliminar un servicio de BizTalk, puede tardar más tiempo del deseado para que el mismo nombre esté disponible. Si no puede esperar a que el mismo nombre esté disponible, restaure a un nuevo servicio de BizTalk.
* Se pueden restaurar BizTalk Services en la misma edición o en una posterior. No se puede realizar la restauración de BizTalk Services en una edición anterior con respecto a la usada para realizar la copia de seguridad.
  
    Por ejemplo, una copia de seguridad que usa la Basic Edition se puede restaurar a la Premium Edition. Sin embargo, una copia de seguridad que usa la Premium Edition no se puede restaurar a la Standard Edition.
* A fin de mantener la continuidad de los números de control del intercambio electrónico de datos (EDI), estos se incluyen en una copia de seguridad. Si los mensajes se procesan después de la última copia de seguridad y se restaura el contenido de esta copia de seguridad, se pueden duplicar los números de control.
* Si un lote tiene mensajes activos, procéselo **antes** de ejecutar una copia de seguridad. Al crear una copia de seguridad (según sea necesario o según se programe), no se almacenan nunca los mensajes en lotes. 
  
    **Si no se realiza una copia de seguridad con mensajes activos en un lote, estos mensajes no son una copia de seguridad y, por tanto, se pierden.**
* Opcional: En el Portal de BizTalk Services, detenga las operaciones de administración.

## <a name="create-a-backup"></a>Creación de una copia de seguridad
Puede realizar una copia de seguridad en cualquier momento y controlarla por completo. Para crear una copia de seguridad, use la [API de REST de administración de BizTalk Services en Azure](/previous-versions/azure/reference/dn232347(v=azure.100)).

## <a name="restore"></a>Restauración
Para restaurar una copia de seguridad, use la [API de REST de administración de BizTalk Services en Azure](/previous-versions/azure/reference/dn232347(v=azure.100)).

### <a name="postrestore"></a>Después de restaurar una copia de seguridad
El servicio de BizTalk siempre está restaurado en un estado **Suspendido** . En este estado, puede realizar cambios de configuración antes de que el nuevo entorno sea funcional, incluyendo:

* Si ha creado aplicaciones del servicio de BizTalk mediante el SDK de Azure BizTalk Services, puede que tenga que actualizar las credenciales de control de acceso (ACS) en dichas aplicaciones para que puedan funcionar con el entorno restaurado.
* Restaure un Servicio de BizTalk para replicar un entorno de Servicio de BizTalk existente. En esta situación, si hay contratos configurados en el portal de BizTalk Services original que usa una carpeta FTP de origen, quizá desee actualizar los contratos del entorno recién restaurado para que usen una carpeta FTP con otro origen. De lo contrario, puede haber dos contratos diferentes intentando extraer el mismo mensaje.
* Si ha llevado a cabo la restauración para tener varios entornos del Servicio de BizTalk, asegúrese de elegir el entorno correcto en las aplicaciones de Visual Studio, los cmdlets de PowerShell, las API de REST o las API de OM de administración de socios comerciales.
* Es recomendable configurar copias de seguridad automatizadas en el entorno del Servicio de BizTalk recién restaurado.

## <a name="what-gets-backed-up"></a>¿Qué se incluye en la copia de seguridad?
Se incluyen los elementos siguientes al crear una copia de seguridad:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Elementos incluidos en la copia de seguridad</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Portal de Azure BizTalk Services</strong></td>
</tr> 
<tr>
<td>Configuración y tiempo de ejecución</td> 
<td>
<ul>
<li>Detalles del asociado y perfil</li>
<li>Acuerdos de socios</li>
<li>Ensamblados personalizados implementados</li>
<li>Puentes implementados</li>
<li>Certificados</li>
<li>Transformaciones implementadas</li>
<li>Procesos</li>
<li>Plantillas creadas y guardadas en el portal de BizTalk Services</li>
<li>Asignaciones X12 ST01 y GS01</li>
<li>Números de control (EDI)</li>
<li>Valores MIC de mensaje AS2</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk Service</strong></td>
</tr> 
<tr>
<td>Certificado SSL</td> 
<td>
<ul>
<li>Datos del certificado SSL</li>
<li>Contraseña del certificado SSL</li>
</ul>
</td>
</tr> 
<tr>
<td>Configuración del servicio de BizTalk</td> 
<td>
<ul>
<li>Recuento de unidades de escalado</li>
<li>Edition</li>
<li>Versión del producto</li>
<li>Región/Centro de datos</li>
<li>Clave y espacio de nombres del servicio de control de acceso (ACS)</li>
<li>Cadena de conexión de la base de datos de seguimiento</li>
<li>Cadena de conexión de la cuenta de almacenamiento de archivado</li>
<li>Cadena de conexión de la cuenta de almacenamiento de supervisión</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Elementos adicionales</strong></td>
</tr> 
<tr>
<td>Tracking Database</td> 
<td>Cuando se crea el Servicio de BizTalk, se definen los detalles de la base de datos de seguimiento, entre los que se incluyen el servidor de Azure SQL Database y el nombre de la base de datos de seguimiento. No se realiza la copia de seguridad de la base de datos de seguimiento de forma automática.
<br/><br/>
<strong>Importante</strong><br/>
Si la base de datos de seguimiento se elimina y hay que recuperarla, debe existir una copia de seguridad previa. Si no la hay, no se podrán recuperar la base de datos de seguimiento ni sus datos. En esta situación, cree una nueva base de datos de seguimiento con el mismo nombre. Además, se recomienda la replicación geográfica.</td>
</tr> 
</table>

## <a name="next"></a>Pasos siguientes
Para crear Azure BizTalk Services, vaya a [BizTalk Services: Aprovisionamiento](https://go.microsoft.com/fwlink/p/?LinkID=302280). Para comenzar a crear aplicaciones, vaya a [Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Vea también
* [Servicio de copia de seguridad de BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Restaurar el servicio de BizTalk desde la copia de seguridad](https://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk Services: Developer, Basic, Standard y Premium gráfico de ediciones](https://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk Services: Aprovisionamiento](https://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Gráfico del estado de aprovisionamiento](https://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Pestañas panel, Monitor y escala](https://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Limitaciones](https://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Nombre del emisor y clave del emisor](https://go.microsoft.com/fwlink/p/?LinkID=303941)
* [¿Cómo puedo comenzar a utilizar el SDK de Azure BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

