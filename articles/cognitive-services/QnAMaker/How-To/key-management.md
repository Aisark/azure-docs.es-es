---
title: 'Administración de claves y recursos: QnA Maker'
titleSuffix: Azure Cognitive Services
description: 'El servicio QnA Maker trabaja con dos tipos de claves: claves de suscripción y claves de punto de conexión.'
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b0b82f37b76a6bca6d84a05bc48b7ae9986fb76b
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967680"
---
# <a name="how-to-manage-keys-in-qna-maker"></a>Cómo administrar las claves de QnA Maker

El servicio QnA Maker trabaja con dos tipos de claves, **claves de suscripción** y **claves de punto de conexión**.

![Administración de claves](../media/qnamaker-how-to-key-management/key-management.png)

1. **Claves de suscripción**: estas claves se usan para acceder a las [API del servicio de administración de QnA Maker](https://go.microsoft.com/fwlink/?linkid=2092179). Estas API le permiten realizar ediciones en Knowledge Base.  

2. **Claves de punto de conexión**: estas claves se usan para tener acceso al punto de conexión de la base de conocimiento para obtener una respuesta para una pregunta del usuario. Normalmente se usaría este punto de conexión en el bot de chat o el código de aplicación cliente que consume el servicio QnA Maker.
 
## <a name="subscription-keys"></a>Claves de suscripción
Puede ver y restablecer las claves de suscripción desde Azure Portal, donde creó el recurso QnA Maker. 
1. Vaya al recurso QnA Maker en Azure Portal.

    ![Lista de recursos de QnA Maker](../media/qnamaker-how-to-key-management/qnamaker-resource-list.png)

2. Vaya a **Claves**.

    ![clave de suscripción](../media/qnamaker-how-to-key-management/subscription-key.PNG)

## <a name="endpoint-keys"></a>Claves de punto de conexión

Las claves de punto de conexión se pueden administrar desde el [portal de QnA Maker](https://qnamaker.ai).

1. Inicie sesión en el [portal de QnA Maker](https://qnamaker.ai), vaya a su perfil y, luego, haga clic en **Service settings** (Configuración del servicio).

    ![Clave de punto de conexión](../media/qnamaker-how-to-key-management/Endpoint-keys.png)

2. Vea o restablezca las claves.

    ![Administrador de claves de punto de conexión](../media/qnamaker-how-to-key-management/Endpoint-keys1.png)

    >[!NOTE]
    >Actualice las claves si considera que se han visto comprometidas. Esto puede requerir realizar los cambios correspondientes en el código del bot o de la aplicación cliente.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Crear una base de conocimiento en un idioma diferente](./language-knowledge-base.md)
