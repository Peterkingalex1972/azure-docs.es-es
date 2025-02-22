---
title: Conexión de datos CEF a la versión preliminar de Azure Sentinel | Microsoft Docs
description: Aprenda a conectar datos CEF a Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: cbf5003b-76cf-446f-adb6-6d816beca70f
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2019
ms.author: rkarlin
ms.openlocfilehash: 28def73926294a025d70844e535a0856153ae30a
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2019
ms.locfileid: "69611935"
---
# <a name="connect-your-external-solution-using-common-event-format"></a>Conexión de su solución externa con Common Event Format

> [!IMPORTANT]
> Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Puede conectar Azure Sentinel con una solución externa que le permite guardar archivos de registro en Syslog. Si su dispositivo le permite guardar registros como Common Event Format (CEF) de Syslog, la integración con Azure Sentinel permite ejecutar fácilmente análisis y consultas en los datos.

> [!NOTE] 
> Los datos se almacenan en la ubicación geográfica del área de trabajo en la que se ejecute Azure Sentinel.

## <a name="how-it-works"></a>Cómo funciona

La conexión entre Azure Sentinel y su dispositivo CEF se lleva a cabo en tres pasos:

1. Debe establecer estos valores en el dispositivo para que envíe los registros necesarios en el formato necesario al agente de Syslog de Azure Sentinel, según Microsoft Monitoring Agent. Puede modificar estos parámetros en su dispositivo, siempre y cuando modifique también el demonio de Syslog en el agente de Azure Sentinel.
    - Protocolo = UDP
    - Puerto = 514
    - Recurso = Local4
    - Formato = CEF
2. El agente de Syslog recopila los datos y los envía de forma segura a Log Analytics, donde se analizan y enriquecen.
3. El agente almacena los datos en un área de trabajo de Log Analytics, por lo que pueden consultarse según sea necesario, mediante análisis, reglas de correlación y paneles.

> [!NOTE]
> El agente puede recopilar registros de varios orígenes, pero debe estar instalado en el equipo dedicado.


 ![CEF en Azure](./media/connect-cef/cef-syslog-azure.png)

También puede implementar el agente manualmente en una máquina virtual existente, en una máquina virtual en otra nube o en una máquina local. 

 ![CEF local](./media/connect-cef/cef-syslog-onprem.png)

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Asegúrese de configurar la seguridad de la máquina de acuerdo con la directiva de seguridad de su organización. Por ejemplo, puede configurar la red para que se alinee con la directiva de seguridad de la red corporativa y cambiar los puertos y protocolos del demonio para que se adapten a sus requisitos. Puede usar las siguientes instrucciones para mejorar la configuración de seguridad de la máquina:   [Protección de máquinas virtuales en Azure](../virtual-machines/linux/security-policy.md), [Procedimientos recomendados de seguridad de la red](../security/fundamentals/network-best-practices.md).

