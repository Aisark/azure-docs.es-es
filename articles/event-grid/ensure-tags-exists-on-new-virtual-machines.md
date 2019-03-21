---
title: Integración de Azure Automation con Event Grid | Microsoft Docs
description: Obtenga información sobre cómo agregar automáticamente una etiqueta cuando se crea una máquina virtual y enviar una notificación a Microsoft Teams.
keywords: automatización, runbook, teams, event grid, máquina virtual, VM
services: automation
author: eamonoreilly
manager: ''
ms.service: automation
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 01/14/2019
ms.author: eamono
ms.openlocfilehash: d0764131f0e7e321a87ed383636606b2124ef7d9
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "58173777"
---
# <a name="tutorial-integrate-azure-automation-with-event-grid-and-microsoft-teams"></a>Tutorial: Integración de Azure Automation con Event Grid y Microsoft Teams

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Importe un runbook de ejemplo de Event Grid.
> * Cree un webhook de Microsoft Teams opcional.
> * Cree un webhook para el runbook.
> * Cree una suscripción de Event Grid.
> * Cree una máquina virtual que desencadene el runbook.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Para completar este tutorial, se requiere una [cuenta de Azure Automation](../automation/automation-offering-get-started.md) para contener el runbook que se desencadena desde la suscripción de Azure Event Grid.

* El módulo `AzureRM.Tags` tiene que cargarse en su cuenta de Automation, consulte [Importación de módulos en Azure Automation](../automation/automation-update-azure-modules.md) para aprender a importar módulos en Azure Automation.

## <a name="import-an-event-grid-sample-runbook"></a>Importación de un runbook de ejemplo de Event Grid

1. Seleccione la cuenta de Automation y la página **Runbooks**.

   ![Selección de runbooks](./media/ensure-tags-exists-on-new-virtual-machines/select-runbooks.png)

2. Seleccione el botón **Examinar galería**.

3. Busque **Event Grid** y seleccione **Integración de Azure Automation con Event Grid**.

    ![Importación de runbook de Galería](media/ensure-tags-exists-on-new-virtual-machines/gallery-event-grid.png)

4. Seleccione **Importar** y asígnele el nombre **Watch-VMWrite**.

5. Después de la importación, seleccione **Editar** para ver el origen de runbook. Seleccione el botón **Publicar**.

> [!NOTE]
> La línea 74 del script tiene que cambiarse a `Update-AzureRmVM -ResourceGroupName $VMResourceGroup -VM $VM -Tag $Tag | Write-Verbose`. El parámetro `-Tags` es ahora `-Tag`.

## <a name="create-an-optional-microsoft-teams-webhook"></a>Creación de un webhook de Microsoft Teams opcional

1. En Microsoft Teams, seleccione **Más opciones** junto al nombre del canal y, luego, seleccione **Conectores**.

    ![Conexiones de Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-webhook.png)

2. Desplácese por la lista de conectores hasta **Webhook de entrada** y seleccione **Agregar**.

3. Escriba **AzureAutomationIntegration** como el nombre y seleccione **Crear**.

4. Copie el webhook en el Portapapeles y guárdelo. La dirección URL del webhook se usa para enviar información a Microsoft Teams.

5. Seleccione **Hecho** para guardar el webhook.

## <a name="create-a-webhook-for-the-runbook"></a>Creación de un webhook para el runbook

1. Abra el runbook Watch-VMWrite.

2. Seleccione **Webhooks** y el botón **Agregar webhook**.

3. Escriba **WatchVMEventGrid** como el nombre. Copie la dirección URL en el Portapapeles y guárdelo.

    ![Configuración del nombre del webhook](media/ensure-tags-exists-on-new-virtual-machines/copy-url.png)

4. Seleccione **Configurar parámetros y ejecutar configuraciones** y escriba la dirección URL del webhook de Microsoft Teams para **CHANNELURL**. Deje en blanco **WEBHOOKDATA**.

    ![Configuración de los parámetros del webhook](media/ensure-tags-exists-on-new-virtual-machines/configure-webhook-parameters.png)

5. Seleccione **Crear** para crear el webhook del runbook de Automation.

## <a name="create-an-event-grid-subscription"></a>Creación de una suscripción de Event Grid

1. En la página de información general de la **cuenta de Automation**, seleccione **Event Grid**.

    ![Seleccionar Event Grid](media/ensure-tags-exists-on-new-virtual-machines/select-event-grid.png)

2. Haga clic en **+Suscripción de eventos**.

3. Configure la suscripción con la información siguiente:

   * En **Tipo de tema**, seleccione **Suscripciones de Azure**.
   * Desactive la casilla **Suscribirse a todos los tipos de evento**.
   * Escriba **AzureAutomation** como el nombre.
   * En la lista desplegable **Tipos de evento definidos**, desactive todas las opciones excepto **Resource Write Success** (Escritura de recursos correcta).
   * En **Tipo de punto de conexión**, seleccione **Webhook**.
   * Haga clic en **Seleccionar un punto de conexión**. En la página **Seleccionar webhook** que se abre, pegue la dirección URL del webhook que creó para el runbook Watch-VMWrite.
   * En **FILTROS**, escriba la suscripción y el grupo de recursos en los que desea buscar las nuevas máquinas virtuales creadas. Debe verse de una manera similar a: `/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Compute/virtualMachines`

4. Seleccione **Crear** para guardar la suscripción de Event Grid.

## <a name="create-a-vm-that-triggers-the-runbook"></a>Creación de una máquina virtual que desencadena el runbook

1. Cree una máquina virtual nueva en el grupo de recursos que especificó en el filtro de prefijo de la suscripción de Event Grid.

2. Se debe llamar al runbook Watch-VMWrite y se debe agregar una etiqueta nueva a la máquina virtual.

    ![Etiqueta de máquina virtual](media/ensure-tags-exists-on-new-virtual-machines/vm-tag.png)

3. Se envía un mensaje nuevo al canal de Microsoft Teams.

    ![Notificación de Microsoft Teams](media/ensure-tags-exists-on-new-virtual-machines/teams-vm-message.png)

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se configura la integración entre Event Grid y Automation. Ha aprendido a:

> [!div class="checklist"]
> * Importe un runbook de ejemplo de Event Grid.
> * Cree un webhook de Microsoft Teams opcional.
> * Cree un webhook para el runbook.
> * Cree una suscripción de Event Grid.
> * Cree una máquina virtual que desencadene el runbook.

> [!div class="nextstepaction"]
> [Creación y enrutamiento de eventos personalizados con Event Grid](../event-grid/custom-event-quickstart.md)
