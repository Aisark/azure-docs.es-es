---
title: 'Qué es OPC Vault: Azure | Microsoft Docs'
description: En este artículo se proporciona información general de OPC Vault. Puede configurar, registrar y administrar el ciclo de vida de los certificados para aplicaciones de OPC UA en la nube.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826213"
---
# <a name="what-is-opc-vault"></a>¿Qué es OPC Vault?

OPC Vault es un microservicio que puede configurar, registrar y administrar el ciclo de vida de los certificados para aplicaciones cliente y servidor de OPC UA en la nube. En este artículo se describen los casos de uso sencillos de OPC Vault.

## <a name="certificate-management"></a>Administración de certificados

Por ejemplo, una empresa de fabricación debe conectar su máquina servidor de OPC UA a su aplicación cliente recién creada. Cuando el fabricante crea el acceso inicial de la máquina servidor, se muestra inmediatamente un mensaje de error en la aplicación servidor de OPC UA para indicar que la aplicación cliente no es segura. Este mecanismo se incorpora en la máquina servidor de OPC UA para evitar el acceso no autorizado a las aplicaciones, y así impedir la piratería feroz en la planta.

## <a name="application-security-management"></a>Administración de la seguridad de aplicaciones
Un profesional de seguridad usa el microservicio OPC Vault para permitir que el servidor OPC UA se comunique fácilmente con cualquier aplicación cliente, porque OPC Vault tiene todas las funciones para el registro, el almacenamiento y la administración del ciclo de vida de los certificados. Ahora que el servidor de OPC UA está conectado de forma segura, se puede comunicar con la aplicación cliente recién creada.

## <a name="the-complete-opc-vault-architecture"></a>La arquitectura completa de OPC Vault
El siguiente diagrama ilustra la arquitectura completa de OPC Vault.

![Arquitectura de OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido sobre OPC Vault y sus usos, este es el siguiente paso sugerido:

> [!div class="nextstepaction"]
> [Arquitectura de OPC Vault](overview-opc-vault-architecture.md)