Para usar la comunicación TLS entre la solución de seguridad y la máquina de Syslog, debe configurar el demonio de Syslog (rsyslog o syslog-ng) para que se comunique en TLS: [Cifrado del tráfico de Syslog con TLS -rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html), [Cifrado de los mensajes de registro con TLS –syslog-ng](https://support.oneidentity.com/technical-documents/syslog-ng-open-source-edition/3.22/administration-guide/60#TOPIC-1209298).


## <a name="step-1-configure-your-syslog-vm"></a>Paso 1: Configuración de la VM de Syslog

Para conectar su dispositivo Fortinet a Azure Sentinel, implemente un agente en una máquina virtual dedicada o máquina local para admitir la comunicación entre el dispositivo y Azure Sentinel. 

> [!NOTE]
> Asegúrese de configurar la seguridad de la máquina de acuerdo con la directiva de seguridad de su organización. Por ejemplo, puede configurar la red para que se alinee con la directiva de seguridad de la red corporativa y cambiar los puertos y protocolos del demonio para que se adapten a sus requisitos. 


1. En el portal de Azure Sentinel, haga clic en **Data connectors** (Conectores de datos) y seleccione **Common Event Format (CEF)** y, luego, **Open connector page** (Abrir página de conectores). 

1. En **Download and install the Syslog agent** (Descargar e instalar el agente de Syslog), seleccione el tipo de máquina, ya sea en Azure o local. 
1. En la pantalla **Máquinas virtuales** que se abre, seleccione la máquina que quiere usar y haga clic en **Conectar**.
1. Si elige **Download and install agent for Azure Linux virtual machines** (Descargar e instalar el agente para máquinas virtuales Linux de Azure), seleccione la máquina y haga clic en **Conectar**. Si eligió **Download and install agent for non-Azure Linux virtual machines** (Descargar e instalar el agente para máquinas virtuales Linux que no son de Azure), en la pantalla de **Agente directo**, ejecute el script en **Descargar e incorporar Agente para Linux**.
1. En la pantalla del conector CEF, en **Configure and forward Syslog** (Configurar y reenviar a Syslog), establezca si su demonio de Syslog es **rsyslog.d** o **syslog-ng**. 
1. Copie estos comandos y ejecútelos en su dispositivo:
    - Si seleccionó rsyslog.d:
              
       1. Indique al demonio Syslog que escuche en el recurso local_4 y envíe los mensajes de Syslog al agente de Azure Sentinel mediante el puerto 25226. `sudo bash -c "printf 'local4.debug  @127.0.0.1:25226' > /etc/rsyslog.d/security-config-omsagent.conf"`
            
       2. Descargue e instale el [archivo de configuración security_events](https://aka.ms/asi-syslog-config-file-linux) que configura el agente de Syslog para que escuche en el puerto 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` en que {0} debe reemplazarse por el GUID del área de trabajo.
            
       1. Reinicio del demonio Syslog `sudo service rsyslog restart`.<br> Para más información, consulte la [documentación de rsyslog](https://www.rsyslog.com/doc/v8-stable/tutorials/tls_cert_summary.html).
           
    - Si seleccionó syslog-ng:
       1. Indique al demonio Syslog que escuche en el recurso local_4 y envíe los mensajes de Syslog al agente de Azure Sentinel mediante el puerto 25226. `sudo bash -c "printf 'filter f_local4_oms { facility(local4); };\n  destination security_oms { tcp(\"127.0.0.1\" port(25226)); };\n  log { source(src); filter(f_local4_oms); destination(security_oms); };' > /etc/syslog-ng/security-config-omsagent.conf"`
       2. Descargue e instale el [archivo de configuración security_events](https://aka.ms/asi-syslog-config-file-linux) que configura el agente de Syslog para que escuche en el puerto 25226. `sudo wget -O /etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"` en que {0} debe reemplazarse por el GUID del área de trabajo.

        3. Reinicio del demonio Syslog `sudo service syslog-ng restart` <br>Para más información, consulte la [documentación de syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/mutual-authentication-using-tls/2).
1. Reinicie el agente de Syslog mediante este comando: `sudo /opt/microsoft/omsagent/bin/service_control restart [{workspace GUID}]`
1. Confirme que no hay ningún error en el registro del agente ejecutando este comando: `tail /var/opt/microsoft/omsagent/log/omsagent.log`

Para usar el esquema correspondiente en Log Analytics para los eventos CEF, busque `CommonSecurityLog`.

## <a name="step-2-forward-common-event-format-cef-logs-to-syslog-agent"></a>Paso 2: Reenviar los registros de Common Event Format (CEF) al agente de Syslog

Establezca su solución de seguridad para enviar mensajes de Syslog en formato CEF a su agente de Syslog. Asegúrese de usar los mismos parámetros que aparecen en la configuración del agente. Por lo general, son:

- Puerto 514
- Recurso local4

## <a name="step-3-validate-connectivity"></a>Paso 3: Validar conectividad

Hasta que los registros empiecen a aparecer en Log Analytics, pueden transcurrir más de 20 minutos. 

1. Asegúrese de usar el recurso adecuado. El recurso debe ser el mismo en su dispositivo y en Azure Sentinel. Puede comprobar qué archivo de recurso usa en Azure Sentinel y modificarlo en el archivo `security-config-omsagent.conf`. 

2. Asegúrese de que sus registros tienen acceso al puerto adecuado en el agente de Syslog. Ejecute este comando en la máquina del agente de Syslog: `tcpdump -A -ni any  port 514 -vv` Este comando le muestra los registros que se transmiten a la máquina Syslog desde el dispositivo. Asegúrese de que los registros se reciben del dispositivo de origen en el puerto y el recurso adecuados.

3. Asegúrese de que los registros que envía cumplen con [RFC 3164](https://tools.ietf.org/html/rfc3164).

4. En el equipo que ejecuta el agente de Syslog, asegúrese de que los puertos 514 y 25226 están abiertos y escuchan mediante el comando `netstat -a -n:`. Para obtener más información sobre cómo usar este comando, consulte la [página man de Linux netstat(8)](https://linux.die.net/man/8/netstat). Si escucha correctamente, verá:

   ![Puertos de Azure Sentinel](./media/connect-cef/ports.png) 

5. Asegúrese de que el demonio se establece en el puerto 514, donde envía los registros.
    - Para rsyslog:<br>Asegúrese de que el archivo `/etc/rsyslog.conf` incluye esta configuración:

           # provides UDP syslog reception
           module(load="imudp")
           input(type="imudp" port="514")
        
           # provides TCP syslog reception
           module(load="imtcp")
           input(type="imtcp" port="514")

      Para obtener más información, consulte las páginas [imudp: UDP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imudp.html#imudp-udp-syslog-input-module) (imudp: módulo de entrada de Syslog de UDP) e [imtcp: TCP Syslog Input Module](https://www.rsyslog.com/doc/v8-stable/configuration/modules/imtcp.html#imtcp-tcp-syslog-input-module) (imtcp: módulo de entrada de Syslog de TCP).

   - Para syslog-ng:<br>Asegúrese de que el archivo `/etc/syslog-ng/syslog-ng.conf` incluye esta configuración:

           # source s_network {
            network( transport(UDP) port(514));
             };
     Para más información, consulte la [guía de administración de la edición Open Source Edition 3.16 de syslog-ng](https://www.syslog-ng.com/technical-documents/doc/syslog-ng-open-source-edition/3.16/administration-guide/19#TOPIC-956455).

1. Compruebe que haya comunicación entre el demonio Syslog y el agente. Ejecute este comando en la máquina del agente de Syslog: `tcpdump -A -ni any  port 25226 -vv` Este comando le muestra los registros que se transmiten a la máquina Syslog desde el dispositivo. Asegúrese de que los registros también se reciben en el agente.

6. Si ambos comandos proporcionaron resultados correctos, compruebe Log Analytics para ver si llegan sus registros. Todos los eventos transmitidos desde estos dispositivos aparecen con formato sin procesar en Log Analytics en el tipo `CommonSecurityLog`.

7. Para comprobar si hay errores o si los registros no llegan, eche un vistazo a `tail /var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log`. Si indica que hay errores de coincidencia de formato de registro, vaya a `/etc/opt/microsoft/omsagent/{0}/conf/omsagent.d/security_events.conf "https://aka.ms/syslog-config-file-linux"`, examine el archivo `security_events.conf` y asegúrese de que los registros coinciden con el formato regex que ve en este archivo.

8. Asegúrese de que el tamaño predeterminado de los mensajes de Syslog se limita a 2048 bytes (2 KB). Si los registros son demasiado largos, actualice security_events.conf mediante este comando: `message_length_limit 4096`.


## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar dispositivos CEF a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](quickstart-get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](tutorial-detect-threats.md).

