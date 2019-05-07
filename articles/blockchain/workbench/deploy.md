---
title: Implementación de Azure Blockchain Workbench
description: Implementación de Azure Blockchain Workbench
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/06/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4fffc54428b152a060594a5c107d3ac08457aaaa
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/06/2019
ms.locfileid: "65154659"
---
# <a name="deploy-azure-blockchain-workbench"></a>Implementación de Azure Blockchain Workbench

Azure Blockchain Workbench se implementa mediante una plantilla de solución de Azure Marketplace. La plantilla simplifica la implementación de los componentes necesarios para crear aplicaciones de cadena de bloques. Una vez implementado, Blockchain Workbench proporciona acceso a las aplicaciones cliente para crear y administrar usuarios y aplicaciones de cadena de bloques.

Para más información acerca de los componentes de Blockchain Workbench, consulte [Arquitectura de Azure Blockchain Workbench](architecture.md).

## <a name="prepare-for-deployment"></a>Preparación de la implementación

Blockchain Workbench le permite implementar un libro de contabilidad de cadena de bloques junto con un conjunto de los servicios de Azure correspondientes que se usan más frecuentemente para compilar una aplicación basada en cadena de bloques. La implementación de Blockchain Workbench hace que los siguientes servicios de Azure se aprovisionen en un grupo de recursos de la suscripción de Azure.

* Plan de App Service (estándar)
* Application Insights
* Event Grid
* Azure Key Vault
* Azure Service Bus
* Base de datos SQL (para estándar S0) + servidor lógico de SQL
* Cuenta de Azure Storage (LRS estándar)
* Escalado de máquinas virtuales que se establezca con capacidad 1
* Grupo de recursos de red virtual (con red Virtual de Load Balancer, Network Security Group, dirección IP pública)
* Opcional: Azure Blockchain Service (valor predeterminado de B0 para básico)

La siguiente es una implementación de ejemplo creada en el grupo de recursos **myblockchain**.

![Implementación de ejemplo](media/deploy/example-deployment.png)

El costo de Blockchain Workbench se agrega al costo de los servicios de Azure subyacentes. La información de precios de los servicios de Azure se pueden calcular mediante la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Requisitos previos

