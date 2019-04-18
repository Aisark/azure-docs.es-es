---
title: Requisitos del sistema de Microsoft Azure Data Box Edge | Microsoft Docs
description: Obtenga más información sobre los requisitos de software y red de Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/03/2019
ms.author: alkohli
ms.openlocfilehash: d1e4af6e73c272a7ccc8996b0ccc854be64dd74b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2019
ms.locfileid: "59006349"
---
# <a name="azure-data-box-edge-system-requirements"></a>Requisitos del sistema de borde del cuadro de datos Azure

En este artículo se describen los requisitos del sistema importantes de la solución Microsoft Azure Data Box Edge y de los clientes que se conectan a Azure Data Box Edge. Se recomienda que revise detenidamente la información antes de implementar Data Box Edge. Puede consultar esta información según considere necesario durante la implementación y las operaciones posteriores.

Los requisitos del sistema de Data Box Edge incluyen:

- **Requisitos de software para hosts**: describe las plataformas compatibles, los exploradores de la interfaz de usuario de configuración local, los clientes SMB y los requisitos adicionales de los clientes que acceden al dispositivo.
- **Requisitos de red para el dispositivo**: proporciona información acerca de los requisitos de red para el funcionamiento del dispositivo físico.

## <a name="supported-os-for-clients-connected-to-device"></a>Sistema operativo compatible con los clientes conectados al dispositivo

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-supported-client-os.md)]

## <a name="supported-protocols-for-clients-accessing-device"></a>Protocolos compatibles con los clientes que acceden al dispositivo

[!INCLUDE [Supported protocols for clients accessing device](../../includes/data-box-edge-gateway-supported-client-protocols.md)]

## <a name="supported-storage-accounts"></a>Cuentas de almacenamiento admitidas

[!INCLUDE [Supported storage accounts](../../includes/data-box-edge-gateway-supported-storage-accounts.md)]

## <a name="supported-storage-types"></a>Tipos de almacenamiento admitidos

[!INCLUDE [Supported storage types](../../includes/data-box-edge-gateway-supported-storage-types.md)]

## <a name="supported-browsers-for-local-web-ui"></a>Exploradores compatibles con la interfaz de usuario web local

[!INCLUDE [Supported browsers for local web UI](../../includes/data-box-edge-gateway-supported-browsers.md)]

## <a name="networking-port-requirements"></a>Requisitos de los puertos de redes

### <a name="port-requirements-for-data-box-edge"></a>Requisitos de puertos para Data Box Edge

La siguiente tabla enumera los puertos que deben abrirse en el firewall para permitir el tráfico de administración, de la nube o de SMB. En esta tabla, *dentro* o *entrante* hace referencia a la dirección desde la que el cliente entrante solicita acceso al dispositivo. *Fuera* o *saliente* hace referencia a la dirección en la que el dispositivo de Data Box Edge envía datos externamente después de la implementación: por ejemplo, saliente a Internet.

[!INCLUDE [Port configuration for device](../../includes/data-box-edge-gateway-port-config.md)]

### <a name="port-requirements-for-iot-edge"></a>Requisitos de puertos para IoT Edge

Azure IoT Edge permite la comunicación saliente desde un dispositivo Edge local a la nube de Azure mediante protocolos de IoT Hub compatibles. La comunicación entrante solo es necesaria para escenarios específicos donde Azure IoT Hub necesita insertar mensajes en el dispositivo de Azure IoT Edge (por ejemplo, mensajes de la nube al dispositivo).

Utilice la siguiente tabla para la configuración de los puertos de los servidores que hospedan Azure IoT Edge en tiempo de ejecución:

| N.º de puerto | Dentro o fuera | Ámbito de puerto | Obligatorio | Guía |
|----------|-----------|------------|----------|----------|
| TCP 443 (HTTPS)| Fuera       | WAN        | Sí      | Abierto para comunicación de salida en el aprovisionamiento de IoT Edge. Esta configuración es necesaria cuando se usen scripts manuales o Azure IoT Device Provisioning Service (DPS).|

