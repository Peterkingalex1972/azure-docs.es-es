---
title: 'CLI de Azure Service Fabric: sfctl mesh code-package-log | Microsoft Docs'
description: Se describen los comandos de sfctl mesh code-package-log de la CLI de Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: b1949f87dcdb1e3d9fe8e7fd08d8d8ba3b8203a0
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/15/2019
ms.locfileid: "69036438"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Obtiene los registros para el contenedor del paquete de código especificado para la réplica de servicio dada.

## <a name="commands"></a>Comandos:

|Get-Help|DESCRIPCIÓN|
| --- | --- |
| get | Obtiene los registros del contenedor. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Obtiene los registros del contenedor.

Obtiene los registros del contenedor del paquete de código especificado de la réplica de servicio.

### <a name="arguments"></a>Argumentos

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --app-name: nombre de la aplicación [obligatorio] | Nombre de la aplicación. |
| --code-package-name [obligatorio] | El nombre del paquete de código del servicio. |
| --réplica-name [obligatorio] | Nombre de la réplica de Service Fabric. |
| --service-name [obligatorio] | El nombre del servicio. |
| --tail | Número de líneas para mostrar desde el final de los registros. El valor predeterminado es 100. "all" para mostrar los registros completos. |

### <a name="global-arguments"></a>Argumentos globales

|Argumento|DESCRIPCIÓN|
| --- | --- |
| --debug | Aumenta el nivel de detalle de registro para mostrar todos los registros de depuración. |
| --help -h | Muestra este mensaje de ayuda y sale. |
| --output -o | Formato de salida.  Valores permitidos\: json, jsonc, table y tsv.  Valor predeterminado\: json. |
| --query | Cadena de consulta de JMESPath. Consulte http\://jmespath.org/ para obtener más información y ejemplos. |
| --verbose | Aumenta el nivel de detalle de registro. Use --debug para obtener registros de depuración completos. |


## <a name="next-steps"></a>Pasos siguientes
- [Configuración](service-fabric-cli.md) de la CLI de Service Fabric.
- Obtenga información sobre cómo utilizar la CLI de Service Fabric con los [scripts de ejemplo](/azure/service-fabric/scripts/sfctl-upgrade-application).