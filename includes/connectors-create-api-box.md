---
ms.openlocfilehash: d1db175ecc2cf61de0debc15d198d6367aae8bdd
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119981"
---
### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de [box](http://box.com).  

Antes de poder usar la cuenta de box en una aplicación lógica, debe autorizar a la aplicación lógica a conectarse a dicha cuenta. Por suerte, esto se puede hacer fácilmente dentro de la aplicación lógica en Azure Portal.  

Aquí se explica cómo autorizar a la aplicación lógica para conectarse a su cuenta de box:  

1. Para crear una conexión a box, en el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, después, escriba *box* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar:   
   ![paso de creación de conexión de box](./media/connectors-create-api-box/box-1.png)  
2. Si no ha creado ninguna conexión a box antes, se le pedirá que indique sus credenciales de box. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta de box:  
   ![paso de creación de conexión de box](./media/connectors-create-api-box/box-2.png)  
3. Indique su nombre de usuario y contraseña de box para autorizar a la aplicación lógica:  
   ![paso de creación de conexión de box](./media/connectors-create-api-box/box-3.png)  
4. Deje que nos conectemos a box:  
   ![paso de creación de conexión de box](./media/connectors-create-api-box/box-4.png)  
5. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica:   
   ![paso de creación de conexión de box](./media/connectors-create-api-box/box-5.png)  

