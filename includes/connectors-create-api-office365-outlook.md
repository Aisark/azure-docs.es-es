---
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 11/03/2016
ms.author: estfan
ms.openlocfilehash: b216de0a5094066977467b2899567122d585fb7e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66149728"
---
#### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de Azure; puede crear una [gratuita](https://azure.microsoft.com/free)
* Una cuenta de [Office 365](https://office365.com).  

Antes de usar su cuenta de Office 365 en una aplicación lógica, autorice a la aplicación lógica a conectarse a su cuenta de Office 365. Esto lo puede hacer fácilmente dentro de la aplicación lógica en el Portal de Azure.  

Autorice a la aplicación lógica a conectarse a su cuenta de Office 365 con los pasos siguientes:

1. Cree una aplicación lógica. En el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, luego, escriba "office 365" en el cuadro de búsqueda. Seleccione uno de los desencadenadores o una de las acciones:  
    ![paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-sendemail.png)  
2. Si no ha creado anteriormente ninguna conexión a Office 365, se le pedirá que inicie sesión con sus credenciales de Office 365:  
    ![Paso de creación de una conexión a Office 365](./media/connectors-create-api-office365-outlook/office365-signin.png)  
3. Seleccione **Iniciar sesión** y escriba su nombre de usuario y contraseña. Seleccione **Iniciar sesión**:  
    ![paso de creación de conexión de Office 365](./media/connectors-create-api-office365-outlook/office365-usernamepassword.png)
   
    Estas credenciales se usan para autorizar a la aplicación lógica a conectarse y acceder a su cuenta de Office 365. 
4. Observe que la conexión se ha creado en el portal. Ahora, continúe con el resto de los pasos en la aplicación lógica:   
    ![Paso de creación de una conexión a Office 365](./media/connectors-create-api-office365-outlook/office365-sendemailproperties.png)  

