---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4cffbb1aaa438bac08ec77f576b32a901abacabd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60724788"
---
> [!NOTE] 
> No se puede modificar la configuración de cifrado ni las credenciales de la cuenta de almacenamiento asociadas a un contenedor de volúmenes después de crearlo.

#### <a name="to-modify-a-volume-container"></a>Para modificar un contenedor de volúmenes

1. Vaya al servicio StorSimple Device Manager y, después, a **Administración > Contenedores de volúmenes**.

2. En la lista tabular de contenedores de volúmenes, seleccione el contenedor que desea modificar. En la página **Dispositivos**, seleccione el dispositivo, haga doble clic en él y, a continuación, haga clic en la pestaña **Contenedores de volúmenes**.

2. En la lista tabular de contenedores de volúmenes, seleccione el contenedor que desea modificar. En la hoja que se abre, haga clic en **Modificar** en la barra de comandos.

    ![Modificar contenedor de volúmenes](./media/storsimple-8000-modify-volume-container/modify-vol-container1.png)

3. En la hoja **Modificar contenedor de volúmenes**, realice los siguientes pasos:
   
   1. No se pueden cambiar el nombre, la clave de cifrado ni la cuenta de almacenamiento asociados con el contenedor de volúmenes una vez especificados. Cambie la configuración de ancho de banda asociada.
      
       ![Cambiar la configuración de ancho de banda](./media/storsimple-8000-modify-volume-container/modify-vol-container2.png)

   2.  Haga clic en **OK**.
4. En la página siguiente del cuadro de diálogo **Modificar contenedor de volúmenes** :
   
   1. En la lista desplegable, elija una plantilla de ancho de banda existente.
   2. Revise la configuración de programación para la plantilla de ancho de banda especificada.
   3. Haga clic en **Guardar** y confirme los cambios.
      
       ![Confirmar cambios](./media/storsimple-8000-modify-volume-container/modify-vol-container3.png)

   3. La hoja **Contenedores de volúmenes** se actualiza para reflejar los cambios.

