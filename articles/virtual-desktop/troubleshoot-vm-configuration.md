---
title: Creación de grupos de inquilinos y de host en Windows Virtual Desktop en Azure
description: Cómo resolver problemas al configurar una máquina virtual (VM) de inquilino y host de sesión en un entorno de Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 0e32c81f37a8b81511cd009dfddbcc546aee1797
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876750"
---
# <a name="tenant-and-host-pool-creation"></a>Creación de los grupos de inquilinos y de host

Use este artículo para solucionar los problemas que tiene al configurar las máquinas de virtuales (VM) de host de sesión de Windows Virtual Desktop.

## <a name="provide-feedback"></a>Envío de comentarios

En este momento no se aceptan casos de soporte técnico mientras Windows Virtual Desktop se encuentre en versión preliminar. Visite la [Comunidad técnica de Windows Virtual Desktop](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para hablar sobre Windows Virtual Desktop con el equipo de producto y los miembros activos de la comunidad.

## <a name="vms-are-not-joined-to-the-domain"></a>Las VM no se unen al dominio

Siga estas instrucciones si tiene problemas para unir las VM al dominio.

- Una la VM manualmente con el proceso de [Unir una máquina virtual de Windows Server a un dominio administrado](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) o con la [plantilla de unión a un dominio](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Intente hacer ping en el nombre de dominio desde la línea de comandos en la VM.
- Revise la lista de mensajes de error de unión a un dominio en [Troubleshooting Domain Join Error Messages](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx) (Solución de problemas con mensajes de error de unión a un dominio).

### <a name="error-incorrect-credentials"></a>Error: Credenciales incorrectas

**Causa:** Hubo un error ortográfico al escribir las credenciales en las correcciones de la interfaz de la plantilla de Azure Resource Manager.

**Corrección:** Siga estas instrucciones para corregir las credenciales.

1. Agregue manualmente las VM a un dominio.
2. Vuelva a implementar una vez que las credenciales se hayan confirmado. Consulte [Creación de un grupo host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Una las VM a un dominio con una plantilla con [Joins an existing Windows VM to AD Domain](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/) (Unir una VM Windows existente a un dominio de AD).

### <a name="error-timeout-waiting-for-user-input"></a>Error: Se agotó el tiempo de espera para la entrada del usuario

**Causa:** La cuenta usada para completar la unión a un dominio puede tener autenticación multifactor (MFA).

**Corrección:** Siga estas instrucciones para completar la unión a un dominio.

1. Quite temporalmente la MFA para la cuenta.
2. Use una cuenta de servicio.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Error: La cuenta usada durante el aprovisionamiento no tiene permisos para completar la operación

**Causa:** La cuenta usada no tiene permisos para unir las VM al dominio debido al cumplimiento de normas y reglamentos.

**Corrección:** Siga estas instrucciones.

1. Use una cuenta que sea miembro del grupo Administrador.
2. Conceda los permisos necesarios para la cuenta usada.

### <a name="error-domain-name-doesnt-resolve"></a>Error: El nombre de dominio no se resuelve

**Causa 1:** Las VM están en un grupo de recursos que no está asociado con la red virtual (VNET) donde se encuentra el dominio.

**Corrección 1:** Cree el emparejamiento de VNET entre la red virtual donde se han aprovisionado las VM y la red virtual donde se ejecuta el controlador de dominio (DC). Consulte [Crear un emparejamiento de redes virtuales: Resource Manager, suscripciones diferentes](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**Causa 2:** Al usar AadService (AADS), no se han establecido las entradas de DNS.

**Corrección 2:** Para establecer los servicios de dominio, consulte [Habilitación de Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>El agente de Windows Virtual Desktop y el cargador de arranque de Windows Virtual Desktop no están instalados

La manera recomendada para aprovisionar VM es mediante la plantilla **Create and provision Windows Virtual Desktop host pool** (Creación y aprovisionamiento del grupo de host de Windows Virtual Desktop) de Azure Resource Manager. La plantilla instala automáticamente el agente de Windows Virtual Desktop y el cargador de arranque de Windows Virtual Desktop.

Siga estas instrucciones para confirmar que los componentes se han instalado y para comprobar los mensajes de error.

1. Para confirmar que los dos componentes se han instalado, consulte **Panel de control** > **Programas** > **Programas y características**. Si no ve **Windows Virtual Desktop Agent** y **Windows Virtual Desktop Agent Boot Loader**, no están instalados en la VM.
2. Abra el **Explorador de archivos** y vaya a **C:\Windows\Temp\scriptlogs.log**. Si falta el archivo, es señal de que el DSC de PowerShell que instala los dos componentes no pudo ejecutarse en el contexto de seguridad proporcionado.
3. Si se encuentra el archivo **C:\Windows\Temp\scriptlogs.log**, ábralo y busque mensajes de error.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Error: Faltan el agente de Windows Virtual Desktop y el cargador de arranque de Windows Virtual Desktop. C:\Windows\Temp\scriptlogs.log también falta

**Causa 1:** Las credenciales proporcionadas durante la entrada de la plantilla de Azure Resource Manager son incorrectas o los permisos eran insuficientes.

**Corrección 1:** Agregue manualmente los componentes que faltan para las VM con [Creación de un grupo host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Causa 2:** DSC de PowerShell se puedo iniciar y ejecutar, pero no se pudo completar, ya no se puede iniciar sesión en Windows Virtual Desktop y obtener la información necesaria.

**Corrección 2:** Confirme los elementos de la lista siguiente.

- Asegúrese de que la cuenta no tenga MFA.
- Confirme que el nombre del inquilino esté correcto y que el inquilino exista en Windows Virtual Desktop.
- Confirme que la cuenta tiene al menos permisos de colaborador de RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Error: Error de autenticación, error en C:\Windows\Temp\scriptlogs.log

**Causa:** DSC de PowerShell pudo ejecutarse, pero no pudo conectarse a Windows Virtual Desktop.

**Corrección:** Confirme los elementos de la lista siguiente.

- Registre manualmente las VM con el servicio de Windows Virtual Desktop.
- Confirme que la cuenta usada para conectarse a Windows Virtual Desktop tiene permisos en el inquilino para crear grupos host.
- Confirme que la cuenta no tenga MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>El agente de Windows Virtual Desktop no se registra en el servicio de Windows Virtual Desktop

Cuando el agente de Windows Virtual Desktop se instala por primera vez en las VM de host de sesión (ya sea manualmente o a través de la plantilla de Azure Resource Manager y DSC de PowerShell), proporciona un token de registro. En la siguiente sección se trata la solución de problemas aplicables al agente de Windows Virtual Desktop y el token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Error: El estado archivado en el cmdlet Get-RdsSessionHost muestra el estado como no disponible

![El cmdlet Get-RdsSessionHost muestra el estado como no disponible.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** El agente no puede actualizarse automáticamente a una nueva versión.

**Corrección:** Siga estas instrucciones para actualizar manualmente el agente.

1. Descargue una nueva versión del agente en la VM del host de sesión.
2. Inicie el Administrador de tareas y, en la pestaña Servicio, detenga el servicio RDAgentBootLoader.
3. Ejecute al instalador para la nueva versión del agente de Windows Virtual Desktop.
4. Cuando se le pida el token de registro, quite la entrada INVALID_TOKEN y presione Siguiente (no es necesario un nuevo token).
5. Complete el Asistente para la instalación.
6. Abra el Administrador de tareas e inicie el servicio RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Error:  La entrada del registro IsRegistered del agente de Windows Virtual Desktop muestra un valor de 0

**Causa:** El token de registro ha expirado o se ha generado con el valor de expiración de 999999.

**Corrección:** Siga estas instrucciones para corregir el error del registro del agente.

1. Si ya hay un token de registro, quítelo con Remove-RDSRegistrationInfo.
2. Genere un nuevo token con Rds-NewRegistrationInfo.
3. Confirme que el parámetro -ExpriationHours esté configurado en 72 (el valor máximo es 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Error: El agente de Windows Virtual Desktop no notifica un latido al ejecutar Get-RdsSessionHost

**Causa 1:** Se ha detenido el servicio RDAgentBootLoader.

**Corrección 1:** Inicie el Administrador de tareas y, si la pestaña Servicio notifica un estado detenido para el servicio RDAgentBootLoader, inicie el servicio.

**Causa 2:** Puede que el puerto 443 esté cerrado.

**Corrección 2:** Siga estas instrucciones para abrir el puerto 443.

1. Para confirmar que el puerto 443 esté abierto, descargue la herramienta PSPing desde las [herramientas de Sysinternal](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Instale PSPing en la VM de host de sesión donde se ejecuta el agente.
3. Abra el símbolo del sistema como administrador y emita el comando siguiente:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Confirme que PSPing reciba la información desde RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Solución de problemas con la pila en paralelo de Windows Virtual Desktop

La pila en paralelo de Windows Virtual Desktop se instala automáticamente con Windows Server 2019. Use Microsoft Installer (MSI) para instalar la pila en paralelo en Microsoft Windows Server 2016 o Windows Server 2012 R2. Para Microsoft Windows 10, la pila en paralelo de Windows Virtual Desktop se habilita con **enablesxstackrs.ps1**.

Hay tres maneras principales de instalar la pila en paralelo o habilitarla en VM del grupo de host de sesión:

- Con la plantilla **Create and provision Windows Virtual Desktop host pool** (Creación y aprovisionamiento del grupo de host de Windows Virtual Desktop) de Azure Resource Manager.
- Al incluirla y habilitarla en la imagen maestra.
- Al instalarla o habilitarla manualmente en cada VM (con extensiones o PowerShell).

Si tiene problemas con la pila en paralelo de Windows Virtual Desktop, escriba el comando **qwinsta** desde el símbolo del sistema para confirmar que la pila en paralelo esté instalada o habilitada.

La salida de **qwinsta** enumerará **rdp-sxs** en el resultado si la pila en paralelo está instalada y habilitada.

![La pila en paralelo instalada o habilitada, donde qwinsta aparece como rdp-sxs en la salida.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Examine las entradas del Registro que se enumeran a continuación y confirme que coincidan con sus valores. Si faltan las claves del Registro o los valores no coinciden, siga las instrucciones de [Creación de un grupo host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) para conocer cómo reinstalar la pila en paralelo.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Error: O_REVERSE_CONNECT_STACK_FAILURE

![Código de error de O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Causa:** La pila en paralelo no está instalada en la VM del host de sesión.

**Corrección:** Siga estas instrucciones para instalar la pila en paralelo en la VM del host de sesión.

1. Use el Protocolo de escritorio remoto (RDP) para ir directamente a la VM del host de sesión como administrador local.
2. Si aún no lo ha hecho, [descargue e importe el módulo de PowerShell para Windows Virtual Desktop](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) que se usará en la sesión de PowerShell.
3. Instale la pila en paralelo con [Creación de un grupo host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Cómo corregir una pila en paralelo de Windows Virtual Desktop con error de funcionamiento

Existen circunstancias conocidas que pueden provocar que la pila en paralelo no funcione correctamente:

- No seguir el orden correcto de los pasos para habilitar la pila en paralelo
- Actualizar automáticamente a Enhanced Versatile Disc (EVD) de Windows 10
- La falta del rol de host de sesión de Escritorio remoto (RDSH)
- Ejecutar enablesxsstackrc.ps1 varias veces
- Ejecutar enablesxsstackrc.ps1 en una cuenta que no tiene privilegios de administrador local

Las instrucciones de esta sección pueden ayudarle a desinstalar la pila en paralelo de Windows Virtual Desktop. Una vez que se desinstale la pila en paralelo, vaya a "Registrar las máquinas virtuales al grupo host de vista previa de Escritorio Virtual de Windows" en [Creación de un grupo host con PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) para reinstalar la pila en paralelo.

La VM usada para ejecutar la corrección debe estar en la misma subred y el mismo dominio que la VM con la pila en paralelo con error de funcionamiento.

Siga estas instrucciones para ejecutar la corrección desde la misma subred y dominio:

1. Conéctese con el Protocolo de escritorio remoto (RDP) a la VM desde donde se aplicará la revisión.
2. Descargue PsExec de https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Descomprima el archivo descargado.
4. Inicie un símbolo del sistema como administrador local.
5. Navegue hasta la carpeta donde ha descomprimido PsExec.
6. En un símbolo del sistema, use el siguiente comando:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname es el nombre de equipo de la VM con la pila en paralelo con error de funcionamiento.

7. Para aceptar el contrato de licencia de PsExec, haga clic en Aceptar.

    ![Captura de pantalla del contrato de licencia de software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Este cuadro de diálogo se muestra solo la primera vez que se ejecuta PsExec.

8. Una vez que se abre la sesión de símbolo del sistema en la VM con la pila en paralelo con error de funcionamiento, ejecute qwinsta y confirme que haya disponible una entrada denominada rdp-sxs. Si no es así, no hay una pila en paralelo en la VM, así que el problema no está vinculado a la pila en paralelo.

    ![Símbolo del sistema de administrador](media/AdministratorCommandPrompt.png)

9. Ejecute el comando siguiente, que mostrará una lista de componentes de Microsoft instalados en la VM con la pila en paralelo con error de funcionamiento.

    ```cmd
        wmic product get name
    ```

10. Ejecute el comando siguiente con los nombres de productos del paso anterior.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Desinstale todos los productos que empiecen por "Remote Desktop".

12. Una vez se hayan desinstalado todos los componentes de Windows Virtual Desktop, siga las instrucciones para su sistema operativo:

13. Si el sistema operativo es Windows Server, reinicie la VM que tenía la pila en paralelo con error de funcionamiento (ya sea con Azure Portal o desde la herramienta PsExec).

Si el sistema operativo es Microsoft Windows 10, continúe con las instrucciones siguientes:

14. Desde la VM que ejecuta PsExec, abra el Explorador de archivos y copie disablesxsstackrc.ps1 en la unidad del sistema de la VM con la pila en paralelo con error de funcionamiento.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname es el nombre de equipo de la VM con la pila en paralelo con error de funcionamiento.

15. Proceso recomendado: desde la herramienta PsExec, inicie PowerShell y navegue hasta la carpeta del paso anterior y ejecute disablesxsstackrc.ps1. Como alternativa, puede ejecutar los cmdlets siguientes:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Cuando se terminen de ejecutar los cmdlets, reinicie la VM con la pila en paralelo con error de funcionamiento.

## <a name="remote-licensing-model-is-not-configured"></a>El modelo de licencias de Escritorio remoto no está configurado

Si inicia sesión en Windows 10 Enterprise multisesión con una cuenta administrativa, puede recibir una notificación que dice que "el modo de administración de licencias de Escritorio remoto no está configurado, los servicios de Escritorio remoto dejarán de funcionar en X días. En el servidor de Agente de conexión, use el administrador del servidor para especificar el modo de administración de licencias de Escritorio remoto." Si ve este mensaje, significa que debe configurar manualmente el modo de administración de licencias a **Por usuario**.

Para configurar manualmente el modo de administración de licencias:  

1. Vaya al cuadro de búsqueda del **menú Inicio** y, a continuación, busque y abra **gpedit.msc** para acceder al editor local de directivas de grupo. 
2. Vaya a  **Configuración del equipo** > **Plantillas administrativas** > **Componentes de Windows** > **Servicios de Escritorio remoto** > **Host de sesión de Escritorio remoto** > **Licencias**. 
3. Seleccione **Establecer el modo de licencia de Escritorio remoto** y cámbielo a **Por usuario**.

Estamos analizando los problemas de notificación y de tiempo de expiración del período de gracia y se les dará una solución en una futura actualización. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre cómo solucionar problemas de Windows Virtual Desktop y las pistas de escalación, consulte [Introducción, comentarios y soporte técnico para solucionar problemas](troubleshoot-set-up-overview.md).
- Para solucionar problemas durante la creación de un grupo de inquilinos y de hosts en un entorno de Windows Virtual Desktop, consulte [Creación de los grupos de inquilinos y de host](troubleshoot-set-up-issues.md).
- Para solucionar problemas al configurar una máquina virtual (VM) en Windows Virtual Desktop, consulte [Configuración de la máquina virtual del host de sesión](troubleshoot-vm-configuration.md).
- Para solucionar problemas con conexiones de cliente de Windows Virtual Desktop, consulte [Conexiones de cliente de Escritorio remoto](troubleshoot-client-connection.md).
- Para solucionar problemas al usar PowerShell con Windows Virtual Desktop, consulte [PowerShell para Windows Virtual Desktop](troubleshoot-powershell.md).
- Para obtener más información acerca del servicio en versión preliminar, consulte [Entorno de versión preliminar de Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Para realizar un tutorial de solución de problemas, consulte [Tutorial: Solución de problemas de las implementaciones de plantillas de Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Para más información sobre las acciones de auditoría, consulte [Operaciones de auditoría con Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Si desea conocer más detalles sobre las acciones que permiten determinar los errores durante la implementación, consulte [Visualización de operaciones de implementación con el Portal de Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).