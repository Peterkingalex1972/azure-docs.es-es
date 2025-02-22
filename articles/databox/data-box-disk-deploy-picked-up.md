---
title: Tutorial sobre la devolución de Azure Data Box Disk | Microsoft Docs
description: Use este tutorial para saber cómo enviar Azure Data Box Disk a Microsoft
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 1104c017541b8124366a6121763318f199f3aad5
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70126085"
---
::: zone target="chromeless"

## <a name="return-azure-data-box-disk"></a>Devolución de Azure Data Box Disk 

::: zone-end

::: zone target="docs"

# <a name="tutorial-return-azure-data-box-disk"></a>Tutorial: Devolución de Azure Data Box Disk 

En este tutorial se describe cómo programar una recogida para devolver la instancia de Azure Data Box Disk. Las instrucciones de recogida dependen de dónde se devuelva el dispositivo. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Envío de Data Box Disk a Microsoft
> * Recogida de Data Box Disk en distintas regiones

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que ha completado [Tutorial: Copia de datos a Azure Data Box Disk y comprobación de los mismos](data-box-disk-deploy-copy-data.md).


## <a name="ship-data-box-disk-back"></a>Devolución de Data Box Disk

::: zone-end

1. Una vez completada la validación de datos, desconecte los discos. Quite los cables de conexión.
2. Envuelva todos los discos y los cables de conexión en un envoltorio de burbujas y colóquelos en la caja de envío. La falta de accesorios puede acarrear un costo.
    - Utilice el empaquetado que se usó en el envío inicial.  
    - Es aconsejable usar un envoltorio de burbujas bien protegido para empaquetar los discos.
    - Asegúrese de que todo encaja perfectamente para reducir los movimientos dentro de la caja.

Los siguientes pasos vienen determinados por el lugar al que se vaya a devolver el dispositivo. Las instrucciones son diferentes para EE. UU. o Canadá, Australia o países de Asia.

