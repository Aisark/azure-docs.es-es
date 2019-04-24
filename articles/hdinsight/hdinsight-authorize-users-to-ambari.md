---
title: Autorización de usuarios para vistas de Ambari en Azure HDInsight
description: Procedimiento para administrar los permisos de usuarios y grupos de Ambari en los clústeres de HDInsight con ESP habilitado.
author: maxluk
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/26/2017
ms.author: maxluk
ms.openlocfilehash: cf001d86356f4dd5fd3735803f0e329aa1e0940d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60254790"
---
# <a name="authorize-users-for-apache-ambari-views"></a>Autorización de usuarios para Apache Ambari Views

Los [clústeres de HDInsight habilitados con Enterprise Security Package (ESP)](./domain-joined/apache-domain-joined-introduction.md) proporcionan funcionalidades empresariales, incluida la autenticación basada en Azure Active Directory. Puede [sincronizar los usuarios nuevos](hdinsight-sync-aad-users-to-cluster.md) agregados a los grupos de Azure AD a los que se ha proporcionado acceso al clúster y permitir que esos usuarios específicos realicen determinadas acciones. El trabajo con usuarios, grupos y permisos en [Apache Ambari](https://ambari.apache.org/) es compatible tanto con clústeres de HDInsight ESP como con clústeres de HDInsight estándar.

Usuarios de Active Directory pueden iniciar sesión en los nodos del clúster con sus credenciales de dominio. También pueden usar sus credenciales de dominio para autenticar las interacciones del clúster con otros puntos de conexión aprobados como [Hue](https://gethue.com/), Ambari Views, ODBC, JDBC, PowerShell y las API REST.

> [!WARNING]  
> No cambie la contraseña del guardián Ambari (hdinsightwatchdog) en el clúster de HDInsight basado en Linux. El cambio de la contraseña impide usar acciones de script o realizar operaciones de escalado con el clúster.

Si no lo ha hecho aún, siga [estas instrucciones](./domain-joined/apache-domain-joined-configure.md) para aprovisionar un nuevo clúster ESP.

## <a name="access-the-ambari-management-page"></a>Acceso a la página de administración de Ambari

Para llegar a la **página de administración de Ambari** en la [interfaz de usuario web de Ambari](hdinsight-hadoop-manage-ambari.md), vaya a **`https://<YOUR CLUSTER NAME>.azurehdinsight.net`**. Escriba el nombre de usuario y la contraseña del administrador del clúster que definió cuando creó el clúster. A continuación, en el panel de Ambari, seleccione **Manage Ambari** (Administrar Ambari) en el menú **admin** (Administrador):

![Administración de Ambari](./media/hdinsight-authorize-users-to-ambari/manage-ambari.png)

## <a name="grant-permissions-to-apache-hive-views"></a>Concesión de permisos para vistas de Apache Hive

Ambari incluye instancias de vista para [Apache Hive](https://hive.apache.org/) y [Apache TEZ](https://tez.apache.org/), entre otros. Para conceder acceso a una o varias instancias de vistas de Hive, vaya a la **página de administración de Ambari**.

1. En la página de administración, seleccione el vínculo **Views** (Vistas) que está debajo del título del menú **Views** (Vistas) de la izquierda.

    ![Vínculo de vistas](./media/hdinsight-authorize-users-to-ambari/views-link.png)

2. En la página de vistas, expanda la fila **HIVE**. Hay una vista de Hive personalizada que se crea cuando se agrega el servicio Hive al clúster. También puede crear más instancias de la vista de Hive según sea necesario. Seleccione una vista de Hive:

    ![Vistas: Vista de Hive](./media/hdinsight-authorize-users-to-ambari/views-hive-view.png)

3. Desplácese hacia la parte inferior de la página de la vista. En la sección *Permissions* (Permisos), tiene dos opciones para conceder permisos a los usuarios del dominio para la vista:

**Grant permission to these users** (Conceder permiso a estos usuarios) ![Grant permission to these users](./media/hdinsight-authorize-users-to-ambari/add-user-to-view.png) (Conceder permiso a estos usuarios)

**Grant permission to these groups** (Conceder permiso a estos grupos) ![Grant permission to these groups](./media/hdinsight-authorize-users-to-ambari/add-group-to-view.png) (Conceder permiso a estos grupos)

1. Para agregar un usuario, seleccione el botón **Add User** (Agregar usuario).

   * Empiece a escribir el nombre de usuario y se mostrará una lista desplegable de nombres definidos anteriormente.

     ![Opción para completar automáticamente el usuario](./media/hdinsight-authorize-users-to-ambari/user-autocomplete.png)

   * Seleccione el nombre de usuario o termine de escribirlo. Para agregar este nombre de usuario como un usuario nuevo, seleccione el botón **New** (Nuevo).

   * Para guardar los cambios, seleccione la **casilla azul**.

     ![Usuario especificado](./media/hdinsight-authorize-users-to-ambari/user-entered.png)

1. Para agregar un grupo, seleccione el botón **Add Group** (Agregar grupo).

   * Comience a escribir el nombre del grupo. El proceso para seleccionar un nombre de grupo existente o para agregar un grupo nuevo es el mismo que el que se aplica para agregar usuarios.
   * Para guardar los cambios, seleccione la **casilla azul**.

     ![Grupo especificado](./media/hdinsight-authorize-users-to-ambari/group-entered.png)

Agregar los usuarios directamente a una vista es útil cuando desea asignar permisos a un usuario para que use esa vista, pero no desea que sea miembro de un grupo que tiene permisos adicionales. Para reducir la cantidad de trabajo administrativo, puede ser más fácil asignar permisos a grupos.

## <a name="grant-permissions-to-apache-tez-views"></a>Concesión de permisos para vistas de Apache TEZ

Las instancias de vista de [Apache TEZ](https://tez.apache.org/) permiten a los usuarios supervisar y depurar todos los trabajos de Tez, enviados por consultas de [Apache Hive](https://hive.apache.org/) y scripts de [Apache Pig](https://pig.apache.org/). Hay una instancia de vista de Tez predeterminada que se crea cuando se aprovisiona el clúster.

Para asignar usuarios y grupos a una instancia de vista de Tez, expanda la fila **TEZ** en la página de vistas, como se describió anteriormente.

![Vistas: vista de Tez](./media/hdinsight-authorize-users-to-ambari/views-tez-view.png)

Para agregar usuarios o grupos, repita los pasos 3 a 5 de la sección anterior.

## <a name="assign-users-to-roles"></a>Asignación de usuarios a roles

Hay cinco roles de seguridad para usuarios y grupos, que se enumeran como permisos de acceso en orden decreciente:

* Administrador de clústeres
* Operador de clústeres
* Administrador de servicios
* Operador de servicio
* Usuario del clúster

Para administrar roles, vaya a la **página de administración de Ambari** y después seleccione el vínculo **Roles** en el grupo de menús *Clusters* (Clústeres) de la izquierda.

![Vínculo del menú de roles](./media/hdinsight-authorize-users-to-ambari/roles-link.png)

Para ver la lista de permisos concedidos a cada rol, haga clic en el signo de interrogación azul junto al encabezado de tabla **Roles** de la página Roles.

![Vínculo del menú de roles](./media/hdinsight-authorize-users-to-ambari/roles-permissions.png)

En esta página, hay dos vistas diferentes que puede usar para administrar roles para los usuarios y los grupos: Block (bloque) y List (lista).

### <a name="block-view"></a>Vista de bloque

La vista Block (Bloque) muestra cada rol en su propia fila y ofrece las opciones **Assign roles to these users** (Asignar roles a estos usuarios) y **Assign roles to these groups** (Asignar roles a estos grupos) descritas anteriormente.

![Vista de bloque de roles](./media/hdinsight-authorize-users-to-ambari/roles-block-view.png)

### <a name="list-view"></a>Vista de lista

La vista List (Lista) proporciona funcionalidades de edición rápidas en dos categorías: Users (Usuarios) y Groups (Grupos).

* La categoría Users (Usuarios) de la vista List (Lista) muestra una lista de todos los usuarios, que le permite seleccionar un rol para cada usuario en la lista desplegable.

    ![Vista de lista de roles: usuarios](./media/hdinsight-authorize-users-to-ambari/roles-list-view-users.png)

*  La categoría Groups (Grupos) de la vista List (Lista) muestra todos los grupos y los roles asignados a cada grupo. En el ejemplo, la lista de grupos se sincroniza desde los grupos de Azure AD especificados en la propiedad **Access user group** (Grupo de usuarios con acceso) de la configuración del dominio del clúster. Consulte [Creación de un clúster de HDInsight con ESP habilitado](./domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-a-hdinsight-cluster-with-esp).

    ![Vista de lista de roles: grupos](./media/hdinsight-authorize-users-to-ambari/roles-list-view-groups.png)

    En la imagen anterior, se asigna al grupo "hiveusers" el rol *Cluster User* (Usuario del clúster). Se trata de un rol de solo lectura que permite a los usuarios de ese grupo ver, pero no cambiar, las configuraciones del servicio y las métricas del clúster.

## <a name="log-in-to-ambari-as-a-view-only-user"></a>Inicio de sesión en Ambari como un usuario de solo lectura

Se han asignado a los usuarios del dominio de Azure AD "hiveuser1" permisos para las vistas de Hive y Tez. Cuando se inicia la interfaz de usuario web de Ambari y se escriben las credenciales del dominio de este usuario (nombre de usuario de Azure AD en formato de correo electrónico y contraseña), se redirige al usuario a la página de vistas de Ambari. Desde aquí, el usuario puede seleccionar cualquier vista accesible. El usuario no puede visitar cualquier otra parte del sitio, incluidas las páginas de panel, servicios, hosts, alertas o administrador.

![Usuario con permisos solo para las vistas](./media/hdinsight-authorize-users-to-ambari/user-views-only.png)

## <a name="log-in-to-ambari-as-a-cluster-user"></a>Inicio de sesión en Ambari como un usuario del clúster

Se ha asignado al usuario del dominio de Azure AD "hiveuser2" el rol *Cluster User* (Usuario del clúster). Este rol puede acceder al panel y a todos los elementos de menú. Un usuario del clúster tiene menos opciones permitidas que un administrador. Por ejemplo, hiveuser2 puede ver las configuraciones de cada uno de los servicios, pero no puede modificarlas.

![Usuario con rol de usuario del clúster](./media/hdinsight-authorize-users-to-ambari/user-cluster-user-role.png)

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de directivas de Apache Hive en HDInsight con ESP](./domain-joined/apache-domain-joined-run-hive.md)
* [Administración de clústeres de HDInsight ESP](./domain-joined/apache-domain-joined-manage.md)
* [Uso de la vista de Apache Hive con Apache Hadoop en HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md)
* [Sincronización de usuarios de Azure AD con el clúster](hdinsight-sync-aad-users-to-cluster.md)
