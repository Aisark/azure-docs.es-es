---
title: 'Tutorial: Implementación de la aplicación de ASP.NET y el código de Azure SQL Database mediante Azure DevOps Projects'
description: Con DevOps Projects es fácil empezar a usar Azure. Con DevOps Projects puede implementar la aplicación de ASP.NET y el código de Azure SQL Database en pocos pasos.
ms.author: mlearned
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/09/2018
author: mlearned
monikerRange: vsts
ms.openlocfilehash: 0d05a2f3de92791572f0a5e6313777b5388af3df
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/19/2019
ms.locfileid: "57845229"
---
# <a name="tutorial-deploy-your-aspnet-app-and-azure-sql-database-code-by-using-azure-devops-projects"></a>Tutorial: Implementación de la aplicación de ASP.NET y el código de Azure SQL Database mediante Azure DevOps Projects

Azure DevOps Projects ofrece una experiencia simplificada en la que puede utilizar su código existente y el repositorio de Git, o elegir una aplicación de ejemplo para crear una canalización de integración continua (CI) y entrega continua (CD) en Azure. 

DevOps Projects también:
* Crea automáticamente recursos de Azure, como una base de datos SQL de Azure.
* Crea y configura una canalización de versión en Azure Pipelines que incluye una canalización de compilación para integración continua.
* Configura una canalización de versión para implementación continua. 
* Crea un recurso de Azure Application Insights para la supervisión.

En este tutorial, aprenderá lo siguiente:

> [!div class="checklist"]
> * A usar Azure DevOps Projects para implementar una aplicación de ASP.NET y código de Azure SQL Database
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Examen de la canalización de CI
> * Examen de la canalización de CD
> * Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure
> * A conectar con la base de datos SQL de Azure 
> * Limpieza de recursos

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure. Puede obtener una gratuita mediante [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="create-a-project-in-devops-projects-for-an-aspnet-app-and-an-azure-sql-database"></a>Creación de un proyecto en DevOps Projects para una aplicación ASP.NET y una base de datos SQL de Azure

DevOps Projects crea una canalización de CI/CD en Azure Pipelines. Puede crear una organización de Azure DevOps nueva o usar una existente. DevOps Projects también crea recursos de Azure, como una base de datos SQL de Azure, en la suscripción de Azure que prefiera.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).

1. En el panel izquierdo, seleccione **Crear un recurso**.

1. En el cuadro de búsqueda, escriba **DevOps Projects** y, después, seleccione **Crear**.

    ![El panel de DevOps Projects](_img/azure-devops-project-github/fullbrowser.png)

1. Seleccione **.NET** y después **Siguiente**.

1. En **Elegir un marco de trabajo de la aplicación**, seleccione **ASP.NET**.

1. Seleccione **Agregar una base de datos** y, después, seleccione **Siguiente**.  
    El marco de trabajo de la aplicación que eligió en un paso anterior, determina el tipo de destino de implementación del servicio de Azure que está disponible aquí. 
    
1. Seleccione **Next** (Siguiente).

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Configuración de Azure DevOps y una suscripción de Azure

1. Cree una organización de Azure DevOps nueva o seleccione una existente. 

1. Escriba el nombre del proyecto de Azure DevOps. 

1. Seleccione los servicios de la suscripción de Azure.  
    Opcionalmente, para ver los valores de configuración adicionales de Azure e identificar el nombre de usuario en la sección **Detalles de inicio de sesión del servidor de bases de datos**, puede seleccionar **Cambiar**. Almacene el nombre de usuario para los pasos posteriores de este tutorial. Si realiza este paso opcional, salga del área de configuración de Azure antes de seleccionar **Listo**.
 
1. Seleccione **Listo**.  
    Pocos minutos después, el proceso se completa y el panel de DevOps Projects se abre en Azure Portal. También puede ir al panel directamente desde **Todos los recursos** en Azure Portal. A la derecha, seleccione **Examinar** para ver la aplicación en ejecución.
    
## <a name="examine-the-ci-pipeline"></a>Examen de la canalización de CI

DevOps Projects configura automáticamente una canalización de CI/CD completa en Azure Repos. Puede explorar y personalizar la canalización. Para familiarizarse con la canalización de compilación de Azure DevOps, siga estos pasos:

1. En la parte superior del panel de DevOps Projects, seleccione **Compilar canalizaciones**.  
    Una pestaña del explorador muestra la canalización de compilación del nuevo proyecto.

1. Elija el campo **Estado** y seleccione los puntos suspensivos (...).  
    Un menú muestra varias opciones, como poner en cola una nueva compilación, poner en pausa una compilación y editar la canalización de compilación.

