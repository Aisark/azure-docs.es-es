---
title: 'Inicio rápido: Creación y automatización del primer flujo de trabajo: Azure Logic Apps | Microsoft Docs'
description: Cree la primera aplicación lógica que automatiza tareas, procesos y flujos de trabajo con Azure Logic Apps. Creación de aplicaciones lógicas para la integración de sistemas y la integración de aplicaciones empresariales (EAI) para los sistemas y servicios en la nube
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: quickstart
ms.custom: mvc
ms.date: 07/20/2018
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: 577a047fb88208d7e14e8ba06a1b9e7e87c5ef91
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884248"
---
# <a name="quickstart-create-your-first-automated-workflow-with-azure-logic-apps---azure-portal"></a>Inicio rápido: Creación del primer flujo de trabajo automatizado con Azure Logic Apps: Azure Portal

En esta guía de inicio rápido se muestra cómo crear su primer flujo de trabajo automatizado con [Azure Logic Apps](../logic-apps/logic-apps-overview.md). En este artículo, creará una aplicación lógica que comprobará periódicamente la fuente RSS de un sitio web para detectar si se ha publicado nuevo contenido. Si existe algún elemento nuevo, la aplicación lógica envía un correo electrónico por cada elemento. Cuando haya terminado, la aplicación lógica se parecerá a este flujo de trabajo, en un alto nivel:

![Introducción: aplicación lógica de ejemplo](./media/quickstart-create-first-logic-app-workflow/overview.png)

