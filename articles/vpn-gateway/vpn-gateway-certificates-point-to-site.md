---
title: 'Generación y exportación de certificados para conexiones de punto a sitio: PowerShell: Azure | Microsoft Docs'
description: Cree un certificado raíz autofirmado, exporte la clave pública y genere los certificados de cliente mediante PowerShell en Windows 10 o Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "60764023"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generación y exportación de certificados para conexiones de punto a sitio con PowerShell

Las conexiones de punto a sitio utilizan certificados para realizar la autenticación. En este artículo, se muestra cómo crear un certificado raíz autofirmado y generar certificados de cliente usando PowerShell en Windows 10 o Windows Server 2016. Si busca otras instrucciones de certificado, vea los artículos sobre [certificados con Linux](vpn-gateway-certificates-point-to-site-linux.md) o [certificados con MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Debe realizar los pasos de este artículo en un equipo que ejecute Windows 10 o Windows Server 2016. Los cmdlets de PowerShell que se usan para generar certificados forman parte del sistema operativo y no funcionan en otras versiones de Windows. El equipo con Windows 10 o Windows Server 2016 solo es necesario para generar los certificados. Una vez que se generan los certificados, puede cargarlos o instalarlos en cualquier sistema operativo cliente compatible. 

Si no tiene acceso a un equipo con Windows 10 o Windows Server 2016, puede usar [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) para generar certificados. Los certificados que genera mediante cualquiera de estos métodos pueden instalarse en cualquier sistema operativo cliente [compatible](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq).

## <a name="rootcert"></a>1. Creación de un certificado raíz autofirmado

Use el cmdlet New-SelfSignedCertificate para crear un certificado raíz autofirmado. Para obtener información adicional sobre los parámetros, consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. En un equipo con Windows 10 o Windows Server 2016, abra una consola de Windows PowerShell con privilegios elevados. Estos ejemplos no funcionan en Azure Cloud Shell "Try It". Debe ejecutar estos ejemplos localmente.
2. Utilice el ejemplo siguiente para crear el certificado raíz autofirmado. En el ejemplo siguiente se crea un certificado raíz firmado automáticamente con el nombre "P2SRootCert" que se instala automáticamente en "Certificates-Current User\Personal\Certificates". Puede ver el certificado si abre *certmgr.msc*, o bien *Administrar certificados de usuario*.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. Generación de un certificado de cliente

Cada equipo cliente que se conecta a una red virtual con una conexión de punto a sitio debe tener instalado un certificado de cliente. Puede generarlo desde un certificado raíz autofirmado y, luego, exportar e instalar el certificado de cliente. Si no está instalado el certificado de cliente, se produce un error de autenticación. 

Los pasos siguientes lo llevan por el proceso de generación de un certificado de cliente a partir de un certificado autofirmado. Puede generar varios certificados de cliente desde el mismo certificado raíz. Al generar certificados de cliente mediante los pasos siguientes, el certificado de cliente se instala automáticamente en el equipo que se usó para generar el certificado. Si desea instalar un certificado de cliente en otro equipo cliente, puede exportar el certificado.

Los ejemplos usan el cmdlet New-SelfSignedCertificate para generar un certificado de cliente que expira en un año. Para obtener información adicional sobre los parámetros (por ejemplo, cómo establecer un valor de expiración diferente para el certificado de cliente), consulte [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Ejemplo 1

Use este ejemplo si no ha cerrado la consola de PowerShell después de crear el certificado raíz autofirmado. Este ejemplo continúa desde la sección anterior y usa la variable "$cert" declarada. Si cerró la consola de PowerShell después de crear el certificado raíz autofirmado o va a crear más certificados de cliente en una nueva sesión de consola de PowerShell, siga los pasos del [Ejemplo 2](#ex2).

Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificación alguna, el resultado es un certificado de cliente con el nombre "P2SChildCert".  Si quiere agregar algo más al nombre del certificado secundario, modifique el valor CN. No cambie el valor de TextExtension cuando ejecute este ejemplo. El certificado de cliente que genera se instala automáticamente en la ruta del equipo "Certificates - Current User\Personal\Certificates".

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Ejemplo 2

Si va a crear más certificados de cliente, o bien no está usando la misma sesión de PowerShell que empleó para crear el certificado raíz autofirmado, siga estos pasos:

1. Identifique el certificado raíz autofirmado que se instaló en el equipo. Este cmdlet devuelve una lista de certificados que están instalados en el equipo.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Busque el nombre del firmante de la lista devuelta y, luego, copie la huella digital que se encuentra junto a él en un archivo de texto. En el ejemplo siguiente, hay dos certificados. El nombre CN es el nombre del certificado raíz autofirmado a partir del que va a generar un certificado secundario. En este caso, "P2SRootCert".

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Declare una variable para el certificado raíz con la huella digital del paso anterior. Reemplace la huella digital con la del certificado raíz a partir del que va a generar un certificado secundario.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Por ejemplo, al usar la huella digital de P2SRootCert del paso anterior, la variable tendrá el aspecto similar al siguiente:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Modifique y ejecute el ejemplo para generar un certificado de cliente. Si ejecuta el ejemplo siguiente sin modificación alguna, el resultado es un certificado de cliente con el nombre "P2SChildCert". Si quiere agregar algo más al nombre del certificado secundario, modifique el valor CN. No cambie el valor de TextExtension cuando ejecute este ejemplo. El certificado de cliente que genera se instala automáticamente en la ruta del equipo "Certificates - Current User\Personal\Certificates".

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. Exportación de la clave pública del certificado raíz (.cer)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportación del certificado raíz autofirmado y clave privada para almacenarlo (opcional)

Puede que desee exportar el certificado autofirmado y almacenarlo de manera segura como copia de seguridad. Si es necesario, más adelante puede instalarlo en otro equipo y generar más certificados de cliente. Para exportar el certificado raíz autofirmado como archivo .pfx, seleccione el certificado raíz y use los mismos pasos descritos en [Exportación de un certificado de cliente](#clientexport).

## <a name="clientexport"></a>4. Exportación del certificado de cliente

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Instalación de un certificado de cliente exportado

Cada cliente que se conecta a la red virtual a través de una conexión de punto a sitio requiere que haya un certificado de cliente instalado localmente.

Para instalar un certificado de cliente, consulte cómo [instalar un certificado de cliente para conexiones de punto a sitio](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Continúe con los pasos de configuración de conexiones de punto a sitio.

Continúe con la configuración de punto a sitio.

* Para conocer los pasos del modelo de implementación de **Resource Manager**, consulte cómo [configurar una conexión de punto a sitio mediante la autenticación de certificados de Azure nativa](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Para ver los pasos del modelo de implementación **clásica**, consulte el artículo [Configuración de una conexión VPN de punto a sitio a una red virtual mediante el Portal clásico](vpn-gateway-howto-point-to-site-classic-azure-portal.md).
* Para obtener información acerca de cómo solucionar problemas de P2S, consulte [Solución de problemas: conexión de punto a sitio de Azure](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
