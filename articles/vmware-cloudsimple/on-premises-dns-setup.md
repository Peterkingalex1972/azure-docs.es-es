---
title: 'Solución de VMware en Azure de CloudSimple: configuración de DNS para la nube privada de CloudSimple'
description: Describe cómo configurar la resolución de nombres DNS para el acceso a vCenter Server en una nube privada de CloudSimple desde estaciones de trabajo en el entorno local.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0246d667f93ac98f345a50ee0d7a7001a6b3195
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536212"
---
# <a name="configure-dns-for-name-resolution-for-private-cloud-vcenter-access-from-on-premises-workstations"></a>Configuración de DNS para la resolución de nombres en el acceso a vCenter de la nube privada desde estaciones de trabajo en el entorno local

Para acceder a vCenter Server en una nube privada de CloudSimple desde estaciones de trabajo en el entorno local, debe configurar la resolución de direcciones DNS para que las de vCenter Server se puedan resolver por el nombre de host, además de por la dirección IP.

## <a name="obtain-the-ip-address-of-the-dns-server-for-your-private-cloud"></a>Obtención de la dirección IP del servidor DNS para la nube privada

1. Inicie sesión en el [portal de CloudSimple](access-cloudsimple-portal.md).

2. Vaya a **Recursos** > **Nubes privadas** y seleccione la nube privada a la que desee conectarse.

3. En la página **Resumen** de la nube privada, en **Información básica**, copie la dirección IP del servidor DNS de la nube privada.

    ![Servidores DNS de la nube privada](media/private-cloud-dns-server.png)


Use cualquiera de estas opciones para la configuración de DNS.

* [Cree una zona en el servidor DNS para *.cloudsimple.io](#create-a-zone-on-a-microsoft-windows-dns-server)
* [Cree un reenviador condicional en el servidor DNS del entorno local para resolver *.cloudsimple.io](#create-a-conditional-forwarder)

## <a name="create-a-zone-on-the-dns-server-for-cloudsimpleio"></a>Creación de una zona en el servidor DNS para *.cloudsimple.io

Puede configurar una zona como zona de rutas internas y apuntar a los servidores DNS de la nube privada para la resolución de nombres. En esta sección se proporciona información sobre el uso de un servidor DNS BIND o un servidor DNS de Microsoft Windows.

### <a name="create-a-zone-on-a-bind-dns-server"></a>Creación de una zona en un servidor DNS BIND

El archivo específico y los parámetros que se van a configurar pueden variar en función de la configuración de DNS individual.

Por ejemplo, para la configuración predeterminada del servidor BIND, edite el archivo /etc/named.conf en el servidor DNS y agregue la siguiente información de zona.

```
zone “cloudsimple.io”
{
    type stub;
    masters { IP address of DNS servers; };
    file “slaves/cloudsimple.io.db”;
};
```

### <a name="create-a-zone-on-a-microsoft-windows-dns-server"></a>Creación de una zona en un servidor DNS de Microsoft Windows

1. Haga clic con el botón derecho en el servidor DNS y seleccione **Nueva zona**.  
![Nueva zona](media/DNS01.png)
2. Seleccione **Zona de rutas internas** y haga clic en **Siguiente**.
![Nueva zona](media/DNS02.png)
3. Seleccione la opción adecuada en función de su entorno y haga clic en **Siguiente**.
![Nueva zona](media/DNS03.png)
4. Seleccione **Zona de búsqueda directa** y haga clic en **Siguiente**.
![Nueva zona](media/DNS01.png)
5. Escriba el nombre de la zona y haga clic en **Siguiente**.
![Nueva zona](media/DNS05.png)
6. Escriba las direcciones IP de los servidores DNS de la nube privada que obtuvo en el portal de CloudSimple.
![Nueva zona](media/DNS06.png)
7. Haga clic en **Siguiente** cuando sea necesario para completar la instalación del asistente.

## <a name="create-a-conditional-forwarder"></a>Creación de un reenviador condicional

Un reenviador condicional reenvía todas las solicitudes de resolución de nombres DNS al servidor designado. Con esta configuración, cualquier solicitud a *. cloudsimple.io se reenvía a los servidores DNS ubicados en la nube privada. En los siguientes ejemplos se muestra cómo configurar reenviadores en diferentes tipos de servidores DNS.

### <a name="create-a-conditional-forwarded-on-a-bind-dns-server"></a>Creación de un reenviador condicional en un servidor DNS BIND

El archivo específico y los parámetros que se van a configurar pueden variar en función de la configuración de DNS individual.

Por ejemplo, para la configuración predeterminada del servidor BIND, edite el archivo /etc/named.conf en el servidor DNS y agregue la siguiente información de reenvío condicional.

```
zone “cloudsimple.io” {
    type forward;
    forwarders { IP address of DNS servers; };
};
```

### <a name="create-a-conditional-forwarder-on-a-microsoft-windows-dns-server"></a>Creación de un reenviador condicional en un servidor DNS de Microsoft Windows

1. Abra el Administrador de DNS en el servidor DNS.
2. Haga clic con el botón derecho en **Reenviadores condicionales** y seleccione la opción para agregar uno nuevo.
![DNS de Windows para el reenviador condicional 1](media/DNS08.png)
3. Escriba el dominio DNS y la dirección IP de los servidores DNS en la nube privada y haga clic en **Aceptar**.
