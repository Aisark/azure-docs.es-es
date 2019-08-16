---
title: Directivas de autenticación de Azure API Management | Microsoft Docs
description: Aprenda sobre las directivas de autenticación disponibles para su uso en Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: bd31d711c58a63b5c15712c1774d48433c62f18d
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2019
ms.locfileid: "68774973"
---
# <a name="api-management-authentication-policies"></a>Directivas de autenticación de Azure API Management
En este tema se proporciona una referencia para las siguientes directivas de API Management. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="AuthenticationPolicies"></a> Directivas de autenticación

-   [Autenticar con opción básica](api-management-authentication-policies.md#Basic) : autenticar con un servicio de back-end mediante la autenticación básica.

-   [Autenticar con certificado de cliente](api-management-authentication-policies.md#ClientCertificate) : autenticar con un servicio de back-end mediante certificados de cliente.

-   [Autenticar con identidad administrada](api-management-authentication-policies.md#ManagedIdentity): autenticar con una [identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) para el servicio API Management.

##  <a name="Basic"></a> Autenticación con Basic
 Use la directiva `authentication-basic` para realizar la autenticación con un servicio de back-end mediante autenticación Básica. Esta directiva establece eficazmente el encabezado de autorización HTTP en el valor correspondiente a las credenciales proporcionadas en la directiva.

### <a name="policy-statement"></a>Instrucción de la directiva

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Ejemplo

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elementos

|NOMBRE|DESCRIPCIÓN|Obligatorio|
|----------|-----------------|--------------|
|authentication-basic|Elemento raíz.|Sí|

### <a name="attributes"></a>Atributos

|NOMBRE|DESCRIPCIÓN|Obligatorio|Valor predeterminado|
|----------|-----------------|--------------|-------------|
|username|Especifica el nombre de usuario de la credencial básica.|Sí|N/D|
|password|Especifica la contraseña de usuario de la credencial básica.|Sí|N/D|

### <a name="usage"></a>Uso
 Esta directiva puede usarse en las siguientes [secciones](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.

-   **Secciones de la directiva:** inbound (entrada)

-   **Ámbitos de la directiva:** todos los ámbitos

##  <a name="ClientCertificate"></a> Autenticación Básica
 Use la directiva `authentication-certificate` para realizar la autenticación con un servicio de back-end mediante un certificado de cliente. El certificado se debe [instalar primero en API Management](https://go.microsoft.com/fwlink/?LinkID=511599) y se identifica mediante su huella digital.

### <a name="policy-statement"></a>Instrucción de la directiva

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Ejemplos

En este ejemplo, el certificado de cliente se identifica mediante su huella digital.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
En este ejemplo, el certificado de cliente se identifica mediante el nombre de recurso.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Elementos  
  
|NOMBRE|DESCRIPCIÓN|Obligatorio|  
|----------|-----------------|--------------|  
|authentication-certificate|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|NOMBRE|DESCRIPCIÓN|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|thumbprint|La huella digital del certificado de cliente.|`thumbprint` o `certificate-id` debe estar presente.|N/D|  
|certificate-id|Nombre del recurso de certificado.|`thumbprint` o `certificate-id` debe estar presente.|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound (entrada)  
  
-   **Ámbitos de la directiva:** todos los ámbitos  

##  <a name="ManagedIdentity"></a> Autenticación con una identidad administrada  
 Use la directiva `authentication-managed-identity` para autenticarse con un servicio de back-end mediante la identidad administrada del servicio de API Management. En esencia, esta directiva usa la identidad administrada para obtener un token de acceso de Azure Active Directory para acceder al recurso especificado. Después de obtener el token correctamente, la Directiva establecerá el valor del token en el `Authorization` encabezado mediante el esquema `Bearer`.
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Ejemplo  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Uso de una identidad administrada para autenticación con un servicio back-end
```xml  
<authentication-managed-identity resource="https://graph.windows.net"/> 
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Uso de una identidad administrada en una directiva send-request
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elementos  
  
|NOMBRE|DESCRIPCIÓN|Obligatorio|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|NOMBRE|DESCRIPCIÓN|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|resource|String. URI del identificador de aplicación de la API web de destino (recurso seguro) en Azure Active Directory.|Sí|N/D|  
|output-token-variable-name|String. Nombre de la variable de contexto que recibirá el valor del token como un tipo de objeto `string`. |Sin|N/D|  
|ignore-error|Booleano. Si se establece en `true`, la canalización de directivas seguirá ejecutándose incluso si no se obtiene un token de acceso.|Sin|false|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound (entrada)  
  
-   **Ámbitos de la directiva:** todos los ámbitos

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo trabajar con directivas, consulte:

+ [Directivas de Azure API Management](api-management-howto-policies.md)
+ [API de transformación](transform-api.md)
+ En la [Referencia de directivas](api-management-policy-reference.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](policy-samples.md)
