---
title: Solución de problemas en un área de trabajo
titleSuffix: Azure Machine Learning Studio
description: En esta guía se proporcionan soluciones para solventar algunos de los desafíos más comunes al configurar áreas de trabajo de Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/20/2017
ms.openlocfilehash: 7cc825daa29a0398793f3c6fc5ce8ee426ad79e6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60193872"
---
# <a name="troubleshooting-guide-create-and-connect-to-an-azure-machine-learning-studio-workspace"></a>Guía de solución de problemas: creación y conexión a un área de trabajo de Azure Machine Learning Studio
En esta guía se proporcionan soluciones para solventar algunos de los desafíos más comunes al configurar áreas de trabajo de Azure Machine Learning Studio.



## <a name="workspace-owner"></a>Propietario del espacio de trabajo
Para abrir un área de trabajo en Machine Learning Studio, debe iniciar sesión en la cuenta Microsoft que utilizó para crear el área de trabajo, o bien, puede ser necesario que reciba una invitación del propietario para unirse al área de trabajo. Desde Azure Portal puede administrar el área de trabajo, lo que incluye la capacidad de configurar el acceso.

Para más información acerca de cómo administrar un área de trabajo, consulte [Administración de un área de trabajo de Azure Machine Learning Studio].

[Administración de un área de trabajo de Azure Machine Learning Studio]: manage-workspace.md

## <a name="allowed-regions"></a>Regiones permitidas
Machine Learning se encuentra actualmente disponible en un número limitado de regiones. Si su suscripción no incluye una de estas regiones, es posible que vea el mensaje de error "No dispone de una suscripción en las regiones permitidas".

Para solicitar que una región se agregue a su suscripción, cree una nueva solicitud de soporte técnico de Microsoft desde Azure Portal, elija **Facturación** como el tipo de problema y siga las indicaciones para enviar su solicitud.

## <a name="storage-account"></a>Cuenta de almacenamiento
El servicio Machine Learning necesita una cuenta de almacenamiento para almacenar los datos. Puede utilizar una cuenta de almacenamiento existente o crear una nueva cuenta de almacenamiento al crear la nueva área de trabajo de Machine Learning Studio (si dispone de cuota para crear una nueva cuenta de almacenamiento).

Tras crear la nueva área de trabajo de Machine Learning Studio, puede iniciar sesión en Machine Learning Studio con la cuenta de Microsoft que usó para crear el área de trabajo. Si encuentra el mensaje de error "No se encontró el área de trabajo" (similar a la captura de pantalla siguiente), use estos pasos para eliminar las cookies del explorador.

![Espacio de trabajo no encontrado](media/troubleshooting-creating-ml-workspace/screen3.png)

**Para eliminar las cookies del explorador**

1. Si usa Internet Explorer, haga clic en el botón **Herramientas** en la esquina superior derecha y seleccione **Opciones de Internet**.  

   ![Opciones de Internet](media/troubleshooting-creating-ml-workspace/screen4.png)

2. En la pestaña **General**, haga clic en **Eliminar…**

   ![Pestaña General](media/troubleshooting-creating-ml-workspace/screen5.png)

3. En el cuadro de diálogo **Eliminar el historial de exploración**, asegúrese de que **Cookies y datos del sitio web** está seleccionado y haga clic en **Eliminar**.

   ![Eliminación de cookies](media/troubleshooting-creating-ml-workspace/screen6.png)

Una vez eliminadas las cookies, reinicie el explorador y vaya a la página [Microsoft Azure Machine Learning Studio](https://studio.azureml.net). Cuando se le solicite un nombre de usuario y contraseña, especifique la misma cuenta Microsoft que usó para crear el área de trabajo.

## <a name="comments"></a>Comentarios

Nuestro objeto es hacer que la experiencia de Machine Learning sea lo más directa posible. Publique cualquier comentario o problema en el [foro de Azure Machine Learning](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=MachineLearning) para ayudarnos a ofrecerle un mejor servicio.
