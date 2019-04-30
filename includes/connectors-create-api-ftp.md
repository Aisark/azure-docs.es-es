---
ms.openlocfilehash: 65f1e6d2489775a17ba2dacef0623706364fffab
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62108988"
---
### <a name="prerequisites"></a>Requisitos previos
* Una cuenta de [FTP](https://wikipedia.org/wiki/File_Transfer_Protocol).  

Antes de poder usar la cuenta de FTP en una aplicación lógica, debe autorizar a la aplicación lógica a conectare a dicha cuenta. Por suerte, esto se puede hacer fácilmente desde dentro de la aplicación lógica en Azure Portal.  

Aquí se explica cómo autorizar a la aplicación lógica a conectarse a su cuenta de FTP:  

1. Para crear una conexión a FTP, en el diseñador de Logic Apps, seleccione **Mostrar API administradas por Microsoft** en la lista desplegable y, luego, escriba *FTP* en el cuadro de búsqueda. Seleccione el desencadenador o la acción que quiera usar:   
   ![paso de creación de conexión de FTP](./media/connectors-create-api-ftp/ftp-1.png)  
2. Si no ha creado ninguna conexión a FTP antes, se le pedirá que indique sus credenciales de FTP. Estas credenciales se usarán para autorizar a la aplicación lógica a conectarse y acceder a los datos de su cuenta de FTP:  
   ![paso de creación de conexión de FTP](./media/connectors-create-api-ftp/ftp-2.png)  
3. Observe que la conexión se ha creado y que puede continuar sin problemas con el resto de pasos en la aplicación lógica:  
   ![paso de creación de conexión de FTP](./media/connectors-create-api-ftp/ftp-3.png)  

