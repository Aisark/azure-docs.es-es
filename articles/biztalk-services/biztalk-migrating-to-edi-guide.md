---
title: Migración de soluciones EDI de BizTalk Server a BizTalk Services | Microsoft Docs
description: Aprenda a migrar las soluciones EDI de Microsoft BizTalk Server para Microsoft Azure BizTalk Services (MABS)
services: biztalk-services
ms.service: biztalk-services
author: jonfancey
ms.author: jonfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: jonfan, LADocs
ms.suite: integration
ms.openlocfilehash: 5543fd5ee2b86a57414a384df9d808e87b297a5e
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983037"
---
# <a name="migrate-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migrar soluciones EDI de BizTalk Server a BizTalk Services: Guía técnica

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: TIM Wieman y Nitin Mehrotra

Revisores: Karthik Bharthy

Escrito utilizando:  Microsoft Azure BizTalk Services: versión de febrero de 2014.

## <a name="introduction"></a>Introducción
Intercambio electrónico de datos (EDI) es uno de los medios más frecuentes mediante los que las empresas intercambian datos electrónicamente. También se llaman transacciones entre empresas o B2B. BizTalk Server lleva teniendo compatibilidad con EDI más de una década, desde su primera versión. Con BizTalk Services, Microsoft continúa la compatibilidad con las soluciones EDI en la Plataforma Microsoft Azure. Las transacciones B2B son mayormente externas a una organización y, por tanto, resultan más fáciles de implementar si se han implementado en una plataforma en la nube. Microsoft Azure proporciona esta capacidad a través de BizTalk Services.

Aunque algunos clientes consideran BizTalk Services como una plataforma "virgen" para nuevas soluciones EDI, muchos tienen soluciones EDI de BizTalk Server actuales que pueden migrar a Azure. Porque EDI de BizTalk Services está diseñado según las mismas entidades clave que la arquitectura de EDI de BizTalk Server (socios comerciales, entidades, contratos), es posible migrar artefactos de EDI de BizTalk Server a BizTalk Services.

