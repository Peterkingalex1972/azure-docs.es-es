---
title: Configuración de un dispositivo para Azure Migrate Server Assessment/Migration para máquinas virtuales de VMware | Microsoft Docs
description: Describe cómo configurar un dispositivo para la detección, la valoración y la migración sin agente de máquinas virtuales de VMware mediante Azure Migrate Server Assessment/Migration.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/08/2019
ms.author: raynew
ms.openlocfilehash: fe190381df346278e75a3e6fd9876b80c33bd86b
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2019
ms.locfileid: "67810199"
---
# <a name="set-up-an-appliance-for-vmware-vms"></a>Configuración de un dispositivo para máquinas virtuales de VMware

En este artículo se describe cómo configurar el dispositivo de Azure Migrate si va a evaluar máquinas virtuales de VMware con la herramienta Azure Migrate Server Assessment o si va a migrar máquinas virtuales de VMware a Azure con la migración sin agente mediante la herramienta Azure Migrate Server Migration.

El dispositivo de máquina virtual de VMware es un dispositivo ligero que Azure Migrate Server Assessment/Migration usa para lo siguiente:

- detecta las máquinas virtuales locales de VMware.
- Enviar metadatos y datos de rendimiento para las máquinas virtuales detectadas a Azure Migrate Server Assessment/Migration.

[Más información](migrate-appliance.md) sobre el dispositivo de Azure Migrate.


## <a name="appliance-deployment-steps"></a>Pasos de implementación del dispositivo

Para configurar el dispositivo:
- Descargue una plantilla OVA e impórtela en vCenter Server.
- Crear el dispositivo y comprobar que se puede conectar a Azure Migrate Server Assessment. 
- Configurar el dispositivo por primera vez y registrarlo en el proyecto de Azure Migrate.

## <a name="download-the-ova-template"></a>Descarga de la plantilla OVA

1. En **Objetivos de migración** > **Servidores** > **Azure Migrate: Server Assessment**, haga clic en **Detectar**.
2. En **Detectar máquinas** >  **¿Las máquinas están virtualizadas?** , haga clic en **Sí, con VMware vSphere Hypervisor**.
3. Haga clic en **Descargar** para descargar el archivo de plantilla .OVA.



### <a name="verify-security"></a>Comprobación de la seguridad

Compruebe que el archivo OVA es seguro, antes de implementarlo.

1. En la máquina en la que descargó el archivo, abra una ventana de comandos de administrador.
2. Ejecute el siguiente comando para generar el código hash para el archivo OVA:
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - Ejemplo de uso: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3. El código hash generado debe coincidir con esta configuración para la versión 1.0.0.5 del dispositivo. 

  **Algoritmo** | **Valor del código hash**
  --- | ---
  MD5 | ddfdf21c64af02a222ed517ce300c977


## <a name="create-the-appliance-vm"></a>Creación de la máquina virtual del dispositivo

Importe el archivo descargado y cree una máquina virtual.

1. En la consola de cliente de vSphere, haga clic en **File** (Archivo) > **Deploy OVF Template** (Implementar plantilla de OVF).
2. En el Deploy OVF Template Wizard (Asistente para implementar la plantilla de OVF) > **Source** (Origen), especifique la ubicación del archivo OVA.
3. En **Name** (Nombre) y **Location** (Ubicación), especifique un nombre descriptivo para la máquina virtual. Seleccione el objeto de inventario en el que se hospedará la máquina virtual.
5. En **Host/Cluster** (Host o clúster), especifique el host o clúster en que se ejecutará la máquina virtual.
6. En **Storage**, especifique el destino de almacenamiento de la máquina virtual.
7. En **Disk Format** (Formato de disco), especifique el tamaño y el tipo de disco.
8. En **Network Mapping** (Asignación de red), especifique la red a la que se conectará la máquina virtual. La red necesita conectividad a Internet para enviar metadatos a Azure Migrate Server Assessment.
9. Revise y confirme la configuración y haga clic en **Finish** (Finalizar).


### <a name="verify-appliance-access-to-azure"></a>Comprobación de que el dispositivo puede acceder a Azure

