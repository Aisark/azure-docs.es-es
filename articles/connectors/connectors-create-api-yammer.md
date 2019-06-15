---
title: Conexión a Yammer desde Azure Logic Apps | Microsoft Docs
description: Automatice tareas y flujos de trabajo que supervisan, publican y administran mensajes, fuentes y más en Yammer con Azure Logic Apps.
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: b5ae0827-fbb3-45ec-8f45-ad1cc2e7eccc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: ca2d28f3438fd166fa282488206662c95777bf3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "62104738"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Supervisión y administración de la cuenta de Yammer con Azure Logic Apps

Con Azure Logic Apps y el conector Yammer, puede crear tareas y flujos de trabajo automatizados que supervisan y administran mensajes, fuentes y mucho más en su cuenta de Yammer, junto con otras acciones, por ejemplo:

* Supervisar cuándo aparecen nuevos mensajes en fuentes y grupos seguidos
* Obtener mensajes, grupos, redes, detalles de los usuarios y mucho más
* Publicar e indicar que le gusta un mensaje

Puede usar desencadenadores que obtengan respuestas de su cuenta de Yammer y permitan que la salida esté disponible para otras acciones. Puede usar acciones que realicen tareas con su cuenta de Yammer. También puede hacer que otras acciones usen la salida de las acciones de Yammer. Por ejemplo, cuando aparezcan nuevos mensajes en fuentes o grupos, puede compartir esos mensajes con el conector Slack. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>. 

* Sus credenciales de usuario y la cuenta de Yammer

   Las credenciales autorizan a la aplicación lógica a crear una conexión con la cuenta de Yammer y acceder a ella.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* La aplicación lógica desde donde quiere acceder a la cuenta de Yammer. Para comenzar con un desencadenador de Yammer, [cree una aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar una acción de Yammer, inicie la aplicación lógica con otro desencadenador, por ejemplo, **Recurrence**.

## <a name="connect-to-yammer"></a>Conexión a Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Si aún no lo ha hecho, inicie sesión en [Azure Portal](https://portal.azure.com) y abra la aplicación lógica en el Diseñador de aplicaciones lógicas.

1. Elija una ruta de acceso: 

   * Para las aplicaciones lógicas en blanco, en el cuadro de búsqueda, escriba "yammer" como filtro. 
   En la lista de desencadenadores, seleccione el que desee. 

     O bien

   * Para las aplicaciones lógicas existentes: 
   
     * En el último paso para agregar una acción, elija **Nuevo paso**. 

       O bien

     * Entre los pasos en los que desee agregar una acción, mueva el puntero sobre la flecha. 
     Elija el signo más ( **+** ) que aparece y seleccione **Agregar una acción**.
     
       En el cuadro de búsqueda, escriba "yammer" como filtro. 
       En la lista de acciones, seleccione la acción que desee.

1. Si se le pide que inicie sesión en Yammer, inicie sesión ahora para permitir el acceso.

1. Proporcione los detalles necesarios para el desencadenador o la acción seleccionados y continúe con la compilación del flujo de trabajo de la aplicación lógica.

## <a name="connector-reference"></a>Referencia de conectores

Para obtener detalles técnicos acerca de desencadenadores, acciones y límites, que se describen en la descripción de OpenAPI (antes Swagger) del conector, consulte la [página de referencia](/connectors/yammer/) del conector.

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)