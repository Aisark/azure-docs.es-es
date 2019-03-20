---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: 975d6625f46a6c10417793764b28d0a529a5c0cd
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2018
ms.locfileid: "58114058"
---
### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de [ProjectOnline](https://products.office.com/Project/project-online-with-project-for-office-365). 

Para poder usar su cuenta de ProjectOnline en una aplicación lógica, debe autorizar a la aplicación lógica para que pueda conectarse a dicha cuenta. Por suerte, esto es muy fácil de hacer desde la aplicación lógica en Azure Portal. 

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de ProjectOnline:

1. Para crear una conexión a ProjectOnline, en el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, luego, escriba *ProjectOnline* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar:   
   ![ProjectOnline, paso 1](./media/connectors-create-api-projectonline/projectonline-1.png)
2. Si no ha creado ninguna conexión a ProjectOnline antes, se le pedirá que indique sus credenciales de ProjectOnline. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y tener acceso a los datos de su cuenta de ProjectOnline:  
   ![ProjectOnline, paso 2](./media/connectors-create-api-projectonline/projectonline-2.png)
3. Indique su nombre de usuario y contraseña de ProjectOnline para autorizar a la aplicación lógica:  
   ![ProjectOnline, paso 3](./media/connectors-create-api-projectonline/projectonline-3.png)   
4. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica:   
   ![ProjectOnline, paso 4](./media/connectors-create-api-projectonline/projectonline-4.png)   

