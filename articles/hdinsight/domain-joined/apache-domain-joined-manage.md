---
title: 'Administración de clústeres de HDInsight con Enterprise Security Enterprise: Azure'
description: Aprenda a administrar clústeres de HDInsight con Enterprise Security Package.
ms.service: hdinsight
author: omidm1
ms.author: omidm
ms.reviewer: mamccrea
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 08/24/2018
ms.openlocfilehash: 951bd74c67c77c944a17e41646c4fe49ef46b33f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128320"
---
# <a name="manage-hdinsight-clusters-with-enterprise-security-package"></a>Administración de clústeres de HDInsight con Enterprise Security Package
Aprenda sobre los usuarios y roles de Enterprise Security Package (ESP) de HDInsight y cómo administrar clústeres de ESP.

## <a name="use-vscode-to-link-to-domain-joined-cluster"></a>Uso de VSCode para vincular a un clúster unido a un dominio

Puede vincular un clúster normal mediante un nombre de usuario administrado de Apache Ambari; también puede vincular un clúster de Apache Hadoop de seguridad mediante un nombre de usuario de dominio (como user1@contoso.com).
1. Para abrir la paleta de comandos, presione **CTRL+MAYÚS+P** y, después, escriba **HDInsight: Link a cluster** (HDInsight: vincular un clúster).

   ![comando de clúster de vinculación](./media/apache-domain-joined-manage/link-cluster-command.png)

2. Escriba la dirección URL del clúster de HDInsight, escriba el nombre de usuario y la contraseña y seleccione el tipo de clúster; si la comprobación se realiza correctamente, se muestra información de operación completada con éxito.
   
   ![cuadro de diálogo de vinculación de clúster](./media/apache-domain-joined-manage/link-cluster-process.png)

   > [!NOTE]  
   > Si el clúster se registró en la suscripción de Azure y se vinculó, se usan el nombre de usuario y la contraseña vinculados. 
   
3. Puede ver un clúster vinculado mediante el comando **List cluster**. Ahora puede enviar un script a este clúster vinculado.

   ![clúster vinculado](./media/apache-domain-joined-manage/linked-cluster.png)

4. También puede desvincular un clúster si escribe **HDInsight: Unlink a cluster** (Desvincular un clúster) desde la paleta de comandos.

## <a name="use-intellij-to-link-to-domain-joined-cluster"></a>Uso de IntelliJ para vincular a un clúster unido a un dominio

