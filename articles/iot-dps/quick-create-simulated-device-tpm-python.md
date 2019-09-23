---
title: Aprovisionamiento de un dispositivo de TPM simulado en Azure IoT Hub mediante Python | Microsoft Docs
description: 'Guía de inicio rápido de Azure: Creación y aprovisionamiento de un dispositivo de TPM simulado mediante el SDK de dispositivos para Java para IoT Hub Device Provisioning Service. Esta guía de inicio rápido utiliza inscripciones individuales.'
author: wesmc7777
ms.author: wesmc
ms.date: 05/21/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.devlang: python
ms.custom: mvc
ms.openlocfilehash: df12acad5fa9287f43cc256bfcc89fa6775c3e3b
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2019
ms.locfileid: "71001305"
---
# <a name="create-and-provision-a-simulated-tpm-device-using-python-device-sdk-for-iot-hub-device-provisioning-service"></a>Creación y aprovisionamiento de un dispositivo TPM simulado mediante el SDK de dispositivos Python para el servicio IoT Hub Device Provisioning

[!INCLUDE [iot-dps-selector-quick-create-simulated-device-tpm](../../includes/iot-dps-selector-quick-create-simulated-device-tpm.md)]

En estos pasos se muestra cómo crear un dispositivo simulado en la máquina de desarrollo que ejecuta el sistema operativo Windows, ejecutar el simulador de TPM de Windows como el [módulo de seguridad de hardware (HSM)](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/) del dispositivo y usar el código de ejemplo de Python para conectar este dispositivo simulado con el servicio Device Provisioning y su instancia de IoT Hub. 

Si no está familiarizado con el proceso de aprovisionamiento automático, no olvide revisar los [Conceptos sobre aprovisionamiento automático](concepts-auto-provisioning.md). Asegúrese de completar los pasos descritos en [Configuración del servicio Azure IoT Hub Device Provisioning con Azure Portal](./quick-setup-auto-provision.md) antes de continuar. 

