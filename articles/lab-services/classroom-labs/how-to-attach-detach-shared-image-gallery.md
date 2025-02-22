---
title: Asociación o desasociación de una galería de imágenes compartidas en Azure Lab Services | Microsoft Docs
description: Obtenga información sobre cómo asociar una galería de imágenes compartidas a un laboratorio en Azure Lab Services.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: de4e9fb4b15f4c346926fe46f23255c668204c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "65413889"
---
# <a name="attach-or-detach-a-shared-image-gallery-in-azure-lab-services"></a>Asociación o desasociación de una galería de imágenes compartidas en Azure Lab Services
Los profesores y administradores del laboratorio pueden guardar una imagen de máquina virtual de plantilla en una [galería de imágenes compartidas](../../virtual-machines/windows/shared-image-galleries.md) de Azure para que otros usuarios puedan reutilizarla. Como primer paso, el administrador del laboratorio asocia una galería de imágenes compartidas existente a la cuenta de laboratorio. Una vez que se ha asociado la galería de imágenes compartidas, los laboratorios creados en la cuenta de laboratorio pueden guardar imágenes en la galería de imágenes compartidas. Otros profesores pueden seleccionar esta imagen de la galería de imágenes compartidas para crear una plantilla para sus clases. 

En este artículo se muestra cómo asociar o desasociar una galería de imágenes compartidas en una cuenta de laboratorio. 

## <a name="configure-at-the-time-of-lab-account-creation"></a>Configuración al crear la cuenta de laboratorio
Al crear una cuenta de laboratorio, puede asociarle una galería de imágenes compartidas. Puede seleccionar una galería de imágenes compartidas existente en la lista desplegable o bien crear una. Para crear y adjuntar una galería de imágenes compartidas a la cuenta de laboratorio, seleccione **Crear nuevo**, escriba un nombre para la galería y seleccione **Aceptar**. 

![Configuración de la Galería de imágenes compartidas en el momento de crear la cuenta de laboratorio](../media/how-to-use-shared-image-gallery/new-lab-account.png)

## <a name="configure-after-the-lab-account-is-created"></a>Configuración después de crear la cuenta de laboratorio
Una vez que se ha creado la cuenta de laboratorio, puede realizar las tareas siguientes:

- Crear y asociar una galería de imágenes compartidas
- Asociar una galería de imágenes compartidas a la cuenta de laboratorio
- Desasociar una galería de imágenes compartidas de la cuenta de laboratorio

