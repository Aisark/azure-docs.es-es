---
title: Conexión a Office 365 Outlook
description: Administrar el correo electrónico, los contactos y los calendarios con las API REST de Office 365 y Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 858366947fe21a20d6f112fc51899d1533a36472
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789617"
---
# <a name="get-started-with-the-office-365-outlook-connector"></a>Introducción al conector de Office 365 Outlook
El conector de Office 365 Outlook permite la interacción con Outlook en Office 365. Utilice este conector para crear, editar y actualizar elementos de calendario y contactos, y también recibir, enviar y responder al correo electrónico.

Con Office 365 Outlook:

* Creará el flujo de trabajo con las características de correo electrónico y calendario de Office 365. 
* Usará desencadenadores para iniciar el flujo de trabajo cuando reciba un correo electrónico nuevo, se actualice un elemento de calendario, etc.
* Usará acciones para enviar correos electrónicos, crear eventos de calendario y mucho más. Por ejemplo, al recibir un nuevo objeto de Salesforce (un desencadenador), enviar de un correo electrónico a Office 365 Outlook (acción). 

En este artículo se muestra cómo usar el conector de Office 365 Outlook en una aplicación lógica, y se enumeran los desencadenadores y las acciones.

> [!NOTE]
> Esta versión del artículo se aplica a la disponibilidad general de Logic Apps.
> 
> 

Para más información sobre Logic Apps, consulte [¿Qué son las aplicaciones lógicas?](../logic-apps/logic-apps-overview.md) y [Creación de una aplicación lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office-365"></a>Conectarse a Office 365
Antes de que la aplicación lógica pueda acceder a cualquier servicio, cree primero una *conexión* a este. Una conexión proporciona conectividad entre una aplicación lógica y otro servicio. Por ejemplo, para conectarse a Office 365 Outlook, primero necesita una *conexión* de Office 365. Para crear una conexión, escriba las credenciales que utiliza normalmente para acceder al servicio al que desea conectarse. Por lo tanto, para crear la conexión con Office 365 Outlook, escriba las credenciales de la cuenta de Office 365.

## <a name="create-the-connection"></a>Creación de la conexión
> [!INCLUDE [Steps to create a connection to Office 365](../../includes/connectors-create-api-office365-outlook.md)]
> 
> 

## <a name="use-a-trigger"></a>Uso de un desencadenador
Un desencadenador es un evento que se puede utilizar para iniciar el flujo de trabajo definido en una aplicación lógica. Los desencadenadores "sondean" el servicio en el intervalo y la frecuencia que desee. [Más información sobre los desencadenadores](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. En la aplicación lógica, escriba "office 365" para obtener una lista de los desencadenadores:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-trigger.png)
2. Seleccione **Office 365 Outlook - Cuando un evento próximo va a comenzar pronto**. Si ya existe una conexión, seleccione un calendario de la lista desplegable.
   
    ![](./media/connectors-create-api-office365-outlook/sample-calendar.png)
   
    Si se le solicita que inicie sesión, escriba los datos de inicio de sesión para crear la conexión. En la sección [Creación de la conexión](connectors-create-api-office365-outlook.md#create-the-connection) de este tema se enumeran los pasos. 
   
   > [!NOTE]
   > En este ejemplo, la aplicación lógica se ejecuta cuando se actualiza un evento de calendario. Para ver los resultados de este desencadenador, agregue otra acción que le envíe un mensaje de texto. Por ejemplo, agregue la acción de Twilio *Enviar mensaje* que envía un mensaje de texto cuando quedan 15 minutos para que empiece el evento de calendario. 
   > 
   > 
3. Seleccione el botón **Editar** y defina los valores de **Frecuencia** e **Intervalo**. Por ejemplo, si desea que el desencadenador sondee cada 15 minutos, establezca el valor de **Frecuencia** en **Minuto** y el de **Intervalo** en **15**. 
   
    ![](./media/connectors-create-api-office365-outlook/calendar-settings.png)
4. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="use-an-action"></a>Uso de una acción
Una acción es una operación que se lleva a cabo mediante el flujo de trabajo definido en una aplicación lógica. [Más información acerca de las acciones](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Seleccione el signo más. Aparecen varias opciones: **Agregar una acción**, **Agregar una condición** o una de las opciones de **Más**.
   
    ![](./media/connectors-create-api-office365-outlook/add-action.png)
2. Elija **Add an action**(Agregar una acción).
3. En el cuadro de texto, escriba "office 365" para obtener una lista de todas las acciones disponibles.
   
    ![](./media/connectors-create-api-office365-outlook/office365-actions.png) 
4. En nuestro ejemplo, elija **Office 365 Outlook - Crear contacto**. Si ya existe una conexión, elija las propiedades **Folder ID** (Identificador de carpeta), **Given Name** (Nombre), etc.:  
   
    ![](./media/connectors-create-api-office365-outlook/office365-sampleaction.png)
   
    Si se le solicita la información de conexión, escriba los detalles para crear la conexión. Estas propiedades se describen en la sección [Creación de la conexión](connectors-create-api-office365-outlook.md#create-the-connection) de este tema. 
   
   > [!NOTE]
   > En este ejemplo, creamos un nuevo contacto en Office 365 Outlook. Para crear el contacto puede utilizar la salida de otro desencadenador. Por ejemplo, agregue el desencadenador de SalesForce *Cuando se crea un objeto*. Después, agregue la acción *Crear contacto* de Office 365 Outlook, que usa los campos de SalesForce para crear el contacto nuevo en Office 365. 
   > 
   > 
5. **Guarde** los cambios (esquina superior izquierda de la barra de herramientas). La aplicación lógica se guarda y se puede habilitar automáticamente.

## <a name="connector-specific-details"></a>Detalles específicos del conector

Vea los desencadenadores y las acciones definidos en Swagger y vea también todos los límites en los [detalles del conector](/connectors/office365connector/). 

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre otros [conectores de Logic Apps](../connectors/apis-list.md)