Azure Blockchain Workbench requiere de registros de aplicación y configuraciones de Azure AD. Puede elegir entre [configurar manualmente](#azure-ad-configuration) Azure AD antes de la implementación o ejecutar un script posterior a la implementación. Si está implementando nuevamente Blockchain Workbench, consulte la [configuración de Azure AD](#azure-ad-configuration) para verificar la configuración de Azure AD.

> [!IMPORTANT]
> El área de trabajo no tiene que implementarse en el mismo inquilino que el que se usa para registrar una aplicación de Azure AD. Se debe implementar en un inquilino donde tenga permisos suficientes para implementar recursos. Para más información sobre los inquilinos de Azure AD, consulte [Obtención de un inquilino de Azure Active Directory](../../active-directory/develop/quickstart-create-new-tenant.md) e [Integración de aplicaciones con Azure Active Directory](../../active-directory/develop/quickstart-v1-integrate-apps-with-azure-ad.md).

## <a name="deploy-blockchain-workbench"></a>Implementación de Blockchain Workbench

Una vez que se han completado los pasos descritos en los requisitos previos, estará listo para implementar Blockchain Workbench. En las secciones siguientes se describe cómo implementar la plataforma.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD en el que desee implementar Azure Blockchain Workbench.
3. En el panel izquierdo, seleccione **Crear un recurso**. Busque `Azure Blockchain Workbench` en la barra de búsqueda de **Buscar en el Marketplace**. 

    ![Barra de búsqueda en Marketplace](media/deploy/marketplace-search-bar.png)

4. Seleccione **Azure Blockchain Workbench**.

    ![Resultados de la búsqueda en Marketplace](media/deploy/marketplace-search-results.png)

5. Seleccione **Crear**.
6. Complete la configuración básica.

    ![Creación de Azure Blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Prefijo de recurso. | Identificador único corto para la implementación. Este valor se utiliza como base para asignar nombres a los recursos. |
    | Nombre de usuario de máquina virtual | El nombre de usuario se utiliza como administrador de todas las máquinas virtuales (VM). |
    | Tipo de autenticación | Seleccione si desea utilizar una contraseña o clave para conectarse a las máquinas virtuales. |
    | Contraseña | La contraseña se usa para conectarse a las máquinas virtuales. |
    | SSH | Use una clave pública RSA en formato de una sola línea con **ssh-rsa** o utilice el formato PEM de varias líneas. Puede generar claves SSH mediante `ssh-keygen` en Linux y OS X o PuTTYGen en Windows. Para más información sobre las claves SSH, consulte [Uso de claves SSH con Windows en Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Contraseña de la base de datos y la cadena de bloques | Especifique la contraseña que se utilizará para acceder a la base de datos creada como parte de la implementación. La contraseña debe cumplir tres de los cuatro requisitos siguientes: longitud debe estar entre 12 y 72 caracteres, caracteres de 1 letra minúscula, 1 letra mayúscula, 1 número y 1 carácter especial que es el número no sign(#), porcentaje (%), coma (,), star(*), comilla inversa (\`), haga doble quote("), comillas simples, dash(-) y semicolumn(;) |
    | Región de la implementación | Especifique dónde se van a implementar los recursos de Blockchain Workbench. Para una mejor disponibilidad, el valor debe ser el mismo que el de **Ubicación**. |
    | Subscription | Especifique la suscripción de Azure que desea usar para la implementación. |
    | Grupos de recursos | Cree un nuevo grupo de recursos seleccionando **Crear nuevo** y especifique un nombre de grupo de recursos único. |
    | Ubicación | Especifique la región en la que desea implementar la plataforma. |

7. Seleccione **Aceptar** para finalizar la sección de configuración básica.

8. En **Configuración avanzada**, elija si desea crear una nueva red de cadena de bloques o usar una red de cadena de bloques de prueba de autoridad existente.

    Para **Crear nueva**:

    El *crear nuevos* opción implementa un libro de contabilidad de quórum de servicio de Azure Blockchain con la sku básica de forma predeterminada.

    ![Configuración avanzada para la nueva red de la cadena de bloques](media/deploy/advanced-blockchain-settings-new.png)

    | Configuración | DESCRIPCIÓN  |
    |---------|--------------|
    | Tarifa de Azure Blockchain Service | Elija **básica** o **estándar** nivel de servicio de la cadena de bloques de Azure que se usa para Blockchain Workbench |
    | Configuración de Azure Active Directory | Seleccione **Agregar más adelante**.</br>Nota: Si decide [configurar previamente Azure AD](#azure-ad-configuration) o volver a implementar, elija *Agregar ahora*. |
    | Selección de máquina virtual | Seleccione el rendimiento de almacenamiento que prefiera y tamaño de máquina virtual para la red de la cadena de bloques. Elija un tamaño más pequeño de máquina virtual, como *Estándar DS1 v2*, si tiene una suscripción con límites de servicio bajos, como el nivel Gratis de Azure. |

    Para **Usar existente**:

    La opción *usar existente* opción le permite especificar una red de cadena de bloques de prueba de autoridad (PoA) de Ethereum. Los puntos de conexión tienen los siguientes requisitos.

   * El punto de conexión debe ser una red de cadena de bloques de prueba de autoridad (PoA) de Ethereum.
   * El punto de conexión debe ser públicamente accesible a través de la red.
   * La red de cadena de bloques PoA debe configurarse para que el precio del gas se establezca en cero.

     > [!NOTE]
     > Las cuentas de Blockchain Workbench no tienen fondos. Si se requieren fondos, habrá un error de transacción.

     ![Configuración avanzada para la red existente de la cadena de bloques](media/deploy/advanced-blockchain-settings-existing.png)

     | Configuración | DESCRIPCIÓN  |
     |---------|--------------|
     | Punto de conexión RPC de Ethereum | Proporcione el punto de conexión RPC de una red de cadena de bloques PoA existente. El punto de conexión comienza con https:// o http:// y termina con un número de puerto. Por ejemplo: `http<s>://<network-url>:<port>` |
     | Configuración de Azure Active Directory | Seleccione **Agregar más adelante**.</br>Nota: Si decide [configurar previamente Azure AD](#azure-ad-configuration) o volver a implementar, elija *Agregar ahora*. |
     | Selección de máquina virtual | Seleccione el rendimiento de almacenamiento que prefiera y tamaño de máquina virtual para la red de la cadena de bloques. Elija un tamaño más pequeño de máquina virtual, como *Estándar DS1 v2*, si tiene una suscripción con límites de servicio bajos, como el nivel Gratis de Azure. |

9. Seleccione **Aceptar** para finalizar la configuración avanzada.

10. Revise el resumen para comprobar que los parámetros son precisos.

    ![Resumen](media/deploy/blockchain-workbench-summary.png)

11. Seleccione **Crear** para aceptar los términos e implementar Azure Blockchain Workbench.

La implementación puede tardar hasta 90 minutos. Puede usar Azure Portal para supervisar el progreso. En el grupo de recursos recién creado, seleccione **Implementaciones > Información general** para ver el estado de los artefactos implementados.

> [!IMPORTANT]
> Después de la implementación debe completar la configuración de Active Directory. Si eligió **Agregar más adelante**, deberá ejecutar el [script de configuración de Azure AD](#azure-ad-configuration-script).  Si eligió **Agregar ahora**, deberá [configurar la dirección URL de respuesta](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Dirección URL web de Blockchain Workbench

Cuando se haya completado la implementación de Blockchain Workbench, un nuevo grupo de recursos contendrá los recursos de este. Se puede acceder a los servicios de Blockchain Workbench a través de una dirección URL web. Los pasos siguientes muestran cómo recuperar la dirección URL web de la plataforma implementada.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. En el panel de navegación de la izquierda, seleccione **Grupos de recursos**.
3. Elija el nombre del grupo de recursos que especificó al implementar Blockchain Workbench.
4. Seleccione el encabezado de columna **TIPO** para ordenar la lista alfabéticamente por tipo.
5. Hay dos recursos del tipo **App Service**. Seleccione el recurso del tipo **App Service** *sin* el sufijo "-api".

    ![Lista de App Service](media/deploy/resource-group-list.png)

6. En la sección **Información esencial** de App Service, copie el valor **URL** que representa la dirección URL web a la instancia de Blockchain Workbench implementada.

    ![Información esencial de App Service](media/deploy/app-service.png)

Para asociar un nombre de dominio personalizado a Blockchain Workbench, consulte [Configuración de un nombre de dominio personalizado para una aplicación web en Azure App Service mediante Traffic Manager](../../app-service/web-sites-traffic-manager-custom-domain-name.md).

## <a name="azure-ad-configuration-script"></a>Script de configuración de Azure AD

Azure AD debe configurarse para completar la implementación de Blockchain Workbench. Usará un script de PowerShell para realizar la configuración.

1. En un explorador, vaya a la [URL web de Blockchain Workbench](#blockchain-workbench-web-url).
2. Podrá ver instrucciones para configurar Azure AD con Cloud Shell. Copie el comando e inicie Cloud Shell.

    ![Inicio del script de AAD](media/deploy/launch-aad-script.png)

3. Elija al inquilino de Azure AD en el que se ha implementado Blockchain Workbench.
4. En Cloud Shell, pegue y ejecute el comando.
5. Cuando se le solicite, escriba al inquilino de Azure AD que desea usar para Blockchain Workbench. Este será el inquilino que contiene los usuarios de Blockchain Workbench.

    > [!IMPORTANT]
    > El usuario autenticado requiere permisos para crear registros de aplicación de Azure AD y conceder permisos de aplicación delegados en el inquilino. Tendrá que solicitar al administrador del inquilino que ejecute el script de configuración de Azure AD o cree un nuevo inquilino.

    ![Ingreso al inquilino de Azure AD](media/deploy/choose-tenant.png)

6. Se le pedirá que se autentique en el inquilino de Azure AD mediante un explorador. Abra la dirección URL web en un explorador, escriba el código y autentíquese.

    ![Autenticación con código](media/deploy/authenticate.png)

7. El script genera varios mensajes de estado. Obtendrá un mensaje de estado **CORRECTO** si el inquilino se ha aprovisionado correctamente.
8. Navegue a la URL de Blockchain Workbench. Deberá dar su consentimiento para conceder permisos de lectura en el directorio. Esto permite que la aplicación web de Blockchain Workbench tenga acceso a los usuarios en el inquilino. Si es el administrador de inquilinos, puede dar su consentimiento por toda la organización. Esta opción acepta el consentimiento para todos los usuarios en el inquilino. En caso contrario, a cada usuario se le pedirá su consentimiento la primera vez que use la aplicación web de Blockchain Workbench.
9. Seleccione **Aceptar** para dar su consentimiento.

     ![Consentimiento para leer los perfiles de usuarios](media/deploy/graph-permission-consent.png)

10. Después de dar su consentimiento, puede usar la aplicación web de Blockchain Workbench.

## <a name="azure-ad-configuration"></a>Configuración de Azure AD

Si decide configurar o comprobar manualmente la configuración de Azure AD antes de la implementación, complete todos los pasos de esta sección. Si prefiere establecer automáticamente la configuración de Azure AD, utilice el [script de configuración de Azure AD](#azure-ad-configuration-script) después de implementar Blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Registro de aplicación de API de Blockchain Workbench

La implementación de Blockchain Workbench requiere el registro de una aplicación de Azure AD. Necesita un inquilino de Azure Active Directory (Azure AD) para registrar la aplicación. Puede usar un inquilino existente o crear uno nuevo. Si va a usar un inquilino de Azure AD ya existente, necesitará suficientes permisos para registrar aplicaciones, otorgar permisos de Graph API y permitir el acceso a los invitados dentro de un inquilino de Azure AD. Si no tiene permisos suficientes en un inquilino de Azure AD existente, cree un inquilino.


1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione su cuenta en la esquina superior derecha y cambie al inquilino de Azure AD que desee. El inquilino debe ser el inquilino del administrador de la suscripción en la que está implementado Workbench y tener permisos suficientes para registrar aplicaciones.
3. En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **Registros de aplicaciones** > **Nuevo registro de aplicaciones**.

    ![Registro de aplicación](media/deploy/app-registration.png)

4. Proporcione un **nombre** y una **dirección URL de inicio de sesión** para la aplicación. Puede usar valores de marcador de posición ya que los valores se cambian durante la implementación. 

    ![Crear el registro de aplicaciones](media/deploy/app-registration-create.png)

    |Configuración  | Valor  |
    |---------|---------|
    |NOMBRE | `Blockchain API` |
    |Tipo de aplicación |Aplicación web/API|
    |URL de inicio de sesión | `https://blockchainapi` |

5. Seleccione **Crear** para registrar la aplicación de Azure AD.

### <a name="modify-manifest"></a>Modificación del manifiesto

A continuación, debe modificar el manifiesto para que use los roles de aplicación en Azure AD con el fin de especificar los administradores de Blockchain Workbench.  Para más información acerca de los manifiestos de aplicación, consulte [Manifiesto de aplicación de Azure Active Directory](../../active-directory/develop/reference-app-manifest.md).

1. Para la aplicación que registró, seleccione **Manifiesto** en el panel de detalles de la aplicación registrada.
2. Genere un identificador único global. Puede generar un GUID mediante el comando de PowerShell [guid] :: NewGuid () o el cmdlet New-GUID. Otra opción es usar un sitio web generador de GUID.
3. Va a actualizar la sección **appRoles** del manifiesto. En el panel Editar manifiesto, seleccione **Editar** y sustituya `"appRoles": []` por el código JSON que se proporciona. Asegúrese de reemplazar el valor del campo **ID** por el identificador único global que generó. 

    ![Editar manifiesto](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > El valor **Administrador** es necesario para identificar los administradores de Blockchain Workbench.

4. En el manifiesto, cambie también el valor de **Oauth2AllowImplicitFlow** a **true**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

5. Seleccione **Guardar** para guardar los cambios en el manifiesto.

### <a name="add-graph-api-required-permissions"></a>Incorporación de los permisos necesarios de Graph API

La aplicación de API necesita solicitar permiso del usuario para acceder al directorio. Establezca el siguiente permiso necesario para la aplicación de API:

1. En el registro de la aplicación de API de Blockchain, seleccione **Configuración > Permisos necesarios > Seleccionar una API > Microsoft Graph**.

    ![Selección de una API](media/deploy/client-app-select-api.png)

    Haga clic en **Seleccionar**.

2. En **Habilitar acceso** en **Permisos delegados**, elija **Read all users' basic profiles** (Leer los perfiles básicos de todos los usuarios).

    ![Habilitar acceso](media/deploy/client-app-read-perms.png)

    Seleccione **Guardar** y, después, seleccione **Listo**.

3. En **Permisos necesarios**, seleccione **Conceder permisos** y, a continuación, seleccione **Sí** para el mensaje de comprobación.

   ![Concesión de permisos](media/deploy/client-app-grant-permissions.png)

   La concesión de permisos permite a Blockchain Workbench acceder a los usuarios del directorio. Se requiere el permiso de lectura para buscar y agregar miembros a Blockchain Workbench.

### <a name="get-application-id"></a>Obtención del identificador de la aplicación

Se necesita el identificador de la aplicación y la información del inquilino para la implementación. Recopile y almacene la información para su uso durante la implementación.

1. Para la aplicación que registró, seleccione **Configuración** > **Propiedades**.
2. En el panel **Propiedades**, copie y almacene los siguientes valores para su uso durante la implementación.

    ![Propiedades de la aplicación de API](media/deploy/app-properties.png)

    | Configuración para almacenar  | Uso en la implementación |
    |------------------|-------------------|
    | Identificador de aplicación | Configuración de Azure Active Directory > Identificador de aplicación |

### <a name="get-tenant-domain-name"></a>Obtención del nombre de dominio del inquilino

Recopile y almacene el nombre de dominio del inquilino de Active Directory donde se registran las aplicaciones. 

En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **Nombres de dominio personalizados**. Copie y almacene el nombre de dominio.

![Nombre de dominio](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Configuración de usuario invitado

Si tiene usuarios invitados en su inquilino de Azure AD, siga los pasos adicionales para asegurarse de que la asignación y administración de usuarios de Blockchain Workbench funciona correctamente.

1. Cambie a su inquilino de Azure AD y seleccione **Azure Active Directory > Configuración de usuario > Administrar la configuración de colaboración externa**.
2. Establezca la opción **Guest user permissions are limited** (Los permisos de los usuarios invitados están limitados) en **No**.
    ![Configuración de colaboración externa](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Configuración de la dirección URL de respuesta

Una vez que se ha implementado Azure Blockchain Workbench, debe configurar la **dirección URL de respuesta** de la aplicación cliente de Azure Active Directory (Azure AD) correspondiente a la dirección URL web de la instancia de Blockchain Workbench implementada.

1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Compruebe que se encuentra en el inquilino en el que se ha registrado la aplicación cliente de Azure AD.
3. En el panel de navegación izquierdo, seleccione el servicio **Azure Active Directory**. Seleccione **App registrations** (Registros de aplicaciones).
4. Seleccione la aplicación cliente de Azure AD que registró en la sección de requisitos previos.
5. Seleccione **Configuración > URL de respuesta**.
6. Especifique la dirección URL web principal de la implementación de Azure Blockchain Workbench que recuperó en la sección **Obtención de la dirección URL web de Azure Blockchain Workbench**. La dirección URL de respuesta tiene como prefijo `https://`. Por ejemplo: `https://myblockchain2-7v75.azurewebsites.net`

    ![URL de respuesta](media/deploy/configure-reply-url.png)

7. Seleccione **Guardar** para actualizar el registro del cliente.

## <a name="remove-a-deployment"></a>Eliminación de una implementación

Cuando ya no se necesita una implementación, puede quitarla eliminando el grupo de recursos de Blockchain Workbench.

1. En Azure Portal, vaya a **Grupo de recursos** en el panel de navegación izquierdo y seleccione el grupo de recursos que desea eliminar. 
2. Seleccione **Eliminar grupo de recursos**. Compruebe la eliminación escribiendo el nombre del grupo de recursos y seleccionando **Eliminar**.

    ![Eliminación de un grupo de recursos](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha implementado Azure Blockchain Workbench. Para aprender a crear una aplicación de cadena de bloques, continúe con el siguiente artículo de procedimientos.

> [!div class="nextstepaction"]
> [Creación de una aplicación de cadena de bloques en Azure Blockchain Workbench](create-app.md)
