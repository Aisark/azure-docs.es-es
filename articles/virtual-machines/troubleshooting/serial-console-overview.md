---
title: Consola serie de Azure para Linux | Microsoft Docs
description: Consola serie bidireccional para máquinas virtuales y conjuntos de escalado de máquinas virtuales de Azure.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/6/2019
ms.author: alsin
ms.openlocfilehash: cc964b1b11a68921c1d5d2b68a7413934565fdc8
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70129452"
---
# <a name="azure-serial-console"></a>Consola serie de Azure

La consola serie de Azure Portal proporciona acceso a una consola basada en texto para máquinas virtuales e instancias de conjunto de escalado de máquinas virtuales que se ejecutan en Linux o Windows. Esta conexión serie se conecta al puerto serie ttyS0 o COM1 de la máquina virtual o de la instancia de conjunto de escalado de máquinas virtuales y ofrece acceso independiente del estado del sistema operativo o de la red. A la consola serie solo se puede acceder mediante Azure Portal; además, esta solo se permite a los usuarios que tienen un rol de acceso de colaborador o superior en el conjunto de escalado de máquinas virtuales o la máquina virtual.

La consola serie funciona de la misma manera para las máquinas virtuales y las instancias de conjunto de escalado de máquinas virtuales. En este documento, todas las menciones a las máquinas virtuales incluirán implícitamente las instancias de conjunto de escalado de máquinas virtuales, a menos que se indique lo contrario.

> [!NOTE]
> La consola serie está disponible con carácter general en regiones de Azure globales. Aún no está disponible en las nubes de Azure Government ni Azure China.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Requisitos previos para acceder a la consola serie de Azure
Para acceder a la consola serie de la máquina virtual o de la instancia de conjunto de escalado de máquinas virtuales, necesitará lo siguiente:

- Los diagnósticos de arranque deben estar habilitados para la máquina virtual.
- Debe existir en la máquina virtual una cuenta de usuario que use la autenticación de contraseña. Puede crear un usuario basado en contraseña con la función para [restablecer la contraseña](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de la extensión de acceso de máquina virtual. Seleccione **Restablecer contraseña** en la sección **Soporte técnico y solución de problemas**.
- La cuenta de Azure que accede a la consola serie debe tener el [rol Colaborador de la máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para la máquina virtual y la cuenta de almacenamiento de [diagnósticos de arranque](boot-diagnostics.md).

> [!NOTE]
> - No se admiten las implementaciones clásicas. La máquina virtual o la instancia de conjunto de escalado de máquinas virtuales deben usar el modelo de implementación de Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Introducción al uso de la consola serie
La consola serie para las máquinas virtuales y los conjuntos de escalado de máquinas virtuales solo es accesible mediante Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Consola serie para máquinas virtuales
Usar la consola serie para máquinas virtuales es tan sencillo como hacer clic en **Consola serie** en la sección **Soporte técnico y solución de problemas** de Azure Portal.
  1. Abra [Azure Portal](https://portal.azure.com).

  1. Vaya a **Todos los recursos** y seleccione una máquina virtual. Se abrirá la página de información general de la máquina virtual.

  1. Desplácese hacia abajo hasta la sección **Soporte técnico y solución de problemas** y seleccione la opción **Consola serie**. Se abrirá un panel nuevo con la consola serie y se iniciará la conexión.

     ![Ventana de la consola serie de Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Consola serie para los conjuntos de escalado de máquinas virtuales
La consola serie está disponible para los conjuntos de escalado de máquinas virtuales y es accesible en cada instancia del conjunto de escalado. Tendrá que ir a la instancia individual de un conjunto de escalado de máquinas virtuales para ver el botón **Consola serie**. Si su conjunto de escalado de máquinas virtuales no tiene habilitado el diagnóstico de arranque, asegúrese de actualizar el modelo para habilitarlo y actualice todas las instancias en el nuevo modelo para acceder a la consola serie.
  1. Abra [Azure Portal](https://portal.azure.com).

  1. Vaya a **Todos los recursos** y seleccione un conjunto de escalado de máquinas virtuales. Se abrirá la página de información general del conjunto de escalado de máquinas virtuales.

  1. Vaya a **Instancias**.

  1. Seleccione un conjunto de escalado de máquinas virtuales.

  1. En la sección **Soporte técnico y solución de problemas**, seleccione **Consola serie**. Se abrirá un panel nuevo con la consola serie y se iniciará la conexión.

     ![Consola serie para conjuntos de escalado de máquinas virtuales Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Usos avanzados de la consola serie
Además del acceso desde la consola a la máquina virtual, puede usar la consola serie de Azure para lo siguiente:
* Enviar un [comando de solicitud del sistema a la máquina virtual](./serial-console-nmi-sysrq.md)
* Enviar una [interrupción no enmascarable a la máquina virtual](./serial-console-nmi-sysrq.md)
* [Reiniciar correctamente o forzar el ciclo de energía de la máquina virtual](./serial-console-power-options.md)


## <a name="next-steps"></a>Pasos siguientes
Encontrará más documentación sobre la consola serie en la barra lateral.
- Hay más información disponible sobre la [consola serie para máquinas virtuales Linux](./serial-console-linux.md).
- Hay más información disponible sobre la [consola serie para máquinas virtuales Windows](./serial-console-windows.md).
