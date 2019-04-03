---
title: Copia de seguridad y restauración de máquinas virtuales de Azure cifradas mediante el uso de servicios de Azure Backup
description: Este artículo trata sobre la experiencia de copia de seguridad y restauración de máquinas virtuales cifradas mediante Azure Disk Encryption.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 7/10/2018
ms.author: geetha
ms.openlocfilehash: 28126df0dfd9a03e93a76fa5071331603c4819a4
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 04/02/2019
ms.locfileid: "58851026"
---
# <a name="back-up-and-restore-encrypted-virtual-machines-with-azure-backup"></a>Copia de seguridad y restauración de máquinas virtuales cifradas con Azure Backup
Este artículo trata sobre los pasos para realizar la copia de seguridad y restauración de máquinas virtuales (VM) mediante Azure Backup. También se proporcionan detalles sobre los escenarios admitidos, requisitos previos y pasos para solucionar problemas en los casos de error.

## <a name="supported-scenarios"></a>Escenarios admitidos

 La copia de seguridad y restauración de máquinas virtuales cifradas solo se admite para máquinas virtuales que usan el modelo de implementación de Azure Resource Manager. No se admite en las que usan el modelo de implementación clásica. La copia de seguridad y la restauración de máquinas virtuales cifradas se admite en máquinas virtuales Windows y Linux que usen Azure Disk Encryption. Disk Encryption emplea BitLocker, la característica estándar del sector de Windows y la característica dm-crypt de Linux para proporcionar el cifrado de los discos. En la tabla siguiente se muestra el tipo de cifrado y la compatibilidad con las máquinas virtuales.

   |  | BEK + máquinas virtuales con KEK | Máquinas virtuales solo con BEK |
   | --- | --- | --- |
   | **Máquinas virtuales no administradas**  | Sí | Sí  |
   | **Máquinas virtuales administradas**  | Sí | Sí  |

   > [!NOTE]
   > Azure Backup admite máquinas virtuales cifradas mediante claves independiente. Actualmente no se admite ninguna clave que forme parte de un certificado usado para cifrar una máquina virtual.
   >

## <a name="prerequisites"></a>Requisitos previos
* Las máquinas virtuales se cifran mediante [Azure Disk Encryption](../security/azure-security-disk-encryption.md).

* Se ha creado un almacén de Recovery Services y la replicación del almacenamiento se ha establecido siguiendo los pasos que se indican en la [preparación del entorno para la copia de seguridad](backup-azure-arm-vms-prepare.md).

* Se han otorgado a Azure Backup permisos de acceso a un almacén de claves que contiene las claves y secretos de las máquinas virtuales cifradas.

## <a name="back-up-an-encrypted-vm"></a>Realizar copias de seguridad de una máquina virtual cifrada
Utilice los pasos siguientes para establecer un objetivo de copia de seguridad, definir directivas, configurar elementos y desencadenar una copia de seguridad.

### <a name="configure-backup"></a>Configuración de la copia de seguridad
1. Si ya tiene abierto un almacén de Recovery Services, vaya al siguiente paso. Si no tiene abierto ningún almacén de Recovery Services, pero está en Azure Portal, seleccione **Todos los servicios**.

    a. En la lista de recursos, escriba **Recovery Services**.

   b. Cuando comience a escribir, la lista se filtrará en función de la entrada. Cuando vea la opción **Almacenes de Recovery Services**, haga clic en ella.

      ![Almacén de Recovery Services](./media/backup-azure-vms-encryption/browse-to-rs-vaults.png) <br/>

    c. Aparece la lista de almacenes de Recovery Services. Seleccione un almacén de la lista.

     Se abre el panel del almacén seleccionado.
1. En la lista de elementos que aparece en el almacén, seleccione **Copia de seguridad** para iniciar la copia de seguridad de la máquina virtual cifrada.

      ![Hoja Copia de seguridad](./media/backup-azure-vms-encryption/select-backup.png)
1. En el icono **Copia de seguridad**, seleccione **Objetivo de Backup**.

      ![Hoja Escenario](./media/backup-azure-vms-encryption/select-backup-goal-one.png)
1. En **¿Dónde se ejecuta su carga de trabajo?**, seleccione **Azure**. En **qué desea realizar copias de seguridad?**, seleccione **Máquina Virtual**. Después seleccione **Aceptar**.

   ![Hoja Escenario abierta](./media/backup-azure-vms-encryption/select-backup-goal-two.png)