## <a name="create-and-attach-a-shared-image-gallery"></a>Creación y asociación de una galería de imágenes compartidas
1. Inicie sesión en el [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** en el menú de la izquierda. Seleccione **Lab Services** en la sección **DEVOPS**. Si selecciona la estrella (`*`) junto a **Lab Services**, se agrega a la sección **FAVORITOS** en el menú de la izquierda. A partir de la próxima vez, seleccione **Lab Services** en **FAVORITOS**.

    ![Todos los servicios -> Lab Services](../media/tutorial-setup-lab-account/select-lab-accounts-service.png)
3. Seleccione la cuenta de laboratorio para ver la página **Cuenta de laboratorio**. 
4. Seleccione **Galería de imágenes compartidas** en el menú de la izquierda y **+ Crear** en la barra de herramientas.  

    ![Botón para crear la galería de imágenes compartidas](../media/how-to-use-shared-image-gallery/new-shared-image-gallery-button.png)
5. En la ventana **Crear galería de imágenes compartidas**, escriba un **nombre** para la galería y seleccione **Aceptar**. 

    ![Ventana Crear galería de imágenes compartidas](../media/how-to-use-shared-image-gallery/create-shared-image-gallery-window.png)

    Azure Lab Services crea la galería de imágenes compartidas y la asocia a la cuenta de laboratorio. Todos los laboratorios creados en esta cuenta de laboratorio tienen acceso a la galería de imágenes compartidas adjunta. 

    ![Galería de imágenes adjunta](../media/how-to-use-shared-image-gallery/image-gallery-in-list.png)

    En el panel inferior, verá imágenes en la galería de imágenes compartidas. En esta galería nueva no hay ninguna imagen. Al cargar imágenes a la galería, las verá en esta página.     

    Todas las imágenes de la galería de imágenes compartidas adjunta están habilitadas de forma predeterminada. Puede habilitar o deshabilitar imágenes concretas si las selecciona en la lista y usa el botón **Enable selected images** (Habilitar imágenes seleccionadas) o **Disable selected images** (Deshabilitar imágenes seleccionadas).

## <a name="attach-an-existing-shared-image-gallery"></a>Asociación de una galería de imágenes compartidas existente
En el procedimiento siguiente se muestra cómo asociar una galería de imágenes compartidas existente a una cuenta de laboratorio. 

1. En la página **Cuenta de laboratorio**, seleccione **Galería de imágenes compartidas** en el menú de la izquierda y **Asociar** en la barra de herramientas. 

    ![Galería de imágenes compartidas: botón Agregar](../media/how-to-use-shared-image-gallery/sig-attach-button.png)
5. En la página  **	Asociar una galería de imágenes compartidas existente**, seleccione la galería de imágenes compartidas y después **Aceptar**.

    ![Selección de una galería existente](../media/how-to-use-shared-image-gallery/select-image-gallery.png)
6. Verá la pantalla siguiente: 

    ![Mi galería en la lista](../media/how-to-use-shared-image-gallery/my-gallery-in-list.png)
    
    En este ejemplo, todavía no hay ninguna imagen en la galería de imágenes compartidas.

    La identidad de Azure Lab Services se agrega como colaborador a la galería de imágenes compartidas asociada al laboratorio. Permite a profesores y administradores de TI guardar imágenes de máquina virtual en la galería de imágenes compartidas. Todos los laboratorios creados en esta cuenta de laboratorio tienen acceso a la galería de imágenes compartidas adjunta. 

    Todas las imágenes de la galería de imágenes compartidas adjunta están habilitadas de forma predeterminada. Puede habilitar o deshabilitar imágenes concretas si las selecciona en la lista y usa el botón **Enable selected images** (Habilitar imágenes seleccionadas) o **Disable selected images** (Deshabilitar imágenes seleccionadas). 

## <a name="save-an-image-to-the-shared-image-gallery"></a>Guardado de una imagen en la galería de imágenes compartidas
Una vez que se ha asociado una galería de imágenes compartidas, un administrador de cuenta de laboratorio o un profesor puede guardar o cargar una imagen a la galería de imágenes compartidas para que otros profesores la puedan reutilizar. Para obtener instrucciones sobre cómo cargar una imagen en la galería de imágenes compartidas, vea [Información general de la galería de imágenes compartidas](../../virtual-machines/windows/shared-images.md). 

> [!NOTE]
> Actualmente, la interfaz de usuario (IU) de Laboratorios educativos no permite que se guarde una imagen de laboratorio en la galería de imágenes compartidas. 

## <a name="detach-a-shared-image-gallery"></a>Desasociación de una galería de imágenes compartidas
Solo se puede asociar una galería de imágenes compartidas a un laboratorio. Si quiere asociar otra galería de imágenes compartidas, desasocie la actual antes de asociar la nueva. Para desasociar una galería de imágenes compartidas del laboratorio, seleccione **Desasociar** en la barra de herramientas y confirme la operación de desasociación. 

![Desasociación de la galería de imágenes compartidas de la cuenta de laboratorio](../media/how-to-use-shared-image-gallery/detach.png)

## <a name="next-steps"></a>Pasos siguientes
Para obtener información sobre cómo guardar una imagen de laboratorio en la galería de imágenes compartidas o cómo usar una imagen de la galería para crear una máquina virtual, vea [Procedimientos para usar la galería de imágenes compartidas](how-to-use-shared-image-gallery.md).

Para obtener más información sobre las galerías de imágenes compartidas en general, vea [Galería de imágenes compartidas](../../virtual-machines/windows/shared-image-galleries.md).
