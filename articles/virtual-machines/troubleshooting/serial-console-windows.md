---
title: Consola serie de Azure para Windows | Microsoft Docs
description: Consola serie bidireccional para máquinas virtuales y conjuntos de escalado de máquinas virtuales de Azure.
services: virtual-machines-windows
documentationcenter: ''
author: asinn826
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 5/1/2019
ms.author: alsin
ms.openlocfilehash: a6e303c26278eff290a2d4efb6f96e9962cf2f87
ms.sourcegitcommit: 4b5dcdcd80860764e291f18de081a41753946ec9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2019
ms.locfileid: "68775359"
---
# <a name="azure-serial-console-for-windows"></a>Consola serie de Azure para Windows

La consola serie de Azure Portal proporciona acceso a una consola basada en texto para máquinas virtuales e instancias de conjunto de escalado de máquinas virtuales de Windows. Esta conexión serie se conecta al puerto serie COM1 de la máquina virtual o del conjunto de escalado de máquinas virtuales y ofrece acceso a ellos sin estar relacionada con el estado del sistema operativo o de la red. A la consola serie solo se puede acceder mediante Azure Portal; además, esta solo se permite a los usuarios que tienen un rol de acceso de colaborador o superior en el conjunto de escalado de máquinas virtuales o la máquina virtual.

La consola serie funciona de la misma manera para las máquinas virtuales y las instancias de conjunto de escalado de máquinas virtuales. En este documento, todas las menciones a las máquinas virtuales incluirán implícitamente las instancias de conjunto de escalado de máquinas virtuales, a menos que se indique lo contrario.

Para obtener documentación sobre la consola serie para máquinas virtuales Linux, consulte [Consola serie de máquina virtual para Linux](serial-console-linux.md).

> [!NOTE]
> La consola serie está disponible con carácter general en regiones de Azure globales. Aún no está disponible en las nubes de Azure Government ni Azure China.


## <a name="prerequisites"></a>Requisitos previos

* La máquina virtual o la instancia de conjunto de escalado de máquinas virtuales deben usar el modelo de implementación de Resource Manager. No se admiten las implementaciones clásicas.

- La cuenta que usa una consola serie debe tener el [rol Colaborador de la máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) y la cuenta de almacenamiento de [diagnósticos de arranque](boot-diagnostics.md).

- La máquina virtual o la instancia de conjunto de escalado de máquinas virtuales deben tener un usuario con contraseña. Puede crear una con la función para [restablecer la contraseña](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) de la extensión de acceso de máquina virtual. Seleccione **Restablecer contraseña** en la sección **Soporte técnico y solución de problemas**.

* La máquina virtual en la que accede a una consola serie tiene que tener los [diagnósticos de arranque](boot-diagnostics.md) habilitados.

    ![Configuración de los diagnósticos de arranque](../media/virtual-machines-serial-console/virtual-machine-serial-console-diagnostics-settings.png)

## <a name="get-started-with-the-serial-console"></a>Introducción al uso de la consola serie
La consola serie para las máquinas virtuales y el conjunto de escalado de máquinas virtuales solo es accesible mediante Azure Portal:

