---
title: Creación de un recurso de Personalizer
description: La configuración del servicio incluye la forma en que el servicio trata las recompensas, la frecuencia con el servicio realiza las exploraciones, la frecuencia con que se vuelve a entrenar al modelo y la cantidad de datos que se almacenan.
ms.topic: conceptual
ms.date: 02/19/2020
ms.openlocfilehash: cb14415f3a5950ad1534d9eb8da94198a41f4f91
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/26/2020
ms.locfileid: "77624186"
---
# <a name="create-a-personalizer-resource"></a>Creación de un recurso de Personalizer

Un recurso de Personalizer es lo mismo que un bucle de aprendizaje de Personalizer. Se crea un solo recurso, o bucle de aprendizaje, para cada dominio de asunto o área de contenido que tenga. No utilice varias áreas de contenido en el mismo bucle, ya que esto confundirá el bucle de aprendizaje y proporcionará predicciones insuficientes.

Si desea que Personalizer seleccione el mejor contenido para más de un área de contenido de una página web, use un bucle de aprendizaje diferente para cada uno.


## <a name="create-a-resource-in-the-azure-portal"></a>Crear un recurso en Azure Portal

Crear un recurso de Personalizer para cada bucle de comentarios.

1. Inicie sesión en el [portal de Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). El vínculo anterior le lleva a la página **Crear** del servicio Personalizer.
1. Escriba el nombre del servicio, seleccione la suscripción, la ubicación, el plan de tarifa y el grupo de recursos.

    > [!div class="mx-imgBorder"]
    > ![Use Azure Portal para crear el recurso de Personalizer, también denominado bucle de aprendizaje.](./media/how-to-create-resource/how-to-create-personalizer-resource-learning-loop.png)

1. Seleccione **Crear** para crear el recurso.

1. Una vez implementado el recurso, seleccione el botón **Ir al recurso** para ir al recurso de Personalizer. Vaya a la página **Configuración** del nuevo recurso para [configurar el bucle de aprendizaje](how-to-settings.md).

## <a name="create-a-resource-with-the-azure-cli"></a>Creación de un recurso con la CLI de Azure

1. Inicie sesión en la CLI de Azure con el siguiente comando:

    ```bash
    az login
    ```

1. Cree un grupo de recursos, una agrupación lógica para administrar todos los recursos de Azure que piensa usar con el recurso de Personalizer.


    ```bash
    az group create \
        --name your-personalizer-resource-group \
        --location westus2
    ```

1. Cree un nuevo recurso Personalizer, el _bucle de aprendizaje_, con el siguiente comando para un grupo de recursos existente.

    ```bash
    az cognitiveservices account create \
        --name your-personalizer-learning-loop \
        --resource-group your-personalizer-resource-group \
        --kind Personalizer \
        --sku F0 \
        --location westus2 \
        --yes
    ```
## <a name="next-steps"></a>Pasos siguientes

* [Configuración](how-to-settings.md) del bucle de aprendizaje de Personalizer