---
title: Conexión a una máquina virtual Linux con Azure Bastion | Microsoft Docs
description: En este artículo aprenderá a conectarse a una máquina virtual Linux mediante Azure Bastion.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 69548541d16db95f633400808f72aebaf59cff08
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2019
ms.locfileid: "67477775"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Conexión mediante SSH a una máquina virtual Linux con Azure Bastion (versión preliminar)

En este artículo se muestra cómo establecer una conexión SSH segura y sin problemas con sus máquinas virtuales Linux en una red virtual de Azure. Puede conectarse a una máquina virtual directamente desde Azure Portal. Cuando se utiliza Azure Bastion, las máquinas virtuales no requieren un cliente, un agente ni software adicional. Para más información sobre Azure Bastion, consulte la página de [información general](bastion-overview.md).

Puede usar Azure Bastion para conectar a una máquina virtual Linux mediante SSH. Puede utilizar tanto el nombre de usuario y la contraseña como las claves SSH para realizar la autenticación. Puede conectarse a la máquina virtual con claves SSH mediante una de estas opciones:

* Una clave privada que se escribe manualmente.
* Un archivo que contiene la información de la clave privada.

La clave privada SSH debe tener un formato que empieza con `"-----BEGIN RSA PRIVATE KEY-----"` y finaliza con `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Esta versión preliminar pública se proporciona sin un acuerdo de nivel de servicio y no debe usarse para cargas de trabajo de producción. Puede que algunas características no se admitan, que tengan funcionalidades limitadas o que no estén disponibles en todas las ubicaciones de Azure. Para más información, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Antes de empezar

Asegúrese de haber configurado un host de Azure Bastion para la red virtual donde reside la máquina virtual. Para más información, consulte [Create an Azure Bastion host](bastion-create-host-portal.md) (Creación de un host de Azure Bastion). Cuando el servicio Bastion se aprovisiona e implementa en la red virtual, puede usarlo para conectarse a cualquier máquina virtual de esta red virtual. En esta versión preliminar, cuando se conecta mediante Bastion, se presupone que usa RDP para conectarse a una máquina virtual Windows y SSH para conectarse a las máquinas virtuales Linux.

Para crear una conexión, se requieren los siguientes roles:

* Rol de lector en la máquina virtual
* Rol de lector en la tarjeta de interfaz de red con la dirección IP privada de la máquina virtual
* Rol de lector en el recurso de Azure Bastion

## <a name="username"></a>Conexión: mediante un nombre de usuario y una contraseña


1.  Use [este vínculo](https://aka.ms/BastionHost) para abrir la página del portal de la versión preliminar de Azure Bastion. Vaya a la máquina virtual a la que quiere conectarse y, luego, haga clic en **Conectar**. La VM debe ser una máquina virtual Linux cuando se use una conexión SSH.
1. Después de hacer clic en Conectar, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Si se aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](bastion-create-host-portal.md) (Configuración de Bastion). Si no aparece **Bastion** es porque no ha abierto el portal de la versión preliminar. Abra el portal con [este vínculo](https://aka.ms/BastionHost).

      ![Conexión a la máquina virtual](./media/bastion-connect-vm-ssh/bastion.png)

1. Escriba el nombre de usuario y la contraseña para establecer una conexión SSH con la máquina virtual.
1. Haga clic en el botón **Connect** (Conectar) después de escribir la clave.

## <a name="privatekey"></a>Conexión: con una clave privada escrita

1.  Use [este vínculo](https://aka.ms/BastionHost) para abrir la página del portal de la versión preliminar de Azure Bastion. Vaya a la máquina virtual a la que quiere conectarse y, luego, haga clic en **Conectar**. La VM debe ser una máquina virtual Linux cuando se use una conexión SSH.
1. Después de hacer clic en Conectar, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Si se aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](bastion-create-host-portal.md) (Configuración de Bastion). Si no aparece **Bastion** es porque no ha abierto el portal de la versión preliminar. Abra el portal con [este vínculo](https://aka.ms/BastionHost).

      ![Conexión a la máquina virtual](./media/bastion-connect-vm-ssh/bastion.png)

1. Escriba el nombre de usuario y seleccione **Clave privada SSH**.
1. Escriba la clave privada en el área de texto **Clave privada SSH** (o péguela directamente).
1. Haga clic en el botón **Connect** (Conectar) después de escribir la clave.

## <a name="ssh"></a>Conexión: con un archivo de clave privada

1.  Use [este vínculo](https://aka.ms/BastionHost) para abrir la página del portal de la versión preliminar de Azure Bastion. Vaya a la máquina virtual a la que quiere conectarse y, luego, haga clic en **Conectar**. La VM debe ser una máquina virtual Linux cuando se use una conexión SSH.

    ![Conexión a la máquina virtual](./media/bastion-connect-vm-ssh/connect.png)

1. Después de hacer clic en Conectar, aparecerá una barra lateral con tres pestañas: RDP, SSH y Bastion. Si se aprovisionó Bastion para la red virtual, la pestaña Bastion aparece activa de manera predeterminada. Si no aprovisionó Bastion para la red virtual, consulte [Configure Bastion](bastion-create-host-portal.md) (Configuración de Bastion). Si no aparece **Bastion** es porque no ha abierto el portal de la versión preliminar. Abra el portal con [este vínculo](https://aka.ms/BastionHost).

    ![Conexión a la máquina virtual](./media/bastion-connect-vm-ssh/bastion.png)

1. Escriba el nombre de usuario y seleccione **SSH Private Key from Local File** (Clave privada SSH desde archivo local).
1. Haga clic en el botón **Examinar** (el icono de carpeta en el archivo local).
1. Busque el archivo y haga clic en **Abrir**.
1. Haga clic en **Connect** (Conectar) para conectarse a la máquina virtual. Al hacer clic en Connect (Conectar), la conexión SSH con esta máquina virtual se abrirá directamente en Azure Portal. Esta conexión se realiza a través de HTML5 mediante el puerto 443 en el servicio Bastion a través de la dirección IP privada de la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Lea el artículo sobre [preguntas frecuentes de Bastion](bastion-faq.md).
