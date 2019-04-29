---
title: Usar Azure Automation para escalar verticalmente máquinas virtuales Windows | Microsoft Docs
description: Escalado vertical de una máquina virtual de Windows en respuesta a las alertas de supervisión con Azure Automation
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 4f964713-fb67-4bcc-8246-3431452ddf7d
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/18/2019
ms.author: kasing
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 44243f97b6c431578185fcdeb1ddcabc548d927f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60581010"
---
# <a name="vertically-scale-windows-vms-with-azure-automation"></a>Escalado vertical de máquinas virtuales de Windows con Azure Automation

El escalado vertical es el proceso de aumentar o disminuir los recursos de una máquina como respuesta a la carga de trabajo. Para lograrlo en Azure, cambie el tamaño de la máquina virtual. Esto puede ser útil en los siguientes escenarios:

* Si la máquina virtual no se utiliza con frecuencia, puede disminuir su tamaño para reducir los costos mensuales.
* Si la máquina virtual experimenta una carga máxima, es posible ajustarla en un mayor tamaño para aumentar su capacidad.

Los pasos para lograr esto se describen a continuación:

1. Configurar Azure Automation para tener acceso a las máquinas virtuales.
2. Importar los runbooks de escalado vertical de Azure Automation a la suscripción.
3. Agregar un webhook al runbook.
4. Agregar una alerta a la máquina virtual.

> [!NOTE]
> Debido al tamaño de la primera máquina virtual, los tamaños a los que se puede escalar pueden estar limitados en virtud de la disponibilidad de los demás tamaños en el clúster donde actualmente está implementada la máquina virtual. En los runbooks de automatización publicados que se usan en este artículo nos hacemos cargo de esta situación y solo escalamos dentro de los siguientes pares de tamaños de máquina virtual. Esto significa que una máquina virtual Standard_D1v2 no se aumentará de manera repentina a Standard_G5 ni se reducirá a Basic_A0. También no se admite la máquina de Virtual restringida tamaños escalar horizontal y verticalmente. Puede elegir escalar entre los siguientes pares de tamaños:
> 
> | Pares de escalado de tamaños de VM |  |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2v3 |Standard_E64v3 |
> | Standard_E2sv3 |Standard_E64sv3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> 
> 

## <a name="setup-azure-automation-to-access-your-virtual-machines"></a>Configurar Azure Automation para tener acceso a las máquinas virtuales.
Lo primero que debe hacer es crear una cuenta de Azure Automation que hospedará los runbooks que se usan para escalar una máquina virtual. Recientemente, el servicio Automation presentó la característica "Cuenta de ejecución", que facilita la configuración de la entidad de servicio para ejecutar los runbooks automáticamente en nombre de un usuario. Encontrará más información al respecto en el siguiente artículo:

* [Autenticación de Runbooks con una cuenta de ejecución de Azure](../../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-the-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar los runbooks de escalado vertical de Azure Automation a la suscripción.
Los runbooks necesarios para el escalado vertical de la máquina virtual están publicados actualmente en la galería de runbooks de Azure Automation. Deberá importarlos a la suscripción. En el siguiente artículo, puede obtener información sobre cómo importar runbooks:

* [Galerías de runbooks y módulos para Azure Automation](../../automation/automation-runbook-gallery.md)

En la imagen que aparece a continuación, se muestran los runbooks que es necesario importar:

![Importar runbooks](./media/vertical-scaling-automation/scale-runbooks.png)

## <a name="add-a-webhook-to-your-runbook"></a>Agregar un webhook al runbook.
Una vez que importe los runbooks, deberá agregar un webhook al runbook para que, de este modo, una alerta proveniente de una máquina virtual pueda desencadenarlo. A continuación, puede leer los detalles sobre cómo crear un webhook para el runbook:

* [Webhooks de Azure Automation](../../automation/automation-webhooks.md)

Asegúrese de copiar el webhook antes de cerrar el cuadro de diálogo de webhook, porque lo necesitará en la siguiente sección.

## <a name="add-an-alert-to-your-virtual-machine"></a>Agregar una alerta a la máquina virtual.
1. Seleccione la configuración de la máquina virtual.
2. Seleccione "Reglas de alerta".
3. Seleccione "Agregar alerta".
4. Seleccione una métrica según la cual activar la alerta.
5. Seleccione una condición que, cuando se cumpla, hará que se active la alerta.
6. Seleccione un umbral para la condición establecida en el paso 5 que se satisfaga.
7. Seleccione un período durante el cual el servicio de supervisión comprobará la condición y el umbral que se establecieron en los pasos 5 y 6.
8. Péguelo en el webhook que copió desde la sección anterior.

![Agregar alerta a máquina virtual 1](./media/vertical-scaling-automation/add-alert-webhook-1.png)

![Agregar alerta a máquina virtual 2](./media/vertical-scaling-automation/add-alert-webhook-2.png)

