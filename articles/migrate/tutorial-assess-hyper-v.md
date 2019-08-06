---
title: Evaluación de máquinas virtuales de Hyper-V para la migración a Azure con Azure Migrate | Microsoft Docs
description: Describe cómo evaluar máquinas virtuales de Hyper-V locales para su migración a Azure mediante Azure Migrate.
author: rayne-wiselman
manager: carmonm
ms.service: azure-migrate
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: c790667c73adfed061b97b14ebb7df4c68461786
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663795"
---
# <a name="assess-hyper-v-vms-with-azure-migrate-server-assessment"></a>Evaluación de las máquinas virtuales de Hyper-V con Azure Migrate Server Assessment

En este artículo se muestra cómo evaluar máquinas virtuales de Hyper-V locales mediante Azure Migrate: Herramienta Server Assessment.

[Azure Migrate](migrate-services-overview.md) proporciona un centro de herramientas que le ayuda a detectar las aplicaciones, la infraestructura y las cargas de trabajo, a evaluarlas y a migrarlas a Microsoft Azure. Dicho centro incluye herramientas de Azure Migrate y ofertas de fabricantes de software independientes (ISV) de terceros.



Este tutorial es el segundo de una serie que muestra cómo evaluar máquinas virtuales de Hyper-V y migrarlas a Azure. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar un proyecto de Azure Migrate.
> * Configurar y registrar un dispositivo de Azure Migrate.
> * Iniciar la detección continua de máquinas virtuales locales.
> * Agrupar las máquinas virtuales detectadas y evaluar el grupo.
> * Revisar la evaluación.

> [!NOTE]
> En los tutoriales se muestra la ruta de implementación más sencilla para un escenario, de modo que pueda configurar rápidamente una prueba de concepto. En ellos se usan las opciones predeterminadas siempre que es posible y no muestran todos los valores y rutas de acceso posibles. Para obtener instrucciones detalladas, consulte los artículos acerca de los distintos procedimientos.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/pricing/free-trial/) antes de empezar.


## <a name="prerequisites"></a>Requisitos previos