Este documento explica algunas de las diferencias de migrar los artefactos de EDI de BizTalk Server a BizTalk Services. En este documento se da por hecho que se dispone de un conocimiento práctico del procesamiento de EDI de BizTalk Server y de los contratos de socios comerciales. Para obtener más información sobre EDI de BizTalk Server, consulte [Administración de socios comerciales mediante BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>¿Qué versión de los artefactos de EDI de BizTalk Server se puede migrar a BizTalk Services?
El módulo de EDI de BizTalk Server se ha mejorado significativamente para BizTalk Server 2010, cuando se rediseñó para incluir socios, perfiles y contratos. BizTalk Services usa el mismo modelo para organizar los socios comerciales y las divisiones de negocio situadas dentro de esos socios comerciales. Como resultado, la migración de artefactos EDI de BizTalk Server 2010 y versiones posteriores a BizTalk Services es un proceso mucho más sencillo. Para migrar los artefactos EDI asociados a las versiones anteriores de BizTalk Server 2010, debe actualizar primero a BizTalk Server 2010 y, a continuación, migrar los artefactos de EDI a BizTalk Services.

## <a name="scenariosmessage-flow"></a>Escenarios y flujo de mensajes
Como con BizTalk Server, el procesamiento de EDI en BizTalk Services se basa en una solución de Administración de socios comerciales (TPM). La solución TPM tiene los siguientes componentes clave:

* Los socios comerciales, que representan una organización en una transacción B2B.
* Los perfiles, que representan divisiones dentro de un socio comercial.
* Contratos de socio comercial (o contratos), que representan el acuerdo comercial entre dos socios o perfiles.

En la siguiente ilustración se muestran las similitudes, así como las diferencias, entre una solución de EDI de BizTalk Server y una solución EDI de BizTalk Services:

![][EDImessageflow]

Las similitudes y diferencias clave entre un flujo de solución EDI en BizTalk Server y BizTalk Services son:

* Al igual que BizTalk Server usa una canalización EDIReceive para recibir un mensaje EDI y una canalización EDISend para enviar un mensaje EDI, BizTalk Services usa un puente de recepción EDI para recibir y un puente de envío EDI para enviar mensajes EDI. En BizTalk Server, las canalizaciones se asocian con un acuerdo mediante el uso de puertos de envío o de recepción. En BizTalk Services, el propio contrato denota el puente de envío o recepción.
* En BizTalk Server, una vez que la canalización EDIReceive procesa el mensaje EDI, el mensaje se vuelca en una base de datos de SQL Server. A continuación, la canalización EdiSend toma el mensaje de la base de datos de SQL Server, lo procesa y, a continuación, lo envía al socio comercial.
  
    En BizTalk Services, después de que el puente de recepción de EDI procesa el mensaje EDI, enruta el mensaje a un proceso externo. El proceso externo podría ejecutarse en Microsoft Azure o de forma local. El proceso externo debe enrutar el mensaje al puente de envío EDI; el puente de envío no lo extrae intrínsecamente. Después de procesar el mensaje, el puente de envío EDI enruta el mensaje al socio comercial.

BizTalk Services proporciona una experiencia de configuración fácil de usar para crear e implementar rápidamente un contrato B2B entre socios comerciales sin configurar cualquier instancia de proceso de Microsoft Azure (roles web o de trabajo), las bases de datos de Microsoft Azure SQL Database o las cuentas de Microsoft Azure Storage. Escenarios más complejos requerirán enlazar flujos de trabajo u otro procesamiento de servicio "en torno a los bordes" de un acuerdo de socio comercial, es decir, antes o después del procesamiento de puente EDI de contrato de socio comercial. Con mayor detalle, las siguientes secuencias de eventos se producen durante un procesamiento de mensaje EDI en BizTalk Services.

1. Se recibe un mensaje EDI del socio comercial, Fabrikam.  Para recibir mensajes EDI de socios comerciales, BizTalk Services admiten protocolos de transporte como FTP, SFTP, AS2 y HTTP/S.
2. El procesamiento del lado de recepción del contrato de socio comercial desensambla el mensaje EDI en formato XML.  Puede enrutar el mensaje EDI desensamblado (en formato XML) a los puntos de conexión de Service Bus como un punto de conexión de Service Bus Relay, tema de Service Bus, cola de Service Bus o un puente de BizTalk Services.
3. Los mensajes XML desensamblados podrán recibirse entonces desde el extremo para efectuar un procesamiento más personalizado.  Estos extremos podrán ser procesados por un componente local o una instancia de proceso de Microsoft Azure para procesar el mensaje en un servicio de flujo de trabajo de Windows (WF) o Windows Communication Foundation (WCF), por ejemplo.
4. El "procesamiento de envío" del contrato del socio comercial ensambla a continuación el mensaje XML en formato EDI y lo envía al socio comercial, Contoso.  Para enviar mensajes EDI a socios comerciales, BizTalk Services admiten los mismos protocolos que los usados para recibir los mensajes EDI.

Este documento proporciona instrucciones conceptuales sobre la migración de algunos de los diferentes artefactos de EDI de BizTalk Server para BizTalk Services.

## <a name="sendreceive-ports-to-trading-partners"></a>Puertos de envío/recepción para socios comerciales
En BizTalk Server se configuran ubicaciones de recepción y puertos de recepción para recibir mensajes EDI/XML de socios comerciales y configurar puertos de envío para enviar los mensajes EDI/XML al socio comercial. A continuación se asocian estos puertos a un acuerdo entre socios comerciales mediante la consola de administración de BizTalk Server. En BizTalk Services, las ubicaciones en las que recibe los mensajes de los socios comerciales y donde envía mensajes a los socios comerciales se configuran como parte del acuerdo entre socios comerciales (como parte de la configuración de transporte) en el Portal de BizTalk Services.  Por lo tanto, realmente no se tiene el concepto de "puertos de envío" y "ubicaciones de recepción" en sí en BizTalk Services. Para obtener más información, consulte [Creación de contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Canalizaciones (puentes)
En EDI de BizTalk Server, las canalizaciones son entidades de procesamiento de mensajes que también pueden incluir una lógica personalizada para capacidades de procesamiento específicas, tal como requiere la aplicación. Para BizTalk Services, el equivalente sería un puente EDI. Sin embargo, en BizTalk Services, por ahora, los puentes EDI están "cerrados".  Es decir, no puede agregar sus propias actividades personalizadas a un puente EDI. Cualquier procesamiento personalizado debe hacerse fuera el puente EDI en la aplicación, ya sea antes o después de que el mensaje acceda al puente configurado como parte del contrato de socio comercial. Los puentes EAI tienen la opción de hacer un procesamiento personalizado. Si desea efectuar el procesamiento personalizado, puede usar puentes EAI antes o después de que el mensaje sea procesado por el puente EDI. Para obtener más información, consulte [Cómo incluir código personalizado en puentes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Puede insertar un flujo de publicación/suscripción con código personalizado y/o usar colas y temas de mensajería de Service Bus antes de que el contrato de socio comercial reciba el mensaje, o después de que el acuerdo procese el mensaje y lo enrute a un punto de conexión de Service Bus.

Consulte **Escenarios y flujo de mensajes** en este artículo para obtener el modelo de flujo de mensajes.

## <a name="agreements"></a>Contratos
Si está familiarizado con los contratos de socio comercial de BizTalk Server 2010 usados para el procesamiento de EDI, los contratos de socios comerciales de BizTalk Services le resultarán conocidos. La mayor parte de la configuración del acuerdo es la misma y usa la misma terminología. En algunos casos, la configuración del contrato es mucho más simple en comparación con la misma configuración de BizTalk Server. Microsoft Azure BizTalk Services es compatible con el transporte X12, EDIFACT y AS2.

Microsoft Azure BizTalk Services también proporciona una herramienta de **migración de datos de TPM** para migrar socios comerciales y contratos del módulo de socios comerciales de BizTalk Server al portal de BizTalk Services. La herramienta de migración de datos de TPM está disponible como parte de un paquete de herramientas, que puede descargarse del [SDK de MABS](https://go.microsoft.com/fwlink/p/?LinkId=235057). El paquete también incluye un archivo Léame que proporciona instrucciones sobre cómo usar la herramienta e información de solución de problemas básica de la herramienta.

## <a name="schemas"></a>Esquemas
BizTalk Services proporciona esquemas EDI que se pueden usar en soluciones de BizTalk Services.  Además, los esquemas EDI de BizTalk Server también se pueden usar con BizTalk Services porque el nodo raíz del esquema EDI es el mismo en BizTalk Server y en BizTalk Services. De este modo, podrá tomar los esquemas EDI de BizTalk Server directamente y usarlos en las soluciones EDI que desarrolle mediante BizTalk Services. También puede descargar los esquemas del [SDK MABS](https://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Asignaciones (transformaciones)
Las asignaciones en BizTalk Server se llaman transformaciones en BizTalk Services. Migrar asignaciones de BizTalk Server a BizTalk Services puede ser una de las tareas más complejas para realizar (según la complejidad de la asignación). La herramienta de asignación que se utiliza para BizTalk Services es diferente del asignador de BizTalk. Aunque el asignador parece casi igual, el formato de asignación subyacente es diferente. Los functoids (denominados **Operaciones de asignación** en BizTalk Services) disponibles para los usuarios también son diferentes.  De hecho, no se puede usar directamente una asignación de BizTalk en BizTalk Services. Además, no todos los functoids disponibles en BizTalk Server están disponibles como operaciones de asignación en BizTalk Services.

### <a name="new-transform-operations"></a>Nuevas operaciones de transformación
Aunque la lista de operaciones de asignación de transformación disponibles pueden ser muy diferentes de las del asignador de BizTalk Server, las transformaciones de BizTalk Services tienen formas nuevas de realizar las mismas tareas. Por ejemplo, las transformaciones de BizTalk Services tienen **operaciones de lista** disponibles. Esto no estaba disponible en el asignador de BizTalk.  Las **operaciones de lista** le permiten crear y operar en una "Lista", donde las listas son un conjunto de elementos (también conocido como "filas") y cada elemento puede tener varios miembros (también conocidos como "columnas").  Puede ordenar la lista, seleccionar elementos según una condición, etc.

Otro ejemplo de la nueva funcionalidad de las transformaciones de BizTalk Services son las **operaciones de bucle**.  Es difícil crear bucles anidados en el asignador de BizTalk Server.  Por lo tanto, las operaciones de asignación de bucle se agregan para las transformaciones de BizTalk Services.

Otro ejemplo es la operación de asignación de expresión **If-Then-Else** .  En el asignador BizTalk era posible realiza una operación de if-then-else, pero requería que varios functoids realizasen una tarea aparentemente simple.

### <a name="migrating-biztalk-server-maps"></a>Migración de asignaciones de BizTalk Server
Microsoft Azure BizTalk Services proporciona una herramienta de migración de asignaciones de BizTalk Server a transformaciones de BizTalk Services. **BTMMigrationTool** está disponible como parte del paquete de **Herramientas** que se incluye con la [descarga del SDK de BizTalk Services](https://go.microsoft.com/fwlink/p/?LinkId=235057). Para obtener más información acerca de la herramienta, consulte [Convertir una asignación de BizTalk en una transformación de BizTalk Services](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

También puede ver un ejemplo realizado por Sandro Pereira, MVP de BizTalk, sobre cómo [migrar asignaciones de BizTalk Server a transformaciones de BizTalk Services](https://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orquestaciones
Si necesita migrar a Microsoft Azure el procesamiento de la orquestación de BizTalk Server, las orquestaciones tendrían que reescribirse porque Microsoft Azure no admite la ejecución de las orquestaciones de BizTalk Server.  Puede volver a escribir la funcionalidad de orquestación en un servicio de Windows Workflow Foundation 4.0 (WF4).  Esto sería una reescritura completa, ya que no hay actualmente ninguna migración desde orquestaciones de BizTalk Server a WF4. Estos son algunos recursos para el flujo de trabajo de Windows:

* [*Integrar un servicio de flujo de trabajo de WCF con temas y colas de Service Bus*](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/), por Paolo Salvatori. 
* [Sesión *Generación de aplicaciones con Windows Workflow Foundation y Azure*](https://go.microsoft.com/fwlink/p/?LinkId=237314) de la conferencia Build de 2011.
* [*Centro para desarrolladores de Windows Workflow Foundation*](https://docs.microsoft.com/previous-versions/dotnet/articles/ee342461(v=msdn.10)).
* [*Documentación de Windows Workflow Foundation 4 (WF4)*](https://msdn.microsoft.com/library/dd489441.aspx) en MSDN.

## <a name="other-considerations"></a>Otras consideraciones
A continuación se facilitan algunas consideraciones que debe realizar durante el uso de BizTalk Services.

### <a name="fallback-agreements"></a>Contratos de reserva
El procesamiento de EDI de BizTalk Server tiene el concepto de "contratos de reserva".  BizTalk Services **no** tiene un concepto de contrato de reserva hasta ahora.  Vea los temas de la documentación de BizTalk [El rol de los contratos en el procesamiento de EDI](https://go.microsoft.com/fwlink/p/?LinkId=237317) y [Configuración de las propiedades del contrato global o de reserva](https://msdn.microsoft.com/library/bb245981.aspx) para obtener información sobre cómo se usan los acuerdos de reserva en BizTalk Server.

### <a name="routing-to-multiple-destinations"></a>Redirección a múltiples destinos
Los puentes de BizTalk Services, en su estado actual, no admiten enrutamiento de mensajes a varios destinos mediante un modelo publicación-suscripción. En su lugar se podrían enrutar mensajes de un puente de BizTalk Services a un tema de Service Bus, que puede tener varias suscripciones para recibir el mensaje en más de un punto de conexión.

## <a name="see-also"></a>Vea también
[Soluciones LOB en Azure](https://azure.microsoft.com/solutions/lob-applications)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
