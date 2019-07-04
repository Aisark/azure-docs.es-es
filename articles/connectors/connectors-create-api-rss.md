---
title: Conexión a fuentes RSS desde Azure Logic Apps | Microsoft Docs
description: Automatice las tareas y los flujos de trabajo que supervisan y administran fuentes RSS mediante Azure Logic Apps
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204856"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Administración de fuentes RSS mediante Azure Logic Apps

Con Azure Logic Apps y el conector RSS, puede crear tareas automatizadas y flujos de trabajo para cualquier fuente RSS, por ejemplo:

* Supervise cuando se publican los elementos de la fuente RSS.
* Enumere todos los elementos de la fuente RSS.

RSS (Rich Site Summary), también llamado Really Simple Syndication, es un formato popular para la redifusión web y se utiliza para publicar contenido actualizado con frecuencia, como entradas de blogs y titulares de noticias. Muchos editores de contenido ofrecen una fuente RSS para que los usuarios se suscriban a dicho contenido. 

Puede utilizar un desencadenador RSS que obtiene respuestas de una fuente RSS y hace que la salida esté disponible para otras acciones. Puede usar una acción de RSS en las aplicaciones lógicas para realizar una tarea con la fuente RSS. Si no está familiarizado con las aplicaciones lógicas, consulte [¿Qué es Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>. 

* La dirección URL de una fuente RSS

* Conocimientos básicos acerca de [cómo crear aplicaciones lógicas](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* La aplicación lógica donde quiere acceder a la fuente RSS. Para comenzar con un desencadenador RSS, [cree una aplicación lógica en blanco](../logic-apps/quickstart-create-first-logic-app-workflow.md). Para usar una acción de RSS, inicie la aplicación lógica con otro desencadenador, por ejemplo, el desencadenador de **periodicidad**.

## <a name="connect-to-an-rss-feed"></a>Conexión a una fuente RSS

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y abra la aplicación lógica en el diseñador de aplicaciones lógicas, si aún no lo ha hecho.

1. Elija una ruta de acceso: 

   * Para las aplicaciones lógicas en blanco, en el cuadro de búsqueda, escriba "rss" como filtro. En la lista de desencadenadores, seleccione el que desee. 

     O bien

   * Para las aplicaciones lógicas existentes, en el paso donde desea agregar una acción, elija **Nuevo paso**. En el cuadro de búsqueda, escriba "rss" como filtro. En la lista de acciones, seleccione la que desee.

1. Proporcione los detalles necesarios para el desencadenador o la acción seleccionados y continúe con la compilación del flujo de trabajo de la aplicación lógica.

## <a name="connector-reference"></a>Referencia de conectores

Para obtener detalles técnicos acerca de desencadenadores, acciones y límites, que se describen en la descripción de OpenAPI (antes Swagger) del conector, consulte la [página de referencia](/connectors/rss/) del conector.

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)