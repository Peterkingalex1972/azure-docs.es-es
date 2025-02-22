---
title: 'Tutorial: Implementación y configuración de Azure Firewall mediante Azure Portal'
description: En este tutorial, aprenderá a implementar y configurar Azure Firewall mediante Azure Portal.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 0892bde09891d2edbd7f8cc8715ccc0d2f047ed4
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70113473"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Tutorial: Implementación y configuración de Azure Firewall mediante Azure Portal

El control del acceso de red saliente es una parte importante de un plan de seguridad de red de ámbito general. Por ejemplo, es posible que desee limitar el acceso a sitios web. O bien, que desee limitar las direcciones IP de salida y los puertos a los que se puede acceder.

Una manera de controlar el acceso de red saliente desde una subred de Azure es con Azure Firewall. Con Azure Firewall, puede configurar:

* Reglas de aplicación que definen los nombres de dominio completos (FQDN) a los que se puede acceder desde una subred.
* Reglas de red que definen la dirección de origen, el protocolo, el puerto de destino y la dirección de destino.

El tráfico está sujeto a las reglas de firewall configuradas cuando enruta el tráfico al firewall como puerta de enlace predeterminada de la subred.

En este tutorial, creará una red virtual única simplificada con tres subredes para facilitar la implementación. Para las implementaciones de producción, se recomienda un [modelo de concentrador y radio](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke), en el que el firewall está en su propia red virtual. Los servidores de las cargas de trabajo están en redes virtuales emparejadas en la misma región con una o varias subredes.

* **AzureFirewallSubnet**: el firewall está en esta subred.
* **Workload-SN**: el servidor de carga de trabajo está en esta subred. El tráfico de red de esta subred va a través del firewall.
* **Jump-SN**: el servidor de "salto" está en esta subred. El servidor de salto tiene una dirección IP pública a la que puede conectarse mediante Escritorio remoto. Desde allí se puede conectar posteriormente al servidor de carga de trabajo (mediante otro Escritorio remoto).

![Infraestructura de red del tutorial](media/tutorial-firewall-rules-portal/Tutorial_network.png)

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar un entorno de red de prueba
> * Implementar un firewall
> * Crear una ruta predeterminada
> * Configurar una regla de aplicación para permitir el acceso a www.google.com
> * Configuración de una regla de red para permitir el acceso a los servidores DNS externos
> * Probar el firewall

Si lo prefiere, puede seguir los pasos de este tutorial mediante [Azure PowerShell](deploy-ps.md).

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="set-up-the-network"></a>Configuración de la red

En primer lugar, cree un grupo de recursos para que contenga los recursos necesarios para implementar el firewall. A continuación, cree una red virtual, subredes y pruebe los servidores.

### <a name="create-a-resource-group"></a>Crear un grupo de recursos

El grupo de recursos contiene todos los recursos necesarios para el tutorial.

