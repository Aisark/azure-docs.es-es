---
title: 'Interfaces comunes: IoT Plug and Play (versión preliminar) | Microsoft Docs'
description: Descripción de las interfaces comunes para desarrolladores de Plug and Play de IoT
author: ChrisGMsft
ms.author: chrisgre
ms.date: 07/16/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 848210509bf9ab0ffec35004cbb07e39d6de1bc0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69878940"
---
# <a name="iot-plug-and-play-preview-common-interfaces"></a>Interfaces comunes de IoT Plug and Play (versión preliminar)

Se espera que todos los dispositivos IoT Plug and Play implementen algunas interfaces comunes. Las interfaces comunes benefician a las soluciones de IoT porque proporcionan una funcionalidad coherente. La [certificación](tutorial-build-device-certification.md) requiere que el dispositivo implemente varias interfaces comunes. Puede recuperar las definiciones de las interfaces comunes en el repositorio de modelos global.

## <a name="summary-of-common-interfaces"></a>Resumen de las interfaces comunes

| NOMBRE | id | DESCRIPCIÓN | Implementado por el SDK de Azure IoT | Debe declararse en el modelo de funcionalidad |
| -------- | -------- | -------- | -------- | -------- | -------- |
| Información del modelo | urn:azureiot:ModelDiscovery:ModelInformation:1 | Para que los dispositivos declaren el identificador de modelo de funcionalidad y las interfaces. Se requiere para todos los dispositivos IoT Plug and Play. | Sí | Sin |
| Información del SDK de cliente de gemelos digitales | urn:azureiot:Client:SDKInformation:1 | SDK de cliente para conectar el dispositivo con Azure. Se requiere para la [certificación](tutorial-build-device-certification.md) | Sí | Sin |
| Información del dispositivo | urn:azureiot:DeviceManagement:DeviceInformation:1 | Información del hardware y del sistema operativo sobre el dispositivo. Se requiere para la [certificación](tutorial-build-device-certification.md) | Sin | Sí |
| Definición de modelo | urn:azureiot:ModelDiscovery:ModelDefinition:1 | Para que los dispositivos declaren la definición completa de su modelo de funcionalidad e interfaces. Debe implementarse cuando las definiciones de modelo no se hospedan en un repositorio de modelos. | Sin | Sí |
| Gemelo digital | urn:azureiot:ModelDiscovery:DigitalTwin:1 | Para que los desarrolladores de soluciones recuperen el identificador del modelo de funcionalidad y los identificadores de interfaz para un gemelo digital. Esta interfaz no se declara ni se implementa por un dispositivo IoT Plug and Play. | Sin | Sin |

- Implementado por el SDK de Azure IoT: si el SDK de Azure IoT implementa las funcionalidades declaradas en las interfaces. Los dispositivos IoT Plug and Play que usan el SDK de Azure IoT no necesitan implementar esta interfaz.
- Se debe declarar en el modelo de funcionalidad: si es "Sí", esta interfaz se debe declarar dentro de la sección `"implements":` del modelo de funcionalidad del dispositivo para este dispositivo IoT Plug and Play.

## <a name="retrieve-interface-definitions-from-the-public-repository"></a>Recuperación de las definiciones de interfaz desde el repositorio público

### <a name="cli"></a>CLI

Puede usar la extensión de Azure IoT para la CLI de Azure para recuperar las interfaces comunes desde el repositorio de modelos global.

```cmd/sh
az iot pnp interface show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

```cmd/sh
az iot pnp model show --interface {InterfaceID} --login {ModelRepoConnectionString}
```

### <a name="vs-code"></a>Código de VS

1. Use **Ctrl+Mayús+P** para abrir la paleta de comandos.

1. Escriba **Plug and Play** y, luego, seleccione el comando **IoT Plug and Play: Open Model Repository** (Abrir el repositorio de modelos). Elija **Global Model Repository** (Repositorio de modelos global). El repositorio del modelo global se abre en VS Code.

1. En el repositorio de modelos global, escriba el nombre de la interfaz en el campo de búsqueda.

1. Para crear una copia local de la interfaz, selecciónela en los resultados de la búsqueda y, a continuación, seleccione **Descargar**.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido acerca de las interfaces comunes, estos son algunos recursos adicionales:

- [Lenguaje de definición de gemelos digitales (DTDL)](https://aka.ms/DTDL)
- [SDK para dispositivos C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [API REST de IoT](https://docs.microsoft.com/rest/api/iothub/device)
