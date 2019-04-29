---
title: Solución de problemas de inicio y detención de máquinas virtuales con Azure Automation
description: En este artículo se proporciona información sobre la solución de problemas de inicio y detención de máquinas virtuales en Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 03bad12b7fcba5a247e05884aa0eb0493163a5c4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60579004"
---
# <a name="troubleshoot-the-startstop-vms-during-off-hours-solution"></a>Solución de problemas de la solución Start/Stop VMs during off-hours

## <a name="deployment-failure"></a>Escenario: No se puede implementar correctamente la solución Start/Stop VM

### <a name="issue"></a>Problema

Al implementar la [solución Start/Stop VM during off hours](../automation-solution-vm-management.md), se producirá uno de los siguientes errores:

```error
Account already exists in another resourcegroup in a subscription. ResourceGroupName: [MyResourceGroup].
```

```error
Resource 'StartStop_VM_Notification' was disallowed by policy. Policy identifiers: '[{\\\"policyAssignment\\\":{\\\"name\\\":\\\"[MyPolicyName]”.
```

```error
The subscription is not registered to use namespace 'Microsoft.OperationsManagement'.
```

```error
The subscription is not registered to use namespace 'Microsoft.Insights'.
```

```error
The scope '/subscriptions/000000000000-0000-0000-0000-00000000/resourcegroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView' cannot perform write operation because following scope(s) are locked: '/subscriptions/000000000000-0000-0000-0000-00000000/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>/views/StartStopVMView'. Please remove the lock and try again
```

### <a name="cause"></a>Causa

Es posible que se produzcan errores de implementación por uno de los siguientes motivos:

1. Ya hay una cuenta de Automation con el mismo nombre en la región seleccionada.
2. Hay una directiva en vigor que no permite la implementación de la solución Start/Stop VMs.
3. Los tipos de recursos `Microsoft.OperationsManagement`, `Microsoft.Insights` o `Microsoft.Automation` no están registrados.
4. Su área de trabajo de Log Analytics tiene un bloqueo.

### <a name="resolution"></a>Resolución

Revise la siguiente lista de posibles soluciones para su problema o para saber dónde buscar:

1. Las cuentas de Automation deben ser únicas en una misma región de Azure, incluso si se encuentran en distintos grupos de recursos. Compruebe sus cuentas de Automation existentes en la región de destino.
2. Una directiva existente impide que se implemente un recurso necesario para la solución Start/Stop VM. Vaya a las asignaciones de directivas en Azure Portal y compruebe si tiene una asignación de directiva que no permite la implementación de este recurso. Para más información acerca de esto, consulte [RequestDisallowedByPolicy](../../azure-resource-manager/resource-manager-policy-requestdisallowedbypolicy-error.md).
3. Para implementar la solución Start/Stop VM, la suscripción debe estar registrada en los siguientes espacios de nombres de recursos de Azure:
    * `Microsoft.OperationsManagement`
    * `Microsoft.Insights`
    * `Microsoft.Automation`

   Consulte [Resolución de errores del registro del proveedor de recursos](../../azure-resource-manager/resource-manager-register-provider-errors.md) para más información acerca de errores al registrar los proveedores.
4. Si tiene un bloqueo en el área de trabajo de Log Analytics, vaya al área de trabajo de Azure Portal y quite los bloqueos en el recurso.

## <a name="all-vms-fail-to-startstop"></a>Escenario: Error al iniciar o detener todas las máquinas virtuales

### <a name="issue"></a>Problema

Ha configurado la solución Start/Stop VMs, pero esta no inicia o detiene todas las máquinas virtuales configuradas.

### <a name="cause"></a>Causa

Este error puede deberse a alguna de las siguientes razones:

1. Una programación no está configurada correctamente.
2. La cuenta de ejecución puede no estar configurada correctamente.
3. Un runbook puede presentar errores.
4. Las máquinas virtuales pueden haberse excluido.

### <a name="resolution"></a>Resolución

Revise la siguiente lista de posibles soluciones para su problema o para saber dónde buscar:

* Compruebe que ha configurado correctamente una programación para la solución Start/Stop VMs. Para más información sobre cómo configurar una programación, vea el artículo sobre [programación](../automation-schedules.md).

