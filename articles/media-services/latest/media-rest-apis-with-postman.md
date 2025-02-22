---
title: Configuración de Postman para llamadas API de REST de Azure Media Services
description: Aprenda cómo configurar Postman para llamadas API de REST de Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2019
ms.author: juliako
ms.openlocfilehash: a2171ff8a4354a59ec2f790f9bf38b7a687419ca
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "60322556"
---
# <a name="configure-postman-for-media-services-rest-api-calls"></a>Configuración de Postman para llamadas API de REST de Azure Media Services

En este artículo se muestra cómo configurar **Postman** para que se pueda usar para llamar a las API REST de Azure Media Services (AMS). El artículo muestra cómo importar archivos de entorno y colección a **Postman**. La colección contiene definiciones agrupadas de solicitudes HTTP que llaman a API de REST de Azure Media Services. El archivo de entorno contiene variables que la colección usa.

Antes de comenzar a desarrollar, revise [Desarrollo con las API de Media Services v3](media-services-apis-overview.md).

## <a name="prerequisites"></a>Requisitos previos

- [Cree una cuenta de Media Services](create-account-cli-how-to.md). Asegúrese de recordar el nombre del grupo de recursos y el nombre de la cuenta de Media Services. 
- Obtenga la información que necesita para [acceder a las API](access-api-cli-how-to.md)
- Instale el cliente de REST de [Postman](https://www.getpostman.com/) para ejecutar las API de REST mostradas en algunos de los tutoriales de REST de AMS. 

    Usamos **Postman** pero cualquier herramienta de REST sería adecuada. Otras alternativas son: **Visual Studio Code** con el complemento de REST o **Telerik Fiddler**. 

## <a name="download-postman-files"></a>Descarga de archivos Postman

Clone un repositorio de GitHub que contenga los archivos de recopilación y entorno de Postman.

 ```bash
 git clone https://github.com/Azure-Samples/media-services-v3-rest-postman.git
 ```

## <a name="configure-postman"></a>Configuración de Postman

En esta sección se configura Postman.

### <a name="configure-the-environment"></a>Configuración del entorno 

1. Abra **Postman**.
2. A la derecha de la pantalla, seleccione la opción **Manage Environments** (Administrar entornos).

    ![Administración del entorno](./media/develop-with-postman/postman-import-env.png)
4. En el cuadro de diálogo **Manage Environments** (Administrar entornos), haga clic en **Import** (Importar).
2. Vaya al archivo `Azure Media Service v3 Environment.postman_environment.json` que se descargó cuando clonó `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`.
6. Se agrega el entorno **Azure Media Service v3 Environment**.

    > [!Note]
    > Actualice las variables de acceso con los valores que obtuvo de la sección **Acceso a la API de Media Services** anterior.

7. Haga doble clic en el archivo seleccionado y escriba los valores que obtuvo al seguir los pasos descritos en Acceso a la API.
8. Cierre el cuadro de diálogo.
9. Seleccione el entorno **Azure Media Service v3 Environment** en la lista desplegable.

    ![Selección del entorno](./media/develop-with-postman/choose-env.png)
   
### <a name="configure-the-collection"></a>Configuración de la colección

1. Haga clic en **Import** (Importar) para importar el archivo de la colección.
1. Vaya al archivo `Media Services v3.postman_collection.json` que se descargó cuando clonó `https://github.com/Azure-Samples/media-services-v3-rest-postman.git`
3. Elija el archivo **Media Services v3.postman_collection.json**.

    ![Importación de un archivo](./media/develop-with-postman/postman-import-collection.png)

## <a name="get-azure-ad-token"></a>Obtención del token de Azure AD 

Antes de empezar a manipular recursos de AMS v3, debe obtener y establecer el token de Azure AD para la autenticación de entidad de servicio.

1. En la ventana izquierda de Postman, seleccione "Paso 1: Get AAD Auth token" (Paso 1: Obtención del token de autorización de AAD).
2. A continuación, seleccione "Get Azure AD Token for Service Principal Authentication" (Obtener token de Azure AD para la autenticación de la entidad de servicio).
3. Presione **Enviar**.

    Se envía la siguiente operación **POST**.

    ```
    https://login.microsoftonline.com/:tenantId/oauth2/token
    ```

4. La respuesta regresa con el token y establece la variable de entorno "AccessToken" en el valor del token.  

    ![Obtención del token de AAD](./media/develop-with-postman/postman-get-aad-auth-token.png)

## <a name="see-also"></a>Otras referencias

- [Carga de archivos en una cuenta de Media Services: REST](upload-files-rest-how-to.md)
- [Creación de filtros con Media Services: REST](filters-dynamic-manifest-rest-howto.md)
- [API REST basada en Azure Resource Manager](https://github.com/Azure-Samples/media-services-v3-arm-templates)

## <a name="next-steps"></a>Pasos siguientes

- [Stream files with REST](stream-files-tutorial-with-rest.md) (Transmisión de archivos con REST).  
- [Tutorial: Carga, codificación y transmisión de vídeos con REST](stream-files-tutorial-with-rest.md)
