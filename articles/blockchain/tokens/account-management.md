---
title: Administración de cuentas de Azure Blockchain Tokens
description: Con la administración de cuentas de Azure Blockchain Tokens, puede crear grupos y vincular cuentas de Blockchain para controlar el acceso a las acciones de Blockchain.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: 91dcadd3c7704b7b8c6cab45005bc83e1bba18bb
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579771"
---
# <a name="azure-blockchain-tokens-account-management"></a>Administración de cuentas de Azure Blockchain Tokens

[!INCLUDE [Preview note](./includes/preview.md)]

En el caso de una solución de cadena de bloques, los usuarios pueden requerir distintos niveles de acceso a los tokens que se crean con el servicio de Azure Blockchain Tokens. En la mayoría de los escenarios de Blockchain, debe planear e implementar diferentes cuentas de Blockchain que existen en el libro de contabilidad. También debe administrar el acceso entre los participantes. Con la administración de cuentas de Azure Blockchain Tokens, puede crear grupos y vincular cuentas de Blockchain para controlar el acceso a las acciones de Blockchain.

## <a name="blockchain-networks"></a>Redes de cadena de bloques

Azure Blockchain Tokens permite la implementación y administración de tokens en un conjunto de redes de cadena de bloques. Puede conectar uno o varios libros de contabilidad de cadena de bloques al servicio.

## <a name="accounts"></a>Cuentas

En el caso de redes de cadena de bloques conectadas a Azure Blockchain Tokens, el servicio crea y administra los pares de claves públicas y privadas de la cuenta y realiza la firma y el envío de transacciones. Azure Blockchain Tokens también proporciona asignación de identidad para hacer coincidir las cuentas con la identidad de clave pública en el libro de contabilidad.

## <a name="groups"></a>Grupos

Los grupos le permiten administrar un gran número de cuentas de Blockchain en redes conectadas. Puede realizar el seguimiento y la auditoría de las aplicaciones y los usuarios del directorio que tienen la capacidad de usar cuentas a través de las API de Azure Blockchain Tokens. Por ejemplo, puede agrupar un conjunto de cuentas que representen distintas líneas de negocio o roles diferentes y el acceso a los tokens de Blockchain.

También puede asociar un grupo a una entidad de servicio o a un usuario de Azure Active Directory y esta entidad tiene permisos para el grupo y sus cuentas asociadas.  

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de las [plantillas de Azure Blockchain Tokens](templates.md) disponibles.
