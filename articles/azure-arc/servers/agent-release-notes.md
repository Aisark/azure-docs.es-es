---
title: Novedades del agente de servidores habilitados para Azure Arc
description: En este artículo se incluyen las notas de la versión del agente de servidores habilitados para Azure Arc. Muchos de los problemas resumidos incluyen vínculos para obtener detalles adicionales.
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 3a01113d0d6416fb050f5c66191d5c420b7ac137
ms.sourcegitcommit: 2ba6303e1ac24287762caea9cd1603848331dd7a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97505654"
---
# <a name="whats-new-with-azure-arc-enabled-servers-agent"></a>Novedades del agente de servidores habilitados para Azure Arc

El agente de Connected Machine de los servidores habilitados para Azure Arc se mejora de manera continua. Para mantenerse al día de los avances más recientes, este artículo proporciona información acerca de los elementos siguientes:

- Versiones más recientes
- Problemas conocidos
- Corrección de errores

## <a name="december-2020"></a>Diciembre de 2020

### <a name="new-feature"></a>Nueva característica

Se ha agregado soporte técnico para Windows Server 2008 R2

### <a name="fixed"></a>Fijo

Problema resuelto que impide que la extensión de script personalizado de Linux se instale correctamente.

## <a name="november-2020"></a>Noviembre de 2020

### <a name="fixed"></a>Fijo

Se resolvió un problema en el que la configuración del proxy se podía perder después de actualizar las distribuciones basadas en RPM.

## <a name="october-2020"></a>Octubre de 2020

### <a name="fixed"></a>Fijo

- Se corrigió un script de proxy para controlar la ubicación alternativa del archivo de unidad de demonio GC.
- Cambios de confiabilidad del agente GuestConfig.
- Compatibilidad del agente GuestConfig con la región US Gov Virginia.
- Los mensajes de informes de la extensión del agente GuestConfig para ser más detallados en caso de errores.

## <a name="september-2020"></a>Septiembre de 2020

Versión: 1.0 (Disponibilidad general)

### <a name="plan-for-change"></a>Plan de cambio

- La compatibilidad con las versiones preliminares de agentes (todas las versiones anteriores a 1.0) se quitará en una actualización de servicio futura.
- Se ha quitado la compatibilidad con el punto de conexión de reserva `.azure-automation.net`. Si tiene un proxy, debe permitir el punto de conexión `*.his.arc.azure.com`.
- Si el agente de Connected Machine está instalado en una máquina virtual hospedada en Azure, las extensiones de máquina virtual no se pueden instalar ni modificar desde el recurso de servidores habilitados para Arc. Esto es así para evitar que se realicen operaciones de extensión en conflicto desde los recursos **Microsoft.Compute** y **Microsoft.HybridCompute** de la máquina virtual. Use el recurso **Microsoft.Compute** de la máquina para todas las operaciones de extensión.
- El nombre del proceso Configuración de invitado ha cambiado, de *gcd* a *gcad* en Linux y de *gcservice* a *gcarcservice* en Windows.

### <a name="new-feature"></a>Nueva característica

- Se ha agregado la opción `azcmagent logs` para recopilar información de soporte técnico.
- Se ha agregado la opción `azcmagent license` para mostrar el CLUF.
- Se ha agregado la opción `azcmagent show --json` al estado del agente de salida en un formato fácilmente analizable.
- Se ha agregado una marca en la salida `azcmagent show` para indicar si el servidor está en una máquina virtual hospedada en Azure.
- Se ha agregado la opción `azcmagent disconnect --force-local-only` para permitir el restablecimiento del estado del agente local cuando no se puede acceder al servicio de Azure.
- Se ha agregado la opción `azcmagent connect --cloud` para admitir nubes adicionales. En esta versión, solo Azure es compatible con el servicio en el momento de la versión del agente.
- El agente se ha localizado en idiomas compatibles con Azure.

### <a name="fixed"></a>Fijo

- Mejoras en la comprobación de conectividad.
- Se ha corregido la incidencia con la configuración del servidor proxy que se había perdido al actualizar el agente en Linux.
- Se han resuelto incidencias al intentar instalar el agente en el servidor que ejecuta Windows Server 2012 R2.
- Mejoras en la fiabilidad de la instalación de extensiones

## <a name="august-2020"></a>Agosto de 2020

Versión: 0,11

- Esta versión ha anunciado anteriormente la compatibilidad con Ubuntu 20.04. Dado que algunas extensiones de máquina virtual de Azure no admiten Ubuntu 20.04, se está quitando la compatibilidad con esta versión de Ubuntu.

- Mejoras de la confiabilidad para las implementaciones de extensiones.

### <a name="known-issues"></a>Problemas conocidos

Si usa una versión anterior del agente de Linux y la ha configurado para usar un servidor proxy, debe volver a configurar los valores del servidor proxy después de la actualización. Para ello, ejecute `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Pasos siguientes

Antes de evaluar o habilitar los servidores habilitados para Arc en varias máquinas híbridas, consulte el artículo [Información general del agente de Connected Machine](agent-overview.md) a fin de conocer los requisitos, los detalles técnicos del agente y los métodos de implementación.