Para seguir esta guía de inicio rápido, necesita una cuenta de correo electrónico de un proveedor compatible con Logic Apps, como Office 365 Outlook, Outlook.com o Gmail. En el caso de otros proveedores, [consulte la lista de conectores que se muestra aquí](https://docs.microsoft.com/connectors/). Esta aplicación lógica usa una cuenta de Office 365 Outlook. Si usa otra cuenta de correo electrónico, los pasos generales serán los mismos, pero su interfaz de usuario puede ser ligeramente distinta. 

Asimismo, si no tiene una suscripción de Azure, <a href="https://azure.microsoft.com/free/" target="_blank">regístrese para obtener una cuenta gratuita de Azure</a>.

## <a name="sign-in-to-the-azure-portal"></a>Inicio de sesión en Azure Portal

Inicie sesión en <a href="https://portal.azure.com" target="_blank">Azure Portal</a> con sus credenciales de su cuenta de Azure.

## <a name="create-your-logic-app"></a>Creación de una aplicación lógica 

1. En el menú principal de Azure, elija **Crear un recurso** > **Integración** > **Aplicación lógica**.

   ![Creación de la aplicación lógica](./media/quickstart-create-first-logic-app-workflow/create-logic-app.png)

3. En **Crear aplicación lógica**, proporcione información sobre la aplicación lógica, como se muestra a continuación. Cuando termine, seleccione **Crear**.

   ![Proporcione los detalles de la aplicación lógica](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Propiedad | Valor | DESCRIPCIÓN | 
   |----------|-------|-------------| 
   | **NOMBRE** | MyFirstLogicApp | Nombre de la aplicación lógica | 
   | **Subscription** | <*nombre-de-su-suscripción-a-Azure*> | El nombre de la suscripción a Azure | 
   | **Grupos de recursos** | My-First-LA-RG | Nombre del [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) que se utiliza para organizar recursos relacionados | 
   | **Ubicación** | Oeste de EE. UU. | Región en la que desea almacenar la información de la aplicación lógica | 
   | **Log Analytics** | Off | Mantenga el valor **Off** para el registro de diagnóstico. | 
   |||| 

3. Una vez que Azure ha implementado la aplicación, el Diseñador de aplicaciones lógicas se abre y muestra una página con un vídeo de introducción y desencadenadores utilizados frecuentemente. En **plantillas**, elija **Aplicación lógica en blanco**.

   ![Selección de la plantilla de aplicación lógica en blanco](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

A continuación, añada un [desencadenador](../logic-apps/logic-apps-overview.md#logic-app-concepts) que se active cuando aparezca un nuevo elemento en la fuente RSS. Cada aplicación lógica debe comenzar con un desencadenador, que se activa cuando sucede un evento específico o cuando se cumple una condición determinada. Cada vez que el desencadenador se activa, el motor de Logic Apps crea una instancia de aplicación lógica que inicia y ejecuta el flujo de trabajo.

<a name="add-rss-trigger"></a>

## <a name="check-rss-feed-with-a-trigger"></a>Comprobación de la fuente RSS con un desencadenador

1. En el diseñador, escriba "rss" en el cuadro de búsqueda. Seleccione este desencadenador: **RSS - When a feed item is published** (RSS: Cuando se publica un elemento de fuente)

   ![Seleccione el desencadenador: "RSS - When a feed item is published" (RSS: Cuando se publica un elemento de fuente)](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss.png)

2. Proporcione esta información para el desencadenador, como se describe a continuación: 

   ![Configuración del desencadenador con la fuente RSS, la frecuencia y el intervalo](./media/quickstart-create-first-logic-app-workflow/add-trigger-rss-settings.png)

   | Propiedad | Valor | DESCRIPCIÓN | 
   |----------|-------|-------------| 
   | **URL de fuente RSS** | ```http://feeds.reuters.com/reuters/topNews``` | Vínculo de la fuente RSS que desea supervisar | 
   | **Intervalo** | 1 | Número de intervalos que se espera entre comprobaciones | 
   | **Frecuencia** | Minuto | La unidad de tiempo de cada intervalo entre comprobaciones  | 
   |||| 

   Juntos, el intervalo y la frecuencia definen la programación para el desencadenador de la aplicación lógica. 
   Esta aplicación lógica comprueba la fuente cada minuto.

3. Para ocultar por ahora los detalles del desencadenador, haga clic dentro de la barra de título del desencadenador.

   ![Contraer la forma para ocultar detalles](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

4. Guarde la aplicación lógica. En la barra de herramientas del diseñador, haga clic en **Guardar**. 

La aplicación lógica estará activa, pero no hace más que comprobar la fuente RSS. Por lo tanto, agregue una acción que responda cuando se active el desencadenador.

## <a name="send-email-with-an-action"></a>Envío de correo electrónico con una acción

Ahora, añada una [acción](../logic-apps/logic-apps-overview.md#logic-app-concepts) para enviar un correo electrónico cuando aparezca un nuevo elemento en la fuente RSS. 

1. En el desencadenador **When a feed item is published** (Cuando se publica un elemento de fuente), elija **New step** (Nuevo paso).

   ![Agregar una acción](./media/quickstart-create-first-logic-app-workflow/add-new-action.png)

2. En **Elegir una acción**, escriba "enviar un correo electrónico" en el cuadro de búsqueda. En el cuadro de búsqueda, elija **Todas**. En la lista de acciones, seleccione la acción "enviar un correo electrónico" del proveedor de correo electrónico que desee. 

   ![Seleccione esta acción: "Office 365 Outlook - Send an email" (Office 365 Outlook: Enviar un correo electrónico)](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Para filtrar la lista de acciones de una aplicación o un servicio específicos, puede seleccionar la aplicación o servicio primero:

   * Para las cuentas profesionales o educativas de Azure, seleccione Office 365 Outlook. 
   * Para las cuentas de Microsoft personales, seleccione Outlook.com.

3. Si se le piden credenciales, inicie sesión en su cuenta de correo electrónico para que Logic Apps cree una conexión a su cuenta de correo electrónico.

4. En la acción **Enviar correo electrónico**, especifique los datos que desea que el correo electrónico incluya. 

   1. En el cuadro **Para**, escriba la dirección de correo electrónico del destinatario. 
   Para realizar pruebas, puede usar su propia dirección de correo electrónico.

      Por ahora, ignore la lista **Agregar contenido dinámico** que aparece. 
      Al hacer clic en algunos cuadros de edición, esta lista aparece y muestra los parámetros disponibles del paso anterior que se pueden incluir como entradas en el flujo de trabajo. 

   2. En el cuadro **Asunto**, escriba este texto con un espacio en blanco final: ```New RSS item:```

      ![Incorporación del asunto del correo electrónico](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)
 
   3. En la lista **Agregar contenido dinámico**, seleccione **Título de fuente** para incluir el título del elemento RSS.

      ![Lista de contenido dinámico - "Título de fuente"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Cuando haya terminado, el asunto del correo electrónico será similar a este ejemplo:

      ![Título de fuente agregado](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Si un bucle "Para cada uno" aparece en el diseñador, habrá seleccionado un token de una matriz, como el token **categorías-elemento**. 
      Para este tipo de token, el diseñador añade automáticamente este bucle alrededor de la acción que hace referencia a ese token. 
      De este modo, la aplicación lógica realiza la misma acción en cada elemento de la matriz. 
      Para quitar el bucle, elija el botón de **puntos suspensivos** (**...**) de la barra de título del bucle y **Eliminar**.

   4. En el cuadro **Cuerpo**, escriba este texto y seleccione estos tokens para el cuerpo del correo electrónico. 
   Para agregar líneas en blanco en el cuadro de edición, presione Mayús + Entrar. 

      ![Incorporación del contenido del cuerpo del correo electrónico](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Propiedad | DESCRIPCIÓN | 
      |----------|-------------| 
      | **Título de fuente** | Título del elemento | 
      | **Fuente publicada el** | Fecha y hora de publicación del elemento | 
      | **Vínculo de fuente principal** | Dirección URL del elemento | 
      ||| 
   
5. Guarde la aplicación lógica.

A continuación, pruebe la aplicación lógica.

## <a name="run-your-logic-app"></a>Ejecución de la aplicación lógica

Para iniciar manualmente la aplicación lógica, en la barra de la barra de herramientas del diseñador, elija **Ejecutar**. También puede esperar a que la aplicación lógica compruebe la fuente RSS basada en la programación especificada (cada minuto). Si la fuente RSS tiene nuevos elementos, la aplicación lógica envía un correo electrónico para cada uno de ellos. En caso contrario, la aplicación lógica espera hasta el siguiente intervalo antes de volver a comprobar. 

Por ejemplo, este es un correo electrónico de ejemplo que envía esta aplicación lógica. Si no obtiene los mensajes de correo electrónico, compruebe la carpeta de correo electrónico no deseado.

![Envío de correo electrónico por un elemento nuevo en la fuente RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Técnicamente, cuando el desencadenador comprueba la fuente RSS y encuentra nuevos elementos, el desencadenador se activa y el motor de Logic Apps crea una instancia de flujo de trabajo de la aplicación lógica que ejecuta las acciones en el flujo de trabajo.
Si el desencadenador no encuentra nuevos elementos, no se activa y "omite" crear instancias de flujo de trabajo.

¡Enhorabuena, acaba de compilar y ejecutar correctamente su primera aplicación lógica con Azure Portal!

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando ya no necesite este ejemplo, elimine el grupo de recursos que contiene la aplicación lógica y los recursos relacionados. 

1. En el menú principal de Azure, vaya a **Grupos de recursos** y seleccione el grupo de recursos de la aplicación lógica. En la página **Información general**, elija **Eliminar grupo de recursos**. 

   !["Grupos de recursos" > "Introducción" > "Eliminar grupo de recursos"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

2. Escriba el nombre del grupo de recursos como confirmación y elija **Eliminar**.

   ![Confirmar eliminación](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Cuando se elimina una aplicación lógica, no se crean instancias de nuevas ejecuciones. Todas las ejecuciones nuevas y pendientes se cancelan. Si tiene miles de ejecuciones, la cancelación puede tardar bastante tiempo en completarse.

## <a name="get-support"></a>Obtención de soporte técnico

* Si tiene alguna duda, visite el [foro de Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para enviar ideas sobre características o votar sobre ellas, visite el [sitio de comentarios de los usuarios de Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado su primera aplicación lógica que comprueba si hay actualizaciones de RSS según la programación especificada (cada minuto) y realiza una acción (envía un correo electrónico) cuando hay actualizaciones. Para más información, continúe con este tutorial para crear flujos de trabajo basados en programación más avanzados:

> [!div class="nextstepaction"]
> [Comprobación del tráfico con una aplicación lógica basada en una programación](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
