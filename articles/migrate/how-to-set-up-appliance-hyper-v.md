---
title: Configurar un dispositivo de Azure Migrate para Hyper-V
description: Obtenga información sobre cómo configurar un dispositivo de Azure Migrate para evaluar y migrar VM de Hyper-V.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: a94d11d48728b03dd978af85db4b6c2af4887938
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534501"
---
# <a name="set-up-an-appliance-for-hyper-v-vms"></a>Configuración de un dispositivo para VM de Hyper-V

En este artículo se describe cómo configurar el dispositivo Azure Migrate si va a evaluar VM de Hyper-V con la herramienta Azure Migrate Server Assessment o a migrar VM de VMware a Azure con la herramienta Azure Migrate Server Migration.

El dispositivo de VM de Hyper-V es un dispositivo ligero que Azure Migrate Server Assessment/Migration usa para lo siguiente:

- Detectar VM de Hyper-V locales.
- Enviar metadatos y datos de rendimiento para las VM detectadas a Azure Migrate Server Assessment/Migration.

[Más información](migrate-appliance.md) sobre el dispositivo de Azure Migrate.


## <a name="appliance-deployment-steps"></a>Pasos de implementación del dispositivo

Para configurar el dispositivo:
- Descargue un disco duro virtual de Hyper-V comprimido desde Azure Portal.
- Cree el dispositivo y compruebe que se puede conectar a Azure Migrate Server Assessment.
- Configure el dispositivo por primera vez y regístrelo en el proyecto de Azure Migrate.

## <a name="download-the-vhd"></a>Descarga del disco duro virtual

Descargue la plantilla del disco duro virtual comprimido del dispositivo.