- [Si devuelve el dispositivo en Estados Unidos o Canadá, programe una recogida con UPS](data-box-disk-deploy-picked-up.md#pick-up-in-us-canada).
- [En Europa, programe una recogida con DHL](data-box-disk-deploy-picked-up.md#pick-up-in-europe); para ello, visite su sitio web y especifique el número de factura de porte aéreo.
- [Programe una recogida en Australia](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up#pick-up-in-australia).
- [Programe una recogida de países de Asia](data-box-disk-deploy-picked-up.md#pick-up-in-asia), como Japón, Corea y Singapur.

::: zone target="chromeless"

Después de que el transportista recoge los discos, el estado del pedido en el portal se actualiza y se muestra un identificador de seguimiento.

::: zone-end

### <a name="pick-up-in-us-canada"></a>Recogida en EE. UU. y Canadá

Realice los pasos siguientes si va a devolver el dispositivo en Estados Unidos o Canadá.

1. Utilice la etiqueta de envío de devolución en la funda plástica transparente fijada en la caja. Si la etiqueta se daña o se pierde:
    - Vaya a **Información general > Descargar la etiqueta de envío** y descargar una etiqueta de envío de devolución.
    - Pegue la etiqueta en el dispositivo.

2. Selle la caja de envío y asegúrese de que la etiqueta de envío de devolución está visible.
3. Póngase en contacto con UPS para programar la recogida del dispositivo. Para programar una recogida:

    - Llame a la oficina local de UPS (número gratuito específico del país o región).
    - En la llamada, indique el número de seguimiento del envío inverso, que se muestra en la etiqueta impresa.
    - Si no se indica el número de seguimiento, UPS solicitará que el abono de una cantidad adicional en la recogida.
    - En lugar de programar la recogida, también puede devolver la instancia de Data Box Disk en el punto de recogida más cercano.

### <a name="pick-up-in-europe"></a>Recogida en Europa

Realice los pasos siguientes si va a devolver el dispositivo en Europa.

1. Utilice la etiqueta de envío de devolución en la funda plástica transparente fijada en la caja. Si la etiqueta se daña o se pierde:
    - Vaya a **Información general > Descargar la etiqueta de envío** y descargar una etiqueta de envío de devolución.
    - Pegue la etiqueta en el dispositivo.

2. Selle la caja de envío y asegúrese de que la etiqueta de envío de devolución está visible.
3. Si va a devolver el dispositivo en Europa con DHL, para solicitar la recogida de DHL, visite su sitio Web y especifique el número de factura de porte aéreo.
4. Visite el sitio Web de DHL Express del país o región y elija **Book a Courier Collection > eReturn Shipment** (Reservar una colección Courier > Envío eReturn).    
3. Especifique el número de factura de porte aéreo y haga clic en **Schedule Pickup** (Programar la recogida) para organizar la recogida.

### <a name="pick-up-in-australia"></a>Recogida en Australia

Los centros de datos de Azure en Australia tienen una notificación de seguridad adicional. Todos los envíos entrantes deben tener una notificación avanzada. Realice los pasos siguientes para la recogida en Australia.

1. Envíe un correo electrónico a `adbops@microsoft.com` para solicitar una etiqueta de envío con el identificador entrante único o el código TAU. Para tener la etiqueta a tiempo, realice la solicitud al menos 3 días de antelación a la fecha de envío planeada.
2. El asunto del correo electrónico debe ser: *Request for reverse shipping label with TAU code* (Solicitud de etiqueta de envío inverso con código TAU). Asegúrese de incluir los siguientes detalles en el mensaje de correo electrónico: 

    - Nombre del pedido
    - Dirección
    - Nombre de contacto

### <a name="pick-up-in-asia"></a>Recogida en Asia

Las instrucciones de recogida son diferentes para Japón, Corea y Singapur.

#### <a name="pick-up-in-japan"></a>Recogida en Japón

1. Escriba el nombre y la dirección de la empresa en la nota de entrega como información del remitente.
2. Envíe un correo electrónico a Quantium Solutions mediante la plantilla de correo electrónico que tiene a continuación.

    ```
    To: Customerservice.JP@quantiumsolutions.com
    Subject: Pickup request for Microsoft Azure Data Box Disk｜Job Name： 
    Body: 
    - Japan Post Yu-Pack tracking number (reference number)：
    - Requested pickup date：mmdd (Select a requested time slot from below).
        a. 08：00-13：00 
        b. 13：00-15：00 
        c. 15：00-17：00 
        d. 17：00-19：00 
    ```
    - **Si va a recoger en Osaka**, modifique el asunto de la plantilla de correo electrónico por `Pickup request for Microsoft Azure OSA`.
    - Tanto si no se incluyó la nota de entrega de Japan Post Chakubarai como si falta, especifíquelo en este correo electrónico. Quantium Solutions Japan se encargará de solicitar a Japan Post que le proporcionen una nota de entrega en la recogida.
    - Si tiene varios pedidos, envíe un correo electrónico para comprobar cada recogida individual.

3. Recibirá un correo electrónico de confirmación de Quantium Solutions tras concertar una recogida. Este correo electrónico también incluye información sobre la nota de entrega de Chakubarai.

Si es necesario, puede ponerse en contacto con el soporte técnico de Quantium Solutions (en japonés) en: 

- Correo electrónico: Customerservice.JP@quantiumsolutions.com 
- Teléfono：03-5755-0150 

#### <a name="pick-up-in-korea"></a>Recogida en Corea

1. Asegúrese de incluir la nota de entrega de la devolución.
2. Para solicitar la recogida con la nota de entrega:
    1. Llame a la línea directa de *Quantium Solutions International*, 070-8231-1418, en horario de oficina (de 10 AM a 5 PM, de lunes a viernes). Indique *Microsoft Azure pickup* y el número de solicitud de servicio para organizar la recogida.  
    2. Si la línea directa está ocupada, envíe un mensaje de correo electrónico a `microsoft@rocketparcel.com` con el asunto *Microsoft Azure Pickup* y el número de solicitud de servicio como referencia.
    3. Si el mensajero no realiza la recogida, llame a la línea directa de *Quantium Solutions International* para buscar otra fecha. 
    4. Recibirá una confirmación por correo electrónico para concertar la recogida.
3. Siga este paso solo si la nota de entrega no está presente. Para solicitar la recogida:
    1. Llame a la línea directa de *Quantium Solutions International*, 070-8231-1418, en horario de oficina (de 10 AM a 5 PM, de lunes a viernes). Indique *Microsoft Azure pickup* y el número de solicitud de servicio para organizar la recogida. Especifique que necesita una nueva nota de entrega para concertar una recogida. Proporcione la información sobre el remitente (cliente) y el receptor (centro de datos de Azure), así como el número de referencia (número de solicitud de servicio). 
    2. Si la línea directa está ocupada, envíe un mensaje de correo electrónico a `microsoft@rocketparcel.com` con el asunto *Microsoft Azure Pickup* y el número de solicitud de servicio como referencia.
    3. Si el mensajero no realiza la recogida, llame a la línea directa de *Quantium Solutions International* para buscar otra fecha. 
    4. Recibirá una confirmación verbal si la solicitud se realiza por teléfono.

#### <a name="pick-up-in-singapore"></a>Recogida en Singapur

1. Imprima la etiqueta de envío y póngala en la caja. Si la etiqueta se daña o se pierde:
    - Vaya a **Información general > Descargar la etiqueta de envío** y obtenga una etiqueta de envío de devolución.

        ![Descarga de la etiqueta de envío](media/data-box-disk-deploy-picked-up/download-shipping-label.png)

    - Pegue la etiqueta en el dispositivo. Asegúrese de que la etiqueta está visible.

2. Para solicitar la recogida:
    - Llame a la línea directa de **SingPost**, en el número **6845 6485**, en horario de oficina (de 9 a 17 horas de lunes a viernes).  
    - Indique *Microsoft Azure pickup* y el número de solicitud de servicio (número de seguimiento en la etiqueta de envío de la devolución) para concertar la recogida. 
    - Recibirá una confirmación verbal para la organización de la recogida. 
    - Si el mensajero no realiza la recogida, llame a **SingPost** en el **6845 6485** para concertar otra cita. 
3. Realice la entrega al mensajero. 


::: zone target="docs"

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido sobre temas relacionados con Azure Data Box Disk; por ejemplo:

> [!div class="checklist"]
> * Envío de Data Box Disk a Microsoft
> * Recogida de Data Box Disk en distintas regiones

Continúe con el siguiente procedimiento para obtener información sobre cómo comprobar la carga de datos de Data Box Disk en la cuenta de Azure Storage.

> [!div class="nextstepaction"]
> [Comprobación de la carga de datos desde Azure Data Box Disk](./data-box-disk-deploy-picked-up.md)

::: zone-end




