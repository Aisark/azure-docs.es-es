---
title: Seguimiento del uso de un laboratorio en Azure Lab Services | Microsoft Docs
description: En este tutorial, realizará el seguimiento del uso de un laboratorio como su creador o propietario.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580228"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Tutorial: Seguimiento del uso de un laboratorio en Azure Lab Services
Este tutorial muestra cómo un creador o propietario de un laboratorio puede realizar un seguimiento de su uso.

En este tutorial realizará lo siguiente:

> [!div class="checklist"]
> * Ver los usuarios registrados con el laboratorio
> * Visualización del uso de las máquinas virtuales en el laboratorio
> * Administración de máquinas virtuales de estudiantes 


## <a name="view-users-registered-with-the-lab"></a>Ver los usuarios registrados en el laboratorio

1. Vaya al [sitio web de Azure Lab Services](https://labs.azure.com). 
2. Seleccione **Iniciar sesión** y escriba las credenciales. Azure Lab Services es compatible con cuentas profesionales y cuentas Microsoft.
3. En la página **My labs** (Mis laboratorios), seleccione el laboratorio de cuyo uso desea realizar un seguimiento. 
4. Seleccione **Users** (Usuarios) en el menú izquierdo o el icono **Usuarios**. Verá los alumnos que se han registrado en el laboratorio. Seleccione **Registration link** (Vínculo de registro), copie el vínculo y envíelo a cualquier nuevo alumno que no se haya registrado aún en el laboratorio. 

    ![Usuarios registrados](../media/tutorial-track-usage/registered-users.png)

    Para obtener más información sobre cómo agregar y administrar usuarios para el laboratorio, vea [Incorporación y administración de usuarios de laboratorio](how-to-configure-student-usage.md).

## <a name="view-the-usage-of-vms-in-the-lab"></a>Visualización del uso de las máquinas virtuales en el laboratorio 

1. Seleccione **Máquinas virtuales** en el menú de la izquierda. 
2. Confirme que ve el estado de las máquinas virtuales y el número de horas que han estado en ejecución. El tiempo que el propietario de un laboratorio emplea en una máquina virtual de un alumno no cuenta para el tiempo de uso que se muestra en la última columna. 

    ![Uso de máquinas virtuales](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Administración de máquinas virtuales de estudiantes 
En esta página, puede iniciar, detener o restablecer las máquinas virtuales de los alumnos mediante los controles de la columna **Estado** o de la barra de herramientas.

![Controles de máquinas virtuales](../media/tutorial-track-usage/vm-controls.png)

Para obtener más información sobre cómo administrar el grupo de máquinas virtuales para el laboratorio, consulte [Configuración y administración del grupo de máquinas virtuales](how-to-set-virtual-machine-passwords.md).

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los laboratorios educativos, consulte los artículos enumerados en [Guías de procedimientos](how-to-manage-lab-accounts.md).