Puede vincular un clúster normal mediante un nombre de usuario administrado de Ambari; también puede vincular un clúster de Hadoop de seguridad mediante un nombre de usuario de dominio (como user1@contoso.com). 
1. Haga clic en **Link a cluster** (Vincular un clúster) desde **Azure Explorer**.

   ![menú contextual de vinculación de un clúster](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Escriba el **Nombre de clúster**, el **Nombre de usuario** y la **Contraseña**. Si obtiene un error de autenticación, debe comprobar el nombre de usuario y la contraseña. Si lo desea, agregue la cuenta de almacenamiento, la clave de almacenamiento, y seleccione un contenedor del contenedor de almacenamiento. La información de almacenamiento es para el explorador de almacenamiento en el árbol de la izquierda
   
   ![cuadro de diálogo de vinculación de clúster](./media/apache-domain-joined-manage/link-a-cluster-dialog.png)

   > [!NOTE]  
   > Si el clúster se registró en la suscripción de Azure y se vinculó, se usan la clave de almacenamiento, el nombre de usuario y la contraseña vinculados.
   > ![explorador de almacenamiento en IntelliJ](./media/apache-domain-joined-manage/storage-explorer-in-IntelliJ.png)

   
3. Puede ver un clúster vinculado en el nodo de **HDInsight** si la información de entrada es correcta. Ahora puede enviar una aplicación a este clúster vinculado.

   ![clúster vinculado](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. También puede desvincular un clúster de **Azure Explorer**.
   
   ![clúster no vinculado](./media/apache-domain-joined-manage/unlink.png)

## <a name="use-eclipse-to-link-to-domain-joined-cluster"></a>Uso de Eclipse para vincular a un clúster unido a un dominio

Puede vincular un clúster normal mediante un nombre de usuario administrado de Ambari; también puede vincular un clúster de Hadoop de seguridad mediante un nombre de usuario de dominio (como user1@contoso.com).
1. Haga clic en **Link a cluster** (Vincular un clúster) desde **Azure Explorer**.

   ![menú contextual de vinculación de un clúster](./media/apache-domain-joined-manage/link-a-cluster-context-menu.png)

2. Escriba el **Nombre de clúster**, el **Nombre de usuario** y la **Contraseña** y haga clic en el botón Aceptar para vincular el clúster. De manera opcional, escriba la cuenta de almacenamiento, la clave de almacenamiento y, a continuación, seleccione el contenedor de almacenamiento para que el explorador de almacenamiento funcione en la vista de árbol de la izquierda.
   
   ![cuadro de diálogo de vinculación de clúster](./media/apache-domain-joined-manage/link-cluster-dialog.png)
   
   > [!NOTE]  
   > Si el clúster se registró en la suscripción de Azure y se vinculó, se usan la clave de almacenamiento, el nombre de usuario y la contraseña vinculados.
   > ![explorador de almacenamiento en Eclipse](./media/apache-domain-joined-manage/storage-explorer-in-Eclipse.png)

3. Si la información de entrada es correcta, puede ver un clúster vinculado en el nodo de **HDInsight** tras hacer clic en el botón OK (Aceptar). Ahora puede enviar una aplicación a este clúster vinculado.

   ![clúster vinculado](./media/apache-domain-joined-manage/linked-cluster-intellij.png)

4. También puede desvincular un clúster de **Azure Explorer**.
   
   ![clúster no vinculado](./media/apache-domain-joined-manage/unlink.png)

## <a name="access-the-clusters-with-enterprise-security-package"></a>Acceda a clústeres con Enterprise Security Package.

Enterprise Security Package (conocido anteriormente como HDInsight Premium) proporciona acceso multiusuario al clúster, donde Active Directory realiza la autenticación con Apache Ranger y ACL de Storage (ADLS ACL). La autorización proporciona límites de seguridad entre varios usuarios y permite que solo los usuarios con privilegios tengan acceso a los datos en función de las directivas de autorización.

El aislamiento de usuarios y la seguridad son importantes para un clúster de HDInsight con Enterprise Security Package. Para cumplir estos requisitos, se bloquea el acceso de SSH al clúster con Enterprise Security Package. En la tabla siguiente se muestran los métodos de acceso recomendados para cada tipo de clúster:

|Carga de trabajo|Escenario|Modo de acceso|
|--------|--------|-------------|
|Apache Hadoop|Hive: consultas/trabajos interactivos  |<ul><li>[Beeline](#beeline)</li><li>[Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC: Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark de Apache|Consultas/trabajos interactivos, PySpark interactivo|<ul><li>[Beeline](#beeline)</li><li>[Zeppelin con Livy](../spark/apache-spark-zeppelin-notebook.md)</li><li>[Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC: Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Spark de Apache|Escenarios de Batch: envío de Spark, PySpark|<ul><li>[Livy](../spark/apache-spark-livy-rest-interface.md)</li></ul>|
|Interactive Query (LLAP)|Interactive|<ul><li>[Beeline](#beeline)</li><li>[Vista de Hive](../hadoop/apache-hadoop-use-hive-ambari-view.md)</li><li>[ODBC/JDBC: Power BI](../hadoop/apache-hadoop-connect-hive-power-bi.md)</li><li>[Visual Studio Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)</li></ul>|
|Cualquiera|Instalar aplicaciones personalizadas|<ul><li>[Acciones de script](../hdinsight-hadoop-customize-cluster-linux.md)</li></ul>|

   > [!NOTE]  
   > Jupyter no está instalado en Enterprise Security Package o no es compatible con él.

El uso de API estándar ayuda desde el punto de vista de la seguridad. Además, obtendrá las siguientes ventajas:

1.  **Administración**: puede administrar el código y automatizar trabajos con las API estándar (Livio, HS2, etc.)
2.  **Auditoría**: con SSH, es imposible auditar qué usuarios accedieron a través de dicho protocolo al clúster. Este no será el caso si los trabajos se construyen a través de puntos de conexión estándar, ya que se ejecutan en el contexto del usuario. 



### <a name="beeline"></a>Uso de BeeLine 
Instale Beeline en la máquina, conéctese a través de la red pública de Internet y use los siguientes parámetros: 

```
- Connection string: -u 'jdbc:hive2://<clustername>.azurehdinsight.net:443/;ssl=true;transportMode=http;httpPath=/hive2'
- Cluster login name: -n admin
- Cluster login password -p 'password'
```

Si tiene Beeline instalado localmente y se conecta a través de una red virtual de Azure, use los siguientes parámetros: 

```
- Connection string: -u 'jdbc:hive2://<headnode-FQDN>:10001/;transportMode=http'
```

Para buscar el nombre de dominio completo de un nodo principal, use la información del documento Administración de clústeres de HDInsight con la API de REST de Ambari.














## <a name="users-of-hdinsight-clusters-with-esp"></a>Usuarios de clústeres de HDInsight con Enterprise Security Package
Un clúster de HDInsight sin Enterprise Security Package tiene dos cuentas de usuario que se crean durante la creación del clúster:

* **Administrador de Ambari**: esta cuenta también se conoce como *usuario de Hadoop* o *usuario HTTP*. Esta cuenta puede usarse para iniciar sesión en Ambari en https://&lt;nombreDeClúster >. azurehdinsight.net. También puede usarse para ejecutar consultas en vistas de Ambari, ejecutar trabajos mediante herramientas externas (por ejemplo, PowerShell, Templeton, Visual Studio) y autenticarse con el controlador ODBC de Hive y herramientas de BI (por ejemplo, Excel, Powerbi o Tableau).

Un clúster de HDInsight con Enterprise Security Package tiene tres nuevos usuarios además del administrador de Ambari.

* **Administrador de Ranger**:  esta es la cuenta de administrador local de Apache Ranger. No es un usuario de dominio de Active Directory. Esta cuenta se puede usar para configurar directivas y crear otros administradores de usuarios o administradores delegados (para que esos usuarios puedan administrar directivas). De forma predeterminada, el nombre de usuario es *admin* y la contraseña es la misma que la contraseña de administrador de Ambari. En la página de configuración de Ranger se puede actualizar la contraseña.
* **Usuario de dominio administrador de clúster**: esta cuenta es un usuario de dominio de Active Directory designado como administrador de clúster de Hadoop, incluido Ambari y Ranger. Se deben proporcionar las credenciales del usuario durante la creación del clúster. Este usuario tiene los privilegios siguientes:

  * Unir máquinas al dominio y colocarlas en la unidad organizativa que especifique durante la creación del clúster.
  * Crear entidades de servicio dentro de la unidad organizativa que especifique durante la creación del clúster.
  * Crear entradas de DNS inversas.

    Tenga en cuenta que los demás usuarios de AD también tienen estos privilegios.

    Hay algunos puntos de conexión en el clúster (por ejemplo, Templeton) que no están administrados por Ranger, de ahí que no sean seguros. Estos puntos de conexión están bloqueados para todos los usuarios excepto para el usuario de dominio administrador del clúster.
* **Normal**: durante la creación del clúster, puede proporcionar varios grupos de Active Directory. Los usuarios de estos grupos se sincronizan con Ranger y Ambari. Estos usuarios son usuarios de dominio y solo tienen acceso a los puntos de conexión administrados por Ranger (por ejemplo, Hiveserver2). Todas las directivas y la auditoría de RBAC serán aplicable a estos usuarios.

## <a name="roles-of-hdinsight-clusters-with-esp"></a>Roles de clústeres de HDInsight con Enterprise Security Package
Enterprise Security Package de HDInsight tiene los siguientes roles:

* Administrador de clústeres
* Operador de clústeres
* Administrador de servicios
* Operador de servicio
* Usuario del clúster

**Para ver los permisos de estos roles:**

1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en el signo de interrogación azul para ver los permisos:

    ![Permisos de roles de HDInsight con Enterprise Security Package](./media/apache-domain-joined-manage/hdinsight-domain-joined-roles-permissions.png)

## <a name="open-the-ambari-management-ui"></a>Abrir la interfaz de usuario de administración de Ambari

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Abra el clúster de HDInsight.
3. Haga clic en **Panel** en el menú superior para abrir Ambari.
4. Inicie sesión en Ambari con el nombre de usuario de dominio de administrador de clúster y la contraseña.
5. Haga clic en el menú desplegable **Administrador** de la esquina superior derecha y luego haga clic en **Administrar Ambari**.

    ![Administración de Ambari para HDInsight con Enterprise Security Package](./media/apache-domain-joined-manage/hdinsight-domain-joined-manage-ambari.png)

    La interfaz de usuario tiene el siguiente aspecto:

    ![Interfaz de usuario de administración de Ambari para HDInsight con Enterprise Security Package](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui.png)

## <a name="list-the-domain-users-synchronized-from-your-active-directory"></a>Enumeración de los usuarios de dominio sincronizados desde Active Directory
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Usuarios**. Verá todos los usuarios sincronizados desde Active Directory con el clúster de HDInsight.

    ![Interfaz de usuario de administración de Ambari para HDInsight con Enterprise Security Package: enumeración de usuarios](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-users.png)

## <a name="list-the-domain-groups-synchronized-from-your-active-directory"></a>Enumeración de los grupos de dominios sincronizados desde Active Directory
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Grupos**. Verá todos los grupos sincronizados desde Active Directory con el clúster de HDInsight.

    ![Interfaz de usuario de administración de Ambari para HDInsight con Enterprise Security Package: enumeración de grupos](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-groups.png)

## <a name="configure-hive-views-permissions"></a>Configuración de permisos de vistas de Hive
1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Vistas**.
3. Haga clic en **HIVE** para mostrar los detalles.

    ![Interfaz de usuario de administración de Ambari para HDInsight con Enterprise Security Package: vistas de Hive](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views.png)
4. Haga clic en el vínculo **Vista de Hive** para configurar vistas de Hive.
5. Desplácese hacia abajo hasta la sección **Permisos**.

    ![Interfaz de usuario de administración de Ambari para HDInsight con Enterprise Security Package: vistas de Hive, configuración de permisos](./media/apache-domain-joined-manage/hdinsight-domain-joined-ambari-management-ui-hive-views-permissions.png)
6. Haga clic en **Agregar usuario** o **Agregar grupo**, y especifique los usuarios o grupos que pueden usar vistas de Hive.

## <a name="configure-users-for-the-roles"></a>Configuración de usuarios para los roles
 Para ver una lista de roles y sus permisos, consulte Roles de clústeres de HDInsight con Enterprise Security Package.

1. Abra la interfaz de usuario de administración de Ambari.  Consulte [Abrir la interfaz de usuario de administración de Ambari](#open-the-ambari-management-ui).
2. En el menú de la izquierda, haga clic en **Roles**.
3. Haga clic en **Agregar usuario** o **Agregar grupo** para asignar usuarios y grupos a roles diferentes.

## <a name="next-steps"></a>Pasos siguientes
* Para configurar un clúster de HDInsight con Enterprise Security Package, consulte [Configuración de clústeres de HDInsight con Enterprise Security Package](apache-domain-joined-configure.md).
* Para configurar directivas de Hive y ejecutar consultas de Hive, vea [Configuración de directivas de Apache Hive en HDInsight con Enterprise Security Package](apache-domain-joined-run-hive.md).