Asegúrese de que la máquina virtual del dispositivo se puede conectar a las [direcciones URL de Azure](migrate-support-matrix-vmware.md#assessment-url-access-requirements).


## <a name="configure-the-appliance"></a>Configuración del dispositivo

Configure el dispositivo por primera vez.

1. En la consola del cliente de vSphere, haga clic con el botón derecho en VM > **Open Console** (Abrir consola).
2. Especifique el idioma, la zona horaria y la contraseña del dispositivo.
3. Abra un explorador en cualquier equipo que pueda conectarse a la máquina virtual y abra la dirección URL de la aplicación web del dispositivo: **https://*nombre o dirección IP del dispositivo*: 44368**.

   Como alternativa, puede abrir la aplicación desde el escritorio del dispositivo, para lo que debe hacer clic en el acceso directo de la aplicación.
4. En la aplicación web > **Set up prerequisites** (Configurar los requisitos previos ), realice las siguientes operaciones:
    - **License** (Licencia): Acepte los términos de licencia y lea la información de terceros.
    - **Connectivity** (Conectividad): la aplicación comprueba que la máquina virtual tiene acceso a Internet. Si la máquina virtual usa un proxy:
        - Haga clic en **Configuración de proxy** y especifique el puerto de escucha y la dirección del proxy con los formatos http://ProxyIPAddress o http://ProxyFQDN.
        - Especifique las credenciales si el proxy requiere autenticación.
        - Solo se admite un proxy HTTP.
    - **Time sync** (Sincronización de hora): Se comprueba la hora. Para que la detección funcione correctamente, la hora del dispositivo debe estar sincronizada con la hora de Internet.
    - **Instalación de actualizaciones**: Azure Migrate comprueba si están instaladas las últimas actualizaciones del dispositivo.
    - **Install VDDK** (Instalar VDDK): Azure Migrate comprueba si VMWare vSphere Virtual Disk Development Kit (VDDK) está instalado.
        - Azure Migrate usa VDDK para replicar las máquinas durante la migración a Azure.
        - Descargue VDDK 6.7 de VMware y extraiga el contenido del archivo zip descargado en la ubicación especificada del dispositivo.

## <a name="register-the-appliance-with-azure-migrate"></a>Registro del dispositivo en Azure Migrate

1. Haga clic en **Iniciar sesión**. Si no aparece, asegúrese de que ha deshabilitado el bloqueador de elementos emergentes en el explorador.
2. En la pestaña nueva, inicie sesión con sus credenciales de Azure. 
    - Inicie sesión con su nombre de usuario y contraseña.
    - No se admite el inicio de sesión con un PIN.
3. Después de iniciar sesión correctamente, vuelva a la aplicación web.
2. Seleccione la suscripción en la que se creó el proyecto de Azure Migrate. Seleccione el proyecto.
3. Escriba un nombre para el dispositivo. Este nombre debe ser alfanumérico y no puede tener más de 14 caracteres.
4. Haga clic en **Registrar**.


## <a name="start-continuous-discovery"></a>Inicio de detección continua

Ahora, conéctese desde el dispositivo a vCenter Server e inicie la detección de máquinas virtuales. 

1. En **Specify vCenter Server details** (Especificar detalles de vCenter Server), especifique el nombre (nombre de dominio completo) o la dirección IP de vCenter Server. Puede dejar el puerto predeterminado o especificar un puerto personalizado en el que vCenter Server escuche.
2. En **User name** (Nombre de usuario) y **Password** (Contraseña), especifique las credenciales de la cuenta de solo lectura que el dispositivo utilizará para detectar las máquinas virtuales en vCenter Server. Asegúrese de que la cuenta tenga los [permisos necesarios para la detección](migrate-support-matrix-vmware.md#assessment-vcenter-server-permissions).
3. Haga clic en **Validate connection** (Validar conexión) para asegurarse de que el dispositivo puede conectarse a vCenter Server.
4. Una vez establecida la conexión, haga clic en **Save and start discovery** (Guardar e iniciar detección).


De esta forma comienza la detección. Los metadatos de las máquinas virtuales detectadas tardan unos 15 minutos en aparecer en el portal. 


## <a name="next-steps"></a>Pasos siguientes

Revise los tutoriales para la [valoración de VMware](tutorial-assess-vmware.md) y la [migración sin agente](tutorial-migrate-vmware.md).
