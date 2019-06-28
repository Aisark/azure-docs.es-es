---
title: 'Aprovisionamiento automático de dispositivos Linux con DPS: Azure IoT Edge | Microsoft Docs'
description: Usar un TPM simulado en una máquina virtual con Linux para probar Device Provisioning Service en Azure para Azure IoT Edge
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9a549221a9e1864e1b7565f35139cb4c2a6ca65e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61248120"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-virtual-tpm-on-a-linux-virtual-machine"></a>Cree y aprovisione un dispositivo IoT Edge con un TPM virtual en una máquina virtual de Linux.

Los dispositivos Azure IoT Edge pueden aprovisionarse automáticamente con el [Servicio de aprovisionamiento de dispositivos](../iot-dps/index.yml). Si no está familiarizado con el proceso de aprovisionamiento automático, revise los [conceptos sobre aprovisionamiento automático](../iot-dps/concepts-auto-provisioning.md) antes de continuar. 

En este artículo se muestra cómo probar el aprovisionamiento automático en un dispositivo IoT Edge simulado con los pasos siguientes: 

* Cree una máquina virtual (VM) con Linux en Hyper-V con un Módulo de plataforma segura (TPM) simulado para la seguridad de hardware.
* Cree una instancia de IoT Hub Device Provisioning Service (DPS).
* Cree una inscripción individual para el dispositivo.
* Instale el entorno de ejecución de IoT Edge y conecte el dispositivo a IoT Hub.

Los pasos descritos en este artículo están destinados a la prueba.

## <a name="prerequisites"></a>Requisitos previos

* Un equipo de desarrollo de Windows [habilitado para Hyper-V](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v). En este artículo se usa Windows 10 que ejecuta una máquina virtual de servidor de Ubuntu. 
* Una instancia de IoT Hub activa. 

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>Crear una máquina virtual con Linux con TPM virtual

En esta sección, se va a crear una nueva máquina virtual con Linux en Hyper-V. Se ha configurado esta máquina virtual con un TPM simulado con el fin de que se pueda usar para probar cómo funciona el aprovisionamiento automático con IoT Edge. 

### <a name="create-a-virtual-switch"></a>Crear un conmutador virtual

Un conmutador virtual permite que la máquina virtual se conecte a una red física.

1. Abra el administrador de Hyper-V en la máquina Windows. 

2. En el menú **Acciones**, seleccione **Administrador de conmutadores virtuales**. 

3. Elija un conmutador virtual **Externo** y, después, seleccione **Crear conmutador virtual**. 

4. Asigne un nombre al nuevo conmutador virtual, por ejemplo **EdgeSwitch**. Asegúrese de que el tipo de conexión esté establecido en **Red externa** y, a continuación, seleccione **Aceptar**.

5. Un mensaje emergente advierte de que es posible que se interrumpa la conectividad de red. Seleccione **Yes** (Sí) para continuar. 

Si ve errores al crear el nuevo conmutador virtual, asegúrese de que ningún otro conmutador utilice el adaptador Ethernet y que ningún otro conmutador use el mismo nombre. 

### <a name="create-virtual-machine"></a>Crear máquina virtual

