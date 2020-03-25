---
title: archivo de inclusión
description: archivo de inclusión
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 03/05/2020
ms.openlocfilehash: ff449626ce528cfe0218a95330a567303c547e5f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/24/2020
ms.locfileid: "79486121"
---
1. Use las instrucciones del [SDL de Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) para instalar el SDK de Azure Machine Learning para Python

1. Cree un [área de trabajo de Azure Machine Learning](../articles/machine-learning/how-to-manage-workspace.md).

1. Escriba un [archivo de configuración](../articles/machine-learning/how-to-configure-environment.md#workspace) (**aml_config/config.json**).

1. Clone [el repositorio de GitHub](https://aka.ms/aml-notebooks).

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Inicie el servidor del cuaderno desde el directorio clonado.

    ```bash
    jupyter notebook
    ```