Para obtener información completa, vaya a [Reglas de configuración de puertos y firewall para la implementación de IoT Edge](https://docs.microsoft.com/azure/iot-edge/troubleshoot).

## <a name="url-patterns-for-firewall-rules"></a>Patrones de URL para reglas de firewall

Con frecuencia, los administradores de red pueden configurar reglas avanzadas de firewall de acuerdo con los patrones de URL para filtrar el tráfico saliente y entrante. El dispositivo de Data Box Edge y el servicio dependen de otras aplicaciones de Microsoft, como Azure Service Bus, Azure Active Directory Access Control, las cuentas de almacenamiento y los servidores de Microsoft Update. Es posible usar los patrones de URL asociados a estas aplicaciones para configurar las reglas de firewall. Es importante entender que los patrones de URL asociados a estas aplicaciones pueden cambiar. Estos cambios requieren que el administrador de red supervise y actualice las reglas de firewall de Data Box Edge según sea necesario.

Se recomienda que establezca las reglas de firewall para el tráfico saliente, basándose en las direcciones IP fijas de Data Box Edge, de forma generosa en la mayoría de los casos. Sin embargo, puede utilizar la información siguiente con el objetivo de establecer las reglas avanzadas de firewall que se necesitan para crear entornos seguros.

> [!NOTE]
> - Las direcciones IP del dispositivo (origen) siempre se deben establecer en todas las interfaces de red habilitadas para la nube.
> - Las IP de destino, por su parte, se deben establecer en los [intervalos de direcciones IP del centro de datos de Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653).

### <a name="url-patterns-for-gateway-feature"></a>Patrones de dirección URL para la característica de puerta de enlace

[!INCLUDE [URL patterns for firewall](../../includes/data-box-edge-gateway-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-feature"></a>Patrones de dirección URL para la característica de proceso

| Patrón de URL                      | Componente o funcionalidad                     |   
|----------------------------------|---------------------------------------------|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.io | Registro de contenedor de Microsoft (obligatorio)               |
| https://\*.azurecr.io                     | Registros de contenedores personales y de terceros (opcional) | 
| https://\*.azure-devices.net              | Acceso de IoT Hub (obligatorio)                             | 

### <a name="url-patterns-for-gateway-for-azure-government"></a>Patrones de dirección URL de puerta de enlace de Azure Government

[!INCLUDE [Azure Government URL patterns for firewall](../../includes/data-box-edge-gateway-gov-url-patterns-firewall.md)]

### <a name="url-patterns-for-compute-for-azure-government"></a>Patrones de dirección URL para el proceso de Azure Government

| Patrón de URL                      | Componente o funcionalidad                     |  
|----------------------------------|---------------------------------------------|
| https://mcr.microsoft.com<br></br>https://\*.cdn.mscr.com | Registro de contenedor de Microsoft (obligatorio)               |
| https://\*.azure-devices.us              | Acceso de IoT Hub (obligatorio)           |
| https://\*.azurecr.us                    | Registros de contenedores personales y de terceros (opcional) | 

## <a name="internet-bandwidth"></a>Ancho de banda de Internet

[!INCLUDE [Internet bandwidth](../../includes/data-box-edge-gateway-internet-bandwidth.md)]

## <a name="compute-sizing-considerations"></a>Consideraciones de tamaño de proceso

Use su experiencia al desarrollar y probar la solución para asegurarse de hay suficiente capacidad en el dispositivo de borde del cuadro de datos y obtener un rendimiento óptimo del dispositivo.

Debe considerar los siguientes factores:

- **Detalles de contenedor** -pensar en lo siguiente.

    - ¿Cuántos contenedores se encuentran en la carga de trabajo? Podría tener mucha contenedores ligeros en comparación con algunos estilos que consumen muchos recursos.
    - ¿Cuáles son los recursos asignados a estos contenedores frente a cuáles son los recursos que están consumiendo?
    - ¿Cuántas capas compartir los contenedores?
    - ¿Hay contenedores sin usar? Un contenedor detenido todavía ocupa espacio en disco.
    - ¿En qué idioma se escriben los contenedores?
- **Tamaño de los datos procesados** -¿cuántos datos los contenedores van a procesar? ¿Estos datos consumirá espacio en disco o los datos se procesarán en la memoria?
- **Rendimiento esperado** -¿cuáles son las características de rendimiento deseado de la solución? 

Para comprender y refinar el rendimiento de la solución, puede usar:

- Las métricas de proceso disponibles en el portal de Azure. Vaya al recurso de borde del cuadro de datos y, a continuación, vaya a **supervisión > métricas**. Examine el **Edge compute - uso de memoria** y **Edge compute: porcentaje de CPU** para comprender los recursos disponibles y cómo son los recursos de introducción consume el.
- Los comandos de supervisión disponibles a través de la interfaz de PowerShell del dispositivo, como:

    - `dkr` estadísticas para obtener estadísticas de uso de recursos de una secuencia en directo de contenedores. El comando es compatible con las métricas de E/S de red, uso de memoria, límite de memoria y CPU.
    - `dkr system df` Para obtener información relacionada con la cantidad de espacio en disco utilizado. 
    - `dkr image [prune]` para limpiar las imágenes no utilizadas y liberar espacio.
    - `dkr ps --size` Para ver el tamaño aproximado de un contenedor en ejecución. 

    Para obtener más información sobre los comandos disponibles, vaya a [supervisar y solucionar problemas de los módulos de proceso](data-box-edge-connect-powershell-interface.md#monitor-and-troubleshoot-compute-modules).

Por último, asegúrese de que validar la solución en el conjunto de datos y cuantificar el rendimiento en el borde del cuadro de datos antes de implementarlo en producción.


## <a name="next-step"></a>Paso siguiente

- [Implementación de Azure Data Box Edge](data-box-edge-deploy-prep.md)