1. Descargue un archivo de imagen de disco para usarlo para la máquina virtual y guárdelo localmente. Por ejemplo, [Servidor de Ubuntu](https://www.ubuntu.com/download/server). 

2. De nuevo en el administrador de Hyper-V, en el menú **Acciones**, seleccione **Nuevo** > **Máquina virtual**.

3. Complete el **Asistente para crear nueva máquina virtual** con las siguientes configuraciones específicas:

   1. **Especificación de la generación**: seleccione **Generación 2**. Las máquinas virtuales de generación 2 tienen la virtualización anidada habilitada, lo cual es necesario para ejecutar IoT Edge en una máquina virtual.
   2. **Configurar funciones de red**: establezca el valor de **Conexión** en el conmutador virtual que creó en la sección anterior. 
   3. **Opciones de instalación**: seleccione **Instalar un sistema operativo desde un archivo de imagen de arranque** y busque el archivo de imagen de disco que ha guardado localmente.

4. Seleccione **Finalizar** en el asistente para crear la máquina virtual.

La creación de la máquina virtual puede tardar unos minutos. 

### <a name="enable-virtual-tpm"></a>Habilitar TPM virtual

Una vez creada la máquina virtual, abra la configuración para habilitar el módulo de plataforma segura (TPM) virtual, que permite aprovisionar de forma automática el dispositivo. 

1. Seleccione la máquina virtual y luego abra la **Configuración**.

2. Navegue hasta **Seguridad**. 

3. Anule la selección de la opción **Habilitar arranque seguro**.

4. Seleccione **Habilitar módulo de plataforma segura**. 

5. Haga clic en **Aceptar**.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>Iniciar la máquina virtual y recopilar datos de TPM

En la máquina virtual, cree una herramienta de SDK de C que se pueda utilizar para recuperar la **Id. de registro** y la **clave de aprobación** del dispositivo. 

1. Inicie la máquina virtual y conéctese.

2. Siga las indicaciones en la máquina virtual para finalizar el proceso de instalación y reinicie la máquina. 

3. Inicie sesión en la máquina virtual y luego siga los pasos de [Configurar un entorno de desarrollo con Linux](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) para instalar y compilar el SDK de dispositivo IoT de Azure para C. 

   >[!TIP]
   >Durante este artículo, copiará en la máquina virtual y pegará desde esta, cosa que no es sencilla, a través de la aplicación de conexión de administrador de Hyper-V. Es posible que quiera conectarse a la máquina virtual a través del administrador de Hyper-V una vez para recuperar su dirección IP: `ifconfig`. Después, puede usar la dirección IP para conectarse a través de SSH: `ssh <username>@<ipaddress>`.

4. Ejecute los comandos siguientes para crear una herramienta de SDK de C que recupere la información de aprovisionamiento de dispositivos. 

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

5. Copie los valores de **Id. de registro** y **Clave de aprobación**. Use estos valores para crear una inscripción individual para el dispositivo en DPS. 

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>Configurar IoT Hub Device Provisioning Service

Cree una nueva instancia de IoT Hub Device Provisioning Service en Azure y vincúlela a IoT Hub. Puede seguir las instrucciones de [Configuración de IoT Hub DPS](../iot-dps/quick-setup-auto-provision.md).

Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge. 

## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Recupere la información de aprovisionamiento de la máquina virtual y úsela para crear una inscripción individual en Device Provisioning Service. 

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **Estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](how-to-deploy-monitor.md). 


1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia de IoT Hub Device Provisioning Service. 

2. En **Configuración**, seleccione **Administrar inscripciones**. 

3. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. En **Mecanismo**, seleccione **TPM**. 
   
   2. Proporcione la **Clave de aprobación** y la **Id. de registro** que se ha copiado de la máquina virtual.
   
   3. Seleccione **Verdadero** para declarar que esta máquina virtual es un dispositivo IoT Edge. 
   
   4. Elija la instancia de **IoT Hub** vinculada a la que quiere conectar el dispositivo. Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada. 
   
   5. Si lo desea, proporcione un identificador para el dispositivo. Puede usar identificadores de dispositivo para dirigirse a un dispositivo individual para la implementación del módulo. Si no proporciona un id. de dispositivo, se usará el id. de registro.
   
   6. Agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**, si lo desea. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo: 

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   7. Seleccione **Guardar**. 

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación. 

## <a name="install-the-iot-edge-runtime"></a>Instalación del entorno de ejecución de IoT Edge

El runtime de IoT Edge se implementa en todos los dispositivos de IoT Edge. Sus componentes se ejecutan en contenedores y permiten implementar contenedores adicionales en el dispositivo para que pueda ejecutar código en Edge. Instale el entorno de ejecución de IoT Edge en la máquina virtual. 