1. En **Objetivos de migración** > **Servidores** > **Azure Migrate: Server Assessment**, haga clic en **Detectar**.
2. En **Detectar máquinas** >  **¿Las máquinas están virtualizadas?** , haga clic en **Sí, con Hyper-V**.
3. Haga clic en **Descargar** para descargar el archivo del disco duro virtual.

    ![Descargar máquina virtual](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash del disco duro virtual.
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.vhd SHA256```
3.  El código hash generado debe coincidir con esta configuración para la versión 2.19.11.12 del dispositivo.

  **Algoritmo** | **Valor del código hash**
  --- | ---
  MD5 | 29a7531f32bcf69f32d964fa5ae950bc
  SHA256 | 37b3f27bc44f475872e355f04fcb8f38606c84534c117d1609f2d12444569b31



## <a name="create-the-appliance-vm"></a>Creación de la máquina virtual del dispositivo

Importe el archivo descargado y cree la VM.

1. Extraiga el archivo del disco duro virtual comprimido en la carpeta del host de Hyper-V que hospedará la VM del dispositivo. Se extraen tres carpetas.
2. Abra el administrador de Hyper-V. En **Acciones**, haga clic en **Importar máquina virtual**.

    ![Implementar disco duro virtual](./media/how-to-set-up-appliance-hyper-v/deploy-vhd.png)

2. En el Asistente para importar máquinas virtuales > **Antes de comenzar**, haga clic en **Siguiente**.
3. En **Buscar carpeta**, especifique la carpeta que contiene el disco duro virtual extraído. A continuación, haga clic en **Siguiente**.
1. En **Seleccionar máquina virtual**, haga clic en **Siguiente**.
2. En **Elegir tipo de importación**, haga clic en **Copiar la máquina virtual (crear un identificador único nuevo)** . A continuación, haga clic en **Siguiente**.
3. En **Elegir destino**, deje la configuración predeterminada. Haga clic en **Next**.
4. En **Carpetas de almacenamiento**, deje la configuración predeterminada. Haga clic en **Next**.
5. En **Elegir red**, especifique el conmutador virtual que usará la VM. El conmutador necesita conectividad a Internet para enviar datos a Azure.
6. En **Resumen**, revise los valores de configuración. Haga clic en **Finalizar**.
7. En Administrador de Hyper-V > **Máquinas virtuales**, inicie la VM.


### <a name="verify-appliance-access-to-azure"></a>Verificación de que el dispositivo puede acceder a Azure

Asegúrese de que la máquina virtual del dispositivo se puede conectar a las [direcciones URL de Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

## <a name="configure-the-appliance"></a>Configuración del dispositivo

Configure el dispositivo por primera vez.

1. En Administrador de Hyper-V > **Máquinas virtuales**, haga clic con el botón derecho en la VM > **Conectar**.
2. Especifique el idioma, la zona horaria y la contraseña del dispositivo.
3. Abra un explorador en cualquier equipo que pueda conectarse a la máquina virtual y abra la dirección URL de la aplicación web del dispositivo: **https://*nombre o dirección IP del dispositivo*: 44368**.

   Como alternativa, puede abrir la aplicación desde el escritorio del dispositivo, para lo que debe hacer clic en el acceso directo de la aplicación.
1. En la aplicación web > **Set up prerequisites** (Configurar los requisitos previos ), realice las siguientes operaciones:
    - **License** (Licencia): Acepte los términos de licencia y lea la información de terceros.
    - **Connectivity** (Conectividad): la aplicación comprueba que la máquina virtual tiene acceso a Internet. Si la máquina virtual usa un proxy:
        - Haga clic en **Configuración de proxy** y especifique el puerto de escucha y la dirección del proxy con los formatos http://ProxyIPAddress o http://ProxyFQDN.
        - Especifique las credenciales si el proxy requiere autenticación.
        - Solo se admite un proxy HTTP.
    - **Time sync** (Sincronización de hora): Se verifica la hora. Para que la detección funcione correctamente, la hora del dispositivo debe estar sincronizada con la hora de Internet.
    - **Instalación de actualizaciones**: Azure Migrate Server Assessment comprueba que el dispositivo tiene instaladas las actualizaciones más recientes.

### <a name="register-the-appliance-with-azure-migrate"></a>Registro del dispositivo en Azure Migrate

1. Haga clic en **Iniciar sesión**. Si no aparece, asegúrese de que ha deshabilitado el bloqueador de elementos emergentes en el explorador.
2. En la pestaña nueva, inicie sesión con sus credenciales de Azure.
    - Inicie sesión con su nombre de usuario y contraseña.
    - No se admite el inicio de sesión con un PIN.
3. Después de iniciar sesión correctamente, vuelva a la aplicación web.
4. Seleccione la suscripción en la que se creó el proyecto de Azure Migrate. Seleccione el proyecto.
5. Escriba un nombre para el dispositivo. Este nombre debe ser alfanumérico y no puede tener más de 14 caracteres.
6. Haga clic en **Registrar**.


### <a name="delegate-credentials-for-smb-vhds"></a>Delegación de credenciales para discos duros virtuales de SMB

Si va a ejecutar discos duros virtuales en SMB, debe habilitar la delegación de credenciales desde el dispositivo a los hosts de Hyper-V. Para ello, desde el dispositivo:

1. En la VM del dispositivo, ejecute este comando. HyperVHost1 y HyperVHost2 son nombres de host de ejemplo.

    ```
    Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
    ```

2. También puede hacerlo en el Editor de directivas de grupo local en el dispositivo:
    - En **Directiva de equipo local** > **Configuración del equipo**, haga clic en **Plantillas administrativas** > **Sistema** > **Delegación de credenciales**.
    - Haga doble clic en **Permitir delegación de credenciales nuevas** y seleccione **Habilitado**.
    - En **Opciones**, haga clic en **Mostrar** y agregue cada host de Hyper-V que desee detectar en la lista, con **wsman/** como prefijo.
    - En **Delegación de credenciales**, haga doble clic en **Permitir la delegación de credenciales nuevas con autenticación solo NTLM de servidor**. De nuevo, agregue cada host de Hyper-V que quiera detectar en la lista, con **wsman/** como prefijo.

## <a name="start-continuous-discovery"></a>Inicio de detección continua

Conéctese desde el dispositivo a los hosts o clústeres de Hyper-V e inicie la detección de VM.

1. En **Nombre de usuario** y **Contraseña**, especifique las credenciales de la cuenta que el dispositivo utilizará para detectar las VM. Especifique un nombre descriptivo para las credenciales y haga clic en **Guardar los detalles**.
2. Haga clic en **Agregar host** y especifique los detalles del host o clúster de Hyper-V.
3. Haga clic en **Validar**. Después de la validación, se muestra el número de VM que se pueden detectar en cada host o clúster.
    - Si se produce un error de validación para un host, revise el error manteniendo el puntero sobre el icono en la columna **Estado**. Corrija los problemas y vuelva a validar.
    - Para quitar hosts o clústeres, seleccione > **Eliminar**.
    - No se puede quitar un host específico de un clúster. Solo puede quitar todo el clúster.
    - Puede agregar un clúster, incluso si hay problemas con hosts específicos del clúster.
4. Después de la validación, haga clic en **Guardar e iniciar la detección** para iniciar el proceso de detección.

De esta forma comienza la detección. Los metadatos de las VM detectadas tardan unos 15 minutos en aparecer en Azure Portal.

## <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

Una vez finalizada la detección, puede verificar que las VM aparezcan en el portal.

1. Abra el panel de Azure Migrate.
2. En la página **Azure Migrate - Servidores** > **Azure Migrate: Server Assessment**, haga clic en el icono que muestra el número de **servidores detectados**.


## <a name="next-steps"></a>Pasos siguientes

Pruebe la [evaluación de Hyper-V](tutorial-assess-hyper-v.md) con Azure Migrate Server Assessment.
