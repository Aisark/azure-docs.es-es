---
title: Hospedaje de aplicaciones web de Azure con equilibrio de carga en el vértice de zona
description: Uso de un registro de alias de Azure DNS para hospedar aplicaciones web con equilibrio de carga en el vértice de zona
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 7/13/2019
ms.author: victorh
ms.openlocfilehash: 7d20ef750aa4556a73852982631423d3d08271f5
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854107"
---
# <a name="host-load-balanced-azure-web-apps-at-the-zone-apex"></a>Hospedaje de aplicaciones web de Azure con equilibrio de carga en el vértice de zona

El protocolo DNS evita la asignación de cualquier elemento que no sea un registro D o AAAA en el vértice de la zona. Un vértice de zona de ejemplo es contoso.com. Esta restricción presenta un problema para los propietarios de aplicaciones que tienen aplicaciones con equilibrio de carga detrás de Traffic Manager. No es posible señalar al perfil de Traffic Manager desde el registro de vértice de zona. Como consecuencia, los propietarios de aplicaciones deben usar una solución alternativa. Un redireccionamiento en la capa de la aplicación debe redirigirse desde el vértice de zona a otro dominio. Un ejemplo es el redireccionamiento de contoso.com a www\.contoso.com. Esa organización presenta un único punto de error en cuanto a la función de redireccionamiento.

Con los registros de alias, este problema ya no existe. Ahora los propietarios de aplicaciones pueden apuntar su registro de vértice de zona a un perfil de Traffic Manager que tenga puntos de conexión externos. Los propietarios de aplicaciones pueden apuntar al mismo perfil de Traffic Manager que se utilice para cualquier otro dominio dentro de su zona DNS.

Por ejemplo, contoso.com y www\.contoso.com pueden apuntar al mismo perfil de Traffic Manager. Esto ocurre siempre que el perfil de Traffic Manager solo tenga configurados puntos de conexión externos.

En este artículo, aprenderá a crear un registro de alias para el vértice de dominio y a configurar los puntos de conexión del perfil de Traffic Manager para sus aplicaciones web.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

Debe tener un nombre de dominio disponible que pueda hospedar en Azure DNS para realizar las pruebas. Debe tener control total de este dominio. El control total incluye la capacidad de establecer los registros de nombre de servidor (NS) para el dominio.

Para obtener instrucciones sobre cómo hospedar el dominio en Azure DNS, vea [Tutorial: Hospedaje del dominio en Azure DNS](dns-delegate-domain-azure-dns.md).

El dominio en el ejemplo que se usa en este tutorial es contoso.com, pero debe usar su propio nombre de dominio.

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos que contenga todos los recursos usados en este artículo.

## <a name="create-app-service-plans"></a>Creación de planes de App Service

Cree dos planes web de App Service en el grupo de recursos, con la siguiente tabla para consultar la información de configuración. Para más información sobre cómo crear un plan de App Service, consulte [Administración de un plan de App Service en Azure](../app-service/app-service-plan-manage.md).


|NOMBRE  |Sistema operativo  |Location  |Nivel de precios  |
|---------|---------|---------|---------|
|ASP-01     |Windows|Este de EE. UU|D1-Shared para desarrollo/pruebas|
|ASP-02     |Windows|Centro de EE. UU.|D1-Shared para desarrollo/pruebas|

## <a name="create-app-services"></a>Creación de servicios de aplicaciones

Cree dos aplicaciones web, una en cada plan de App Service.

1. En la esquina superior izquierda de la página de Azure Portal, haga clic en **Crear un recurso**.
2. Escriba **Aplicación web** en la barra de búsqueda y presione Entrar.
3. Haga clic en **Aplicación web**.
4. Haga clic en **Create**(Crear).
5. Acepte los valores predeterminados y use la tabla siguiente para configurar las dos aplicaciones web:

   |NOMBRE<br>(debe ser único dentro de .azurewebsites.net)|Grupo de recursos |Plan de App Service/ubicación
   |---------|---------|---------|
   |App-01|Usar existente<br>Seleccionar el grupo de recursos|ASP-01(Este de EE. UU.)|
   |App-02|Usar existente<br>Seleccionar el grupo de recursos|ASP 02(Centro de EE. UU.)|

### <a name="gather-some-details"></a>Recopilar algunos detalles

Ahora debe anotar la dirección IP y el nombre de host de las aplicaciones.

1. Abra el grupo de recursos y haga clic en la primera aplicación (**App-01** en este ejemplo).
2. En la columna izquierda, haga clic en **Propiedades**.
3. Anote la dirección que se muestra en **URL** y, en **Direcciones IP de salida**, anote la primera dirección IP de la lista. Usará esta información más adelante cuando configure los puntos de conexión de Traffic Manager.
4. Repita el proceso con **App-02**.