Azure IoT Hub Device Provisioning Service admite dos tipos de inscripciones:
- [Grupos de inscripción](concepts-service.md#enrollment-group): usados para inscribir varios dispositivos relacionados.
- [Inscripciones individuales](concepts-service.md#individual-enrollment): usadas para inscribir un solo dispositivo.

En este artículo se mostrarán las inscripciones individuales.

[!INCLUDE [IoT Device Provisioning Service basic](../../includes/iot-dps-basic.md)]

> [!NOTE]
> Esta guía solo se aplica al SDK de Python v1 ya en desuso. Los dispositivos de TPM simulados no se admiten aún en v2. El equipo está actualmente intentado incorporar la versión 2 a la paridad de características.

## <a name="prepare-the-environment"></a>Preparación del entorno 

1. Asegúrese de que ha instalado [Visual Studio](https://visualstudio.microsoft.com/vs/) 2015 o posterior, con la carga de trabajo "Desarrollo para el escritorio con C++" habilitada para la instalación de Visual Studio.

1. Descargue e instale el [sistema de compilación CMake](https://cmake.org/download/).

1. Asegúrese de que `git` está instalado en su máquina y se agrega a las variables de entorno accesibles para la ventana de comandos. Consulte las [herramientas de cliente de Git de Software Freedom Conservancy](https://git-scm.com/download/) para instalar la versión más reciente de las herramientas `git`, lo que incluye **Git Bash**, la aplicación de línea de comandos que puede usar para interactuar con su repositorio de Git local. 

1. Abra un símbolo del sistema o Git Bash. Clone el repositorio GitHub para el ejemplo de código de simulación de dispositivo:
    
    ```cmd/sh
    git clone --single-branch --branch v1-deprecated https://github.com/Azure/azure-iot-sdk-python.git --recursive
    ```

1. Cree una carpeta en su copia local de este repositorio GitHub para el proceso de compilación de CMake. 

    ```cmd/sh
    cd azure-iot-sdk-python/c
    mkdir cmake
    cd cmake
    ```

1. El ejemplo de código utiliza un simulador de Windows TPM. Ejecute el siguiente comando para habilitar la autenticación de token SAS. También genera una solución de Visual Studio para el dispositivo simulado.

    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Duse_tpm_simulator:BOOL=ON ..
    ```

1. En un símbolo del sistema independiente, vaya a la carpeta del simulador de TPM y ejecute el simulador de [TPM](https://docs.microsoft.com/windows/device-security/tpm/trusted-platform-module-overview). Haga clic en **Permitir acceso**. Atiende en un socket en los puertos 2321 y 2322. No cierre esta ventana de comandos; debe mantener este simulador ejecutándose hasta el final de esta guía de inicio rápido. 

    ```cmd/sh
    .\azure-iot-sdk-python\c\provisioning_client\deps\utpm\tools\tpm_simulator\Simulator.exe
    ```

    ![Simulador de TPM](./media/python-quick-create-simulated-device/tpm-simulator.png)


## <a name="create-a-device-enrollment-entry"></a>Creación de una entrada de inscripción de dispositivo

1. Abra la solución generada en la carpeta *cmake* denominada `azure_iot_sdks.sln` y compílela en Visual Studio.

1. Haga clic con el botón derecho en el proyecto **tpm_device_provision** y seleccione **Establecer como proyecto de inicio**. Ejecute la solución. La ventana de salida muestra la **_clave de aprobación_** y el **_id. de registro_** necesarios para la inscripción del dispositivo. Anote estos valores. 

    ![Programa de instalación de TPM](./media/python-quick-create-simulated-device/tpm-setup.png)

1. Inicie sesión en Azure Portal, haga clic en el botón **Todos los recursos** situado en el menú izquierdo y abra el servicio Device Provisioning.

1. En la hoja de resumen del servicio Device Provisioning, seleccione **Manage enrollments** (Administrar inscripciones). Seleccione la pestaña **Inscripciones individuales** y haga clic en el botón **Agregar inscripción individual** de la parte superior. 

1. En **Agregar inscripción**, escriba la siguiente información:
   - Seleccione **TPM** como *Mecanismo* de atestación de identidad.
   - Escriba el *Identificador de registro* y la *Clave de aprobación* del dispositivo TPM. 
   - Seleccione un centro de IoT vinculado con el servicio de aprovisionamiento.
   - Escriba un identificador de dispositivo único. Asegúrese de evitar datos confidenciales al asignar nombre al dispositivo.
   - Actualice el **Estado inicial del dispositivo gemelo** con la configuración inicial deseada para el dispositivo.
   - Una vez completado, haga clic en el botón **Guardar**. 

     ![Especificación de la información de inscripción del dispositivo en la hoja de portal](./media/python-quick-create-simulated-device/enterdevice-enrollment.png)  

   Al inscribir el dispositivo correctamente, el *id. del registro* del dispositivo aparecerá en la lista de la pestaña *Individual Enrollments* (Inscripciones individuales). 


## <a name="simulate-the-device"></a>Simulación del dispositivo

1. Descargue e instale [Python 2.x o 3.x](https://www.python.org/downloads/). Asegúrese de usar la instalación de 32 bits o 64 bits en función del programa de instalación. Cuando se le solicite durante la instalación, asegúrese de agregar Python a las variables de entorno específicas de la plataforma.
    - Si usa el sistema operativo Windows, puede que necesite el [paquete redistribuible de Visual C++](https://www.microsoft.com/download/confirmation.aspx?id=48145) para permitir el uso de archivos DLL nativos de Python.

1. Siga [estas instrucciones](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) para compilar los paquetes de Python.

   > [!NOTE]
   > Si ejecuta `build_client.cmd`, asegúrese de usar la marca `--use-tpm-simulator`.
   > 
   > [!NOTE]
   > Si usa `pip`, asegúrese de instalar también el paquete `azure-iot-provisioning-device-client`. Tenga en cuenta que los paquetes de PIP publicados utilizan el TPM real y no el simulador. Para usar el simulador, debe compilar desde el código fuente mediante la marca `--use-tpm-simulator`.

1. Navegue hasta la carpeta de ejemplos.

    ```cmd/sh
    cd azure-iot-sdk-python/provisioning_device_client/samples
    ```

1. Con el IDE de Python, edite el script de Python llamado **provisioning\_device\_client\_sample.py**. Modifique las variables *GLOBAL\_PROV\_URI* e *ID\_SCOPE* con los valores que se han indicado anteriormente. Asegúrese también de que *SECURITY\_DEVICE\_TYPE* está establecido en `ProvisioningSecurityDeviceType.TPM`

    ```python
    GLOBAL_PROV_URI = "{globalServiceEndpoint}"
    ID_SCOPE = "{idScope}"
    SECURITY_DEVICE_TYPE = ProvisioningSecurityDeviceType.TPM
    PROTOCOL = ProvisioningTransportProvider.HTTP
    ```

    ![Información del servicio](./media/python-quick-create-simulated-device/extract-dps-endpoints.png)

1. Ejecute el ejemplo. 

    ```cmd/sh
    python provisioning_device_client_sample.py
    ```

1. Tenga en cuenta los mensajes que simulan el arranque del dispositivo y la conexión al servicio Device Provisioning para obtener la información del centro de IoT. 

    ![El registro se completó correctamente.](./media/python-quick-create-simulated-device/registration-success.png)

1. Si se aprovisiona correctamente el dispositivo simulado para el centro de IoT vinculado con el servicio de aprovisionamiento, el identificador de dispositivo aparece en la hoja **Device Explorer** del centro.

    ![El dispositivo se registra con el centro de IoT](./media/python-quick-create-simulated-device/hubregistration.png) 

    Si ha cambiado el valor predeterminado de *Estado inicial del dispositivo gemelo* en la entrada de inscripción para el dispositivo, el dispositivo puede extraer el estado gemelo deseado desde el centro y actuar en consecuencia. Para más información, consulte [Información y uso de dispositivos gemelos en IoT Hub](../iot-hub/iot-hub-devguide-device-twins.md)


## <a name="clean-up-resources"></a>Limpieza de recursos

Si piensa seguir trabajando con el ejemplo de cliente de dispositivo y explorándolo, no limpie los recursos que se crean en esta guía de inicio rápido. Si no va a continuar, use el siguiente comando para eliminar todos los recursos creados.

1. Cierre la ventana de salida de ejemplo del cliente del dispositivo en su máquina.
1. Cierre la ventana del simulador de TPM en su máquina.
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione el servicio Device Provisioning. Abra la hoja **Administrar inscripciones** de su servicio y, a continuación, haga clic en la pestaña **Inscripciones individuales**. Seleccione el *ID. DE REGISTRO* del dispositivo que inscribió en esta guía de inicio rápido y haga clic en el botón **Eliminar** situado en la parte superior. 
1. Desde el menú de la izquierda en Azure Portal, haga clic en **Todos los recursos** y seleccione su centro de IoT. Abra la hoja **IoT Devices** (Dispositivos IoT) de su centro, seleccione el *ID. DE DISPOSITIVO* del dispositivo que registró en esta guía de inicio rápido y, a continuación, haga clic en el botón **Eliminar** situado en la parte superior.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un dispositivo de TPM simulado en su máquina y lo ha aprovisionado con IoT Hub mediante el servicio Azure IoT Hub Device Provisioning. Para obtener información sobre cómo inscribir el dispositivo de TPM mediante programación, siga la guía de inicio rápido para la inscripción de un dispositivo de TPM mediante programación. 

> [!div class="nextstepaction"]
> [Guía de inicio rápido de Azure: Inscripción de dispositivos de TPM al Servicio Azure IoT Hub Device Provisioning](quick-enroll-device-tpm-python.md)
