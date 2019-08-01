---
title: Solución de problemas con la configuración de Azure Change Tracking
description: Este artículo contiene información sobre la solución de problemas de Change Tracking.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: bobbytreed
ms.author: robreed
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: beb0b89bdbf143c89a83c0813313a8bbda7235d4
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564852"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Solución de problemas de Change Tracking e Inventario

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Escenario: no se muestran los registros de Seguimiento de cambios sobre las máquinas de Windows

#### <a name="issue"></a>Problema

No aparecen los resultados de Inventariar ni de Seguimiento de cambios sobre las máquinas Windows que se han incorporado para hacer un seguimiento de los cambios.

#### <a name="cause"></a>Causa

Este error puede deberse a las siguientes razones:

1. **Microsoft Monitoring Agent** no está en ejecución.
2. Está bloqueada la comunicación a la cuenta de Automation.
3. No se han descargado los módulos de administración de Change Tracking.
4. La máquina virtual que se incorpora puede provenir de una máquina clonada que no estaba preparada con sysprep con Microsoft Monitoring Agent instalado.

#### <a name="resolution"></a>Resolución

1. Compruebe que **Microsoft Monitoring Agent** (HealthService.exe) esté ejecutándose en la máquina.
1. Consulte el **Visor de eventos** de la máquina y busque cualquier evento que contenga la palabra `changetracking`.
1. Consulte el [planeamiento de la red](../automation-hybrid-runbook-worker.md#network-planning) para saber qué direcciones y puertos deben estar permitidos para que Change Tracking funcione.
1. Compruebe que los siguientes módulos de administración de Change Tracking e Inventario existen en el entorno local:
    * Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
    * Microsoft.IntelligencePacks.InventoryChangeTracking.*
    * Microsoft.IntelligencePacks.SingletonInventoryCollection.*
1. Si utiliza una imagen clonada, primero prepare con sysprep la imagen e instale después Microsoft Monitoring Agent.

Si estas soluciones no resuelven el problema y desea ponerse en contacto con el servicio de soporte técnico, puede ejecutar los siguientes comandos para recopilar el diagnóstico del agente

En la máquina del agente, vaya a `C:\Program Files\Microsoft Monitoring Agent\Agent\Tools` y ejecute los siguientes comandos:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> El seguimiento de errores está habilitado de forma predeterminada. Si desea habilitar mensajes de error detallados similares al del ejemplo anterior, use el parámetro `VER`. Para más información sobre los seguimientos, use `INF` cuando invoque `StartTracing.cmd`.

## <a name="next-steps"></a>Pasos siguientes

Si su problema no aparece o es incapaz de resolverlo, visite uno de nuestros canales para obtener ayuda adicional:

* Obtenga respuestas de expertos de Azure en los [foros de Azure](https://azure.microsoft.com/support/forums/).
* Póngase en contacto con [@AzureSupport](https://twitter.com/azuresupport): la cuenta de Microsoft Azure oficial para mejorar la experiencia del cliente, que pone en contacto a la comunidad de Azure con los recursos adecuados: respuestas, soporte técnico y expertos.
* Si necesita más ayuda, puede registrar un incidente de soporte técnico de Azure. Vaya al [sitio de soporte técnico de Azure](https://azure.microsoft.com/support/options/) y seleccione **Obtener soporte**.