1. En **Elegir directiva de copia de seguridad**, seleccione la directiva de copia de seguridad que quiere aplicar al almacén. Después seleccione **Aceptar**.

      ![Seleccionar directiva de copia de seguridad](./media/backup-azure-vms-encryption/setting-rs-backup-policy-new.png)

    Se muestran los detalles de la directiva predeterminada. Si quiere crear una directiva, seleccione **Crear nuevo** en la lista desplegable. Al seleccionar **Aceptar**, la directiva de copia de seguridad se asocia con el almacén.

1. Elija las máquinas virtuales cifradas que se asociarán con la directiva especificada y seleccione **Aceptar**.

      ![Selección de las máquinas virtuales cifradas](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)
1. Esta página muestra un mensaje sobre los almacenes de claves asociados a las máquinas virtuales cifradas seleccionadas. El servicio Backup necesita acceso de solo lectura a las claves y los secretos del almacén de claves. Estos permisos los usa para hacer copia de seguridad de ellos, junto con la de las máquinas virtuales asociadas.<br>
Si eres un **usuario miembro**, proceso de habilitar la copia de seguridad a la perfección adquirirá el acceso al almacén de claves para realizar una copia de seguridad de máquinas virtuales cifradas sin necesidad de intervención del usuario.

   ![Mensaje de máquinas virtuales cifradas](./media/backup-azure-vms-encryption/member-user-encrypted-vm-warning-message.png)

   En el caso de los **usuarios invitados**, es necesario conceder permisos al servicio Backup para que pueda acceder al almacén de claves y puedan realizarse copias de seguridad. Para proporcionar estos permisos, siga los pasos que se indican en la siguiente sección

   ![Mensaje de máquinas virtuales cifradas](./media/backup-azure-vms-encryption/guest-user-encrypted-vm-warning-message.png)

    Ahora que ha definido toda la configuración del almacén, haga clic en **Habilitar Backup** en la parte inferior de la página. **Habilitar Backup** permite implementar la directiva en el almacén y en las máquinas virtuales.

1. La siguiente fase de la preparación es instalar el agente de máquina virtual o comprobar que esté instalado. Para ello, siga los pasos que se indican en la [preparación de su entorno para la copia de seguridad](backup-azure-arm-vms-prepare.md).

### <a name="trigger-a-backup-job"></a>Desencadenamiento de un trabajo de copia de seguridad
Siga los pasos mencionados en el artículo [Copia de seguridad de máquinas virtuales de Azure en un almacén de Recovery Services](backup-azure-arm-vms.md) para desencadenar el trabajo de copia de seguridad.