Averigüe el **Ámbito de id.** de DPS y la **Id. de registro** del dispositivo antes de comenzar el artículo que coincide con el tipo de su dispositivo. Si instaló el servidor de Ubuntu de ejemplo, use las instrucciones de **x64**. Asegúrese de configurar el entorno de ejecución de IoT Edge para el aprovisionamiento automático, no manual. 

* [Instalar el entorno de ejecución de Azure IoT Edge en Linux (x64)](how-to-install-iot-edge-linux.md)
* [Instalación del entorno de ejecución de Azure IoT Edge en Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>Conceder acceso a IoT Edge en el TPM

Para que el entorno de ejecución de IoT Edge aprovisione automáticamente el dispositivo, necesita acceso al TPM. 

Puede conceder acceso TPM al entorno de ejecución de Azure IoT Edge mediante la invalidación de la configuración de systemd para que el servicio **iotedge** tenga privilegios raíz. Si no desea elevar los privilegios de servicio, también puede usar los pasos siguientes para proporcionar manualmente el acceso TPM. 

1. Busque la ruta de acceso para el módulo de hardware TPM en el dispositivo y guárdela como variable local. 

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. Cree una nueva regla que conceda al entorno de ejecución de IoT Edge acceso a tpm0. 

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. Abra el archivo de reglas. 

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. Copie la siguiente información de acceso en el archivo de reglas. 

   ```input 
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", GROUP="iotedge", MODE="0660"
   ```

5. Guarde y cierre el archivo. 

6. Desencadene el sistema udev para evaluar la nueva regla. 

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. Compruebe que la regla se haya aplicado correctamente.

   ```bash
   ls -l /dev/tpm0
   ```

   La salida correcta tendrá un aspecto similar al siguiente:

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   Si no ve que se hayan aplicado los permisos correctos, intente reiniciar la máquina para actualizar udev. 

8. Abra el archivo de invalidaciones del entorno de ejecución de IoT Edge. 

   ```bash
   sudo systemctl edit iotedge.service
   ```

9. Agregue el código siguiente para establecer una variable de entorno de TPM.

   ```input
   [Service]
   Environment=IOTEDGE_USE_TPM_DEVICE=ON
   ```

10. Guarde y cierre el archivo.

11. Compruebe que la invalidación se realizó correctamente.

    ```bash
    sudo systemctl cat iotedge.service
    ```

    En la salida correcta se muestran las variables de servicio predeterminadas **iotedge** y, a continuación, se muestra la variable de entorno que estableció en **override.conf**. 

12. Vuelva a cargar la configuración.

    ```bash
    sudo systemctl daemon-reload
    ```

## <a name="restart-the-iot-edge-runtime"></a>Reinicie el entorno de ejecución de IoT Edge

Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo. 

   ```bash
   sudo systemctl restart iotedge
   ```

Compruebe que el entorno de ejecución de IoT Edge está en ejecución. 

   ```bash
   sudo systemctl status iotedge
   ```

Si ve errores de aprovisionamiento, es posible que los cambios de configuración no hayan surtido efecto todavía. Pruebe a reiniciar de nuevo el demonio de IoT Edge. 

   ```bash
   sudo systemctl daemon-reload
   ```
   
O bien, pruebe a reiniciar la máquina virtual para ver si los cambios surten efecto con un inicio nuevo. 

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede entrar en su instancia de IoT Hub y ver que el nuevo dispositivo se aprovisionó automáticamente. Ahora el dispositivo está listo para ejecutar módulos de IoT Edge. 

Compruebe el estado de IoT Edge Daemon.

```cmd/sh
systemctl status iotedge
```

Examine los registros del daemon.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

Puede comprobar que la inscripción individual que ha creado se ha utilizado en el servicio Device Provisioning. En Azure Portal, vaya a la instancia del servicio Device Provisioning. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo. 

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer la id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-monitor.md) o la [CLI de Azure](how-to-deploy-monitor-cli.md).
