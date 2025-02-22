---
title: Creación de una zona privada de Azure DNS con Azure Portal
description: En este tutorial se crean y se prueban una zona DNS privada y un registro en Azure DNS. Esta es una guía paso a paso para crear y administrar su primer registro y zona DNS privada con Azure Portal.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 6/15/2019
ms.author: victorh
ms.openlocfilehash: e3772d8f385ed93c96f8aa2f7ee2ded8f46d4e00
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147822"
---
# <a name="tutorial-create-an-azure-dns-private-zone-using-the-azure-portal"></a>Tutorial: Creación de una zona privada de Azure DNS con Azure Portal

Este tutorial te guiará por los pasos necesarios para crear una zona y un registro DNS privados con Azure Portal.

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

Una zona DNS se usa para hospedar los registros DNS de un dominio concreto. Para iniciar el hospedaje de su dominio en DNS de Azure, debe crear una zona DNS para ese nombre de dominio. Cada registro DNS del dominio se crea luego en esta zona DNS. Para publicar una zona DNS privada en la red virtual, especifique la lista de redes virtuales que pueden resolver registros en ella.  Se denominan redes virtuales *vinculadas*. Cuando se habilita el registro automático, Azure DNS también actualiza los registros de zona cuando se crea una máquina virtual, se cambia su dirección IP o se elimina.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una zona DNS privada
> * Creación de una red virtual
> * Vincular la red virtual
> * Creación de máquinas virtuales de prueba
> * Creación de un registro de DNS adicional
> * Prueba de la zona privada

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

Si lo prefiere, puede completar este tutorial con [Azure PowerShell](private-dns-getstarted-powershell.md) o la [CLI de Azure](private-dns-getstarted-cli.md).

## <a name="create-a-dns-private-zone"></a>Creación de una zona DNS privada

En el ejemplo siguiente, se crea una zona DNS llamada **private.contoso.com** en el grupo de recursos **MyAzureResourceGroup**.

Una zona DNS contiene las entradas de DNS para un dominio. Para iniciar el hospedaje de su dominio en Azure DNS, debe crear una zona DNS para ese nombre de dominio.

![Búsqueda de zonas DNS privadas](media/private-dns-portal/search-private-dns.png)

1. En la barra de búsqueda del portal, escribe **zonas dns privadas** en el cuadro de texto de búsqueda y presiona **ENTRAR**.
1. Selecciona **Zona DNS privada**.
2. Selecciona **Create private dns zone** (Crear zona dns privada).

1. En la página **Create Private DNS zone**, escribe o selecciona los siguientes valores:

   - **Grupo de recursos**: Selecciona **Crear nuevo**, escribe *MyAzureResourceGroup* y selecciona **Aceptar**. El nombre del grupo de recursos debe ser único dentro de la suscripción de Azure. 
   -  **Nombre**: Escribe *private.contoso.com* para este ejemplo.
1. Para **Ubicación del grupo de recursos**, selecciona **Centro-oeste de EE. UU.** .

1. Seleccione **Revisar + crear**.

1. Seleccione **Crear**.

La creación de la zona puede tardar unos minutos.

## <a name="create-a-virtual-network"></a>Creación de una red virtual

1. En la parte superior izquierda de la página del portal, selecciona **Crear un recurso**, luego, **Redes** y, luego, selecciona **Red virtual**.
2. En **Nombre**, escribe **myAzureVNet**.
3. Para **Grupo de recursos**, selecciona **myAzureResourceGroup**.
4. En **Ubicación**, selecciona **Centro-oeste de EE. UU.**
5. Acepta los otros valores predeterminados y haz clic en **Crear**.

## <a name="link-the-virtual-network"></a>Vincular la red virtual

Para vincular la zona DNS privada a una red virtual, crea un vínculo de red virtual.

![Agregar el vínculo de red virtual](media/private-dns-portal/dns-add-virtual-network-link.png)

1. Abre el grupo de recursos **MyAzureResourceGroup** y selecciona la zona privada **private.contoso.com**.
2. En el panel izquierdo, selecciona **Virtual network links** (Vínculos de red virtual).
3. Seleccione **Agregar**.
4. Escribe **myLink** para el **Nombre del vínculo**.
5. Como **Red virtual**, selecciona **myAzureVNet**.
6. Selecciona la casilla **Enable auto registration** (Habilitar registro automático).
7. Seleccione **Aceptar**.

## <a name="create-the-test-virtual-machines"></a>Creación de las máquinas virtuales de prueba

Ahora, cree dos máquinas virtuales para poder probar su zona DNS privada:

