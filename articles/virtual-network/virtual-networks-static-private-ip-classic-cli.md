---
title: 'Configuración de direcciones IP privadas para máquinas virtuales (clásicas): CLI de Azure clásica'
titlesuffix: Azure Virtual Network
description: Aprenda a configurar direcciones IP privadas para máquinas virtuales (implementación clásica) mediante la interfaz de línea de comandos (CLI) de Azure clásica.
services: virtual-network
documentationcenter: na
author: genlin
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: 2f71dc2bcd5463f81ae286bbe3099124eb3fa539
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "62107985"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-classic-cli"></a>Configuración de direcciones IP privadas para una máquina virtual (implementación clásica) mediante la CLI de Azure clásica

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artículo trata sobre el modelo de implementación clásico. También puede [administrar la dirección IP privada estática en el modelo de implementación del Administrador de recursos](virtual-networks-static-private-ip-arm-cli.md).

En los siguientes comandos de ejemplo de la CLI de Azure clásica se presupone que ya se creó un entorno simple. Si desea ejecutar los comandos que aparecen en este documento, cree primero el entorno de prueba descrito en [creación de una red virtual](virtual-networks-create-vnet-classic-cli.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Especificación de una dirección IP privada estática al crear una VM
Para crear una máquina virtual denominada *DNS01* en un nuevo servicio en la nube denominado *TestService* según el escenario anterior, siga estos pasos:

1. Si nunca ha usado la CLI de Azure, consulte [Instalación y configuración de la CLI de Azure](/cli/azure/install-cli-version-1.0) y siga las instrucciones hasta el punto donde deba seleccionar su cuenta y suscripción de Azure.
2. Ejecute el comando **azure service create** para crear el servicio en la nube.
   
        azure service create TestService --location uscentral
   
    Resultado esperado:
   
        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name TestService
        info:    service create command OK
3. Ejecute el comando **azure crear vm** para crear la VM. Tenga en cuenta el valor de una dirección IP privada estática. En la lista que se muestra en la salida se explican los parámetros utilizados.
   
        azure vm create -l centralus -n DNS01 -w TestVNet -S "192.168.1.101" TestService bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 adminuser AdminP@ssw0rd
   
    Resultado esperado:
   
        info:    Executing command vm create
        warn:    --vm-size has not been specified. Defaulting to "Small".
        info:    Looking up image bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
        info:    Looking up virtual network
        info:    Looking up cloud service
        warn:    --location option will be ignored
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Retrieving storage accounts
        info:    Creating VM
        info:    OK
        info:    vm create command OK
   
   * **-l (or --location)** . La región de Azure donde se creará la VM. En este escenario, *centralus*.
   * **-n (o --vm-name)** . Nombre de la VM que se va a crear.
   * **-w (o --virtual-network-name)** . Nombre de la red virtual donde se creará la VM. 
   * **-S (o --static-ip)** . Dirección IP privada estática para la VM.
   * **TestService**. Nombre del servicio en la nube en el que se creó la VM.
   * **bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2**. Imagen utilizada para crear la VM.
   * **adminuser**. Administrador local para la VM de Windows.
   * <strong>AdminP@ssw0rd</strong>. Contraseña del administrador local para la VM de Windows.

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Recuperación de la información de la dirección IP privada estática para una VM
Para ver la información de la dirección IP privada estática para la VM creada con el script anterior, ejecute el comando de la CLI de Azure y observe el valor para *Network StaticIP*:

    azure vm static-ip show DNS01

Resultado esperado:

    info:    Executing command vm static-ip show
    info:    Getting virtual machines
    data:    Network StaticIP "192.168.1.101"
    info:    vm static-ip show command OK

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Eliminación de una dirección IP privada estática de una VM
Para quitar la dirección IP privada estática agregada a la VM en el script anterior, ejecute los siguientes comandos de la CLI de Azure.

    azure vm static-ip remove DNS01

Resultado esperado:

    info:    Executing command vm static-ip remove
    info:    Getting virtual machines
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip remove command OK

## <a name="how-to-add-a-static-private-ip-to-an-existing-vm"></a>Adición de una IP privada estática a una VM existente
Para agregar una dirección IP privada estática a la VM creada con el script anterior, ejecute el siguiente comando:

    azure vm static-ip set DNS01 192.168.1.101

Resultado esperado:

    info:    Executing command vm static-ip set
    info:    Getting virtual machines
    info:    Looking up virtual network
    info:    Reading network configuration
    info:    Updating network configuration
    info:    vm static-ip set command OK

## <a name="set-ip-addresses-within-the-operating-system"></a>Configuración de direcciones IP en el sistema operativo

Se recomienda no asignar estáticamente la dirección IP privada asignada a la máquina virtual de Azure en el sistema operativo de una máquina virtual, a menos que sea necesario. Al establecer manualmente la dirección IP privada en el sistema operativo, asegúrese de que sea la misma que la dirección IP privada asignada a la máquina virtual de Azure, de lo contrario, perderá la conectividad a la máquina virtual. No asigne manualmente la dirección IP pública asignada a una máquina virtual de Azure en el sistema operativo de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información acerca de las [direcciones IP públicas reservadas](virtual-networks-reserved-public-ip.md) .
* Obtenga información sobre las [direcciones IP públicas a nivel de instancia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Consulte las [API de REST de IP reservada](https://msdn.microsoft.com/library/azure/dn722420.aspx).