1. Inicie sesión en Azure Portal en [https://portal.azure.com](https://portal.azure.com).
2. En la página principal de Azure Portal, seleccione **Grupos de recursos** > **Agregar**.
3. En **Nombre del grupo de recursos**, escriba **Test-FW-RG**.
4. En **Suscripción**, seleccione la suscripción.
5. En **Ubicación del grupo de recursos**: seleccione una ubicación. Todos los recursos posteriores que cree deben estar en la misma ubicación.
6. Seleccione **Crear**.

### <a name="create-a-vnet"></a>Creación de una red virtual

Esta red virtual contiene tres subredes.

> [!NOTE]
> El tamaño de la subred AzureFirewallSubnet es /26. Para más información sobre el tamaño de la subred, consulte [Preguntas más frecuentes sobre Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

1. En la página principal de Azure Portal, seleccione **Crear un recurso**.
2. En **Redes**, seleccione **Red virtuales**.
4. En **Nombre**, escriba **Test-FW-VN**.
5. En **Espacio de direcciones**, escriba **10.0.0.0/16**.
6. En **Suscripción**, seleccione la suscripción.
7. En **Grupo de recursos**, seleccione **Test-FW-RG**.
8. En **Ubicación**, seleccione la misma ubicación que usó anteriormente.
9. En **Subred**, como **Nombre** escriba **AzureFirewallSubnet**. El firewall estará en esta subred y el nombre de la subred **debe** ser AzureFirewallSubnet.
10. En **Intervalo de direcciones**, escriba **10.0.1.0/26**.
11. Acepte los restantes valores predeterminados y seleccione **Crear**.

### <a name="create-additional-subnets"></a>Creación de subredes adicionales

A continuación, cree las subredes para el servidor de salto y una subred para los servidores de cargas de trabajo.

1. En la página principal de Azure Portal, seleccione **Grupos de recursos** > **Test-FW-RG**.
2. Seleccione la red virtual **Test-FW-VN**.
3. Seleccione **Subredes** >  **+Subred**.
4. En **nombre**, escriba **Workload-SN**.
5. En **Intervalo de direcciones**, escriba **10.0.2.0/24**.
6. Seleccione **Aceptar**.

Cree otra subred denominada **Jump-SN**, con un intervalo de direcciones **10.0.3.0/24**.

### <a name="create-virtual-machines"></a>Creación de máquinas virtuales

Ahora cree las máquinas virtuales de salto y de cargas de trabajo, y colóquelas en las subredes adecuadas.

1. En Azure Portal, seleccione **Crear un recurso**.
2. Haga clic en **Compute** y, a continuación, seleccione **Windows Server 2016 Datacenter** en la lista de destacados.
3. Especifique estos valores para la máquina virtual:

   |Configuración  |Valor  |
   |---------|---------|
   |Resource group     |**Test-FW-RG**|
   |Nombre de la máquina virtual     |**Srv-Jump**|
   |Region     |Igual que la anterior|
   |Nombre de usuario del administrador     |**azureuser**|
   |Contraseña     |**Azure123456!**|

4. En **Reglas de puerto de entrada**, en **Puertos de entrada públicos**, seleccione **Permitir los puertos seleccionados**.
5. En **Seleccionar puertos de entrada**, seleccione **RDP (3389)** .

6. Acepte los restantes valores predeterminados y seleccione **Siguiente: Discos**.
7. Acepte los valores predeterminados del disco y seleccione **Siguiente: Redes**.
8. Asegúrese de que **Test-FW-VN** está seleccionada como red virtual y que la subred es **Jump-SN**.
9. En **Dirección IP pública**, acepte el nombre predeterminado de la nueva dirección IP pública (Srv-Jump-ip).
11. Acepte los restantes valores predeterminados y seleccione **Siguiente: Administración**.
12. Seleccione **Desactivar** para deshabilitar los diagnósticos de arranque. Acepte los restantes valores predeterminados y seleccione **Revisar y crear**.
13. Revise la configuración en la página de resumen y seleccione **Crear**.

Use la información de la tabla siguiente para configurar otra máquina virtual llamada **Srv-Work**. El resto de la configuración es la misma que la de la máquina virtual Srv-Jump.

|Configuración  |Valor  |
|---------|---------|
|Subnet|**Workload-SN**|
|Dirección IP pública|**None**|
|Puertos de entrada públicos|**None**|

## <a name="deploy-the-firewall"></a>Implementación del firewall

Implemente el firewall en la red virtual.

1. En la página principal de Azure Portal, seleccione **Crear un recurso**.
2. Escriba **firewall** en el cuadro de búsqueda y presione **Entrar**.
3. Seleccione **Firewall** y después **Crear**.
4. En la página **Creación de un firewall**, utilice la tabla siguiente para configurar el firewall:

   |Configuración  |Valor  |
   |---------|---------|
   |Subscription     |\<su suscripción\>|
   |Resource group     |**Test-FW-RG** |
   |NOMBRE     |**Test-FW01**|
   |Location     |Seleccione la misma ubicación que usó anteriormente.|
   |Elegir una red virtual     |**Usar existente**: **Test-FW-VN**|
   |Dirección IP pública     |**Cree uno nuevo**. La dirección IP pública tiene que ser del tipo de SKU estándar.|

5. Seleccione **Revisar + crear**.
6. Revise el resumen y seleccione **Crear** para crear el firewall.

   La implementación tardará varios minutos.
7. Una vez finalizada la implementación, vaya al grupo de recursos **Test-FW-RG** y seleccione el firewall **Test-FW01**.
8. Anote la dirección IP privada. Se usará más adelante al crear la ruta predeterminada.

## <a name="create-a-default-route"></a>Crear una ruta predeterminada

En la subred **Workload-SN**, configure la ruta predeterminada de salida que pase por el firewall.

1. En la página principal de Azure Portal, seleccione **Todos los servicios**.
2. En **Redes**, seleccione **Tablas de rutas**.
3. Seleccione **Agregar**.
4. En **Nombre**, escriba **Firewall-route**.
5. En **Suscripción**, seleccione la suscripción.
6. En **Grupo de recursos**, seleccione **Test-FW-RG**.
7. En **Ubicación**, seleccione la misma ubicación que usó anteriormente.
8. Seleccione **Crear**.
9. Seleccione **Actualizar**y, después, seleccione la tabla de rutas **Firewall-route**.
10. Seleccione **Subredes** y, después, seleccione **Asociar**.
11. Seleccione **Red virtual** > **FW-Test-VN**.
12. En **Subred**, seleccione **Workload-SN**. Asegúrese de seleccionar únicamente la subred **Workload-SN** para esta ruta o el firewall no funcionará correctamente.

13. Seleccione **Aceptar**.
14. Seleccione **Rutas** y después **Agregar**.
15. En **Nombre de ruta**, escriba **fw-dg**.
16. En **Prefijo de dirección** escriba **0.0.0.0/0**.
17. En **Tipo del próximo salto**, seleccione **Aplicación virtual**.

    Azure Firewall es realmente un servicio administrado, pero una aplicación virtual funciona en esta situación.
18. En **Dirección del próximo salto**, escriba la dirección IP privada del firewall que anotó anteriormente.
19. Seleccione **Aceptar**.

## <a name="configure-an-application-rule"></a>Configuración de una regla de aplicación

Se trata de la regla de aplicación que permite el acceso saliente a www.google.com.

1. Abra **Test-FW-RG**y seleccione el firewall **Test-FW01**.
2. En la página **Test-FW01**, en **Configuración**, seleccione **Reglas**.
3. Seleccione la pestaña **Recopilación de reglas de aplicación**.
4. Seleccione **Agregar una colección de reglas de aplicación**.
5. En **Nombre**, escriba **App-Coll01**.
6. En **Priority**, escriba **200**.
7. En **Acción**, seleccione **Permitir**.
8. En **Reglas**, **FQDN de destino**, como **Nombre** escriba **Allow-Google**.
9. En **Direcciones de origen**, escriba **10.0.2.0/24**.
10. En **Protocolo:Puerto**, escriba **http, https**.
11. En **FQDN de destino**, escriba **www.google.com**
12. Seleccione **Agregar**.

Azure Firewall incluye una colección de reglas integradas para FQDN de infraestructura que están permitidos de forma predeterminada. Estos FQDN son específicos para la plataforma y no se pueden usar para otros fines. Para más información, consulte [Nombres de dominio completos de infraestructura](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Configurar una regla de red

Se trata de la regla de red que permite el acceso saliente a dos direcciones IP en el puerto 53 (DNS).

1. Seleccione la pestaña **Recopilación de reglas de red**.
2. Seleccione **Agregar una colección de reglas de red** .
3. En **Nombre**, escriba **Net-Coll01**.
4. En **Priority**, escriba **200**.
5. En **Acción**, seleccione **Permitir**.

6. En **Reglas**, en **Nombre**, escriba **Allow-DNS**.
7. En **Protocolo**, seleccione **UDP**.
8. En **Direcciones de origen**, escriba **10.0.2.0/24**.
9. Como dirección de destino, escriba **209.244.0.3,209.244.0.4**
10. En **Puertos de destino**, escriba **53**.
11. Seleccione **Agregar**.

### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Cambio de la dirección DNS principal y secundaria para la interfaz de red **Srv-Work**

Con fines de prueba para este tutorial, configure las direcciones DNS principal y secundaria del servidor. Esto no es un requisito general de Azure Firewall.

1. En Azure Portal, abra el grupo de recursos **Test-FW-RG**.
2. Seleccione la interfaz de red de la máquina virtual **Srv-Work**.
3. En **Configuración**, seleccione **Servidores DNS**.
4. En **Servidores DNS**, seleccione **Personalizado**.
5. Escriba **209.244.0.3** en el cuadro de texto **Agregar servidor DNS** y **209.244.0.4** en el siguiente cuadro de texto.
6. Seleccione **Guardar**.
7. Reinicie la máquina virtual **Srv-Work**.

## <a name="test-the-firewall"></a>Probar el firewall

Ahora, pruebe el firewall para confirmar que funciona según lo previsto.

1. En Azure Portal, revise la configuración de red de la máquina virtual **Srv-Work** y anote la dirección IP privada.
2. Conecte un escritorio remoto a la máquina virtual **Srv-Jump** e inicie sesión. Desde ahí, abra una conexión del escritorio remoto a la dirección IP privada de **Srv-Work**.

3. Abra Internet Explorer y vaya a https://www.google.com.
4. Seleccione **Aceptar** > **Cerrar** en las alertas de seguridad de Internet Explorer.

   Debería ver la página principal de Google.

5. Vaya a https://www.microsoft.com.

   El firewall debería bloquearle.

Con ello, ha comprobado que las reglas de firewall funcionan:

* Puede navegar al FQDN permitido pero no a ningún otro.
* Puede resolver nombres DNS con el servidor DNS externo configurado.

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede conservar los recursos relacionados con el firewall para el siguiente tutorial o, si ya no los necesita, eliminar el grupo de recursos **Test-FW-RG** para eliminarlos todos.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tutorial: Supervisión de los registros de Azure Firewall](./tutorial-diagnostics.md)
