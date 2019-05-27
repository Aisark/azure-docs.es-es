---
title: Alertas de seguridad por tipo en Azure Security Center | Microsoft Docs
description: En este artículo se describen los distintos tipos de alertas de seguridad disponibles en Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: b3e7b4bc-5ee0-4280-ad78-f49998675af1
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2018
ms.author: v-mohabe
ms.openlocfilehash: 4592caacf7f73e4bce9f974fb3bb2ab3ed1a89ff
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968363"
---
# <a name="understanding-security-alerts-in-azure-security-center"></a>Comprensión de las alertas de seguridad en Azure Security Center
Este artículo ayuda a comprender los distintos tipos de alertas de seguridad y aspectos relacionados disponibles en Azure Security Center. Para más información sobre cómo administrar las alertas y los incidentes, consulte [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md).

Para configurar las detecciones avanzadas, actualice a la versión estándar de Azure Security Center. Está disponible una evaluación gratuita. Para actualizar a este nivel, seleccione el **plan de tarifa** en la [directiva de seguridad](tutorial-security-policy.md). Para más información, consulte la [página de precios](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="what-type-of-alerts-are-available"></a>¿Qué tipo de alertas está disponible?
Azure Security Center usa una variedad de [funcionalidades de detección](security-center-detection-capabilities.md) para alertar a los clientes de posibles ataques contra sus entornos. Estas alertas contienen información útil acerca de lo que desencadenó la alerta, los recursos objetivo y el origen del ataque. La información incluida en las alertas varía según el tipo de análisis utilizado para detectar la amenaza. Los incidentes también pueden contener información contextual adicional que puede resultar útil para la investigación de la amenaza.  En este artículo se proporciona información acerca de los siguientes tipos de alertas:

* Análisis del comportamiento de la máquina virtual (VMBA)
* Análisis de red
* Análisis de SQL Database y SQL Data Warehouse
* Información contextual

## <a name="virtual-machine-behavioral-analysis"></a>Análisis del comportamiento de la máquina virtual
Azure Security Center puede utilizar el análisis de comportamiento para identificar los recursos en peligro a partir del análisis de registros de eventos de la máquina virtual. Por ejemplo, eventos de creación de proceso y eventos de inicio de sesión. Además, existe una correlación con otras señales que se comprueban para fundamentar las pruebas de que se trata una campaña de gran difusión.

> [!NOTE]
> Para más información acerca de cómo actúan las funcionalidades de detección de Security Center, consulte [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md).


### <a name="event-analysis"></a>Análisis de eventos
Security Center utiliza análisis avanzado para identificar los recursos en peligro a partir del análisis de los registros de eventos de la máquina virtual. Por ejemplo, eventos de creación de proceso y eventos de inicio de sesión. Además, existe una correlación con otras señales que se comprueban para fundamentar las pruebas de que se trata una campaña de gran difusión.

* **Ejecución de proceso sospechoso detectado**: a menudo, los atacantes intentan ejecutar código malintencionado sin que se detecte simulando ser procesos benignos. Estas alertas indican que la ejecución de un proceso coincide con uno de los patrones siguientes:
    * Se ha ejecutado un proceso conocido por su uso con fines malintencionados. Aunque los comandos individuales pueden parecer benignos, la alerta se califica según una agregación de estos comandos.
    * Se ha ejecutado un proceso desde una ubicación poco habitual.
    * Se ha ejecutado un proceso desde una ubicación en común con archivos sospechosos conocidos.
    * Se ha ejecutado un proceso desde una ruta de acceso sospechosa.
    * Se ha ejecutado un proceso en un contexto anómalo.
    * Se ha ejecutado un proceso con una cuenta inusual.
    * Se ha ejecutado un proceso con una extensión sospechosa.
    * Se ha ejecutado un proceso con una doble extensión sospechosa.
    * Se ha ejecutado un proceso con un carácter de derecha a izquierda (RLO) sospechoso en el nombre de archivo.
    * Se ha ejecutado un proceso cuyo nombre es diferente pero similar al de un proceso de ejecución común.
    * Se ha ejecutado un proceso cuyo nombre se corresponde a una herramienta conocida utilizada por atacantes.
    * Se ha ejecutado un proceso con un nombre aleatorio.
    * Se ha ejecutado un proceso con una extensión sospechosa.
    * Se ha ejecutado un archivo oculto.
    * Se ha ejecutado un proceso como proceso secundario de otro proceso no relacionado.
    * Un proceso del sistema ha creado un proceso inusual.
    * El servicio Windows Update ha iniciado un proceso anómalo.
    * Se ha ejecutado un proceso con una línea de comandos inusual. Esto se ha asociado con procesos legítimos secuestrados para la ejecución de contenido malintencionado.
    * Se ha ejecutado desde la línea de comandos un intento de iniciar todos los archivos ejecutables (*.exe) de un directorio.
    * Se ha ejecutado un proceso con la utilidad PsExec, que se puede usar para ejecutar procesos de forma remota.
    * Se ha utilizado el archivo ejecutable primario de Apache Tomcat® (Tomcat#.exe) para iniciar procesos secundarios sospechosos que pueden hospedar o iniciar comandos malintencionados.
    * Se ha utilizado el "asistente de compatibilidad de programas" (pcalua.exe) de Microsoft Windows para iniciar código ejecutable, que podría ser malintencionado.
    * Se ha detectado una ráfaga de terminación de procesos sospechosa.
    * Se ha ejecutado el proceso del sistema SVCHOST en un contexto anómalo.
    * Se ha ejecutado el proceso del sistema SVCHOST en un grupo de servicio poco frecuente.
    * Se ha ejecutado una línea de comandos sospechosa.
    * Un script de PowerShell tiene características en común con scripts sospechosos conocidos.
    * Se ha ejecutado un cmdlet de PowerShell Powersploit malintencionado conocido.
    * Un usuario integrado de SQL ha ejecutado un proceso que normalmente no ejecutaría.
    * Se ha detectado un ejecutable codificado en base 64, lo que podría indicar que un atacante intenta evitar ser detectado mediante la creación de un ejecutable sobre la marcha mediante una secuencia de comandos.

* **Actividad sospechosa de recursos RDP**: a menudo, los atacantes tienen como objetivo de ataques por fuerza bruta los puertos de administración abiertos, como RDP. Estas alertas indican actividad de inicio de sesión de escritorio remoto sospechosa:
    * Se han intentado inicios de sesión de escritorio remoto.
    * Se han intentado inicios de sesión de escritorio remoto mediante cuentas no válidas.
    * Se han intentado inicios de sesión de escritorio remoto, algunos de los cuales pudieron iniciar una sesión correctamente en la máquina.
* **Actividad sospechosa de recursos SSH**: a menudo, los atacantes tienen como objetivo de ataques por fuerza bruta los puertos de administración abiertos, como SSH. Estas alertas indican actividad de inicio de sesión de SSH sospechosa:
    * Se intentaron inicios de sesión de SSH erróneos.
    * Se intentaron inicios de sesión de SSH, algunos de los cuales fueron correctos.
* **Valor de registro de WindowPosition sospechoso**: esta alerta indica que se intentó un cambio de configuración del registro de WindowPosition, lo que puede indicar que se ha intentado ocultar ventanas de aplicación en secciones no visibles del escritorio.
* **Posible intento de evitar AppLocker**: AppLocker puede utilizarse para limitar los procesos que se pueden ejecutar en Windows, limitando así la exposición a malware. Esta alerta indica un posible intento de evitar las restricciones de AppLocker mediante el uso de ejecutables de confianza (permitidos por la directiva de AppLocker) para ejecutar código no seguro.
* **Comunicaciones de canalización con nombre sospechosas**: esta alerta indica que se han escrito datos en una canalización con nombre local desde un comando de la consola de Windows. Se sabe que los atacantes utilizan canalizaciones con nombre para realizar tareas y comunicarse con un implante malintencionado.
* **Descodificación de un ejecutable mediante la herramienta integrada certutil.exe**: esta alerta indica que se ha utilizado una utilidad de administración integrada, certutil.exe, para descodificar un ejecutable. Se sabe que los atacantes abusan de la funcionalidad de herramientas de administrador legítimas para realizar acciones malintencionadas; por ejemplo, utilizan una herramienta como certutil.exe para descodificar un ejecutable malintencionado que se ejecutará posteriormente.
* **Se ha borrado un registro de eventos**: esta alerta indica una operación de borrado del registro de eventos sospechosa que suele ser utilizada por los atacantes para intentar cubrir sus huellas.
* **Deshabilitación y eliminación de archivos de registro de IIS**: esta alerta indica que el archivo de registro de IIS se ha deshabilitado o eliminado, lo que suele ser utilizado por los atacantes para intentar cubrir sus huellas.
* **Eliminación de archivos sospechosos**: esta alerta indica una eliminación de archivos sospechosa, que un atacante puede utilizar para eliminar la evidencia de archivos binarios malintencionados.
* **Todas las copias shadow de archivos que se han eliminado**: esta alerta indica que se han eliminado copias shadow.
* **Comandos de limpieza de archivos sospechosos**: esta alerta indica una combinación de comandos systeminfo que se usaron para realizar una actividad de autolimpieza después de estar en peligro.  Aunque *systeminfo.exe* es una herramienta legítima de Windows, ejecutarla dos veces consecutivas, seguida de un comando de eliminación en la forma en que se ha producido aquí es poco frecuente.
* **Cuenta sospechosa creada**: esta alerta indica que se ha creado una cuenta con un cierto parecido a una cuenta con privilegios administrativos integrada existente. Los atacantes pueden usar esta técnica para crear una cuenta no autorizada sin ser detectados.
* **Actividad de instantánea de volumen sospechosa**: esta alerta indica una actividad de eliminación de instantánea en el recurso. Instantáneas de volumen (VSC) es un artefacto importante que almacena instantáneas de datos. Esta actividad está asociada con el Ransomware, pero también podría ser legítima.
* **Método de persistencia del Registro de Windows**: esta alerta indica un intento de conservar un archivo ejecutable en el registro de Windows. El malware suele utilizar esta técnica para sobrevivir a un reinicio.
* **Nueva regla de firewall sospechosa**: esta alerta indica que se ha agregado una nueva regla de firewall a través de *netsh.exe* para permitir el tráfico desde un archivo ejecutable de una ubicación sospechosa.
* **Ejecuciones de XCOPY sospechosas**: esta alerta indica una serie de ejecuciones de XCOPY que podría indicar que una de las máquinas se ha puesto en peligro y se ha utilizado para propagar malware.
* **Supresión del aviso legal que se muestra a los usuarios en el inicio de sesión**: esta alerta indica un cambio en la clave del registro que controla si se muestra un aviso legal a los usuarios cuando inician sesión. Se trata de una actividad comúnmente llevada a cabo por los atacantes después de poner en peligro un host.
* **Detectada una combinación anómala de mayúsculas y minúsculas en la línea de comandos**: esta alerta indica el uso de una combinación de mayúsculas y minúsculas en la línea de comandos, que es una técnica utilizada por los atacantes para evitar reglas de la máquina que distinguen mayúsculas de minúsculas o están basadas en un hash.
* **Línea de comandos ofuscada**: esta alerta indica que se han detectado indicadores de ofuscación sospechosos en la línea de comandos.
* **Múltiples cuentas de dominio consultadas**: los atacantes a menudo consultan las cuentas del dominio de AD mientras se realiza el reconocimiento de usuarios, cuentas de administrador de dominio, controladores de dominio y relaciones de confianza entre los dominios. Esta alerta indica que se ejecutaron consultas en un número inusual de cuentas del dominio distintas en un breve período de tiempo.
* **Posible actividad de reconocimiento local**: esta alerta indica que se ha ejecutado una combinación de comandos systeminfo asociada con una actividad de reconocimiento.  Aunque *systeminfo.exe* es una herramienta de Windows legítima, su ejecución dos veces seguidas es poco frecuente.
* **Posible ejecución de ejecutable keygen**: esta alerta indica que se ha ejecutado un proceso cuyo nombre es indicativo de una herramienta keygen. Estas herramientas se utilizan normalmente para anular los mecanismos de licencias de software, pero su descarga a menudo viene incluida con otro malware.
* **Ejecución sospechosa mediante rundll32.exe**: esta alerta indica que se ha utilizado rundll32.exe para ejecutar un proceso con un nombre poco común, de forma coherente con el esquema de nomenclatura de proceso utilizado por los atacantes para instalar un implante de primera fase en un host en peligro.
* **Combinación sospechosa de HTA y PowerShell**: esta alerta indica que un host de aplicación HTML (HTA) de Microsoft está iniciando comandos de PowerShell. Se trata de una técnica utilizada por los atacantes para iniciar scripts malintencionados de PowerShell.
* **Cambiar a una clave del registro que puede ser usada para pasar por alto UAC**: esta alerta indica que se ha cambiado una clave del registro que puede ser usada para pasar por alto UAC (Control de cuentas de usuario), que los atacantes usan a menudo para pasar desde accesos sin privilegios (usuario estándar) a acceso con privilegios (por ejemplo, administrador) en un host en peligro.
* **Uso de nombre de dominio sospechoso en la línea de comandos**: esta alerta indica que se utilizó un nombre de dominio sospechoso, lo que puede ser evidencia de que un atacante hospeda herramientas malintencionadas y como puntos de conexión para comando y control y filtración de datos.
* **Se ha creado una cuenta en varios hosts en un período de 24 horas**: esta alerta indica que se ha intentado crear la misma cuenta de usuario en varios hosts, lo que puede ser evidencia de que un atacante se mueve lateralmente a través de la red después de poner en peligro una o más entidades de red.
* **Uso sospechoso de CACLS para reducir el estado de seguridad del sistema**: esta alerta indica que se cambió la lista de control de acceso de cambios (CACLS). Los atacantes utilizan esta técnica a menudo para conceder acceso total a archivos binarios del sistema como ftp.exe, net.exe, wscript.exe, etcétera.
* **Sospecha de parámetros de ataque Golden Ticket a Kerberos**: esta alerta indica que se ejecutaron parámetros de línea de comandos coherentes con un ataque Golden Ticket a Kerberos. Un atacante puede usar una clave krbtgt en peligro para suplantar a cualquier usuario que desee.
* **Habilitación de la clave del registro UseLogonCredential de WDigest**: esta alerta indica que se ha cambiado la clave del registro para permitir que las credenciales de inicio de sesión se almacenen en texto no cifrado en memoria LSA, desde donde pueden ser recopiladas.
* **Uso potencialmente sospechoso de la herramienta Telegram**: esta alerta indica la instalación de Telegram, un servicio de mensajería instantánea basado en la nube utilizado por los atacantes para transferir archivos binarios malintencionados a cualquier otro equipo, teléfono o tableta.
* **Creación de nuevo ASEP**: esta alerta indica la creación de un nuevo ASEP (punto de extensibilidad de inicio automático), que hace que el nombre del proceso identificado en la línea de comandos se inicie automáticamente y pueda ser utilizado por un atacante para lograr persistencia.
* **Cambios sospechosos en Set-ExecutionPolicy y WinRM**: esta alerta indica cambios de configuración que están asociados con el uso del webshell malintencionado ChinaChopper.
* **Deshabilitación de servicios críticos**: esta alerta indica que se ha utilizado el comando "net.exe stop" para detener servicios críticos como el acceso compartido o Windows Security Center.
* **Uso sospechoso del modificador -s de FTP**: esta alerta indica el uso del modificador FTP "-s", que puede ser utilizado por malware para conectarse a un servidor FTP remoto y descargar archivos binarios malintencionados adicionales.
* **Ejecución sospechosa del comando VBScript.Encode**: esta alerta indica que se ha ejecutado el comando *VBScript.Encode* que codifica los scripts en texto ilegible, lo que dificulta que los usuarios puedan examinar el código.
* **Asignación de objeto HTTP de VBScript**: esta alerta indica la creación de un archivo de VBScript mediante el símbolo del sistema que puede utilizarse para descargar archivos malintencionados.
* **Ataque de teclas especiales**: esta alerta indica que un atacante puede haber alterado un archivo binario de accesibilidad (por ejemplo las teclas especiales, el teclado en pantalla o el narrador) para proporcionar una puerta trasera de acceso.
* **Indicadores de ransomware Petya**: esta alerta indica que se han observado técnicas asociadas con el ransomware Petya.
* **Intento de deshabilitar AMSI**: esta alerta indica un intento de deshabilitar la interfaz de examen de antimalware (AMSI), lo que podría deshabilitar la detección antimalware.
* **Indicadores de ransomware**: esta alerta indica actividad sospechosa tradicionalmente asociada a la pantalla de bloqueo y a ransomware de cifrado.
* **Archivo de salida de recopilación de seguimiento sospechoso**: esta alerta indica que se ha recopilado un seguimiento (por ejemplo de actividad de red) y se ha enviado la salida a un tipo de archivo inusual.
* **Software de alto riesgo**: esta alerta indica el uso de software asociado con la instalación de malware. Los atacantes a menudo empaquetan el malware con herramientas de otro modo benignas, como la que se ve en esta alerta, e instalan de forma silenciosa el malware en segundo plano.
* **Creación de archivo sospechoso**: esta alerta indica la creación o la ejecución de un proceso usado por los atacantes para descargar malware adicional en un host en peligro después de abrir un archivo adjunto en un documento de suplantación de identidad.
* **Credenciales sospechosas en la línea de comandos**: esta alerta indica que se ha utilizado una contraseña sospechosa para ejecutar un archivo. Los atacantes han utilizado esta técnica para ejecutar el malware Pirpi.
* **Posible ejecución de un instalador de malware**: esta alerta indica un nombre de archivo que los atacantes han utilizado para instalar malware.
* **Ejecución sospechosa mediante rundll32.exe**: esta alerta indica se ha utilizado rundll32.exe para ejecutar un archivo notepad.exe o reg.exe, coherente con la técnica de inyección de proceso utilizada por los atacantes.
* **Argumentos de línea de comandos sospechosos**: esta alerta indica que se han usado argumentos de línea de comandos sospechosos junto con un shell inverso utilizado por el grupo de actividad HYDROGEN.
* **Credenciales de documento sospechosas**: esta alerta indica que el malware ha utilizado un hash de contraseña precalculado común sospechoso para ejecutar un archivo.
* **Construcción dinámica de script de PS**: esta alerta indica que se ha construido dinámicamente un script de PowerShell. Los atacantes usan esta técnica para generar progresivamente un script con el fin de eludir los sistemas IDS.
* **Indicadores de Metasploit**: esta alerta indica actividad asociada con el marco de trabajo Metasploit, que proporciona diversas herramientas y funcionalidades al atacante.
* **Actividad de cuentas sospechosa**: esta alerta indica un intento de conexión a una máquina con una cuenta que se ha puesto en peligro recientemente.
* **Creación de cuenta**: esta alerta indica la creación de una cuenta nueva en el equipo.


### <a name="crash-analysis"></a>Análisis de bloqueos


El análisis de la memoria de volcado de bloqueos es un método utilizado para detectar malware sofisticado que puede eludir las soluciones de seguridad tradicionales. Diversos tipos de malware intentan reducir la posibilidad de ser detectados por productos antivirus no escribiendo nunca en el disco o mediante el cifrado de los componentes de software escritos en el disco. Esta técnica hace que el malware sea difícil de detectar mediante los enfoques tradicionales de antimalware. Sin embargo, este tipo de malware se puede detectar mediante un análisis de memoria, ya que, para poder funcionar, tiene que dejar rastros en la memoria.

En el momento en que el software se bloquea, un volcado de memoria captura una porción de la memoria. El bloqueo puede deberse a malware o a problemas de aplicaciones generales o del sistema. Mediante el análisis de la memoria durante el volcado, Security Center puede detectar las técnicas utilizadas para aprovechar las vulnerabilidades del software, acceder a información confidencial y permanecer subrepticiamente en una máquina en peligro. Esto se consigue con un impacto mínimo en el rendimiento de los hosts, ya que el análisis se realiza en el back-end de Security Center.

* **Inyección de código detectada**: La inserción de código es la inserción de módulos ejecutables en subprocesos o procesos de ejecución. Esta técnica se utiliza por malware para tener acceso a datos, ocultar o impedir su eliminación (por ejemplo, persistencia). Esta alerta indica que un módulo insertado está presente en el volcado de memoria. Los desarrolladores de software legítimo realizan cada cierto tiempo una inserción de código por razones no malintencionadas, como modificar o extender una aplicación existente o un componente de sistema operativo. Para ayudar a diferenciar entre módulos insertados malintencionados y no malintencionados, Security Center comprueba si el módulo insertado se ajusta a un perfil de comportamiento sospechoso. El resultado de esta comprobación se indica en el campo "SIGNATURE" (FIRMA) de la alerta y se refleja en la gravedad de la alerta, su descripción y los pasos de corrección.
* **Segmento de código sospechoso**: esta alerta indica que se ha asignado un segmento de código con métodos no estándar, como los usados por el vaciado de proceso y la inserción reflexiva. Se procesan características adicionales del segmento de código para proporcionar un contexto en cuanto a las funcionalidades y los comportamientos del segmento de código del que se ha informado.
* **Shellcode detectado**: Shellcode es la carga que se ejecuta después de que un malware haya aprovechado una vulnerabilidad en el software. Esta alerta indica que el análisis del volcado de memoria ha detectado código ejecutable que muestra un comportamiento normalmente realizado mediante cargas malintencionadas. Aunque el software no malintencionado puede tener este comportamiento, no es típico en las prácticas de desarrollo de software normal.
* **Secuestro de módulo detectado**: Windows usa las bibliotecas de vínculos dinámicos (DLL) para que el software pueda utilizar la funcionalidad del sistema Windows común. El secuestro de DLL se produce cuando el malware cambia el orden de carga de las DLL para realizar cargas malintencionadas en la memoria, donde se puede ejecutar código arbitrario. Esta alerta indica que el análisis de volcado de memoria ha detectado un módulo con el mismo nombre que se carga desde dos rutas de acceso diferentes. Una de las rutas de acceso de carga procede de una ubicación binaria común del sistema de Windows. Los desarrolladores de software legítimo ocasionalmente cambian el orden de carga DLL por razones no malintencionadas, como instrumentar, extender el sistema operativo Windows o aplicaciones de Windows. Para ayudar a diferenciar entre los cambios malintencionados y potencialmente benignos en el orden de carga del archivo DLL, Security Center comprueba si un módulo cargado se ajusta a un perfil sospechoso.
* **Módulo de Windows enmascarado detectado**: El malware puede usar nombres comunes de archivos binarios (por ejemplo, SVCHOST.EXE) o de módulos (por ejemplo, NTDLL.DLL) del sistema Windows para mezclarse y ocultar la naturaleza del software malintencionado a los administradores de sistemas. Esta alerta indica que el archivo del volcado de memoria contiene módulos que utilizan nombres de módulo del sistema Windows, pero no cumplen otros criterios típicos de los módulos de Windows. El análisis de la copia en disco del módulo enmascarado puede proporcionar más información acerca de la naturaleza legítima o malintencionada de este módulo.
* **Archivos binarios del sistema modificados detectados**: El malware puede modificar archivos binarios del sistema básicos para un acceso clandestino a los datos o persistir clandestinamente en un sistema en peligro. Esta alerta indica que el análisis del volcado de memoria ha detectado que se han modificado archivos binarios básicos del sistema operativo Windows en la memoria o el disco. Los desarrolladores de software legítimo modifican ocasionalmente módulos del sistema en la memoria por razones no malintencionados, como desvíos o compatibilidad de aplicaciones. Para ayudar a diferenciar entre módulos malintencionados y potencialmente legítimos, Security Center comprueba si el módulo modificado se ajusta a un perfil sospechoso.

## <a name="network-analysis"></a>Análisis de red
La detección de amenazas de Security Center recopila automáticamente información de seguridad de su tráfico (exportación de la información de flujo del protocolo de Internet) de IPFIX de Azure. Después, analiza estos datos (a menudo, relacionando la información de diferentes orígenes) para identificar las amenazas.

* **Posibles intentos de ataque por fuerza bruta de SQL entrante**: el análisis del tráfico ha detectado comunicación SQL entrante sospechosa. Esta actividad es coherente con los intentos de ataque por fuerza bruta contra servidores SQL.
* **Actividad de red RDP entrante sospechosa procedente de varios orígenes**: el análisis del tráfico ha detectado comunicación del Protocolo de escritorio remoto (RDP) entrante anómala procedente de varios orígenes. En concreto, los datos de red de la muestra indican direcciones IP únicas conectadas a su equipo, lo que se considera anómalo para este entorno. Esta actividad puede indicar un intento de ataque por fuerza bruta al punto de conexión RDP desde varios hosts (Botnet).
* **Actividad de red RDP entrante sospechosa**: el análisis del tráfico ha detectado comunicación del Protocolo de escritorio remoto (RDP) entrante anómala. En concreto, los datos de red de la muestra indican un alto número de conexiones entrantes a su equipo, lo que se considera anómalo para este entorno. Esta actividad puede indicar un intento de ataque por fuerza bruta al punto de conexión RDP.
* **Actividad de red RDP saliente sospechosa hacia varios destinos**: el análisis del tráfico ha detectado una comunicación del Protocolo de escritorio remoto (RDP) saliente anómala hacia varios destinos. Esta actividad puede indicar que el equipo está en peligro y ahora se utiliza para ataques por fuerza bruta contra puntos de conexión de RDP externos. Tenga en cuenta que este tipo de actividad puede dar lugar a que entidades externas marquen su dirección IP como malintencionada.
* **Actividad de red RDP saliente sospechosa**: el análisis del tráfico ha detectado una comunicación del Protocolo de escritorio remoto (RDP) saliente anómala. En concreto, los datos de red de la muestra indican un alto número de conexiones salientes desde su equipo, lo que se considera anómalo para este entorno. Esta actividad puede indicar que el equipo está en peligro y ahora se utiliza para ataques por fuerza bruta contra puntos de conexión de RDP externos. Tenga en cuenta que este tipo de actividad puede dar lugar a que entidades externas marquen su dirección IP como malintencionada.
* **Actividad de red SSH entrante sospechosa**: el análisis del tráfico ha detectado una comunicación de SSH entrante anómala. En concreto, los datos de red de la muestra indican un alto número de conexiones entrantes a su equipo, lo que se considera anómalo para este entorno. Esta actividad puede indicar un intento de ataque por fuerza bruta al punto de conexión de SSH.
* **Actividad de red SSH entrante sospechosa procedente de varios orígenes**: el análisis del tráfico de red ha detectado una comunicación de SSH entrante anómala. En concreto, los datos de red de la muestra indican direcciones IP únicas conectadas a su equipo, lo que se considera anómalo para este entorno. Esta actividad puede indicar un intento de ataque por fuerza bruta al punto de conexión de SSH desde varios hosts (Botnet).
* **Actividad de red de SSH saliente sospechosa**: el análisis del tráfico de red ha detectado una comunicación de SSH saliente anómala. En concreto, los datos de red de la muestra indican un alto número de conexiones salientes desde su equipo, lo que se considera anómalo para este entorno. Esta actividad puede indicar que el equipo está en peligro y ahora se utiliza para ataques por fuerza bruta contra puntos de conexión de SSH externos. Tenga en cuenta que este tipo de actividad puede dar lugar a que entidades externas marquen su dirección IP como malintencionada.
* **Actividad de red de SSH saliente sospechosa hacia varios destinos**: el análisis del tráfico de red ha detectado una comunicación de SSH saliente anómala hacia varios destinos. En concreto, los datos de red de la muestra indican conexiones de su equipo a direcciones IP únicas, lo que se considera anómalo para este entorno. Esta actividad puede indicar que el equipo está en peligro y ahora se utiliza para ataques por fuerza bruta contra puntos de conexión de SSH externos. Tenga en cuenta que este tipo de actividad puede dar lugar a que entidades externas marquen su dirección IP como malintencionada.
* **Detectada comunicación de red con un equipo malintencionado**: el análisis del tráfico de red indica que el equipo se ha comunicado con lo que posiblemente es un centro de comando y control.
* **Detectado posible equipo en peligro**: el análisis del tráfico ha detectado actividad saliente, lo que puede indicar que está actuando como parte de una red de robots (botnet). El análisis se basa en direcciones IP a las que ha tenido acceso el recurso junto con los registros DNS públicos.


## <a name="sql-database-and-sql-data-warehouse-analysis"></a>Análisis de SQL Database y SQL Data Warehouse

El análisis de los recursos de Security Center se centra en los servicios de la plataforma como servicio (PaaS), como la integración con [Detección de amenazas de Azure SQL Database](../sql-database/sql-database-threat-detection.md) y Azure SQL Data Warehouse. Detección de amenazas de SQL detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de acceder a bases de datos y desencadenadores o de aprovechar sus vulnerabilidades:

* **Vulnerabilidad ante ataques por inyección de código SQL**: esta alerta se desencadena cuando una aplicación genera una instrucción SQL errónea en la base de datos. Esto puede indicar una posible vulnerabilidad ante ataques por inyección de código SQL. Hay dos posibles razones para la generación de una instrucción errónea:
    * Existe un defecto en el código de la aplicación que crea la instrucción SQL errónea
    * El código de la aplicación o los procedimientos almacenados no corrigen los datos que proporciona el usuario al construir la instrucción SQL errónea, lo que se puede aprovechar para ataques por inyección de código SQL
* **Potencial inyección de código SQL**: esta alerta se desencadena cuando se produce una vulnerabilidad de seguridad activa contra una vulnerabilidad de la aplicación identificada ante inyección de código SQL. Esto significa que el atacante intentan inyectar instrucciones SQL malintencionadas mediante el código de la aplicación vulnerable o procedimientos almacenados.
* **Acceso desde una ubicación inusual**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a SQL Server, donde alguien ha iniciado sesión en el servidor SQL Server desde una ubicación geográfica inusual. En algunos casos, la alerta detecta una acción legítima (una nueva aplicación o el mantenimiento de un desarrollador). En otros casos, la alerta detecta una acción malintencionada (por ejemplo, un antiguo empleado o un atacante externo).
* **Acceso desde un centro de datos de Azure inusual**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a SQL Server, donde alguien ha iniciado sesión en el servidor SQL Server desde un centro de datos de Azure inusual que se vio en el servidor recientemente. En algunos casos, la alerta detecta una acción legítima (una aplicación nueva en Azure, Power BI o Azure SQL Query Editor). En otros casos, la alerta detecta una acción malintencionada procedente de un recurso o servicio de Azure (por ejemplo, un antiguo empleado o un atacante externo).
* **Acceso desde una entidad de seguridad desconocida**: esta alerta se desencadena cuando se produce un cambio en el patrón de acceso a SQL Server, donde alguien ha iniciado sesión en el servidor SQL Server mediante una entidad de seguridad inusual (usuario de SQL). En algunos casos, la alerta detecta una acción legítima (una nueva aplicación o el mantenimiento de un desarrollador). En otros casos, la alerta detecta una acción malintencionada (por ejemplo, un antiguo empleado o un atacante externo).
* **Acceso desde una aplicación potencialmente dañina**: esta alerta se desencadena cuando una aplicación potencialmente dañina se utiliza para tener acceso a la base de datos. En algunos casos, la alerta detecta la realización de pruebas de seguridad. En otros casos, la alerta detecta un ataque que se realiza con herramientas de ataque comunes.
* **Credenciales de SQL por fuerza bruta**: esta alerta se desencadena cuando hay un número anormalmente elevado de inicios de sesión infructuosos con distintas credenciales. En algunos casos, la alerta detecta la realización de pruebas de seguridad. En otros casos, la alerta detecta ataques por fuerza bruta.

## <a name="contextual-information"></a>Información contextual
Durante una investigación, los analistas necesitan más contexto para llegar a un veredicto sobre la naturaleza de la amenaza y cómo mitigarla.  Por ejemplo, se detecta una anomalía de red, pero sin saber qué más sucede en la red o con respecto al recurso objetivo, es muy difícil conocer las medidas que se deben tomar a continuación. Para ayudar a los investigadores con esto, los incidentes de seguridad pueden incluir artefactos, eventos e información relacionada. La disponibilidad de información adicional variará según el tipo de amenaza detectada y la configuración de su entorno y no estará disponible para todos los incidentes de seguridad.

Si hay disponible información adicional, se mostrará en el incidente de seguridad por debajo de la lista de alertas. Podría contener datos como:

- Eventos de registro borrados
- Dispositivos Plug and Play desconocidos
- Alertas que no se pueden activar
- Creación de una cuenta nueva
- Archivo descodificado mediante la herramienta CertUtil

![Alerta de acceso inusual](./media/security-center-alerts-type/security-center-alerts-type-fig20.png)


## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido acerca de los distintos tipos de alertas de seguridad en Security Center. Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Control de incidentes de seguridad en Azure Security Center](security-center-incident.md)
* [Funcionalidades de detección de Azure Security Center](security-center-detection-capabilities.md)
* [Guía de planeamiento y operaciones de Azure Security Center](security-center-planning-and-operations-guide.md)
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): Preguntas más frecuentes acerca del uso del servicio.
* [Blog de seguridad de Azure](https://blogs.msdn.com/b/azuresecurity/): Encuentre artículos de blog sobre el cumplimiento y la seguridad de Azure.