## <a name="create-a-traffic-manager-profile"></a>Crear un perfil de Traffic Manager

Cree un perfil de Traffic Manager en el grupo de recursos. Use los valores predeterminados y escriba un nombre único dentro del espacio de nombres trafficmanager.net.

Para obtener información sobre cómo crear un perfil de Traffic Manager, consulte [Inicio rápido: crear un perfil de Traffic Manager para una aplicación web de alta disponibilidad](../traffic-manager/quickstart-create-traffic-manager-profile.md).

### <a name="create-endpoints"></a>Creación de puntos de conexión

Ahora puede crear los puntos de conexión de las dos aplicaciones web.

1. Abra el grupo de recursos y haga clic en el perfil de Traffic Manager.
2. En la columna izquierda, haga clic en **Puntos de conexión**.
3. Haga clic en **Agregar**.
4. Use la tabla siguiente para configurar los puntos de conexión:

   |type  |NOMBRE  |Destino  |Location  |Configuración de encabezado personalizado|
   |---------|---------|---------|---------|---------|
   |Punto de conexión externo     |End-01|Dirección IP que anotó para App-01|Este de EE. UU|host:\<la dirección URL que anotó para App-01\><br>Ejemplo: **host:app-01.azurewebsites.net**|
   |Punto de conexión externo     |End-02|Dirección IP que anotó para App-02|Centro de EE. UU.|host:\<la dirección URL que anotó para App-02\><br>Ejemplo: **host:app-02.azurewebsites.net**

## <a name="create-dns-zone"></a>Creación de una zona DNS

Puede usar una zona DNS existente para las pruebas o puede crear una zona. Para crear y delegar una nueva zona DNS en Azure, consulte [Tutorial: Hospedaje del dominio en Azure DNS](dns-delegate-domain-azure-dns.md).

### <a name="add-the-alias-record-set"></a>Incorporación del conjunto de registros de alias

Cuando la zona DNS esté lista, puede agregar un registro de alias para el vértice de zona.

1. Abra el grupo de recursos y haga clic en la zona DNS.
2. Haga clic en **Conjunto de registros**.
3. Agregue el conjunto de registros con la siguiente tabla:

   |NOMBRE  |type  |Conjunto de registros de alias  |Tipo de alias  |Recurso de Azure|
   |---------|---------|---------|---------|-----|
   |@     |Una|Sí|Recurso de Azure|Traffic Manager: su perfil|

## <a name="add-custom-hostnames"></a>Incorporación de nombres de host personalizados

Agregue un nombre de host personalizado a ambas aplicaciones web.

1. Abra el grupo de recursos y haga clic en su primera aplicación web.
2. En la columna izquierda, haga clic en **Dominios personalizados**.
3. Cierre **Agregar nombre de host**.
4. En Nombre de host, escriba el nombre de dominio. Por ejemplo, contoso.com.

   El dominio debe pasar la validación y mostrar marcas de verificación verdes junto a **Disponibilidad de nombre de host** y **Propiedad de dominio**.
5. Cierre **Agregar nombre de host**.
6. Para ver el nuevo nombre de host en **Nombres de host asignados al sitio**, actualice el explorador. La actualización en la página no siempre muestra los cambios inmediatamente.
7. Repita este procedimiento con la segunda aplicación web.

## <a name="test-your-web-apps"></a>Prueba de las aplicaciones web

Ahora puede comprobar que puede llegar a la aplicación web y que se está equilibrando la carga.

1. Abra un explorador web y vaya a su dominio. Por ejemplo, contoso.com. Verá la página de aplicación web predeterminada.
2. Detenga su primera aplicación web.
3. Cierre el explorador web y espere unos minutos.
4. Inicie el explorador web y vaya a su dominio. Debería seguir viendo la página de aplicación web predeterminada.
5. Detenga la segunda aplicación web.
6. Cierre el explorador web y espere unos minutos.
7. Inicie el explorador web y vaya a su dominio. Debería ver el Error 403, que indica que la aplicación web se ha detenido.
8. Inicie la segunda aplicación web.
9. Cierre el explorador web y espere unos minutos.
10. Inicie el explorador web y vaya a su dominio. Debería ver de nuevo la página de aplicación web predeterminada.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los registros de alias, consulte los artículos siguientes:

- [Tutorial: Configuración de un registro de alias para hacer referencia a una dirección IP pública de Azure](tutorial-alias-pip.md)
- [Tutorial: Configuración de un registro de alias para admitir nombres de dominio de Apex con Traffic Manager](tutorial-alias-tm.md)
- [Preguntas más frecuentes sobre DNS](https://docs.microsoft.com/azure/dns/dns-faq#alias-records)

Para información sobre cómo migrar un nombre de DNS activo, consulte [Migración de un nombre de DNS activo a Azure App Service](../app-service/manage-custom-dns-migrate-domain.md).
