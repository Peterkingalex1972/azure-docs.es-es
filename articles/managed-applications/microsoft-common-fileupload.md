---
title: Elemento de interfaz de usuario FileUpload de Azure | Microsoft Docs
description: Describe el elemento de la interfaz de usuario Microsoft.Common.FileUpload para Azure Portal.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/05/2018
ms.author: tomfitz
ms.openlocfilehash: 92a5f7c058904015cb22a239b7e7c4938ae1fdae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/13/2019
ms.locfileid: "61044650"
---
# <a name="microsoftcommonfileupload-ui-element"></a>Elemento de interfaz de usuario Microsoft.Common.FileUpload
Control que permite al usuario especificar uno o varios archivos para cargar.

## <a name="ui-sample"></a>Ejemplo de interfaz de usuario
![Microsoft.Common.FileUpload](./media/managed-application-elements/microsoft.common.fileupload.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.FileUpload",
  "label": "Some file upload",
  "toolTip": "",
  "constraints": {
    "required": true,
    "accept": ".doc,.docx,.xml,application/msword"
  },
  "options": {
    "multiple": false,
    "uploadMode": "file",
    "openMode": "text",
    "encoding": "UTF-8"
  },
  "visible": true
}
```

## <a name="remarks"></a>Comentarios
- `constraints.accept` especifica los tipos de archivos que se muestran en el cuadro de diálogo de archivo del explorador. Consulte la [especificación HTML5](https://www.w3.org/TR/html5/forms.html#attr-input-accept) para ver los valores permitidos. El valor predeterminado es **null**.
- Si `options.multiple` se establece en **true**, el usuario puede seleccionar más de un archivo en el cuadro de diálogo del explorador. El valor predeterminado es **false**.
- Este elemento permite cargar archivos de dos modos, en función del valor de `options.uploadMode`. Si se especifica **file**, la salida incluye el contenido del archivo como un blob. Si se especifica **url**, el archivo se carga en una ubicación temporal y la salida tiene la dirección URL del blob. Los blobs temporales se eliminan después de 24 horas. El valor predeterminado es **file**.
- Un archivo cargado está protegido. La dirección URL de salida incluye un [token de SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para acceder al archivo durante la implementación.
- El valor de `options.openMode` determina cómo se lee el archivo. Si se espera que el archivo sea de texto sin formato, especifique **text**; en caso contrario, especifique **binary**. El valor predeterminado es **text**.
- Si `options.uploadMode` está establecido en **file** y `options.openMode` está establecido en **binary**, la salida estará codificada en base64.
- `options.encoding` especifica la codificación que se utilizará al leer el archivo. El valor predeterminado es **UTF-8**, y se usa solo cuando `options.openMode` está establecido en **text**.

## <a name="sample-output"></a>Salida de ejemplo
Si options.multiple es false y options.uploadMode es file, la salida tiene el contenido del archivo como una cadena JSON:

```json
"Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua."
```

Si options.multiple es true y options.uploadMode es file, la salida incluye el contenido del archivo como una matriz JSON:

```json
[
  "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua.",
  "Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat.",
  "Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur.",
  "Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
]
```

Si options.multiple es false y options.uploadMode es url, la salida incluye una dirección URL como una cadena JSON:

```json
"https://myaccount.blob.core.windows.net/pictures/profile.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
```

Si options.multiple es true y options.uploadMode es url, la salida incluye una lista de direcciones URL como una matriz JSON:
```json
[
  "https://myaccount.blob.core.windows.net/pictures/profile1.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile2.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d",
  "https://myaccount.blob.core.windows.net/pictures/profile3.jpg?sv=2013-08-15&st=2013-08-16&se=2013-08-17&sr=c&sp=r&rscd=file;%20attachment&rsct=binary &sig=YWJjZGVmZw%3d%3d&sig=a39%2BYozJhGp6miujGymjRpN8tsrQfLo9Z3i8IRyIpnQ%3d"
]
```

Cuando se prueba una definición CreateUiDefinition, algunos exploradores (como Google Chrome) truncan las direcciones URL generadas por el elemento Microsoft.Common.FileUpload en la consola del explorador. Puede que tenga que hacer clic en cada vínculo individual para copiar las direcciones URL completas.


## <a name="next-steps"></a>Pasos siguientes
* Para ver una introducción sobre la creación de definiciones de interfaz de usuario, consulte [Introducción a CreateUiDefinition](create-uidefinition-overview.md).
* Para ver una descripción de las propiedades comunes de los elementos de interfaz de usuario, consulte [Elementos CreateUiDefinition](create-uidefinition-elements.md).