### <a name="continue-backups-of-already-backed-up-vms-with-encryption-enabled"></a>Continuación de las copias de seguridad de máquinas virtuales ya copiadas con el cifrado habilitado  
Si tiene máquinas virtuales ya que se va a una copia de seguridad en un almacén de Recovery Services que están habilitadas para el cifrado más adelante, debe conceder permisos al fondo hasta el acceso al almacén de claves para que las copias de seguridad continuar. Para proporcionar estos servicios, puede seguir los [pasos de la sección siguiente](#provide-permissions). O bien, puede seguir los pasos de PowerShell en la sección "Habilitar Backup" de la [documentación de PowerShell](backup-azure-vms-automation.md).

## <a name="provide-permissions"></a>Proporcionar permisos
Utilice los pasos siguientes para proporcionar los permisos relevantes para Azure Backup acceder al almacén de claves y realizar copia de seguridad de máquinas virtuales cifradas.
1. Seleccione **Todos los servicios** y busque **Almacenes de claves**.

    ![Almacenes de claves](./media/backup-azure-vms-encryption/search-key-vault.png)

1. En la lista de almacenes de claves, seleccione el almacén de claves asociado a la máquina virtual cifrada de la que es necesario hacer una copia de seguridad.

     ![Selección del almacén de claves](./media/backup-azure-vms-encryption/select-key-vault.png)

1. Seleccione **Directivas de acceso** y luego seleccione **Agregar nueva**.

    ![Agregar nueva](./media/backup-azure-vms-encryption/select-key-vault-access-policy.png)

1. Haga clic en **Seleccionar la entidad de seguridad** y escriba **servicio de administración de copias de seguridad** en el cuadro de búsqueda.

    ![Búsqueda del servicio Backup](./media/backup-azure-vms-encryption/search-backup-service.png)

1. Seleccione **Backup Management Service** (Servicio de administración de copias de seguridad) y haga clic en el botón **Seleccionar**.

    ![Selección del servicio Backup](./media/backup-azure-vms-encryption/select-backup-service.png)

1. En **Configurar a partir de una plantilla (opcional)**, seleccione **Azure Backup**. Los permisos necesarios se rellenan previamente en **Permisos clave** y **Permisos de secretos**. Si la máquina virtual se cifra con **solo BEK**, únicamente se necesitan permisos para los secretos, por lo que debe eliminar la selección de los **permisos de clave**.

    ![Selección de Azure Backup](./media/backup-azure-vms-encryption/select-backup-template.png)

1. Seleccione **Aceptar**. Tenga en cuenta que **Backup Management Service** se agrega en **Directivas de acceso**.

    ![Directivas de acceso](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

1. Seleccione **Guardar** para conceder los permisos necesarios a Backup.

    ![Directiva de acceso de Backup](./media/backup-azure-vms-encryption/save-access-policy.png)

Una vez que los permisos se han proporcionado correctamente, puede continuar con la habilitación de la copia de seguridad para máquinas virtuales cifradas.

## <a name="restore-an-encrypted-vm"></a>Restauración de una máquina virtual cifrada
Azure Backup admite ahora la restauración de la [máquina virtual cifrada de Azure sin Azure AD](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-prerequisites-aad), además de la oferta anterior de soporte técnico de restauración para máquinas virtuales cifradas de Azure con Azure AD.<br>

Para restaurar una máquina virtual cifrada, primero restaure los discos; para ello, siga los pasos de la sección "Restauración de discos de copia de seguridad" de [Elección de una configuración de restauración para una máquina virtual](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Después de eso, puede usar una de las siguientes opciones:

* Siga los pasos de PowerShell que se indican en [Creación de una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para crear una máquina virtual completa a partir de discos restaurados.
* O bien, [use plantillas para personalizar una máquina virtual restaurada](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm) para crear máquinas virtuales a partir de discos restaurados. Solo se pueden usar plantillas para los puntos de recuperación creados después del 26 de abril de 2017.

## <a name="troubleshooting-errors"></a>Solución de errores
| Operación | Detalles del error | Resolución |
| --- | --- | --- |
|Copia de seguridad | Código de error: UserErrorKeyVaultPermissionsNotConfigured<br><br>Mensaje de error: El servicio Azure Backup no tiene los permisos suficientes para acceder a Key Vault para realizar copias de seguridad de máquinas virtuales cifradas. | Es necesario proporcionar estos permisos a Backup mediante los [pasos de la sección anterior](#provide-permissions). O bien puede seguir los pasos de PowerShell en la sección "Habilitar protección" del artículo, [Usar PowerShell para realizar copias de seguridad y restauración de máquinas virtuales](backup-azure-vms-automation.md#enable-protection). |  
| Restauración | No se puede restaurar esta máquina virtual cifrada porque no existe el almacén de claves asociado con esta máquina virtual. |Cree un almacén de claves mediante los pasos descritos en [¿Qué es Azure Key Vault?](../key-vault/key-vault-overview.md). Consulte [Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup](backup-azure-restore-key-secret.md) para restaurar la clave y el secreto si estos no existen. |
| Restauración | Código de error: UserErrorKeyVaultKeyDoesNotExist<br><br> Mensaje de error: No se puede restaurar esta máquina virtual cifrada porque no existe la clave asociada con esta máquina virtual. |Consulte [Restauración de la clave y el secreto de Key Vault para máquinas virtuales cifradas mediante Azure Backup](backup-azure-restore-key-secret.md) para restaurar la clave y el secreto si estos no existen. |
| Restauración | Código de error: ProviderAuthorizationFailed/UserErrorProviderAuthorizationFailed<br><br>Mensaje de error: El servicio Backup no tiene autorización para acceder a los recursos de su suscripción. |Como se ha mencionado anteriormente, restaure primero los discos según los pasos descritos en la sección "Restauración de discos de copia de seguridad" de [Elección de una configuración de restauración para una máquina virtual](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Después, use PowerShell para [crear una máquina virtual a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks). |