* Compruebe el [flujos de trabajo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para buscar los errores. En Azure Portal, vaya a la cuenta de Automation y seleccione **Trabajos** en **Automatización de procesos**. En la página **Trabajos**, busque los trabajos de alguno de los siguientes runbooks:

  * AutoStop_CreateAlert_Child
  * AutoStop_CreateAlert_Parent
  * AutoStop_Disable
  * AutoStop_VM_Child
  * ScheduledStartStop_Base_Classic
  * ScheduledStartStop_Child_Classic
  * ScheduledStartStop_Child
  * ScheduledStartStop_Parent
  * SequencedStartStop_Parent

* Verifique que su [cuenta de ejecución](../manage-runas-account.md) tiene los permisos adecuados para las máquinas virtuales que intenta iniciar o detener. Para obtener información sobre cómo comprobar los permisos para un recurso, vea [Guía de inicio rápido: Visualización de los roles asignados a un usuario mediante Azure Portal](../../role-based-access-control/check-access.md). Deberá proporcionar el identificador de la aplicación para la entidad de servicio utilizada por la cuenta de ejecución. Para obtener este valor, vaya a la cuenta de Automation en Azure Portal, seleccione **Cuentas de ejecución** en **Configuración de cuenta** y haga clic en la cuenta de ejecución apropiada.

* Las máquinas virtuales no pueden iniciarse o detenerse en caso de que se hayan excluido de forma explícita. Las máquinas virtuales excluidas se definen en la variable **External_ExcludeVMNames** en la cuenta de Automation en la que está implementada la solución. El ejemplo siguiente, se muestra cómo se puede consultar ese valor con PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

## <a name="some-vms-fail-to-startstop"></a>Escenario: Algunas máquinas virtuales no se pueden iniciar o detener

### <a name="issue"></a>Problema

Ha configurado la solución Start/Stop VMs, pero esta no inicia o detiene algunas de las máquinas virtuales configuradas.

### <a name="cause"></a>Causa

Este error puede deberse a alguna de las siguientes razones:

1. Si usa el escenario en secuencia, podría faltar una etiqueta o ser incorrecta.
2. La máquina virtual puede estar excluida.
3. La cuenta de ejecución puede no tener suficientes permisos en la máquina virtual.
4. La máquina virtual puede tener alguna configuración que impide su inicio o detención.

### <a name="resolution"></a>Resolución

Revise la siguiente lista de posibles soluciones para su problema o para saber dónde buscar:

* Si usa el [escenario en secuencia](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags) de la solución Start/Stop VMs during off-hours, debe asegurarse de que cada máquina virtual que desea iniciar o detener tiene la etiqueta correcta. Asegúrese de que las máquinas virtuales que desea iniciar tienen la etiqueta `sequencestart` y que aquellas que desea detener tienen la etiqueta `sequencestop`. Ambas etiquetas requieren un valor entero positivo. Puede usar una consulta similar a la del ejemplo siguiente para buscar todas las máquinas virtuales con las etiquetas y sus valores.

  ```powershell-interactive
  Get-AzureRmResource | ? {$_.Tags.Keys -contains "SequenceStart" -or $_.Tags.Keys -contains "SequenceStop"} | ft Name,Tags
  ```

* Las máquinas virtuales no pueden iniciarse o detenerse en caso de que se hayan excluido de forma explícita. Las máquinas virtuales excluidas se definen en la variable **External_ExcludeVMNames** en la cuenta de Automation en la que está implementada la solución. El ejemplo siguiente, se muestra cómo se puede consultar ese valor con PowerShell.

  ```powershell-interactive
  Get-AzureRmAutomationVariable -Name External_ExcludeVMNames -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName> | Select-Object Value
  ```

* Para iniciar y detener máquinas virtuales, la cuenta de ejecución de la cuenta de Automation debe tener los permisos apropiados para la máquina virtual. Para obtener información sobre cómo comprobar los permisos para un recurso, vea [Guía de inicio rápido: Visualización de los roles asignados a un usuario mediante Azure Portal](../../role-based-access-control/check-access.md). Deberá proporcionar el identificador de la aplicación para la entidad de servicio utilizada por la cuenta de ejecución. Para obtener este valor, vaya a la cuenta de Automation en Azure Portal, seleccione **Cuentas de ejecución** en **Configuración de cuenta** y haga clic en la cuenta de ejecución apropiada.

* Si la máquina virtual presenta algún problema para iniciarse o desasignarse, este comportamiento puede deberse a algún problema en la propia máquina virtual. Entre algunos ejemplos o posibles problemas destacan la aplicación en curso de alguna actualización al intentar apagar, el bloqueo de un servicio, etc. Vaya al recurso de máquina virtual y consulte los **registros de actividad** para ver si en ellos se ha identificado algún error. También puede intentar iniciar sesión en la máquina virtual para ver si existe algún error en los registros de eventos. Para obtener más información acerca de la máquina virtual de solución de problemas, vea [solución de problemas de Azure virtual machines](../../virtual-machines/troubleshooting/index.md)

* Compruebe el [flujos de trabajo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para buscar los errores. En Azure Portal, vaya a la cuenta de Automation y seleccione **Trabajos** en **Automatización de procesos**.

## <a name="custom-runbook"></a>Escenario: Mi runbook personalizado no puede iniciar o detener mis máquinas virtuales

### <a name="issue"></a>Problema

Ha creado un runbook personalizado o ha descargado uno de la Galería de PowerShell, pero no funciona correctamente.

### <a name="cause"></a>Causa

La causa del error podría ser una entre muchas. Vaya a la cuenta de Automation en Azure Portal y seleccione **Trabajos** en **Automatización de procesos**. En la página **Trabajos**, busque los trabajos del runbook para ver cualquier error que pueda haber en ellos.

### <a name="resolution"></a>Resolución

Se recomienda usar la [solución Start/Stop VMs during off-hours](../automation-solution-vm-management.md) para iniciar y detener máquinas virtuales en Azure Automation. Esta solución la creó Microsoft. Los runbooks personalizados no son compatibles con Microsoft. Puede encontrar una solución para su runbook personalizado en el artículo [Solución de problemas relativos a errores con runbooks](runbooks.md). En este artículo se proporcionan instrucciones generales y solución de problemas para runbooks de todos los tipos. Compruebe el [flujos de trabajo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para buscar los errores. En Azure Portal, vaya a la cuenta de Automation y seleccione **Trabajos** en **Automatización de procesos**.

## <a name="dont-start-stop-in-sequence"></a>Escenario: Las máquinas virtuales no se inician o detienen en la secuencia correcta

### <a name="issue"></a>Problema

Las máquinas virtuales configuradas en la solución no se inician o detienen en la secuencia correcta.

### <a name="cause"></a>Causa

Esto se debe a un etiquetado incorrecto de las máquinas virtuales.

### <a name="resolution"></a>Resolución

Realice los pasos siguientes para asegurarse de que la solución está configurada correctamente.

1. Asegúrese de que todas las máquinas virtuales que se van a iniciar o detener tienen una etiqueta `sequencestart` o `sequencestop`, en función de su situación. Estas etiquetas necesitan un número entero positivo como valor. Las máquinas virtuales se procesan en orden ascendente según este valor.
2. Asegúrese de que los grupos de recursos de las máquinas virtuales que se van a iniciar o detener están en las variables `External_Start_ResourceGroupNames` o `External_Stop_ResourceGroupNames`, en función de su situación.
3. Pruebe los cambios mediante la ejecución del runbook `SequencedStartStop_Parent` con el parámetro WHATIF establecido en True para obtener una vista previa de dichos cambios.

Para obtener información detallada e instrucciones adicionales sobre cómo usar la solución para iniciar y detener máquinas virtuales en secuencia, vea [Iniciar o detener las máquinas virtuales en secuencia ](../automation-solution-vm-management.md#scenario-2-startstop-vms-in-sequence-by-using-tags).

## <a name="403"></a>Escenario: El trabajo de inicio y detención de máquinas virtuales genera un estado de error 403 Prohibido 

### <a name="issue"></a>Problema

Puede encontrar trabajos con el error `403 forbidden` para los runbooks de la solución Start/Stop VMs during off-hours.

### <a name="cause"></a>Causa

Este problema puede deberse a una cuenta de ejecución mal configurada o expirada. La causa también puede ser que la cuenta de ejecución de Cuentas de Automation tenga permisos inadecuados para los recursos de máquina virtual.

### <a name="resolution"></a>Resolución

Para comprobar si la cuenta de ejecución está configurada correctamente, vaya a la cuenta de Automation en Azure Portal y seleccione **Cuentas de ejecución** en **Configuración de la cuenta**. Aquí verá el estado de las cuentas de ejecución; si alguna de ellas está configurada incorrectamente o expirada, el estado lo indicará.

Si la cuenta de ejecución está [mal configurada](../manage-runas-account.md#misconfiguration), debe eliminarla y volver a crearla.

Si el certificado de la cuenta de ejecución ha expirado, siga los pasos de [Renovación de certificado autofirmado](../manage-runas-account.md#cert-renewal) para renovarlo.

El problema puede deberse a la falta de permisos. Para obtener información sobre cómo comprobar los permisos para un recurso, vea [Guía de inicio rápido: Visualización de los roles asignados a un usuario mediante Azure Portal](../../role-based-access-control/check-access.md). Deberá proporcionar el identificador de la aplicación para la entidad de servicio utilizada por la cuenta de ejecución. Para obtener este valor, vaya a la cuenta de Automation en Azure Portal, seleccione **Cuentas de ejecución** en **Configuración de cuenta** y haga clic en la cuenta de ejecución apropiada.

## <a name="other"></a>Escenario: Mi problema no se ha indicado anteriormente

### <a name="issue"></a>Problema

Experimenta un problema u obtiene un resultado inesperado al utilizar la solución Start/Stop VMs during off-hours que no se indica en esta página.

### <a name="cause"></a>Causa

Muchas veces los errores pueden deberse a que se usa una versión anterior y obsoleta de la solución.

### <a name="resolution"></a>Resolución

Para resolver muchos errores, se recomienda quitar y actualizar la solución. Para obtener información sobre cómo actualizar la solución, vea [Actualización de la solución](../automation-solution-vm-management.md#update-the-solution). Además, puede comprobar el [flujos de trabajo](../automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) para buscar los errores. En Azure Portal, vaya a la cuenta de Automation y seleccione **Trabajos** en **Automatización de procesos**.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure en los [foros de Azure](https://azure.microsoft.com/support/forums/).
* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport): la cuenta de Microsoft Azure oficial para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.
* Si necesita más ayuda, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.