1. En la parte superior izquierda de la página del portal, selecciona **Crear un recurso** y luego selecciona **Windows Server 2016 Datacenter**.
1. Para el grupo de recursos, selecciona **MyAzureResourceGroup**.
1. Escribe **myVM01** como nombre de la máquina virtual.
1. Selecciona **Centro-oeste de EE. UU.** como **Región**.
1. Escribe **azureadmin** como nombre de usuario administrador.
2. Escribe **Azure12345678** como contraseña y confírmala.

5. En **Puerto de entrada públicos**, en **Permitir los puertos seleccionados** y, luego, selecciona **RDP (3389)** para **Seleccionar puertos de entrada**.
10. Acepta los demás valores predeterminados para la página y, luego, haz clic en **Siguiente: Discos >** .
11. Acepta los demás valores predeterminados para la página **Discos** y, luego, haz clic en **Siguiente: Redes >** .
1. Asegúrate de que **myAzureVNet** esté seleccionada para la red virtual.
1. Acepta los demás valores predeterminados para la página y, luego, haz clic en **Siguiente: Administración >** .
2. Para **Diagnósticos de arranque**, selecciona **Desactivado**, acepta los demás valores predeterminados y selecciona **Revisar y crear**.
1. Revisa la configuración y, luego, haz clic en **Crear**.

Repite estos pasos para crear otra máquina virtual denominada **myVM02**.

Ambas máquinas virtuales tardarán algunos minutos en completarse.

## <a name="create-an-additional-dns-record"></a>Creación de un registro de DNS adicional

 En el ejemplo siguiente se crea un registro con el nombre relativo **db** en la zona DNS **private.contoso.com** del grupo de recursos **MyAzureResourceGroup**. El nombre completo del conjunto de registros es **db.private.contoso.com**. El tipo de registro es "A", con la dirección IP de **myVM01**.

1. Abre el grupo de recursos **MyAzureResourceGroup** y selecciona la zona privada **private.contoso.com**.
2. Seleccione **+ Conjunto de registros**.
3. En **Nombre**, escribe **db**.
4. Para **Dirección IP**, escribe la dirección IP que viste para **myVM01**. Debería registrarse de forma automática al iniciarse la máquina virtual.
5. Seleccione **Aceptar**.

## <a name="test-the-private-zone"></a>Prueba de la zona privada

Ya puede probar la resolución de nombres de la zona privada **private.contoso.com**.

### <a name="configure-vms-to-allow-inbound-icmp"></a>Configuración de máquinas virtuales para permitir ICMP de entrada

Puede usar el comando ping para probar la resolución de nombres. Por tanto, configure el firewall en ambas máquinas virtuales para permitir paquetes ICMP entrantes.

1. Conéctese a myVM01 y abra una ventana de Windows PowerShell con privilegios de administrador.
2. Ejecute el siguiente comando:

   ```powershell
   New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
   ```

Repita la operación con myVM02.

### <a name="ping-the-vms-by-name"></a>Realización de ping en las máquinas virtuales por nombre

1. En el símbolo del sistema de Windows PowerShell de myVM02, haga ping a myVM01 con el nombre de host registrado automáticamente:
   ```
   ping myVM01.private.contoso.com
   ```
   La salida es similar a esta:
   ```
   PS C:\> ping myvm01.private.contoso.com

   Pinging myvm01.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time=1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 1ms, Average = 0ms
   PS C:\>
   ```
2. Ahora haga ping en el nombre de la **base de datos** que creó anteriormente:
   ```
   ping db.private.contoso.com
   ```
   La salida es similar a esta:
   ```
   PS C:\> ping db.private.contoso.com

   Pinging db.private.contoso.com [10.2.0.4] with 32 bytes of data:
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128
   Reply from 10.2.0.4: bytes=32 time<1ms TTL=128

   Ping statistics for 10.2.0.4:
       Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
   Approximate round trip times in milli-seconds:
       Minimum = 0ms, Maximum = 0ms, Average = 0ms
   PS C:\>
   ```

## <a name="delete-all-resources"></a>Eliminación de todos los recursos

Cuando no lo necesite, elimine el grupo de recursos **MyAzureResourceGroup** para eliminar los recursos que ha creado en este tutorial.


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha implementado una zona DNS privada, ha creado un registro de DNS y ha probado la zona.
A continuación, puede obtener más información acerca de las zonas DNS privadas.

> [!div class="nextstepaction"]
> [Uso de Azure DNS en dominios privados](private-dns-overview.md)