1. Seleccione **Editar**.

1. En este panel puede examinar las distintas tareas de la canalización de compilación.  
    La compilación ejecuta varias tareas, como capturar códigos fuente del repositorio Git, restaurar dependencias y publicar salidas usadas para implementaciones.

1. En la parte superior de la canalización de compilación, seleccione el nombre de esta.

1. Cambie el nombre de la canalización de compilación por otro más descriptivo, seleccione **Guardar y poner en cola** y, luego, **Guardar**.

1. En el nombre de la canalización de compilación, seleccione **Historial**.  
    Este panel muestra un registro de auditoría de los cambios recientes de la compilación. Azure Pipelines realiza un seguimiento de los cambios realizados en la canalización de compilación y permite comparar las versiones.

1. Seleccione **Desencadenadores**.  
    DevOps Projects crea automáticamente un desencadenador de integración continua y cada confirmación al repositorio inicia una compilación. Opcionalmente, puede elegir incluir o excluir ramas del proceso de integración continua.

1. Seleccione **Retención**.  
    En función del escenario, puede especificar directivas para conservar o quitar un determinado número de compilaciones.

## <a name="examine-the-cd-pipeline"></a>Examen de la canalización de CD

DevOps Projects crea y configura automáticamente los pasos necesarios para implementar desde la organización de Azure DevOps en la suscripción de Azure. Dichos pasos incluyen la configuración de una conexión de servicio de Azure para realizar la autenticación de Azure DevOps en la suscripción de Azure. La automatización también crea una canalización de implementación continua, lo que proporciona la implementación continua a la máquina virtual de Azure. Para más información acerca de la canalización de la implementación continua de Azure DevOps, siga estos pasos:

1. Seleccione **Compilación y versión** y, después, **Versiones**.  
    DevOps Projects crea una canalización de versión para administrar implementaciones en Azure.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a la canalización de versión, y, después, **Editar**.  
    La canalización de versión contiene una *canalización*, que define el proceso de lanzamiento.

1. En **Artefactos**, seleccione **Colocar**.  
    La canalización de compilación que ha examinado en los pasos anteriores genera la salida que se usa para el artefacto. 

1. A la derecha del icono **Colocar**, seleccione **Desencadenador de implementación continua**.  
    Esta canalización de versión tiene un desencadenador de implementación continua habilitado, que ejecuta una implementación cada vez que hay un nuevo artefacto de compilación disponible. Opcionalmente, puede deshabilitar el desencadenador, con lo que las implementaciones van a requerir una ejecución manual. 

    DevOps Projects configura una contraseña de SQL aleatoria y la utiliza para la canalización de versión.
    
1. En el lado izquierdo, seleccione **Variables**. 

   > [!NOTE]
   > El siguiente paso solo se puede realizar si se ha cambiado la contraseña de SQL Server. Hay una sola variable de contraseña.
  
1. Junto al cuadro **Valor**, seleccione el icono del candado, escriba la nueva contraseña y seleccione **Guardar**.

1. En el lado izquierdo, seleccione **Tareas** y, después, seleccione un entorno.  
    Las tareas son las actividades que ejecuta el proceso de implementación y se agrupan en fases. Esta canalización de versión tiene una fase individual que contiene las tareas *Azure App Service Deploy* (Implementación de Azure App Service) y *Azure SQL Database Deployment* (Implementación de Azure SQL Database).

1. Seleccione la tarea *Execute Azure SQL* (Ejecutar SQL de Azure) y examine las distintas propiedades que se usan para la implementación de SQL.  
    En **Paquete de implementación**, la tarea usa un archivo *SQL DACPAC*.

1. A la derecha, seleccione **Ver versiones** para mostrar un historial de las versiones.

1. Seleccione los puntos suspensivos (...) que se encuentran junto a una de las versiones y, después, **Abrir**.  
     Puede explorar varios menús, como un resumen de las versiones, elementos de trabajo asociados y las pruebas.

1. Seleccione **Confirmaciones**.  
     Esta vista muestra las confirmaciones de código que están asociadas a esta implementación. Compare las versiones para ver las diferencias de confirmación entre las implementaciones.

1. Seleccione **Registros**.  
     Los registros contienen información útil sobre el proceso de implementación. Se pueden ver tanto durante las implementaciones como después de ellas.

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure 

 > [!NOTE]
 > El siguiente procedimiento prueba la canalización de CI/CD. Para ello, realiza un cambio de texto simple. Para probar el proceso de implementación de SQL tiene la opción de realizar un cambio de esquema de SQL Server en la tabla.

