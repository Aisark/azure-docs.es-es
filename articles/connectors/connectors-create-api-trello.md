---
title: Conexión a Trello desde Azure Logic Apps | Microsoft Docs
description: Automatización de tareas y flujos de trabajo de supervisión y administración de listas, paneles y tarjetas en proyectos de Trello con Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 31357fa313cfa31c0f8a90c0f7722f627e4394d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106174"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Supervisión y administración de Trello como Azure Logic Apps

Con Azure Logic Apps y el conector de Trello, puede crear tareas automatizadas y flujos de trabajo que supervisan y administran sus listas, tarjetas, paneles, miembros del equipo y otros elementos de Trello. Por ejemplo:

* Supervise cuando se agregan nuevas tarjetas a paneles y listas. 
* Cree, obtenga y administre paneles, tarjetas y listas.
* Agregue comentarios y miembros a tarjetas.
* Enumere paneles, etiquetas de paneles, tarjetas de paneles, comentarios de tarjetas, miembros de tarjetas, miembros de equipos y equipos de los que sea miembro. 
* Obtenga equipos.

Puede usar desencadenadores que obtengan respuestas de su cuenta de Trello y hagan que la salida esté disponible para otras acciones. Puede usar acciones que realizan tareas con su cuenta de Trello. También puede hacer que otras acciones usen la salida de las acciones de Trello. Por ejemplo, cuando una tarjeta nueva se agrega a un panel o lista, puede enviar mensajes con el conector de Slack. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>. 

* Cuenta de Trello y credenciales del usuario

  Las credenciales autorizan a la aplicación lógica para que cree una conexión con la cuenta de Trello y acceda a ella.

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* La aplicación lógica desde donde quiere acceder a la cuenta de Trello. Para comenzar con un desencadenador de Trello, [cree una aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar una acción de Trello, inicie la aplicación lógica con un desencadenador, por ejemplo, el de **periodicidad**.

## <a name="connect-to-trello"></a>Conexión a Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y abra la aplicación lógica en el diseñador de aplicaciones lógicas, si aún no lo ha hecho.

1. Para las aplicaciones lógicas en blanco, en el cuadro de búsqueda, escriba "trello" como filtro. En la lista de desencadenadores, seleccione el que desee. 

   O bien

   Para las aplicaciones lógicas existentes, en el último paso donde desea agregar una acción, elija **Nuevo paso**. 
   En el cuadro de búsqueda, escriba "trello" como filtro. 
   En la lista de acciones, seleccione la que desee.

   Para agregar una acción entre un paso y otro, mueva el puntero sobre la flecha entre ellos. 
   Elija el signo más ( **+** ) que aparece y seleccione **Agregar una acción**.

1. Si se le solicita que inicie sesión en Trello, autorice el acceso para la aplicación lógica e inicie sesión.

1. Proporcione los detalles necesarios para el desencadenador o la acción seleccionados y continúe con la compilación del flujo de trabajo de la aplicación lógica.

## <a name="connector-reference"></a>Referencia de conectores

Para obtener detalles técnicos acerca de desencadenadores, acciones y límites, que se describen en la descripción de OpenAPI (antes Swagger) del conector, consulte la [página de referencia](/connectors/trello/) del conector.

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)