### <a name="serial-console-for-virtual-machines"></a>Consola serie para máquinas virtuales
Usar la consola serie para máquinas virtuales es tan sencillo como hacer clic en **Consola serie** en la sección **Soporte técnico y solución de problemas** de Azure Portal.
  1. Abra [Azure Portal](https://portal.azure.com).

  1. Vaya a **Todos los recursos** y seleccione una máquina virtual. Se abrirá la página de información general de la máquina virtual.

  1. Desplácese hacia abajo hasta la sección **Soporte técnico y solución de problemas** y seleccione la opción **Consola serie**. Se abrirá un panel nuevo con la consola serie y se iniciará la conexión.

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Consola serie para los conjuntos de escalado de máquinas virtuales
La consola serie está disponible por instancia para los conjuntos de escalado de máquinas virtuales. Tendrá que ir a la instancia individual de un conjunto de escalado de máquinas virtuales para ver el botón **Consola serie**. Si su conjunto de escalado de máquinas virtuales no tiene habilitado el diagnóstico de arranque, asegúrese de actualizar el modelo para habilitarlo y actualice todas las instancias en el nuevo modelo para acceder a la consola serie.
  1. Abra [Azure Portal](https://portal.azure.com).

  1. Vaya a **Todos los recursos** y seleccione un conjunto de escalado de máquinas virtuales. Se abrirá la página de información general del conjunto de escalado de máquinas virtuales.

  1. Vaya a **Instancias**.

  1. Seleccione un conjunto de escalado de máquinas virtuales.

  1. En la sección **Soporte técnico y solución de problemas**, seleccione **Consola serie**. Se abrirá un panel nuevo con la consola serie y se iniciará la conexión.

## <a name="enable-serial-console-functionality"></a>Habilitación de la funcionalidad de la consola serie

> [!NOTE]
> Si no ve nada en la consola serie, asegúrese de que el diagnóstico de arranque está habilitado en la máquina virtual o en el conjunto de escalado de máquinas virtuales.

### <a name="enable-the-serial-console-in-custom-or-older-images"></a>Habilitación de la consola serie en imágenes personalizadas o antiguas
Las imágenes más nuevas de Windows Server en Azure tienen la [consola de administración especial](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) (SAC) habilitada de forma predeterminada. SAC se admite en versiones de servidor de Windows, pero no está disponible en versiones de cliente (por ejemplo, Windows 10, Windows 8 o Windows 7).

En el caso de las imágenes de Windows Server anteriores (creadas antes de febrero de 2018), puede habilitar automáticamente la consola serie mediante la característica de ejecución de comandos de Azure Portal. En Azure Portal, seleccione **Ejecutar comando** y, a continuación, seleccione el comando llamado **EnableEM** de la lista.

![Lista de comandos Ejecutar](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-runcommand.png)

De manera alternativa, para habilitar manualmente la consola serie para las máquinas virtuales o el conjunto de escalado de máquinas virtuales de Windows que se crearon antes de febrero de 2018, siga estos pasos:

1. Conéctese a la máquina virtual Windows mediante Escritorio remoto
1. Ejecute los siguientes comandos como administrador en un símbolo del sistema:
    - `bcdedit /ems {current} on`
    - `bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200`
1. Reinicie el sistema para que la consola de SAC se habilite.

    ![Consola de SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect.gif)

Si es necesario SAC también puede habilitarse sin conexión:

1. Conecte el disco de Windows para el que desea configurar SAC como un disco de datos a la máquina virtual existente.

1. Ejecute los siguientes comandos como administrador en un símbolo del sistema:
   - `bcdedit /store <mountedvolume>\boot\bcd /ems {default} on`
   - `bcdedit /store <mountedvolume>\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

#### <a name="how-do-i-know-if-sac-is-enabled"></a>¿Cómo se puede saber si SAC está habilitada o no?

Si [SAC](https://technet.microsoft.com/library/cc787940(v=ws.10).aspx) no está habilitado, la consola serie no mostrará el símbolo del sistema de SAC. En algunos casos, se muestra la información de mantenimiento de la máquina virtual y, en otros casos, se queda en blanco. Si utiliza una imagen de Windows Server creada antes de febrero de 2018, es probable que SAC no esté habilitado.

### <a name="enable-the-windows-boot-menu-in-the-serial-console"></a>Habilitación del menú de arranque de Windows en la consola serie

Si tiene que habilitar los mensajes del cargador de arranque de Windows para que se muestren en la consola serie, puede agregar las siguientes opciones adicionales a los datos de la configuración de arranque. Para más información, consulte [bcdedit](https://docs.microsoft.com/windows-hardware/drivers/devtest/bcdedit--set).

1. Conéctese a la máquina virtual o la instancia de conjunto de escalado de máquinas virtuales Windows mediante Escritorio remoto.

1. Ejecute los siguientes comandos como administrador en un símbolo del sistema:
   - `bcdedit /set {bootmgr} displaybootmenu yes`
   - `bcdedit /set {bootmgr} timeout 10`
   - `bcdedit /set {bootmgr} bootems yes`

1. Reinicie el sistema para que el menú de arranque se habilite

> [!NOTE]
> El tiempo de espera que establezca para que se muestre el menú del administrador de arranque afectará al tiempo de arranque del sistema operativo. Si cree que el valor de tiempo de espera de 10 segundos es demasiado corto o demasiado largo, establézcalo en un valor diferente.

## <a name="use-serial-console"></a>Uso de Serial Console

### <a name="use-cmd-or-powershell-in-serial-console"></a>Uso de CMD o PowerShell en la consola serie

1. Conéctese a la consola serie. Si se conecta correctamente, el símbolo del sistema es **SAC>** :

    ![Conectarse a SAC](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-connect-sac.png)

1.  Escriba `cmd` para crear un canal que contenga una instancia de CMD.

1.  Escriba `ch -si 1` o presione las teclas de método abreviado `<esc>+<tab>` para cambiar al canal que ejecuta la instancia de CDM.

1.  Presione **Entrar** y, a continuación, escriba las credenciales de inicio de sesión con permisos administrativos.

1.  Después de escribir las credenciales válidas, se abre la instancia de CMD.

1.  Para iniciar una instancia de PowerShell, escriba `PowerShell` en la instancia de CMD y, a continuación, presione **Entrar**.

    ![Abrir una instancia de PowerShell](./media/virtual-machines-serial-console/virtual-machine-windows-serial-console-powershell.png)

### <a name="use-the-serial-console-for-nmi-calls"></a>Uso de la consola serie para llamadas NMI
Una interrupción no enmascarable (NMI) está diseñada para crear una señal que el software de una máquina virtual no pasará por alto. Históricamente, las NMI se han usado para supervisar problemas de hardware en sistemas que necesitaban tiempos de respuesta específicos. En la actualidad, los administradores del sistema y los programadores suelen usar NMI como mecanismo para depurar o solucionar los problemas de sistemas que no responden.

La consola serie se puede usar para enviar una interrupción NMI a una máquina virtual de Azure mediante el icono de teclado de la barra de comandos. Una vez que se entrega la interrupción NMI, la configuración de la máquina virtual controla cómo responde el sistema. Windows se puede configurar para bloquearse y crear un archivo de volcado de memoria cuando recibe una interrupción NMI.

![Enviar NMI](../media/virtual-machines-serial-console/virtual-machine-windows-serial-console-nmi.png) <br>

Para obtener información sobre cómo configurar Windows para crear un archivo de volcado de memoria cuando recibe una interrupción NMI, consulte: [Cómo generar un archivo de volcado de memoria con una interrupción NMI](https://support.microsoft.com/help/927069/how-to-generate-a-complete-crash-dump-file-or-a-kernel-crash-dump-file).

### <a name="use-function-keys-in-serial-console"></a>Uso de las teclas de función en la consola serie
Las teclas de función están habilitadas para usarse en la consola serie en máquinas virtuales Windows. La tecla F8 en la lista desplegable de la consola serie ofrece la comodidad de especificar fácilmente el menú de configuración de arranque avanzado, pero la consola serie es compatible con todas las demás teclas de función. Es posible que tenga que presionar **Fn** + **F1** (o F2, F3, etc.) en el teclado en función del equipo en el que use la consola serie.

### <a name="use-wsl-in-serial-console"></a>Uso de WSL en la consola serie
Subsistema de Windows para Linux (WSL) se ha habilitado para Windows Server 2019 o versiones posteriores, por lo que también es posible habilitar WSL para usarlo dentro de la consola serie si se ejecuta Windows Server 2019 o posterior. Esto puede resultar útil para los usuarios que también conocen los comandos de Linux. Para obtener instrucciones que permitan habilitar WSL para Windows Server, consulte la [Guía de instalación](https://docs.microsoft.com/windows/wsl/install-on-server).

### <a name="restart-your-windows-vmvirtual-machine-scale-set-instance-within-serial-console"></a>Reinicio de la máquina virtual o la instancia de conjunto de escalado de máquinas virtuales Windows en la consola serie
Puede comenzar el reinicio dentro de la consola serie si va al botón de encendido y hace clic en "Reiniciar VM". Así se iniciará un reinicio de la máquina virtual, y verá una notificación en Azure Portal relativa al reinicio.

Esto es útil en aquellas situaciones en que es posible que desee acceder al menú de arranque sin salir de la consola serie.

![Reinicio de la consola serie Windows](./media/virtual-machines-serial-console/virtual-machine-serial-console-restart-button-windows.gif)

## <a name="disable-serial-console"></a>Deshabilitación de la consola serie
De forma predeterminada, todas las suscripciones tienen el acceso a la consola serie habilitado para todas las VM. Puede deshabilitar la consola serie en el nivel de la suscripción o de la máquina virtual.

### <a name="vmvirtual-machine-scale-set-level-disable"></a>Nivel de la máquina virtual o del conjunto de escalado de máquinas virtuales deshabilitado
La consola serie puede deshabilitarse para una máquina virtual o un conjunto de escalado de máquinas virtuales específicos al deshabilitar la configuración de diagnóstico de arranque. Desactive el diagnóstico de arranque desde Azure Portal para deshabilitar la consola serie para la máquina virtual o el conjunto de escalado de máquinas virtuales. Si utiliza la consola serie en un conjunto de escalado de máquinas virtuales, asegúrese de actualizar las instancias al modelo más reciente.

> [!NOTE]
> Para habilitar o deshabilitar la consola serie en una suscripción, debe tener permisos de escritura en la suscripción. Estos permisos incluyen, entre otros, los roles de administrador o propietario. Los roles personalizados también pueden tener permisos de escritura.

### <a name="subscription-level-disable"></a>Deshabilitación a nivel de supervisión
La consola serie puede deshabilitarse para toda una suscripción a través de la [llamada API REST Disable Console](/rest/api/serialconsole/console/disableconsole). Esta acción requiere acceso de nivel de colaborador o superior en la suscripción. Puede usar la función **Probarlo** disponible en la página de documentación de la API para deshabilitar y habilitar la consola serie para una suscripción. Escriba el identificador de suscripción para **subscriptionId**, escriba "default" para el valor **predeterminado** y, a continuación, seleccione **Ejecutar**. Los comandos de la CLI de Azure aún no están disponibles.

Para volver a habilitar la consola serie para una suscripción, utilice la [llamada de API REST Enable Console](/rest/api/serialconsole/console/enableconsole).

![Probarlo en API REST](../media/virtual-machines-serial-console/virtual-machine-serial-console-rest-api-try-it.png)

Como alternativa, puede usar el conjunto de comandos Bash siguiente en Cloud Shell para deshabilitar, habilitar y ver el estado deshabilitado de la consola serie para una suscripción.

* Para obtener el estado deshabilitado de la consola serie para una suscripción:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s | jq .properties
    ```
* Para deshabilitar la consola serie para una suscripción:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/disableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```
* Para habilitar la consola serie para una suscripción:
    ```azurecli-interactive
    $ export ACCESSTOKEN=($(az account get-access-token --output=json | jq .accessToken | tr -d '"'))

    $ export SUBSCRIPTION_ID=$(az account show --output=json | jq .id -r)

    $ curl -X POST "https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/providers/Microsoft.SerialConsole/consoleServices/default/enableConsole?api-version=2018-05-01" -H "Authorization: Bearer $ACCESSTOKEN" -H "Content-Type: application/json" -H "Accept: application/json" -s -H "Content-Length: 0"
    ```

## <a name="serial-console-security"></a>Seguridad de la consola serie

### <a name="access-security"></a>Seguridad de acceso
El acceso a la consola serie está limitado a los usuarios que tienen un rol de acceso de [Colaborador de la máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) o superior. Si el inquilino de Azure Active Directory requiere la autenticación multifactor (MFA), el acceso a la consola serie también requerirá MFA porque el acceso a la consola serie se realiza mediante [Azure Portal](https://portal.azure.com).

### <a name="channel-security"></a>Seguridad del canal
Todos los datos enviados y recibidos se cifran en la conexión.

### <a name="audit-logs"></a>Registros de auditoría
Todo el acceso a la consola serie queda registrado en los registros de los [diagnósticos de arranque](https://docs.microsoft.com/azure/virtual-machines/linux/boot-diagnostics) de la máquina virtual. El administrador de la máquina virtual de Azure es el propietario y el que controla el acceso a estos registros.

> [!CAUTION]
> Las contraseñas de acceso de la consola no se registran. Sin embargo, si los comandos ejecutados en la consola contienen o generan contraseñas, secretos, nombres de usuario o cualquier otra forma de información de identificación personal, se escribirán en los registros de diagnóstico de arranque de la máquina virtual. Se escribirán junto con el resto del texto visible, como parte de la implementación de la función de desplazamiento de la consola serie. Estos registros son circulares y solo tienen acceso a ellos aquellas personas con permisos de lectura a la cuenta de almacenamiento de diagnósticos. Sin embargo, recomendamos usar el Escritorio remoto para cualquier operación que pueda implicar secretos o información de identificación personal.

### <a name="concurrent-usage"></a>Uso simultáneo
Si un usuario se conecta a la consola serie y otro usuario solicita correctamente acceso a esa misma máquina virtual, se desconectará el primer usuario y se conectará el segundo a la misma sesión.

> [!CAUTION]
> Esto significa que no se cerrará la sesión de un usuario que se haya desconectado. La posibilidad de forzar el cierre de sesión con la desconexión (mediante SIGHUP u otro mecanismo similar) sigue en proceso de valoración. En Windows hay un tiempo de espera automático habilitado en SAC; en Linux puede configurar el valor de tiempo de espera del terminal.

## <a name="accessibility"></a>Accesibilidad
La accesibilidad es un factor clave de la consola serie de Azure. Para ello, nos hemos asegurado de que la consola serie es accesible para quienes tienen dificultades auditivas y visuales, así como para las personas que no pueden utilizar un mouse.

### <a name="keyboard-navigation"></a>Navegación con el teclado
Use la tecla **Tabulador** del teclado para navegar por la interfaz de la consola serie en Azure Portal. La ubicación se resaltará en pantalla. Para salir del enfoque de la ventana de la consola serie, presione **Control**+**F6** en el teclado.

### <a name="use-the-serial-console-with-a-screen-reader"></a>Uso de la consola serie con un lector de pantalla
La consola serie tiene integrada la compatibilidad con el lector de pantalla. Si navega con el lector de pantalla activado, el lector de pantalla podrá leer en voz alta el texto alternativo del botón seleccionado actualmente.

## <a name="common-scenarios-for-accessing-the-serial-console"></a>Escenarios comunes para acceder a la consola de serie

Escenario          | Acciones en la consola serie
:------------------|:-----------------------------------------
Reglas de firewall incorrectas | Accede a la consola de serie y corrige las reglas de firewall de Windows.
Comprobación o daños en el sistema de archivos | Acceda a la consola serie y recupere el sistema de archivos.
Problemas de configuración de RDP | Acceda a la consola serie y cambie la configuración. Para más información, consulte la [documentación de RDP](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-allow-access).
Sistema de bloqueo de red | Acceda a la consola serie desde Azure Portal para administrar el sistema. Algunos comandos de red se enumeran en [Comandos de Windows: CMD y PowerShell](serial-console-cmd-ps-commands.md).
Interacción con el cargador de arranque | Acceda a BCD mediante la consola serie. Para obtener información, consulte [Habilitación del menú de arranque de Windows en la consola serie](#enable-the-windows-boot-menu-in-the-serial-console).


## <a name="errors"></a>Errors
Dado que la mayoría de los errores son transitorios, es posible que se solucionen si vuelve a intentar la conexión. En la siguiente tabla se muestra una lista de errores y mitigaciones para las máquinas virtuales y las instancias de conjunto de escalado de máquinas virtuales.

Error                            |   Mitigación
:---------------------------------|:--------------------------------------------|
No se puede recuperar la configuración de diagnóstico de arranque para *&lt;VMNAME&gt;* . Para usar la consola serie, asegúrese de que el diagnóstico de arranque está habilitado para esta máquina virtual. | Asegúrese de que la máquina virtual tiene los [diagnósticos de arranque](boot-diagnostics.md) habilitados.
La máquina virtual se encuentra en un estado desasignado detenido. Inicie la máquina virtual y vuelva a intentar la conexión de la consola serie. | La máquina virtual tiene que estar en un estado iniciado para tener acceso a la consola serie
No tiene los permisos necesarios para usar la consola serie de esta máquina virtual. Asegúrese de tener los permisos del rol de Colaborador de la máquina virtual como mínimo.| El acceso a la consola serie requiere determinados permisos. Para obtener más información, consulte la sección [Requisitos previos](#prerequisites).
No se puede determinar el grupo de recursos para la cuenta de almacenamiento de diagnósticos de arranque *&lt;STORAGEACCOUNTNAME&gt;* . Compruebe que los diagnósticos de arranque están habilitados para esta máquina virtual y acceda a esta cuenta de almacenamiento. | El acceso a la consola serie requiere determinados permisos. Para obtener más información, consulte la sección [Requisitos previos](#prerequisites).
Se encontró una respuesta "Prohibido" al obtener acceso a la cuenta de almacenamiento de diagnósticos de arranque de la VM. | Asegúrese de que el diagnóstico de arranque no tenga un firewall de cuentas. Se necesita una cuenta de almacenamiento de diagnósticos de arranque accesible para que la consola serie funcione.
Socket web está cerrado o no se pudo abrir. | Puede que necesite incluir `*.console.azure.com` en la lista blanca. Un enfoque más detallado, pero más largo, es incluir en la lista blanca los [rangos de IP del centro de datos de Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653), que cambian con bastante frecuencia.
Se muestra solamente información de mantenimiento cuando se conecta a una máquina virtual Windows| Este error se produce si no se ha habilitado la consola de administración especial para la imagen de Windows. Consulte [Habilitación de la consola serie en imágenes personalizadas o antiguas](#enable-the-serial-console-in-custom-or-older-images) para obtener instrucciones sobre cómo habilitar manualmente SAC en la máquina virtual Windows. Para más información, consulte [Señales de mantenimiento de Windows](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Windows_Health_Info.md).

## <a name="known-issues"></a>Problemas conocidos
Somos conscientes de que la consola serie presenta algunos problemas. A continuación, tiene una lista de dichos problemas y los pasos que puede realizar para mitigarlos. Estos problemas y mitigaciones se aplican tanto a las máquinas virtuales como a las instancias de conjunto de escalado de máquinas virtuales.

Problema                             |   Mitigación
:---------------------------------|:--------------------------------------------|
Al presionar **Entrar** después del banner de conexión no se muestra ninguna solicitud de inicio de sesión. | Para obtener más información, consulte [Hitting enter does nothing](https://github.com/Microsoft/azserialconsole/blob/master/Known_Issues/Hitting_enter_does_nothing.md) (Al presionar Entrar, no se realiza ninguna acción). Este error puede ocurrir si está ejecutando una máquina virtual personalizada, un dispositivo protegido o una configuración de arranque que hace que Windows no pueda conectarse correctamente al puerto serie. Este error se producirá también si está ejecutando una máquina virtual cliente de Windows 10, ya que únicamente las máquinas virtuales de Windows Server están configuradas para tener habilitada la opción EMS.
No se puede escribir en el símbolo del sistema de SAC si la depuración del kernel está habilitada. | Conéctese mediante RDP a la máquina virtual y ejecute `bcdedit /debug {current} off` desde un símbolo del sistema con privilegios elevados. Si no puede conectarse mediante RDP, en su lugar puede conectar el disco del sistema operativo a otra máquina virtual de Azure y modificarlo mientras está conectado como un disco de datos mediante la ejecución de `bcdedit /store <drive letter of data disk>:\boot\bcd /debug <identifier> off` para, seguidamente, volver a intercambiar el disco.
Pegar en PowerShell en SAC da como resultado un tercer carácter si el contenido original tenía un carácter repetido. | Una solución alternativa es ejecutar `Remove-Module PSReadLine` para descargar el módulo PSReadLine desde la sesión actual. Esta acción no eliminará ni desinstalará el módulo.
Algunas entradas de teclado generan resultados extraños de SAC (por ejemplo, **[A**, **[3~** ). | Las secuencias de escape [VT100](https://aka.ms/vtsequences) no son compatibles con el símbolo del sistema de SAC.
El hecho de pegar cadenas largas no funciona. | La consola serie limita la longitud de las cadenas pegadas en el terminal a 2048 caracteres a fin de impedir que el ancho de banda del puerto serie se sobrecargue.
La consola serie no funciona con un firewall de la cuenta de almacenamiento. | Por su diseño, la consola serie no puede operar con firewalls de la cuenta de almacenamiento habilitados en la cuenta de almacenamiento del diagnóstico de arranque.
La consola serie no funciona con una cuenta de almacenamiento que use Azure Data Lake Storage Gen2 con espacios de nombres jerárquicos. | Se trata de un problema conocido con los espacios de nombres jerárquicos. Para mitigar el problema, asegúrese de que la cuenta de almacenamiento con diagnóstico de arranque de la máquina virtual no se crea mediante Azure Data Lake Storage Gen2. Esta opción solo puede establecerse durante la creación de la cuenta de almacenamiento. Es posible que deba crear cuenta de almacenamiento con diagnóstico de arranque independiente sin Azure Data Lake Storage Gen2 habilitado para mitigar este problema.


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

**P. ¿Cómo puedo enviar comentarios?**

A. Proporcione comentarios mediante la creación de un problema de GitHub en https://aka.ms/serialconsolefeedback. También tiene la posibilidad (aunque no es tan recomendable) de enviar comentarios a través de azserialhelp@microsoft.com o en la categoría de la máquina virtual de https://feedback.azure.com.

**P. ¿La consola serie admite las operaciones de copiar y pegar?**

A. Sí. Use **Control**+**Mayús**+**C** y **Control**+**Mayús**+**V** para copiar y pegar contenido en el terminal.

**P. ¿Quién puede habilitar o deshabilitar la consola serie en mi suscripción?**

A. Para habilitar o deshabilitar la consola serie en toda una suscripción, es preciso tener permisos de escritura en la suscripción. Los roles que tienen permiso de escritura son, entre otros, los roles de administrador o propietario. Los roles personalizados también pueden tener permisos de escritura.

**P. ¿Quién puede acceder a la consola serie de mi máquina virtual?**

A. Debe tener el rol Colaborador o superior para una máquina virtual a fin de tener acceso a la consola serie de la máquina virtual.

**P. La consola serie no muestra nada, ¿qué hago?**

A. Es probable que la imagen esté mal configurada para el acceso a la consola serie. Consulte [Habilitación de la consola serie en imágenes personalizadas o antiguas](#enable-the-serial-console-in-custom-or-older-images) para obtener información sobre la configuración de la imagen para habilitar la consola serie.

**P. ¿Está disponible la consola serie en los conjuntos de escalado de máquinas virtuales?**

A. En estos momentos no se admite el acceso a la consola serie mediante instancias del conjunto de escalado de máquinas virtuales.

## <a name="next-steps"></a>Pasos siguientes
* Para obtener una guía detallada de los comandos de CMD y PowerShell que puede usar en SAC para Windows, consulte [Comandos de Windows: CMD y PowerShell](serial-console-cmd-ps-commands.md).
* La consola serie también está disponible para máquinas virtuales [Linux](serial-console-linux.md).
* Obtenga más información sobre [diagnósticos de arranque](boot-diagnostics.md).
