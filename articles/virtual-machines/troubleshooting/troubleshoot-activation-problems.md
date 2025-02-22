---
title: Solución de problemas de activación de máquinas virtuales de Windows en Azure | Microsoft Docs
description: Ofrece los pasos de solución de problemas para resolver problemas de activación de máquinas virtuales de Windows en Azure
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: genlin
manager: willchen
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 11/15/2018
ms.author: genli
ms.openlocfilehash: d403292a7f7ab1080f4270a420c23353eda5fd71
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70090042"
---
# <a name="troubleshoot-azure-windows-virtual-machine-activation-problems"></a>Solución de problemas de activación de máquinas virtuales Windows de Azure

Si tiene algún problema al activar una máquina virtual (VM) Windows de Azure que se crea a partir de una imagen personalizada, puede usar la información proporcionada en este documento para solucionar el problema. 

## <a name="understanding-azure-kms-endpoints-for-windows-product-activation-of-azure-virtual-machines"></a>Descripción de los puntos de conexión de KMS de Azure para la activación de productos Windows de Azure Virtual Machines

Azure usa puntos de conexión diferentes para la activación de KMS en función de la región en la nube donde resida la máquina virtual. Al usar esta guía de solución de problemas, utilice el punto de conexión de KMS adecuado que se aplica a su región.

* Regiones de la nube pública de Azure: kms.core.windows.net:1688
* Regiones de la nube nacional de Azure China 21Vianet: kms.core.chinacloudapi.cn:1688
* Regiones de la nube nacional de Azure Alemania: kms.core.cloudapi.de:1688
* Regiones de la nube nacional de Azure US Gov: kms.core.usgovcloudapi.net:1688

## <a name="symptom"></a>Síntoma

Al intentar activar una VM Windows de Azure, recibe un mensaje de error similar al del ejemplo siguiente:

**Error: 0xC004F074 The Software LicensingService reported that the computer could not be activated. No Key ManagementService (KMS) could be contacted. Please see the Application Event Log for additional information.** (Error: 0xC004F074 El Servicio de licencias de software ha notificado que el servicio no se ha podido activar. No se ha podido establecer contacto con ningún Servicio de administración de claves (KMS). Vea el registro de eventos de la aplicación para obtener información adicional).

## <a name="cause"></a>Causa

Por lo general, los problemas de activación de máquinas virtuales de Azure se producen si la VM de Windows no está configurada con la clave de instalación de cliente KMS apropiada o la tiene un problema de conectividad con el servicio de KMS de Azure (kms.core.windows.net, puerto 1688). 

## <a name="solution"></a>Solución

