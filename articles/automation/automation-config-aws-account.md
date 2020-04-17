---
title: Autenticación de runbooks de Azure Automation con Amazon Web Services
description: En este artículo se describe cómo crear y validar una credencial de AWS para los Runbooks en Azure Automation y administrar los recursos de AWS.
keywords: autenticación AWS, configurar aws
services: automation
ms.subservice: process-automation
ms.date: 04/17/2018
ms.topic: conceptual
ms.openlocfilehash: 02ff9cedfbeaa36b2fafc84637ea3141b223a064
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310476"
---
# <a name="authenticate-azure-automation-runbooks-with-amazon-web-services"></a>Autenticación de runbooks de Azure Automation con Amazon Web Services

La automatización de las tareas comunes con recursos de Amazon Web Services (AWS) se puede realizar con los runbooks de Azure Automation. Puede automatizar muchas tareas en AWS mediante Runbooks de Automation exactamente igual que con recursos de Azure. Todo lo que se necesita son dos cosas:

* Una suscripción a AWS y un conjunto de credenciales. En concreto, la clave de acceso y la clave secreta de AWS. Para obtener más información, consulte el artículo [Using AWS Credentials](https://docs.aws.amazon.com/powershell/latest/userguide/specifying-your-aws-credentials.html)(Uso de credenciales de AWS).
* Una suscripción a Azure y una cuenta de Automation.

Para realizar la autenticación con AWS, debe especificar un conjunto de credenciales de AWS para autenticar los Runbooks que se ejecutan desde Azure Automation. Si ya tiene creada una cuenta de Automation y quiere usarla para autenticarse con AWS, puede seguir los pasos que se describen en la sección siguiente: Si desea dedicar una cuenta a runbooks dirigidos a recursos de AWS, primero debe crear una nueva [cuenta de Automation](automation-offering-get-started.md) (omita la opción para crear una entidad de servicio) y, luego, siga los pasos que se indican a continuación:

## <a name="configure-automation-account"></a>Configuración de la cuenta de Automation

Para que Azure Automation se comunique con AWS, primero deberá recuperar las credenciales de AWS y almacenarlas como recursos en Azure Automation. Realice los siguientes pasos que se describen en el documento de AWS [Administración de las claves de acceso para la cuenta de AWS](https://docs.aws.amazon.com/general/latest/gr/managing-aws-access-keys.html) y copie el **identificador de clave de acceso** y la **clave de acceso secreta** (también puede descargar el archivo de clave para almacenarlo en un lugar seguro).

Después de crear y copiar las claves de seguridad de AWS, debe crear un recurso de credencial con una cuenta de Azure Automation para almacenarlas de forma segura y hacer referencia a ellas con sus runbooks. Siga los pasos descritos en la sección: **Creación de un nuevo recurso de credencial** del artículo [Recursos de credenciales en Azure Automation](shared-resources/credentials.md#create-a-new-credential-asset-with-the-azure-portal) y escriba la información siguiente:

1. En el cuadro **Nombre**, escriba **AWScred** o un valor adecuado que siga las normas de nomenclatura.
2. En el cuadro **Nombre de usuario**, escriba su **identificador de acceso** y su **clave de acceso secreta** en el cuadro **Contraseña** y en **Confirmar contraseña**.

## <a name="next-steps"></a>Pasos siguientes

* Revise el artículo [Automatización de la implementación de una máquina virtual en Amazon Web Services](automation-scenario-aws-deployment.md) para aprender a crear runbooks y automatizar las tareas en AWS.