A partir de ese momento ya puede empezar a colaborar con un equipo en una aplicación mediante el uso de un proceso de CI/CD que implemente automáticamente el trabajo más reciente en su sitio web. Cada cambio que se realiza en el repositorio de Git inicia una compilación en Azure DevOps y una canalización de CD ejecuta una implementación en Azure. Siga el procedimiento descrito en esta sección o utilice otra técnica para confirmar los cambios en el repositorio. Los cambios en el código inician el proceso de CI/CD e implementan automáticamente los cambios en Azure.

1. En el panel izquierdo, seleccione **Código**y, después, vaya al repositorio.

1. Vaya al directorio *SampleWebApplication\Views\Home*, seleccione los puntos suspensivos (...) junto al archivo *Index.cshtml* y, seleccione **Editar**. 

1. Realice un cambio en el archivo, como agregar texto dentro de una de las etiquetas div. 

1. En la parte superior derecha, seleccione **Confirmar** y, después, seleccione **Confirmar** de nuevo para insertar el cambio.  
    Tras unos instantes, se inicia una compilación se inicia en Azure DevOps y se ejecuta una versión para implementar los cambios. Supervise el estado de la compilación en el panel de DevOps Projects o en el explorador con la organización de Azure DevOps.

1. Una vez que complete la versión, actualice la aplicación para comprobar los cambios.

## <a name="connect-to-the-azure-sql-database"></a>Conexión a la base de datos SQL de Azure

Necesita los permisos adecuados para conectarse a la base de datos SQL de Azure.

1. En el panel de DevOps Projects, seleccione **SQL Database** para ir a la página de administración de la base de datos SQL.
   
1. Seleccione **Establecer el firewall del servidor** y, después, seleccione **Agregar IP de cliente**. 

1. Seleccione **Guardar**.  
    La dirección IP del cliente ahora tiene acceso al recurso de Azure de SQL Server.

1. Vuelva al panel **SQL Database**. 

1. En el lado derecho, seleccione el nombre del servidor para ir a la página de configuración de **SQL Server**.

1. Seleccione **Restablecer contraseña**, escriba una contraseña para el inicio de sesión de administrador de SQL Server y seleccione **Guardar**.  
    No olvide guardar esta contraseña para usarla más adelante en este tutorial.

    Ahora tiene la opción de usar las herramientas de cliente, como SQL Server Management Studio o Visual Studio para conectarse a SQL Server y a la base de datos SQL de Azure. Use la propiedad de **nombre del servidor** para conectarse.

    Si no cambió el nombre de usuario de la base de datos cuando configuró inicialmente el proyecto en DevOps Projects, el nombre de usuario es la parte local de la dirección de correo electrónico. Por ejemplo, si la dirección de correo electrónico es *johndoe\@microsoft.com*, el nombre de usuario es *johndoe*.

   > [!NOTE]
   > Si cambia la contraseña de inicio de sesión de SQL, debe cambiarla también en la variable de la canalización de versión, como se describe en la sección Examen de la canalización de CD.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si va a realizar pruebas, limpie los recursos para que no se acumulen costos de facturación. Cuando dejen de ser necesarios, puede eliminar la base de datos SQL de Azure y los recursos relacionados que ha creado en este tutorial. Para ello, utilice la funcionalidad de **eliminación** del panel de DevOps Projects.

> [!IMPORTANT]
> El siguiente procedimiento elimina permanentemente los recursos. La funcionalidad de *eliminación* destruye los datos que crea el proyecto en DevOps Projects tanto en Azure como en Azure DevOps y no se podrán recuperar. Utilice este procedimiento cuando haya leído detenidamente las indicaciones.

1. En Azure Portal, vaya al panel de DevOps Projects.
2. En la parte superior derecha, seleccione **Eliminar**. 
3. En el mensaje, seleccione **Sí** para *eliminar permanentemente* los recursos.

## <a name="next-steps"></a>Pasos siguientes

Si lo desea, puede modificar estas canalizaciones de compilación y de versión para satisfacer las necesidades de su equipo. También puede usar este patrón de CI/CD como plantilla para las demás canalizaciones. En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * A usar Azure DevOps Projects para implementar una aplicación de ASP.NET y código de Azure SQL Database
> * Configuración de Azure DevOps y una suscripción de Azure 
> * Examen de la canalización de CI
> * Examen de la canalización de CD
> * Confirmación de los cambios en Azure Repos e implementación automática de los mismos en Azure
> * A conectar con la base de datos SQL de Azure 
> * Limpieza de recursos

Para más información acerca de la canalización de CI/CD, consulte:

> [!div class="nextstepaction"]
> [Definición de la canalización de implementación continua (CD) en varias fases](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

## <a name="videos"></a>Vídeos

> [!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3308/player]