>[!NOTE]
>Si usa una VPN de sitio a sitio y tunelización forzada, vea [Use Azure custom routes to enable KMS activation with forced tunneling](https://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) (Uso de rutas personalizadas de Azure para permitir la activación de KMS con tunelización forzada). 
>
>Si usa ExpressRoute y tiene una ruta predeterminada publica, vea [Azure VM may fail to activate over ExpressRoute](https://blogs.msdn.com/b/mast/archive/2015/12/01/azure-vm-may-fail-to-activate-over-expressroute.aspx) (Es posible que la máquina virtual de Azure no se pueda activar por ExpressRoute).

### <a name="step-1-configure-the-appropriate-kms-client-setup-key"></a>Paso 1 Establecimiento de la clave de configuración de cliente KMS adecuada

En el caso de máquinas virtuales creadas a partir de una imagen personalizada, hay que configurar la clave de configuración de cliente KMS adecuada para la máquina virtual.

1. Ejecute **slmgr.vbs /dlv** en un símbolo del sistema con privilegios elevados. Compruebe el valor de Descripción en la salida y determine si se ha creado desde el canal comercial (RETAIL) o desde soportes de licencia por volumen (VOLUME_KMSCLIENT):
  

    ```
    cscript c:\windows\system32\slmgr.vbs /dlv
    ```

2. Si **slmgr.vbs /dlv** muestra el canal RETAIL, ejecute los siguientes comandos para establecer la [clave de instalación de cliente KMS](https://technet.microsoft.com/library/jj612867%28v=ws.11%29.aspx?f=255&MSPPError=-2147217396) de la versión de Windows Server que se usa y haga que vuelva a intentar la activación: 

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk <KMS client setup key>

    cscript c:\windows\system32\slmgr.vbs /ato
     ```

    Por ejemplo, para Windows Server 2016 Datacenter, debe ejecutar el siguiente comando:

    ```
    cscript c:\windows\system32\slmgr.vbs /ipk CB7KF-BWN84-R7R2Y-793K2-8XDDG
    ```

### <a name="step-2-verify-the-connectivity-between-the-vm-and-azure-kms-service"></a>Paso 2 Comprobación de la conectividad entre la VM y el servicio KMS de Azure

1. Descargue la herramienta [PSping](http:/technet.microsoft.com/sysinternals/jj729731.aspx) y extráigala en una carpeta local en la VM que no se activa. 

2. Vaya a Inicio, busque en Windows PowerShell, haga clic con el botón derecho en Windows PowerShell y seleccione Ejecutar como administrador.

3. Asegúrese de que la VM está configurada para usar el servidor de KMS de Azure correcto. Para ello, ejecute el siguiente comando:
  
    ```powershell
    Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /skms kms.core.windows.net:1688"
    ```

    El comando debe devolver: Key Management Service machine name set to kms.core.windows.net:1688 successfully (El nombre de máquina del servicio de administración de claves se estableció correctamente en kms.core.windows.net:1688).

4. Compruebe con Psping que dispone de conectividad con el servidor de KMS. Vaya a la carpeta en la que extrajo la descarga de Pstools.zip y ejecute lo siguiente:
  
    ```
    \psping.exe kms.core.windows.net:1688
    ```
   En la penúltima línea, asegúrese de que aparece: Sent = 4, Received = 4, Lost = 0 (0% loss) [Enviados = 4, Recibidos = 4, Perdidos = 0 (0 % perdidos)]

   Si el valor de perdidos es mayor que 0 (cero), la máquina virtual no tiene conectividad con el servidor de KMS server. En este caso, si la VM está en una red virtual y tiene especificado un servidor DNS personalizado, debe asegurarse de que el servidor DNS es capaz de resolver kms.core.windows.net. También puede cambiar el servidor DNS a uno que pueda resolver kms.core.windows.net.

   Tenga en cuenta que si quita todos los servidores DNS de una red virtual, las máquinas virtuales usan el servicio DNS interno de Azure. Este servicio pude resolver kms.core.windows.net.
  
    Además, asegúrese de que el tráfico de red saliente al punto de KMS con el puerto 1688 no esté bloqueado por el firewall de la máquina virtual.

5. Después de comprobar la conectividad correcta con kms.core.windows.net, ejecute el siguiente comando en un símbolo del sistema de Windows PowerShell con privilegios elevados. Este comando intenta la activación varias veces.

    ```powershell
    1..12 | ForEach-Object { Invoke-Expression "$env:windir\system32\cscript.exe $env:windir\system32\slmgr.vbs /ato" ; start-sleep 5 }
    ```

    Una activación correcta devuelve información similar a la siguiente:
    
    **Activating Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678) …  Product activated successfully.** (Activación de Windows(R), ServerDatacenter edition (12345678-1234-1234-1234-12345678) …  El producto se activó correctamente).

## <a name="faq"></a>Preguntas más frecuentes 

### <a name="i-created-the-windows-server-2016-from-azure-marketplace-do-i-need-to-configure-kms-key-for-activating-the-windows-server-2016"></a>He creado Windows Server 2016 desde Azure Marketplace. ¿Tengo que configurar la clave KMS para activar Windows Server 2016? 

 
No. La imagen de Azure Marketplace ya tiene configurada la clave de instalación de cliente KMS apropiada. 

### <a name="does-windows-activation-work-the-same-way-regardless-if-the-vm-is-using-azure-hybrid-use-benefit-hub-or-not"></a>¿La activación de Windows funciona siempre igual sin importar que la VM use la Ventaja de uso híbrido (HUB) de Azure? 

 
Sí. 
 

### <a name="what-happens-if-windows-activation-period-expires"></a>¿Qué sucede si el período de activación de Windows expira? 

 
Cuando el período de gracia ha expirado y Windows no está activado todavía, Windows Server 2008 R2 y las versiones posteriores de Windows mostrarán notificaciones adicionales sobre la activación. El fondo de pantalla del escritorio permanece negro y Windows Update solo instalará las actualizaciones críticas y de seguridad, pero no las actualizaciones opcionales. Vea la sección de notificaciones en la parte inferior de la página [Licensing Conditions](https://technet.microsoft.com/library/ff793403.aspx) (Condiciones de licencias).   

## <a name="need-help-contact-support"></a>¿Necesita ayuda? Póngase en contacto con el servicio de soporte técnico.

Si sigue necesitando ayuda, [póngase en contacto con el servicio de soporte técnico](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para resolver el problema rápidamente.