- [Complete](tutorial-prepare-hyper-v.md) el primer tutorial de esta serie. Si no lo hace, las instrucciones de este tutorial no funcionarán.
- Esto es lo que debería haber hecho en el primer tutorial:
    - [Configurar los permisos de Azure](tutorial-prepare-hyper-v.md#prepare-azure) para Azure Migrate.
    - [Preparar clústeres, hosts y máquinas virtuales de Hyper-V](tutorial-prepare-hyper-v.md#prepare-for-hyper-v-assessment) para su evaluación.

## <a name="set-up-an-azure-migrate-project"></a>Configuración de un proyecto de Azure Migrate

1. En Azure Portal > **Todos los servicios**, busque **Azure Migrate**.
2. En los resultados de la búsqueda, seleccione **Azure Migrate**.
3. En **Información general**, en **Detectar, evaluar y migrar servidores**, haga clic en **Evaluar y migrar servidores**.

    ![Detectar y evaluar servidores](./media/tutorial-assess-hyper-v/assess-migrate.png)

4. En **Introducción**, haga clic en **Agregar herramientas**.
5. En la pestaña **Migrar proyecto**, seleccione la suscripción a Azure y cree un grupo de recursos si no lo tiene.
6. En **Detalles del proyecto**, especifique el nombre del proyecto y la región en la que desea crearlo.


    ![Creación de un proyecto de Azure Migrate](./media/tutorial-assess-hyper-v/migrate-project.png)

    Puede crear un proyecto de Azure Migrate en estas regiones.

    **Geografía** | **Región**
    --- | ---
    Asia  | Sudeste asiático
    Europa | Norte de Europa y Oeste de Europa
    Reino Unido |  Sur de Reino Unido u Oeste de Reino Unido
    Estados Unidos | Este de EE. UU., Oeste de EE. UU. 2 o Centro-oeste de EE. UU.

    - La región del proyecto solo se utiliza para almacenar los metadatos que se recopilan de las máquinas virtuales locales.
    - Puede seleccionar una región de destino de Azure diferente cuando migre las máquinas virtuales. Se admiten todas las regiones de Azure como destino de la migración.

7. Haga clic en **Next**.
8. En **Seleccione una herramienta de evaluación**, seleccione **Azure Migrate: Server Assessment** > **Siguiente**.

    ![Crear un proyecto de Azure Migrate](./media/tutorial-assess-hyper-v/assessment-tool.png)

9. En **Seleccione una herramienta de migración**, seleccione **Omitir por ahora la adición de una herramienta de migración** > **Siguiente**.
10. En **Revisar y agregar herramientas**, revise la configuración y haga clic en **Agregar herramientas**.
11. Espere unos minutos para que se implemente el proyecto de Azure Migrate. Se le dirigirá a la página del proyecto. Si no ve el proyecto, puede acceder a él desde **Servidores** en el panel de Azure Migrate.




## <a name="set-up-the-appliance-vm"></a>Configuración de la máquina virtual del dispositivo

Azure Migrate Server Assessment ejecuta un dispositivo ligero de máquina virtual de Hyper-V.

- Este dispositivo realiza detección de máquinas virtuales y envía metadatos y datos de rendimiento de estas a Azure Migrate: Server Assessment.
- Para configurar el dispositivo:
    - Descargue un disco duro virtual de Hyper-V comprimido desde Azure Portal.
    - Cree el dispositivo y compruebe que se puede conectar a Azure Migrate Server Assessment.
    - Configure el dispositivo por primera vez y regístrelo con el proyecto de Azure Migrate.

### <a name="download-the-vhd"></a>Descarga del disco duro virtual

Descargue la plantilla del disco duro virtual comprimido del dispositivo.

1. En **Objetivos de migración** > **Servidores** > **Azure Migrate: Server Assessment**, haga clic en **Detectar**.
2. En **Detectar máquinas** >  **¿Las máquinas están virtualizadas?** , haga clic en **Sí, con Hyper-V**.
3. Haga clic en **Descargar** para descargar el archivo del disco duro virtual.

    ![Descargar máquina virtual](./media/tutorial-assess-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo comprimido es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.

2. Ejecute el siguiente comando de PowerShell para generar el código hash para el archivo ZIP
    - ```C:\>Get-FileHash -Path <file_location> -Algorithm [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>Get-FileHash -Path ./AzureMigrateAppliance_v1.19.06.27.zip -Algorithm SHA256```

3.  El código hash generado debe coincidir con esta configuración para la versión 1.19.06.27 del dispositivo.

  **Algoritmo** | **Valor del código hash**
  --- | ---
  MD5 | 3681F745FA2B0A0A6910707D85161EC5
  SHA256 | E6CA109AFAB9657BDCFB291C343B3E3ABCED9A273D25273059171F9954D25832



### <a name="create-the-appliance-vm"></a>Creación de la máquina virtual del dispositivo

Importe el archivo descargado y cree la máquina virtual.

1. Extraiga el archivo del disco duro virtual comprimido en la carpeta del host de Hyper-V que hospedará la máquina virtual del dispositivo. Se extraen tres carpetas.
2. Abra el administrador de Hyper-V. En **Acciones**, haga clic en **Importar máquina virtual**.

    ![Implementar disco duro virtual](./media/tutorial-assess-hyper-v/deploy-vhd.png)

2. En el Asistente para importar máquinas virtuales > **Antes de comenzar**, haga clic en **Siguiente**.
3. En **Buscar carpeta**, especifique la carpeta que contiene el disco duro virtual extraído. A continuación, haga clic en **Siguiente**.
1. En **Seleccionar máquina virtual**, haga clic en **Siguiente**.
2. En **Elegir tipo de importación**, haga clic en **Copiar la máquina virtual (crear un identificador único nuevo)** . A continuación, haga clic en **Siguiente**.
3. En **Elegir destino**, deje la configuración predeterminada. Haga clic en **Next**.
4. En **Carpetas de almacenamiento**, deje la configuración predeterminada. Haga clic en **Next**.
5. En **Elegir red**, especifique el conmutador virtual que usará la máquina virtual. El conmutador necesita conectividad a Internet para enviar datos a Azure.
6. En **Resumen**, revise los valores de configuración. Haga clic en **Finalizar**.
7. En Administrador de Hyper-V > **Máquinas virtuales**, inicie la máquina virtual.


### <a name="verify-appliance-access-to-azure"></a>Comprobación de que el dispositivo puede acceder a Azure

Asegúrese de que la máquina virtual del dispositivo se puede conectar a las [direcciones URL de Azure](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access).

### <a name="configure-the-appliance"></a>Configuración del dispositivo

Configure el dispositivo por primera vez.

1. En Administrador de Hyper-V > **Máquinas virtuales**, haga clic con el botón derecho en la máquina virtual > **Conectar**.
2. Especifique el idioma, la zona horaria y la contraseña del dispositivo.
3. Abra un explorador en cualquier equipo que pueda conectarse a la máquina virtual y abra la dirección URL de la aplicación web del dispositivo: **https://*nombre o dirección IP del dispositivo*: 44368**.

   Como alternativa, puede abrir la aplicación desde el escritorio del dispositivo, para lo que debe hacer clic en el acceso directo de la aplicación.
1. En la aplicación web > **Set up prerequisites** (Configurar los requisitos previos ), realice las siguientes operaciones:
    - **License** (Licencia): Acepte los términos de licencia y lea la información de terceros.
    - **Connectivity** (Conectividad): la aplicación comprueba que la máquina virtual tiene acceso a Internet. Si la máquina virtual usa un proxy:
      - Haga clic en **Configuración de proxy** y especifique el puerto de escucha y la dirección del proxy con los formatos http://ProxyIPAddress o http://ProxyFQDN.
      - Especifique las credenciales si el proxy requiere autenticación.
      - Solo se admite un proxy HTTP.
    - **Time sync** (Sincronización de hora): Se comprueba la hora. Para que la detección funcione correctamente, la hora del dispositivo debe estar sincronizada con la hora de Internet.
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

Si va a ejecutar discos duros virtuales en SMB, debe habilitar la delegación de credenciales desde el dispositivo a los hosts de Hyper-V. Esto requiere lo siguiente:

- Permita que cada host actúe como delegado para el dispositivo. Esto debió de hacerlo en el tutorial anterior, cuando preparó Hyper-V para la evaluación y la migración. Debería haber configurado CredSSP para los hosts [manualmente](tutorial-prepare-hyper-v.md#enable-credssp-on-hosts) o mediante[ la ejecución del script de configuración de requisitos previos de Hyper-V](tutorial-prepare-hyper-v.md#hyper-v-prerequisites-configuration-script).
- Habilite la delegación CredSSP para que el dispositivo de Azure Migrate pueda actuar como cliente y delegar las credenciales en un host.

Habilite en el dispositivo de la manera siguiente:

#### <a name="option-1"></a>Opción 1

En la máquina virtual del dispositivo, ejecute este comando. HyperVHost1 y HyperVHost2 son nombres de host de ejemplo.

```
Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force
```

Ejemplo: ` Enable-WSManCredSSP -Role Client -DelegateComputer HyperVHost1.contoso.com HyperVHost2.contoso.com -Force `

#### <a name="option-2"></a>Opción 2

También puede hacerlo en el Editor de directivas de grupo local en el dispositivo:

1. En **Directiva de equipo local** > **Configuración del equipo**, haga clic en **Plantillas administrativas** > **Sistema** > **Delegación de credenciales**.
2. Haga doble clic en **Permitir delegación de credenciales nuevas** y seleccione **Habilitado**.
3. En **Opciones**, haga clic en **Mostrar** y agregue cada host de Hyper-V que desee detectar en la lista, con **wsman/** como prefijo.
4. En **Delegación de credenciales**, haga doble clic en **Permitir la delegación de credenciales nuevas con autenticación solo NTLM de servidor**. De nuevo, agregue cada host de Hyper-V que desee detectar en la lista, con **wsman/** como prefijo.

## <a name="start-continuous-discovery"></a>Inicio de detección continua

Conéctese desde el dispositivo a los hosts o clústeres de Hyper-V e inicie la detección de máquinas virtuales.

1. En **Nombre de usuario** y **Contraseña**, especifique las credenciales de la cuenta que el dispositivo utilizará para detectar las máquinas virtuales. Especifique un nombre descriptivo para las credenciales y haga clic en **Guardar los detalles**.
2. Haga clic en **Agregar host** y especifique los detalles del host o clúster de Hyper-V.
3. Haga clic en **Validar**. Después de la validación, se muestra el número de máquinas virtuales que se pueden detectar en cada host o clúster.
    - Si se produce un error de validación para un host, revise el error manteniendo el puntero sobre el icono en la columna **Estado**. Corrija los problemas y vuelva a validar.
    - Para quitar hosts o clústeres, seleccione > **Eliminar**.
    - No se puede quitar un host específico de un clúster. Solo puede quitar todo el clúster.
    - Puede agregar un clúster, incluso si hay problemas con hosts específicos del clúster.
4. Después de la validación, haga clic en **Guardar e iniciar la detección** para iniciar el proceso de detección.

De esta forma comienza la detección. Los metadatos de las máquinas virtuales detectadas tardan unos 15 minutos en aparecer en Azure Portal.

### <a name="verify-vms-in-the-portal"></a>Comprobación de VM en el portal

Una vez finalizada la detección, puede comprobar que las máquinas virtuales aparecen en el portal.

1. Abra el panel de Azure Migrate.
2. En la página **Azure Migrate - Servidores** > **Azure Migrate: Server Assessment**, haga clic en el icono que muestra el número de **servidores detectados**.

## <a name="set-up-an-assessment"></a>Configuración de una evaluación

Se pueden ejecutar dos tipos de evaluaciones mediante Azure Migrate Server Assessment.

**Valoración** | **Detalles** | **Datos**
--- | --- | ---
**Basada en el rendimiento** | Evaluaciones basadas en los datos de rendimiento recopilados | **Tamaño de máquina virtual recomendado**: se basa en los datos de uso de la CPU y de la memoria.<br/><br/> **Tipo de disco recomendado (disco administrado estándar o Premium**): se basa en IOPS y en el rendimiento de los discos locales.
**Como local** | Evaluaciones que se basan en el tamaño local. | **Tamaño de máquina virtual recomendado**: se basa en el tamaño de la máquina virtual local<br/><br> **Tipo de disco recomendado**: se basa en el valor del tipo de almacenamiento que se selecciona para la evaluación.



### <a name="run-an-assessment"></a>Ejecución de una evaluación

Las evaluaciones se realizan como se indica a continuación:

1. Consulte los [procedimientos recomendados](best-practices-assessment.md) para crear evaluaciones.
2. En **Servidores** > **Azure Migrate: Server Assessment**, haga clic en **Evaluar**.

    ![Evaluación](./media/tutorial-assess-hyper-v/assess.png)

3. En **Evaluar los servidores**, especifique el nombre de la evaluación.
4. Haga clic en **View all** (Ver todo) para revisar la configuración de la valoración.

    ![Propiedades de la evaluación](./media/tutorial-assess-hyper-v/assessment-properties.png)

3. En **Seleccionar o crear un grupo**, seleccione **Crear nuevo** y especifique un nombre de grupo. Un grupo recopila una o varias máquinas virtuales para su evaluación.
4. En **Agregar máquinas al grupo**, seleccione las máquinas virtuales que se van a agregar al grupo.
5. Haga clic en **Crear evaluación** para crear el grupo y realizar la evaluación.

    ![Crear una evaluación](./media/tutorial-assess-hyper-v/assessment-create.png)

6. Una vez creada la evaluación, se puede ver en **Servidores** > **Azure Migrate: Server Assessment**.
7. Haga clic en **Exportar la evaluación** para descargarla como un archivo de Excel.


## <a name="review-an-assessment"></a>Revisión de una evaluación

Una evaluación describe:

- **Preparación para Azure**: si las máquinas virtuales son adecuadas para la migración a Azure.
- **Estimación del costo mensual**: los costos mensuales estimados de proceso y almacenamiento por ejecutar las máquinas virtuales en Azure.
- **Estimación del costo mensual de almacenamiento**: costos estimados del almacenamiento en disco después de la migración.


### <a name="view-an-assessment"></a>Visualización de una evaluación

1. En **Objetivos de migración** >  **Servidores** > **Azure Migrate: Server Assessment**, haga clic en **Evaluar**.
2. En **Evaluaciones**, haga clic en una evaluación para abrirla.

    ![Resumen de evaluaciones](./media/tutorial-assess-hyper-v/assessment-summary.png)


### <a name="review-azure-readiness"></a>Revisión de la preparación para Azure

1. En **Preparación para Azure**, compruebe si las máquinas virtuales están listas para su migración a Azure.
2. Revise el estado de la máquina virtual:
    - **Preparada para Azure**: Azure Migrate recomienda unas estimaciones de tamaño y costo de las máquinas virtuales en la evaluación.
    - **Preparado con condiciones**: muestra los problemas y las soluciones que se sugieren.
    - **No está preparado para Azure**: muestra los problemas y las soluciones que se sugieren.
    - **Preparación desconocida**: se usa cuando Azure Migrate no puede evaluar la preparación debido a problemas de disponibilidad de datos.

2. Haga clic en cualquiera de los estados de **Preparación para Azure**. Puede ver los detalles de la preparación de la máquina virtual y explorar en profundidad los detalles de esta, entre los que se incluye la configuración de proceso, almacenamiento y red.

### <a name="review-cost-details"></a>Revisión de los datos de costo

Esta vista muestra el costo estimado de almacenamiento y proceso que conlleva ejecutar máquinas virtuales en Azure.

1. Revise los costos mensuales de proceso y almacenamiento. Los costos se agregan para todas las máquinas virtuales del grupo evaluado.

    - Las estimaciones de costo se basan en las recomendaciones de tamaño de una máquina, así como en sus discos y propiedades.
    - Se muestran los costos mensuales estimados de proceso y almacenamiento.
    - La estimación de costos es para ejecutar las máquinas virtuales locales como máquinas virtuales IaaS. Azure Migrate Server Assessment no tiene en cuenta los costos de PaaS o SaaS.

2. Puede revisar las estimaciones del costo de almacenamiento mensual. Esta vista muestra los costos de almacenamiento agregados del grupo evaluado, divididos por los diferentes tipos de discos de almacenamiento.
3. Puede explorar en profundidad para ver los detalles de cada una de las máquinas virtuales.


### <a name="review-confidence-rating"></a>Examen de la clasificación de confianza

Cuando se realizan valoraciones basadas en el rendimiento, se asigna una clasificación de confianza a la evaluación.

![Clasificación de confianza](./media/tutorial-assess-hyper-v/confidence-rating.png)

- Se concede una clasificación que oscila entre 1 estrella (la más baja) y 5 estrellas (la más alta).
- La clasificación de confianza sirve de ayuda para calcular la confiabilidad de las recomendaciones de tamaño que proporciona la evaluación.
- La clasificación de confianza se basa en la disponibilidad de los puntos de datos necesarios para calcular tal evaluación.

Estas son las posibles clasificaciones de confianza de una evaluación.

**Disponibilidad del punto de datos** | **Clasificación de confianza**
--- | ---
0 % - 20 % | 1 estrella
21 % - 40 % | 2 estrellas
41 % - 60 % | 3 estrellas
61 % - 80 % | 4 estrellas
81 % - 100 % | 5 estrellas

[Más información](best-practices-assessment.md#best-practices-for-confidence-ratings) acerca de los procedimientos recomendados para las clasificaciones de confianza.





## <a name="next-steps"></a>Pasos siguientes

En este tutorial, hizo lo siguiente:

> [!div class="checklist"]
> * Configurar un dispositivo de Azure Migrate
> * Crear y revisar una evaluación

Vaya al tercer tutorial de la serie, donde aprenderá a migrar máquinas virtuales de Hyper-V a Azure con Azure Migrate Server Migration.

> [!div class="nextstepaction"]
> [Migración de máquinas virtuales de Hyper-V](./tutorial-migrate-hyper-v.md)
