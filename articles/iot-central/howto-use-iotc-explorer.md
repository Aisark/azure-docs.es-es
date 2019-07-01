---
title: Supervisar la conectividad de dispositivos con Azure IoT Central Explorer
description: Supervise los mensajes de los dispositivo y observe los cambios de los dispositivos gemelos mediante la CLI de IoT Central Explorer.
author: viv-liu
ms.author: viviali
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 4d17f0e5273c7397bd9c6a71d14b7992d8652768
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165874"
---
# <a name="monitor-device-connectivity-using-the-azure-iot-central-explorer"></a>Supervisar la conectividad de dispositivos con Azure IoT Central Explorer

*Este tema se aplica a los compiladores y administradores.*

Use la CLI de IoT Central Explorer para ver los mensajes que sus dispositivos envían a IoT Central y observe los cambios en el IoT Hub gemelo. Puede usar esta herramienta de código abierto para obtener una visión más detallada del estado de la conectividad del dispositivo y así diagnosticar los problemas de los mensajes del dispositivo que no llegan a la nube o los dispositivos que no responden a cambios gemelos.

[Visite el repositorio iotc-explorer en GitHub](https://aka.ms/iotciotcexplorercligithub).

## <a name="prerequisites"></a>Requisitos previos

+ Node.js, versión 8.x o posterior - https://nodejs.org
+ Un administrador de la aplicación debe generar un token de acceso para usarlo en iotc-explorer.

## <a name="install-iotc-explorer"></a>Instalación de iotc-explorer

Ejecute el siguiente comando desde su línea de comando paras instalarlo:

```cmd/sh
npm install -g iotc-explorer
```

> [!NOTE]
> Normalmente es necesario ejecutar el comando de instalación con `sudo` en entornos similares a Unix.

## <a name="run-iotc-explorer"></a>Ejecución de iotc-explorer

En las secciones siguientes se describen comandos y opciones comunes que puede usar al ejecutar `iotc-explorer`. Para ver el conjunto completo de comandos y opciones, pase `--help` a `iotc-explorer` o a cualquiera de sus subcomandos.

### <a name="login"></a>Inicio de sesión

Antes de comenzar, un administrador de su aplicación de IoT Central debe obtener un token de acceso para que usted pueda usarlo. El administrador debe realizar los siguientes pasos:

1. Vaya a **Administración** y, luego, **Tokens de acceso**.
1. Seleccione **Generar token**.
    ![Captura de pantalla de la página de tokens de acceso](media/howto-use-iotc-explorer/accesstokenspage.png)

1. Escriba un nombre de token, seleccione **Siguiente** y, luego, **Copiar**.
    > [!NOTE]
    > El valor del token solo se muestra una vez, por lo que se debe copiar antes de cerrar el cuadro de diálogo. Después de cerrar el cuadro de diálogo, nunca se vuelve a mostrar.

    ![Captura de pantalla del cuadro diálogo de la opción para copiar el token de acceso](media/howto-use-iotc-explorer/copyaccesstoken.png)

Puede usar el token para iniciar sesión en la CLI de la manera siguiente:

```cmd/sh
iotc-explorer login "<Token value>"
```

Si prefiere que el token no se guarde en su historial de shell, puede omitirlo y proporcionarlo cuando se le solicite:

```cmd/sh
iotc-explorer login
```

### <a name="monitor-device-messages"></a>Supervisar los mensajes del dispositivo

Puede ver los mensajes provenientes de un dispositivo específico o de todos los dispositivos en su aplicación mediante el comando `monitor-messages`. Este comando inicia un observador que envía continuamente nuevos mensajes conforme llegan:

Para ver todos los dispositivos en la aplicación, ejecute el siguiente comando:

```cmd/sh
iotc-explorer monitor-messages
```

Salida:

![salida del comando monitor-messages](media/howto-use-iotc-explorer/monitormessages.png)

Para inspeccionar un dispositivo específico, basta con agregar el identificador de dispositivo al final del comando:

```cmd/sh
iotc-explorer monitor-messages <your-device-id>
```

También puede generar un formato que sea más fácil de reconocer para la máquina si agrega la opción `--raw` al comando:

```
iotc-explorer monitor-messages --raw
```

### <a name="get-device-twin"></a>Obtener dispositivo gemelo

Puede usar el comando `get-twin` para obtener el contenido del dispositivo gemelo para un dispositivo de IoT Central. Para ello, ejecute el siguiente comando:

```cmd/sh
iotc-explorer get-twin <your-device-id>
```

Salida:

![salida del comando get-twin](media/howto-use-iotc-explorer/getdevicetwin.png)

Igual que sucede con `monitor-messages`, puede obtener un resultado que sea más fácil de reconocer para la máquina si usa la opción `--raw`:

```cmd/sh
iotc-explorer get-twin <your-device-id> --raw
```

## <a name="next-steps"></a>Pasos siguientes

Ahora que ya ha aprendido a usar IoT Central Explorer, el siguiente paso sugerido es explorar la [administración de dispositivos de IoT Central](howto-manage-devices.md).
