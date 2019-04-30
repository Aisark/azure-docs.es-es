---
title: Administración de inscripciones de dispositivos con Azure Portal | Microsoft Docs
description: Procedimientos para administrar las inscripciones de dispositivos para el servicio de aprovisionamiento de dispositivos en Azure Portal
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51b072bfd0827528a5504133dff8c1cdd7a7ca86
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122776"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Administración de inscripciones de dispositivos con Azure Portal

Una *inscripción de dispositivos* crea un registro de un único dispositivo o grupo de dispositivos que, en algún momento, se registrará en el servicio Azure IoT Hub Device Provisioning. El registro de inscripción contiene la configuración inicial preferida para los dispositivos como parte de la inscripción, incluida la instancia de IoT. En este artículo se muestra cómo administrar las inscripciones de dispositivos para el servicio de aprovisionamiento.


## <a name="create-a-device-enrollment"></a>Creación de una inscripción de dispositivos

Hay dos maneras de inscribir los dispositivos con el servicio de aprovisionamiento:

* Un **grupo de inscripción** es una entrada de un grupo de dispositivos que comparten un mecanismo de atestación común de certificados X.509, firmado por el mismo certificado de firma, que puede ser el [certificado raíz](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) o el [certificado intermedio](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate), y que se usa para generar un certificado de dispositivo en un dispositivo físico. Se recomienda usar un grupo de inscripción para un gran número de dispositivos que compartan la configuración inicial deseada o para dispositivos que vayan todos al mismo inquilino. Tenga en cuenta que solo se pueden inscribir los dispositivos que usen el mecanismo de atestación de X.509 como *grupos de inscripción*. 

    Se puede crear un grupo de inscripción en el portal para un grupo de dispositivos mediante los siguientes pasos:

  1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú a la izquierda.  
  2. Haga clic en el servicio de aprovisionamiento de dispositivos donde desee inscribir su dispositivo en la lista de recursos.  
  3. En el servicio de aprovisionamiento:  
      a. Haga clic en **Administrar inscripciones**y seleccione la pestaña **Grupos de inscripción**.  
     b. Haga clic en el botón **Agregar** de la parte superior.  
     c. Cuando aparezca el panel de incorporación de grupos de inscripción, escriba la información de entrada en la lista de inscripción.  El **nombre del grupo** es obligatorio. Seleccione también "de entidad de certificación" o "intermedio" como **Tipo de certificado** y cargar el **certificado principal** raíz para el grupo de dispositivos.  
     d. Haga clic en **Save**(Guardar). Si el grupo de inscripción se creó correctamente, verá el nombre del grupo aparecer en la pestaña **Enrollment Groups** (Grupos de inscripción).  

     [![Grupo de inscripción en el portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Una **inscripción individual** es una entrada para el registro de un único dispositivo. Las inscripciones individuales pueden usar certificados X.509 o tokens de SAS (de un módulo de plataforma segura físico o virtual) como mecanismos de atestación. Se recomiendan las inscripciones individuales para los dispositivos que requieran configuraciones iniciales únicas o para los dispositivos que solo se puedan utilizar tokens de SAS en módulos de plataforma segura reales o virtuales como mecanismo de atestación. En las inscripciones individuales se puede especificar el identificador de dispositivo del centro de IoT deseado.

    Para crear una inscripción individual en el portal, siga estos pasos:

    1. Inicie sesión en Azure Portal y haga clic en **Todos los recursos** en el menú a la izquierda.
    1. Haga clic en el servicio de aprovisionamiento de dispositivos donde desee inscribir su dispositivo en la lista de recursos.
    1. En el servicio de aprovisionamiento:  
        a. Haga clic en **Administrar inscripciones** y seleccione la pestaña **Inscripciones individuales**.  
       b. Haga clic en el botón **Agregar** de la parte superior.   
       c. Cuando aparezca el panel de incorporación de inscripciones, escriba la información de entrada en la lista de inscripción. Seleccione primero el **Mecanismo** de atestación para el dispositivo (X.509 o TPM). La atestación de X.509 requiere la carga del **certificado principal** de hoja para el dispositivo. TPM requiere que escriba la **clave de atestación** y el **identificador de registro** del dispositivo.  
       d. Haga clic en **Save**(Guardar). Si el grupo de inscripción se creó correctamente, verá el nombre del dispositivo aparecer en la pestaña **Individual Enrollments** (Inscripciones individuales).  

       [![Inscripción individual en el portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Actualización de una entrada de inscripción
Para actualizar una entrada de inscripción existente, siga estos pasos:

1. Abra el servicio de aprovisionamiento de dispositivos en Azure Portal y haga clic en **Manage Enrollments** (Administrar inscripciones). 
1. Navegue a la entrada de inscripción que desee modificar. Haga clic en la entrada para abrir un resumen de la información sobre la inscripción del dispositivo. 
1. En esta página podrá modificar otros elementos distintos del tipo de seguridad y las credenciales, como la instancia de IoT Hub a la que vincular el dispositivo y el identificador de este. También podrá modificar el estado inicial del dispositivo gemelo. 
1. Al terminar, haga clic en **Guardar** para actualizar la inscripción del dispositivo. 

    ![Actualización de la inscripción en el portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Eliminación de una inscripción de dispositivo
Cuando no sea necesario aprovisionar dispositivos en ninguna instancia de IoT Hub, para eliminar la entrada de inscripción asociada en el portal siga estos pasos:

1. Abra el servicio de aprovisionamiento de dispositivos en Azure Portal y haga clic en **Manage Enrollments** (Administrar inscripciones). 
1. Navegue a la entrada de inscripción que desee eliminar y selecciónela. 
1. Haga clic en el botón **Eliminar** de la parte superior y seleccione **Sí** cuando se le pida confirmación. 
1. Una vez completada la acción, verá que se ha eliminado la entrada de la lista de inscripciones de dispositivos. 
 
    ![Eliminación de la inscripción en el portal](./media/how-to-manage-enrollments/remove-enrollment.png)


