---
author: mmacy
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 02/12/2020
ms.author: marsma
ms.openlocfilehash: 264604bfdf0c514e6464854f431addbc9d2dcdef
ms.sourcegitcommit: ef568f562fbb05b4bd023fe2454f9da931adf39a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2020
ms.locfileid: "77373107"
---
## <a name="sample-templates"></a>Plantillas de ejemplo
Aquí encontrará plantillas de ejemplo de personalización de interfaz de usuario:

```bash
git clone https://github.com/Azure-Samples/Azure-AD-B2C-page-templates
```

El proyecto contiene las plantillas siguientes:
- [Azul océano](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/ocean_blue)
- [Gris pizarra](https://github.com/Azure-Samples/Azure-AD-B2C-page-templates/tree/master/slate_gray)

Para usar el ejemplo:

1. Clone el repositorio en la máquina local. Seleccione una carpeta de plantilla, `/ocean_blue` o `/slate_gray`.
1. Cargue todos los archivos de la carpeta de plantilla y la carpeta `/assets` en Blob Storage, como se describe en las secciones anteriores.
1. A continuación, abra cada archivo `\*.html` en la raíz de `/ocean_blue` o `/slate_gray` y reemplace todas las instancias de direcciones URL relativas con las direcciones URL de los archivos css, de imágenes y de fuentes que cargó en el paso 2. Por ejemplo:
    ```html
    <link href="./css/assets.css" rel="stylesheet" type="text/css" />
    ```

    A 
    ```html
    <link href="https://your-storage-account.blob.core.windows.net/your-container/css/assets.css" rel="stylesheet" type="text/css" />
    ```
1. Guarde los archivos `\*.html` y cárguelos en Blob Storage.
1. Ahora modifique la directiva, que apunta al archivo HTML, como se mencionó anteriormente.
1. Si ve que faltan las fuentes, imágenes o CSS, compruebe las referencias en la directiva de extensiones y los archivos \*.html.
